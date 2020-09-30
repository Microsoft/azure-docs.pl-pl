---
title: Azure Service Bus — wygaśnięcie komunikatu
description: W tym artykule wyjaśniono, jak wygasa i czas na żywo komunikatów Azure Service Bus. Po upływie tego terminu wiadomość nie zostanie już dostarczona.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 47f8bdb4440adfeb5197f90cdad5358a442ce6a7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569912"
---
# <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Ładunek w wiadomości lub polecenie lub zapytanie wysyłane przez komunikat do odbiorcy jest prawie zawsze uzależnione od formy wygaśnięcia na poziomie aplikacji. Po upływie tego terminu zawartość nie jest już dostarczana lub żądana operacja nie jest już wykonywana.

W przypadku środowisk deweloperskich i testowych, w których kolejki i tematy są często używane w kontekście częściowych przebiegów aplikacji lub części aplikacji, pożądane jest również, aby przełączenie komunikatów testowych zostało wykonane automatycznie, aby następny przebieg testu mógł rozpocząć czyszczenie.

Wygaśnięcie poszczególnych komunikatów może być kontrolowane przez ustawienie właściwości System [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , która określa względny czas trwania. Wygaśnięcie jest bezwzględnym chwilą, gdy wiadomość zostanie przejdzie do kolejki w jednostce. W tym czasie Właściwość [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) przyjmuje wartość [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc)  +  [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). Ustawienie czasu wygaśnięcia (TTL) dla komunikatu obsługiwanego przez brokera nie jest wymuszane w przypadku, gdy żaden klient aktywnie nasłuchuje.

Po **ExpiresAtUtc** natychmiast wiadomości stają się nieodpowiednie do pobrania. Wygaśnięcie nie ma wpływu na komunikaty, które są aktualnie zablokowane na potrzeby dostarczania; te komunikaty są nadal obsługiwane normalnie. Jeśli blokada wygaśnie lub komunikat zostanie porzucony, wygaśnięcie będzie obowiązywać natychmiast.

Gdy wiadomość jest zablokowana, aplikacja może być w posiadaniu wiadomości, która wygasła. Bez względu na to, czy aplikacja jest gotowa do przetworzenia, czy też decyduje o odrzuceniu komunikatu do realizatora.

## <a name="entity-level-expiration"></a>Wygaśnięcie na poziomie jednostki

Wszystkie komunikaty wysyłane do kolejki lub tematu podlegają domyślnym wygaśnięciu ustawionym na poziomie jednostki z właściwością [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) , które można również ustawić w portalu podczas tworzenia i korygowania później. Domyślne wygaśnięcie jest używane dla wszystkich komunikatów wysyłanych do jednostki, gdzie [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nie jest jawnie ustawiona. Domyślne wygaśnięcie również działa jako pułap dla wartości **TimeToLive** . Komunikaty, które mają dłuższy czas wygaśnięcia **TimeToLive** niż wartość domyślna, są w trybie dyskretnym dostosowywane do wartości **defaultMessageTimeToLive** przed utworzeniem ich w kolejce.

> [!NOTE]
> Domyślna wartość [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) dla komunikatu obsługiwanego przez brokera to [TimeSpan. Max](/dotnet/api/system.timespan.maxvalue) , jeśli nie określono inaczej.
>
> W przypadku jednostek obsługi komunikatów (kolejek i tematów) domyślny czas wygaśnięcia jest określany jako [TimeSpan. Max](/dotnet/api/system.timespan.maxvalue) dla warstw Service Bus Standard i Premium. W przypadku warstwy **podstawowa** domyślny czas wygaśnięcia (również maksymalny) to **14 dni**.

Wygasłe komunikaty można opcjonalnie przenieść do [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md) , ustawiając właściwość [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) lub zaznaczając odpowiednie pole w portalu. Jeśli opcja jest wyłączona, wygasłe komunikaty są usuwane. Wygasłe komunikaty przeniesione do kolejki utraconych wiadomości można odróżnić od innych utraconych wiadomości, oceniając Właściwość [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) , która jest przechowywana przez brokera w sekcji właściwości użytkownika; w tym przypadku wartość jest [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) .

W powyższym przypadku, gdy komunikat jest chroniony przed wygaśnięciem, a w obszarze Zablokuj i jeśli flaga jest ustawiona w jednostce, komunikat jest przenoszony do kolejki utraconych wiadomości, ponieważ blokada została porzucona lub wygaśnie. Nie jest on jednak przenoszony w przypadku pomyślnego rozliczenia komunikatu, a następnie zakłada, że aplikacja została pomyślnie obsłużona, a nie przez wartość nominalną ważności.

Kombinacja [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) i automatycznych (i transakcyjnych) utraconych wiadomości w dniu wygaśnięcia jest cennym narzędziem służącym do ustanowienia pewności, czy zadanie przekazane do procedury obsługi lub grupy programów obsługi w ramach ostatecznego terminu jest pobierane do przetwarzania po osiągnięciu terminu ostatecznego.

Rozważmy na przykład witrynę sieci Web, która wymaga niezawodnego wykonywania zadań w zasobie z ograniczeniami skalowalnymi, a czasami powoduje, że ruch jest osiągany w zależności od dostępności tego zaplecza. W regularnych przypadkach program obsługi po stronie serwera dla przesłanych danych użytkownika wypycha informacje do kolejki, a następnie otrzymuje odpowiedź potwierdzającą pomyślne obsłudze transakcji w kolejce odpowiedzi. W przypadku przepełnienia ruchu, gdy program obsługi zaplecza nie może przetworzyć swoich elementów zaległości w czasie, wygasłe zadania są zwracane w kolejce utraconych wiadomości. Użytkownik interaktywny może zostać powiadomiony o tym, że żądana operacja potrwa nieco dłużej niż zwykle, a żądanie może zostać umieszczone w innej kolejce dla ścieżki przetwarzania, w której wyniki przetwarzania ostatecznego są wysyłane do użytkownika za pośrednictwem poczty e-mail. 


## <a name="temporary-entities"></a>Jednostki tymczasowe

Service Bus kolejkami, tematami i subskrypcjami można utworzyć jako jednostki tymczasowe, które są automatycznie usuwane, gdy nie były używane przez określony czas.
 
Automatyczne czyszczenie jest przydatne w scenariuszach deweloperskich i testowych, w których jednostki są tworzone dynamicznie i nie są czyszczone po ich użyciu z powodu przerwy w działaniu testu lub debugowania. Jest on również przydatny, gdy aplikacja tworzy jednostki dynamiczne, takie jak kolejka odpowiedzi, do odbierania odpowiedzi z powrotem do procesu serwera sieci Web lub do innego stosunkowo krótkotrwałego obiektu, w którym trudno wyczyścić te jednostki, gdy wystąpienie obiektu zniknie.

Funkcja jest włączona przy użyciu właściwości [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) . Ta właściwość jest ustawiona na czas trwania, przez który jednostka musi być bezczynna (nieużywana), zanim zostanie automatycznie usunięta. Minimalna wartość tej właściwości to 5.
 
Właściwość **autoDeleteOnIdle** musi być ustawiona za pośrednictwem operacji Azure Resource Manager lub za pośrednictwem interfejsów API .NET Framework Client [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) . Nie można go ustawić w portalu.

## <a name="idleness"></a>Bezczynność

Poniżej przedstawiono informacje, które są uważane za bezczynne jednostki (kolejki, tematy i subskrypcje):

- Kolejki
    - Brak wysłanych  
    - Nie odebrano  
    - Brak aktualizacji kolejki  
    - Brak zaplanowanych komunikatów  
    - Brak przeglądania/wglądu 
- Tematy  
    - Brak wysłanych  
    - Brak aktualizacji tematu  
    - Brak zaplanowanych komunikatów 
- Subskrypcje
    - Nie odebrano  
    - Brak aktualizacji subskrypcji  
    - Nie dodano żadnych nowych reguł do subskrypcji  
    - Brak przeglądania/wglądu  
 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)