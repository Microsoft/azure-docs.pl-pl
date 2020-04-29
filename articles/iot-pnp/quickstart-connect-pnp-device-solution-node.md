---
title: Współpracuj z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej podłączonym do rozwiązania Azure IoT | Microsoft Docs
description: Użyj środowiska Node. js, aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550744"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem (Node. js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z możliwościami urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać środowiska Node. js do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć program Node. js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start użyto przykładowego czujnika środowiska, który został zapisany w środowisku Node. js jako urządzenie Plug and Play IoT. Poniższe instrukcje przedstawiają sposób instalowania i uruchamiania urządzenia:

1. Otwórz okno terminalu w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [przykłady usługi Azure IoT dla repozytorium GitHub środowiska Node. js](https://github.com/azure-samples/azure-iot-samples-node) do tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia_ . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Device** . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom próbkę za pomocą następującego polecenia:

    ```cmd/sh
    node sample_device.js
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Oznacza to, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminala. będzie on potrzebny później w celu potwierdzenia, że także zadziałały również przykłady usługi.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku Node. js do współpracy z przykładowym urządzeniem.

1. Otwórz inne okno terminalu (będzie to Terminal _usługi_ ). Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _Parametry połączenia usługi IoT Hub_ , aby umożliwić usłudze łączenie się z nią:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po podłączeniu _urządzenia_ do terminalu zobaczysz następujący komunikat wskazujący jego stan online:

    ```cmd/sh
    reported state property as online
    ```

1. W folderze **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** otwórz plik **get_digital_twin. js**. Zastąp `<DEVICE_ID_GOES_HERE>` symbol ZASTĘPCZy identyfikatorem urządzenia i Zapisz plik.

1. Przejdź do terminalu _usługi_ i użyj następującego polecenia, aby uruchomić przykład do odczytu informacji o urządzeniu:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. W danych wyjściowych terminalu _usług_ przewiń do `environmentalSensor` składnika. Zobaczysz, że `state` właściwość została zgłoszona jako _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości z możliwością zapisu

1. Otwórz plik **update_digital_twin_property. js**.

1. Na początku pliku istnieje zestaw stałych definiowany z dużymi symbolami zastępczymi. Zastąp `<DEVICE_ID_GOES_HERE>` symbol zastępczy rzeczywistym identyfikatorem urządzenia, zaktualizuj pozostałe stałe przy użyciu następujących wartości, a następnie Zapisz plik:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Przejdź do terminalu _usługi_ i użyj następującego polecenia, aby uruchomić przykład do aktualizacji właściwości:

    ```cmd/sh
    node update_digital_twin_property.js
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
            "value": "online"
          }
        }
      }
    }
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Wróć do terminalu _usługi_ i uruchom poniższe polecenie, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. W danych wyjściowych terminalu _usługi_ pod `environmentalSensor` składnikiem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: ukończenie aktualizacji może zająć trochę czasu. Ten krok można powtórzyć do momentu rzeczywistego przetworzenia przez urządzenie aktualizacji właściwości.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Otwórz plik **invoke_command. js**.

1. Na początku pliku Zastąp `<DEVICE_ID_GOES_HERE>` symbol zastępczy rzeczywistym identyfikatorem urządzenia. Zaktualizuj pozostałe stałe przy użyciu następujących wartości, a następnie Zapisz plik:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Przejdź do terminalu _usługi_ . Użyj następującego polecenia, aby uruchomić próbkę wywołującą polecenie:

    ```cmd/sh
    node invoke_command.js
    ```

1. Dane wyjściowe w terminalu _usługi_ powinny zawierać następujące potwierdzenie:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
