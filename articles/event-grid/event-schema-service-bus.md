---
title: Azure Service Bus jako źródło Event Grid
description: Opisuje właściwości, które są dostępne dla zdarzeń Service Bus z Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 81293321b3a8fb989023a231c905996b4059bd81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86121138"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń Service Bus.Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Service Bus emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Uruchamiany, gdy istnieją aktywne komunikaty w kolejce lub subskrypcji i nie ma nasłuchujących odbiorników. |
| Microsoft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Uruchamiany, gdy istnieją aktywne komunikaty w kolejce utraconych wiadomości i brak aktywnych odbiorników. |

### <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat aktywnych komunikatów bez zdarzenia odbiornika:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia kolejki utraconych wiadomości jest podobny:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
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
| dane | object | Dane zdarzenia magazynu obiektów BLOB. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| namespaceName | ciąg | Przestrzeń nazw Service Bus, w której znajduje się zasób. |
| requestUri | ciąg | Identyfikator URI konkretnej kolejki lub subskrypcji emitującej zdarzenie. |
| Elementy | ciąg | Typ jednostki Service Bus emitującej zdarzenia (kolejkę lub subskrypcję). |
| Zmienną QueueName | ciąg | Kolejka z aktywnymi komunikatami w przypadku subskrybowania kolejki. Wartość null w przypadku korzystania z tematów/subskrypcji. |
| temat | ciąg | Temat, do którego należy subskrypcja Service Bus z aktywnymi wiadomościami. Wartość null w przypadku używania kolejki. |
| subscriptionName | ciąg | Subskrypcja Service Bus z aktywnymi wiadomościami. Wartość null w przypadku używania kolejki. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przykłady dotyczące Azure Event Grid integracji Azure Service Bus](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid wysyła wiadomości z tematu Service Bus do aplikacji funkcji i aplikacji logiki. |
| [Azure Service Bus Event Grid integrację](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Omówienie integrowania Service Bus z Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby uzyskać szczegółowe informacje na temat korzystania z Azure Event Grid z Service Bus, zobacz [Omówienie integracji Service Bus z Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Spróbuj [odebrać zdarzenia Service Bus za pomocą funkcji lub Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
