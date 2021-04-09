---
title: Azure Service Bus — Przeglądanie komunikatów
description: Przeglądanie i wgląd Service Bus komunikaty umożliwiają klientowi Azure Service Bus Wyliczenie wszystkich komunikatów w kolejce lub subskrypcji.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090311"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów

Przeglądanie komunikatów lub wgląd, umożliwia klientowi Service Bus Wyliczenie wszystkich komunikatów w kolejce lub subskrypcji w celach diagnostycznych i debugowania.

Operacja wglądu w **kolejce** zwraca wszystkie komunikaty w kolejce, a nie tylko te, które są dostępne do natychmiastowego pozyskiwania. Operacja wglądu w **subskrypcję** zwraca wszystkie komunikaty oprócz zaplanowanych komunikatów w dzienniku komunikatów subskrypcji. 

Zużyte i wygasłe komunikaty są czyszczone przez asynchroniczne uruchomienie "wyrzucania elementów bezużytecznych". Ten krok może niekoniecznie wystąpić natychmiast po wygaśnięciu komunikatów. Dlatego operacja wglądu może zwracać komunikaty, które już wygasły. Te komunikaty zostaną usunięte lub utracone w przypadku wywołania operacji odbierania dla kolejki lub subskrypcji za następnym razem. Należy zachować takie zachowanie podczas próby odzyskania odroczonych komunikatów z kolejki. Wygasły komunikat nie kwalifikuje się do regularnego pobierania w inny sposób, nawet gdy jest zwracany przez wgląd. Zwróceniem tych komunikatów jest zaprojektowanie jako wgląd w narzędzie diagnostyczne odzwierciedlające bieżący stan dziennika.

Funkcja wgląd zwraca również komunikaty, które zostały zablokowane i są aktualnie przetwarzane przez innych odbiorników. Jednak ponieważ funkcja wgląd zwraca odłączoną migawkę, nie można zaobserwować stanu blokady komunikatu w przypadku wiadomości z wglądem.

## <a name="peek-apis"></a>Wgląd do interfejsów API
## <a name="azuremessagingservicebus"></a>[Azure. Messaging. ServiceBus](#tab/dotnet)
Metody [PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) i [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) istnieją w obiektach odbiornika: `ServiceBusReceiver` , `ServiceBusSessionReceiver` . Wgląd w kolejki, subskrypcje i odpowiadające im kolejki utraconych wiadomości.

Gdy jest wywoływana wielokrotnie, program `PeekMessageAsync` wylicza wszystkie komunikaty w kolejce lub dzienniku subskrypcji, w kolejności od najniższego dostępnego numeru sekwencyjnego do najwyższego. Jest to kolejność, w której wiadomości zostały dodane do kolejki, a nie kolejność, w jakiej komunikaty mogą być ostatecznie pobrane.
PeekMessagesAsync pobiera wiele komunikatów i zwraca je jako Wyliczenie. Jeśli żadne komunikaty nie są dostępne, obiekt wyliczenia jest pusty, a nie null.

Można również wypełnić parametr [fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) z SequenceNumber, na którym ma się rozpocząć, a następnie wywołać metodę ponownie bez określenia parametru do dalszej wyliczenia. `PeekMessagesAsync` funkcje są równoważne, ale Pobiera zestaw komunikatów jednocześnie.


## <a name="microsoftazureservicebus"></a>[Microsoft. Azure. ServiceBus](#tab/dotnetold)
Metody [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) i [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) istnieją w obiektach odbiornika: `MessageReceiver` , `MessageSession` . Wgląd w kolejki, subskrypcje i odpowiadające im kolejki utraconych wiadomości.

Gdy jest wywoływana wielokrotnie, program `Peek` wylicza wszystkie komunikaty w kolejce lub dzienniku subskrypcji, w kolejności od najniższego dostępnego numeru sekwencyjnego do najwyższego. Jest to kolejność, w której wiadomości zostały dodane do kolejki, a nie kolejność, w jakiej komunikaty mogą być ostatecznie pobrane.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) pobiera wiele komunikatów i zwraca je jako Wyliczenie. Jeśli żadne komunikaty nie są dostępne, obiekt wyliczenia jest pusty, a nie null.

Można również użyć przeciążenia metody z [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , od którego należy zacząć, a następnie wywołać Przeciążenie metody bez parametrów, aby wyliczyć więcej. **PeekBatch** funkcje są równoważne, ale Pobiera zestaw komunikatów jednocześnie.


---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
