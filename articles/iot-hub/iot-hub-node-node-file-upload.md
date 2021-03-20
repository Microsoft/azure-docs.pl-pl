---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub z węzłem | Microsoft Docs
description: Jak przekazywać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzeń Azure IoT dla Node.js. Przekazane pliki są przechowywane w kontenerze obiektów BLOB usługi Azure Storage.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: db4f78e14696c421adaedd16b0b3f8d598f12846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251902"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

W tym samouczku przedstawiono kod w oknie [wysyłanie komunikatów z chmury do urządzeń za pomocą](iot-hub-node-node-c2d.md) samouczka IoT Hub, aby dowiesz się, jak używać [funkcji przekazywania plików IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania plików do [usługi Azure Blob Storage](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpieczne zapewnianie urządzenia za pomocą identyfikatora URI obiektu blob platformy Azure na potrzeby przekazywania pliku.

* Użyj powiadomień przekazywania plików IoT Hub, aby wyzwolić przetwarzanie pliku w zapleczu aplikacji.

[Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md) przedstawia podstawowe funkcje obsługi komunikatów z urządzenia do chmury IoT Hub. Jednak w niektórych scenariuszach nie można łatwo zmapować danych wysyłanych przez urządzenia do bezwzględnie niewielkich komunikatów z urządzenia do chmury, które IoT Hub akceptowane. Na przykład:

* Duże pliki zawierające obrazy
* Filmy wideo
* Próbkowanie danych drgań z wysoką częstotliwością
* Niektóre formy wstępnie przetworzonych danych.

Te pliki są zwykle przetwarzane wsadowo w chmurze przy użyciu narzędzi, takich jak [Azure Data Factory](../data-factory/introduction.md) lub stos [Hadoop](../hdinsight/index.yml) . W przypadku konieczności przewożenia plików z urządzenia można nadal korzystać z zabezpieczeń i niezawodności IoT Hub.

Na końcu tego samouczka uruchomisz dwie Node.js aplikacje konsolowe:

* **SimulatedDevice.js**, która przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego dostarczonego przez Centrum IoT.

* **ReadFileUploadNotification.js**, który odbiera powiadomienia o przekazywaniu plików z Centrum IoT Hub.

> [!NOTE]
> IoT Hub obsługuje wiele platform i języków urządzeń (w tym C, .NET, JavaScript, Python i Java) za pomocą zestawów SDK urządzeń usługi Azure IoT. Zapoznaj się z artykułem [Azure IoT Developer Center], aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z usługą Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0. x lub nowszej. [Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisuje sposób instalowania Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia w celu przekazania pliku do centrum IoT Hub.

1. Utwórz pusty folder o nazwie ```simulateddevice```.  W folderze ```simulateddevice``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w folderze ```simulateddevice``` uruchom następujące polecenie, aby zainstalować pakiet zestawu SDK urządzenia **azure-iot-device** i pakiet **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów utwórz plik **SimulatedDevice.js** w folderze ```simulateddevice```.

4. Dodaj następujące instrukcje ```require``` na początku pliku **SimulatedDevice.js**:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Dodaj zmienną `deviceconnectionstring` i użyj jej do utworzenia wystąpienia **Client**.  Zamień `{deviceconnectionstring}` na nazwę urządzenia utworzonego w sekcji *Tworzenie IoT Hub* :

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > W celu uproszczenia parametry połączenia są zawarte w kodzie: nie jest to zalecane rozwiązanie, w zależności od przypadków użycia i architektury, warto rozważyć bezpieczniejsze sposoby przechowywania tego klucza tajnego.

6. Dodaj następujący kod, aby połączyć klienta:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Utwórz wywołanie zwrotne i użyj funkcji **uploadToBlob** , aby przekazać plik.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Zapisz i zamknij plik **SimulatedDevice.js**.

9. Skopiuj plik obrazu do `simulateddevice` folderu i zmień jego nazwę `myimage.png` .

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do odbierania komunikatów powiadomień o przekazywaniu plików z Centrum IoT Hub utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-node.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa musi mieć uprawnienia do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbieranie powiadomienia o przekazywaniu plików

W tej sekcji utworzysz aplikację konsolową Node.js, która odbiera komunikaty powiadomień o przekazaniu plików z IoT Hub.

Możesz użyć parametrów połączenia **iothubowner** z IoT Hub, aby ukończyć tę sekcję. Parametry połączenia można znaleźć w [Azure Portal](https://portal.azure.com/) w bloku **zasady dostępu współużytkowanego** .

1. Utwórz pusty folder o nazwie ```fileuploadnotification```.  W folderze ```fileuploadnotification``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia w ```fileuploadnotification``` folderze Uruchom następujące polecenie, aby zainstalować pakiet **Azure-iothub** SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz plik **FileUploadNotification.js** w `fileuploadnotification` folderze.

4. Dodaj następujące `require` instrukcje na początku pliku **FileUploadNotification.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Dodaj zmienną `iothubconnectionstring` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp `{iothubconnectionstring}` wartość symbolu zastępczego parametrami połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > W celu uproszczenia parametry połączenia są zawarte w kodzie: nie jest to zalecane rozwiązanie, w zależności od przypadków użycia i architektury, warto rozważyć bezpieczniejsze sposoby przechowywania tego klucza tajnego.

6. Dodaj następujący kod, aby połączyć klienta:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Otwórz klienta i użyj funkcji **getFileNotificationReceiver** , aby otrzymywać aktualizacje stanu.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Zapisz i zamknij plik **FileUploadNotification.js** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia w `fileuploadnotification` folderze Uruchom następujące polecenie:

```cmd/sh
node FileUploadNotification.js
```

W wierszu polecenia w `simulateddevice` folderze Uruchom następujące polecenie:

```cmd/sh
node SimulatedDevice.js
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **SimulatedDevice** :

![Wyjście z aplikacji symulowanej — urządzenie](./media/iot-hub-node-node-file-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **FileUploadNotification** :

![Dane wyjściowe z aplikacji Read-File-Upload-Notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Możesz użyć portalu, aby wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

![Przekazany plik](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia funkcji przekazywania plików IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz w dalszym ciągu eksplorować funkcje i scenariusze dotyczące programu IoT Hub z następującymi artykułami:

* [Programistyczne tworzenie Centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do zestawu SDK języka C](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)
