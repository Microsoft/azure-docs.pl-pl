---
title: Service Bus kolejki utraconych | Microsoft Docs
description: Opisuje kolejki utraconych komunikatów w Azure Service Bus. Service Bus i subskrypcje tematów zapewniają dodatkową kolejkę podrzędną nazywaną kolejką utraconych wiadomości.
ms.topic: article
ms.date: 04/08/2021
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: cb791982b50d7afff7b74d70adfd285bb5e0a11c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773229"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Omówienie Service Bus utraconych komunikatów

Azure Service Bus i subskrypcje tematu zapewniają dodatkową kolejkę, nazywaną kolejką utraconych komunikatów (DLQ, *dead-letter queue).* Kolejki utraconych wiadomości nie trzeba jawnie tworzyć i nie można jej usunąć ani zarządzać niezależnie od jednostki głównej.

W tym artykule opisano kolejki utraconych komunikatów w Service Bus. Większość dyskusji można zilustrować w przykładzie [Dead-Letter queues (Kolejki utraconych komunikatów)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/DeadletterQueue) w witrynie GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejka utraconych wiadomości

Kolejka utraconych wiadomości ma na celu przechowywania komunikatów, których nie można dostarczyć do żadnego odbiornika ani komunikatów, których nie można przetworzyć. Komunikaty można następnie usunąć z biblioteki DLQ i sprawdzić. Aplikacja może, za pomocą operatora, rozwiązać problemy i ponownie przesłać komunikat, rejestrować fakt wystąpienia błędu i podjąć działania naprawcze. 

Z perspektywy interfejsu API i protokołu dlq jest w większości podobny do każdej innej kolejki, z tą różnicą, że komunikaty mogą być przesyłane tylko za pośrednictwem operacji utraconych jednostek nadrzędnych. Ponadto nie zaobserwowano czasu życia i nie można utracić komunikatu z biblioteki DLQ. Kolejka utraconych wiadomości w pełni obsługuje dostarczanie z blokadą wglądu i operacje transakcyjne.

Nie ma automatycznego czyszczenia biblioteki DLQ. Komunikaty pozostają w dlq do momentu jawnego pobrania ich z biblioteki DLQ i ukończenia utraconych komunikatów.


## <a name="dlq-message-count"></a>Liczba komunikatów DLQ
Nie można uzyskać liczby komunikatów w kolejce utraconych wiadomości na poziomie tematu. Wynika to z tego, że komunikaty nie są wyświetlane na poziomie tematu, Service Bus zgłasza błąd wewnętrzny. Zamiast tego, gdy nadawca wysyła komunikat do tematu, komunikat jest przesyłany dalej do subskrypcji tematu w ciągu milisekund i w związku z tym nie znajduje się już na poziomie tematu. W związku z tym komunikaty są wyświetlane w często zadawanych pytaniach skojarzonych z subskrypcją tematu. W poniższym przykładzie **Service Bus Explorer,** że obecnie w dlq dla subskrypcji "test1" istnieją 62 komunikaty. 

![Liczba komunikatów DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Liczbę komunikatów DLQ można również uzyskać za pomocą polecenia interfejsu wiersza polecenia platformy Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) . 

## <a name="moving-messages-to-the-dlq"></a>Przenoszenie komunikatów do biblioteki DLQ
Istnieje kilka działań w Service Bus, które powodują wypychanie komunikatów do biblioteki DLQ z poziomu samego aparatu obsługi komunikatów. Aplikacja może również jawnie przenosić komunikaty do biblioteki DLQ. Następujące dwie właściwości (przyczyna utraconych wiadomości i opis utraconych wiadomości) są dodawane do komunikatów utraconych. Aplikacje mogą definiować własne kody dla właściwości przyczyny utraconych informacji, ale system ustawia następujące wartości.

| Przyczyna utraconych danych | Opis błędu utraconych komunikatów |
| --- | --- |
|HeaderSizeExceeded |Przekroczono limit przydziału rozmiaru dla tego strumienia. |
|TTLExpiredException |Komunikat wygasł i został uznany za utracony. Aby uzyskać [szczegółowe informacje, zobacz sekcję](#time-to-live) Czas do transmisji na żywo. |
|Identyfikator sesji ma wartość null. |Jednostka z obsługą sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
|MaxTransferHopCountExceeded | Maksymalna liczba dozwolonych przeskoków podczas przekazywania między kolejkami. Wartość jest ustawiona na 4. |
| MaxDeliveryCountExceededExceptionMessage | Nie można zużyć komunikatu po maksymalnej próbie dostarczenia. Aby uzyskać [szczegółowe informacje, zobacz sekcję Maksymalna liczba](#maximum-delivery-count) dostaw. |

## <a name="maximum-delivery-count"></a>Maksymalna liczba dostaw
Istnieje limit liczby prób dostarczenia komunikatów dla Service Bus i subskrypcji. Wartość domyślna to 10. Za każdym razem, gdy komunikat został dostarczony z blokadą podglądu, ale został jawnie porzucony lub blokada wygasła, liczba dostaw dla komunikatu jest zwiększana. Gdy liczba dostaw przekroczy limit, komunikat zostanie przeniesiony do dlq. Przyczyna utraconych komunikatów w DLQ jest ustawiona na: MaxDeliveryCountExceeded. Tego zachowania nie można wyłączyć, ale maksymalną liczbę dostaw można ustawić na dużą liczbę.

## <a name="time-to-live"></a>Czas wygaśnięcia
Po włączeniu utraconych komunikatów w kolejkach lub subskrypcjach wszystkie wygasające komunikaty są przenoszone do biblioteki DLQ. Kod przyczyny utraconych liter jest ustawiony na: TTLExpiredException.

Wygasłe komunikaty są przeczyszczane i przenoszone do biblioteki DLQ tylko wtedy, gdy istnieje co najmniej jeden aktywny odbiornik ściągający z głównej kolejki lub subskrypcji. Odroczone komunikaty również nie zostaną przeczyszone i przeniesione do kolejki utraconych wiadomości po ich wygaśnięciu. To zachowanie jest celowe.

## <a name="errors-while-processing-subscription-rules"></a>Błędy podczas przetwarzania reguł subskrypcji
Jeśli włączysz utracony komunikat o wyjątkach oceny filtru, wszystkie błędy występujące podczas wykonywania reguły filtru SQL subskrypcji są przechwytywane w bazie danych DLQ wraz z komunikatem o błędzie. Nie używaj tej opcji w środowisku produkcyjnym, w którym nie wszystkie typy komunikatów mają subskrybentów.

## <a name="application-level-dead-lettering"></a>Utraconych na poziomie aplikacji
Oprócz funkcji utraconych komunikatów dostarczanych przez system aplikacje mogą używać biblioteki DLQ do jawnego odrzucania nieakceptowalnych komunikatów. Mogą one zawierać komunikaty, których nie można prawidłowo przetworzyć ze względu na problem systemowy, komunikaty, które zawierają źle sformułowane ładunki, lub komunikaty, które nie mogą zostać uwierzytelnione, gdy jest używany schemat zabezpieczeń na poziomie komunikatów.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Utraconych wiadomości w scenariuszach ForwardTo lub SendVia
Komunikaty będą wysyłane do kolejki utraconych wiadomości transferu w następujących warunkach:

- Komunikat przechodzi przez więcej niż cztery kolejki lub tematy, które są [połączone łańcuchem](service-bus-auto-forwarding.md).
- Kolejka lub temat docelowy jest wyłączona lub usunięta.
- Kolejka lub temat docelowy przekracza maksymalny rozmiar jednostki.

## <a name="path-to-the-dead-letter-queue"></a>Ścieżka do kolejki utraconych wiadomości
Dostęp do kolejki utraconych wiadomości można uzyskać przy użyciu następującej składni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Service Bus kolejkach, zobacz następujące artykuły:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Porównanie kolejek i kolejek Service Bus Azure](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
