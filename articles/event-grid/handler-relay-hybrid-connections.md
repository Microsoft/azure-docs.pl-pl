---
title: Przekazuj połączenie hybrydowe jako procedurę obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można użyć Azure Relay połączeń hybrydowych jako programów obsługi zdarzeń Azure Event Grid zdarzeń.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270206"
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