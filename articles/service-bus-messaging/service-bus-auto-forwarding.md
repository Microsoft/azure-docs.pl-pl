---
title: Autoprzekazywanie Azure Service Bus jednostek komunikatów
description: W tym artykule opisano sposób łańcucha kolejki Azure Service Bus lub subskrypcji do innej kolejki lub tematu.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678992"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Łączenie Service Bus jednostek z funkcją autoprzesyłania dalej

Funkcja *autoprzesyłania dalej* Service Bus umożliwia łańcuchowanie kolejki lub subskrypcji do innej kolejki lub tematu, który jest częścią tej samej przestrzeni nazw. Gdy automatyczne przesyłanie dalej jest włączone, Service Bus automatycznie usuwa komunikaty umieszczone w pierwszej kolejce lub subskrypcji (źródłowej) i umieszcza je w drugiej kolejce lub temacie (miejsce docelowe). Nadal możliwe jest bezpośrednie wysyłanie komunikatów do jednostki docelowej.

> [!NOTE]
> Warstwa Podstawowa Service Bus nie obsługuje funkcji autoprzesyłania dalej. Warstwy Standardowa i Premium obsługują tę funkcję. Aby zapoznać się z różnicami między tymi warstwami, zobacz [Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-autoforwarding"></a>Używanie autoprzesyłania dalej

Aby włączyć funkcję autoprzesyłania dalej, można ustawić właściwości [QueueDescription. Prześlij dalej][QueueDescription.ForwardTo] lub [SubscriptionDescription. Prześlij dalej][SubscriptionDescription.ForwardTo] dla obiektów [QueueDescription][QueueDescription] lub [SubscriptionDescription][SubscriptionDescription] dla źródła, jak w poniższym przykładzie:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Jednostka docelowa musi istnieć w momencie utworzenia jednostki źródłowej. Jeśli jednostka docelowa nie istnieje, Service Bus zwraca wyjątek, gdy zostanie wyświetlony monit o utworzenie jednostki źródłowej.

Do skalowania poszczególnych tematów można użyć funkcji automatycznego przesyłania dalej. Service Bus ogranicza [liczbę subskrypcji dla danego tematu](service-bus-quotas.md) do 2 000. Dodatkowe subskrypcje można obsłużyć, tworząc tematy drugiego poziomu. Nawet jeśli nie powiążesz się z ograniczeniem Service Bus liczby subskrypcji, dodanie drugiego poziomu tematów może poprawić ogólną przepływność tematu.

![Diagram scenariusza autoprzesyłania dalej przedstawiający komunikat przetworzony w temacie Orders, który może odgałęziać się do któregokolwiek z trzech tematów zamówień drugiego poziomu.][0]

Można również użyć autoprzesyłania dalej do rozdzielania nadawców komunikatów od odbiorników. Rozważmy na przykład system ERP obejmujący trzy moduły: przetwarzanie zamówień, zarządzanie zapasami i zarządzanie relacjami z klientami. Każdy z tych modułów generuje komunikaty, które znajdują się w kolejce do odpowiedniego tematu. Alicja i Robert to przedstawiciele handlowi zainteresowani wszystkimi wiadomościami odnoszącymi się do klientów. Aby otrzymywać te komunikaty, Alicja i Robert tworzą kolejkę osobistą i subskrypcję w każdym z tematów ERP, które automatycznie przekazują wszystkie komunikaty do ich kolejki.

![Diagram scenariusza autoprzesyłania dalej przedstawiający trzy moduły przetwarzania wysyłające komunikaty przez trzy odpowiednie tematy do dwóch oddzielnych kolejek.][1]

Jeśli Alicja przechodzi do urlopu, jego kolejki osobistej, a nie tematu ERP, wypełnia. W tym scenariuszu, ponieważ przedstawiciel handlowy nie otrzymał żadnych komunikatów, żaden z tych tematów nie osiągnął limitu przydziału.

> [!NOTE]
> W przypadku automatycznego przesyłania dalej wartość ustawienia AutoDeleteOnIdle na serwerze **źródłowym i docelowym** jest automatycznie ustawiana na wartość maksymalną wartości typu danych.
> 
>   - Po stronie źródłowej, autoprzesyłanie dalej działa jako operacja odbierania. W związku z tym źródło, które ma konfigurację autoprzekazywania, nigdy nie jest naprawdę "bezczynne".
>   - Na stronie docelowej jest to konieczne, aby upewnić się, że jest zawsze miejscem docelowym do przesyłania dalej wiadomości.

## <a name="autoforwarding-considerations"></a>Zagadnienia dotyczące autoprzesyłania dalej

Jeśli jednostka docelowa sumuje zbyt wiele komunikatów i przekracza limit przydziału lub jednostka docelowa jest wyłączona, jednostka źródłowa dodaje komunikaty do [kolejki utraconych](service-bus-dead-letter-queues.md) wiadomości do momentu, w którym miejsce docelowe (lub jednostka jest ponownie włączona). Te komunikaty są nadal aktywne w kolejce utraconych wiadomości, więc musisz jawnie je odbierać i przetwarzać z kolejki utraconych wiadomości.

Podczas łączenia poszczególnych tematów w celu uzyskania złożonego tematu z wieloma subskrypcjami zaleca się posiadanie umiarkowanej liczby subskrypcji w temacie pierwszego poziomu i wielu subskrypcji tematów drugiego poziomu. Na przykład pierwszy temat z 20 subskrypcjami, każdy z nich jest powiązany z artykułem drugiego poziomu z subskrypcjami 200, umożliwia wyższą przepływność niż temat pierwszego poziomu z subskrypcjami 200, z których każda jest poddana połączeniu do drugiego tematu zawierającego 20 subskrypcji.

Service Bus weksle jednej operacji dla każdego przesyłanego dalej komunikatu. Na przykład w przypadku wysyłania komunikatu do tematu z 20 subskrypcjami każda z nich została skonfigurowana do przesyłania dalej wiadomości do innej kolejki lub tematu, jest rozliczana jako 21 operacji, jeśli wszystkie subskrypcje pierwszego poziomu otrzymają kopię wiadomości.

Aby utworzyć subskrypcję, która jest łańcuchem do innej kolejki lub tematu, twórca subskrypcji musi mieć uprawnienia do **zarządzania** zarówno dla jednostki źródłowej, jak i docelowej. Wysyłanie komunikatów do tematu źródłowego wymaga tylko uprawnień do **wysyłania** w temacie źródłowym.

Nie twórz łańcucha, który przekracza 4 przeskoki. Komunikaty przekraczające 4 przeskoki są utracone.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat autoprzesyłania dalej, zobacz następujące tematy dotyczące odwołań:

* [Prześlij dalej][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Aby dowiedzieć się więcej na temat ulepszeń wydajności Service Bus, zobacz 

* [Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu komunikatów usługi Service Bus](service-bus-performance-improvements.md)
* [Partycjonowane jednostki obsługi komunikatów][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
