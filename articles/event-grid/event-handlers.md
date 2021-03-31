---
title: Obsługa zdarzeń Azure Event Grid
description: Opisuje obsługiwane procedury obsługi zdarzeń dla Azure Event Grid. Azure Automation, funkcje, Event Hubs, Połączenia hybrydowe, Logic Apps, Service Bus, Queue Storage, elementy webhook.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86117019"
---
# <a name="event-handlers-in-azure-event-grid"></a>Programy obsługi zdarzeń w Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń. Możesz również użyć dowolnego elementu webhook do obsługi zdarzeń. Elementy webhook nie muszą być hostowane na platformie Azure, aby obsługiwać zdarzenia. Event Grid obsługuje tylko punkty końcowe elementu webhook protokołu HTTPS.

## <a name="supported-event-handlers"></a>Obsługiwane programy obsługi zdarzeń
Oto obsługiwane programy obsługi zdarzeń: 

- Elementy [webhook](handler-webhooks.md). Azure Automation elementy Runbook i Logic Apps są obsługiwane za pośrednictwem elementów webhook. 
- [Usługa Azure Functions](handler-functions.md)
- [Centra zdarzeń](handler-event-hubs.md)
- [Połączenia hybrydowe w usłudze Relay](handler-relay-hybrid-connections.md)
- [Service Bus kolejki i tematy](handler-service-bus.md)
- [Kolejki magazynu](handler-storage-queues.md)

## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
