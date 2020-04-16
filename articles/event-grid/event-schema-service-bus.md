---
title: Usługa Azure Service Bus jako źródło siatki zdarzeń
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń usługi Service Bus z usługą Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393239"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Usługa Azure Service Bus jako źródło siatki zdarzeń

Ten artykuł zawiera właściwości i schemat zdarzeń usługi Service Bus.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Service Bus emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesDostępneDołącza Nielisteners | Wywoływane, gdy istnieją aktywne wiadomości w kolejce lub subskrypcji i nie odbiorników nasłuchuje. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Wywoływane, gdy istnieją aktywne wiadomości w kolejce utraconych list i nie aktywnych odbiorników. |

### <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat aktywnych komunikatów bez zdarzenia odbiorników:

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

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Namespacename | ciąg | Obszar nazw usługi Service Bus, w istnieje. |
| Requesturi | ciąg | Identyfikator URI do określonej kolejki lub subskrypcji emitujących zdarzenie. |
| Entitytype | ciąg | Typ jednostki usługi Service Bus emitującej zdarzenia (kolejka lub subskrypcja). |
| Queuename | ciąg | Kolejka z aktywnymi wiadomościami, jeśli subskrybuje kolejkę. Wartość null w przypadku używania tematów / subskrypcji. |
| topicName | ciąg | Temat, do którego należy subskrypcja usługi Service Bus z aktywnymi wiadomościami. Wartość null, jeśli używasz kolejki. |
| subscriptionName | ciąg | Subskrypcja usługi Service Bus z aktywnymi wiadomościami. Wartość null, jeśli używasz kolejki. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: przykłady integracji usługi Azure Service Bus to Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Usługa Event Grid wysyła wiadomości z tematu usługi Service Bus do aplikacji funkcji i aplikacji logiki. |
| [Integracja usługi Azure Service Bus to Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Omówienie integrowania usługi Service Bus z siatką zdarzeń. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby uzyskać szczegółowe informacje na temat korzystania z usługi Azure Event Grid z usługą Service Bus, zobacz [omówienie integracji usługi Service Bus to Event Grid.](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)
* Spróbuj [odbierać zdarzenia usługi Service Bus za pomocą funkcji lub aplikacji logiki](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
