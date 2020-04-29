---
title: Azure Service Bus — Przeglądanie komunikatów
description: Przeglądanie i wgląd Service Bus komunikaty umożliwiają klientowi Azure Service Bus Wyliczenie wszystkich komunikatów znajdujących się w kolejce lub subskrypcji.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77539369"
---
# <a name="message-browsing"></a>Przeglądanie komunikatów

Przeglądanie komunikatów lub wgląd, umożliwia klientowi Service Bus Wyliczenie wszystkich komunikatów, które znajdują się w kolejce lub subskrypcji, zwykle do celów diagnostycznych i debugowania.

Operacje wglądu zwracają wszystkie komunikaty znajdujące się w kolejce lub dzienniku komunikatów subskrypcji, a nie tylko te, które są dostępne `Receive()` do natychmiastowego pozyskiwania z lub `OnMessage()` pętlą. `State` Właściwość każdego komunikatu informuje, czy wiadomość jest aktywna (dostępna do odebrania), [odroczona](message-deferral.md)lub [zaplanowana](message-sequencing.md).

Wykorzystane i wygasłe komunikaty są czyszczone przez asynchroniczne uruchomienie "wyrzucania elementów bezużytecznych" i niekoniecznie `Peek` nie tylko w przypadku wygaśnięcia komunikatów i dlatego mogą zwracać komunikaty, które już wygasły i zostaną usunięte lub utracone po następnym wywołaniu dla kolejki lub subskrypcji.

Jest to szczególnie ważne w przypadku próby odzyskania odroczonych komunikatów z kolejki. Komunikat, dla którego przekazana [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) natychmiast, nie kwalifikuje się do regularnego pobierania w inny sposób, nawet gdy jest zwracany przez wgląd. Zwracanie tych komunikatów jest celowe, ponieważ wgląd jest narzędziem diagnostycznym odzwierciedlającym bieżący stan dziennika.

Funkcja wgląd zwraca również komunikaty, które zostały zablokowane i są aktualnie przetwarzane przez innych odbiorników, ale nie zostały jeszcze zakończone. Jednak ponieważ funkcja wgląd zwraca odłączoną migawkę, nie można zaobserwować stanu blokady komunikatu w przypadku wiadomości z wglądem, a właściwości [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) i [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) zgłaszają [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , gdy aplikacja próbuje je odczytać.

## <a name="peek-apis"></a>Wgląd do interfejsów API

Metody [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) i [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) istnieją we wszystkich bibliotekach klienckich .NET i Java oraz we wszystkich obiektach odbiornika: **MessageReceiver**, **MessageSession**. Wgląd w wszystkie kolejki i subskrypcje oraz ich odpowiednie kolejki utraconych wiadomości.

Gdy jest wywoływana wielokrotnie, Metoda wglądu wylicza wszystkie komunikaty znajdujące się w kolejce lub dzienniku subskrypcji w kolejności numerów sekwencyjnych od najniższego dostępnego numeru sekwencji do najwyższej. Jest to kolejność, w której wiadomości zostały dodane do kolejki i nie jest kolejnością, w której komunikaty mogą być ostatecznie pobrane.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) pobiera wiele komunikatów i zwraca je jako Wyliczenie. Jeśli żadne komunikaty nie są dostępne, obiekt wyliczenia jest pusty, a nie null.

Można również umieścić Przeciążenie metody z [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , od którego należy zacząć, a następnie wywołać Przeciążenie metody bez parametrów, aby wyliczyć więcej. **PeekBatch** funkcje są równoważne, ale Pobiera zestaw komunikatów jednocześnie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
