---
title: Tworzenie urządzenia w wersji zapoznawczej usługi Azure IoT Plug and Play (Linux) | Microsoft Docs
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia i sprawdź, czy urządzenie jest połączone z IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550489"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Szybki Start: korzystanie z modelu możliwości urządzenia do tworzenia urządzenia w wersji zapoznawczej IoT Plug and Play (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Plug and Play IoT. DCM jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w DCM są zorganizowane w interfejsy wielokrotnego użytku. Można wygenerować kod urządzenia szkieletowego na podstawie DCM. W tym przewodniku szybki start pokazano, jak używać VS Code w Ubuntu Linux do tworzenia urządzenia Plug and Play IoT przy użyciu DCM.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że używasz Ubuntu Linux ze środowiskiem pulpitu. Kroki opisane w tym samouczku zostały przetestowane przy użyciu Ubuntu 18,04.

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na lokalnym komputerze z systemem Linux:

* Zainstaluj usługi w **zatoce**, **git**, **CMAKE**i wszystkie zależności przy `apt-get` użyciu polecenia:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Sprawdź, czy wersja `cmake` programu jest powyżej **2.8.12** , a wersja **tej wersji jest większa** niż **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Azure IoT Tools

Wykonaj następujące kroki, aby zainstalować pakiet Microsoft [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. W VS Code wybierz kartę **rozszerzenia** .
1. Wyszukaj **narzędzia Azure IoT Tools**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Pobierz parametry połączenia dla repozytorium modelu firmy

_Parametry połączenia repozytorium modelu firmy_ można znaleźć w portalu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) , gdy zalogujesz się przy użyciu konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz. Po zalogowaniu wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start użyjesz Menedżera bibliotek [Vcpkg](https://github.com/microsoft/vcpkg) , aby zainstalować zestaw SDK urządzeń Azure IoT C w środowisku deweloperskim.

Otwórz powłokę. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku szybki start użyjesz istniejącego modelu możliwości przykładowego urządzenia i skojarzonych interfejsów.

1. Utwórz `pnp_app` katalog na dysku lokalnym. Ten folder służy do plików modelu urządzenia i szczątkowego kodu urządzenia.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Pobierz model możliwości urządzenia i przykładowe pliki interfejsu do `pnp_app` folderu.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Otwórz `pnp_app` folder z vs Code. Pliki można wyświetlić przy użyciu funkcji IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device-linux/dcm.png)

1. W pobranych plikach Zastąp `<YOUR_COMPANY_NAME_HERE>` pola `@id` i `schema` unikatowymi wartościami. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora cyfrowego przędzy](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generuj procedurę tworzenia kodu w języku C

Teraz, gdy masz już DCM i skojarzone z nią interfejsy, możesz wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w VS Code:

1. Gdy folder `pnp_app` jest otwarty w vs Code, użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu narzędzia generatora kodu Plug and Play IoT trwa kilka sekund, aby pobrać i zainstalować ją automatycznie.

1. Wybierz plik **SampleDevice. capabilitymodel. JSON** , który ma być używany do generowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**. Będzie to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **przez IoT Hub parametry połączenia urządzenia** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Linux** jako szablon projektu.

1. Wybierz pozycję **Via Vcpkg** , aby dołączać zestaw SDK urządzeń.

1. Nowy folder o nazwie **sample_device** jest tworzony w tej samej lokalizacji, w której znajduje się plik DCM, a w tym wygenerowane pliki zastępcze kodu urządzenia. VS Code otwiera nowe okno, aby je wyświetlić.
    ![Kod urządzenia](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Kompiluj i uruchamiaj kod

Kod źródłowy zestawu SDK urządzenia służy do tworzenia wygenerowanej procedury wejścia kodu urządzenia. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. Utwórz folder kompilacji **CMAKE** dla aplikacji **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Uruchom CMake, aby skompilować aplikację przy użyciu zestawu SDK. Następujące polecenie założono, że zainstalowano **vcpkg** w folderze głównym:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację przekazującą parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub.

    ![Uruchomiono aplikację urządzenia](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikuj pliki modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą polecenia **AZ** CLI, należy opublikować pliki w repozytorium modelu.

1. Przy otwartym `pnp_app` folderze w programie vs Code **naciśnij kombinację klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **IoT plug & Play: Prześlij pliki do repozytorium modeli**.

1. Wybierz `SampleDevice.capabilitymodel.json` pozycję `EnvironmentalSensor.interface.json` i.

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko podczas pierwszego połączenia z repozytorium.

1. W VS Code oknie danych wyjściowych i powiadomieniu można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > W przypadku wystąpienia błędów podczas publikowania plików modelu urządzenia można spróbować użyć polecenia **IoT Plug and Play: Wyloguj repozytorium modeli** , aby się wylogować, i wykonaj kroki ponownie.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą interfejsu wiersza polecenia usługi Azure IoT

Po rozpoczęciu uruchamiania klienta urządzenia można sprawdzić, czy pracuje on z interfejsem wiersza polecenia platformy Azure.

Użyj poniższego polecenia, aby wyświetlić dane telemetryczne wysyłane przez przykładowe urządzenie. Może być konieczne poczekanie minutę lub dwie, zanim zobaczysz dane telemetryczne w danych wyjściowych:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Użyj następującego polecenia, aby wyświetlić wszystkie właściwości wysyłane przez urządzenie:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia urządzenia Plug and Play IoT przy użyciu DCM.

Aby dowiedzieć się więcej o DCMs i sposobach tworzenia własnych modeli, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu Visual Studio Code](tutorial-pnp-visual-studio-code.md)
