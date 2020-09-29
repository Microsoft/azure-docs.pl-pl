---
title: Komunikaty z chmury do urządzenia za pomocą usługi Azure IoT Hub (węzeł) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla Node.js. Zmodyfikuj aplikację symulowanego urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446883"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat [wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md). Pokazano, jak:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.
* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.
* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w [przewodniku dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie Node.js aplikacje konsolowe:

* **SimulatedDevice**, zmodyfikowana wersja aplikacji utworzona w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md), która łączy się z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym C, Java, Python i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0. x lub nowszej. [Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisuje sposób instalowania Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieraj komunikaty w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz aplikację symulowanego urządzenia utworzoną w artykule wysyłanie danych [telemetrycznych z urządzenia do usługi IoT Hub](quickstart-send-telemetry-node.md) w celu odbierania komunikatów z chmury do urządzenia z Centrum IoT Hub.

1. Za pomocą edytora tekstów Otwórz plik **SimulatedDevice.js** . Ten plik znajduje się w folderze **IoT-hub\Quickstarts\simulated-Device** , który znajduje się w folderze głównym Node.js przykładowym kodzie pobranym w polu [Wyślij dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md) .

2. Zarejestruj procedurę obsługi z klientem urządzenia, aby odbierać komunikaty wysyłane z IoT Hub. Dodaj wywołanie do `client.on` tuż po wierszu, który tworzy klienta urządzenia, tak jak w poniższym fragmencie kodu:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

W tym przykładzie urządzenie wywoła **kompletną** funkcję w celu powiadomienia IoT Hub o przetworzeniu komunikatu i można ją bezpiecznie usunąć z kolejki urządzeń. Wywołanie do **ukończenia** nie jest wymagane, jeśli używany jest transport MQTT i można go pominąć. Jest to wymagane w przypadku AMQP i HTTPS.

W przypadku AMQP i HTTPS, ale nie MQTT, urządzenie może również:

* Porzuć komunikat, co spowoduje IoT Hub zachowywanie komunikatu w kolejce urządzeń w celu użycia w przyszłości.
* Odrzuć komunikat, który trwale usuwa komunikat z kolejki urządzeń.

W przypadku, gdy coś się nie powiedzie, że urządzenie zakończy działanie, porzucanie lub odrzucanie komunikatu, IoT Hub po upływie ustalonego limitu czasu będzie kolejkować komunikat o dostarczeniu ponownie. Z tego powodu logika przetwarzania komunikatów w aplikacji urządzenia musi być *idempotentne*, dzięki czemu ten sam komunikat wielokrotnie daje ten sam wynik.

Aby uzyskać szczegółowe informacje na temat sposobu, w jaki IoT Hub przetwarza komunikatów z chmury do urządzenia, w tym szczegółowe informacje o cyklu życia komunikatów z chmury do urządzenia, zobacz [wysyłanie komunikatów z chmury do urządzeń z Centrum IoT](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Jeśli używasz protokołu HTTPS zamiast MQTT lub AMQP jako transportu, wystąpienie **DeviceClient** sprawdza komunikaty IoT Hub rzadko (co najmniej 25 minut). Aby uzyskać więcej informacji o różnicach między obsługą MQTT, AMQP i HTTPS, zobacz [wskazówki dotyczące komunikacji z chmury do urządzeń](iot-hub-devguide-c2d-guidance.md) i [Wybierz protokół komunikacyjny](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową Node.js, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia. Potrzebujesz identyfikatora urządzenia dodanego w polu [Wyślij telemetrię z urządzenia do](quickstart-send-telemetry-node.md) przewodnika Szybki Start dotyczącego usługi IoT Hub. Potrzebne są również parametry połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

1. Utwórz pusty folder o nazwie **sendcloudtodevicemessage**. W folderze **sendcloudtodevicemessage** Utwórz package.jsw pliku przy użyciu następującego polecenia z poziomu wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```shell
    npm init
    ```

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** Uruchom następujące polecenie, aby zainstalować pakiet **Azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz plik **SendCloudToDeviceMessage.js** w folderze **sendcloudtodevicemessage** .

4. Dodaj następujące `require` instrukcje na początku pliku **SendCloudToDeviceMessage.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Dodaj następujący kod do pliku **SendCloudToDeviceMessage.js** . Zastąp wartości symboli zastępczych "{ciąg połączenia usługi IoT Hub}" i "{Device ID}" parametrami połączenia usługi IoT Hub i zanotowanym wcześniej IDENTYFIKATORem urządzenia:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, aby drukować wyniki operacji do konsoli:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Dodaj następującą funkcję, aby drukować wiadomości z opiniami o dostarczaniu do konsoli programu:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Dodaj następujący kod, aby wysłać wiadomość na urządzenie i obsłużyć komunikat o opinii, gdy urządzenie potwierdzi komunikat z chmury do urządzenia:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Zapisz i zamknij plik **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze **symulowane urządzenia** Uruchom następujące polecenie, aby wysłać dane telemetryczne do IoT Hub i nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    node SimulatedDevice.js
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-node-node-c2d/receivec2d.png)

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** Uruchom następujące polecenie, aby wysłać komunikat z chmury do urządzenia i poczekać na potwierdzenie opinii:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Uruchom aplikację, aby wysłać polecenie z chmury do urządzenia](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Dla uproszczenia w tym samouczku nie są implementowane żadne zasady ponawiania. W polu kod produkcyjny należy zaimplementować zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz [Akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
