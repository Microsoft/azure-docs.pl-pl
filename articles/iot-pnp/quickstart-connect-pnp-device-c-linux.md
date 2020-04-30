---
title: Podłączanie przykładowego kodu urządzenia do programu IoT Plug and Play w wersji zapoznawczej do IoT Hub (Linux) | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykład kod urządzenia w systemie Linux, który nawiązuje połączenie z usługą IoT Hub. Użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75531272"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej uruchomionej w systemie Linux do IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play w systemie Linux, nawiązywania połączenia z usługą IoT BUB oraz używania interfejsu wiersza polecenia platformy Azure do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla języka C. Deweloperzy rozwiązań mogą korzystać z interfejsu wiersza polecenia platformy Azure, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzeń.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym samouczku zostały przetestowane przy użyciu Ubuntu 18,04.

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na lokalnym komputerze z systemem Linux:

Zainstaluj usługi w **zatoce**, **git**, **CMAKE**i wszystkie zależności przy `apt-get` użyciu polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy wersja `cmake` programu jest powyżej **2.8.12** , a wersja **tej wersji jest większa** niż **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla systemu Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. `cmake` Utwórz podkatalog w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aktualizowanie repozytorium modelu

Przed uruchomieniem przykładu Dodaj model możliwości urządzenia i definicje interfejsów do repozytorium modelu firmy:

1. Zaloguj się do portalu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) przy użyciu konta służbowego firmy Microsoft lub swojego identyfikatora partnera firmy Microsoft, jeśli go masz.

1. Wybierz opcję **repozytorium firmowe** , a następnie pozycję **modele możliwości**.

1. Wybierz pozycję **Nowy** , a następnie **Przekaż**.

1. Wybierz plik `SampleDevice.capabilitymodel.json` z `digitaltwin_client/samples` folderu w folderze głównym zestawu SDK urządzeń. Wybierz pozycję **Otwórz** , a następnie **Zapisz** , aby przekazać plik modelu do repozytorium.

1. Wybierz opcję **repozytorium firmowe** , a następnie pozycję **interfejsy**.

1. Wybierz pozycję **Nowy** , a następnie **Przekaż**.

1. Wybierz plik `EnvironmentalSensor.interface.json` z `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` folderu w folderze głównym zestawu SDK urządzeń. Wybierz pozycję **Otwórz** , a następnie **Zapisz** , aby przekazać plik interfejsu do repozytorium.

1. Wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**. Zanotuj pierwsze _Parametry połączenia repozytorium modelu firmy_, korzystając z niego w dalszej części tego przewodnika Szybki Start.

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Uruchom przykładową aplikację w zestawie SDK, aby symulować urządzenie Plug and Play IoT wysyłające dane telemetryczne do centrum IoT. Aby uruchomić przykładową aplikację:

1. W `cmake` folderze przejdź do folderu, który zawiera plik wykonywalny:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Uruchom plik wykonywalny:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą interfejsu wiersza polecenia usługi Azure IoT

Po uruchomieniu przykładowego klienta urządzenia sprawdź, czy pracuje on z interfejsem wiersza polecenia platformy Azure.

Użyj poniższego polecenia, aby wyświetlić dane telemetryczne wysyłane przez przykładowe urządzenie. Może być konieczne poczekanie minutę lub dwie, zanim zobaczysz dane telemetryczne w danych wyjściowych:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Użyj następującego polecenia, aby wyświetlić właściwości wysyłane przez urządzenie:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
