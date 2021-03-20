---
title: Reagowanie na zdarzenia klucza konfiguracji aplikacji platformy Azure
description: Użyj Azure Event Grid, aby subskrybować zdarzenia konfiguracji aplikacji, dzięki czemu aplikacje mogą reagować na zmiany w kluczowych wartościach bez konieczności stosowania skomplikowanego kodu.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 12a62bbd06cf9adf3b5978bb061e1d014599b44c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550746"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reagowanie na zdarzenia konfiguracji aplikacji platformy Azure

Zdarzenia konfiguracji aplikacji platformy Azure umożliwiają aplikacjom reagowanie na zmiany w kluczowych wartościach. Jest to wykonywane bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania. Zamiast tego zdarzenia są wypychane za pośrednictwem [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)lub nawet do własnego niestandardowego odbiornika http. W sposób krytyczny płacisz tylko za to, czego używasz.

Zdarzenia konfiguracji aplikacji platformy Azure są wysyłane do Azure Event Grid, które zapewniają niezawodne usługi dostarczania do aplikacji dzięki rozbudowanym zasadom ponawiania i dostarczaniu wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [Event Grid dostarczania komunikatów i ponów próbę](../event-grid/delivery-and-retry.md).

Typowe scenariusze zdarzeń konfiguracji aplikacji obejmują odświeżanie konfiguracji aplikacji, wyzwalanie wdrożeń lub przepływ pracy zorientowany na konfigurację. Gdy zmiany są rzadko wykonywane, ale scenariusz wymaga natychmiastowej reakcji, Architektura oparta na zdarzeniach może być szczególnie wydajna.

Zapoznaj się z tematem [używanie Event Grid na potrzeby powiadomień o zmianach danych](./howto-app-configuration-event.md) . 

![Model Event Grid](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Dostępne zdarzenia konfiguracji aplikacji platformy Azure
Funkcja Event Grid używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Subskrypcje zdarzeń konfiguracji aplikacji platformy Azure mogą zawierać dwa typy zdarzeń:  

> |Nazwa zdarzenia|Opis|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Uruchamiany, gdy wartość klucza jest tworzona lub zastępowana|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Uruchamiany po usunięciu wartości klucza|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia konfiguracji aplikacji platformy Azure zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie konfiguracji aplikacji, ponieważ właściwość eventType zaczyna się od "Microsoft. AppConfiguration". Dodatkowe informacje na temat użycia właściwości zdarzeń Event Grid są udokumentowane w [Event Grid schemacie zdarzeń](../event-grid/event-schema.md).  

> |Właściwość|Typ|Opis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |temat|ciąg|Pełny Azure Resource Manager Identyfikator konfiguracji aplikacji, która emituje zdarzenie.|
> |subject|ciąg|Identyfikator URI wartości klucz-wartość, która jest podmiotem zdarzenia.|
> |eventTime|ciąg|Data/godzina wygenerowania zdarzenia w formacie ISO 8601.|
> |eventType|ciąg|"Microsoft. AppConfiguration. KeyValueModified" lub "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|ciąg|Unikatowy identyfikator tego zdarzenia.|
> |dataVersion|ciąg|Wersja schematu obiektu danych.|
> |metadataVersion|ciąg|Wersja schematu właściwości najwyższego poziomu.|
> |dane|object|Kolekcja danych zdarzeń specyficznych dla konfiguracji aplikacji platformy Azure|
> |Data. Key|ciąg|Klucz wartości, który został zmodyfikowany lub usunięty.|
> |Data. Label|ciąg|Etykieta (jeśli istnieje) wartości klucz-wartość, która została zmodyfikowana lub usunięta.|
> |Data. ETag|ciąg|Dla elementu `KeyValueModified` ETag nowej wartości klucz-wartość. Dla elementu `KeyValueDeleted` ETag, który został usunięty.|

Oto przykład zdarzenia KeyValueModified:
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

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń konfiguracji aplikacji platformy Azure](../event-grid/event-schema-app-configuration.md).

## <a name="practices-for-consuming-events"></a>Praktyki związane z zużywaniem zdarzeń
W aplikacjach, które obsługują zdarzenia konfiguracji aplikacji, powinny być stosowane następujące zalecenia:
> [!div class="checklist"]
> * Można skonfigurować wiele subskrypcji, aby kierować zdarzenia do tego samego programu obsługi zdarzeń, dlatego nie zakłada się, że zdarzenia pochodzą z konkretnego źródła. Zamiast tego Sprawdź temat wiadomości, aby upewnić się, że wystąpienie konfiguracji aplikacji wysyła zdarzenie.
> * Sprawdź typ zdarzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, będą to typy, których oczekujesz.
> * Użyj pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne.  
> * Użyj pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu.
> * Użyj pola podmiot, aby uzyskać dostęp do zmodyfikowanej wartości klucz-wartość.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i wydawanie zdarzeń konfiguracji aplikacji platformy Azure spróbuj:

- [Event Grid — informacje](../event-grid/overview.md)
- [Jak używać Event Grid do powiadomień o zmianach danych](./howto-app-configuration-event.md)