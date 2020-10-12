---
title: Samouczek — łączenie ogólnej aplikacji klienta Node.js z usługą Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką Node.js do aplikacji IoT Central platformy Azure. Szablon urządzenia można utworzyć przez zaimportowanie modelu możliwości urządzenia i dodanie widoków, które umożliwiają współpracę z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 87284b88076cbd205a5d8ae388fe0b37c35cf6f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328551"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku przedstawiono sposób, w jaki deweloper urządzenia nawiązuje połączenie Node.js aplikacji klienckiej z aplikacją IoT Central platformy Azure. Aplikacja Node.js symuluje zachowanie urządzenia czujnika środowiska. Korzystając z przykładowego _modelu możliwości urządzenia_ , można utworzyć _szablon urządzenia_ w IoT Central. Dodaj widoki do szablonu urządzenia, aby umożliwić operatorowi współpracujące z urządzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportuj model możliwości urządzenia, aby utworzyć szablon urządzenia.
> * Dodawanie domyślnych i niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia i Dodaj rzeczywiste urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia Node.js i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie poleceń synchronicznych i asynchronicznych w celu sterowania urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja IoT Central platformy Azure utworzona przy użyciu szablonu **aplikacji niestandardowej** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md). Aplikacja musi zostać utworzona w dniu lub po 07/14/2020.
* Komputer deweloperski z zainstalowanym [Node.js](https://nodejs.org/) w wersji 10.0.0 lub nowszej. `node --version`Aby sprawdzić swoją wersję, można uruchomić polecenie w wierszu polecenia. W instrukcjach przedstawionych w tym samouczku założono, że uruchomiono polecenie **Node** w wierszu polecenia systemu Windows. Można jednak używać Node.js w wielu innych systemach operacyjnych.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js

Poniższe kroki pokazują, jak utworzyć aplikację kliencką Node.js, która łączy się z rzeczywistym urządzeniem dodanym do aplikacji. Ta aplikacja Node.js symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia przejdź do `environmental-sensor` folderu utworzonego wcześniej.

1. Aby zainicjować projekt Node.js i zainstalować wymagane zależności, uruchom następujące polecenia — Zaakceptuj wszystkie opcje domyślne podczas uruchamiania `npm init` :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Utwórz plik o nazwie **environmentalSensor.js** w `environmental-sensor` folderze.

1. Dodaj następujące `require` instrukcje na początku pliku **environmentalSensor.js** :

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

    Aktualizowanie symboli zastępczych `{your Scope ID}` , `{your Device ID}` i `{your Primary Key}` z wartościami, które zostały wcześniej wykonane. W tym przykładzie zainicjowano `targetTemperature` do zera, można użyć bieżącego odczytu z urządzenia lub wartości z sznurka urządzenia.

1. Aby wysłać symulowane dane telemetryczne do aplikacji IoT Central platformy Azure, Dodaj następującą funkcję do pliku:

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

    Nazwy elementów telemetrycznych ( `temp` i `humid` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Aby wysłać właściwości sznurka urządzenia do aplikacji IoT Central platformy Azure, Dodaj następującą funkcję do pliku:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central używa bliźniaczych reprezentacji urządzenia do synchronizowania wartości właściwości między urządzeniem a aplikacją IoT Central. Wartości właściwości urządzenia używają właściwości zgłaszanych przez sznurek urządzeń. Właściwości do zapisu używają zarówno raportowanych, jak i żądanych właściwości.

1. Aby zdefiniować i obsłużyć zapisywalne właściwości, na które odpowiada urządzenie, Dodaj następujący kod. Komunikat wysyłany przez urządzenie w odpowiedzi na [aktualizację właściwości zapisywalnej](concepts-telemetry-properties-commands.md#writeable-property-types) musi zawierać `av` `ac` pola i. `ad`Pole jest opcjonalne:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Gdy operator ustawia właściwość do zapisu w aplikacji IoT Central, aplikacja używa odpowiedniej właściwości przędzy urządzenia do wysłania wartości do urządzenia. Następnie urządzenie odpowiada za pomocą właściwości zgłoszonej przez urządzenie. Gdy IoT Central otrzymuje raportowaną wartość właściwości, aktualizuje widok właściwości ze stanem **zsynchronizowane**.

    Nazwy właściwości ( `name` i `brightness` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

1. Dodaj następujący kod, aby obsłużyć polecenia wysyłane z aplikacji IoT Central:

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

    Nazwy poleceń ( `blink` , `turnon` , `turnoff` i `rundiagnostics` ) muszą być zgodne z nazwami używanymi w szablonie urządzenia.

    Obecnie IoT Central nie korzysta ze schematu odpowiedzi zdefiniowanego w modelu możliwości urządzenia. W przypadku polecenia synchronicznego ładunek odpowiedzi może być dowolnym prawidłowym kodem JSON. W przypadku polecenia asynchronicznego urządzenie powinno natychmiast zwrócić odpowiedź 202, a następnie zgłosić aktualizację właściwości po zakończeniu pracy. Format raportowanej aktualizacji właściwości to:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Operator może wyświetlać ładunek odpowiedzi w historii poleceń.

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
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
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

Możesz zobaczyć, że urządzenie nawiązuje połączenie z aplikacją IoT Central platformy Azure i zacznie wysyłać dane telemetryczne:

![Uruchom aplikację kliencką](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Można sprawdzić, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

![Obserwuj aplikację kliencką](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz korzystać z zestawu samouczków IoT Central i dowiedzieć się więcej na temat tworzenia rozwiązania IoT Central, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)

Jako deweloper urządzenia znasz już podstawowe informacje dotyczące sposobu tworzenia urządzenia przy użyciu Node.js, ale Oto kilka sugerowanych następnych kroków:

* Przeczytaj [co to są szablony urządzeń?](./concepts-device-templates.md) aby dowiedzieć się więcej na temat roli szablonów urządzeń podczas implementowania kodu urządzenia.
* Aby dowiedzieć się więcej o sposobach rejestrowania urządzeń w usłudze IoT Central i sposobach IoT Central zabezpieczania połączeń urządzeń, przeczytaj artykuł [wprowadzenie do usługi Azure IoT Central](./concepts-get-connected.md) .
