---
title: Szybki Start — wdrażanie kontenera platformy Docker do wystąpienia kontenera — interfejs wiersza polecenia platformy Docker
description: W tym przewodniku szybki start użyjemy interfejsu wiersza polecenia platformy Docker, aby szybko wdrożyć aplikację sieci Web, która działa w izolowanym wystąpieniu kontenera platformy Azure
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408088"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Szybki Start: Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Docker

Używanie Azure Container Instances do uruchamiania kontenerów platformy Docker bez serwera na platformie Azure z prostotą i szybkością. Wdrażaj do wystąpienia kontenera na żądanie podczas tworzenia aplikacji natywnych w chmurze i chcesz bezproblemowo przełączać się od lokalnego tworzenia do wdrożenia w chmurze.

W tym przewodniku szybki start użyjemy natywnych poleceń interfejsu wiersza polecenia platformy Docker w celu wdrożenia kontenera Docker i udostępnienia aplikacji w Azure Container Instances. Ta funkcja jest włączana przez [integrację między platformą Docker i platformą Azure](https://docs.docker.com/engine/context/aci-integration/) (beta). Kilka sekund po wykonaniu polecenia można `docker run` przejść do aplikacji działającej w kontenerze:

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-account].

W tym przewodniku Szybki Start należy zainstalować program Docker Desktop w wersji 2.3.2.0 lub nowszej, który jest dostępny dla [systemu Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) lub [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Lub zainstaluj [interfejs wiersza polecenia integracji usługi Docker ACI dla systemu Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (beta). 

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i wymaga funkcji beta (wersja zapoznawcza) w programie Docker. Przeczytaj więcej [na temat stabilnych i brzegowych wersji programu Docker Desktop](https://docs.docker.com/desktop/#stable-and-edge-versions). Nie wszystkie funkcje Azure Container Instances są obsługiwane. Prześlij opinię na temat integracji platformy Docker z platformą Azure, tworząc problem w repozytorium GitHub [ACI-Integration-beta](https://github.com/docker/aci-integration-beta) .

## <a name="create-azure-context"></a>Utwórz kontekst platformy Azure

Aby użyć poleceń platformy Docker do uruchamiania kontenerów w Azure Container Instances, najpierw Zaloguj się do platformy Azure:

```bash
docker login azure
```

Po wyświetleniu monitu wprowadź lub wybierz swoje poświadczenia platformy Azure.


Uruchom `docker context create aci` , aby utworzyć kontekst ACI. Ten kontekst kojarzy platformę Docker z subskrypcją platformy Azure, aby można było tworzyć wystąpienia kontenerów. Na przykład, aby utworzyć kontekst o nazwie *myacicontext*:

```
docker context create aci myacicontext
```

Po wyświetleniu monitu wybierz identyfikator subskrypcji platformy Azure, a następnie wybierz istniejącą grupę zasobów lub **Utwórz nową grupę zasobów**. W przypadku wybrania nowej grupy zasobów zostanie ona utworzona z użyciem nazwy wygenerowanej przez system. Wystąpienia kontenerów platformy Azure, tak jak wszystkie zasoby platformy Azure, muszą zostać wdrożone w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.


Uruchom, `docker context ls` Aby potwierdzić dodanie kontekstu ACI do kontekstów platformy Docker:

```
docker context ls
```

## <a name="create-a-container"></a>Tworzenie kontenera

Po utworzeniu kontekstu platformy Docker można utworzyć kontener na platformie Azure. W tym przewodniku szybki start użyjesz `mcr.microsoft.com/azuredocs/aci-helloworld` obrazu publicznego. Ten obraz zawiera małą aplikację internetową napisaną w języku Node.js, która udostępnia statyczną stronę HTML.

Najpierw przejdź do kontekstu ACI. Wszystkie kolejne polecenia platformy Docker są uruchamiane w tym kontekście.

```
docker context use myacicontext
```

Uruchom następujące `docker run` polecenie, aby utworzyć wystąpienie kontenera platformy Azure z portem 80 uwidocznionym dla Internetu:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Przykładowe dane wyjściowe dla pomyślnego wdrożenia:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Uruchom `docker ps` polecenie, aby uzyskać szczegółowe informacje o działającym kontenerze, w tym publiczny adres IP:

```
docker ps
```


Przykładowe dane wyjściowe pokazują publiczny adres IP, w tym przypadku *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Teraz przejdź do adresu IP w przeglądarce. Jeśli zostanie wyświetlona strona internetowa podobna do poniższej — gratulacje! Aplikacja działającą w kontenerze Docker została pomyślne wdrożona na platformie Azure.

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

## <a name="pull-the-container-logs"></a>Ściąganie dzienników kontenera

Jeśli trzeba rozwiązać problem z kontenerem lub z uruchomioną na nim aplikacją (lub po prostu wyświetlić dane wyjściowe), zacznij od wyświetlenia dzienników wystąpienia kontenera.

Na przykład uruchom polecenie, `docker logs` Aby wyświetlić dzienniki kontenera *zasobożernych-Kirch* w kontekście ACI:

```azurecli-interactive
docker logs hungry-kirch
```

W dziennikach kontenera powinny być widoczne żądania HTTP GET utworzone podczas przeglądania aplikacji w przeglądarce.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem Uruchom polecenie, `docker rm` aby je usunąć. To polecenie powoduje zatrzymanie i usunięcie wystąpienia kontenera platformy Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wystąpienie kontenera platformy Azure z obrazu publicznego przy użyciu integracji między platformą Docker i platformą Azure. Dowiedz się więcej o scenariuszach integracji w [dokumentacji platformy Docker](https://docs.docker.com/engine/context/aci-integration/). 

Możesz również użyć [rozszerzenia Docker](https://aka.ms/VSCodeDocker) , aby uzyskać Visual Studio Code zintegrowanego środowiska do opracowywania i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

Aby używać narzędzi platformy Azure do tworzenia wystąpień kontenera i zarządzania nimi, zobacz inne Przewodniki Szybki Start przy użyciu szablonu [wiersza polecenia platformy Azure](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)i [Azure Resource Manager](container-instances-quickstart-template.md).

Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

