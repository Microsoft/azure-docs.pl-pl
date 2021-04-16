---
title: Azure Service Bus wykrywania zduplikowanych komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak można wykrywać duplikaty w Azure Service Bus wiadomościach. Zduplikowany komunikat można zignorować i porzucić.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499482"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja zakończy się niepowodzeniem z powodu błędu krytycznego natychmiast po wysłaniu komunikatu, a ponownie uruchomione wystąpienie aplikacji błędnie uważa, że wcześniejsze dostarczenie komunikatu nie wystąpiło, kolejne wysłanie powoduje dwukrotne wystąpienie tego samego komunikatu w systemie.

Możliwe jest również, że błąd na poziomie klienta lub sieci wystąpi chwilę wcześniej, a wysłany komunikat zostanie zatwierdzona w kolejce, a potwierdzenie nie zostanie pomyślnie zwrócone do klienta. Ten scenariusz pozostawia klientowi wątpliwości co do wyniku operacji wysyłania.

Wykrywanie duplikatów usuwa te wątpliwości, włączając nadawcę ponownie wysłać ten sam komunikat, a kolejka lub temat odrzuca wszystkie zduplikowane kopie.

> [!NOTE]
> Podstawowa warstwa usługi Service Bus nie obsługuje wykrywania duplikatów. Warstwy Standardowa i Premium obsługują wykrywanie duplikatów. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Jak to działa? 
Włączenie wykrywania duplikatów pomaga śledzić kontrolowany przez aplikację identyfikator *MessageId* wszystkich komunikatów wysyłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli zostanie wysłany nowy komunikat z *messageId,* który został zarejestrowany w oknie czasowym, komunikat zostanie zgłoszony jako zaakceptowany (operacja wysyłania zakończy się pomyślnie), ale nowo wysłany komunikat zostanie natychmiast zignorowany i porzucony. Nie są rozważane żadne inne części komunikatu niż *MessageId.*

Kontrola aplikacji nad identyfikatorem jest niezbędna, ponieważ umożliwia tylko aplikacji powiązycie identyfikatora *MessageId* z kontekstem procesu biznesowego, z którego można go odtworzyć w przewidywalny sposób w przypadku wystąpienia awarii.

W przypadku procesu biznesowego, w którym w trakcie obsługi kontekstu aplikacji jest wysyłanych wiele komunikatów, identyfikator *MessageId* może być złożonym identyfikatorem kontekstu na poziomie aplikacji, takim jak numer zamówienia zakupu i temat komunikatu, na przykład **12345.2017/payment.**

Identyfikator *MessageId* zawsze może być pewnym identyfikatorem GUID, ale zakotwiczenie identyfikatora w procesie biznesowym daje przewidywalną powtarzalność, co jest pożądane do skutecznego korzystania z funkcji wykrywania duplikatów.

> [!IMPORTANT]
>- Gdy **partycjonowanie** jest **włączone,** `MessageId+PartitionKey` funkcja służy do określania unikatowości. Po włączeniu sesji klucz partycji i identyfikator sesji muszą być takie same. 
>- Gdy **partycjonowanie** jest **wyłączone** (ustawienie domyślne), służy tylko `MessageId` do określania unikatowości.
>- Aby uzyskać informacje o sessionid, PartitionKey i MessageId, zobacz [Korzystanie z kluczy partycji](service-bus-partitioning.md#use-of-partition-keys).
>- Warstwa [Premier nie](service-bus-premium-messaging.md) obsługuje partycjonowania, dlatego zalecamy używanie unikatowych identyfikatorów komunikatów w aplikacjach i nie poleganie na kluczach partycji w celu wykrywania duplikatów. 


## <a name="enable-duplicate-detection"></a>Włączanie wykrywania duplikatów

Oprócz włączenia wykrywania duplikatów można również skonfigurować rozmiar okna czasu historii wykrywania duplikatów, w którym są zachowywane identyfikatory komunikatów.
Ta wartość domyślna to 10 minut dla kolejek i tematów, przy minimalnej wartości od 20 sekund do maksymalnej wartości 7 dni.

Włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływa na przepływność kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory komunikatów muszą być dopasowane do nowo przesłanego identyfikatora komunikatu.

Utrzymywanie małego okna oznacza, że należy zachować i dopasować mniejszą liczbę identyfikatorów komunikatów, a przepływność będzie mieć mniejszy wpływ. W przypadku jednostek o wysokiej przepływności, które wymagają wykrywania duplikatów, okno powinno być jak najmniejsze.

### <a name="using-the-portal"></a>Korzystanie z portalu

W portalu funkcja wykrywania duplikatów jest włączona  podczas tworzenia jednostki z polem wyboru Włącz wykrywanie duplikatów, które jest domyślnie wyłączone. Ustawienie tworzenia nowych tematów jest równoważne.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie kolejki z wybraną opcją Włącz wykrywanie duplikatów i zaznaczoną kolorem czerwonym.][1]

> [!IMPORTANT]
> Po utworzeniu kolejki nie można włączyć/wyłączyć wykrywania duplikatów. Można to zrobić tylko w czasie tworzenia kolejki. 

Okno czasu historii wykrywania duplikatów można zmienić w oknie właściwości kolejki i tematu w Azure Portal.

![Zrzut ekranu przedstawiający Service Bus z wyróżnionem ustawieniem Właściwości i opcją Historia wykrywania duplikatów wyróżnione kolorem czerwonym.][2]

### <a name="using-sdks"></a>Używanie zestawów SDK

Dowolny z naszych zestawów SDK na platformach .NET, Java, JavaScript, Python i Go umożliwia włączenie funkcji wykrywania duplikatów podczas tworzenia kolejek i tematów. Można również zmienić okno czasu historii wykrywania duplikatów.
Właściwości, które należy zaktualizować podczas tworzenia kolejek i tematów w celu osiągnięcia tego celu, to:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Należy pamiętać, że chociaż nazwy właściwości są podane w tym miejscu w języku Pascal, zestawy SDK języków JavaScript i Python będą używać odpowiednio notcji camel casing i casing .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na Service Bus wiadomości, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

W scenariuszach, w których kod klienta nie może ponownie przesłać komunikatu z tym samym elementem *MessageId* co wcześniej, ważne jest zaprojektowanie komunikatów, które mogą być bezpiecznie ponownie przetworzone. W [tym wpisie w blogu o idempotenności](https://particular.net/blog/what-does-idempotent-mean) opisano różne techniki, aby to zrobić.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
