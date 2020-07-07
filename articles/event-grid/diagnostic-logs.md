---
title: Dzienniki diagnostyczne Azure Event Grid dla tematów lub domen
description: Ten artykuł zawiera informacje o pojęciach dotyczących dzienników diagnostycznych dotyczących tematu usługi Azure Event Grid lub domeny.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 93e7e47cbcc1ab9542ba333b89f7dd655a412489
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82630254"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Dzienniki diagnostyczne dla Azure Event Grid tematów/domen
Ustawienia diagnostyczne umożliwiają użytkownikom Event Grid przechwytywanie i wyświetlanie dzienników **błędów publikowania i dostarczania** na koncie magazynu, centrum zdarzeń lub w obszarze roboczym log Analytics. Ten artykuł zawiera schemat dzienników i przykład wpisu dziennika.


## <a name="schema-for-publishdelivery-failure-logs"></a>Schemat dzienników błędów publikowania/dostarczania

| Nazwa właściwości | Typ danych | Opis |
| ------------- | --------- | ----------- | 
| Godzina | DateTime | Godzina wygenerowania wpisu dziennika <p>**Przykładowa wartość:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | Nazwa subskrypcji zdarzeń <p>**Przykładowa wartość:** "EVENTSUB1"</p> <p>Ta właściwość istnieje tylko w przypadku dzienników błędów dostarczania.</p>  |
| Kategoria | String | Nazwa kategorii dziennika. <p>**Przykładowe wartości:** "DeliveryFailures" lub "PublishFailures" | 
| OperationName | String | Nazwa operacji wykonywanej podczas napotkania błędu.<p>**Przykładowe wartości:** "Dostarcz" w przypadku niepowodzeń dostarczania. |
| Komunikat | String | Komunikat dziennika dla użytkownika z wyjaśnieniem przyczyny niepowodzenia i innych dodatkowych szczegółów. |
| ResourceId | String | Identyfikator zasobu tematu/zasobu domeny<p>**Przykładowe wartości:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Przykład

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak włączyć dzienniki diagnostyczne dla tematów lub domen, zobacz [Włączanie dzienników diagnostycznych](enable-diagnostic-logs-topic.md).
