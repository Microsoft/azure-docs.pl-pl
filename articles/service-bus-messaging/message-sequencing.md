---
title: Azure Service Bus sekwencjonowanie komunikatów i sygnatury czasowe | Microsoft Docs
description: W tym artykule wyjaśniono, jak zachować sekwencjonowanie i porządkowanie (z sygnaturami czasowymi) komunikatów Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fdb18802e576ad114fd3f783d5efd7bb826a5f94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341175"
---
# <a name="message-sequencing-and-timestamps"></a>Sekwencjonowanie i sygnatury czasowe komunikatów

Sekwencjonowanie i sygnatury czasowe to dwie funkcje, które są zawsze włączone na wszystkich Service Bus jednostkach i powierzchni przez właściwości [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) i [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) odebranych lub przeglądanych komunikatów.

W przypadku, gdy bezwzględne kolejność komunikatów jest istotna i/lub w którym odbiorca potrzebuje wiarygodnego identyfikatora unikatowego dla komunikatów, Broker wysyła komunikaty z niewolnej przerwy, zwiększając numer sekwencyjny względem kolejki lub tematu. W przypadku jednostek partycjonowanych numer sekwencyjny jest wystawiany względem partycji.

Wartość **SequenceNumber** jest unikatowym 64-bitową liczbą całkowitą przypisaną do wiadomości, gdy zostanie ona zaakceptowana i zapisana przez brokera i działa jako wewnętrzny identyfikator. W przypadku jednostek partycjonowanych najwyżej 16 bitów odzwierciedla identyfikator partycji. Numery sekwencji są przenoszone do zera, gdy zostanie wyczerpany zakres 48/64-bitowy.

Numer sekwencyjny może być traktowany jako unikatowy identyfikator, ponieważ jest przypisany przez centralne i neutralne władze, a nie przez klientów. Reprezentuje również prawdziwą kolejność przybycia i jest bardziej precyzyjna niż sygnatura czasowa jako kryterium zamówienia, ponieważ sygnatury czasowe mogą nie mieć wystarczającej rozdzielczości w przypadku najwyższej wartości komunikatów i mogą być zależne od tego, co się dzieje w sytuacjach, gdy własność brokera przechodzi między węzłami.

Niezależny porządek przybycia, na przykład w scenariuszach biznesowych, w których ograniczona liczba oferowanych towarów jest obsługiwana w oparciu o pierwszą pobraną wartość, podczas gdy dostawy są ostatnie. jest to przykład z uzgadnianiem sprzedaży biletów.

Funkcja sygnatur czasowych działa jako urząd neutralny i godny zaufania, który dokładnie przechwytuje czas UTC przybycia komunikatu, odzwierciedlony we właściwości **EnqueuedTimeUtc** . Ta wartość jest przydatna, jeśli scenariusz biznesowy zależy od terminów, takich jak to, czy element roboczy został przesłany w określonym dniu przed północy, ale przetwarzanie jest daleko do zaległości kolejki.

## <a name="scheduled-messages"></a>Zaplanowane wiadomości

Komunikaty można przesyłać do kolejki lub tematu w celu wykonania opóźnionego przetwarzania, na przykład aby zaplanować udostępnienie zadania do przetwarzania przez system w określonym czasie. Ta funkcja realizuje niezawodne dystrybuowany harmonogram oparty na czasie.

Zaplanowane wiadomości nie są zmaterializowania w kolejce do czasu zdefiniowanej kolejki. Przed upływem tego czasu można anulować zaplanowanych komunikatów. Anulowanie usuwa komunikat.

Komunikaty można zaplanować przez ustawienie właściwości [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) podczas wysyłania komunikatu za pomocą zwykłej ścieżki wysyłania lub jawnie za pomocą interfejsu API [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) . Ta druga natychmiast zwraca zaplanowaną wiadomość **SequenceNumber**, której można później użyć do anulowania zaplanowanego komunikatu w razie potrzeby. Zaplanowanych komunikatów i ich numerów sekwencji można także odnaleźć przy użyciu funkcji [przeglądania komunikatów](message-browsing.md).

**SequenceNumber** dla zaplanowanego komunikatu jest prawidłowy tylko wtedy, gdy komunikat jest w tym stanie. Gdy komunikat przechodzi do stanu aktywnego, komunikat jest dołączany do kolejki, tak jakby był umieszczany w kolejce na bieżącym miejscu, co obejmuje przypisanie nowego **SequenceNumber**.

Ponieważ funkcja jest zakotwiczenia w poszczególnych wiadomościach, a komunikaty mogą być umieszczane tylko raz, Service Bus nie obsługuje cyklicznych harmonogramów komunikatów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)