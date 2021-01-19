---
title: Wydajność i skalowanie w Durable Functions na platformie Azure
description: Dowiedz się więcej na temat unikatowych cech skalowania rozszerzenia Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 120335a7bce83bc3d4771ea64f665d67c7d1079a
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572803"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Wydajność i skalowanie w usłudze Durable Functions (Azure Functions)

Aby zoptymalizować wydajność i skalowalność, ważne jest zrozumienie unikatowych cech skalowania [Durable Functions](durable-functions-overview.md).

Aby zrozumieć zachowanie skalowania, należy poznać niektóre szczegóły podstawowego dostawcy usługi Azure Storage.

## <a name="history-table"></a>Tabela historii

Tabela **historia** jest tabelą usługi Azure Storage, która zawiera zdarzenia historii dla wszystkich wystąpień aranżacji w centrum zadań. Nazwa tej tabeli ma postać historia *TaskHubName*. W miarę uruchamiania wystąpień nowe wiersze są dodawane do tej tabeli. Klucz partycji tej tabeli pochodzi od identyfikatora wystąpienia aranżacji. Identyfikator wystąpienia jest w większości przypadków losowych, co zapewnia optymalną dystrybucję partycji wewnętrznych w usłudze Azure Storage.

Gdy wystąpienie aranżacji musi działać, odpowiednie wiersze tabeli historii są ładowane do pamięci. Te *zdarzenia historii* są następnie odtwarzane w kodzie funkcji programu Orchestrator w celu przywrócenia ich do stanu poprzedniego punktu kontrolnego. Użycie historii wykonywania w celu odbudowania stanu w ten sposób wpływa na wzorzec określania [źródła zdarzeń](/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela wystąpień

Tabela **wystąpień** jest inną tabelą usługi Azure Storage, która zawiera Stany wszystkich wystąpień aranżacji i jednostek w ramach centrum zadań. Po utworzeniu wystąpienia nowe wiersze są dodawane do tej tabeli. Klucz partycji tej tabeli jest IDENTYFIKATORem wystąpienia aranżacji lub kluczem jednostki, a klucz wiersza jest pustym ciągiem. Istnieje jeden wiersz dla aranżacji lub wystąpienia jednostki.

Ta tabela służy do zaspokojenia żądań zapytań wystąpienia z `GetStatusAsync` interfejsów API (.NET) i `getStatus` (JavaScript), a także do [zapytania o stan Query API](durable-functions-http-api.md#get-instance-status). Jest on stale spójny z zawartością tabeli **historii** wymienionej wcześniej. Użycie oddzielnej tabeli usługi Azure Storage w celu wydajnej realizacji operacji zapytania o wystąpienie w ten sposób wpływa na [wzorzec Command and Query Responsibility Segregation (CQRS)](/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Wyzwalacze wewnętrznej kolejki

Funkcje i funkcje programu Orchestrator są wyzwalane przez kolejki wewnętrzne w centrum zadań aplikacji funkcji. Użycie kolejek w ten sposób zapewnia niezawodne gwarancje dostarczania komunikatów "co najmniej raz". W Durable Functions istnieją dwa typy kolejek: **Kolejka sterowania** i **Kolejka elementów roboczych**.

### <a name="the-work-item-queue"></a>Kolejka elementów roboczych

W Durable Functions istnieje jedna kolejka elementów pracy dla każdego centrum zadań. Jest to podstawowa Kolejka i zachowuje się podobnie do każdej innej `queueTrigger` kolejki w Azure Functions. Ta Kolejka służy do wyzwalania bezstanowych *funkcji działania* przez odtworzenie pojedynczej wiadomości w danym momencie. Każdy z tych komunikatów zawiera dane wejściowe funkcji działania i dodatkowe metadane, takie jak funkcja, która ma zostać wykonana. Gdy aplikacja Durable Functions skaluje się do wielu maszyn wirtualnych, te maszyny wirtualne współpracują w celu uzyskania pracy z kolejki elementów roboczych.

### <a name="control-queues"></a>Kolejki sterowania

Istnieje wiele *kolejek sterowania* na centrum zadań w Durable Functions. *Kolejka sterująca* jest bardziej zaawansowana niż w przypadku prostszej kolejki elementów roboczych. Kolejki sterujące są używane do wyzwalania stanowych programów Orchestrator i Entity. Ponieważ wystąpienia usługi Orchestrator i Entity Functions są stanowymi pojedynczymi, nie jest możliwe używanie konkurującego modelu konsumenta do dystrybucji obciążenia między maszynami wirtualnymi. Zamiast tego komunikaty programu Orchestrator i jednostek są zrównoważone obciążenie w kolejkach sterowania. Więcej szczegółów dotyczących tego zachowania można znaleźć w kolejnych sekcjach.

Kolejki sterujące zawierają różne typy komunikatów cyklu życia aranżacji. Przykładami mogą być [komunikaty sterujące programu Orchestrator](durable-functions-instance-management.md), komunikaty *odpowiedzi* funkcji działania i komunikaty czasomierza. Tak wiele komunikatów, jak wiadomości 32 zostaną rozkolejkowane z kolejki kontroli w ramach jednej ankiety. Te komunikaty zawierają dane ładunku, a także metadane, w tym to wystąpienie aranżacji, dla którego jest przeznaczone. Jeśli wiele komunikatów z kolejki jest przeznaczonych dla tego samego wystąpienia aranżacji, zostaną one przetworzone jako Partia zadań.

### <a name="queue-polling"></a>Sondowanie kolejki

Rozszerzenie zadania trwałego implementuje losowy wykładniczy algorytm wycofywania, aby zmniejszyć wpływ sondowania w kolejce na koszty transakcji magazynu. Po znalezieniu komunikatu środowisko uruchomieniowe natychmiast sprawdza inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka przez pewien czas przed ponowieniem próby. Po kolejnych nieudanych próbach uzyskania komunikatu w kolejce czas oczekiwania będzie się zwiększać do momentu osiągnięcia maksymalnego czasu oczekiwania, którego wartość domyślna to 30 sekund.

Maksymalne opóźnienie sondowania można skonfigurować za pomocą `maxQueuePollingInterval` właściwości w [host.jsna pliku](../functions-host-json.md#durabletask). Ustawienie tej właściwości na wyższą wartość może skutkować większymi opóźnieniami przetwarzania komunikatów. Wyższe opóźnienia byłyby oczekiwane tylko po okresach braku aktywności. Ustawienie tej właściwości na niższą wartość może skutkować wyższym kosztem magazynowania spowodowanym zwiększonymi transakcjami magazynu.

> [!NOTE]
> Po uruchomieniu w planach użycia Azure Functions i Premium [kontroler Azure Functions skalowania](../event-driven-scaling.md) będzie sondował każdą kolejkę formantów i elementów roboczych co 10 sekund. Ta dodatkowa sonda jest konieczna do określenia, kiedy należy aktywować wystąpienia aplikacji funkcji i podejmować decyzje dotyczące skalowania. W czasie pisania ten 10-sekundowy interwał jest stały i nie można go skonfigurować.

### <a name="orchestration-start-delays"></a>Opóźnienia rozpoczęcia aranżacji
Wystąpienia aranżacji są uruchamiane przez umieszczenie `ExecutionStarted` komunikatu w jednej z kolejek sterowania centrum zadań. W pewnych warunkach mogą wystąpić opóźnienia Wielosekundowe między rozpoczęciem planowania i uruchomieniem jego działania. W tym przedziale czasu wystąpienie aranżacji pozostanie w `Pending` stanie. Istnieją dwie potencjalne przyczyny tego opóźnienia:

1. **Zaległe kolejki kontroli**: Jeśli kolejka kontroli dla tego wystąpienia zawiera dużą liczbę komunikatów, może upłynąć trochę czasu, zanim `ExecutionStarted` komunikat zostanie odebrany i przetworzony przez środowisko uruchomieniowe. Zaległości komunikatów mogą wystąpić, gdy aranżacje przetwarzają wiele zdarzeń współbieżnie. Zdarzenia, które przechodzą do kolejki sterującej, obejmują zdarzenia uruchamiania aranżacji, uzupełniania działań, trwałe czasomierze, zakończenie i zdarzenia zewnętrzne. Jeśli to opóźnienie występuje w normalnych warunkach, należy rozważyć utworzenie nowego centrum zadań o większej liczbie partycji. Skonfigurowanie kolejnych partycji spowoduje, że środowisko uruchomieniowe utworzy więcej kolejek kontroli na potrzeby dystrybucji obciążenia.

2. **Wycofaj opóźnienia sondowania**: Inną częstą przyczyną opóźnień aranżacji jest [poprzednio opisana w ten sposób zachowanie sondowania dla kolejek kontroli](#queue-polling). To opóźnienie jest jednak oczekiwane tylko wtedy, gdy aplikacja jest skalowana do dwóch lub więcej wystąpień. Jeśli istnieje tylko jedno wystąpienie aplikacji lub jeśli wystąpienie aplikacji, które uruchamia aranżację, jest również tym samym wystąpieniem, które sonduje docelową kolejkę kontroli, nie będzie opóźnienia sondowania kolejki. Opóźnienia sondowania można zmniejszyć przez aktualizację **host.jsw** ustawieniach, jak opisano wcześniej.

## <a name="storage-account-selection"></a>Wybór konta magazynu

Kolejki, tabele i obiekty blob używane przez Durable Functions są tworzone w skonfigurowanym koncie usługi Azure Storage. Konto, które ma być używane, można określić przy użyciu `durableTask/storageProvider/connectionStringName` Ustawienia (lub `durableTask/azureStorageConnectionStringName` ustawienia w Durable Functions 1. x) w **host.js** pliku.

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Jeśli nie zostanie określony, `AzureWebJobsStorage` zostanie użyte domyślne konto magazynu. W przypadku obciążeń z uwzględnieniem wydajności zaleca się jednak skonfigurowanie konta magazynu innego niż domyślne. Durable Functions korzysta z usługi Azure Storage silniej i użycie dedykowanego konta magazynu izoluje Durable Functions użycie magazynu z użytku wewnętrznego przez hosta Azure Functions.

## <a name="orchestrator-scale-out"></a>Skalowanie w poziomie programu Orchestrator

Funkcje działania są bezstanowe i skalowane automatycznie przez dodanie maszyn wirtualnych. Funkcje i jednostki programu Orchestrator, z drugiej strony, są *podzielone na partycje* w jednej lub kilku kolejkach sterowania. Liczba kolejek sterujących jest definiowana w **host.js** pliku. Poniższy przykład host.jsna fragmencie kodu ustawia `durableTask/storageProvider/partitionCount` Właściwość (lub `durableTask/partitionCount` w Durable Functions 1. x) na `3` .

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Centrum zadań można skonfigurować z zakresu od 1 do 16 partycji. Jeśli nie zostanie określony, domyślna liczba partycji to **4**.

Podczas skalowania do wielu wystąpień hosta funkcji (zwykle na różnych maszynach wirtualnych) każde wystąpienie uzyskuje blokadę dla jednej z kolejek kontrolek. Te blokady są wewnętrznie implementowane jako dzierżawy magazynu obiektów blob i zapewniają, że wystąpienie lub jednostka aranżacji działa tylko na jednym wystąpieniu hosta naraz. Jeśli centrum zadań jest skonfigurowane z trzema kolejkami sterowania, wystąpienia aranżacji i jednostki można zrównoważyć obciążenie w ramach maksymalnie trzech maszyn wirtualnych. Dodatkowe maszyny wirtualne można dodać, aby zwiększyć pojemność wykonywania funkcji działania.

Na poniższym diagramie przedstawiono sposób interakcji hosta Azure Functions z jednostkami magazynu w środowisku skalowania w poziomie.

![Diagram skalowania](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak pokazano na poprzednim diagramie, wszystkie maszyny wirtualne konkurują o komunikaty w kolejce elementów roboczych. Jednak tylko trzy maszyny wirtualne mogą pobierać komunikaty z kolejek kontroli, a każda maszyna wirtualna blokuje jedną kolejkę kontroli.

Wystąpienia aranżacji i jednostki są dystrybuowane we wszystkich wystąpieniach kolejek kontroli. Dystrybucja odbywa się przez mieszanie identyfikatora wystąpienia aranżacji lub nazwy jednostki i pary kluczy. Identyfikatory wystąpień aranżacji domyślnie to losowe identyfikatory GUID, dzięki czemu wystąpienia są równomiernie dystrybuowane we wszystkich kolejkach sterowania.

Ogólnie mówiąc, funkcje programu Orchestrator mają być lekkie i nie powinny wymagać dużej ilości mocy obliczeniowej. Dlatego nie jest konieczne tworzenie dużej liczby partycji kolejki kontroli w celu uzyskania dużej przepływności dla aranżacji. Większość dużej ilości pracy należy wykonać w funkcjach bezstanowych, które można skalować w nieskończoność.

## <a name="auto-scale"></a>Automatyczne skalowanie

Podobnie jak w przypadku wszystkich Azure Functions działających w planach użycia i elastycznych warstw Premium, Durable Functions obsługuje skalowanie automatyczne za pośrednictwem [kontrolera Azure Functions skali](../event-driven-scaling.md#runtime-scaling). Kontroler skalowania monitoruje opóźnienie wszystkich kolejek przez okresowe wydawanie poleceń _wglądu_ . Na podstawie opóźnień wiadomości z wglądem, kontroler skalowania zdecyduje się, czy dodać lub usunąć maszyny wirtualne.

Jeśli kontroler skalowania ustali, że opóźnienia komunikatów w kolejce sterującej są zbyt wysokie, to spowoduje dodanie wystąpień maszyn wirtualnych do momentu zmniejszenia opóźnienia komunikatów do akceptowalnego poziomu lub osiągnie liczbę partycji kolejki kontroli. Podobnie kontroler skalowania ciągle dodaje wystąpienia maszyn wirtualnych, jeśli opóźnienia kolejki elementu pracy są wysokie, niezależnie od liczby partycji.

> [!NOTE]
> Począwszy od Durable Functions 2,0, aplikacje funkcji można skonfigurować tak, aby były uruchamiane w punktach końcowych usługi chronionej przez sieć wirtualną w planie elastycznej Premium. W tej konfiguracji Durable Functions wyzwalacze inicjują żądania skalowania zamiast kontrolera skalowania.

## <a name="thread-usage"></a>Użycie wątku

Funkcje programu Orchestrator są wykonywane w pojedynczym wątku, aby zapewnić, że wykonywanie może być deterministyczne dla wielu odtworzeń. Ze względu na to wykonywanie jednowątkowe należy pamiętać, że wątki funkcji programu Orchestrator nie wykonują zadań intensywnie korzystających z procesora CPU, operacji we/wy lub bloku z dowolnego powodu. Każda z zadań, które mogą wymagać wejścia/wyjścia, blokowania lub wielu wątków, powinna zostać przeniesiona do funkcji działania.

Funkcje działania mają wszystkie te same zachowania co regularne funkcje wyzwalane przez kolejki. Mogą bezpiecznie wykonywać operacje we/wy, wykonywać intensywność procesora CPU i korzystać z wielu wątków. Ponieważ wyzwalacze aktywności są bezstanowe, mogą swobodnie skalować w poziomie do nieograniczonej liczby maszyn wirtualnych.

Funkcje jednostki są również wykonywane w jednym wątku, a operacje są przetwarzane jeden w czasie. Jednak funkcje jednostki nie mają żadnych ograniczeń dotyczących typu kodu, który może być wykonywany.

## <a name="concurrency-throttles"></a>Ograniczenia współbieżności

Azure Functions obsługuje wykonywanie wielu funkcji jednocześnie w ramach jednego wystąpienia aplikacji. Takie współbieżne wykonywanie pozwala zwiększyć równoległość i zminimalizować liczbę "zimnego startu", które będą miały w czasie typową aplikację. Jednak wysoka współbieżność może wyczerpać zasoby systemowe dla maszyn wirtualnych, takie jak połączenia sieciowe lub dostępna pamięć. W zależności od potrzeb aplikacji funkcji może być konieczne ograniczenie współbieżności poszczególnych wystąpień, aby uniknąć możliwości uruchamiania pamięci w sytuacjach wymagających dużego obciążenia.

Limity współbieżności działań, Orchestrator i Entity można skonfigurować w **host.js** pliku. Odpowiednie ustawienia dotyczą `durableTask/maxConcurrentActivityFunctions` funkcji działania oraz dla programów `durableTask/maxConcurrentOrchestratorFunctions` Orchestrator i Entity.

### <a name="functions-20"></a>Funkcje 2,0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

W poprzednim przykładzie maksymalnie 10 funkcje programu Orchestrator lub jednostek oraz 10 funkcji działania można uruchomić na jednej maszynie wirtualnej współbieżnie. Jeśli nie zostanie określony, liczba operacji współbieżnych i wykonywanych przez program Orchestrator lub Entity Functions jest ograniczona w 10X liczbę rdzeni na maszynie wirtualnej.

> [!NOTE]
> Te ustawienia ułatwiają zarządzanie użyciem pamięci i procesora na jednej maszynie wirtualnej. Jednak w przypadku skalowania w poziomie wielu maszyn wirtualnych każda maszyna wirtualna ma swój własny zestaw limitów. Tych ustawień nie można używać do kontrolowania współbieżności na poziomie globalnym.

## <a name="extended-sessions"></a>Sesje rozszerzone

Sesje rozszerzone to ustawienie, które zachowuje aranżacje i jednostki w pamięci nawet po zakończeniu przetwarzania komunikatów. Typowym efektem włączenia sesji rozszerzonych jest zredukowanie operacji we/wy na konto usługi Azure Storage i ogólna lepsza przepływność.

Sesje rozszerzone można włączyć przez ustawienie `durableTask/extendedSessionsEnabled` `true` w **host.js** pliku. To `durableTask/extendedSessionIdleTimeoutInSeconds` ustawienie może służyć do kontrolowania, jak długo bezczynna sesja będzie utrzymywana w pamięci:

**Funkcje 2,0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Funkcje 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Istnieją dwa potencjalne downsides tego ustawienia, które należy wziąć pod uwagę:

1. Istnieje ogólny wzrost użycia pamięci aplikacji funkcji.
2. W przypadku wielu współbieżnych, krótkoterminowych wykonań lub funkcji jednostek może istnieć ogólny spadek przepływności.

Przykładowo, jeśli `durableTask/extendedSessionIdleTimeoutInSeconds` jest ustawiona na 30 sekund, a następnie odcinek usługi Orchestrator lub Entity Functions, który jest wykonywany w mniej niż 1 sekund, nadal zajmuje pamięć przez 30 sekund. Liczy się również w stosunku do `durableTask/maxConcurrentOrchestratorFunctions` wspomnianego wcześniej przydziału, co potencjalnie uniemożliwia uruchomienie innych funkcji programu Orchestrator lub Entity.

W następnych sekcjach opisano określone skutki rozszerzonych sesji w programie Orchestrator i w ramach funkcji Entity.

> [!NOTE]
> Sesje rozszerzone są obecnie obsługiwane tylko w językach .NET, takich jak C# lub F #. Ustawienie `extendedSessionsEnabled` `true` dla innych platform może prowadzić do problemów z czasem wykonywania, takich jak dyskretne niepowodzenie wykonywania działań i funkcji wyzwalanych przez aranżację.


### <a name="orchestrator-function-replay"></a>Powtarzanie funkcji programu Orchestrator

Jak wspomniano wcześniej, funkcje programu Orchestrator są odtwarzane przy użyciu zawartości tabeli **historii** . Domyślnie kod funkcji programu Orchestrator jest odtwarzany za każdym razem, gdy partia komunikatów jest usuwana z kolejki sterującej. Nawet w przypadku korzystania z wentylatorów i wzorca wentylatorów, które oczekują na ukończenie wszystkich zadań (na przykład przy użyciu programu `Task.WhenAll` w środowisku .NET lub `context.df.Task.all` w języku JavaScript), zostaną odtworzone dane, które wystąpiły po przetworzeniu partii odpowiedzi zadań w czasie. Po włączeniu rozszerzonych sesji wystąpienia funkcji programu Orchestrator są przechowywane w pamięci, a nowe komunikaty mogą być przetwarzane bez powtarzania pełnej historii.

Zwiększenie wydajności rozszerzonych sesji jest najczęściej zauważalne w następujących sytuacjach:

* W przypadku równoczesnego uruchamiania ograniczonej liczby wystąpień aranżacji.
* Gdy aranżacje mają dużą liczbę akcji sekwencyjnych (np. setki wywołań funkcji działania), które szybko zakończyły pracę.
* Gdy organizuje wentylator i wentylator — w dużej liczbie działań, które są wykonywane w tym samym czasie.
* Gdy funkcje programu Orchestrator muszą przetwarzać duże komunikaty lub wykonywać przetwarzanie danych intensywnie korzystających z procesora CPU.

We wszystkich innych sytuacjach zwykle nie ma zauważalnej poprawy wydajności funkcji programu Orchestrator.

> [!NOTE]
> Tych ustawień należy używać tylko po pełnym opracowaniu i przetestowaniu funkcji programu Orchestrator. Domyślne zachowanie funkcji agresywnego powtarzania może być przydatne do wykrywania naruszeń [kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) w czasie projektowania i dlatego jest domyślnie wyłączona.

### <a name="entity-function-unloading"></a>Zwalnianie funkcji jednostki

Funkcja Entity Functions przetwarza do 20 operacji w pojedynczej partii. Gdy tylko jednostka zakończy przetwarzanie partii operacji, zachowuje jej stan i zwalnia z pamięci. Można opóźnić wyładowywanie jednostek z pamięci przy użyciu ustawienia sesji rozszerzonych. Jednostki nadal utrzymują swoje zmiany stanu tak jak wcześniej, ale pozostają w pamięci przez skonfigurowany okres, aby zmniejszyć liczbę obciążeń z usługi Azure Storage. Ta redukcja obciążeń z usługi Azure Storage może zwiększyć ogólną przepływność często używanych jednostek.

## <a name="performance-targets"></a>Cele wydajności

Planując użycie Durable Functions dla aplikacji produkcyjnej, ważne jest, aby uwzględnić wymagania dotyczące wydajności wczesne w procesie planowania. W tej sekcji omówiono niektóre podstawowe scenariusze użycia i oczekiwane maksymalne numery przepływności.

* **Wykonywanie działania sekwencyjnego**: w tym scenariuszu opisano funkcję programu Orchestrator, która uruchamia serię funkcji działania jeden po drugim. Jest to najbardziej zbliżone do przykładu [łańcucha funkcji](durable-functions-sequence.md) .
* **Równoległe wykonywanie działań**: w tym scenariuszu opisano funkcje programu [Orchestrator, które](durable-functions-cloud-backup.md) wykonują wiele funkcji działania równolegle przy użyciu wzorca wentylatorów.
* **Równoległe przetwarzanie odpowiedzi**: ten scenariusz to druga połowa [wentylatoru i wzorca wentylatorów](durable-functions-cloud-backup.md) . Koncentruje się na wydajności wentylatorów. Należy pamiętać, że w przeciwieństwie do wentylatorów, wentylator jest wykonywane przez pojedyncze wystąpienie funkcji programu Orchestrator i w związku z tym może działać tylko na jednej maszynie wirtualnej.
* **Przetwarzanie zdarzeń zewnętrznych**: ten scenariusz reprezentuje pojedyncze wystąpienie funkcji programu Orchestrator, które czeka na [zdarzenia zewnętrzne](durable-functions-external-events.md), po jednym naraz.
* **Przetwarzanie operacji jednostki**: ten scenariusz sprawdza, jak szybko _pojedynczy_ [obiekt licznika](durable-functions-entities.md) może przetwarzać stałe strumienie operacji.

> [!TIP]
> W przeciwieństwie do wentylatorów, operacje wentylatorów są ograniczone do pojedynczej maszyny wirtualnej. Jeśli aplikacja korzysta z wentylatorów, wentylator-in i ma na celu wydajność wentylatorów, rozważ poddzielenie wentylatorów funkcji działania między wiele [podkategorii](durable-functions-sub-orchestrations.md).

W poniższej tabeli przedstawiono oczekiwane *maksymalne* numery przepływności dla poprzednio opisanych scenariuszy. "Wystąpienie" odnosi się do pojedynczego wystąpienia funkcji programu Orchestrator działającej na pojedynczej niewielkiej maszynie wirtualnej ([a1](../../virtual-machines/sizes-previous-gen.md)) w Azure App Service. We wszystkich przypadkach zakłada się, że [rozszerzone sesje](#orchestrator-function-replay) są włączone. Rzeczywiste wyniki mogą się różnić w zależności od procesora lub pracy we/wy wykonanej przez kod funkcji.

| Scenariusz | Maksymalna przepływność |
|-|-|
| Wykonywanie działania sekwencyjnego | 5 działań na sekundę, na wystąpienie |
| Równoległe wykonywanie działań (wentylator-out) | 100 działań na sekundę, na wystąpienie |
| Przetwarzanie reakcji równoległej (wentylator) | 150 odpowiedzi na sekundę, na wystąpienie |
| Przetwarzanie zdarzeń zewnętrznych | 50 zdarzeń na sekundę, na wystąpienie |
| Przetwarzanie operacji jednostki | 64 operacji na sekundę |

> [!NOTE]
> Te numery są aktualne w Durable Functions wersji 1.4.0 rozszerzenia (GA). Liczby te mogą ulec zmianie w miarę upływu czasu, gdy funkcja zostanie zakończyła się, a w miarę dokonywania optymalizacji.

Jeśli nie widzisz oczekiwanych numerów przepływności, a użycie procesora i pamięci jest w dobrej kondycji, sprawdź, czy przyczyna jest związana z [kondycją konta magazynu](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozszerzenie Durable Functions może znacznie obciążać konto usługi Azure Storage, a wystarczające duże obciążenia mogą spowodować ograniczenie konta magazynu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o odzyskiwaniu po awarii i dystrybucji geograficznej](durable-functions-disaster-recovery-geo-distribution.md)
