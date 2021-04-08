---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (węzeł) | Microsoft Docs
description: Jak za pomocą IoT Hub zarządzanie urządzeniami zainicjować zdalne ponowne uruchomienie urządzenia. Zestaw SDK usługi Azure IoT dla Node.js służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: cfc0fa45c08f917b2e0b4a0b055e801173a4ba39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91252021"
---
# <a name="get-started-with-device-management-nodejs"></a>Wprowadzenie do zarządzania urządzeniami (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią, która uruchamia ponownie to urządzenie. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsolową Node.js, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem Centrum IoT.

Na końcu tego samouczka będziesz mieć dwie Node.js aplikacje konsolowe:

* **dmpatterns_getstarted_device.js**, która łączy się z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje rozruch fizyczny i raportuje godzinę ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.js**, która wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetli odpowiedź i wyświetla zaktualizowane raportowane właściwości.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0. x lub nowszej. [Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisuje sposób instalowania Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę

* Wyzwalanie ponownego uruchomienia symulowanego urządzenia

* Użyj raportowanych właściwości, aby włączyć do identyfikowania urządzeń i podczas ostatniego ponownego uruchomienia zapytania o sznury

1. Utwórz pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **manageddevice** Uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **Azure-IoT-Device** i pakiet **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów Utwórz plik **dmpatterns_getstarted_device.js** w folderze **manageddevice** .

4. Dodaj następujące instrukcje "Wymagaj" na początku pliku **dmpatterns_getstarted_device.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Zastąp `{yourdeviceconnectionstring}` wartość symbolu zastępczego parametrami połączenia urządzenia skopiowanymi wcześniej w temacie [Rejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Dodaj następującą funkcję, aby zaimplementować metodę bezpośrednią na urządzeniu

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Otwórz połączenie z Centrum IoT Hub i uruchom odbiornik metody bezpośredniej:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Zapisz i zamknij plik **dmpatterns_getstarted_device.js** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową Node.js, która inicjuje zdalne ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań o bliźniaczych urządzeniach do wykrywania czasu ostatniego rozruchu dla tego urządzenia.

1. Utwórz pusty folder o nazwie **triggerrebootondevice**. W folderze **triggerrebootondevice** Utwórz package.jsw pliku przy użyciu następującego polecenia z poziomu wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** Uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **Azure-iothub** i pakiet **Azure-IoT-Device-MQTT** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz plik **dmpatterns_getstarted_service.js** w folderze **triggerrebootondevice** .

4. Dodaj następujące instrukcje "Wymagaj" na początku pliku **dmpatterns_getstarted_service.js** :

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Dodaj następujące deklaracje zmiennych i Zastąp `{iothubconnectionstring}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub skopiowanymi wcześniej w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Dodaj następującą funkcję, aby wywołać metodę urządzenia w celu ponownego uruchomienia urządzenia docelowego:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Dodaj następującą funkcję, aby wykonać zapytanie dotyczące urządzenia i uzyskać czas ostatniego ponownego uruchomienia:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Dodaj następujący kod, aby wywołać funkcje wyzwalające metodę ponownego rozruchu i zapytanie o czas ostatniego ponownego uruchomienia:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Zapisz i zamknij plik **dmpatterns_getstarted_service.js** .

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia w folderze **manageddevice** Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** Uruchom następujące polecenie, aby wyzwolić zdalne ponowne uruchomienie i zapytanie dla sznurka urządzenia, aby znaleźć czas ostatniego ponownego uruchomienia.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Zostanie wyświetlona odpowiedź urządzenia na metodę bezpośredniego ponownego uruchomienia i stan ponownego uruchomienia w konsoli programu.

   Poniżej przedstawiono odpowiedź urządzenia do metody ponownego uruchomienia bezpośredniego wysyłanej przez usługę:

   ![dane wyjściowe aplikacji manageddevice](./media/iot-hub-node-node-device-management-get-started/device.png)

   Poniżej przedstawiono usługę wyzwalającą ponowne uruchomienie i sondowanie sznurka urządzenia po ostatnim ponownym uruchomieniu:

   ![dane wyjściowe aplikacji triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
