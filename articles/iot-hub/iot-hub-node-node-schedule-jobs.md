---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (węzeł) | Dokumenty firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub, aby wywołać metodę bezpośrednią na wielu urządzeniach. ZestawY SDK usługi Azure IoT dla node.js służy do implementowania symulowanych aplikacji urządzenia i aplikacji usługi do uruchomienia zadania.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732264"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Planowanie i emisja zadań (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza tworzenie i śledzenie zadań, które planują i aktualizują miliony urządzeń.  Zadania mogą być używane dla następujących akcji:

* Aktualizowanie żądanych właściwości
* Aktualizuj tagi
* Wywoływanie metod bezpośrednich

Koncepcyjnie zadanie zawija jedną z tych akcji i śledzi postęp wykonywania względem zestawu urządzeń, który jest zdefiniowany przez zapytanie bliźniaczej reprezentacji urządzenia.  Na przykład aplikacja zaplecza można użyć zadania do wywołania metody ponownego uruchamiania na 10 000 urządzeń, określone przez zapytanie bliźniaczej reprezentacji urządzenia i zaplanowane w przyszłości. Ta aplikacja może następnie śledzić postęp, jak każdy z tych urządzeń odbierać i wykonywać metodę ponownego uruchamiania.

Dowiedz się więcej o każdej z tych funkcji w następujących artykułach:

* Bliźniaczy bliźniaczej reprezentacji urządzenia i właściwości: [Wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-node-node-twin-getstarted.md) i [samouczek: Jak używać właściwości bliźniaczej reprezentacji urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [Przewodnik dla programistów IoT Hub - metody bezpośrednie](iot-hub-devguide-direct-methods.md) i [samouczek: metody bezpośrednie](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia symulowanego Node.js, która ma metodę bezpośrednią, która umożliwia **lockDoor**, który może być wywoływany przez zaplecze rozwiązania.

* Utwórz aplikację konsoli Node.js, która wywołuje metodę **lockDoor** direct w symulowanej aplikacji urządzenia przy użyciu zadania i aktualizuje żądane właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka masz dwie aplikacje Node.js:

* **simDevice.js**, który łączy się z hubem IoT z tożsamością urządzenia i otrzymuje metodę **lockDoor** direct.

* **scheduleJobService.js**, który wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia i aktualizuje żądane właściwości bliźniaczej reprezentacji urządzenia przy użyciu zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0.x lub nowszej. [W artykule Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano sposób instalowania pliku Node.js dla tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która odpowiada na metodę bezpośrednią wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor.**

1. Utwórz nowy pusty folder o nazwie **simDevice**.  W folderze **simDevice** utwórz plik package.json, używając następującego polecenia w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

   ```console
   npm init
   ```

2. W wierszu polecenia w folderze **simDevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i pakiet **azure-iot-device-mqtt:**

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Za pomocą edytora tekstu utwórz nowy plik **simDevice.js** w folderze **simDevice.**

4. Na początku pliku **simDevice.js** dodaj następujące instrukcje "require":

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**. Zastąp wartość symbolu `{yourDeviceConnectionString}` zastępczego wcześniej skopiowanym ciągiem połączenia urządzenia.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Dodaj następującą funkcję, aby obsłużyć metodę **lockDoor.**

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

7. Dodaj następujący kod, aby zarejestrować program obsługi dla **lockDoor** metody.

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

8. Zapisz i zamknij plik **simDevice.js.**

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań wywoływania metody bezpośredniej i aktualizowania właściwości bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje zdalną **lockDoor** na urządzeniu przy użyciu metody bezpośredniej i zaktualizuj właściwości bliźniaczej reprezentacji urządzenia.

1. Utwórz nowy pusty folder o nazwie **scheduleJobService**.  W folderze **scheduleJobService** utwórz plik package.json przy użyciu następującego polecenia w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```console
    npm init
    ```

2. W wierszu polecenia w folderze **scheduleJobService** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iothub** i pakiet **azure-iot-device-mqtt:**

    ```console
    npm install azure-iothub uuid --save
    ```

3. Za pomocą edytora tekstu utwórz nowy plik **scheduleJobService.js** w folderze **scheduleJobService.**

4. Dodaj następujące instrukcje "require" na początku pliku **scheduleJobService.js:**

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Dodaj następujące deklaracje zmiennych. Zastąp wartość symbolu `{iothubconnectionstring}` zastępczego wartością skopiowaną w polu Pobierz ciąg połączenia centrum [IoT](#get-the-iot-hub-connection-string). Jeśli zarejestrowałeś urządzenie inne niż **myDeviceId**, pamiętaj, aby zmienić go w warunku zapytania.

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

8. Dodaj następujący kod, aby zaplanować zadanie, aby zaktualizować bliźniaczej reprezentacji urządzenia:

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

9. Zapisz i zamknij plik **scheduleJobService.js.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w folderze **simDevice** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchomienia.

    ```console
    node simDevice.js
    ```

2. W wierszu polecenia w folderze **scheduleJobService** uruchom następujące polecenie, aby wyzwolić zadania, aby zablokować drzwi i zaktualizować bliźniaczej reprezentacji

    ```console
    node scheduleJobService.js
    ```

3. Zostanie wyświetlana odpowiedź urządzenia na metodę bezpośrednią i stan zadania w konsoli.

   Poniżej przedstawiono reakcję urządzenia na metodę bezpośrednią:

   ![Symulowane wyjście aplikacji urządzenia](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Poniżej przedstawiono zadania planowania usługi dla metody bezpośredniej i bliźniaczej aktualizacji urządzenia oraz zadania uruchomione do zakończenia:

   ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania, aby zaplanować metodę bezpośrednią do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować pracę z Usługą IoT Hub i wzorcami zarządzania urządzeniami, takimi jak zdalna aktualizacja oprogramowania układowego, zobacz [Samouczek: Jak wykonać aktualizację oprogramowania układowego](tutorial-firmware-update.md).

Aby kontynuować pracę z Usługą IoT Hub, zobacz [Wprowadzenie do usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
