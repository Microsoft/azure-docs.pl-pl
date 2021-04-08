---
title: Service Bus kolejek utraconych wiadomości | Microsoft Docs
description: Opisuje kolejki utraconych wiadomości w Azure Service Bus. Kolejki Service Bus i subskrypcje tematów zapewniają pomocniczą podkolejkę o nazwie Kolejka utraconych wiadomości.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630102"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Przegląd Service Busych kolejek utraconych

Kolejki Azure Service Bus i subskrypcje tematów zapewniają pomocniczą podkolejkę o nazwie *Kolejka utraconych wiadomości* (DLQ). Kolejka utraconych wiadomości nie musi być jawnie utworzona i nie można jej usunąć ani inaczej zarządzać niezależnie od jednostki głównej.

W tym artykule opisano kolejki utraconych wiadomości w Service Bus. Większość dyskusji przedstawiono w [przykładowej kolejce utraconych wiadomości](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) w witrynie GitHub.
 
## <a name="the-dead-letter-queue"></a>Kolejka utraconych wiadomości

Kolejka utraconych wiadomości polega na zapełnieniu komunikatów, których nie można dostarczyć do żadnego odbiorcy ani komunikatów, których nie można przetworzyć. Następnie można usunąć wiadomości z DLQ i sprawdzić. Aplikacja może, z pomocą operatora, poprawić problemy i ponownie przesłać komunikat, należy zarejestrować fakt, że wystąpił błąd i podjąć działania naprawcze. 

Z punktu widzenia interfejsu API i protokołu DLQ jest głównie podobne do każdej innej kolejki, z tą różnicą, że komunikaty mogą być przesyłane tylko za pośrednictwem operacji utraconej jednostki nadrzędnej. Ponadto nie zaobserwowano czasu wygaśnięcia i nie można martwić się o wiadomość z DLQ. Kolejka utraconych wiadomości w pełni obsługuje dostarczanie z blokadą i operacje transakcyjne.

Nie istnieje automatyczne czyszczenie DLQ. Komunikaty pozostają w DLQ, dopóki nie zostaną jawnie pobrane z DLQ i wywołają [kompletne ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) wiadomości utraconych.

## <a name="dlq-message-count"></a>Liczba komunikatów DLQ
Nie jest możliwe uzyskanie liczby komunikatów w kolejce utraconych danych na poziomie tematu. Wynika to z faktu, że komunikaty nie znajdują się na poziomie tematu, chyba że Service Bus zgłasza błąd wewnętrzny. Zamiast tego, gdy nadawca wysyła komunikat do tematu, komunikat jest przekazywany do subskrypcji tematu w milisekundach, co oznacza, że nie znajduje się już na poziomie tematu. W związku z tym można zobaczyć komunikaty w DLQ skojarzone z subskrypcją dla tematu. W poniższym przykładzie **eksplorator Service Bus** pokazuje, że w DLQ dla subskrypcji "test1" znajdują się obecnie 62 komunikaty. 

![Liczba komunikatów DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Liczbę komunikatów DLQ można także uzyskać za pomocą polecenia interfejsu CLI platformy Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show) . 

## <a name="moving-messages-to-the-dlq"></a>Przeniesienie komunikatów do DLQ

Istnieje kilka działań w Service Bus, które powodują wypychanie komunikatów do DLQ z poziomu samego aparatu obsługi komunikatów. Aplikacja może również jawnie przenosić komunikaty do DLQ. 

Gdy wiadomość zostanie przeniesiona przez brokera, do wiadomości są dodawane dwie właściwości, ponieważ Broker wywoła swoją wewnętrzną wersję metody [utraconych](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) wiadomości: `DeadLetterReason` i `DeadLetterErrorDescription` .

Aplikacje mogą definiować własne kody dla `DeadLetterReason` właściwości, ale system ustawia następujące wartości.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |Przekroczono limit przydziału rozmiaru dla tego strumienia. |
|TTLExpiredException |Komunikat wygasł i został uznany za utracony. Aby uzyskać szczegółowe informacje, zobacz sekcję [przekroczenie TimeToLive](#exceeding-timetolive) . |
|Identyfikator sesji ma wartość null. |Jednostka z obsługą sesji nie pozwala na komunikat, którego identyfikator sesji ma wartość null. |
|MaxTransferHopCountExceeded | Maksymalna liczba dozwolonych przeskoków podczas przekazywania między kolejkami. Wartość jest równa 4. |
| MaxDeliveryCountExceededExceptionMessage | Nie można użyć komunikatu po osiągnięciu maksymalnej liczby prób dostarczenia. Aby uzyskać szczegółowe informacje, zobacz sekcję [przekroczenie MaxDeliveryCount](#exceeding-maxdeliverycount) . |

## <a name="exceeding-maxdeliverycount"></a>Przekraczanie MaxDeliveryCount

Każda kolejka i subskrypcje mają odpowiednio Właściwość [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) ; wartość domyślna to 10. Za każdym razem, gdy komunikat zostanie dostarczony w ramach blokady ([ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), ale został jawnie porzucony lub blokada wygasła, komunikat [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) jest zwiększany. Gdy [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) przekracza [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), komunikat jest przenoszony do DLQ, określając `MaxDeliveryCountExceeded` kod przyczyny.

Nie można wyłączyć tego zachowania, ale można ustawić [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) na dużą liczbę.

## <a name="exceeding-timetolive"></a>Przekraczanie TimeToLive

Gdy właściwość [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) ma **wartość true** (wartością domyślną jest **false**), wszystkie komunikaty wygasające są przenoszone do DLQ, określając  `TTLExpiredException` kod przyczyny.

Wygasłe komunikaty są przeczyszczane i przenoszone do DLQ, gdy istnieje co najmniej jeden aktywny odbiornik ściągający z kolejki lub subskrypcji głównej, a [odroczone komunikaty](./message-deferral.md) również nie zostaną usunięte i przeniesione do kolejki utraconych wiadomości po ich wygaśnięciu. Te zachowania są zaprojektowane.

## <a name="errors-while-processing-subscription-rules"></a>Błędy podczas przetwarzania reguł subskrypcji

Gdy właściwość [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) jest włączona dla subskrypcji, wszelkie błędy występujące podczas wykonywania reguły filtru SQL subskrypcji są przechwytywane w DLQ wraz z komunikatem o błędzie. Nie należy używać tej opcji w środowisku produkcyjnym, w którym nie wszystkie typy komunikatów mają Subskrybenci.

## <a name="application-level-dead-lettering"></a>Utraconych wiadomości na poziomie aplikacji

Oprócz funkcji utraconych wiadomości dostarczonych przez system aplikacje mogą jawnie odrzucać nieakceptowane wiadomości za pomocą DLQ. Mogą one zawierać komunikaty, których nie można prawidłowo przetworzyć z powodu wszelkiego rodzaju problemów z systemem, komunikatów, które przechowują źle sformułowane ładunki, lub komunikatów, które nie są uwierzytelniane, gdy jest używany jakiś schemat zabezpieczeń na poziomie wiadomości.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Utracono martwe scenariusze w scenariuszach Prześlij dalej lub Wyślij za pomocą

Komunikaty będą wysyłane do kolejki przesyłania utraconych wiadomości w następujących warunkach:

- Komunikat przechodzi przez więcej niż cztery kolejki lub tematy, które są [powiązane ze sobą](service-bus-auto-forwarding.md).
- Kolejka docelowa lub temat jest wyłączony lub usunięty.
- Docelowa Kolejka lub temat przekracza maksymalny rozmiar jednostki.

Aby pobrać te wiadomości utraconych, można utworzyć odbiornik przy użyciu metody narzędzia [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) .

## <a name="example"></a>Przykład

Poniższy fragment kodu tworzy odbiorcę wiadomości. W pętli odbioru dla kolejki głównej kod pobiera komunikat z poleceniem [Receive (TimeSpan. zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), które umożliwia brokerowi natychmiastowe zwrócenie wszelkich komunikatów, które są łatwo dostępne lub do zwrócenia bez żadnego wyniku. Jeśli kod otrzymuje komunikat, natychmiast porzuca go, co zwiększa wartość  `DeliveryCount` . Gdy system przenosi komunikat do DLQ, kolejka główna jest pusta, a pętla zostanie zakończona, ponieważ [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) zwraca **wartość null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Ścieżka do kolejki utraconych wiadomości
Możesz uzyskać dostęp do kolejki utraconych wiadomości za pomocą następującej składni:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Jeśli używasz zestawu SDK platformy .NET, możesz uzyskać ścieżkę do kolejki utraconych wiadomości za pomocą metody SubscriptionClient. FormatDeadLetterPath (). Ta metoda przyjmuje nazwę tematu/nazwę subskrypcji i sufiksy z **/$DeadLetterQueue**.


## <a name="next-steps"></a>Następne kroki

Więcej informacji o kolejkach Service Bus można znaleźć w następujących artykułach:

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Porównanie kolejek platformy Azure i kolejek Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

