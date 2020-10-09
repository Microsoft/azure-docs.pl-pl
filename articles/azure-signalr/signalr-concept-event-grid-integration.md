---
title: Reagowanie na zdarzenia usługi Azure sygnalizujące
description: Użyj Azure Event Grid, aby subskrybować zdarzenia usługi Azure Signal Service. Inne usługi podrzędne mogą być wyzwalane przez te zdarzenia.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74158196"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagowanie na zdarzenia usługi Azure SignalR Service

Zdarzenia usługi Azure sygnalizujące umożliwiają aplikacjom reagowanie na połączenia klienckie połączone lub rozłączone przy użyciu nowoczesnych architektur bezserwerowych. Robi to bez konieczności stosowania skomplikowanego kodu lub kosztownych i nieefektywnych usług sondowania.  Zamiast tego zdarzenia są wypychane za pośrednictwem [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do subskrybentów, takich jak [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)lub nawet do własnego niestandardowego odbiornika HTTP i płacisz tylko za to, czego używasz.

Zdarzenia usługi Azure sygnalizujące są niezawodnie wysyłane do usługi Event Grid, która zapewnia niezawodne usługi dostarczania dla aplikacji dzięki rozbudowanym zasadom ponawiania prób i dostarczaniu wiadomości utraconych. Aby dowiedzieć się więcej, zobacz [Event Grid dostarczania komunikatów i ponów próbę](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Model Event Grid](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Stan bezserwerowy
Zdarzenia usługi Azure sygnalizujące są aktywne tylko wtedy, gdy połączenia klientów są w stanie bezserwerowym. Ogólnie mówiąc, jeśli klient nie jest kierowany do serwera Hub, przejdzie w stan bezserwerowy. Tryb klasyczny działa tylko wtedy, gdy koncentrator, z którym nawiąże połączenie z klientem, nie ma serwera centralnego. Jednak tryb bezserwerowy jest zalecany, aby uniknąć pewnego problemu. Aby uzyskać więcej informacji na temat trybu usługi, zobacz [jak wybrać tryb usługi](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Dostępne zdarzenia usługi Azure sygnalizujące
Funkcja Event Grid używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Subskrypcje zdarzeń usługi Azure Signal Service obsługują dwa typy zdarzeń:  

|Nazwa zdarzenia|Opis|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Uruchamiany, gdy połączenie z klientem jest nawiązywane.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Uruchamiany, gdy połączenie z klientem zostanie odłączone.|

## <a name="event-schema"></a>Schemat zdarzeń
Zdarzenia usługi Azure sygnalizujące zawierają wszystkie informacje potrzebne do reagowania na zmiany danych. Możesz zidentyfikować zdarzenie usługi platformy Azure z właściwością eventType rozpoczynającą się od "Microsoft. SignalRService". Dodatkowe informacje na temat użycia właściwości zdarzeń Event Grid są udokumentowane w [Event Grid schemacie zdarzeń](../event-grid/event-schema.md).  

Oto przykład zdarzenia połączonego z klientem:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń usługi sygnalizującej](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i przekaż zdarzenia usługi Azure Signal Service:

> [!div class="nextstepaction"]
> [Wypróbuj Event Grid integrację z usługą Azure Signal Service](./signalr-howto-event-grid-integration.md) 
>  [Informacje o Event Grid](../event-grid/overview.md)
