---
title: Schemat subskrypcji Azure Event Grid
description: W tym artykule opisano właściwości subskrybowania zdarzenia z Azure Event Grid. Event Grid schemat subskrypcji.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720762"
---
# <a name="event-grid-subscription-schema"></a>Schemat subskrypcji Event Grid

Aby utworzyć subskrypcję Event Grid, Wyślij żądanie do operacji tworzenia subskrypcji zdarzeń. Użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Aby na przykład utworzyć subskrypcję zdarzeń dla konta magazynu o nazwie `examplestorage` w grupie zasobów o nazwie `examplegroup`, użyj następującego formatu:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Nazwa subskrypcji zdarzeń musi mieć 3-64 znaków i może zawierać tylko znaki a-z, A-Z, 0-9 i "-". W tym artykule opisano właściwości i schemat treści żądania.
 
## <a name="event-subscription-properties"></a>Właściwości subskrypcji zdarzeń

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| destination | obiekt | Obiekt definiujący punkt końcowy. |
| filtr | obiekt | Opcjonalne pole do filtrowania typów zdarzeń. |

### <a name="destination-object"></a>obiekt docelowy

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| punkt końcowy | ciąg | Typ punktu końcowego dla subskrypcji (element webhook/HTTP, Event Hub lub queue). | 
| endpointUrl | ciąg | Docelowy adres URL dla zdarzeń w tej subskrypcji zdarzeń. | 

### <a name="filter-object"></a>Obiekt Filter

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| includedEventTypes | tablica | Dopasuj, gdy typ zdarzenia w komunikacie zdarzenia to dokładne dopasowanie do jednej z tych nazw typów zdarzeń. Zgłasza błąd, gdy nazwa zdarzenia nie jest zgodna z zarejestrowanymi nazwami typów zdarzeń dla źródła zdarzenia. Domyślnie pasuje do wszystkich typów zdarzeń. |
| subjectBeginsWith | ciąg | Filtr dopasowania prefiksu do pola podmiot w komunikacie zdarzenia. Domyślny lub pusty ciąg pasuje do wszystkich. | 
| subjectEndsWith | ciąg | Filtr dopasowania sufiksu do pola podmiot w komunikacie zdarzenia. Domyślny lub pusty ciąg pasuje do wszystkich. |
| isSubjectCaseSensitive | ciąg | Steruje dopasowaniem wielkości liter dla filtrów. |


## <a name="example-subscription-schema"></a>Przykładowy schemat subskrypcji

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Event Grid, zobacz [co to jest Event Grid?](overview.md)
