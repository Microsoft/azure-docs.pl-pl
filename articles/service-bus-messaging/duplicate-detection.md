---
title: Azure Service Bus wykrywania duplikatów komunikatów | Microsoft Docs
description: W tym artykule wyjaśniono, jak można wykrywać duplikaty w komunikatach Azure Service Bus. Zduplikowany komunikat można zignorować i usunąć.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dbca1b4b4f894d35835e7d37e0b4e742a2d3b917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083892"
---
# <a name="duplicate-detection"></a>Wykrywanie duplikatów

Jeśli aplikacja zakończy się niepowodzeniem z powodu błędu krytycznego natychmiast po wysłaniu komunikatu, a ponownie uruchomione wystąpienie aplikacji uważa, że wcześniejsze dostarczanie komunikatów nie zostało wykonane, kolejne wysłanie powoduje, że ten sam komunikat pojawi się w systemie dwukrotnie.

Istnieje również możliwość, że wystąpił błąd na poziomie klienta lub sieci, a w przypadku wysłania wiadomości do kolejki, a potwierdzenie nie zostało pomyślnie zwrócone do klienta. W tym scenariuszu klient ma wątpliwości dotyczące wyniku operacji wysyłania.

Wykrywanie duplikatów odbierze wątpliwości z tych sytuacji przez umożliwienie nadawcy ponownego wysłania tego samego komunikatu, a kolejka lub temat odrzuca wszystkie zduplikowane kopie.

Włączenie wykrywania duplikatów pomaga śledzić kontrolowane przez aplikację *MessageID* wszystkich komunikatów wysyłanych do kolejki lub tematu w określonym przedziale czasu. Jeśli nowa wiadomość jest wysyłana z identyfikatorem *MessageID* zarejestrowanym w przedziale czasu, komunikat jest raportowany jako zaakceptowany (operacja wysyłania powiedzie się), ale nowo wysłana wiadomość zostanie natychmiast zignorowana i porzucona. Nie *są brane* pod uwagę żadne inne części komunikatu niż komunikat.

Kontrola aplikacji w identyfikatorze jest istotna, ponieważ tylko umożliwia aplikacji powiązanie wartości *MessageID* z kontekstem procesu biznesowego, z którego może być przewidywane odbudowanie w przypadku wystąpienia błędu.

W przypadku procesu biznesowego, w którym wiele komunikatów jest wysyłanych w trakcie obsługi pewnego kontekstu aplikacji, identyfikator *MessageID* może być złożonym z identyfikatora kontekstu na poziomie aplikacji, takiego jak numer zamówienia zakupu, a temat wiadomości, na przykład **12345.2017/płatność**.

Wartość *MessageID* może zawsze być identyfikatorem GUID, ale zakotwiczenie identyfikatora do procesu biznesowego daje przewidywalne powtarzalność, która jest odpowiednia do efektywnego wykorzystania funkcji wykrywania duplikatów.

> [!NOTE]
> Jeśli wykrywanie duplikatów jest włączone, a identyfikator sesji lub klucz partycji nie są ustawione, identyfikator wiadomości jest używany jako klucz partycji. Jeśli identyfikator wiadomości nie jest również ustawiony, biblioteki .NET i AMQP automatycznie generują Identyfikator komunikatu dla wiadomości. Aby uzyskać więcej informacji, zobacz [Korzystanie z kluczy partycji](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Włącz wykrywanie duplikatów

W portalu funkcja jest włączana podczas tworzenia jednostki przy użyciu pola wyboru **Włącz wykrywanie duplikatów** , która jest domyślnie wyłączona. Ustawienie tworzenia nowych tematów jest równoważne.

![Zrzut ekranu przedstawiający okno dialogowe Tworzenie kolejki z wybraną opcją Włącz wykrywanie duplikatów i pokreśloną na czerwono.][1]

> [!IMPORTANT]
> Nie można włączyć/wyłączyć wykrywania duplikatów po utworzeniu kolejki. Można to zrobić tylko w momencie tworzenia kolejki. 

Programowo ustawiasz flagę przy użyciu właściwości [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) w pełnym interfejsie API platformy .NET. W przypadku interfejsu API Azure Resource Manager wartość jest ustawiana za pomocą właściwości [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Wartość domyślna historii czasu wykrywania duplikatów wynosi 30 sekund dla kolejek i tematów z maksymalną wartością siedem dni. To ustawienie można zmienić w oknie właściwości kolejki i tematu w Azure Portal.

![Zrzut ekranu przedstawiający funkcję Service Bus z wyróżnionym ustawieniem właściwości i opcją duplikowanie historii wykrywania zakreśloną na czerwono.][2]

Programowo można skonfigurować rozmiar okna wykrywania duplikatów, podczas którego identyfikatory komunikatów są zachowywane, przy użyciu właściwości [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) z pełnym interfejsem API .NET Framework. W przypadku interfejsu API Azure Resource Manager wartość jest ustawiana za pomocą właściwości [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Włączenie wykrywania duplikatów i rozmiaru okna bezpośrednio wpływa na przepływność kolejki (i tematu), ponieważ wszystkie zarejestrowane identyfikatory komunikatów muszą być dopasowane do nowo przesłanego identyfikatora wiadomości.

Utrzymywanie małego okna oznacza, że należy zachować i dopasować mniej identyfikatorów komunikatów, a przepustowość jest mniejsza. W przypadku jednostek o wysokiej przepływności, które wymagają wykrywania duplikatów, należy pozostawić to okno jako małe, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

W scenariuszach, w których kod klienta nie może ponownie przesłać komunikatu o tym samym *atrybucie MessageID* co poprzednio, ważne jest, aby zaprojektować komunikaty, które można bezpiecznie ponownie przetworzyć. Ten [wpis w blogu dotyczący usługi idempotentność](https://particular.net/blog/what-does-idempotent-mean) opisuje różne techniki, w których należy to zrobić.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
