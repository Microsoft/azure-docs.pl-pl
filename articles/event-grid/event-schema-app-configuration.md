---
title: Konfiguracja aplikacji platformy Azure jako źródło Event Grid
description: W tym artykule opisano sposób korzystania z usługi Azure App Configuration jako źródła zdarzeń Event Grid. Zawiera schemat i linki do samouczka oraz artykuły z artykułami.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d305236e8408052be4be28ec003f4e545119fc59
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550678"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Konfiguracja aplikacji platformy Azure jako źródło Event Grid
Ten artykuł zawiera właściwości i schemat zdarzeń konfiguracji aplikacji platformy Azure. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków umożliwiających korzystanie z konfiguracji aplikacji platformy Azure jako źródła zdarzeń.

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Konfiguracja aplikacji platformy Azure emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Uruchamiany, gdy wartość klucza jest tworzona lub zastępowana. |
| Microsoft. AppConfiguration. KeyValueDeleted | Uruchamiany po usunięciu wartości klucza. |

### <a name="example-event"></a>Przykładowe zdarzenie

Poniższy przykład przedstawia schemat zdarzenia modyfikacji klucz-wartość: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schemat dla zdarzenia usuniętego klucz-wartość jest podobny: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| ID (Identyfikator) | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia konfiguracji aplikacji. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| key | ciąg | Klucz wartości, który został zmodyfikowany lub usunięty. |
| label | ciąg | Etykieta (jeśli istnieje) wartości klucz-wartość, która została zmodyfikowana lub usunięta. |
| element ETag | ciąg | Dla elementu `KeyValueModified` ETag nowej wartości klucz-wartość. Dla elementu `KeyValueDeleted` ETag, który został usunięty. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure za pomocą Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integracji usługi Azure App Configuration z Event Grid. |
| [Użyj Event Grid do powiadomień o zmianach danych](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Dowiedz się, jak wysyłać zdarzenia modyfikacji klucz-wartość do internetowego punktu końcowego za pomocą subskrypcji zdarzeń konfiguracji aplikacji platformy Azure. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby zapoznać się z wprowadzeniem do pracy z zdarzeniami konfiguracji aplikacji platformy Azure, zobacz temat [używanie Event Grid do powiadomień o zmianach danych](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 