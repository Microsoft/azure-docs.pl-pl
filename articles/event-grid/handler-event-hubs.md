---
title: Centrum zdarzeń jako program obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, jak można użyć centrów zdarzeń jako programów obsługi zdarzeń dla zdarzeń Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96005637"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Centrum zdarzeń jako program obsługi zdarzeń dla zdarzeń Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Program obsługi wykonuje akcję, aby przetworzyć zdarzenie. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **platforma azure Event Hubs** jest jednym z nich. 

Użyj **Event Hubs** , gdy rozwiązanie pobiera zdarzenia od Event Grid szybciej niż może przetwarzać zdarzenia. Gdy zdarzenia znajdują się w centrum zdarzeń, aplikacja może przetwarzać zdarzenia z centrum zdarzeń zgodnie z własnym harmonogramem. Przetwarzanie zdarzenia można skalować w celu obsługi zdarzeń przychodzących.

## <a name="tutorials"></a>Samouczki
Zobacz poniższe przykłady: 

|Tytuł  |Opis  |
|---------|---------|
| [Szybki Start: kierowanie zdarzeń niestandardowych do usługi Azure Event Hubs przy użyciu interfejsu wiersza polecenia platformy Azure](custom-event-to-eventhub.md) | Wysyła niestandardowe zdarzenie do centrum zdarzeń w celu przetworzenia przez aplikację. |
| [Szablon Menedżer zasobów: Tworzenie Event Grid niestandardowego tematu i wysyłanie zdarzeń do centrum zdarzeń](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Szablon Menedżer zasobów, który tworzy subskrypcję tematu niestandardowego. Wysyła zdarzenia do Event Hubs platformy Azure. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>Przykłady REST (na potrzeby PUT)


### <a name="event-hub"></a>Centrum zdarzeń

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Centrum zdarzeń — dostarczanie z tożsamością zarządzaną

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
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 
