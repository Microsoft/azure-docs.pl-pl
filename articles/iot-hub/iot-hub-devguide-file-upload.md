---
title: Informacje o przekazywaniu plików przez usługę Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — Użyj funkcji przekazywania plików IoT Hub, aby zarządzać przekazywaniem plików z urządzenia do kontenera obiektów BLOB usługi Azure Storage.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 3286b464051b8fea88d2797d4f82b20fe432b4b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90019533"
---
# <a name="upload-files-with-iot-hub"></a>Przekazywanie plików za pomocą usługi IoT Hub

Zgodnie z opisem w artykule [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) urządzenie może rozpocząć przekazywanie plików przez wysłanie powiadomienia za pomocą punktu końcowego dostępnego na urządzeniu (**/Devices/{deviceId}/Files**). Gdy urządzenie powiadamia IoT Hub o ukończeniu przekazywania, IoT Hub wysyła komunikat z powiadomieniem o przekazaniu pliku za pomocą punktu końcowego połączonego z usługą **/messages/servicebound/filenotifications** .

Zamiast brokera komunikatów za pośrednictwem IoT Hub samego siebie, IoT Hub zamiast tego działa jako Dyspozytor do skojarzonego konta usługi Azure Storage. Urządzenie żąda tokenu magazynu z IoT Hub, który jest specyficzny dla pliku, który urządzenie chce przekazać. Urządzenie używa identyfikatora URI sygnatury dostępu współdzielonego, aby przekazać plik do magazynu, a po zakończeniu przekazywania urządzenie wysyła powiadomienie o zakończeniu do IoT Hub. IoT Hub sprawdza, czy przekazywanie plików zostało ukończone, a następnie dodaje komunikat powiadomienia o przekazywaniu pliku do punktu końcowego powiadomienia o pliku dostępnym dla usługi.

Przed przekazaniem pliku do IoT Hub z urządzenia należy skonfigurować centrum, kojarząc z nim konto [usługi Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) .

Urządzenie może następnie [inicjować przekazywanie](iot-hub-devguide-file-upload.md#initialize-a-file-upload) , a następnie [powiadamiać Centrum IoT o](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) zakończeniu przekazywania. Opcjonalnie, gdy urządzenie powiadamia IoT Hub o ukończeniu przekazywania, usługa może wygenerować [komunikat z powiadomieniem](iot-hub-devguide-file-upload.md#file-upload-notifications).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Kiedy stosować

Za pomocą przekazywania plików można wysyłać pliki multimedialne i duże partie danych telemetrycznych przesłane przez sporadycznie połączone urządzenia lub skompresowane, aby zaoszczędzić przepustowość.

Zapoznaj się z tematem [wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) , jeśli masz wątpliwości między korzystaniem z raportowanych właściwości, komunikatami z urządzenia do chmury lub przekazywaniem plików.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Skojarz konto usługi Azure Storage z usługą IoT Hub

Aby skorzystać z funkcji przekazywania plików, musisz najpierw połączyć konto usługi Azure Storage z IoT Hub. To zadanie można wykonać za pomocą Azure Portal lub programowo przy użyciu [interfejsów API REST dostawcy zasobów IoT Hub](/rest/api/iothub/iothubresource). Po skojarzeniu konta usługi Azure Storage z IoT Hub usługa zwróci identyfikator URI sygnatury dostępu współdzielonego do urządzenia, gdy urządzenie rozpocznie żądanie przekazania pliku.

[Przekazanie plików z urządzenia do chmury z IoT Hub](iot-hub-csharp-csharp-file-upload.md) przewodniki po instrukcji zapewnia kompletny przewodnik dotyczący procesu przekazywania plików. Te przewodniki pokazują, jak używać Azure Portal do kojarzenia konta magazynu z usługą IoT Hub.

> [!NOTE]
> [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie obsługują pobieranie identyfikatora URI sygnatury dostępu współdzielonego, przekazywanie pliku oraz powiadamianie IoT Hub ukończonego przekazywania.

## <a name="initialize-a-file-upload"></a>Zainicjuj przekazywanie plików
IoT Hub ma punkt końcowy przeznaczony dla urządzeń żądających identyfikatora URI sygnatury dostępu współdzielonego dla magazynu w celu przekazania pliku. Aby rozpocząć proces przekazywania plików, urządzenie wysyła żądanie POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files` następującej treści JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub zwraca następujące dane, których urządzenie używa do przekazania pliku:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Przestarzałe: zainicjuj przekazywanie pliku za pomocą GET

> [!NOTE]
> W tej sekcji opisano przestarzałą funkcję odbierania identyfikatora URI sygnatury dostępu współdzielonego z IoT Hub. Użyj opisanej wcześniej metody POST.

IoT Hub ma dwa punkty końcowe REST obsługujące przekazywanie plików, jeden w celu uzyskania identyfikatora URI sygnatury dostępu współdzielonego dla magazynu, a drugi w celu powiadomienia centrum IoT o ukończonym przekazywaniu. Urządzenie uruchamia proces przekazywania plików, wysyłając do centrum IoT Hub w lokalizacji `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . Centrum IoT Hub zwraca:

* Identyfikator URI sygnatury dostępu współdzielonego dla pliku, który ma zostać przekazany.

* Identyfikator korelacji, który ma być używany po zakończeniu przekazywania.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Powiadom IoT Hub ukończonego przekazywania plików

Urządzenie przekazuje plik do magazynu przy użyciu zestawów SDK usługi Azure Storage. Po zakończeniu przekazywania urządzenie wysyła żądanie POST do `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` następującej treści JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Wartość jest wartością `isSuccess` logiczną, która wskazuje, czy plik został pomyślnie przekazany. Kod stanu dla `statusCode` jest stanem przekazywania pliku do magazynu, a `statusDescription` odpowiada `statusCode` .

## <a name="reference-topics"></a>Tematy dotyczące odwołań:

Poniższe tematy referencyjne zawierają więcej informacji na temat przekazywania plików z urządzenia.

## <a name="file-upload-notifications"></a>Powiadomienia dotyczące przekazywania plików

Opcjonalnie, gdy urządzenie powiadamia IoT Hub o ukończeniu przekazywania, IoT Hub generuje komunikat z powiadomieniem. Ten komunikat zawiera nazwę i lokalizację przechowywania pliku.

Jak wyjaśniono w [punktach końcowych](iot-hub-devguide-endpoints.md), IoT Hub dostarcza powiadomienia o przekazywaniu plików przez punkt końcowy w ramach usługi (**/messages/servicebound/fileuploadnotifications**) jako komunikaty. Semantyka odbierania powiadomień dotyczących przekazywania plików jest taka sama jak w przypadku komunikatów z chmury do urządzenia i ma ten sam [cykl życia wiadomości](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Każdy komunikat pobrany z punktu końcowego powiadomienia przekazywania plików jest rekordem JSON o następujących właściwościach:

| Właściwość | Opis |
| --- | --- |
| EnqueuedTimeUtc |Sygnatura czasowa wskazująca, kiedy powiadomienie zostało utworzone. |
| DeviceId |Identyfikator **urządzenia, który** przesłał plik. |
| BlobUri |Identyfikator URI przekazanego pliku. |
| BlobName |Nazwa przekazanego pliku. |
| LastUpdatedTime |Sygnatura czasowa wskazująca, kiedy plik został ostatnio zaktualizowany. |
| BlobSizeInBytes |Rozmiar przekazanego pliku. |

**Przykład**. Ten przykład przedstawia treść komunikatu z powiadomieniem o przekazaniu pliku.

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

Dla każdego centrum IoT Hub dostępne są następujące opcje konfiguracji powiadomień dotyczących przekazywania plików:

| Właściwość | Opis | Zakres i domyślny |
| --- | --- | --- |
| **enableFileUploadNotifications** |Określa, czy powiadomienia dotyczące przekazywania plików są zapisywane w punkcie końcowym powiadomień dotyczących plików. |Logiczna. Wartość domyślna: true. |
| **Jest. ttlAsIso8601** |Domyślny czas wygaśnięcia powiadomień dotyczących przekazywania plików. |Interwał ISO_8601 do 48H (co najmniej 1 minutę). Wartość domyślna: 1 godzina. |
| **Jest. lockDuration** |Czas trwania blokady kolejki powiadomień przekazywania plików. |od 5 do 300 sekund (co najmniej 5 sekund). Wartość domyślna: 60 sekund. |
| **Jest. maxDeliveryCount** |Maksymalna liczba dostaw dla kolejki powiadomień przekazywania pliku. |od 1 do 100. Wartość domyślna: 100. |

Te właściwości można ustawić w centrum IoT Hub przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Aby dowiedzieć się, jak to zrobić, zobacz tematy w obszarze [Konfigurowanie przekazywania plików](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe usługi IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania nimi.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisują zachowania przydziałów i ograniczania przepustowości, które mają zastosowanie do usługi IoT Hub.

* [Zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) wymieniają różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [Język zapytań IoT Hub](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, za pomocą którego można pobrać informacje z IoT Hub dotyczące bliźniaczych reprezentacji i zadań urządzenia.

* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak przekazywać pliki z urządzeń za pomocą IoT Hub, możesz zainteresować Cię następujące IoT Hub tematy dotyczące przewodnika dla deweloperów:

* [Zarządzanie tożsamościami urządzeń w IoT Hub](iot-hub-devguide-identity-registry.md)

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

* [Synchronizowanie stanu i konfiguracji przy użyciu urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujący samouczek IoT Hub:

* [Jak przekazywać pliki z urządzeń do chmury za pomocą IoT Hub](iot-hub-csharp-csharp-file-upload.md)