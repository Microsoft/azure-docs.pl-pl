---
title: Planowanie zadań przy użyciu Azure IoT Hub (Node) | Microsoft Docs
description: Jak zaplanować zadanie Azure IoT Hub wywoływanie metody bezpośredniej na wielu urządzeniach. Zestawy SDK usługi Azure IoT dla usługi Node.js do zaimplementowania symulowanych aplikacji urządzenia i aplikacji usługi do uruchamiania zadania.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: dc0ea9817b9cbc27816354c48abbe26d79a83f04
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477911"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Planowanie i emituj zadania (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji back-end tworzenie i śledzenie zadań, które planować i aktualizować miliony urządzeń.  Zadania mogą być używane do następujących akcji:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

Koncepcyjnie zadanie opakowywuje jedną z tych akcji i śledzi postęp wykonywania względem zestawu urządzeń, który jest definiowany przez zapytanie bliźniaczej reprezentacji urządzenia.  Na przykład aplikacja back-end może użyć zadania do wywołania metody ponownego uruchamiania na 10 000 urządzeń określonej przez zapytanie bliźniaczej reprezentacji urządzenia i zaplanowanej w przyszłości. Ta aplikacja może następnie śledzić postęp w przypadku odbierania i wykonywania metody ponownego uruchamiania każdego z tych urządzeń.

Dowiedz się więcej o każdej z tych możliwości w tych artykułach:

* Bliźniacze i właściwości urządzenia: [Wprowadzenie do bliźniaczych reprezentacji](iot-hub-node-node-twin-getstarted.md) urządzeń i [Samouczek: jak używać właściwości bliźniaczej reprezentacji urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [IoT Hub dewelopera — metody bezpośrednie](iot-hub-devguide-direct-methods.md) i [Samouczek: metody bezpośrednie](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację Node.js symulowanego urządzenia, która ma metodę bezpośrednią, która włącza metodę **lockDoor,** która może być wywoływana przez zadrzewę rozwiązania.

* Utwórz aplikację Node.js konsolową, która wywołuje metodę **bezpośrednią lockDoor** w aplikacji urządzenia symulowanego przy użyciu zadania i aktualizuje żądane właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka będziesz mieć dwie Node.js aplikacji:

* **simDevice.js**, które łączy się z centrum IoT przy użyciu tożsamości urządzenia i odbiera metodę **bezpośrednią lockDoor.**

* **scheduleJobService.js**, który wywołuje metodę bezpośrednią w aplikacji urządzenia symulowanego i aktualizuje żądane właściwości bliźniaczej reprezentacji urządzenia przy użyciu zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js wersji 10.0.x lub nowszej. [W artykule Prepare your development environment (Przygotowywanie](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) środowiska projektowego) opisano Node.js tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [](https://azure.microsoft.com/pricing/free-trial/) bezpłatne konto w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym artykule używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową Node.js, która reaguje na metodę bezpośrednią wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor.**

1. Utwórz nowy pusty folder o **nazwie simDevice.**  W **folderze simDevice** utwórz plik package.jsza pomocą następującego polecenia w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

   ```console
   npm init
   ```

2. W wierszu polecenia w folderze **simDevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i **pakiet azure-iot-device-mqtt:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Za pomocą edytora tekstów utwórz nowy **pliksimDevice.js** w **folderze simDevice.**

4. Dodaj następujące instrukcje "require" na początku **simDevice.js** pliku:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**. `{yourDeviceConnectionString}`Zamień wartość symbolu zastępczego na skopiowane wcześniej ciągi połączenia urządzenia.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Dodaj następującą funkcję do obsługi **metody lockDoor.**

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Dodaj następujący kod, aby zarejestrować program obsługi dla **metody lockDoor.**

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Zapisz i zamknij **simDevice.js** plik.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania (takie jak wykładniczego odgałę roku) zgodnie z sugestią w artykule Transient Fault Handling (Obsługa błędów [przejściowych).](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań wywoływania metody bezpośredniej i aktualizowania właściwości bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje zdalne **blokowanieDoor** na urządzeniu przy użyciu metody bezpośredniej i aktualizuje właściwości bliźniaczej reprezentacji urządzenia.

1. Utwórz nowy pusty folder o nazwie **scheduleJobService.**  W **folderze scheduleJobService** utwórz plik package.jsza pomocą następującego polecenia w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```console
    npm init
    ```

2. W wierszu polecenia w folderze **scheduleJobService** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iothub** i **pakiet azure-iot-device-mqtt:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Za pomocą edytora tekstów utwórz nowy **plikscheduleJobService.js** w **folderze scheduleJobService.**

4. Dodaj następujące instrukcje "require" na początku **scheduleJobService.js** pliku:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Dodaj następujące deklaracje zmiennych. Zastąp wartość `{iothubconnectionstring}` symbolu zastępczego wartością skopiowaną w teście [Get the IoT hub connection string (Uzyskiwanie](#get-the-iot-hub-connection-string)parametrów połączenia centrum IoT). Jeśli zarejestrowano urządzenie inne niż **myDeviceId,** zmień je w warunku zapytania.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, która jest używana do monitorowania wykonywania zadania:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Dodaj następujący kod, aby zaplanować zadanie, które wywołuje metodę urządzenia:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Dodaj następujący kod, aby zaplanować zadanie aktualizacji bliźniaczej reprezentacji urządzenia:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Zapisz i zamknij **scheduleJobService.js** plik.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w **folderze simDevice** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchamiania.

    ```console
    node simDevice.js
    ```

2. W wierszu polecenia w folderze **scheduleJobService** uruchom następujące polecenie, aby wyzwolić zadania w celu zablokowania bramy i zaktualizowania bliźniaczej reprezentacji

    ```console
    node scheduleJobService.js
    ```

3. W konsoli zobaczysz odpowiedź urządzenia na metodę bezpośrednią i stan zadania.

   Poniżej przedstawiono odpowiedź urządzenia na metodę bezpośrednią:

   ![Dane wyjściowe aplikacji urządzenia symulowanego](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Poniżej przedstawiono zadania planowania usługi dla metody bezpośredniej i aktualizacji bliźniaczej reprezentacji urządzenia oraz zadania uruchomione do ukończenia:

   ![Uruchamianie aplikacji urządzenia symulowanego](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku za pomocą zadania zaplanowano metodę bezpośrednią do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować rozpoczynanie pracy z IoT Hub i urządzeniami, takimi jak zdalna aktualizacja oprogramowania układowego, zobacz [Samouczek: jak wykonać aktualizację oprogramowania układowego.](tutorial-firmware-update.md)

Aby kontynuować rozpoczynanie pracy z IoT Hub, zobacz [Wprowadzenie do Azure IoT Edge](../iot-edge/quickstart-linux.md).
