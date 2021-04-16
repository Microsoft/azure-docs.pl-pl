---
title: Azure Service Bus sekwencjonowania komunikatów i znaczników czasu | Microsoft Docs
description: W tym artykule wyjaśniono, jak zachować sekwencjonowanie i kolejność (ze znacznikami czasu) Azure Service Bus komunikatów.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497102"
---
# <a name="message-sequencing-and-timestamps"></a>Sekwencjonowanie i sygnatury czasowe komunikatów

Sekwencjonowanie i oznaczanie znacznikami czasu to dwie funkcje, które są zawsze włączone we wszystkich jednostkach Service Bus i są dostępne za pośrednictwem właściwości `SequenceNumber` i `EnqueuedTimeUtc` odebranych lub przeglądanych komunikatów.

W przypadkach, w których bezwzględna kolejność komunikatów jest istotna i/lub w których konsument potrzebuje wiarygodnego unikatowego identyfikatora komunikatów, broker oznacza komunikaty bez odstępu, zwiększając numer sekwencji względem kolejki lub tematu. W przypadku jednostek podzielonych na partycje numer sekwencji jest wystawiany względem partycji.

Wartość **SequenceNumber** jest unikatową 64-bitową liczbą całkowitą przypisaną do komunikatu, ponieważ jest akceptowana i przechowywana przez brokera oraz działa jako jej wewnętrzny identyfikator. W przypadku jednostek podzielonych na partycje pierwsze 16 bitów odzwierciedla identyfikator partycji. Numery sekwencji są przewłaczane do zera po wyczerpaniu zakresu 48/64-bitowego.

Numer sekwencji może być zaufany jako unikatowy identyfikator, ponieważ jest przypisywany przez centralny i neutralny urząd, a nie przez klientów. Reprezentuje ona również rzeczywisty porządek przylotu i jest bardziej precyzyjna niż sygnatura czasowa jako kryterium zamówienia, ponieważ sygnatury czasowe mogą nie mieć wystarczająco wysokiej rozdzielczości przy skrajnych prędkościach komunikatów i mogą podlegać (jednak minimalnej) niesyłalności zegara w sytuacjach, w których własność brokera przechodzi między węzłami.

Bezwzględne zamówienie przylotu ma znaczenie na przykład w scenariuszach biznesowych, w których ograniczona liczba oferowanych towarów jest obsługiwanych na zasadzie "pierwszy przyjmij, pierwszy, pierwszy, obsługiwany", podczas gdy dostawy są ostatnie; przykładem jest sprzedaż biletów koncertowych.

Możliwość oznaczania sygnaturą czasową działa jako neutralny i wiarygodny urząd, który dokładnie przechwyci czas UTC nadejścia komunikatu, odzwierciedlony we właściwości **EnqueuedTimeUtc.** Ta wartość jest przydatna, jeśli scenariusz biznesowy zależy od terminów ostatecznych, na przykład od tego, czy element pracy został przesłany w określonym dniu przed północą, ale przetwarzanie jest daleko za zaległością kolejki.

## <a name="scheduled-messages"></a>Zaplanowane komunikaty

Komunikaty można przesyłać do kolejki lub tematu w celu wykonania opóźnionego przetwarzania, na przykład aby zaplanować udostępnienie zadania do przetwarzania przez system w określonym czasie. Ta funkcja umożliwia realizację niezawodnego rozproszonego harmonogramu opartego na czasie.

Zaplanowane komunikaty nie materializują się w kolejce do zdefiniowanego czasu w kolejce. Wcześniej można anulować zaplanowane komunikaty. Anulowanie powoduje usunięcie komunikatu.

Komunikaty można zaplanować przy użyciu dowolnego z naszych klientów na dwa sposoby:
- Użyj zwykłego interfejsu API wysyłania, ale ustaw `ScheduledEnqueueTimeUtc` właściwość komunikatu przed wysłaniem.
- Użyj interfejsu API komunikatów harmonogramu, aby przekazać zarówno normalny komunikat, jak i zaplanowaną godzinę. Spowoduje to zwrócenie funkcji **SequenceNumber** zaplanowanego komunikatu, której można później użyć do anulowania zaplanowanego komunikatu w razie potrzeby. 

Zaplanowane komunikaty i ich numery sekwencji można również wykryć przy użyciu [funkcji przeglądania komunikatów.](message-browsing.md)

**SequenceNumber** dla zaplanowanego komunikatu jest prawidłowy tylko wtedy, gdy komunikat jest w tym stanie. Gdy komunikat przechodzi do stanu aktywnego, komunikat jest dołączany do kolejki tak, jakby był w kolejce w bieżącej chwili, co obejmuje przypisanie nowego ciągu **SequenceNumber**.

Ponieważ funkcja jest zakotwiczona w poszczególnych komunikatach, a komunikaty mogą być kolejkowane tylko raz, Service Bus nie obsługuje cyklicznych harmonogramów komunikatów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na Service Bus komunikatów, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
