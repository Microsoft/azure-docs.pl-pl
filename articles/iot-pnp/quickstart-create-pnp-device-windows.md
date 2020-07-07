---
title: Tworzenie urządzenia w wersji zapoznawczej IoT Plug and Play (Windows) | Microsoft Docs
description: Użyj modelu możliwości urządzenia do generowania kodu urządzenia. Następnie uruchom kod urządzenia i sprawdź, czy urządzenie jest połączone z IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b02455c153c6205b87a64a388d5588c3c431e0c9
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963516"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Szybki Start: korzystanie z modelu możliwości urządzenia do tworzenia urządzenia w wersji zapoznawczej IoT Plug and Play (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

_Model możliwości urządzenia_ (DCM) opisuje możliwości urządzenia Plug and Play IoT. DCM jest często skojarzony z jednostką SKU produktu. Możliwości zdefiniowane w DCM są zorganizowane w interfejsy wielokrotnego użytku. Można wygenerować kod urządzenia szkieletowego na podstawie DCM. W tym przewodniku szybki start pokazano, jak używać usługi VS Code w systemie Windows do tworzenia urządzeń Plug and Play IoT przy użyciu DCM.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na komputerze lokalnym:

* [Narzędzia kompilacji dla programu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) z **narzędziami kompilacji C++** i obciążeniami **składników Menedżera pakietów NuGet** . Lub jeśli masz już [program Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z zainstalowanymi takimi samymi obciążeniami.
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).
* [Program Visual Studio Code](https://code.visualstudio.com/)

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi Azure IoT Tools

Wykonaj następujące kroki, aby zainstalować pakiet Microsoft [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. W VS Code wybierz kartę **rozszerzenia** .
1. Wyszukaj **narzędzia Azure IoT Tools**.
1. Wybierz pozycję **Zainstaluj**.

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Pobierz parametry połączenia dla repozytorium modelu firmy

_Parametry połączenia repozytorium modelu firmy_ można znaleźć w portalu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) , gdy zalogujesz się przy użyciu konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz. Po zalogowaniu wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start użyjesz Menedżera bibliotek [Vcpkg](https://github.com/microsoft/vcpkg) , aby zainstalować zestaw SDK urządzeń Azure IoT C w środowisku deweloperskim.

1. Otwórz wiersz polecenia. Wykonaj następujące polecenie, aby zainstalować Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Następnie, aby podłączyć [integrację](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)obejmującą wiele użytkowników, uruchom następujące polecenie (Uwaga: wymaga od administratora przy pierwszym użyciu):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Zainstaluj zestaw SDK urządzeń usługi Azure IoT C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use-prov-client]
    ```

## <a name="author-your-model"></a>Tworzenie modelu

W tym przewodniku szybki start użyjesz istniejącego modelu możliwości przykładowego urządzenia i skojarzonych interfejsów.

1. Utwórz `pnp_app` folder na dysku lokalnym. Ten folder służy do plików modelu urządzenia i szczątkowego kodu urządzenia.

1. Pobierz [model możliwości urządzenia](https://github.com/Azure/opendigitaltwins-dtdl/blob/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/SampleDevice.capabilitymodel.json) i [przykładowe pliki interfejsu](https://github.com/Azure/opendigitaltwins-dtdl/blob/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensor.interface.json) i Zapisz je w `pnp_app` folderze.

    > [!TIP]
    > Aby pobrać plik z usługi GitHub, przejdź do pliku, kliknij prawym przyciskiem myszy pozycję **RAW**, a następnie wybierz pozycję **Zapisz łącze jako**.

1. Otwórz `pnp_app` folder z vs Code. Pliki można wyświetlić przy użyciu funkcji IntelliSense:

    ![Model możliwości urządzenia](media/quickstart-create-pnp-device/dcm.png)

1. W pobranych plikach Zastąp `<YOUR_COMPANY_NAME_HERE>` `@id` `schema` pola i unikatowymi wartościami. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora cyfrowego przędzy](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Generuj procedurę tworzenia kodu w języku C

Teraz, gdy masz już DCM i skojarzone z nią interfejsy, możesz wygenerować kod urządzenia, który implementuje model. Aby wygenerować skrót kodu C w VS Code:

1. Gdy `pnp_app` folder jest otwarty w vs Code, użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz pozycję **Generuj procedurę tworzenia kodu urządzenia**.

    > [!NOTE]
    > Przy pierwszym użyciu interfejsu wiersza polecenia IoT Plug and Play CodeGen można pobrać i zainstalować automatycznie kilka sekund.

1. Wybierz **SampleDevice.capabilitymodel.jsw** pliku, który ma zostać użyty do wygenerowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu **sample_device**. Jest to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **przez IoT Hub parametry połączenia urządzenia** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Windows** jako szablon projektu.

1. Wybierz pozycję **Via Vcpkg** , aby dołączać zestaw SDK urządzeń.

1. Nowy folder o nazwie **sample_device** jest tworzony w tej samej lokalizacji, w której znajduje się plik DCM, a w tym wygenerowane pliki zastępcze kodu urządzenia. VS Code otwiera nowe okno, aby je wyświetlić.
    ![Kod urządzenia](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Kompiluj i uruchamiaj kod

Pakiet Vcpkg jest używany do kompilowania wygenerowanej procedury tworzenia kodu urządzenia. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. Utwórz `cmake` podkatalog w `sample_device` folderze i przejdź do tego folderu:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć wytworzoną procedurę tworzenia kodu (zastępując symbol zastępczy katalogiem repozytorium Vcpkg):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Jeśli używasz programu Visual Studio 2017 lub 2015, musisz określić Generator CMake na podstawie używanych narzędzi kompilacji:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Jeśli CMAKE nie może znaleźć kompilatora języka C++, podczas uruchamiania poprzedniego polecenia pojawiają się błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Po pomyślnym zakończeniu kompilacji Uruchom aplikację, przekazując parametry połączenia urządzenia usługi IoT Hub jako parametr.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Aplikacja urządzenia uruchamia wysyłanie danych do IoT Hub.

    ![Uruchomiono aplikację urządzenia](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Sprawdzanie poprawności kodu

### <a name="publish-device-model-files-to-model-repository"></a>Publikuj pliki modelu urządzenia w repozytorium modelu

Aby sprawdzić poprawność kodu urządzenia za pomocą programu **Azure IoT Explorer**, należy opublikować pliki w repozytorium modeli.

1. Gdy `pnp_app` folder jest otwarty w vs Code, użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **IoT plug & Play: Prześlij pliki do repozytorium modeli**.

1. Wybierz pozycję `SampleDevice.capabilitymodel.json` i `EnvironmentalSensor.interface.json` .

1. Wprowadź parametry połączenia repozytorium modelu firmy.

    > [!NOTE]
    > Parametry połączenia są wymagane tylko podczas pierwszego połączenia z repozytorium.

1. W VS Code oknie danych wyjściowych i powiadomieniu można sprawdzić, czy pliki zostały pomyślnie opublikowane.

    > [!NOTE]
    > W przypadku wystąpienia błędów podczas publikowania plików modelu urządzenia można spróbować użyć polecenia **IoT Plug and Play: Wyloguj repozytorium modeli** , aby się wylogować, i wykonaj kroki ponownie.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby dodać repozytorium firmy, wybierz pozycję **Ustawienia**, a następnie **+ Dodaj źródło definicji modułu**, a następnie pozycję **repozytorium firmy**. Dodaj parametry połączenia repozytorium modelu firmy i wybierz pozycję **Zapisz i Połącz**.

1. Na stronie Przegląd **urządzeń** Znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia jest nadal uruchomiona w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w programie Azure IoT Explorer jest raportowany jako _połączony_ (jeśli nie, należy **odświeżyć** do momentu). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: <YOUR_INTERFACE_NAME>: EnvironmentalSensor: 1** , aby wyświetlić elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne. Nazwa interfejsu, która zostanie wyświetlona, to nazwa wprowadzona podczas tworzenia modelu.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia urządzenia Plug and Play IoT przy użyciu DCM.

Aby dowiedzieć się więcej o DCMs i sposobach tworzenia własnych modeli, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu Visual Studio Code](tutorial-pnp-visual-studio-code.md)
