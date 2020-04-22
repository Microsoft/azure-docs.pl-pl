---
title: Opis przekazywania plików usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — użyj funkcji przekazywania plików usługi IoT Hub, aby zarządzać przekazywaniem plików z urządzenia do kontenera obiektów blob magazynu platformy Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom: mqtt
ms.openlocfilehash: 35337a99706f25d62964e08a5b16cd8e81f315c6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730292"
---
# <a name="upload-files-with-iot-hub"></a>Przekazywanie plików za pomocą usługi IoT Hub

Jak opisano w [artykule punkty końcowe usługi IoT Hub,](iot-hub-devguide-endpoints.md) urządzenie może rozpocząć przekazywanie plików, wysyłając powiadomienie za pośrednictwem punktu końcowego skierowanego do urządzenia (**/devices/{deviceId}/files**). Gdy urządzenie powiadamia Centrum IoT, że przekazywanie zostało zakończone, Usługa IoT Hub wysyła komunikat powiadomienia o przekazywaniu plików za pośrednictwem punktu końcowego **usługi /messages/servicebound/filenotifications.**

Zamiast pośredniczyć wiadomości za pośrednictwem usługi IoT Hub, Usługa IoT Hub działa jako dyspozytor do skojarzonego konta usługi Azure Storage. Urządzenie żąda tokenu magazynu z Usługi IoT Hub, który jest specyficzny dla pliku, który urządzenie chce przekazać. Urządzenie używa identyfikatora URI sygnatury dostępu Współdzielonego do przekazywania pliku do magazynu, a po zakończeniu przekazywania urządzenie wysyła powiadomienie o zakończeniu do usługi IoT Hub. Usługa IoT Hub sprawdza, czy przekazywanie pliku jest zakończone, a następnie dodaje komunikat powiadomienia o przekazywaniu pliku do punktu końcowego powiadomienia o pliku, który jest związany z usługą.

Przed przekazaniem pliku do usługi IoT Hub z urządzenia należy skonfigurować centrum, [kojarząc](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) z nim konto usługi Azure Storage.

Urządzenie może następnie [zainicjować przekazywanie,](iot-hub-devguide-file-upload.md#initialize-a-file-upload) a następnie [powiadomić centrum IoT po](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) zakończeniu przekazywania. Opcjonalnie, gdy urządzenie powiadamia Centrum IoT, że przekazywanie zostało zakończone, usługa może wygenerować [komunikat powiadomienia](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Kiedy stosować

Przekazywanie plików służy do wysyłania plików multimedialnych i dużych partii telemetrycznych przekazywał przez sporadycznie podłączone urządzenia lub kompresował, aby zaoszczędzić przepustowość.

W razie wątpliwości można zapoznać się [ze wskazówkami dotyczącymi komunikacji między urządzeniami](iot-hub-devguide-d2c-guidance.md) a chmurami, korzystając z zgłoszonych właściwości, komunikatów między urządzeniami a chmurami lub przekazywania plików.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Kojarzenie konta usługi Azure Storage z usługą IoT Hub

Aby użyć funkcji przekazywania plików, należy najpierw połączyć konto usługi Azure Storage z centrum IoT Hub. To zadanie można wykonać za pośrednictwem witryny Azure portal lub programowo za pośrednictwem interfejsów API REST INTERFEJSU DOSTAWCY [ZASOBÓW usługi IoT Hub.](/rest/api/iothub/iothubresource) Po skojarzeniu konta usługi Azure Storage z centrum IoT Hub usługa zwraca identyfikator URI sygnatury dostępu Współdzielonego na urządzeniu, gdy urządzenie uruchamia żądanie przekazywania pliku.

Przekazywanie plików z urządzenia do chmury za pomocą przewodników instruktażowych [usługi IoT Hub](iot-hub-csharp-csharp-file-upload.md) zapewniają pełną instruktaż procesu przekazywania plików. Te instrukcje przewodników pokazują, jak używać witryny Azure Portal do skojarzenia konta magazynu z centrum IoT hub.

> [!NOTE]
> Zestaw [SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie obsługuje pobieranie identyfikatora URI sygnatury dostępu Współdzielonego, przekazywanie pliku i powiadamianie Centrum IoT o zakończonym przekazywaniu.

## <a name="initialize-a-file-upload"></a>Inicjowanie przekazywania plików
Usługa IoT Hub ma punkt końcowy specjalnie dla urządzeń, aby zażądać identyfikatora URI sygnatury dostępu Współdzielonego do przechowywania do przekazania pliku. Aby rozpocząć proces przekazywania plików, urządzenie `{iot hub}.azure-devices.net/devices/{deviceId}/files` wysyła żądanie POST z następującą treścią JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Usługa IoT Hub zwraca następujące dane, których urządzenie używa do przekazywania pliku:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Przestarzałe: inicjowanie przekazywania plików za pomocą funkcji GET

> [!NOTE]
> W tej sekcji opisano przestarzałe funkcje dotyczące sposobu odbierania identyfikatora URI sygnatury dostępu Współdzielonego z usługi IoT Hub. Użyj metody POST opisanej wcześniej.

Usługa IoT Hub ma dwa punkty końcowe REST do obsługi przekazywania plików, jeden, aby uzyskać identyfikator URI sygnatury dostępu Współdzielonego do magazynu, a drugi powiadomić centrum IoT hub o zakończonym przekazywaniu. Urządzenie rozpoczyna proces przekazywania plików, wysyłając get do centrum `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`IoT w . Zwraca koncentrator IoT:

* Identyfikator URI sygnatury dostępu Współdzielonego specyficzny dla pliku, który ma zostać przekazany.

* Identyfikator korelacji, który ma być używany po zakończeniu przekazywania.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Powiadamianie Centrum IoT o wypełnionym przekazywaniu plików

Urządzenie przekazuje plik do magazynu przy użyciu zestawów SDK usługi Azure Storage. Po zakończeniu przekazywania urządzenie wysyła żądanie `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` POST z następującą treścią JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Wartość `isSuccess` jest boolean, który wskazuje, czy plik został przekazany pomyślnie. Kod stanu `statusCode` jest stan przekazywania pliku do magazynu, `statusDescription` a odpowiada `statusCode`.

## <a name="reference-topics"></a>Tematy referencyjne:

Poniższe tematy referencyjne zawierają więcej informacji na temat przekazywania plików z urządzenia.

## <a name="file-upload-notifications"></a>Powiadomienia o przekazywaniu plików

Opcjonalnie, gdy urządzenie powiadamia Centrum IoT, że przekazywanie zostało zakończone, Usługa IoT Hub generuje komunikat z powiadomieniem. Ten komunikat zawiera nazwę i lokalizację przechowywania pliku.

Jak wyjaśniono w [punktach końcowych,](iot-hub-devguide-endpoints.md)Usługa IoT Hub dostarcza powiadomienia o przekazywaniu plików za pośrednictwem punktu końcowego skierowanego do usługi (**/messages/servicebound/fileuploadnotifications)** jako wiadomości. Semantyka odbierania powiadomień o przekazywaniu plików jest taka sama jak w przypadku wiadomości z chmury do urządzenia i ma ten sam [cykl życia wiadomości.](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle) Każda wiadomość pobrana z punktu końcowego powiadomienia o przekazywaniu plików jest rekordem JSON o następujących właściwościach:

| Właściwość | Opis |
| --- | --- |
| EnqueuedTimeUtc |Sygnatura czasowa wskazująca, kiedy zostało utworzone powiadomienie. |
| DeviceId |**DeviceId** urządzenia, które przekazało plik. |
| Obiekt BlobUri |Identyfikator URI przesłanego pliku. |
| Nazwa obiektu Blob |Nazwa przesłanego pliku. |
| LastUpdatedTime |Sygnatura czasowa wskazująca, kiedy plik został ostatnio zaktualizowany. |
| Obiekty BlobSizeInBytes |Rozmiar przesłanego pliku. |

**Przykład**. W tym przykładzie przedstawiono treść wiadomości powiadomienia o przekazywaniu pliku.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opcje konfiguracji powiadomień o przekazywaniu plików

Każdy centrum IoT hub ma następujące opcje konfiguracji powiadomień o przekazywaniu plików:

| Właściwość | Opis | Zakres i wartość domyślna |
| --- | --- | --- |
| **enableFileUploadNotifications** |Określa, czy powiadomienia o przekazywaniu plików są zapisywane w punkcie końcowym powiadomień o plikach. |Bool. Domyślnie: Prawda. |
| **fileNotifications.ttlAsIso8601** |Domyślny czas wygaśnięcia dla powiadomień o przekazywaniu plików. |ISO_8601 do 48H (minimum 1 minuta). Domyślnie: 1 godzina. |
| **fileNotifications.lockDuration** |Czas trwania blokady kolejki powiadomień o przekazywaniu plików. |od 5 do 300 sekund (minimum 5 sekund). Domyślnie: 60 sekund. |
| **plikNotifications.maxDeliveryCount** |Maksymalna liczba dostarczenia dla kolejki powiadomień o przekazywaniu plików. |1 do 100. Domyślnie: 100. |

Te właściwości można ustawić w centrum IoT przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Aby dowiedzieć się, jak to zrobić, zobacz tematy w obszarze [Konfigurowanie przekazywania plików](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub opisują](iot-hub-devguide-endpoints.md) różne punkty końcowe centrum IoT dla operacji wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisuje przydziały i zachowania ograniczania przepustowości, które mają zastosowanie do usługi Centrum IoT.

* [Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań centrum IoT](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, którego można użyć do pobierania informacji z Usługi IoT Hub o bliźniaczych urządzeń i zadaniach.

* [Obsługa protokołu MQTT w uężoniać w uiszczaprzestwu IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak przekazywać pliki z urządzeń korzystających z Usługi IoT Hub, możesz zainteresować się następującymi tematami przewodnika dla deweloperów usługi IoT Hub:

* [Zarządzanie tożsamościami urządzeń w Centrum IoT](iot-hub-devguide-identity-registry.md)

* [Kontrola dostępu do centrum IoT Hub](iot-hub-devguide-security.md)

* [Synchronizowanie stanu i konfiguracji za pomocą bliźniąt urządzeń](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujący samouczek Usługi IoT Hub:

* [Jak przesyłać pliki z urządzeń do chmury za pomocą Usługi IoT Hub](iot-hub-csharp-csharp-file-upload.md)