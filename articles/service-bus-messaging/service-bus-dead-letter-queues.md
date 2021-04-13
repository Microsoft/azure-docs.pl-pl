---
title: Service Bus kolejek utraconych wiadomości | Microsoft Docs
description: Opisuje kolejki utraconych wiadomości w Azure Service Bus. Kolejki Service Bus i subskrypcje tematów zapewniają pomocniczą podkolejkę o nazwie Kolejka utraconych wiadomości.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 6459c8edd03427357810c1ad30161e87c18e059c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304328"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Przegląd Service Busych kolejek utraconych

Kolejki Azure Service Bus i subskrypcje tematów zapewniają pomocniczą podkolejkę o nazwie *Kolejka utraconych wiadomości* (DLQ). Kolejka utraconych wiadomości nie musi być jawnie utworzona i nie może być usunięta ani zarządzana niezależnie od jednostki głównej.

W tym artykule opisano kolejki utraconych wiadomości w Service Bus. Większość dyskusji przedstawiono w [przykładowej kolejce utraconych wiadomości](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) w witrynie GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejka utraconych wiadomości

Kolejka utraconych wiadomości polega na zapełnieniu komunikatów, których nie można dostarczyć do żadnego odbiorcy ani komunikatów, których nie można przetworzyć. Następnie można usunąć wiadomości z DLQ i sprawdzić. Aplikacja może, z pomocą operatora, poprawić problemy i ponownie przesłać komunikat, należy zarejestrować fakt, że wystąpił błąd i podjąć działania naprawcze. 

Z punktu widzenia interfejsu API i protokołu DLQ jest głównie podobne do każdej innej kolejki, z tą różnicą, że komunikaty mogą być przesyłane tylko za pośrednictwem operacji utraconej jednostki nadrzędnej. Ponadto nie zaobserwowano czasu wygaśnięcia i nie można martwić się o wiadomość z DLQ. Kolejka utraconych wiadomości w pełni obsługuje dostarczanie z blokadą i operacje transakcyjne.

Nie istnieje automatyczne czyszczenie DLQ. Komunikaty pozostają w DLQ, dopóki nie zostaną jawnie pobrane z DLQ i zakończone komunikatem utraconych wiadomości.


## <a name="dlq-message-count"></a>Liczba komunikatów DLQ
Nie jest możliwe uzyskanie liczby komunikatów w kolejce utraconych danych na poziomie tematu. Wynika to z faktu, że komunikaty nie znajdują się na poziomie tematu, chyba że Service Bus zgłasza błąd wewnętrzny. Zamiast tego, gdy nadawca wysyła komunikat do tematu, komunikat jest przekazywany do subskrypcji tematu w milisekundach, co oznacza, że nie znajduje się już na poziomie tematu. W związku z tym można zobaczyć komunikaty w DLQ skojarzone z subskrypcją dla tematu. W poniższym przykładzie **eksplorator Service Bus** pokazuje, że w DLQ dla subskrypcji "test1" znajdują się obecnie 62 komunikaty. 

![Liczba komunikatów DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Liczbę komunikatów DLQ można także uzyskać za pomocą polecenia interfejsu CLI platformy Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Przeniesienie komunikatów do DLQ
Istnieje kilka działań w Service Bus, które powodują wypychanie komunikatów do DLQ z poziomu samego aparatu obsługi komunikatów. Aplikacja może również jawnie przenosić komunikaty do DLQ. Do komunikatów utraconych są dodawane następujące dwie właściwości (przyczyna utraconych wiadomości i opis utraconych wiadomości). Aplikacje mogą definiować własne kody dla właściwości Przyczyna utraconych wiadomości, ale system ustawia następujące wartości.

| Przyczyna utraconych wiadomości | Opis błędu utraconych wiadomości |
| --- | --- |
|HeaderSizeExceeded |Przekroczono limit przydziału rozmiaru dla tego strumienia. |
|TTLExpiredException |Komunikat wygasł i został uznany za utracony. Aby uzyskać szczegółowe informacje, zobacz sekcję [Time to Live](#time-to-live) . |
|Identyfikator sesji ma wartość null. |Jednostka z obsługą sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
|MaxTransferHopCountExceeded | Maksymalna liczba dozwolonych przeskoków podczas przekazywania między kolejkami. Wartość jest równa 4. |
| MaxDeliveryCountExceededExceptionMessage | Nie można użyć komunikatu po osiągnięciu maksymalnej liczby prób dostarczenia. Aby uzyskać szczegółowe informacje, zobacz sekcję [Maximum Delivery Count](#maximum-delivery-count) . |

## <a name="maximum-delivery-count"></a>Maksymalna liczba dostaw
Istnieje limit liczby prób dostarczenia komunikatów dla kolejek i subskrypcji Service Bus. Wartość domyślna to 10. Za każdym razem, gdy komunikat został dostarczony w ramach blokady wglądu, ale został jawnie porzucony lub blokada wygasła, liczba dostaw w komunikacie jest zwiększana. Gdy liczba dostaw przekracza limit, komunikat jest przenoszony do DLQ. Przyczyna utraconych wiadomości dla komunikatu w DLQ jest ustawiona na: MaxDeliveryCountExceeded. Nie można wyłączyć tego zachowania, ale Maksymalna liczba dostaw można ustawić na dużą liczbę.

## <a name="time-to-live"></a>Czas wygaśnięcia
Po włączeniu obsługi utraconych wiadomości w kolejkach lub subskrypcjach wszystkie komunikaty wygasające są przenoszone do DLQ. Kod przyczyny utraconych wiadomości jest ustawiany na: TTLExpiredException.

Wygasłe komunikaty są przeczyszczane i przenoszone do DLQ, gdy istnieje co najmniej jeden aktywny odbiornik ściągający z kolejki głównej lub subskrypcji. Odroczone komunikaty nie zostaną również przeczyszczone i przeniesione do kolejki utraconych wiadomości po ich wygaśnięciu. To zachowanie jest celowe.

## <a name="errors-while-processing-subscription-rules"></a>Błędy podczas przetwarzania reguł subskrypcji
Po włączeniu utraconych wiadomości na wyjątkach oceny filtru wszystkie błędy występujące podczas wykonywania reguły filtru SQL subskrypcji są przechwytywane w DLQ wraz z komunikatem o błędzie. Nie należy używać tej opcji w środowisku produkcyjnym, w którym nie wszystkie typy komunikatów mają Subskrybenci.

## <a name="application-level-dead-lettering"></a>Utraconych wiadomości na poziomie aplikacji
Oprócz funkcji utraconych wiadomości dostarczonych przez system aplikacje mogą jawnie odrzucać nieakceptowane wiadomości za pomocą DLQ. Mogą one zawierać komunikaty, których nie można prawidłowo przetworzyć z powodu wszelkiego rodzaju problemów z systemem, komunikatów, które przechowują źle sformułowane ładunki, lub komunikatów, które nie są uwierzytelniane, gdy jest używany jakiś schemat zabezpieczeń na poziomie wiadomości.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Utracono martwe scenariusze w scenariuszach Prześlij dalej lub Wyślij za pomocą
Komunikaty będą wysyłane do kolejki przesyłania utraconych wiadomości w następujących warunkach:

- Komunikat przechodzi przez więcej niż cztery kolejki lub tematy, które są [powiązane ze sobą](service-bus-auto-forwarding.md).
- Kolejka docelowa lub temat jest wyłączony lub usunięty.
- Docelowa Kolejka lub temat przekracza maksymalny rozmiar jednostki.

## <a name="path-to-the-dead-letter-queue"></a>Ścieżka do kolejki utraconych wiadomości
Możesz uzyskać dostęp do kolejki utraconych wiadomości za pomocą następującej składni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o Service Bus kolejkach, zobacz następujące artykuły:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Porównanie kolejek platformy Azure i kolejek Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

