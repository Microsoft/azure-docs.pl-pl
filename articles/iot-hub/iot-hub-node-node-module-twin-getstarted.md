---
title: Zacznij od bliźniaczej tożsamości modułu usługi Azure IoT Hub & modułu bliźniaczej reprezentacji (Node.js)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczy moduł przy użyciu pakietów IoT SDK dla node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp
ms.openlocfilehash: 8e1599b1bd5db5e410e8bbd76fffbe0beb5f066e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732300"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Wprowadzenie do tożsamości modułu IoT Hub i bliźniaczej reprezentacji modułu (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Podczas gdy tożsamość urządzenia usługi Azure IoT Hub i bliźniacza reprezentacja urządzenia umożliwiają aplikacji zaplecza konfigurowanie urządzenia i zapewniają wgląd w warunki urządzenia, tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka masz dwie aplikacje Node.js:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.

* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs](iot-hub-devguide-sdks.md) (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0.x lub nowszej. [W artykule Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano sposób instalowania pliku Node.js dla tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Tworzenie tożsamości urządzenia i tożsamości modułu w Centrum IoT

W tej sekcji utworzysz aplikację Node.js, która tworzy tożsamość urządzenia i tożsamość modułu w rejestrze tożsamości w centrum IoT hub. Urządzenie lub moduł nie mogą łączyć się z centrum IoT Hub, jeśli nie mają odpowiedniego wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Rejestr tożsamości" [w przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-identity-registry.md). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

1. Utwórz katalog do przechowywania kodu.

2. Wewnątrz tego katalogu najpierw uruchom **npm init -y,** aby utworzyć pusty plik package.json z wartościami domyślnymi. Jest to plik projektu dla kodu.

3. Uruchom **instalację npm -S\@azure-iothub modules-preview,** aby zainstalować zestaw SDK usługi wewnątrz podkatalogu **node_modules.**

    > [!NOTE]
    > Nazwa podkatalogu node_modules używa modułu słowa jako "biblioteka węzłów". Termin tutaj nie ma nic wspólnego z modułami IoT Hub.

4. Utwórz następujący plik .js w katalogu. Nazwij to **add.js**. Skopiuj i wklej parametry połączenia koncentratora i nazwę koncentratora.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Ta aplikacja tworzy tożsamość urządzenia z identyfikatorem **myFirstDevice** i tożsamości modułu z **identyfikatorem myFirstModule** pod **urządzeniem myFirstDevice**. (Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera istniejące informacje o module).) Następnie aplikacja wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji modułu, aby nawiązać połączenie z centrum IoT.

Uruchom to przy użyciu węzła add.js. To daje parametry połączenia dla tożsamości urządzenia i inny dla tożsamości modułu.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide](iot-hub-devguide-identity-registry.md) (Usługa IoT Hub — przewodnik dewelopera).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu sdk urządzenia Node.js

W tej sekcji utworzysz aplikację Node.js na symulowanym urządzeniu, która aktualizuje właściwości zgłaszane bliźniaczej reprezentacji modułu.

1. **Pobierz ciąg połączenia modułu** — zaloguj się do [witryny Azure portal](https://portal.azure.com/). Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź myFirstDevice, otwórz go i zobaczysz myFirstModule został pomyślnie utworzony. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

   ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Podobnie jak w powyższym kroku, utwórz katalog dla kodu urządzenia i użyj NPM, aby go zainicjować i zainstalować sdk urządzenia (**npm install -S azure-iot-device-amqp\@modules-preview**).

   > [!NOTE]
   > Polecenie instalacji npm może wydawać się powolne. Bądź cierpliwy, to ściągając wiele kodu z repozytorium pakietów.

   > [!NOTE]
   > Jeśli widzisz błąd, który mówi npm ERR! błąd rejestru analizowanie json, jest to bezpieczne do zignorowania. Jeśli widzisz błąd, który mówi npm ERR! błąd rejestru analizowanie json, jest to bezpieczne do zignorowania.

3. Utwórz plik o nazwie twin.js. Skopiuj i wklej ciąg tożsamości modułu.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Teraz uruchom to za pomocą węzła polecenia **twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Następnie zobaczysz:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)

* [Getting started with IoT Edge](../iot-edge/tutorial-simulate-device-linux.md) (Wprowadzenie do usługi IoT Edge)