---
title: Samouczek — używanie Docker Compose do wdrażania grupy wielokontenerowej
description: Użyj Docker Compose do kompilowania i uruchamiania aplikacji wielokontenera, a następnie Wywołaj aplikację w programie, aby Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913844"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Samouczek: Wdrażanie grupy wielokontenerowej przy użyciu Docker Compose 

W tym samouczku użyto [Docker Compose](https://docs.docker.com/compose/) do definiowania i uruchamiania aplikacji wielokontenera lokalnie, a następnie wdrażania jej jako [grupy kontenerów](container-instances-container-groups.md) w Azure Container Instances. 

Uruchamiaj kontenery w Azure Container Instances na żądanie, gdy tworzysz aplikacje natywne w chmurze przy użyciu platformy Docker i chcesz bezproblemowo przełączać się do wdrożenia w chmurze. Ta funkcja jest włączona w ramach [integracji między platformą Docker i platformą Azure](https://docs.docker.com/engine/context/aci-integration/). Możesz użyć natywnych poleceń platformy Docker, aby uruchomić [jedno wystąpienie kontenera](quickstart-docker-cli.md) lub grupę wielokontenerową na platformie Azure.

> [!IMPORTANT]
> Nie wszystkie funkcje Azure Container Instances są obsługiwane. Prześlij opinię na temat integracji Docker-Azure, tworząc problem w repozytorium GitHub usługi [Docker ACI Integration](https://github.com/docker/aci-integration-beta) .

> [!TIP]
> Możesz użyć [rozszerzenia Docker, aby uzyskać Visual Studio Code](https://aka.ms/VSCodeDocker) zintegrowanego środowiska do opracowywania i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

W tym artykule opisano następujące zagadnienia:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów platformy Azure
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Używanie Docker Compose do kompilowania obrazu i uruchamiania aplikacji z obsługą kontenera lokalnie
> * Wypychanie obrazu aplikacji do rejestru kontenerów
> * Tworzenie kontekstu platformy Azure dla usługi Docker
> * Wywołaj aplikację w Azure Container Instances

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejs wiersza polecenia platformy Azure** — na komputerze lokalnym musi być zainstalowany interfejs wiersza polecenia platformy Azure. Zalecana jest wersja 2.10.1 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* **Pulpit platformy Docker** — należy używać systemu Docker w wersji 2.3.0.5 lub nowszej, który jest dostępny w [systemie Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) lub [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Lub zainstaluj [interfejs wiersza polecenia integracji usługi Docker ACI dla systemu Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

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

W katalogu znajduje się kod źródłowy aplikacji i wstępnie utworzony plik do redagowania platformy Docker — YAML.

## <a name="modify-docker-compose-file"></a>Modyfikuj plik redagowania platformy Docker

Otwórz element Docker-Zredaguj. YAML w edytorze tekstu. Plik konfiguruje `azure-vote-back` usługi i `azure-vote-front` .

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

1. Zaktualizuj `image` Właściwość w `azure-vote-front` usłudze. Prefiks nazwy obrazu z nazwą serwera logowania usługi Azure Container Registry, \<acrName\> . azurecr.IO. Na przykład jeśli rejestr ma nazwę moje *Rejestr* , nazwa serwera logowania to *myregistry.azurecr.IO* (wszystkie małe litery), a następnie właściwość Image `myregistry.azurecr.io/azure-vote-front` .
1. Zmień `ports` Mapowanie na `80:80` . Zapisz plik.

Zaktualizowany plik powinien wyglądać podobnie do poniższego:

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

Tworząc te substytuty, obraz, `azure-vote-front` który tworzysz w następnym kroku, jest oznakowany jako rejestr kontenerów platformy Azure, a obraz może zostać rozbudowany do uruchomienia w Azure Container Instances.

> [!TIP]
> W tym scenariuszu nie trzeba używać usługi Azure Container Registry. Na przykład można wybrać prywatne repozytorium w usłudze Docker Hub do hostowania obrazu aplikacji. Jeśli wybierzesz inny rejestr, odpowiednio zaktualizuj właściwość obrazu.

## <a name="run-multi-container-application-locally"></a>Uruchamianie aplikacji wielokontenerowej lokalnie

Uruchom [platformę Docker — Zredaguj](https://docs.docker.com/compose/reference/up/), która używa przykładowego `docker-compose.yaml` pliku do kompilowania obrazu kontenera, pobrania obrazu Redis i uruchomienia aplikacji:

```console
docker-compose up --build -d
```

Po zakończeniu użyj polecenia [docker images](https://docs.docker.com/engine/reference/commandline/images/) w celu wyświetlenia utworzonych obrazów. Zostały pobrane lub utworzone trzy obrazy. `azure-vote-front`Obraz zawiera aplikację frontonu, która używa `uwsgi-nginx-flask` obrazu jako bazy. Obraz `redis` jest używany do uruchomienia wystąpienia usługi Redis.

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

Po podjęciu próby zastosowania aplikacji lokalnej Uruchom [platformę Docker](https://docs.docker.com/compose/reference/down/) , aby zatrzymać aplikację i usunąć kontenery.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Wypychanie obrazu do rejestru kontenerów

Aby wdrożyć aplikację w Azure Container Instances, należy wypchnąć `azure-vote-front` obraz do rejestru kontenerów. Uruchom [platformę Docker — Twórz wypychanie](https://docs.docker.com/compose/reference/push) , aby wypchnąć obraz:

```console
docker-compose push
```

Wypychanie do rejestru może potrwać kilka minut.

Aby sprawdzić, czy obraz jest przechowywany w rejestrze, uruchom polecenie [AZ ACR Repository show](/cli/azure/acr/repository#az-acr-repository-show) :

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Wdrażanie aplikacji w usłudze Azure Container Instances

Następnie przejdź do kontekstu ACI. Kolejne polecenia platformy Docker są uruchamiane w tym kontekście.

```console
docker context use myacicontext
```

Uruchom `docker compose up` , aby uruchomić aplikację w Azure Container Instances. `azure-vote-front`Obraz jest pobierany z rejestru kontenerów, a grupa kontenerów jest tworzona w Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Docker Compose polecenia dostępne obecnie w kontekście ACI są `docker compose up` i `docker compose down` . Nie ma łącznika między `docker` i `compose` w tych poleceniach.

W krótkim czasie Grupa kontenerów jest wdrażana. Przykładowe dane wyjściowe:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Uruchom, `docker ps` Aby wyświetlić uruchomione kontenery i adres IP przypisany do grupy kontenerów.

```console
docker ps
```

Przykładowe dane wyjściowe:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Aby wyświetlić uruchomioną aplikację w chmurze, wprowadź wyświetlany adres IP w lokalnej przeglądarce sieci Web. W tym przykładzie wprowadź `52.179.23.131` . Zostanie załadowana przykładowa aplikacja, jak pokazano w poniższym przykładzie:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Obraz aplikacji do głosowania":::

Aby wyświetlić dzienniki kontenera frontonu, uruchom polecenie [Docker Logs](https://docs.docker.com/engine/reference/commandline/logs) . Na przykład:

```console
docker logs azurevotingappredis_azure-vote-front
```

Możesz również użyć Azure Portal lub innych narzędzi platformy Azure, aby wyświetlić właściwości i stan wdrożonej grupy kontenerów.

Po zakończeniu próby aplikacji Zatrzymaj aplikację i kontenery przy użyciu `docker compose down` :

```console
docker compose down
```

To polecenie usuwa grupę kontenerów w Azure Container Instances.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto Docker Compose do przełączania się z uruchamiania aplikacji wielokontenerowej lokalnie w celu uruchomienia w Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów platformy Azure
> * Klonowanie kodu źródłowego aplikacji z usługi GitHub
> * Używanie Docker Compose do kompilowania obrazu i uruchamiania aplikacji z obsługą kontenera lokalnie
> * Wypychanie obrazu aplikacji do rejestru kontenerów
> * Tworzenie kontekstu platformy Azure dla usługi Docker
> * Wywołaj aplikację w Azure Container Instances

Możesz również użyć [rozszerzenia Docker, aby uzyskać Visual Studio Code](https://aka.ms/VSCodeDocker) zintegrowanego środowiska do opracowywania i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

Jeśli chcesz korzystać z większej liczby funkcji w Azure Container Instances, użyj narzędzi platformy Azure, aby określić grupę z wieloma kontenerami. Na przykład zapoznaj się z samouczkami, aby wdrożyć grupę kontenerów za pomocą interfejsu wiersza polecenia platformy Azure z [plikiem YAML](container-instances-multi-container-yaml.md)lub Wdróż szablon przy użyciu [szablonu Azure Resource Manager](container-instances-multi-container-group.md). 