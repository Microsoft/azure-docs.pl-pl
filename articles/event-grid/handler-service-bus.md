---
title: Service Bus kolejek i tematów jako programów obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można używać kolejek Service Bus i tematów jako programów obsługi zdarzeń Azure Event Grid zdarzeń.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 2b18009f8fb31f1a5f057c7395781f63f182847f
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96024218"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus kolejek i tematów jako programów obsługi zdarzeń dla zdarzeń Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Service Bus** jest jednym z nich. 

Możesz użyć kolejki lub tematu usługi jako procedury obsługi dla zdarzeń z Event Grid. 

## <a name="service-bus-queues"></a>Kolejki usługi Service Bus
Można kierować zdarzenia w Event Grid bezpośrednio do kolejek Service Bus do użycia w scenariuszach buforowania lub & poleceń w aplikacjach dla przedsiębiorstw.

W Azure Portal podczas tworzenia subskrypcji zdarzeń wybierz **kolejkę Service Bus Queue** jako typ punktu końcowego, a następnie kliknij pozycję **Wybierz punkt końcowy** , aby wybrać kolejkę Service Bus.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Używanie interfejsu wiersza polecenia do dodawania procedury obsługi kolejki Service Bus

W przypadku interfejsu wiersza polecenia platformy Azure Poniższy przykład subskrybuje i łączy temat usługi Event Grid z kolejką Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Tematy usługi Service Bus

Zdarzenia w Event Grid można kierować bezpośrednio do tematów Service Bus, aby obsłużyć zdarzenia systemowe platformy Azure za pomocą tematów Service Bus lub dla scenariuszy poleceń & kontroli komunikatów.

W Azure Portal podczas tworzenia subskrypcji zdarzeń wybierz pozycję **Service Bus temat** jako typ punktu końcowego, a następnie kliknij pozycję **Wybierz i punkt końcowy** , aby wybrać temat Service Bus.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Używanie interfejsu wiersza polecenia w celu dodania procedury obsługi tematu Service Bus

W przypadku interfejsu wiersza polecenia platformy Azure Poniższy przykład subskrybuje i łączy temat usługi Event Grid z kolejką Service Bus:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

W przypadku wysyłania zdarzenia do kolejki Service Bus lub tematu jako komunikatu obsługiwanego przez brokera `messageid` komunikat z brokerem jest wewnętrznym identyfikatorem systemu.

Wewnętrzny identyfikator systemu dla wiadomości będzie utrzymywany przez ponowną dostawę zdarzenia, dzięki czemu można uniknąć zduplikowanych dostaw przez włączenie **wykrywania duplikatów** w jednostce usługi Service Bus. Zalecamy włączenie czasu trwania wykrywania duplikatów w jednostce Service Bus tak, aby był to czas wygaśnięcia (TTL) zdarzenia lub maksymalny czas trwania ponawiania prób, w zależności od tego, co jest dłuższe.

## <a name="rest-examples-for-put"></a>Przykłady REST (na potrzeby PUT)

### <a name="service-bus-queue"></a>Kolejka usługi Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus dostarczania kolejki z tożsamością zarządzaną

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Temat usługi Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus w temacie dostarczanie z tożsamością zarządzaną

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 
