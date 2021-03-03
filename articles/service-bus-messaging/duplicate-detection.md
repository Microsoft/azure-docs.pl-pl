---
title: Azure Service Bus wykrywania duplikatów komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak można wykrywać duplikaty w komunikatach Azure Service Bus. Zduplikowany komunikat można zignorować i usunąć.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711929"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja zakończy się niepowodzeniem z powodu błędu krytycznego natychmiast po wysłaniu komunikatu, a ponownie uruchomione wystąpienie aplikacji uważa, że wcześniejsze dostarczanie komunikatów nie zostało wykonane, kolejne wysłanie powoduje, że ten sam komunikat pojawi się w systemie dwukrotnie.

Istnieje również możliwość, że wystąpił błąd na poziomie klienta lub sieci, a w przypadku wysłania wiadomości do kolejki, a potwierdzenie nie zostało pomyślnie zwrócone do klienta. W tym scenariuszu klient ma wątpliwości dotyczące wyniku operacji wysyłania.

Wykrywanie duplikatów odbierze wątpliwości z tych sytuacji przez umożliwienie nadawcy ponownego wysłania tego samego komunikatu, a kolejka lub temat odrzuca wszystkie zduplikowane kopie.

> [!NOTE]
> Warstwa Podstawowa Service Bus nie obsługuje wykrywania duplikatów. Warstwy Standardowa i Premium obsługują wykrywanie duplikatów. Aby zapoznać się z różnicami między tymi warstwami, zobacz [Cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="how-it-works"></a>Jak to działa? 
Włączenie wykrywania duplikatów pomaga śledzić kontrolowane przez aplikację *MessageID* wszystkich komunikatów wysyłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli nowa wiadomość jest wysyłana z identyfikatorem *MessageID* zarejestrowanym w przedziale czasu, komunikat jest raportowany jako zaakceptowany (operacja wysyłania powiedzie się), ale nowo wysłana wiadomość zostanie natychmiast zignorowana i porzucona. Nie *są brane* pod uwagę żadne inne części komunikatu niż komunikat.

Kontrola aplikacji w identyfikatorze jest istotna, ponieważ tylko umożliwia aplikacji powiązanie wartości *MessageID* z kontekstem procesu biznesowego, z którego może być przewidywane odbudowanie w przypadku wystąpienia błędu.

W przypadku procesu biznesowego, w którym wiele komunikatów jest wysyłanych w trakcie obsługi pewnego kontekstu aplikacji, identyfikator *MessageID* może być złożonym z identyfikatora kontekstu na poziomie aplikacji, takiego jak numer zamówienia zakupu, a temat wiadomości, na przykład **12345.2017/płatność**.

Wartość *MessageID* może zawsze być identyfikatorem GUID, ale zakotwiczenie identyfikatora do procesu biznesowego daje przewidywalne powtarzalność, która jest odpowiednia do efektywnego używania funkcji wykrywania duplikatów.

> [!IMPORTANT]
>- Po **włączeniu** **partycjonowania** służy `MessageId+PartitionKey` do określania unikatowości. Gdy sesje są włączone, klucz partycji i identyfikator sesji muszą być takie same. 
>- Gdy **partycjonowanie** jest **wyłączone** (domyślnie), tylko `MessageId` jest używane do ustalania unikatowości.
>- Aby uzyskać informacje na temat SessionId, PartitionKey i MessageId, zobacz [Korzystanie z kluczy partycji](service-bus-partitioning.md#use-of-partition-keys).
>- [Warstwa Premier](service-bus-premium-messaging.md) nie obsługuje partycjonowania, dlatego zalecamy używanie unikatowych identyfikatorów komunikatów w aplikacjach, a nie poleganie na kluczach partycji na potrzeby wykrywania duplikatów. 


## <a name="enable-duplicate-detection"></a>Włącz wykrywanie duplikatów

W portalu funkcja jest włączana podczas tworzenia jednostki przy użyciu pola wyboru **Włącz wykrywanie duplikatów** , która jest domyślnie wyłączona. Ustawienie tworzenia nowych tematów jest równoważne.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie kolejki z wybraną opcją Włącz wykrywanie duplikatów i pokreśloną na czerwono.][1]

> [!IMPORTANT]
> Nie można włączyć/wyłączyć wykrywania duplikatów po utworzeniu kolejki. Można to zrobić tylko w momencie tworzenia kolejki. 

Programowo ustawiasz flagę przy użyciu właściwości [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) w pełnym interfejsie API platformy .NET. W przypadku interfejsu API Azure Resource Manager wartość jest ustawiana za pomocą właściwości [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Ustawienia historia czasu wykrywania duplikatów domyślnie 10 minut dla kolejek i tematów, z minimalną wartością 20 sekund do maksymalnej wartości wynoszącej 7 dni. To ustawienie można zmienić w oknie właściwości kolejki i tematu w Azure Portal.

![Zrzut ekranu przedstawiający funkcję Service Bus z wyróżnionym ustawieniem właściwości i opcją duplikowanie historii wykrywania zakreśloną na czerwono.][2]

Programowo można skonfigurować rozmiar okna wykrywania duplikatów, podczas którego identyfikatory komunikatów są zachowywane, przy użyciu właściwości [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) z pełnym interfejsem API .NET Framework. W przypadku interfejsu API Azure Resource Manager wartość jest ustawiana za pomocą właściwości [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływa na przepływność kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory komunikatów muszą być dopasowane do nowo przesłanego identyfikatora wiadomości.

Utrzymywanie małego okna oznacza, że należy zachować i dopasować mniej identyfikatorów komunikatów, a przepustowość jest mniejsza. W przypadku jednostek o wysokiej przepływności, które wymagają wykrywania duplikatów, należy pozostawić to okno jako małe, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

W scenariuszach, w których kod klienta nie może ponownie przesłać komunikatu o tym samym *atrybucie MessageID* co poprzednio, ważne jest, aby zaprojektować komunikaty, które mogą zostać bezpiecznie przetworzone ponownie. Ten [wpis w blogu dotyczący usługi idempotentność](https://particular.net/blog/what-does-idempotent-mean) opisuje różne techniki, w których należy to zrobić.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
