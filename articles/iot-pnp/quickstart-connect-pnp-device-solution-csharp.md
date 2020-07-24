---
title: Korzystanie z urządzenia z usługą IoT Plug and Play Preview połączonego z rozwiązaniem — C# | Microsoft Docs
description: Użyj języka C# (.NET), aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 226acdda94eb88825d60e35d48bfdd476ad1339c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044087"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z możliwościami urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka C# (z platformą .NET) do łączenia się z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować platformę .NET Core (2. x. x lub 3. x. x) na komputerze deweloperskim. Możesz pobrać preferowaną wersję zestaw .NET Core SDK dla wielu platform [pobieranych z platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Możesz sprawdzić wersję programu .NET, która znajduje się na komputerze deweloperskim, uruchamiając następujące polecenie w lokalnym oknie terminalu: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start użyto przykładowego czujnika środowiska, który jest pisany w języku C# jako urządzenie Plug and Play IoT. Poniższe instrukcje przedstawiają sposób instalowania i uruchamiania urządzenia:

1. Otwórz okno terminalu w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować z repozytorium GitHub [przykłady usługi Azure IoT dla języka C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) do tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia_ . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-CSharp/digitaltwin/Samples/Device/EnvironmentalSensorSample** .

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Utwórz wymagane pakiety i uruchom próbkę za pomocą następującego polecenia:

    ```cmd\sh
        dotnet run
    ```

1. Zobaczysz komunikat informujący o tym, że urządzenie zostało pomyślnie zarejestrowane i oczekuje na aktualizacje z chmury. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Nie zamykaj tego terminala. będzie on potrzebny później w celu potwierdzenia, że także zadziałały również przykłady usługi.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku C# do współpracy z przykładowym urządzeniem.

1. Otwórz inne okno terminalu (będzie to Terminal _usługi_ ). Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-CSharp/digitaltwin/Samples/Service** .

1. Skonfiguruj _Parametry połączenia usługi IoT Hub_ i _Identyfikator urządzenia_ , aby umożliwić usłudze łączenie się z obydwoma:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po podłączeniu _urządzenia_ do terminalu zobaczysz następujący komunikat wskazujący jego stan online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Przejdź do terminalu _usługi_ i użyj następujących poleceń, aby uruchomić przykład do odczytu informacji o urządzeniu:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. W danych wyjściowych terminalu _usług_ przewiń do `environmentalSensor` składnika. Zobaczysz, że `state` Właściwość, która służy do wskazywania, czy urządzenie jest w trybie online, zostało zgłoszone jako _prawdziwe_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości z możliwością zapisu

1. Przejdź do terminalu _usługi_ i ustaw następujące zmienne, aby zdefiniować właściwość, która ma zostać zaktualizowana:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Użyj następujących poleceń, aby uruchomić przykład do aktualizacji właściwości:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. Dane wyjściowe terminalu _usługi_ przedstawiają zaktualizowane informacje o urządzeniu. Przewiń do `environmentalSensor` składnika, aby wyświetlić nową wartość jasności 42.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Wróć do terminalu _usługi_ i uruchom poniższe polecenia, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. W danych wyjściowych terminalu _usługi_ pod `environmentalSensor` składnikiem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: ukończenie aktualizacji może zająć trochę czasu. Ten krok można powtórzyć do momentu rzeczywistego przetworzenia przez urządzenie aktualizacji właściwości.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Przejdź do terminalu _usługi_ i ustaw następujące zmienne, aby zdefiniować polecenie do wywołania:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Użyj następujących poleceń, aby uruchomić przykład służący do wywoływania polecenia:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Dane wyjściowe w terminalu _usługi_ powinny zawierać następujące potwierdzenie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
