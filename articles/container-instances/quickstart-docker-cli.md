---
title: Szybki Start — wdrażanie kontenera platformy Docker do wystąpienia kontenera — interfejs wiersza polecenia platformy Docker
description: W tym przewodniku szybki start użyjemy interfejsu wiersza polecenia platformy Docker, aby szybko wdrożyć aplikację sieci Web, która działa w izolowanym wystąpieniu kontenera platformy Azure
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91262318"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Szybki Start: Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Docker

Używanie Azure Container Instances do uruchamiania kontenerów platformy Docker bez serwera na platformie Azure z prostotą i szybkością. Wdrażaj do wystąpienia kontenera na żądanie podczas tworzenia aplikacji natywnych w chmurze i chcesz bezproblemowo przełączać się od lokalnego tworzenia do wdrożenia w chmurze.

W tym przewodniku szybki start użyjemy natywnych poleceń interfejsu wiersza polecenia platformy Docker w celu wdrożenia kontenera Docker i udostępnienia aplikacji w Azure Container Instances. Ta funkcja jest włączona w ramach [integracji między platformą Docker i platformą Azure](https://docs.docker.com/engine/context/aci-integration/). Kilka sekund po wykonaniu polecenia można `docker run` przejść do aplikacji działającej w kontenerze:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-account].

W tym przewodniku szybki start potrzebny jest program Docker w wersji 2.3.0.5 lub nowszej, który jest dostępny dla [systemu Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) lub [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Lub zainstaluj [interfejs wiersza polecenia integracji usługi Docker ACI dla systemu Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux). 

> [!IMPORTANT]
> Nie wszystkie funkcje Azure Container Instances są obsługiwane. Prześlij opinię na temat integracji Docker-Azure, tworząc problem w repozytorium GitHub [ACI-Integration-beta](https://github.com/docker/aci-integration-beta) .

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

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

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances":::

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


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z kontenerem Uruchom polecenie, `docker rm` aby je usunąć. To polecenie powoduje zatrzymanie i usunięcie wystąpienia kontenera platformy Azure.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wystąpienie kontenera platformy Azure z obrazu publicznego przy użyciu integracji między platformą Docker i platformą Azure. Dowiedz się więcej o scenariuszach integracji w [dokumentacji platformy Docker](https://docs.docker.com/engine/context/aci-integration/). 

Możesz również użyć [rozszerzenia Docker, aby uzyskać Visual Studio Code](https://aka.ms/VSCodeDocker) zintegrowanego środowiska do opracowywania i uruchamiania kontenerów, obrazów i kontekstów oraz zarządzania nimi.

Aby używać narzędzi platformy Azure do tworzenia wystąpień kontenera i zarządzania nimi, zobacz inne Przewodniki Szybki Start przy użyciu szablonu [wiersza polecenia platformy Azure](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md)i [Azure Resource Manager](container-instances-quickstart-template.md).

Jeśli chcesz używać Docker Compose do definiowania i uruchamiania aplikacji wielokontenerowej lokalnie, a następnie przełączać się do Azure Container Instances, przejdź do samouczka.

> [!div class="nextstepaction"]
> [Samouczek Docker Compose](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

