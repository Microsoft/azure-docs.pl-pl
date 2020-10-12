---
title: Azure Maps jako źródło Event Grid
description: Opisuje właściwości i schemat udostępnione dla zdarzeń Azure Maps z Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 4203bdf5222278b698d656835afebd9769557303
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461990"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń Azure Maps. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](./event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków do użycia Azure Maps jako źródło zdarzenia.

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto Azure Maps emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Wywoływane, gdy otrzymane współrzędne zostały przeniesione z zewnątrz danego elementu geołożonego do wewnątrz |
| Microsoft. Maps. GeofenceExited | Wywoływane, gdy odebrane współrzędne przeniesiono z wewnątrz danego elementu geołożonego do zewnątrz |
| Microsoft. Maps. GeofenceResult | Wywoływane za każdym razem, gdy zapytanie geofencingu zwraca wynik, niezależnie od stanu |

### <a name="event-examples"></a>Przykłady zdarzeń

Poniższy przykład przedstawia schemat zdarzenia **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Poniższy przykład przedstawia schemat dla **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia geofencingu. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| apiCategory | ciąg | Kategoria interfejsu API zdarzenia. |
| apiName | ciąg | Nazwa interfejsu API zdarzenia. |
| issues | object | Wyświetla listę problemów napotkanych podczas przetwarzania. W przypadku zwrócenia jakichkolwiek problemów nie będzie żadnych geometrie zwracanych z odpowiedzią. |
| responseCode | liczba | Kod odpowiedzi HTTP |
| geometrie | object | Wyświetla listę ograniczników geometrie, które zawierają położenie współrzędnych lub nakładają się na searchBuffer wokół pozycji. |

Obiekt Error jest zwracany, gdy w interfejsie API Maps wystąpi błąd. Obiekt Error ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| error | ErrorDetails |Ten obiekt jest zwracany w przypadku wystąpienia błędu w interfejsie API Maps  |

Obiekt ErrorDetails jest zwracany, gdy w interfejsie API Maps wystąpi błąd. ErrorDetails lub obiekt ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| kod | ciąg | Kod stanu HTTP. |
| message | ciąg | Jeśli jest dostępny, czytelny dla człowieka opis błędu. |
| innererror | InnerError | Jeśli jest dostępny, obiekt zawierający informacje specyficzne dla usługi o błędzie. |

InnerError jest obiektem zawierającym informacje specyficzne dla usługi o błędzie. Obiekt InnerError ma następujące właściwości: 

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| kod | ciąg | Komunikat o błędzie. |

Obiekt geometrie wyświetla listę identyfikatorów geometrii, które wygasły względem czasu użytkownika w żądaniu. Obiekt geometrie ma elementy geometrii o następujących właściwościach: 

| Właściwość | Type | Opis |
|:-------- |:---- |:----------- |
| Identyfikator | ciąg | Identyfikator urządzenia. |
| odległość | ciąg | <p>Odległość od współrzędnej do najbliższej krawędzi geoogrodzenia. Pozytywna oznacza, że Współrzędna znajduje się poza ogrodzeniem. Jeśli Współrzędna znajduje się poza ogrodzeniem, ale więcej niż wartość searchBuffer z najbliższej granicy geoogrodzenia, wartość to 999. Wartość ujemna oznacza, że Współrzędna jest wewnątrz ogrodzenia. Jeśli Współrzędna znajduje się wewnątrz wielokąta, ale więcej niż wartość searchBuffer z najbliższego obramowania geoogrodzenia, wartość to-999. Wartość 999 oznacza, że istnieje doskonały stopień pewności, że Współrzędna jest poza ogrodzeniem geograficznym. Wartość-999 oznacza, że istnieje doskonały stopień pewności, że Współrzędna jest również w obrębie geofencingu.<p> |
| geometryid |ciąg | Unikatowy identyfikator identyfikuje geometrię geoogrodzenia. |
| nearestlat | liczba | Szerokość geograficzna najbliższego punktu geometrii. |
| nearestlon | liczba | Długość geograficzna najbliższego punktu geometrii. |
| udId | ciąg | Unikatowy identyfikator zwrócony przez usługę przekazywania przez użytkownika podczas przekazywania geoogrodzenia. Nie zostanie uwzględniony w interfejsie API po geoogrodzeniu. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | ciąg [] | Listy o IDENTYFIKATORze geometrii geofencingu, które wygasły względem czasu użytkownika w żądaniu. |
| geometrie | geometrie [] |Wyświetla listę ograniczników geometrie, które zawierają położenie współrzędnych lub nakładają się na searchBuffer wokół pozycji. |
| invalidPeriodGeofenceGeometryId | ciąg []  | Listy identyfikatorów geometrycznych geoogrodzenia, które znajdują się w nieprawidłowym okresie względem czasu użytkownika w żądaniu. |
| isEventPublished | boolean | Wartość true, jeśli co najmniej jedno zdarzenie jest publikowane na subskrybencie zdarzenia Azure Maps, wartość false, jeśli żadne zdarzenie nie jest publikowane na subskrybencie zdarzenia Azure Maps. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Reagowanie na zdarzenia Azure Maps przy użyciu Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integrowania Azure Maps z Event Grid. |
| [Samouczek: Konfigurowanie geoogrodzenia](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ten samouczek przeprowadzi Cię przez podstawowe kroki konfigurowania geofencingu przy użyciu usługi Azure Maps. Używasz Azure Event Grid do przesyłania strumieniowego wyników geoogrodzenia i konfigurowania powiadomienia na podstawie wyników. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
