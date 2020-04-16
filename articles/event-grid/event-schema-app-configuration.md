---
title: Konfiguracja aplikacji platformy Azure jako źródło siatki zdarzeń
description: W tym artykule opisano sposób używania konfiguracji aplikacji platformy Azure jako źródła zdarzeń w usłudze Event Grid. Zawiera schemat i łącza do samouczka i artykułów instruktażowych.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393427"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Konfiguracja aplikacji platformy Azure jako źródło siatki zdarzeń
Ten artykuł zawiera właściwości i schemat zdarzeń konfiguracji aplikacji platformy Azure. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md). Zawiera również listę szybkich uruchomień i samouczków, aby używać konfiguracji aplikacji platformy Azure jako źródła zdarzeń.

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure App Configuration emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Wywoływane po utworzeniu lub zastąpieniu wartości klucza. |
| Microsoft.AppConfiguration.KeyValueDeleted | Wywoływane po usunięciu wartości klucza. |

### <a name="example-event"></a>Przykładowe zdarzenie

Poniższy przykład przedstawia schemat zdarzenia zmodyfikowanego wartości klucza: 

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

Schemat usuniętego zdarzenia wartości klucza jest podobny: 

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
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| ID | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia konfiguracji aplikacji. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| key | ciąg | Klucz klucza wartość, który został zmodyfikowany lub usunięty. |
| label | ciąg | Etykieta, jeśli istnieje, wartości klucza, który został zmodyfikowany lub usunięty. |
| Etag | ciąg | Dla `KeyValueModified` etag nowej wartości klucza. Dla `KeyValueDeleted` etag wartości klucza, który został usunięty. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki

|Tytuł | Opis |
|---------|---------|
| [Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure przy użyciu siatki zdarzeń](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Omówienie integrowania konfiguracji aplikacji platformy Azure z siatką zdarzeń. |
| [Szybki start: kierowanie zdarzeń konfiguracji aplikacji platformy Azure do niestandardowego punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń konfiguracji aplikacji platformy Azure do elementu WebHook. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby zapoznać się ze zmianami dotyczących pracy z konfiguracją aplikacji platformy Azure, zobacz [Kierowanie zdarzeń konfiguracji aplikacji platformy Azure — Interfejsu wiersza polecenia platformy Azure.](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) 