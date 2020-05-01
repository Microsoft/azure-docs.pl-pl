---
title: Tworzenie i testowanie urządzenia IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Jako deweloper urządzenia zapoznaj się z tematem jak używać VS Code do tworzenia i testowania nowego modelu możliwości urządzenia dla urządzenia IoT Plug and Play Preview.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76719725"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Samouczek: Tworzenie i testowanie modelu możliwości urządzenia przy użyciu Visual Studio Code

W tym samouczku pokazano, jak deweloper urządzenia używa Visual Studio Code do tworzenia _modelu możliwości urządzeń_. Możesz użyć modelu do generowania kodu szkieletowego do uruchomienia na urządzeniu, które nawiązuje połączenie z wystąpieniem usługi Azure IoT Hub w chmurze.

Sekcja w tym samouczku opisująca sposób tworzenia generowanego kodu szkieletowego przyjęto założenie, że używasz systemu Windows.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modelu możliwości urządzenia
> * Generuj kod urządzenia szkieletowego z modelu
> * Implementowanie wycinków w wygenerowanym kodzie
> * Uruchamianie kodu w celu przetestowania interakcji z Centrum IoT

## <a name="prerequisites"></a>Wymagania wstępne

Do pracy z modelem możliwości urządzenia w tym samouczku potrzebne są:

* [Visual Studio Code](https://code.visualstudio.com/download): vs Code jest dostępny dla wielu platform
* [Narzędzia Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack. Wykonaj następujące kroki, aby zainstalować pakiet rozszerzeń w VS Code:

    1. W VS Code wybierz kartę **rozszerzenia** .
    1. Wyszukaj **narzędzia Azure IoT Tools**.
    1. Wybierz pozycję **Zainstaluj**.

Do utworzenia wygenerowanego kodu C w systemie Windows w tym samouczku potrzebne są:

* [Narzędzia kompilacji dla programu Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) z **narzędziami kompilacji C++** i obciążeniami **składników Menedżera pakietów NuGet** . Lub jeśli masz już [program Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 lub 2015 z zainstalowanymi takimi samymi obciążeniami.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Aby przetestować kod urządzenia w tym samouczku, potrzebne są:

* [Eksplorator IoT Azure](https://github.com/Azure/azure-iot-explorer/releases).
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelowanie urządzenia

Do utworzenia modelu możliwości urządzenia służy _Język definicji Digital bliźniaczy_ . Model zwykle składa się z wielu plików definicji _interfejsu_ i jednego pliku modelu. **Narzędzia Azure IoT Tools for vs Code** zawierają narzędzia ułatwiające tworzenie i edytowanie tych plików JSON.

### <a name="create-the-interface-file"></a>Tworzenie pliku interfejsu

Aby utworzyć plik interfejsu, który definiuje możliwości urządzenia IoT w VS Code:

1. Utwórz folder o nazwie **devicemodel**.

1. Uruchom VS Code i **naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **IoT plug & Play: Create Interface** .

1. Przejdź do folderu **devicemodel** i wybierz utworzony folder.

1. Następnie wprowadź **EnvironmentalSensor** jako nazwę interfejsu i naciśnij klawisz **Enter**. VS Code tworzy przykładowy plik interfejsu o nazwie **EnvironmentalSensor. Interface. JSON**.

1. Zastąp zawartość tego pliku następującym kodem JSON i Zastąp `{your name}` w `@id` polu unikatową wartością. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia. Aby uzyskać więcej informacji, zobacz [Format identyfikatora cyfrowego przędzy](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Identyfikator interfejsu musi być unikatowy, aby można było zapisać interfejs w repozytorium:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Ten interfejs definiuje właściwości urządzenia, takie jak **Nazwa klienta**, typy telemetrii, takie jak **temperatura**i polecenia, takie jak **wlaczanie**.

1. Dodaj funkcję polecenia o nazwie **Blink** na końcu pliku interfejsu. Pamiętaj, aby dodać przecinek przed dodaniem polecenia. Spróbuj wpisać definicję, aby zobaczyć, jak technologia IntelliSense, Autouzupełnianie i walidacja mogą pomóc w edytowaniu definicji interfejsu:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Zapisz plik.

### <a name="create-the-model-file"></a>Utwórz plik modelu

Plik modelu Określa interfejsy, które implementuje urządzenie Plug and Play IoT. W modelu znajdują się zwykle co najmniej dwa interfejsy — jeden lub więcej, które definiują konkretne możliwości urządzenia, oraz standardowy interfejs, który wszystkie urządzenia Plug and Play IoT muszą zaimplementować.

Aby utworzyć plik modelu określający interfejsy implementowane przez urządzenie IoT Plug and Play w VS Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **IoT plug & Play: Create możliwości model** . Następnie wprowadź **SensorboxModel** jako nazwę modelu. VS Code tworzy przykładowy plik interfejsu o nazwie **SensorboxModel. capabilitymodel. JSON**.

1. Zastąp zawartość tego pliku następującym kodem `{your name}` JSON i Zastąp wartości w `@id` polu i w `EnvironmentalSensor` interfejsie tą samą wartością, która została użyta w pliku **EnvironmentalSensor. Interface. JSON** . Identyfikator interfejsu musi być unikatowy, aby można było zapisać interfejs w repozytorium:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Model definiuje urządzenie implementujące interfejs **EnvironmentalSensor** oraz standardowy interfejs **DeviceInformation** .

1. Zapisz plik.

### <a name="download-the-deviceinformation-interface"></a>Pobierz interfejs DeviceInformation

Aby można było wygenerować kod szkieletowy z modelu, należy utworzyć lokalną kopię **DeviceInformation** z *repozytorium modelu publicznego*. Repozytorium modelu publicznego zawiera już Interfejs **DeviceInformation** .

Aby pobrać Interfejs **DeviceInformation** z repozytorium modelu publicznego przy użyciu vs Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play**, wybierz polecenie **Otwórz repozytorium model** , a następnie wybierz pozycję **Otwórz repozytorium modelu publicznego**.

1. Wybierz pozycję **interfejsy**, a następnie wybierz pozycję Interfejs informacji o `urn:azureiot:DeviceManagement:DeviceInformation:1`urządzeniu o identyfikatorze, a następnie wybierz pozycję **Pobierz**.

Masz teraz trzy pliki wchodzące w skład modelu możliwości urządzenia:

* plik urn_azureiot_DeviceManagement_DeviceInformation_1. Interface. JSON
* EnvironmentalSensor. Interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>Publikowanie modelu

Aby narzędzie Azure IoT Explorer odczytało model możliwości urządzenia, należy go opublikować w repozytorium firmy. Aby można było publikować z VS Code, potrzebne są parametry połączenia dla repozytorium firmy:

1. Przejdź do [portalu Azure Certified for IoT](https://aka.ms/ACFI).

1. Użyj swojego _konta służbowego_ firmy Microsoft, aby zalogować się do portalu.

1. Wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**.

1. Skopiuj parametry połączenia.

Aby otworzyć repozytorium firmowe w VS Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **IoT plug & Play: Open model Repository** .

1. Wybierz pozycję **Otwórz repozytorium modeli organizacyjnych** i wklej w parametrach połączenia.

1. Naciśnij klawisz **Enter** , aby otworzyć repozytorium firmy.

Aby opublikować model możliwości urządzenia i interfejsy w repozytorium firmy:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz polecenie **IoT plug & Play: Prześlij pliki do repozytorium modeli** .

1. Wybierz pliki **EnvironmentalSensor. Interface. JSON** i **SensorboxModel. capabilitymodel. JSON** , a następnie wybierz **przycisk OK**.

Twoje pliki są teraz przechowywane w repozytorium firmy.

## <a name="generate-code"></a>Generowanie kodu

**Narzędzi Azure IoT Tools for vs Code** można użyć do generowania szkieletowego kodu C z modelu. Aby wygenerować kod szkieletu w VS Code:

1. **Naciśnij klawisze Ctrl + Shift + P** , aby otworzyć paletę poleceń.

1. Wprowadź **Plug and Play** a następnie wybierz pozycję **IoT plug & Play: Generate Command Code stub** .

1. Wybierz plik modelu możliwości **SensorboxModel. capabilitymodel. JSON** .

1. Wprowadź **sensorbox_app** jako nazwę projektu.

1. Wybierz **ANSI C** jako język.

1. Wybierz pozycję **przez IoT Hub parametry połączenia urządzenia** , aby nawiązać połączenie.

1. Wybierz **projekt CMAKE w systemie Windows** jako szablon projektu.

1. Wybierz pozycję **Via Vcpkg** , aby uwzględnić zestaw SDK urządzeń.

VS Code generuje kod szkieletu C i zapisuje pliki w folderze **sensorbox_app** w folderze **ModelCode** . VS Code otwiera nowe okno zawierające pliki wygenerowanego kodu.

## <a name="update-the-generated-code"></a>Aktualizowanie wygenerowanego kodu

Aby można było skompilować i uruchomić kod, należy zaimplementować właściwości użyto metod zastępczych, dane telemetryczne i polecenia.

Aby zapewnić implementacje dla kodu użyto metod zastępczych w VS Code:

1. Otwórz plik **SensorboxModel_impl. c** .

1. Dodaj kod w celu zaimplementowania funkcji użyto metod zastępczych.

1. Zapisz zmiany.

## <a name="build-the-code"></a>Kompilowanie kod

Przed uruchomieniem kodu w celu przetestowania urządzenia Plug and Play IoT przy użyciu usługi Azure IoT Hub należy skompilować kod.

Postępuj zgodnie z instrukcjami w pliku **README.MD** w folderze **sensorbox_app** , aby skompilować i uruchomić kod w systemie Windows. Poniższa sekcja zawiera instrukcje dotyczące pobierania parametrów połączenia urządzenia do użycia podczas uruchamiania kodu urządzenia.

## <a name="test-the-code"></a>Przetestuj kod

Po uruchomieniu kodu program nawiązuje połączenie z IoT Hub i uruchamia wysyłanie przykładowych danych telemetrycznych i wartości właściwości. Urządzenie odpowiada również na polecenia wysyłane z IoT Hub. Aby sprawdzić to zachowanie:

1. Aby utworzyć centrum IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Dodaj urządzenie do centrum IoT Hub i Pobierz jego parametry połączenia:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Zanotuj parametry połączenia.

1. W wierszu polecenia przejdź do folderu **Azure-IoT-SDK-c** , w którym SKOMPILOWANO zestaw SDK i przykłady. Następnie przejdź do folderu **cmake\\sensorbox_app\\Release** .

1. Uruchom następujące polecenie:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Za pomocą narzędzia Azure IoT Explorer można korzystać z urządzenia Plug and Play IoT podłączonego do centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Instalowanie i korzystanie z programu Azure IoT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już wbudowaną Plug and Play IoT do certyfikacji, zapoznaj się z tematem:

> [!div class="nextstepaction"]
> [Tworzenie urządzenia gotowego do certyfikacji](tutorial-build-device-certification.md)
