---
title: Wdrażanie niestandardowego obrazu symulacji urządzenia — Azure | Microsoft Docs
description: W tym przewodniku krok po kroku dowiesz się, jak wdrożyć niestandardowy obraz platformy Docker rozwiązania do symulacji urządzenia na platformie Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "61448417"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Wdrażanie niestandardowego obrazu platformy Docker symulacji urządzenia

Rozwiązanie do symulacji urządzeń można zmodyfikować w celu dodania funkcji niestandardowych. Przykładowo w artykule [Serializowanie telemetrii używającej buforów protokołów](iot-accelerators-device-simulation-protobuf.md) pokazano, jak dodać urządzenie niestandardowe do rozwiązania korzystającego z buforów protokołu (protobuf) w celu wysyłania danych telemetrycznych. Po lokalnym przetestowaniu zmian w następnym kroku należy wdrożyć zmiany w wystąpieniu symulacji urządzenia na platformie Azure. Aby wykonać to zadanie, należy utworzyć i wdrożyć obraz platformy Docker, który zawiera zmodyfikowaną usługę.

Kroki opisane w tym przewodniku przedstawiają, jak:

1. Przygotuj środowisko programistyczne
1. Generowanie nowego obrazu platformy Docker
1. Konfigurowanie symulacji urządzenia do korzystania z nowego obrazu platformy Docker
1. Uruchamianie symulacji przy użyciu nowego obrazu

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku:

* Wdrożone wystąpienie [symulacji urządzenia](quickstart-device-simulation-deploy.md) .
* Docker. Pobierz platformę [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) dla danej platformy.
* [Konto koncentratora platformy Docker](https://hub.docker.com/) , na którym można przekazać obrazy platformy Docker. Na koncie centrum platformy Docker Utwórz repozytorium publiczne o nazwie **symulacja urządzenia**.
* Zmodyfikowano i przetestowano [rozwiązanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na komputerze lokalnym. Na przykład można zmodyfikować rozwiązanie, aby serializować dane [telemetryczne przy użyciu buforów protokołu](iot-accelerators-device-simulation-protobuf.md).
* Powłoka, która może korzystać z protokołu SSH. W przypadku instalowania usługi git dla systemu Windows można użyć powłoki **bash** , która jest częścią tej instalacji. Możesz również użyć [Azure Cloud Shell](https://shell.azure.com/).

W instrukcjach przedstawionych w tym artykule przyjęto założenie, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek i poleceń plików do środowiska.

## <a name="create-a-new-docker-image"></a>Tworzenie nowego obrazu platformy Docker

Aby wdrożyć własne zmiany w usłudze symulacji urządzeń, należy edytować skrypty kompilacji i wdrażania w folderze **scripts\docker** , aby przekazać kontenery do konta usługi Docker-Hub

### <a name="modify-the-docker-scripts"></a>Modyfikowanie skryptów platformy Docker

Zmodyfikuj skrypty Docker **Build. cmd**, **Publish. cmd**i **Run. cmd** w folderze **scripts\docker** z informacjami o repozytorium centrum Docker. W tych krokach przyjęto założenie, że utworzono repozytorium publiczne o nazwie **symulacja urządzenia**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Zaktualizuj plik **Docker-Compose. yml** w następujący sposób:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Skonfiguruj rozwiązanie w celu uwzględnienia nowych plików

W przypadku dodania nowych plików modelu urządzenia należy jawnie uwzględnić je w rozwiązaniu. Dodaj wpis do **usługi/Services. csproj** dla każdego dodatkowego pliku do uwzględnienia. Jeśli na przykład zakończono [Serializowanie telemetrii przy użyciu buforów protokołu](iot-accelerators-device-simulation-protobuf.md) , należy dodać następujące wpisy:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generowanie nowych obrazów platformy Docker i wypychanie do centrum platformy Docker

Opublikuj nowy obraz platformy Docker w usłudze Docker Hub przy użyciu konta centrum platformy Docker:

1. Otwórz wiersz polecenia i przejdź do lokalnej kopii repozytorium symulacji urządzenia.

1. Przejdź do folderu **Docker** :

    ```cmd
    cd scripts\docker
    ```

1. Uruchom następujące polecenie, aby skompilować obraz platformy Docker:

    ```cmd
    build.cmd
    ```

1. Uruchom następujące polecenie, aby opublikować obraz platformy Docker w repozytorium centrum platformy Docker. Zaloguj się do platformy Docker przy użyciu poświadczeń centrum Docker:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizowanie usługi

Aby zaktualizować kontener symulacji urządzenia tak, aby korzystał z niestandardowego obrazu, wykonaj następujące czynności:

* Użyj protokołu SSH, aby nawiązać połączenie z maszyną wirtualną hostującym wystąpienie symulacji urządzenia. Użyj adresu IP i hasła, które zostały zanotowane w poprzedniej sekcji:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Przejdź do katalogu **/App** :

    ```sh
    cd /app
    ```

* Edytuj plik **Docker-Compose. yml** :

    ```sh
    sudo nano docker-compose.yml
    ```

    Zmodyfikuj **obraz** , aby wskazywał niestandardowy obraz **symulacji urządzenia** przekazany do repozytorium centrum Docker:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Zapisz zmiany.

* Uruchom następujące polecenie, aby ponownie uruchomić mikrousługi:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Uruchamianie symulacji

Teraz można uruchomić symulację przy użyciu dostosowanego rozwiązania do symulacji urządzenia:

1. Uruchom interfejs użytkownika sieci Web symulacji urządzenia z [Microsoft Azure akceleratorów rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Użyj interfejsu użytkownika sieci Web, aby skonfigurować i uruchomić symulację. Jeśli wcześniej zakończono [Serializowanie telemetrii przy użyciu buforów protokołu](iot-accelerators-device-simulation-protobuf.md), można użyć niestandardowego modelu urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak wdrożyć niestandardowy obraz symulacji urządzenia, warto dowiedzieć się, jak [używać istniejącego Centrum IoT Hub z akceleratorem rozwiązania do symulacji urządzeń](iot-accelerators-device-simulation-choose-hub.md).