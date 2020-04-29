---
title: Współpracuj z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej podłączonym do rozwiązania Azure IoT | Microsoft Docs
description: Użyj języka Java, aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964638"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Szybki Start: współdziałanie z urządzeniem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z możliwościami urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać języka Java do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest podłączony do rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz mieć Java SE 8 na swoim komputerze deweloperskim. Należy również zainstalować Maven 3.

Aby uzyskać szczegółowe informacje na temat sposobu ich konfiguracji, zobacz [Przygotowywanie środowiska deweloperskiego](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) w Microsoft Azure zestawie SDK urządzeń IoT dla języka Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start użyjesz przykładowego czujnika środowiska, który jest pisany w języku Java jako urządzenie Plug and Play IoT. Poniższe instrukcje przedstawiają sposób instalowania i uruchamiania urządzenia:

1. Otwórz okno terminalu w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [przykłady usługi Azure IoT for Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub do tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia_ . Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-Java/Digital-Twin/Samples/Device/JdkSample** . Zainstaluj wymagane biblioteki i skompiluj aplikację symulowanego urządzenia, uruchamiając następujące polecenie:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom następujące polecenie, aby uruchomić przykład z folderu urządzenia.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Zobaczysz komunikaty informujące, że urządzenie jest połączone, wykonując różne czynności konfiguracyjne i oczekujące na aktualizacje usługi, a następnie dzienniki telemetrii. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków. Nie zamykaj tego terminala. będzie on potrzebny później w celu potwierdzenia, że także zadziałały również przykłady usługi.

## <a name="run-the-sample-solution"></a>Uruchom przykładowe rozwiązanie

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku Java do współpracy z przykładowym urządzeniem.

1. Otwórz inne okno terminalu (będzie to Terminal _usługi_ ). Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-java\digital-twin\Samples\service\JdkSample** .

1. Zainstaluj wymagane biblioteki i skompiluj próbkę usługi, uruchamiając następujące polecenie:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Skonfiguruj _Parametry połączenia usługi IoT Hub_ i _Identyfikator urządzenia_ , aby umożliwić usłudze łączenie się z obydwoma:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po podłączeniu _urządzenia_ do terminalu jeden z komunikatów wyjściowych był następujący komunikat informujący o jego stanie online. `state` Właściwość, która służy do wskazywania, czy urządzenie jest w trybie online, jest _prawdziwe_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Przejdź do terminalu _usługi_ i użyj następującego polecenia, aby uruchomić przykład usługi do odczytywania informacji o urządzeniu:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. W danych wyjściowych terminalu _usług_ przewiń do `environmentalSensor` składnika. Zobaczysz, że `state` właściwość została zgłoszona jako _prawda_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Użyj następującego polecenia, aby uruchomić przykład usługi do aktualizacji właściwości:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. Dane wyjściowe terminalu _usługi_ przedstawiają zaktualizowane informacje o urządzeniu. Przewiń do `environmentalSensor` składnika, aby wyświetlić nową wartość jasności 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Wróć do terminalu _usługi_ i uruchom poniższe polecenie, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. W danych wyjściowych terminalu _usługi_ pod `environmentalSensor` składnikiem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: ukończenie aktualizacji może zająć trochę czasu. Ten krok można powtórzyć do momentu rzeczywistego przetworzenia przez urządzenie aktualizacji właściwości.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Przejdź do terminalu _usługi_ i ustaw następujące zmienne, aby zdefiniować polecenie do wywołania:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Użyj następującego polecenia, aby uruchomić przykład usługi do wywoływania polecenia:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Dane wyjściowe w terminalu _usługi_ powinny zawierać następujące potwierdzenie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w rozwiązaniu IoT. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
