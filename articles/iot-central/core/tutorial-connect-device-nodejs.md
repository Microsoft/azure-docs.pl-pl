---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Node.js z usługą Azure IoT Central | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak jako deweloper urządzeń połączyć urządzenie z uruchomieniem aplikacji klienckiej Node.js z aplikacją Azure IoT Central. Szablon urządzenia można utworzyć, importując model możliwości urządzenia i dodaj widoki, które umożliwiają interakcję z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758205"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak jako deweloper urządzeń połączyć aplikację kliencką Node.js z aplikacją Azure IoT Central. Aplikacja Node.js symuluje zachowanie urządzenia czujnika środowiska. Przykładowego _modelu możliwości urządzenia_ służy do tworzenia _szablonu urządzenia_ w centrum IoT. Widoki są dodawanye do szablonu urządzenia, aby umożliwić operatorowi interakcję z urządzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportowanie modelu możliwości urządzenia w celu utworzenia szablonu urządzenia.
> * Dodawanie widoków domyślnych i niestandardowych do szablonu urządzenia.
> * Opublikuj szablon urządzenia i dodaj prawdziwe urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia Node.js i zobacz, jak łączy się z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzenia.
> * Wywołaj polecenia synchroniczne i asynchroniczne, aby sterować urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja Azure IoT Central utworzona przy użyciu szablonu **aplikacji niestandardowej.** Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Komputer dewelopera z [zainstalowanym node.js](https://nodejs.org/) w wersji 10.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Instrukcje w tym samouczku zakładają, że używasz polecenia **węzła** w wierszu polecenia systemu Windows. Jednak można użyć Node.js w wielu innych systemach operacyjnych.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują, jak utworzyć aplikację kliencką Node.js, która łączy się z rzeczywistym urządzeniem dodanym do aplikacji. Ta aplikacja Node.js symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia `environmental-sensor` przejdź do folderu utworzonego wcześniej.

1. Aby zainicjować projekt Node.js i zainstalować wymagane zależności, uruchom następujące polecenia - zaakceptuj `npm init`wszystkie opcje domyślne po uruchomieniu:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Utwórz plik o nazwie **environmentalSensor.js** w folderze. `environmental-sensor`

1. Dodaj następujące `require` instrukcje na początku pliku **environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Zaktualizuj `{your Scope ID}` `{your Device ID}`symbole `{your Primary Key}` zastępcze , a także wartościami, które zostały wcześniej zanotowane. W tym przykładzie `targetTemperature` zainicjować do zera, można użyć bieżącego odczytu z urządzenia lub wartość z bliźniaczej reprezentacji urządzenia.

1. Aby wysłać symulowane dane telemetryczne do aplikacji Usługi Azure IoT Central, dodaj do pliku następującą funkcję:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Nazwy elementów telemetrycznych`temp` `humid`( i ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Aby wysłać właściwości bliźniaczej reprezentacji urządzenia do aplikacji Usługi Azure IoT Central, dodaj do pliku następującą funkcję:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    Usługa IoT Central używa bliźniąt bliźniąt urządzeń do synchronizowania wartości właściwości między urządzeniem a aplikacją IoT Central. Wartości właściwości urządzenia używają bliźniaczej reprezentacji urządzenia zgłoszonych właściwości. Zapisywalne właściwości używają zarówno bliźniaczej reprezentacji urządzenia zgłaszane i żądane właściwości.

1. Aby zdefiniować i obsłużyć zapisywalne właściwości, na które odpowiada urządzenie, dodaj następujący kod:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Gdy operator ustawia zapisowalne właściwości w aplikacji IoT Central, aplikacja używa bliźniaczej reprezentacji urządzenia żądanej właściwości, aby wysłać wartość do urządzenia. Urządzenie następnie odpowiada przy użyciu urządzenia bliźniaczej reprezentacji zgłaszane właściwości. Gdy aplikacja IoT Central odbiera zgłoszoną wartość właściwości, aktualizuje widok właściwości o stan **zsynchronizowany**.

    Nazwy właściwości (`name` i `brightness`) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujący kod do obsługi poleceń wysyłanych z aplikacji IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    `blink`Nazwy poleceń ( , `turnon` `turnoff`, `rundiagnostics`, i ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

    Obecnie usługa IoT Central nie używa schematu odpowiedzi zdefiniowanego w modelu możliwości urządzenia. Dla polecenia synchronicznego ładunku odpowiedzi może być dowolny prawidłowy JSON. W przypadku polecenia asynchronicznego urządzenie powinno natychmiast zwrócić odpowiedź 202, a następnie zgłoszoną aktualizację właściwości po zakończeniu pracy. Format zgłoszonej aktualizacji właściwości jest:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operator może wyświetlić ładunek odpowiedzi w historii poleceń.

1. Dodaj następujący kod, aby umożliwić nawiązanie połączenia z usługą Azure IoT Central i dołączyć funkcje do kodu klienta:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji Node.js

Aby uruchomić aplikację kliencką urządzenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
node environmentalSensor.js
```

Możesz zobaczyć, że urządzenie łączy się z aplikacją Azure IoT Central i rozpoczyna wysyłanie danych telemetrycznych:

![Uruchamianie aplikacji klienckiej](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Możesz zobaczyć, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

![Obserwuj aplikację kliencką](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Następne kroki

Jako deweloper urządzeń, teraz, gdy już znasz podstawy tworzenia urządzenia przy użyciu node.js, niektóre sugerowane następne kroki są następujące:

- Dowiedz się, jak połączyć rzeczywiste urządzenie z centrum IoT w [artykule Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central.](./howto-connect-devkit.md)
- Przeczytaj artykuł [Nawiązanie połączenia z usługą Azure IoT Central,](./concepts-get-connected.md) aby dowiedzieć się więcej o rejestrowaniu urządzeń w usłudze IoT Central i sposobie zabezpieczania połączeń urządzeń przez usługę IoT Central.

Jeśli wolisz kontynuować tworzenie samouczków W centrum IoT i dowiedzieć się więcej o tworzeniu rozwiązania Centrum IoT, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)
