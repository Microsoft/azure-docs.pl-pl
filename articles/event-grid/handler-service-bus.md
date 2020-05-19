---
title: Service Bus kolejek i tematów jako programów obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można używać kolejek Service Bus i tematów jako programów obsługi zdarzeń Azure Event Grid zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 201d3203d845ce84207d103750709fe2ff93f022
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598577"
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

## <a name="service-bus-topics"></a>Tematy Service Bus

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

## <a name="message-properties"></a>Właściwości komunikatu
Jeśli używasz **tematu Service Bus lub kolejki** jako programu obsługi zdarzeń dla zdarzeń z Event Grid, ustaw następujące nagłówki komunikatów: 

| Nazwa właściwości | Opis |
| ------------- | ----------- | 
| AEG-Subscription-Name | Nazwa subskrypcji zdarzeń. |
| AEG-Delivery-Count | <p>Liczba prób wykonanych dla zdarzenia.</p> <p>Przykład: "1"</p> |
| AEG — typ zdarzenia | <p>Typ zdarzenia.</p><p> Przykład: "Microsoft. Storage. blobCreated"</p> | 
| AEG — wersja metadanych | <p>Wersja metadanych zdarzenia.</p> <p>Przykład: "1".</p><p> W przypadku **schematu zdarzeń Event Grid**ta właściwość reprezentuje wersję metadanych i **schemat zdarzeń w chmurze**, który reprezentuje **wersję specyfikacji**. </p>|
| AEG — wersja danych | <p>Wersja danych zdarzenia.</p><p>Przykład: "1".</p><p>W przypadku **schematu zdarzeń Event Grid**ta właściwość reprezentuje wersję danych i **schemat zdarzeń w chmurze**, nie ma zastosowania.</p> |

## <a name="message-headers"></a>Nagłówki komunikatów
W przypadku wysyłania zdarzenia do kolejki Service Bus lub tematu jako komunikatu obsługiwanego przez brokera `messageid` komunikat z brokerem jest **identyfikatorem zdarzenia**.

Identyfikator zdarzenia będzie utrzymywany przez ponowną dostawę zdarzenia, dzięki czemu można uniknąć zduplikowanych dostaw przez włączenie **wykrywania duplikatów** w jednostce usługi Service Bus. Zalecamy włączenie czasu trwania wykrywania duplikatów w jednostce Service Bus tak, aby był to czas wygaśnięcia (TTL) zdarzenia lub maksymalny czas trwania ponawiania prób, w zależności od tego, co jest dłuższe.

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 
