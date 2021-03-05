---
title: Wysyłanie danych telemetrycznych urządzenia do usługi Azure IoT Hub szybki start (Node.js)
description: W tym przewodniku szybki start użyjesz zestawu SDK urządzeń IoT Hub Azure dla Node.js do wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197813"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub (Node.js)

**Dotyczy**: [opracowywanie aplikacji dla urządzeń](about-iot-develop.md#device-application-development)

W tym przewodniku szybki start nauczysz się podstawowy przepływ pracy tworzenia aplikacji dla urządzeń IoT. Za pomocą interfejsu wiersza polecenia platformy Azure można utworzyć centrum Azure IoT Hub i symulowane urządzenie, a następnie za pomocą zestawu SDK usługi Azure IoT Node.js uzyskać dostęp do urządzenia i wysyłać dane telemetryczne do centrum.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
- Interfejs wiersza polecenia platformy Azure. Wszystkie polecenia w tym przewodniku szybki start można uruchomić za pomocą Azure Cloud Shell, interaktywnej powłoki interfejsu wiersza polecenia, która jest uruchamiana w przeglądarce. W przypadku korzystania z Cloud Shell nie trzeba instalować żadnych elementów. Jeśli wolisz używać interfejsu wiersza polecenia lokalnie, ten przewodnik Szybki Start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby odnaleźć wersję, uruchom polecenie az --version. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
- [Node.js 10 +](https://nodejs.org). W przypadku korzystania z Azure Cloud Shell nie należy aktualizować zainstalowanej wersji programu Node.js. Azure Cloud Shell ma już najnowszą wersję Node.js.

    Sprawdź bieżącą wersję Node.js na komputerze deweloperskim, używając następującego polecenia:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Używanie zestawu SDK Node.js do wysyłania komunikatów
W tej sekcji użyjesz zestawu SDK Node.js do wysyłania komunikatów z symulowanego urządzenia do centrum IoT Hub. 

1. Otwórz nowe okno terminalu. Ten terminal będzie używany do instalowania zestawu SDK Node.js i pracy z Node.js przykładowym kodem. Powinny teraz być otwarte dwa terminale: ten właśnie otwarty do pracy z Node.js i powłoką interfejsu wiersza polecenia, która została użyta w poprzednich sekcjach, do wprowadzania poleceń interfejsu wiersza polecenia platformy Azure. 

1. Skopiuj [przykłady urządzeń z zestawem SDK usługi Azure IoT Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) na komputer lokalny:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Przejdź do katalogu *Azure-IoT-SDK-Node/Device/Samples* :

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Zainstaluj zestaw SDK usługi Azure IoT Node.js i wymagane zależności:

    ```console
    npm install
    ```
    To polecenie powoduje zainstalowanie odpowiednich zależności określonych w *package.js* w pliku w katalogu przykłady urządzeń.

1. Ustaw parametry połączenia urządzenia jako zmienną środowiskową o nazwie `DEVICE_CONNECTION_STRING` . Wartość ciągu do użycia jest ciągiem uzyskanym w poprzedniej sekcji po utworzeniu symulowanego urządzenia Node.js. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > W przypadku polecenia Windows CMD nie ma znaków cudzysłowu otaczających parametry połączenia.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. W otwartej powłoki interfejsu wiersza polecenia Uruchom polecenie [AZ IoT Hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) , aby rozpocząć monitorowanie zdarzeń na symulowanym urządzeniu IoT.  Komunikaty o zdarzeniach będą drukowane w terminalu po ich nadejściu.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. W terminalu Node.js Uruchom kod dla zainstalowanego przykładowego pliku *simple_sample_device.js* . Ten kod uzyskuje dostęp do symulowanego urządzenia IoT i wysyła komunikat do centrum IoT Hub.

    Aby uruchomić przykład Node.js z terminalu:
    ```console
    node ./simple_sample_device.js
    ```

    Opcjonalnie można uruchomić kod Node.js z przykładu w środowisku IDE języka JavaScript:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Ponieważ kod Node.js wysyła symulowany komunikat telemetrii z urządzenia do usługi IoT Hub, komunikat pojawi się w powłoce interfejsu wiersza polecenia, które jest monitorowane zdarzenia:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Urządzenie jest teraz bezpiecznie połączone i wysyła dane telemetryczne do usługi Azure IoT Hub.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start nie są już potrzebne, można je usunąć za pomocą interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. 

Aby usunąć grupę zasobów na podstawie nazwy:
1. Uruchom polecenie [AZ Group Delete](/cli/azure/group#az-group-delete) . To polecenie usuwa grupę zasobów, IoT Hub i utworzoną rejestrację urządzenia.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Uruchom polecenie [AZ Group list](/cli/azure/group#az-group-list) , aby potwierdzić, że grupa zasobów została usunięta.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono podstawowy przepływ pracy aplikacji Azure IoT służący do bezpiecznego łączenia urządzenia z chmurą i wysyłania danych telemetrycznych z urządzenia do chmury. Interfejs wiersza polecenia platformy Azure został użyty do utworzenia Centrum IoT i symulowanego urządzenia, a następnie do uzyskania dostępu do urządzenia i wysłania telemetrii do centrum użyto zestawu SDK usługi Azure IoT Node.js. 

Następnym krokiem jest zapoznanie się z zestawem SDK usługi Azure IoT Node.js za pomocą przykładów aplikacji.

- [Więcej przykładów Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): ten katalog zawiera więcej przykładów z repozytorium zestawu SDK Node.js w celu pokazania IoT Hub scenariuszy.