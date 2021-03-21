---
title: Samouczek — łączenie ogólnego modułu usługi Azure IoT Plug and Play | Microsoft Docs
description: Samouczek — używanie przykładowego kodu urządzenia w języku C# Plug and Play w module ogólnym.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831218"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Samouczek: łączenie modułu IoT Plug and Play (C#)

W tym samouczku przedstawiono sposób łączenia ogólnego [modułu](../iot-hub/iot-hub-devguide-module-twins.md)IoT Plug and Play.

Urządzenie jest urządzeniem Plug and Play IoT, jeśli publikuje jego identyfikator modelu podczas nawiązywania połączenia z usługą IoT Hub i implementuje właściwości i metody opisane w modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) identyfikowane przez identyfikator modelu. Aby dowiedzieć się więcej o tym, jak urządzenia używają DTDL i identyfikatora modelu, zobacz [przewodnik dewelopera IoT Plug and Play](./concepts-developer-guide-device.md). Moduły używają identyfikatorów modelu i modeli DTDL w taki sam sposób.

Aby zademonstrować sposób implementacji modułu IoT Plug and Play, w tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Dodaj urządzenie z modułem do centrum IoT Hub.
> * Konwertowanie przykładowego urządzenia z termostatem na język C# do modułu ogólnego.
> * Użyj zestawu SDK usługi, aby móc korzystać z modułu.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Usługi [git](https://git-scm.com/download/).

Za pomocą narzędzia Azure IoT Explorer Dodaj nowe urządzenie o nazwie **My-module-Device** do centrum IoT Hub.

Dodaj moduł o nazwie **My-module** do **urządzenia My-module**:

1. W narzędziu Azure IoT Explorer przejdź do urządzenia **My-module-Device** .

1. Wybierz pozycję **tożsamość modułu**, a następnie wybierz pozycję **+ Dodaj**.

1. Wprowadź **My-module** jako nazwę tożsamości modułu, a następnie wybierz pozycję **Zapisz**.

1. Na liście tożsamości modułów wybierz pozycję **mój-module**. Następnie skopiuj podstawowe parametry połączenia. Te parametry połączenia modułu są używane w dalszej części tego samouczka.

1. Wybierz kartę **sznurki modułu** i zwróć uwagę, że nie ma żądanych lub zgłoszonych właściwości:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Pobieranie kodu

Jeśli jeszcze tego nie zrobiono, Sklonuj repozytorium GitHub zestawu SDK języka C# dla usługi Azure IoT Hub Device na komputerze lokalnym:

Otwórz wiersz polecenia w wybranym folderze. Użyj poniższego polecenia, aby sklonować repozytorium [usługi Azure IoT C# przykłady](https://github.com/Azure-Samples/azure-iot-samples-csharp) w tej lokalizacji:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Przygotowywanie projektu

Aby otworzyć i przygotować przykładowy projekt:

1. Otwórz plik projektu *Azure-IoT-SDK-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* w programie Visual Studio 2019.

1. W programie Visual Studio przejdź do **Właściwości programu Project > termostat > Debuguj**. Następnie Dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Parametry połączenia |
    | IOTHUB_MODULE_CONNECTION_STRING | Parametry połączenia modułu, dla których wykonano adnotację wcześniej |

    Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>modyfikować kod

Aby zmodyfikować kod, który będzie działał jako moduł, a nie na urządzeniu:

1. W programie Visual Studio Otwórz *parametr. cs* i Zmodyfikuj wiersz ustawiający zmienną **PrimaryConnectionString** w następujący sposób:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. W programie Visual Studio Otwórz *program programy. cs* i Zastąp siedem wystąpień klasy klasą `DeviceClient` `ModuleClient` .

    > [!TIP]
    > Używanie funkcji wyszukiwania i zamiany w programie Visual Studio z **wielkością liter** i **Dopasowanie całych wyrazów** z włączoną opcją Zamień na `DeviceClient` `ModuleClient` .

1. W programie Visual Studio Otwórz program *termostat. cs* i Zastąp oba wystąpienia klasy klasą w `DeviceClient` `ModuleClient` następujący sposób.

1. Zapisz zmiany w zmodyfikowanych plikach.

Jeśli uruchomisz kod, a następnie użyjesz programu Azure IoT Explorer do wyświetlenia zaktualizowanego sznurka modułu, zobaczysz zaktualizowaną sznurek urządzenia z IDENTYFIKATORem modelu i zaraportowanym modułem:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Korzystanie z modułu urządzenia

Zestawy SDK usług umożliwiają pobranie identyfikatora modelu połączonych urządzeń i modułów Plug and Play IoT. Zestawy SDK usługi umożliwiają ustawianie właściwości zapisywalnych i Wywoływanie poleceń:

1. W innym wystąpieniu programu Visual Studio Otwórz projekt *Azure-IoT-SDK-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* .

1. W programie Visual Studio przejdź do **Właściwości programu Project > termostat > Debuguj**. Następnie Dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | My-module-Device |
    | IOTHUB_CONNECTION_STRING | Wartość zanotowana po zakończeniu [konfigurowania środowiska](set-up-environment.md) |

    > [!TIP]
    > Parametry połączenia usługi IoT Hub można również znaleźć w narzędziu Azure IoT Explorer.

1. Otwórz plik *program. cs* i Zmodyfikuj wiersz, który wywołuje polecenie w następujący sposób:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. W pliku *program. cs* Zmodyfikuj wiersz, który pobiera sznurki urządzenia w następujący sposób:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Upewnij się, że przykład klienta modułu jest nadal uruchomiony, a następnie uruchom ten przykład usługi. Dane wyjściowe z przykładu usługi przedstawiają Identyfikator modelu z sznurka urządzenia i wywołanie polecenia:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    Dane wyjściowe z klienta modułu przedstawiają odpowiedź programu obsługi poleceń:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Konwertuj na moduł IoT Edge

Aby przekonwertować ten przykład w taki sposób, aby działał jako moduł IoT Edge Plug and Play IoT, należy konteneryzowanie aplikację. Nie musisz wprowadzać żadnych dalszych zmian w kodzie. Zmienna środowiskowa parametrów połączenia jest wprowadzana przez środowisko uruchomieniowe IoT Edge podczas uruchamiania. Aby dowiedzieć się więcej, zobacz [Korzystanie z programu Visual Studio 2019 do tworzenia i debugowania modułów dla Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Aby dowiedzieć się, jak wdrożyć moduł kontenerów, zobacz:

* [Uruchom Azure IoT Edge na Ubuntu Virtual Machines](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemach Linux opartych na Debian](../iot-edge/how-to-install-iot-edge.md).

Za pomocą narzędzia Azure IoT Explorer można zobaczyć:

* Identyfikator modelu urządzenia IoT Edgeowego w postaci dwuosiowego modułu.
* Dane telemetryczne z urządzenia IoT Edge.
* Aktualizacje właściwości sznurka IoT Edge module wyzwalające powiadomienia Plug and Play IoT.
* Moduł IoT Edge reaguje na polecenia Plug and Play IoT.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak połączyć urządzenie z usługą IoT Plug and Play z modułami do centrum IoT. Aby dowiedzieć się więcej na temat modeli urządzeń IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Przewodnik dewelopera modelowania Plug and Play IoT](./concepts-developer-guide-device.md)