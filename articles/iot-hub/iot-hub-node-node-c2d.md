---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (węzeł) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla node.js. Zmodyfikowana aplikacja urządzenia jest modyfikowana w celu odbierania komunikatów z chmury do urządzenia i modyfikowania aplikacji zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
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
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732357"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Wysyłanie komunikatów z chmury do urządzenia za pomocą usługi IoT Hub (node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod symulowanej aplikacji urządzenia, która wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się na [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-node.md)hub . To pokazuje, jak:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.
* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.
* W rozwiązaniu zaplecza, żądanie potwierdzenia dostarczania *(opinie)* dla wiadomości wysyłanych do urządzenia z Usługi IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć w [przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli Node.js:

* **SimulatedDevice**, zmodyfikowana wersja aplikacji utworzonej w [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT hub](quickstart-send-telemetry-node.md), która łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pośrednictwem usługi IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym języka C, Java, Python i Javascript) za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do kodu tego samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów usługi Azure IoT.](https://azure.microsoft.com/develop/iot)
>

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0.x lub nowszej. [W artykule Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano sposób instalowania pliku Node.js dla tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz aplikację symulowanego urządzenia utworzoną w [sekcji Wyślij dane telemetryczne z urządzenia do centrum IoT hub,](quickstart-send-telemetry-node.md) aby odbierać komunikaty z chmury do urządzenia z centrum IoT Hub.

1. Za pomocą edytora tekstu otwórz plik **SimulatedDevice.js.** Ten plik znajduje się w folderze **iot-hub\Quickstarts\simulated-device** poza folderem głównym przykładowego kodu Node.js pobranego w polu [Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT hub.](quickstart-send-telemetry-node.md)

2. Zarejestruj program obsługi u klienta urządzenia, aby odbierać wiadomości wysyłane z usługi IoT Hub. Dodaj wywołanie `client.on` tuż po wierszu, który tworzy klienta urządzenia, jak w poniższym urywek:

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

    W tym przykładzie urządzenie wywołuje **pełną** funkcję, aby powiadomić Centrum IoT, że przetworzył komunikat. Wywołanie **complete** nie jest wymagane, jeśli używasz transportu MQTT i można go pominąć. Jest to wymagane dla HTTPS i AMQP.
  
   > [!NOTE]
   > Jeśli używasz protokołu HTTPS zamiast MQTT lub AMQP jako transportu, **DeviceClient wystąpienie** sprawdza komunikaty z Usługi IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między obsługą MQTT, AMQP i HTTPS oraz ograniczaniem przepustowości centrum IoT, zobacz [przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-node.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która wysyła komunikaty z chmury do urządzenia do symulowanej aplikacji urządzenia. Potrzebny jest identyfikator urządzenia dodanego w danych [telemetrycznych wysyłania z urządzenia do szybkiego startu centrum IoT Hub.](quickstart-send-telemetry-node.md) Potrzebny jest również ciąg połączenia centrum IoT skopiowany wcześniej w [pliku Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

1. Utwórz pusty folder o nazwie **sendcloudtodevicemessage**. W folderze **sendcloudtodevicemessage** utwórz plik package.json za pomocą następującego polecenia w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```shell
    npm init
    ```

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** uruchom następujące polecenie, aby zainstalować pakiet **azure-iothub:**

    ```shell
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstu utwórz plik **SendCloudToDeviceMessage.js** w folderze **sendcloudtodevicemessage.**

4. Dodaj następujące `require` instrukcje na początku pliku **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Dodaj następujący kod do pliku **SendCloudToDeviceMessage.js.** Zastąp wartości zastępcze symboli zastępczych "{iot hub connection string}" i "{device id}" ciągiem połączenia i identyfikatorem urządzenia:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, aby wydrukować wyniki operacji do konsoli:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Dodaj następującą funkcję, aby wydrukować komunikaty zwrotne o dostarczeniu do konsoli:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Dodaj następujący kod, aby wysłać wiadomość do urządzenia i obsłużyć wiadomość zwrotną, gdy urządzenie potwierdzi komunikat z chmury do urządzenia:

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

9. Zapisz i zamknij plik **SendCloudToDeviceMessage.js.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze **symulowanego urządzenia** uruchom następujące polecenie, aby wysłać dane telemetryczne do usługi IoT Hub i nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    node SimulatedDevice.js
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-node-node-c2d/receivec2d.png)

2. W wierszu polecenia w folderze **sendcloudtodevicemessage** uruchom następujące polecenie, aby wysłać wiadomość z chmury do urządzenia i poczekać na opinię o potwierdzeniu:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Uruchamianie aplikacji w celu wysłania polecenia chmury do urządzenia](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Dla uproszczenia ten samouczek nie implementuje żadnych zasad ponawiania prób. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)
   >

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
