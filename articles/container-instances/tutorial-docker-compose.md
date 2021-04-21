---
title: Samouczek — Docker Compose do wdrażania grupy z wieloma kontenerami
description: Użyj Docker Compose, aby skompilować i uruchomić aplikację z wieloma kontenerami, a następnie uruchom aplikację w celu Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: ef08b9f9e0f596f1d94c0e6edfd46f735fe78053
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786924"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Samouczek: wdrażanie grupy z wieloma kontenerami przy użyciu Docker Compose 

W tym samouczku użyjemy [Docker Compose,](https://docs.docker.com/compose/) aby lokalnie zdefiniować i uruchomić aplikację z wieloma kontenerami, [a](container-instances-container-groups.md) następnie wdrożyć ją jako grupę kontenerów w Azure Container Instances. 

Uruchamiaj kontenery na Azure Container Instances na żądanie podczas tworzenia aplikacji natywnych dla chmury za pomocą platformy Docker i chcesz bezproblemowo przełączyć się z lokalnego tworzenia na wdrożenie w chmurze. Tę możliwość można włączyć dzięki [integracji między platformą Docker i platformą Azure.](https://docs.docker.com/engine/context/aci-integration/) Możesz użyć natywnych poleceń platformy Docker, aby uruchomić [pojedyncze wystąpienie](quickstart-docker-cli.md) kontenera lub grupę wielu kontenerów na platformie Azure.

> [!IMPORTANT]
> Nie wszystkie funkcje Azure Container Instances są obsługiwane. Aby przekazać opinię na temat Docker-Azure, należy utworzyć problem w repozytorium GitHub integracji [usługi Docker ACI.](https://github.com/docker/aci-integration-beta)

> [!TIP]
> Za pomocą rozszerzenia [platformy Docker](https://aka.ms/VSCodeDocker) Visual Studio Code zintegrowane środowisko do tworzenia i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

W tym artykule opisano następujące zagadnienia:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów platformy Azure
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Używanie Docker Compose do kompilowania obrazu i lokalnego uruchamiania aplikacji z wieloma kontenerami
> * Wypychanie obrazu aplikacji do rejestru kontenerów
> * Tworzenie kontekstu platformy Azure dla platformy Docker
> * Wyniesienie aplikacji do Azure Container Instances

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejs wiersza polecenia** platformy Azure — interfejs wiersza polecenia platformy Azure musi być zainstalowany na komputerze lokalnym. Zalecana jest wersja 2.10.1 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* **Docker Desktop** — należy użyć programu Docker Desktop w wersji 2.3.0.5 lub nowszej, dostępnej dla systemu [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) [lub macOS.](https://desktop.docker.com/mac/edge/Docker.dmg) Możesz też zainstalować interfejs [wiersza polecenia integracji z usługą Docker ACI dla systemu Linux.](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Pobieranie kodu aplikacji

W tym samouczku jest używana przykładowa prosta aplikacja do głosowania. Ta aplikacja składa się ze składnika internetowego frontonu oraz działającego na zapleczu wystąpienia usługi Redis. Składnik internetowy znajduje się w pakiecie niestandardowego obrazu kontenera. Wystąpienie usługi Redis używa niezmodyfikowanego obrazu z usługi Docker Hub.

Użyj narzędzia [git](https://git-scm.com/downloads), aby sklonować przykładową aplikację do swojego środowiska programistycznego:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Przejdź do sklonowanego katalogu.

```console
cd azure-voting-app-redis
```

Wewnątrz katalogu znajduje się kod źródłowy aplikacji i wstępnie utworzony plik docker compose docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Modyfikowanie pliku docker compose

Otwórz plik docker-compose.yaml w edytorze tekstów. Plik konfiguruje usługi `azure-vote-back` `azure-vote-front` i .

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

W `azure-vote-front` konfiguracji należy wprowadzić następujące dwie zmiany:

1. Zaktualizuj `image` właściwość w `azure-vote-front` usłudze. Przed nazwą obrazu należy poprzeć nazwę serwera logowania rejestru kontenerów platformy Azure, \<acrName\> azurecr.io. Jeśli na przykład rejestr ma nazwę *myregistry,* nazwa serwera logowania to *myregistry.azurecr.io* (wszystkie małe litery), a właściwość obrazu to `myregistry.azurecr.io/azure-vote-front` .
1. Zmień `ports` mapowanie na `80:80` . Zapisz plik.

Zaktualizowany plik powinien wyglądać podobnie do następującego:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Dzięki tym podstawieniom obraz, który zostanie skompilowany w następnym kroku, zostanie otagowany dla rejestru kontenerów platformy Azure, a obraz można ściągnąć w celu uruchomienia w `azure-vote-front` Azure Container Instances.

> [!TIP]
> W tym scenariuszu nie trzeba używać rejestru kontenerów platformy Azure. Możesz na przykład wybrać prywatne repozytorium w Docker Hub, aby hostować obraz aplikacji. Jeśli wybierzesz inny rejestr, odpowiednio zaktualizuj właściwość obrazu.

## <a name="run-multi-container-application-locally"></a>Lokalne uruchamianie aplikacji z wieloma kontenerami

Uruchom [program docker-compose up,](https://docs.docker.com/compose/reference/up/)który używa przykładowego pliku do skompilowania obrazu kontenera, pobrania obrazu redis i `docker-compose.yaml` uruchomienia aplikacji:

```console
docker-compose up --build -d
```

Po zakończeniu użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/) w celu wyświetlenia utworzonych obrazów. Zostały pobrane lub utworzone trzy obrazy. Obraz zawiera aplikację front end, która `azure-vote-front` używa obrazu jako `uwsgi-nginx-flask` podstawy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Uruchom polecenie [docker ps](https://docs.docker.com/engine/reference/commandline/ps/), aby wyświetlić uruchomione kontenery:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Aby wyświetlić działającą aplikację, wprowadź adres `http://localhost:80` w lokalnej przeglądarce internetowej. Zostanie załadowana przykładowa aplikacja, jak pokazano w poniższym przykładzie:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Obraz aplikacji do głosowania":::

Po wypróbowaniu aplikacji lokalnej uruchom [program docker-compose w dół,](https://docs.docker.com/compose/reference/down/) aby zatrzymać aplikację i usunąć kontenery.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Wypychanie obrazu do rejestru kontenerów

Aby wdrożyć aplikację w Azure Container Instances, należy wypchnąć `azure-vote-front` obraz do rejestru kontenerów. Uruchom [wypychanie docker-compose,](https://docs.docker.com/compose/reference/push) aby wypchnąć obraz:

```console
docker-compose push
```

Wypychanie do rejestru może potrwać kilka minut.

Aby sprawdzić, czy obraz jest przechowywany w rejestrze, uruchom [polecenie az acr repository show:](/cli/azure/acr/repository#az_acr_repository_show)

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Wdrażanie aplikacji w usłudze Azure Container Instances

Następnie zmień kontekst na ACI. Kolejne polecenia platformy Docker są uruchamiane w tym kontekście.

```console
docker context use myacicontext
```

Uruchom `docker compose up` , aby uruchomić aplikację w Azure Container Instances. Obraz `azure-vote-front` jest ściągany z rejestru kontenerów, a grupa kontenerów jest tworzona w Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Docker Compose polecenia aktualnie dostępne w kontekście ACI to `docker compose up` i `docker compose down` . W tych poleceniach nie ma łącznika `docker` `compose` między i .

W krótkim czasie zostanie wdrożona grupa kontenerów. Przykładowe dane wyjściowe:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Uruchom `docker ps` , aby wyświetlić uruchomione kontenery i adres IP przypisany do grupy kontenerów.

```console
docker ps
```

Przykładowe dane wyjściowe:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Aby wyświetlić aplikację uruchamianą w chmurze, wprowadź wyświetlony adres IP w lokalnej przeglądarce internetowej. W tym przykładzie wprowadź `52.179.23.131` . Zostanie załadowana przykładowa aplikacja, jak pokazano w poniższym przykładzie:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Obraz aplikacji do głosowania w UCI":::

Aby wyświetlić dzienniki kontenera frontonia, uruchom polecenie [docker logs.](https://docs.docker.com/engine/reference/commandline/logs) Na przykład:

```console
docker logs azurevotingappredis_azure-vote-front
```

Możesz również użyć narzędzia Azure Portal lub innych narzędzi platformy Azure, aby wyświetlić właściwości i stan wdrożonej grupy kontenerów.

Po zakończeniu próby aplikacji zatrzymaj aplikację i kontenery za `docker compose down` pomocą :

```console
docker compose down
```

To polecenie usuwa grupę kontenerów w Azure Container Instances.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykorzystano Docker Compose do przełączenia się z uruchamiania aplikacji z wieloma kontenerami lokalnie na uruchamianie w Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów platformy Azure
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Używanie Docker Compose do kompilowania obrazu i lokalnego uruchamiania aplikacji z wieloma kontenerami
> * Wypychanie obrazu aplikacji do rejestru kontenerów
> * Tworzenie kontekstu platformy Azure dla platformy Docker
> * Wyniesienie aplikacji do Azure Container Instances

Możesz również użyć rozszerzenia [platformy Docker](https://aka.ms/VSCodeDocker) dla platformy Visual Studio Code zintegrowane środowisko do tworzenia i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

Jeśli chcesz korzystać z większej liczby funkcji w Azure Container Instances, użyj narzędzi platformy Azure, aby określić grupę z wieloma kontenerami. Zobacz na przykład samouczki dotyczące wdrażania grupy kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure z plikiem [YAML](container-instances-multi-container-yaml.md)lub wdrażania przy użyciu [Azure Resource Manager szablonu](container-instances-multi-container-group.md). 