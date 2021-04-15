---
title: Wprowadzenie do zarządzania Azure IoT Hub urządzeniami mobilnymi (Node) | Microsoft Docs
description: Jak za pomocą IoT Hub urządzenia zainicjować zdalne ponowne uruchomienie urządzenia. Zestaw SDK usługi Azure IoT dla usługi Node.js służy do implementowania aplikacji urządzenia symulowanego, która zawiera metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 557cdc5fb71f998dc0b69e52148cc93da7cc8ea7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477982"
---
# <a name="get-started-with-device-management-nodejs"></a>Wprowadzenie do zarządzania urządzeniami (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj [Azure Portal,](https://portal.azure.com) aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację urządzenia symulowanego, która zawiera metodę bezpośrednią, która ponownie uruchamia to urządzenie. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację Node.js, która wywołuje metodę bezpośredniej ponownego uruchomienia w aplikacji urządzenia symulowanego za pośrednictwem centrum IoT.

Na końcu tego samouczka będziesz mieć dwie Node.js konsoli:

* **dmpatterns_getstarted_device.js**, które łączy się z centrum IoT przy użyciu utworzonej wcześniej tożsamości urządzenia, odbiera metodę bezpośredniej ponownego uruchamiania, symuluje fizyczne ponowne uruchomienie i zgłasza czas ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.js**, która wywołuje metodę bezpośrednią w aplikacji urządzenia symulowanego, wyświetla odpowiedź i wyświetla zaktualizowane zgłaszane właściwości.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js wersji 10.0.x lub nowszej. [W artykule Prepare your development environment (Przygotowywanie](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) środowiska projektowego) opisano Node.js tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [](https://azure.microsoft.com/pricing/free-trial/) bezpłatne konto w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykładowe urządzenie w tym artykule używa protokołu MQTT, który komunikuje się za pośrednictwem portu 8883. Ten port może być zablokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów na ominiecie tego problemu, zobacz [Connecting to IoT Hub (MQTT) (Nawiązywanie połączenia z IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę

* Wyzwalanie symulowanego ponownego uruchomienia urządzenia

* Użyj zgłoszonych właściwości, aby włączyć zapytania bliźniaczej reprezentacji urządzenia w celu zidentyfikowania urządzeń i ich ostatniego ponownego uruchomienia

1. Utwórz pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **manageddevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i **pakiet azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów utwórz **dmpatterns_getstarted_device.js** w **folderze manageddevice.**

4. Dodaj następujące instrukcje "require" na początku **dmpatterns_getstarted_device.js** pliku:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Zastąp wartość symbolu zastępczego wartością parametrów połączenia urządzenia skopiowanych wcześniej w teście `{yourdeviceconnectionstring}` Rejestrowanie nowego urządzenia w centrum [IoT.](#register-a-new-device-in-the-iot-hub)  

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

7. Otwórz połączenie z centrum IoT i uruchom odbiornik metody bezpośredniej:

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

8. Zapisz i zamknij **dmpatterns_getstarted_device.js** plik.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania (takie jak wykładniczego odejścia) zgodnie z sugestią w artykule Obsługa błędów [przejściowych.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Uzyskiwanie parametrów połączenia centrum IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację Node.js konsolową, która inicjuje zdalne ponowne uruchomienie urządzenia przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczych reprezentacji urządzenia, aby wykryć czas ostatniego ponownego uruchomienia dla tego urządzenia.

1. Utwórz pusty folder o nazwie **triggerrebootondevice.** W **folderze triggerrebootondevice** utwórz package.jspliku przy użyciu następującego polecenia w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iothub** i **pakiet azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów utwórz **dmpatterns_getstarted_service.js** w **folderze triggerrebootondevice.**

4. Dodaj następujące instrukcje "require" na początku **dmpatterns_getstarted_service.js** pliku:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Dodaj następujące deklaracje zmiennych i zastąp wartość symbolu zastępczego wartością parametrów połączenia centrum IoT skopiowanych wcześniej w teście Get the IoT hub connection string (Uzyskiwanie parametrów połączenia `{iothubconnectionstring}` [centrum IoT):](#get-the-iot-hub-connection-string)

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

7. Dodaj następującą funkcję, aby odpytować urządzenie i uzyskać czas ostatniego ponownego uruchomienia:

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

8. Dodaj następujący kod, aby wywołać funkcje, które wyzwalają metodę bezpośrednią ponownego rozruchu i odpytują o czas ostatniego ponownego uruchomienia:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Zapisz i zamknij **dmpatterns_getstarted_service.js** plik.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W wierszu polecenia w **folderze manageddevice** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchamiania.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** uruchom następujące polecenie, aby wyzwolić zdalne ponowne uruchomienie i odpytać bliźniacza reprezentacji urządzenia, aby znaleźć czas ostatniego ponownego uruchomienia.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. W konsoli zobaczysz odpowiedź urządzenia na metodę bezpośrednią ponownego uruchomienia i stan ponownego uruchomienia.

   Poniżej przedstawiono odpowiedź urządzenia na metodę bezpośrednią ponownego uruchomienia wysłaną przez usługę:

   ![dane wyjściowe aplikacji manageddevice](./media/iot-hub-node-node-device-management-get-started/device.png)

   Poniżej przedstawiono usługę, która wyzwala ponowne uruchomienie i sonduje bliźniacze reprezentacji urządzenia podczas ostatniego ponownego uruchomienia:

   ![dane wyjściowe aplikacji triggerrebootondevice](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
