---
title: Łączenie Plug and Play IoT przykład kod urządzenia C do IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj Plug and Play IoT przykład kod urządzenia C, który używa wielu składników i łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1873d2acb96c0c94c7e0d678e450596c60ca51fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575405"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>Samouczek: łączenie IoT Plug and Play aplikacji urządzeń składników działających w systemie Linux lub Windows do IoT Hub (C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla języka C. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Ten samouczek można wykonać w systemie Linux lub Windows. Polecenia powłoki w tym samouczku są zgodne z Konwencją systemu Linux dla separatorów ścieżek " `/` ", jeśli jesteś w systemie Windows, pamiętaj o zamianie tych separatorów dla " `\` ".

Wymagania wstępne różnią się w zależności od systemu operacyjnego:

### <a name="linux"></a>Linux

W tym samouczku przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym samouczku zostały przetestowane przy użyciu Ubuntu 18,04.

Aby wykonać kroki tego samouczka w systemie Linux, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Linux:

Zainstaluj oprogramowanie w **zatoce**, **git**, **CMAKE**i wszystkie wymagane zależności przy użyciu `apt-get` polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy wersja programu `cmake` jest powyżej **2.8.12** , a wersja **tej** wersji jest większa niż **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas [instalowania](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) programu Visual Studio dołączysz **programowanie klasyczne przy użyciu języka C++** .
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

## <a name="download-the-code"></a>Pobieranie kodu

Po zakończeniu [przewodnika Szybki Start: połączenie przykładowej aplikacji IoT Plug and Play Device działającej w systemie Linux lub Windows do IoT Hub (C)](quickstart-connect-device-c.md) , że kod został już pobrany.

W tym samouczku przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla usługi Azure IoT Hub.

Otwórz wiersz polecenia w wybranym folderze. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-and-run-the-code"></a>Kompiluj i uruchamiaj kod

Możesz skompilować i uruchomić kod przy użyciu programu Visual Studio lub `cmake` w wierszu polecenia.

### <a name="use-visual-studio"></a>Korzystanie z programu Visual Studio

1. Otwórz folder główny sklonowanego repozytorium. Po kilku sekundach obsługa **CMAKE** w programie Visual Studio tworzy wszystko, co jest potrzebne do uruchomienia i debugowania projektu.
1. Gdy program Visual Studio jest gotowy, w **Eksplorator rozwiązań**przejdź do przykładu *iothub_client/Samples/PnP/pnp_temperature_controller/*.
1. Kliknij prawym przyciskiem myszy plik *pnp_temperature_controller. c* i wybierz polecenie **Dodaj konfigurację debugowania**. Wybierz pozycję **domyślne**.
1. Program Visual Studio otwiera *launch.vs.js* pliku. Edytuj ten plik, jak pokazano w poniższym fragmencie kodu, aby ustawić wymagane zmienne środowiskowe. Po zakończeniu [konfigurowania środowiska dla przewodników Szybki Start i samouczków Plug and Play IoT](set-up-environment.md)zawarto zanotować identyfikator zakresu i klucz podstawowy rejestracji:

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Kliknij prawym przyciskiem myszy plik *pnp_temperature_controller. c* i wybierz pozycję **Ustaw jako element startowy**.
1. Aby śledzić wykonywanie kodu w programie Visual Studio, Dodaj punkt przerwania do `main` funkcji w pliku *pnp_temperature_controller. c* .
1. Można teraz uruchomić i debugować przykład z menu **debugowanie** .

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

### <a name="use-cmake-at-the-command-line"></a>Użycie `cmake` w wierszu polecenia

Aby skompilować przykład:

1. Utwórz podfolder _CMAKE_ w folderze głównym zestawu SDK sklonowanego urządzenia i przejdź do tego folderu:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby wygenerować i skompilować pliki projektu dla zestawu SDK i przykładów:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Aby uruchomić przykład:

1. W folderze _CMAKE_ przejdź do folderu, który zawiera plik wykonywalny, i uruchom go:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Ten przykład implementuje model z [wieloma składnikami](concepts-components.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

### <a name="iot-plug-and-play-helper-functions"></a>Funkcje pomocnika Plug and Play IoT

Dla tego przykładu kod używa niektórych funkcji pomocnika z folderu */typowe* :

*pnp_device_client_ll* zawiera metodę connect dla Plug and Play IoT z `model-id` dołączonym parametrem: `PnP_CreateDeviceClientLLHandle` .

*pnp_protocol*: zawiera funkcje pomocnika Plug and Play IoT:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Te funkcje pomocnika są ogólnie wystarczające do użycia w Twoim projekcie. Ten przykład korzysta z nich w trzech plikach, które odpowiadają każdemu składnikowi w modelu:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Na przykład w pliku *pnp_deviceinfo_component* `SendReportedPropertyForDeviceInformation` Funkcja używa dwóch funkcji pomocnika:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Każdy składnik w przykładzie jest zgodny z tym wzorcem.

### <a name="code-flow"></a>Przepływ kodu

`main`Funkcja inicjuje połączenie i wysyła identyfikator modelu:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Kod używa `PnP_CreateDeviceClientLLHandle` do nawiązywania połączenia z Centrum IoT, ustawiania `modelId` jako opcji i konfigurowania metody urządzenia oraz obsługi wywołań zwrotnych urządzeń z bezpośrednimi metodami i urządzeniami z systemem:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` zawiera również informacje o połączeniu. Zmienna środowiskowa **IOTHUB_DEVICE_SECURITY_TYPE** określa, czy przykład używa parametrów połączenia lub usługi Device Provisioning w celu nawiązania połączenia z Centrum IoT.

Gdy urządzenie wyśle Identyfikator modelu, jego stan zmieni się na urządzenie Plug and Play IoT.

W przypadku obsługi wywołania zwrotnego urządzenie reaguje na aktualizacje bliźniaczy i bezpośrednie wywołania metod:

* W przypadku wywołania zwrotnego z przędzki urządzenia `PnP_TempControlComponent_DeviceTwinCallback` `PnP_ProcessTwinData` funkcja wywołuje funkcję w celu przetworzenia danych. `PnP_ProcessTwinData` używa *wzorca odwiedzającego* , aby przeanalizować kod JSON, a następnie odwiedzać każdą właściwość, wywołując `PnP_TempControlComponent_ApplicationPropertyCallback` dla każdego elementu.

* W przypadku poleceń wywołania zwrotnego `PnP_TempControlComponent_DeviceMethodCallback` Funkcja używa funkcji pomocnika do analizowania nazw poleceń i składników:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    `PnP_TempControlComponent_DeviceMethodCallback`Funkcja następnie wywołuje polecenie na składniku:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

`main`Funkcja inicjuje właściwości tylko do odczytu wysyłane do centrum IoT Hub:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

`main`Funkcja wprowadza pętlę, aby zaktualizować dane dotyczące zdarzeń i danych telemetrycznych dla każdego składnika:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

`PnP_ThermostatComponent_SendTelemetry`Funkcja pokazuje, jak używać `PNP_THERMOSTAT_COMPONENT` struktury. Przykład używa tej struktury do przechowywania informacji o dwóch termostatach w kontrolerze temperatury. Kod używa funkcji, `PnP_CreateTelemetryMessageHandle` Aby przygotować komunikat i wysłać go:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

`main`Funkcja wreszcie niszczy różne składniki i zamyka połączenie z centrum.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play ze składnikami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dewelopera modelowania Plug and Play IoT](concepts-developer-guide-device-csharp.md)
