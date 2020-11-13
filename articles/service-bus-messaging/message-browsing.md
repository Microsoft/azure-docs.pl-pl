---
title: Azure Service Bus — Przeglądanie komunikatów
description: Przeglądanie i wgląd Service Bus komunikaty umożliwiają klientowi Azure Service Bus Wyliczenie wszystkich komunikatów w kolejce lub subskrypcji.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553645"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów

Przeglądanie komunikatów lub wgląd, umożliwia klientowi Service Bus Wyliczenie wszystkich komunikatów w kolejce lub subskrypcji w celach diagnostycznych i debugowania.

Operacja wglądu w kolejce zwraca wszystkie komunikaty w kolejce, a nie tylko te, które są dostępne do natychmiastowego pozyskiwania z `Receive()` lub `OnMessage()` pętlą. `State`Właściwość każdego komunikatu informuje, czy wiadomość jest aktywna (dostępna do odebrania), [odroczona](message-deferral.md)lub [zaplanowana](message-sequencing.md). Operacja wglądu w subskrypcję zwraca wszystkie komunikaty oprócz zaplanowanych komunikatów w dzienniku komunikatów subskrypcji. 

Zużyte i wygasłe komunikaty są czyszczone przez asynchroniczne uruchomienie "wyrzucania elementów bezużytecznych". Ten krok może niekoniecznie wystąpić natychmiast po wygaśnięciu komunikatów. Dlatego `Peek` może zwrócić komunikaty, które już wygasły. Te komunikaty zostaną usunięte lub utracone w przypadku wywołania operacji odbierania dla kolejki lub subskrypcji za następnym razem. Należy zachować takie zachowanie podczas próby odzyskania odroczonych komunikatów z kolejki. Wygasły komunikat nie kwalifikuje się do regularnego pobierania w inny sposób, nawet gdy jest zwracany przez wgląd. Zwróceniem tych komunikatów jest zaprojektowanie jako wgląd w narzędzie diagnostyczne odzwierciedlające bieżący stan dziennika.

Funkcja wgląd zwraca również komunikaty, które zostały zablokowane i są aktualnie przetwarzane przez innych odbiorników. Jednak ponieważ funkcja wgląd zwraca odłączoną migawkę, nie można zaobserwować stanu blokady komunikatu w przypadku wiadomości z wglądem. Właściwości [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) i [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) zwracają [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , gdy aplikacja próbuje je odczytać.

## <a name="peek-apis"></a>Wgląd do interfejsów API

Metody [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) i [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) istnieją w bibliotekach klienckich platformy .NET i Java oraz w obiektach odbiornika: **MessageReceiver** , **MessageSession**. Wgląd w kolejki, subskrypcje i odpowiadające im kolejki utraconych wiadomości.

Gdy jest wywoływana wielokrotnie, program **Peek** wylicza wszystkie komunikaty w kolejce lub dzienniku subskrypcji, w kolejności od najniższego dostępnego numeru sekwencji do najwyższego. Jest to kolejność, w której wiadomości zostały dodane do kolejki, a nie kolejność, w jakiej komunikaty mogą być ostatecznie pobrane.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) pobiera wiele komunikatów i zwraca je jako Wyliczenie. Jeśli żadne komunikaty nie są dostępne, obiekt wyliczenia jest pusty, a nie null.

Można również użyć przeciążenia metody z [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , od którego należy zacząć, a następnie wywołać Przeciążenie metody bez parametrów, aby wyliczyć więcej. **PeekBatch** funkcje są równoważne, ale Pobiera zestaw komunikatów jednocześnie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
