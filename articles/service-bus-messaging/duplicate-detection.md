---
title: Azure Service Bus wykrywania zduplikowanych komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak można wykrywać duplikaty w Azure Service Bus wiadomościach. Zduplikowany komunikat można zignorować i porzucić.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751278"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja zakończy się niepowodzeniem z powodu błędu krytycznego natychmiast po wysłaniu komunikatu, a ponownie uruchomione wystąpienie aplikacji błędnie uważa, że wcześniejsze dostarczenie komunikatu nie wystąpiło, kolejne wysłanie powoduje dwukrotne wystąpienie tego samego komunikatu w systemie.

Możliwe jest również, że błąd na poziomie klienta lub sieci wystąpi chwilę wcześniej, a wysłany komunikat zostanie zatwierdzona w kolejce, a potwierdzenie nie zostanie pomyślnie zwrócone do klienta. Ten scenariusz pozostawia klientowi wątpliwości co do wyniku operacji wysyłania.

Wykrywanie duplikatów usuwa te wątpliwości, włączając nadawcę ponownie wysłać ten sam komunikat, a kolejka lub temat odrzuca wszystkie zduplikowane kopie.

> [!NOTE]
> Podstawowa warstwa usługi Service Bus nie obsługuje wykrywania duplikatów. Warstwy Standardowa i Premium obsługują wykrywanie duplikatów. Aby uzyskać informacje o różnicach między tymi warstwami, [zobacz Service Bus cennik.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Jak to działa? 
Włączenie wykrywania duplikatów pomaga śledzić kontrolowany przez aplikację identyfikator *MessageId* wszystkich komunikatów wysyłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli zostanie wysłany nowy komunikat z *messageId,* który został zarejestrowany w oknie czasowym, komunikat zostanie zgłoszony jako zaakceptowany (operacja wysyłania zakończy się pomyślnie), ale nowo wysłany komunikat zostanie natychmiast zignorowany i usunięty. Nie są rozważane żadne inne części komunikatu niż *MessageId.*

Kontrola aplikacji nad identyfikatorem jest niezbędna, ponieważ umożliwia tylko aplikacji powiązycie identyfikatora *MessageId* z kontekstem procesu biznesowego, z którego można go odtworzyć w przewidywalny sposób w przypadku wystąpienia awarii.

W przypadku procesu biznesowego, w którym w trakcie obsługi kontekstu aplikacji jest wysyłanych wiele komunikatów, identyfikator *MessageId* może być złożonym identyfikatorem kontekstu na poziomie aplikacji, takim jak numer zamówienia zakupu i temat komunikatu, na przykład **12345.2017/payment.**

Identyfikator *MessageId* może być zawsze pewnym identyfikatorem GUID, ale zakotwiczenie identyfikatora w procesie biznesowym daje przewidywalną powtarzalność, co jest pożądane do skutecznego korzystania z funkcji wykrywania duplikatów.

> [!IMPORTANT]
>- Gdy **partycjonowanie** jest **włączone,** `MessageId+PartitionKey` funkcja służy do określania unikatowości. Po włączeniu sesji klucz partycji i identyfikator sesji muszą być takie same. 
>- Gdy **partycjonowanie** jest **wyłączone** (ustawienie domyślne), służy tylko `MessageId` do określania unikatowości.
>- Aby uzyskać informacje o sessionid, PartitionKey i MessageId, zobacz [Używanie kluczy partycji](service-bus-partitioning.md#use-of-partition-keys).
>- Warstwa [Premier nie](service-bus-premium-messaging.md) obsługuje partycjonowania, dlatego zalecamy używanie unikatowych identyfikatorów komunikatów w aplikacjach i nie poleganie na kluczach partycji w celu wykrywania duplikatów. 


## <a name="duplicate-detection-window-size"></a>Rozmiar okna wykrywania duplikatów

Oprócz włączenia wykrywania duplikatów można również skonfigurować rozmiar okna czasu historii wykrywania duplikatów, w którym są zachowywane identyfikatory komunikatów.
Ta wartość domyślna to 10 minut dla kolejek i tematów, przy minimalnej wartości od 20 sekund do maksymalnej wartości 7 dni.

Włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływa na przepływność kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory komunikatów muszą być dopasowane do nowo przesłanego identyfikatora komunikatu.

Utrzymywanie małych okien oznacza, że należy zachować i dopasować mniejszą liczbę identyfikatorów komunikatów, a przepływność będzie mieć mniejszy wpływ. W przypadku jednostek o wysokiej przepływności, które wymagają wykrywania duplikatów, okno powinno być jak najmniejsze.

## <a name="next-steps"></a>Następne kroki
Wykrywanie zduplikowanych komunikatów można włączyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia, Resource Manager szablonu, .NET, Java, Python i JavaScript. Aby uzyskać więcej informacji, zobacz [Włączanie wykrywania zduplikowanych komunikatów.](enable-duplicate-detection.md) 

W scenariuszach, w których kod klienta nie może ponownie przesłać komunikatu z tym samym elementem *MessageId* co wcześniej, ważne jest zaprojektowanie komunikatów, które mogą być bezpiecznie ponownie przetworzone. W [tym wpisie w blogu o idempotenności](https://particular.net/blog/what-does-idempotent-mean) opisano różne techniki, które to robią.

Wypróbuj przykłady w wybranego języku, aby poznać Azure Service Bus funkcji. 

- [Azure Service Bus biblioteki klienta dla języka Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus biblioteki klienta dla języka JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus biblioteki klienta dla języka TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Przykłady dla platformy .NET Azure.Messaging.ServiceBus](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Poniżej znajdują się przykłady dla starszych bibliotek klienckich .NET i Java:
- [Przykłady microsoft.Azure.ServiceBus dla platformy .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [przykłady azure-servicebus dla języka Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

