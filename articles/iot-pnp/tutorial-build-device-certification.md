---
title: Utwórz urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji | Microsoft Docs
description: Jako deweloper urządzenia zapoznaj się z informacjami na temat tworzenia urządzenia usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80239906"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Utwórz urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji

W tym samouczku opisano, jak jako deweloper urządzenia można utworzyć urządzenie usługi IoT Plug and Play w wersji zapoznawczej, które jest gotowe do certyfikacji.

Testy certyfikacji sprawdzają, czy:

- Twój kod urządzenia IoT Plug and Play jest zainstalowany na urządzeniu.
- Kod urządzenia IoT Plug and Play jest tworzony przy użyciu zestawu Azure IoT SDK.
- Kod urządzenia obsługuje [IoT Hub Device Provisioning Service platformy Azure](../iot-dps/about-iot-dps.md).
- Kod urządzenia implementuje interfejs informacji o urządzeniu.
- Model możliwości i kod urządzenia pracują z IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Narzędzia Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack

Aby utworzyć urządzenie szybkiego startu dla systemu Windows, należy również ukończyć [Korzystanie z modelu możliwości urządzenia](quickstart-create-pnp-device-windows.md) . Przewodnik Szybki Start przedstawia sposób konfigurowania środowiska deweloperskiego przy użyciu programu Vcpkg i tworzenia przykładowego projektu.

## <a name="store-a-capability-model-and-interfaces"></a>Przechowywanie modelu możliwości i interfejsów

W przypadku urządzeń z Plug and Play IoT należy utworzyć model możliwości i interfejsy, które definiują możliwości urządzenia jako pliki JSON.

Te pliki JSON można przechowywać w trzech różnych lokalizacjach:

- Repozytorium modelu publicznego.
- Repozytorium modelu firmy.
- Na urządzeniu.

Obecnie w celu certyfikowania urządzenia pliki muszą być przechowywane w repozytorium modelu firmy lub w repozytorium modelu publicznego.

## <a name="include-the-required-interfaces"></a>Dołącz wymagane interfejsy

Aby przekazać proces certyfikacji, należy uwzględnić i zaimplementować interfejs **informacji o urządzeniu** w modelu możliwości. Ten interfejs ma następujące identyfikatory:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Jeśli ukończono [Przewodnik Szybki Start: korzystanie z modelu możliwości urządzenia do utworzenia urządzenia](quickstart-create-pnp-device-windows.md), interfejs **informacji o urządzeniu** został już uwzględniony w modelu.

Aby dołączyć Interfejs **informacji o urządzeniu** do modelu urządzenia, Dodaj identyfikator interfejsu do `implements` właściwości modelu możliwości:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Aby wyświetlić interfejs **informacji o urządzeniu** w vs Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **Otwórz repozytorium Plug and Play "IoT model** ". Wybierz pozycję **Otwórz repozytorium modelu publicznego**. Repozytorium modelu publicznego zostanie otwarte w VS Code.

1. W repozytorium modelu publicznego wybierz kartę **interfejsy** , wybierz ikonę filtru, a następnie wprowadź **Informacje o urządzeniu** w polu Filtr.

1. Aby utworzyć lokalną kopię interfejsu **informacji o urządzeniu** , zaznacz ją na liście filtrowane, a następnie wybierz pozycję **Pobierz**. VS Code Wyświetla plik interfejsu.

Aby wyświetlić interfejs **informacji o urządzeniu** przy użyciu interfejsu wiersza polecenia platformy Azure:

1. [Zainstaluj rozszerzenie interfejsu wiersza polecenia usługi Azure IoT](howto-install-pnp-cli.md).

1. Poniższe polecenie interfejsu wiersza polecenia platformy Azure umożliwia wyświetlenie interfejsu z IDENTYFIKATORem interfejsu informacji o urządzeniu:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Aby uzyskać więcej informacji, zobacz [Instalowanie i używanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Aktualizowanie kodu urządzenia

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Włączanie aprowizacji urządzeń za pomocą usługi Azure IoT Device Provisioning (DPS)

Aby przeprowadzić certyfikację urządzenia, należy włączyć obsługę administracyjną w [usłudze Azure IoT Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Aby dodać możliwość korzystania z funkcji DPS, można wygenerować procedurę tworzenia kodu w języku C w programie VS Code. Wykonaj następujące kroki:

1. Otwórz folder z plikiem DCM w VS Code, użyj **kombinacji klawiszy Ctrl + Shift + P** , aby otworzyć paletę poleceń, wprowadź **Plug and Play IoT**i wybierz opcję **Generuj procedurę tworzenia kodu urządzenia**.

1. Wybierz plik DCM, którego chcesz użyć do wygenerowania szczątkowego kodu urządzenia.

1. Wprowadź nazwę projektu, na przykład **sample_device**. Jest to nazwa aplikacji urządzenia.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **za pośrednictwem usługi DPS (usługa Device Provisioning) klucz symetryczny** jako metodę połączenia.

1. Wybierz **projekt CMAKE w systemie Windows** jako szablon projektu.

1. Wybierz pozycję **Via Vcpkg** , aby dołączać zestaw SDK urządzeń.

1. VS Code otwiera nowe okno z wygenerowanymi plikami zastępczymi kodu urządzenia.

## <a name="build-and-run-the-code"></a>Kompiluj i uruchamiaj kod

Pakiet Vcpkg jest używany do kompilowania wygenerowanej procedury tworzenia kodu urządzenia. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. `cmake` Utwórz podkatalog w `sample_device` folderze i przejdź do tego folderu:

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

1. Po pomyślnym zakończeniu kompilacji wprowadź parametry aplikacji DPS (dział**identyfikatora**usługi DPS, **klucz symetryczny**, **Identyfikator urządzenia**). Aby uzyskać poświadczenia z portalu certyfikacji, zobacz [łączenie i testowanie urządzenia Plug and Play IoT](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Implementuj interfejsy standardowe

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementowanie informacji o modelu i interfejsów informacji zestawu SDK

Zestaw SDK urządzenia usługi Azure IoT implementuje informacje o modelu i interfejsy informacji zestawu SDK. Jeśli używasz funkcji generowania kodu w VS Code, kod urządzenia używa zestawu SDK Plug and Play urządzenia IoT.

Jeśli zdecydujesz się nie używać zestawu SDK urządzeń Azure IoT, możesz użyć kodu źródłowego zestawu SDK jako odniesienia do własnej implementacji.

#### <a name="implement-the-device-information-interface"></a>Implementowanie interfejsu informacji o urządzeniu

Zaimplementuj Interfejs **informacji o urządzeniu** na urządzeniu i podaj informacje dotyczące urządzenia z urządzenia w czasie wykonywania.

Możesz użyć przykładowej implementacji interfejsu **informacji o urządzeniu** dla systemu [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) jako odwołania.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Zaimplementuj wszystkie możliwości zdefiniowane w modelu

Podczas certyfikacji urządzenie jest testowane programowo, aby upewnić się, że implementuje możliwości zdefiniowane w interfejsach. Użyj kodu stanu HTTP 501, aby odpowiedzieć na żądania właściwości odczytu i zapisu, jeśli urządzenie nie implementuje ich.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzono urządzenie Plug and Play IoT na potrzeby certyfikacji, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Dowiedz się, jak certyfikowanie urządzenia](tutorial-certification-test.md)
