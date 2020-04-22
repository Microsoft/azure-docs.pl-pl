---
title: Wprowadzenie do zarządzania urządzeniami usługi Azure IoT Hub (węzeł) | Dokumenty firmy Microsoft
description: Jak za pomocą zarządzania urządzeniami Usługi IoT Hub zainicjować ponowne uruchomienie urządzenia zdalnego. Użyj zestawu SDK usługi Azure IoT dla node.js, aby zaimplementować aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: a7814970d4ffb051ab54c965d27394f925d79e59
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732327"
---
# <a name="get-started-with-device-management-nodejs"></a>Wprowadzenie do zarządzania urządzeniami (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj [witryny Azure Portal,](https://portal.azure.com) aby utworzyć centrum IoT Hub i utworzyć tożsamość urządzenia w centrum IoT hub.

* Utwórz aplikację symulowanego urządzenia, która zawiera bezpośrednią metodę ponownego uruchomienia tego urządzenia. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsoli Node.js, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem centrum IoT hub.

Na końcu tego samouczka masz dwie aplikacje konsoli Node.js:

* **dmpatterns_getstarted_device.js**, który łączy się z centrum IoT hub z tożsamości urządzenia utworzone wcześniej, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje fizyczny restart i raportuje czas ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.js**, który wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane zgłoszone właściwości.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0.x lub nowszej. [W artykule Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano sposób instalowania pliku Node.js dla tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę

* Wyzwalanie symulowanego ponownego uruchomienia urządzenia

* Użyj zgłoszonych właściwości, aby włączyć zapytania bliźniaczej reprezentacji urządzeń do identyfikowania urządzeń i kiedy ostatnio zostały ponownie uruchomione

1. Utwórz pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **manageddevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i pakiet **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstu utwórz plik **dmpatterns_getstarted_device.js** w folderze **manageddevice.**

4. Na początku pliku **dmpatterns_getstarted_device.js** dodaj następujące instrukcje "require":

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Zastąp wartość symbolu `{yourdeviceconnectionstring}` zastępczego ciągiem połączenia urządzenia skopiowanym wcześniej w polu Zarejestruj nowe urządzenie w centrum [IoT hub](#register-a-new-device-in-the-iot-hub).  

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

7. Otwórz połączenie z centrum IoT i uruchom odbiornik metod bezpośrednich:

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

8. Zapisz i zamknij plik **dmpatterns_getstarted_device.js.**

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia urządzenia przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje zdalny ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczej reprezentacji urządzenia, aby odkryć ostatni czas ponownego uruchomienia dla tego urządzenia.

1. Utwórz pusty folder o nazwie **triggerrebootondevice**. W folderze **triggerrebootondevice** utwórz plik package.json za pomocą następującego polecenia w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iothub** i pakiet **azure-iot-device-mqtt:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstu utwórz plik **dmpatterns_getstarted_service.js** w folderze **triggerrebootondevice.**

4. Dodaj następujące instrukcje "require" na początku pliku **dmpatterns_getstarted_service.js:**

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Dodaj następujące deklaracje zmiennych `{iothubconnectionstring}` i zastąp wartość zastępczą ciągiem połączenia centrum IoT skopiowanym wcześniej w [polu Pobierz ciąg połączenia centrum IoT:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Dodaj następującą funkcję, aby wywołać metodę urządzenia, aby ponownie uruchomić urządzenie docelowe:

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

7. Dodaj następującą funkcję, aby wykonać kwerendę dla urządzenia i uzyskać ostatni czas ponownego uruchomienia:

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

8. Dodaj następujący kod, aby wywołać funkcje, które wyzwalają metodę bezpośredni ponowny rozruchu i kwerendę dla ostatniego ponownego uruchomienia:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Zapisz i zamknij plik **dmpatterns_getstarted_service.js.**

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz możesz uruchomić aplikacje.

1. W wierszu polecenia w folderze **manageddevice** uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchomienia.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. W wierszu polecenia w folderze **triggerrebootondevice** uruchom następujące polecenie, aby wyzwolić zdalny ponowny rozruch i zapytać o bliźniaczej reprezentacji urządzenia, aby znaleźć ostatni czas ponownego uruchomienia.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Zostanie wyświetlonych odpowiedzi urządzenia na metodę bezpośredniego ponownego uruchomienia i stan ponownego uruchomienia w konsoli.

   Poniżej przedstawiono odpowiedź urządzenia na metodę bezpośredniego ponownego uruchomienia wysłaną przez usługę:

   ![dane wyjściowe aplikacji manageddevice](./media/iot-hub-node-node-device-management-get-started/device.png)

   Poniżej przedstawiono usługę wyzwalającą ponowne uruchomienie i sondowanie bliźniaczej reprezentacji urządzenia po raz ostatni ponownego uruchomienia:

   ![triggerrebootondevice wyjście aplikacji](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
