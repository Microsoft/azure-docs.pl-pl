---
title: Metryki obsługiwane przez Azure Event Grid
description: Ten artykuł zawiera Azure Monitor metryki obsługiwane przez usługę Azure Event Grid.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 06c43b1990efc977cae33ced3f66f02e2de0b9c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225176"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metryki obsługiwane przez Azure Event Grid
Ten artykuł zawiera listę metryk Event Grid, które są podzielone według przestrzeni nazw. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Temat, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|Temat, Błądtype, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Brak wymiarów|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Brak wymiarów|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Brak wymiarów|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem: [dzienniki diagnostyczne](diagnostic-logs.md)
