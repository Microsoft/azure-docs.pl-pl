---
title: Omówienie ograniczania Azure Service Bus | Microsoft Docs
description: Omówienie Service Bus ograniczania przepustowości — warstwy Standardowa i Premium.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340946"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Ograniczanie operacji na Azure Service Bus

Natywne rozwiązania w chmurze dają pojęcie nieograniczone zasoby, które można skalować wraz z obciążeniem. Chociaż to pojęcie jest prawdziwe w chmurze niż w systemach lokalnych, nadal istnieją ograniczenia istniejące w chmurze.

Ograniczenia te mogą spowodować ograniczenie żądań aplikacji klienckich w warstwach Standardowa i Premium, zgodnie z opisem w tym artykule. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Ograniczanie wydajności w warstwie Standardowa Azure Service Bus

Warstwa standardowa Azure Service Bus działa jako Konfiguracja wielodostępna z modelem cen z płatność zgodnie z rzeczywistym użyciem. W tym samym klastrze wiele przestrzeni nazw tego samego klastra udostępnia przydzieloną zasoby. Warstwa standardowa jest zalecanym wyborem dla środowisk deweloperskich, testowania i kontroli jakości oraz systemów produkcyjnych o niskiej przepływności.

W przeszłości Azure Service Bus miały ograniczone ograniczenia dotyczące użycia zasobów. Istnieje jednak możliwość udoskonalenia logiki ograniczania przepustowości i zapewnienia przewidywalnego zachowania ograniczania przepustowości dla wszystkich przestrzeni nazw, które współużytkują te zasoby.

Przy próbie zapewnienia właściwego użycia i dystrybucji zasobów we wszystkich Azure Service Bus standardowych przestrzeniach nazw, które używają tych samych zasobów, logika ograniczania przepustowości została zmodyfikowana w celu uzyskania kredytu.

> [!NOTE]
> Należy pamiętać, że ograniczenie ***nie jest nowe*** dla Azure Service Bus lub żadnej usługi natywnej w chmurze.
>
> Ograniczanie przepływności polega na prostu na sposobie, w jaki różne przestrzenie nazw współużytkują zasoby w wielodostępnym środowisku warstwy standardowej i w ten sposób umożliwiają uczciwe użycie przez wszystkie przestrzenie nazw, które współużytkują zasoby.

### <a name="what-is-credit-based-throttling"></a>Co to jest ograniczanie opłaty?

Ograniczanie przepływności ogranicza liczbę operacji, które mogą być wykonywane w danym obszarze nazw w określonym przedziale czasu. 

Poniżej znajduje się przepływ pracy związanej z ograniczaniem na podstawie kredytu — 

  * Na początku każdego okresu udostępniamy określoną liczbę kredytów dla każdej przestrzeni nazw.
  * Wszystkie operacje wykonywane przez nadawcę lub aplikację kliencką odbiorcy będą wliczane do tych kredytów (i odejmowane od dostępnych kredytów).
  * Jeśli kredyty są wyczerpywane, kolejne operacje zostaną ograniczone do momentu rozpoczęcia następnego okresu.
  * Kredyty są uzupełniane na początku następnego okresu.

### <a name="what-are-the-credit-limits"></a>Jakie są limity kredytowe?

Limity kredytu są obecnie ustawione na "1000" kredytów co sekundę (na przestrzeń nazw).

Nie wszystkie operacje są tworzone jako równe. Poniżej przedstawiono koszty kredytowe każdej operacji — 

| Operacja | Koszt kredytu|
|-----------|-----------|
| Operacje na danych (Send, SendAsync, Receive, ReceiveAsync, Peek) |1 kredyt na wiadomość |
| Operacje zarządzania (tworzenie, odczytywanie, aktualizowanie, usuwanie w kolejkach, tematy, subskrypcje, filtry) | 10 kredytów |

> [!NOTE]
> Należy pamiętać, że podczas wysyłania do tematu każdy komunikat jest oceniany względem filtrów przed udostępnieniem ich w ramach subskrypcji.
> Każda Ocena filtru liczy się także względem limitu kredytowego (tj. 1 kredyt na ocenę filtru).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak będzie wiadomo, że ograniczam przepływność?

Gdy żądania aplikacji klienckiej są ograniczane, Poniższa odpowiedź serwera zostanie odebrana przez aplikację i zarejestrowana.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Jak można uniknąć ograniczenia przepustowości?

W przypadku udostępnionych zasobów ważne jest, aby wymusić różnego rodzaju znaczące użycie w różnych przestrzeniach nazw Service Bus, które współużytkują te zasoby. Ograniczanie przepustowości gwarantuje, że żadne skoki w pojedynczym obciążeniu nie spowoduje ograniczenia przepustowości innych obciążeń w ramach tych samych zasobów.

Jak wspomniano w dalszej części artykułu, nie ma ryzyka związanego z ograniczeniami, ponieważ zestawy SDK klienta (i inne oferty usługi Azure PaaS) mają wbudowane zasady ponawiania. Wszystkie żądania z ograniczeniami zostaną ponowione przy użyciu wykładniczej wycofywania i ostatecznie przejdziemy po uzupełnieniu kredytów.

Zrozumienie niektórych aplikacji może być wrażliwe na ograniczenie przepustowości. W takim przypadku zaleca się [migrację bieżącej Service Bus standardowej przestrzeni nazw do warstwy Premium](service-bus-migrate-standard-premium.md). 

W przypadku migracji można przydzielić dedykowane zasoby do przestrzeni nazw Service Bus i odpowiednio skalować zasoby, jeśli wystąpi wzrost obciążenia i zmniejszenie prawdopodobieństwa ograniczenia przepustowości. Ponadto, gdy obciążenie zmniejszy się do normalnych poziomów, można skalować zasoby przydzieloną do przestrzeni nazw.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Ograniczanie przepustowości w Azure Service Bus warstwie Premium

Warstwa [Premium Azure Service Bus](service-bus-premium-messaging.md) alokuje dedykowane zasoby, w odniesieniu do jednostek obsługi komunikatów, do poszczególnych instalacji przestrzeni nazw przez klienta. Te dedykowane zasoby zapewniają przewidywalną przepływność i opóźnienia i są zalecane w przypadku wysokiej przepływności lub wrażliwych systemów jakości produkcyjnych.

Ponadto warstwa Premium umożliwia klientom skalowanie ich przepustowości, gdy są one wzrostem obciążenia.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Jak działa ograniczanie wydajności w Service Bus Premium?

W przypadku wyłącznej alokacji zasobów dla Service Bus Premium ograniczanie przepustowości jest wyłącznie ograniczone przez ograniczenia zasobów przydzielonych do przestrzeni nazw.

Jeśli liczba żądań jest większa niż bieżąca ilość zasobów, które mogą być serwisowane, żądania zostaną ograniczone.

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak będzie wiadomo, że ograniczam przepływność?

Istnieją różne sposoby identyfikowania ograniczania przepustowości w Azure Service Bus Premium — 
  * **Żądania ograniczone** są wyświetlane na [metrykach żądania Azure monitor](service-bus-metrics-azure-monitor.md#request-metrics) , aby określić liczbę żądań, które zostały ograniczone.
  * Wysokie **użycie procesora CPU** wskazuje, że bieżąca alokacja zasobów jest wysoka, a żądania mogą zostać ograniczone, jeśli bieżące obciążenie nie zmniejszy się.
  * Duże **użycie pamięci** wskazuje, że bieżąca alokacja zasobów jest wysoka, a żądania mogą zostać ograniczone, jeśli bieżące obciążenie nie zmniejszy się.

### <a name="how-can-i-avoid-being-throttled"></a>Jak można uniknąć ograniczenia przepustowości?

Ponieważ przestrzeń nazw Premium Service Bus ma już dedykowane zasoby, można zmniejszyć możliwość uzyskiwania ograniczenia przez skalowanie w górę liczby jednostek obsługi komunikatów przydzielonych do przestrzeni nazw w zdarzeniu (lub przewidywaniu) w obciążeniu.

Skalowanie w górę/w dół można osiągnąć przez tworzenie [elementów Runbook](../automation/automation-create-alert-triggered-runbook.md) , które mogą być wyzwalane przez zmiany w powyższych metrykach.

## <a name="faqs"></a>Często zadawane pytania

### <a name="how-does-throttling-affect-my-application"></a>Jak ograniczanie przepływności ma wpływ na moją aplikację?

Gdy żądanie jest ograniczone, oznacza to, że usługa jest zajęta, ponieważ występuje więcej żądań niż zezwala na zasoby. Jeśli ta sama operacja zostanie ponowiona po kilku chwilach, po przejściu usługi przez bieżące obciążenie, żądanie może być honorowane.

Ze względu na to, że ograniczanie jest oczekiwanym zachowaniem usługi natywnej w chmurze, możemy utworzyć logikę ponowień w samym zestawie SDK Azure Service Bus. Domyślnie zostanie ustawiona wartość autoponawianie próby z powrotem w wykładniczy, aby upewnić się, że każde żądanie nie jest ograniczane za każdym razem.

Domyślna logika ponowienia zostanie zastosowana do każdej operacji.

### <a name="does-throttling-result-in-data-loss"></a>Czy ograniczenie powoduje utratę danych?

Azure Service Bus jest zoptymalizowany pod kątem trwałości, firma Microsoft gwarantuje, że wszystkie dane wysyłane do Service Bus są przekazywane do magazynu przed potwierdzeniem sukcesu żądania przez usługę.

Po pomyślnym wykonaniu żądania "ACK" (potwierdzone) przez Service Bus oznacza to, że Service Bus pomyślnie przetworzył żądanie. Jeśli Service Bus zwraca wartość "NACK" (niepowodzenie), oznacza to, że Service Bus nie mógł przetworzyć żądania, a aplikacja kliencka musi ponowić próbę żądania.

Jeśli jednak żądanie jest ograniczone, usługa oznacza, że nie może akceptować i przetwarzać żądania od razu z powodu ograniczeń zasobów. Nie **oznacza to, że nie ma żadnych** posortowanych danych, ponieważ Service Bus po prostu nie sprawdziły się żądania. W takim przypadku poleganie na domyślnych zasadach ponawiania zestawu SDK Service Bus zapewnia, że żądanie jest ostatecznie przetwarzane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady używania Service Bus Messaging, zobacz następujące tematy zaawansowane:

* [Przegląd komunikatów Service Bus](service-bus-messaging-overview.md)
* [Szybki start: wysyłanie i odbieranie komunikatów przy użyciu witryny Azure Portal i platformy .NET](service-bus-quickstart-portal.md)
* [Samouczek: aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)

