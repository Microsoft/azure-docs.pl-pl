---
title: Azure Service Bus komunikaty z wyprzedzeniem | Microsoft Docs
description: Zwiększ wydajność przez pobranie Azure Service Bus komunikatów. Komunikaty są łatwo dostępne do pobrania lokalnego przed zażądaniem aplikacji.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05e23b0590f0c04171efda8fb561b4c2664ed096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341060"
---
# <a name="prefetch-azure-service-bus-messages"></a>Pobieranie z wyprzedzeniem Azure Service Bus komunikatów

Po włączeniu *pobierania z wyprzedzeniem* na którymkolwiek z oficjalnych klientów Service Bus odbiorca w cichy sposób uzyskuje więcej komunikatów, aż do limitu [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) .

Pojedyncze początkowe wywołanie metody [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) lub [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) w związku z tym uzyskuje komunikat do bezpośredniego zużycia, który jest zwracany zaraz po udostępnieniu. Klient uzyskuje następnie dalsze komunikaty w tle, aby wypełnić bufor pobierania z wyprzedzeniem.

## <a name="enable-prefetch"></a>Włącz pobieranie z wyprzedzeniem

W przypadku platformy .NET można włączyć funkcję pobierania z wyprzedzeniem, ustawiając właściwość [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) elementu **MessageReceiver**, **QueueClient** lub **SubscriptionClient** na liczbę większą od zera. Ustawienie wartości zero powoduje wyłączenie pobierania z wyprzedzeniem.

To ustawienie można łatwo dodać do strony odbiorczej ustawień "Przykłady [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) lub [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) ", aby zobaczyć efekt w tych kontekstach.

Gdy komunikaty są dostępne w buforze pobierania z wyprzedzeniem, **wszystkie kolejne** / wywołania **ReceiveAsync** są natychmiast spełnione od buforu, a bufor jest uzupełniany w tle w miarę dostępności miejsca. Jeśli nie ma żadnych komunikatów do dostarczenia, operacja Receive opróżnia bufor, a następnie czeka lub blokuje, zgodnie z oczekiwaniami.

Pobieranie z wyprzedzeniem działa również w taki sam sposób, jak interfejsy API [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) i [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) .

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Jeśli jest to szybsze, dlaczego nie jest domyślnie pobierana opcja pobierania z wyprzedzeniem?

Pobieranie z wyprzedzeniem przyspiesza przepływ komunikatów, dzięki czemu komunikat jest łatwo dostępny do pobrania lokalnego, gdy i przed wymaga aplikacji. Ten wzrost przepływności jest wynikiem wymiany, którą autor aplikacji musi jawnie wprowadzić:

W trybie odbierania [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) wszystkie komunikaty, które są uzyskiwane w buforze pobierania z wyprzedzeniem, nie są już dostępne w kolejce i znajdują się tylko w buforze pobierania z pamięci, dopóki nie zostaną odebrane do aplikacji za pomocą interfejsów API **odbierania** / **ReceiveAsync** lub **OnMessage** / **OnMessageAsync** . Jeśli aplikacja zakończy działanie przed odebraniem komunikatów do aplikacji, te komunikaty zostaną nieodwracalnie utracone.

W trybie odbierania [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) komunikaty pobierane do buforu pobierania z wyprzedzeniem są uzyskiwane w buforze w stanie zablokowanym i mają zegar limitu czasu dla taktowania blokady. Jeśli bufor pobierania z wyprzedzeniem jest duży, a przetwarzanie trwa tak długo, że blokady komunikatów wygasają w buforze pobierania lub nawet wtedy, gdy aplikacja przetwarza komunikat, może wystąpić pewne zdarzenia mylące dla aplikacji.

Aplikacja może uzyskać komunikat z wygasłą lub bezterminowo zablokowaniem. Jeśli tak, aplikacja może przetworzyć komunikat, ale następnie sprawdzić, czy nie można go zakończyć z powodu wygaśnięcia blokady. Aplikacja może sprawdzić Właściwość [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (która jest zależna od zegara między brokerem a zegarem komputera lokalnego). Jeśli blokada wiadomości wygasła, aplikacja musi zignorować komunikat. nie należy wykonać wywołania interfejsu API lub z komunikatem. Jeśli komunikat nie wygasł, ale jego wygaśnięcie jest bezpośrednie, blokadę można odnowić i rozszerzyć przez inny domyślny okres blokady przez wywołanie [komunikatu. RenewLock ()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Jeśli blokada w trybie dyskretnym wygaśnie w buforze pobierania z wyprzedzeniem, komunikat jest traktowany jako porzucony i ponownie udostępniany do pobrania z kolejki. Może to spowodować pobranie go do buforu pobierania z wyprzedzeniem; umieszczone na końcu. Jeśli bufor pobierania z wyprzedzeniem nie może być zwykle używany przez czas wygaśnięcia komunikatu, spowoduje to wielokrotne pobranie wstępnie pobranych komunikatów, ale w przypadku przekroczenia maksymalnej liczby dostaw (w prawidłowym stanie zablokowanym).

Jeśli potrzebujesz wysokiego stopnia niezawodności do przetwarzania komunikatów, a przetwarzanie zajmuje znaczącą ilość pracy i czasu, zaleca się korzystanie z funkcji pobierania z wyprzedzeniem, a nie w ogóle.

Jeśli potrzebujesz dużej przepływności, a przetwarzanie komunikatów jest zwykle tanie, pobieranie z wyprzedzeniem zapewnia znaczne korzyści z przepływności.

Maksymalna liczba pobieranych odczytów i czas trwania blokady skonfigurowane w kolejce lub subskrypcji musi być zrównoważona, tak że limit czasu blokady co najmniej przekracza całkowity oczekiwany czas przetwarzania komunikatów dla maksymalnego rozmiaru buforu pobierania z wyprzedzeniem i jeden komunikat. W tym samym czasie limit czasu blokady nie powinien być tak długi, aby komunikaty mogły przekroczyć maksymalną [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , w związku z czym przed ponownym przekazaniem nie będzie wymagane jego zablokowanie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
