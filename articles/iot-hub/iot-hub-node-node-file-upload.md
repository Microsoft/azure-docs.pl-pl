---
title: Przekazywanie plików z urządzeń do usługi Azure IoT Hub za pomocą węzła | Dokumenty firmy Microsoft
description: Jak przekazać pliki z urządzenia do chmury przy użyciu zestawu SDK urządzenia Usługi Azure IoT dla node.js. Przekazane pliki są przechowywane w kontenerze obiektów blob magazynu platformy Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732246"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Przekazywanie plików z urządzenia do chmury za pomocą usługi IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ten samouczek opiera się na kodzie w [wysyłaniu wiadomości z chmury do urządzenia za pomocą centrum IoT Hub,](iot-hub-node-node-c2d.md) aby pokazać, jak korzystać z [możliwości przekazywania plików usługi IoT Hub](iot-hub-devguide-file-upload.md) do przekazywania pliku do [magazynu obiektów blob platformy Azure](../storage/index.yml). Ten samouczek przedstawia sposób wykonania następujących czynności:

* Bezpiecznie zapewnij urządzeniu identyfikator URI obiektu blob platformy Azure do przekazywania pliku.

* Powiadomienia o przekazywaniu plików usługi IoT Hub za pomocą wyzwalania przetwarzania pliku w zapleczu aplikacji.

Funkcja [Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT](quickstart-send-telemetry-node.md) hub pokazuje podstawowe funkcje obsługi wiadomości między urządzeniami w chmurze usługi IoT Hub. Jednak w niektórych scenariuszach nie można łatwo mapować danych wysyłanych przez urządzenia do komunikatów stosunkowo małych urządzeń do chmury akceptowanych przez usługę IoT Hub. Przykład:

* Duże pliki zawierające obrazy
* Filmy wideo
* Dane dotyczące drgań, z dużą częstotliwością
* Jakaś forma wstępnie przetworzonych danych.

Te pliki są zazwyczaj wsadowe przetwarzane w chmurze przy użyciu narzędzi, takich jak [usługa Azure Data Factory](../data-factory/introduction.md) lub [stosu Hadoop.](../hdinsight/index.yml) Gdy potrzebujesz plików wyżynnych z urządzenia, nadal możesz korzystać z zabezpieczeń i niezawodności usługi IoT Hub.

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli Node.js:

* **SimulatedDevice.js**, który przekazuje plik do magazynu przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego dostarczonego przez centrum IoT hub.

* **ReadFileUploadNotification.js**, który odbiera powiadomienia o przekazywaniu plików z centrum IoT Hub.

> [!NOTE]
> Usługa IoT Hub obsługuje wiele platform i języków urządzeń (w tym C, .NET, Javascript, Python i Java) za pośrednictwem zestawów SDK urządzeń IoT platformy Azure. Zapoznaj się z [Centrum deweloperów usługi Azure IoT], aby uzyskać instrukcje krok po kroku dotyczące podłączania urządzenia do usługi Azure IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

* Node.js w wersji 10.0.x lub nowszej. [W artykule Przygotowanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano sposób instalowania pliku Node.js dla tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w ciągu zaledwie kilku minut).

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Przekazywanie pliku z aplikacji urządzenia

W tej sekcji utworzysz aplikację urządzenia, aby przekazać plik do centrum IoT Hub.

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

5. Dodaj zmienną `deviceconnectionstring` i użyj jej do utworzenia wystąpienia **Client**.  Zamień `{deviceconnectionstring}` nazwę urządzenia utworzonego w sekcji *Utwórz centrum IoT* Hub:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Ze względu na prostotę parametry połączenia znajduje się w kodzie: nie jest to zalecana praktyka i w zależności od przypadku użycia i architektury warto rozważyć bardziej bezpieczne sposoby przechowywania tego klucza tajnego.

6. Dodaj następujący kod, aby połączyć klienta:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Utwórz wywołanie zwrotne i użyj funkcji **uploadToBlob,** aby przekazać plik.

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

9. Skopiuj `simulateddevice` plik obrazu do `myimage.png`folderu i zmień jego nazwę .

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do odbierania wiadomości powiadomień o przekazywaniu plików z centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-node.md). Aby odbierać komunikaty powiadomień o przekazywaniu plików, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Odbierz powiadomienie o przekazaniu pliku

W tej sekcji utworzysz aplikację konsoli Node.js, która odbiera komunikaty powiadomień o przekazywaniu plików z Usługi IoT Hub.

Aby ukończyć tę sekcję, można użyć ciągu połączenia **iothubowner** z usługi IoT Hub. Ciąg połączenia zostanie odnalezieny w [witrynie Azure portal](https://portal.azure.com/) w bloku **zasad dostępu współdzielonego.**

1. Utwórz pusty folder o nazwie ```fileuploadnotification```.  W folderze ```fileuploadnotification``` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. W wierszu polecenia ```fileuploadnotification``` w folderze uruchom następujące polecenie, aby zainstalować pakiet zestawu **Azure-iothub** SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstu utwórz plik **FileUploadNotification.js** w folderze. `fileuploadnotification`

4. Dodaj następujące `require` instrukcje na początku pliku **FileUploadNotification.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Dodaj zmienną `iothubconnectionstring` i użyj jej do utworzenia wystąpienia **Client**.  Zastąp wartość symbolu `{iothubconnectionstring}` zastępczego parametrym połączenia centrum IoT skopiowanym wcześniej w polu Pobierz parametry połączenia centrum [IoT:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Ze względu na prostotę parametry połączenia znajduje się w kodzie: nie jest to zalecana praktyka i w zależności od przypadku użycia i architektury warto rozważyć bardziej bezpieczne sposoby przechowywania tego klucza tajnego.

6. Dodaj następujący kod, aby połączyć klienta:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Otwórz klienta i użyj funkcji **getFileNotificationReceiver,** aby otrzymywać aktualizacje stanu.

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

8. Zapisz i zamknij plik **FileUploadNotification.js.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

W wierszu polecenia `fileuploadnotification` w folderze uruchom następujące polecenie:

```cmd/sh
node FileUploadNotification.js
```

W wierszu polecenia `simulateddevice` w folderze uruchom następujące polecenie:

```cmd/sh
node SimulatedDevice.js
```

Poniższy zrzut ekranu przedstawia dane wyjściowe z aplikacji **SimulatedDevice:**

![Dane wyjściowe z aplikacji symulowanego urządzenia](./media/iot-hub-node-node-file-upload/simulated-device.png)

Poniższy zrzut ekranu przedstawia dane wyjściowe z **aplikacji FileUploadNotification:**

![Dane wyjściowe z aplikacji do odczytu-upload-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Za pomocą portalu można wyświetlić przekazany plik w skonfigurowanym kontenerze magazynu:

![Przesłany plik](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak korzystać z możliwości przekazywania plików usługi IoT Hub, aby uprościć przekazywanie plików z urządzeń. Możesz kontynuować eksplorowanie funkcji i scenariuszy centrum IoT za pomocą następujących artykułów:

* [Programowo tworzenie centrum IoT](iot-hub-rm-template-powershell.md)

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)
