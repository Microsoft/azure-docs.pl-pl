---
title: Przekazuj połączenie hybrydowe jako procedurę obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można użyć Azure Relay połączeń hybrydowych jako programów obsługi zdarzeń Azure Event Grid zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800880"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Przekazuj połączenie hybrydowe jako procedurę obsługi zdarzeń dla zdarzeń Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Relay** jest jednym z nich. 

Za pomocą usługi Azure **Relay połączenia hybrydowe** wysyłać zdarzenia do aplikacji znajdujących się w sieci przedsiębiorstwa i nie mają dostępnego publicznie punktu końcowego.

## <a name="tutorials"></a>Samouczki
Zapoznaj się z poniższym samouczkiem, aby zapoznać się z przykładem użycia połączenia hybrydowego Azure Relay jako procedury obsługi zdarzeń. 

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: wysyłanie zdarzeń do połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła niestandardowe zdarzenie do istniejącego połączenia hybrydowego w celu przetworzenia przez aplikację odbiornika. |

## <a name="rest-example-for-put"></a>Przykład REST (na potrzeby PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 