---
title: Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch
description: Włącz automatyczne skalowanie w puli w chmurze, aby dynamicznie dostosować liczbę węzłów obliczeniowych w puli.
ms.topic: article
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: b790ee286d9edd8cee04ef1db719be6395509be2
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113565"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Utwórz automatyczną formułę skalowania węzłów obliczeniowych w puli usługi Batch

Azure Batch może automatycznie skalować pule na podstawie zdefiniowanych parametrów. W przypadku automatycznego skalowania przetwarzanie wsadowe dynamicznie dodaje węzły do puli w miarę wzrostu wymagań zadań i usuwa węzły obliczeniowe w miarę ich zmniejszania. Możesz zaoszczędzić czas i pieniądze przez automatyczne dostosowanie liczby węzłów obliczeniowych używanych przez aplikację usługi Batch.

Automatyczne skalowanie w puli węzłów obliczeniowych można włączyć, kojarząc z nią zdefiniowaną *formułę skalowania* automatycznego. Usługa Batch używa formuły skalowania automatycznego, aby określić liczbę węzłów obliczeniowych, które są konieczne do wykonania obciążenia. Węzły obliczeniowe mogą być węzłami dedykowanymi lub [węzłami o niskim priorytecie](batch-low-pri-vms.md). Usługa Batch reaguje na dane metryk usług, które są zbierane okresowo. Za pomocą tych danych metryk, wsadowe dostosowuje liczbę węzłów obliczeniowych w puli na podstawie formuły i w skonfigurowanym interwale.

Skalowanie automatyczne można włączyć podczas tworzenia puli lub w istniejącej puli. Istnieje również możliwość zmiany istniejącej formuły w puli, która jest skonfigurowana do skalowania automatycznego. Funkcja Batch umożliwia ocenę formuł przed przypisaniem ich do pul i monitorowanie stanu automatycznych uruchomień skalowania.

W tym artykule omówiono różne jednostki, które tworzą formuły automatycznego skalowania, w tym zmienne, operatory, operacje i funkcje. Omawiamy, jak uzyskać różne metryki zasobów i zadań obliczeniowych w usłudze Batch. Za pomocą tych metryk można dostosować liczbę węzłów puli w zależności od użycia zasobów i stanu zadania. Następnie opisano sposób konstruowania formuły i włączania automatycznego skalowania w puli przy użyciu interfejsów API REST i platformy .NET. Na koniec zakończymy kilka przykładowych formuł.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Batch można określić [konfigurację konta](batch-api-basics.md#account), która decyduje o tym, czy pule są przydzieleni w ramach subskrypcji usługi Batch (wartość domyślna), czy w ramach subskrypcji użytkownika. Jeśli konto usługi Batch zostało utworzone z domyślną konfiguracją usług Batch, konto jest ograniczone do maksymalnej liczby rdzeni, które mogą być używane do przetwarzania. Usługa Batch skaluje węzły obliczeniowe tylko do tego limitu podstawowego. Z tego powodu usługa Batch może nie dotrzeć do docelowej liczby węzłów obliczeniowych określonych przez formułę skalowania automatycznego. Zobacz [przydziały i limity dla usługi Azure Batch,](batch-quota-limit.md) Aby uzyskać informacje na temat przeglądania i zwiększania limitów przydziału konta.
>
>Jeśli konto zostało utworzone przy użyciu konfiguracji subskrypcji użytkownika, Twoje konto ma udział w podstawowym limicie przydziału dla subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatyczne skalowanie formuł

Formuła automatycznego skalowania jest zdefiniowana przez użytkownika wartość ciągu, która zawiera jedną lub więcej instrukcji. Formuła skalowania automatycznego jest przypisana do elementu [autoScaleFormula][rest_autoscaleformula] puli (Batch REST) lub [CloudPool. autoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). Usługa Batch używa formuły do określenia docelowej liczby węzłów obliczeniowych w puli dla następnego interwału przetwarzania. Ciąg formuły nie może przekroczyć 8 KB, może zawierać do 100 instrukcji, które są rozdzielone średnikami i mogą zawierać podziały wierszy i komentarze.

Można myśleć o automatycznym skalowaniu formuł jako języka wsadowego skalowania automatycznego. Instrukcje formuł są niektórymi sformułowanymi wyrażeniami, które mogą zawierać zarówno zmienne zdefiniowane przez usługę, jak i zmienne zdefiniowane przez użytkownika (zdefiniowane zmienne). Mogą wykonywać różne operacje na tych wartościach przy użyciu wbudowanych typów, operatorów i funkcji. Na przykład instrukcja może mieć następującą postać:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zwykle zawierają wiele instrukcji, które wykonują operacje na wartościach, które są uzyskiwane w poprzednich instrukcjach. Na przykład najpierw uzyskamy wartość dla `variable1`, a następnie przekazujemy ją do funkcji, aby wypełnić `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Uwzględnij te instrukcje w formule skalowania automatycznego, aby dotrzeć do docelowej liczby węzłów obliczeniowych. Węzły dedykowane i węzły o niskim priorytecie mają własne ustawienia docelowe, aby można było zdefiniować element docelowy dla każdego typu węzła. Formuła automatycznego skalowania może zawierać wartość docelową dla węzłów dedykowanych, wartość docelową dla węzłów o niskim priorytecie lub obie te metody.

Docelowa liczba węzłów może być wyższa, niższa lub taka sama jak bieżąca liczba węzłów tego typu w puli. Wsadowe oblicza formułę skalowania automatycznego puli w określonym interwale (zobacz [Automatyczne skalowanie interwałów](#automatic-scaling-interval)). Sekwencja wsadowa dostosowuje liczbę docelową każdego typu węzła w puli do liczby, która jest określana przez formułę skalowania automatycznego w czasie szacowania.

### <a name="sample-autoscale-formulas"></a>Przykładowe formuły automatycznego skalowania

Poniżej przedstawiono przykłady dwóch formuł automatycznego skalowania, które można dostosować do pracy w większości scenariuszy. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` w przykładowych formułach można dostosować do własnych potrzeb.

#### <a name="pending-tasks"></a>Oczekujące zadania

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

W przypadku tej formuły automatycznego skalowania Pula jest początkowo tworzona z jedną maszyną wirtualną. `$PendingTasks` Metryka definiuje liczbę zadań, które są uruchomione lub umieszczone w kolejce. Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio ustawia `$TargetDedicatedNodes` zmienną. Formuła zapewnia, że docelowa liczba węzłów dedykowanych nigdy nie przekracza 25 maszyn wirtualnych. W miarę przesyłania nowych zadań Pula automatycznie rośnie. Po zakończeniu zadań maszyny wirtualne stają się wolne od siebie, a formuła skalowania automatycznego zmniejsza pulę.

Ta formuła skaluje dedykowane węzły, ale można ją zmodyfikować, aby zastosować ją również do skalowania węzłów o niskim priorytecie.

#### <a name="preempted-nodes"></a>Zastępujące węzły 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Ten przykład tworzy pulę, która rozpoczyna się od 25 węzłów o niskim priorytecie. Za każdym razem, gdy węzeł o niskim priorytecie jest zastępowany, zostanie on zastąpiony przez dedykowany węzeł. Podobnie jak w przypadku pierwszego przykładu `maxNumberofVMs` zmienna zapobiega przekraczaniu 25 maszyn wirtualnych. Ten przykład jest przydatny do korzystania z maszyn wirtualnych o niskim priorytecie, a także w przypadku, gdy tylko stała liczba przeniesień będzie miała miejsce w okresie istnienia puli.

## <a name="variables"></a>Zmienne

W formułach skalowania automatycznego można używać zarówno zmiennych **zdefiniowanych przez usługę** , jak i **zdefiniowanych przez użytkownika** . Zmienne zdefiniowane przez usługę są wbudowane w usługę Batch. Niektóre zmienne zdefiniowane przez usługę są do odczytu i zapisu, a niektóre z nich są tylko do odczytu. Zmienne zdefiniowane przez użytkownika to zmienne, które definiujesz. W przykładowej formule pokazanej w poprzedniej `$TargetDedicatedNodes` sekcji `$PendingTasks` i są zmiennymi zdefiniowanymi przez usługę. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` są zmiennymi zdefiniowanymi przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługę zawsze poprzedzają znak dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalny.
>
>

W poniższych tabelach przedstawiono zarówno zmienne do odczytu i zapisu, jak i tylko do odczytu, które są zdefiniowane przez usługę Batch.

Można pobrać i ustawić wartości tych zmiennych zdefiniowanych przez usługę w celu zarządzania liczbą węzłów obliczeniowych w puli:

| Zmienne zdefiniowane przez usługę do odczytu i zapisu | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowa liczba dedykowanych węzłów obliczeniowych w puli. Liczba węzłów dedykowanych jest określona jako element docelowy, ponieważ pula może nie zawsze osiągnąć żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów jest modyfikowana przez ocenę skalowania automatycznego przed osiągnięciem początkowej docelowej puli, Pula może nie dotrzeć do miejsca docelowego. <br /><br /> Pula w ramach konta utworzonego przy użyciu konfiguracji usługi Batch może nie osiągnąć swojego celu, jeśli element docelowy przekracza węzeł konta usługi Batch lub przydział podstawowy. Pula w ramach konta utworzonego przy użyciu konfiguracji subskrypcji użytkownika może nie osiągnąć swojego celu, jeśli wartość docelowa przekroczy współużytkowany przydział rdzeni dla subskrypcji.|
| $TargetLowPriorityNodes |Docelowa liczba węzłów obliczeniowych o niskim priorytecie dla puli. Liczba węzłów o niskim priorytecie jest określana jako element docelowy, ponieważ pula może nie zawsze osiągnąć żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów o niskim priorytecie jest modyfikowana przez ocenę skalowania automatycznego przed osiągnięciem początkowej docelowej puli, Pula może nie dotrzeć do miejsca docelowego. Pula może być również nieosiągalna, jeśli element docelowy przekracza węzeł konta wsadowego lub przydział podstawowy. <br /><br /> Aby uzyskać więcej informacji na temat węzłów obliczeniowych o niskim priorytecie, zobacz [Korzystanie z maszyn wirtualnych o niskim priorytecie w usłudze Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja wykonywana po usunięciu węzłów obliczeniowych z puli. Możliwe wartości:<ul><li>**requeue**--wartość domyślna. Natychmiast kończy zadania i umieszcza je z powrotem w kolejce zadań, aby zostały ponownie zaplanowane. Ta akcja zapewnia, że docelowa liczba węzłów jest możliwie najszybsza, ale może być mniej wydajna, ponieważ wszystkie uruchomione zadania zostaną przerwane i trzeba będzie je ponownie uruchomić, transakcję już wykonanej pracy. <li>**Przerwij**— kończy zadania natychmiast i usuwa je z kolejki zadań.<li>**taskcompletion**--czeka na zakończenie działania aktualnie wykonywanych zadań, a następnie usuwa węzeł z puli. Użyj tej opcji, aby uniknąć przerwania i przetworzenia kolejki zadań do wykonania przez zadanie. <li>**retaineddata**--czeka na oczyszczenie wszystkich lokalnych danych zadań przechowywanych w węźle przed usunięciem węzła z puli.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes` Zmienną można również określić przy użyciu aliasu `$TargetDedicated`. Podobnie `$TargetLowPriorityNodes` zmienna może być określona za pomocą aliasu `$TargetLowPriority`. Jeśli zarówno w pełni nazwana zmienna, jak i jej alias są ustawiane przez formułę, pierwszeństwo ma wartość przypisana do pełnej nazwanej zmiennej.
>
>

Możesz uzyskać wartości tych zmiennych zdefiniowanych przez usługę, aby wprowadzić zmiany, które są oparte na metrykach usługi Batch:

| Zmienne zdefiniowane przez usługę tylko do odczytu | Opis |
| --- | --- |
| $CPUPercent |Średni procent użycia procesora CPU. |
| $WallClockSeconds |Liczba użytych sekund. |
| $MemoryBytes |Średnia liczba użytych megabajtów. |
| $DiskBytes |Średnia liczba gigabajtów używanych na dyskach lokalnych. |
| $DiskReadBytes |Liczba odczytanych bajtów. |
| $DiskWriteBytes |Liczba zapisanych bajtów. |
| $DiskReadOps |Liczba wykonanych operacji odczytu dysku. |
| $DiskWriteOps |Liczba wykonanych operacji zapisu na dysku. |
| $NetworkInBytes |Liczba bajtów przychodzących. |
| $NetworkOutBytes |Liczba bajtów wychodzących. |
| $SampleNodeCount |Liczba węzłów obliczeniowych. |
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale jeszcze nie są wykonywane. Liczba $ActiveTasks obejmuje wszystkie zadania znajdujące się w stanie aktywnym, których zależności zostały spełnione. Wszystkie zadania, które znajdują się w stanie aktywnym, ale których zależności nie zostały spełnione, są wykluczone z liczby $ActiveTasks. W przypadku zadania o wielu wystąpieniach $ActiveTasks będzie zawierać liczbę wystąpień ustawionych dla zadania.|
| $RunningTasks |Liczba zadań w stanie uruchomienia. |
| $PendingTasks |Suma $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyły się pomyślnie. |
| $FailedTasks |Liczba zadań, które zakończyły się niepowodzeniem. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzłów obliczeniowych. |
| $CurrentLowPriorityNodes |Bieżąca liczba węzłów obliczeniowych o niskim priorytecie, w tym wszystkich węzłów, które zostały przeniesiona. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie przeniesiona. |

> [!TIP]
> Zmienne zdefiniowane w ramach usługi, które są wyświetlane w poprzedniej tabeli, to *obiekty* , które udostępniają różne metody uzyskiwania dostępu do danych skojarzonych z każdym z nich. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie przykładowych danych](#getsampledata) w dalszej części tego artykułu.
>
>

## <a name="types"></a>Typy

Te typy są obsługiwane w formule:

* double
* doubleVec
* doubleVecList
* ciąg
* Sygnatura czasowa — sygnatura czasowa jest strukturą złożoną, która zawiera następujące elementy członkowskie:

  * rok
  * miesiąc (1-12)
  * dzień (1-31)
  * dzień tygodnia (w formacie liczbowym, na przykład 1 dla poniedziałku)
  * godzina (w formacie 24-godzinnym, na przykład 13 oznacza 1 PM)
  * minuta (00-59)
  * sekunda (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operacje

Te operacje są dozwolone dla typów, które są wymienione w poprzedniej sekcji.

| Operacja | Obsługiwane operatory | Typ wyniku |
| --- | --- | --- |
| podwójny *operator* Double |+, -, *, / |double |
| podwójny *operator* TimeInterval |* |timeinterval |
| *operator* doubleVec (Double) |+, -, *, / |doubleVec |
| *operator* doubleVec doubleVec |+, -, *, / |doubleVec |
| *operator* TimeInterval (Double) |*, / |timeinterval |
| *operator* TimeInterval TimeInterval |+, - |timeinterval |
| Sygnatura czasowa *operatora* TimeInterval |+ |sygnatura czasowa |
| TimeInterval *operatora* sygnatury czasowej |+ |sygnatura czasowa |
| Sygnatura czasowa *operatora* timestamp |- |timeinterval |
| *operator*Double |-, ! |double |
| TimeInterval *operatora* |- |timeinterval |
| podwójny *operator* Double |<, <=, = =, >=, >,! = |double |
| ciąg *operatora* ciągu |<, <=, = =, >=, >,! = |double |
| Sygnatura czasowa *operatora* timestamp |<, <=, = =, >=, >,! = |double |
| *operator* TimeInterval TimeInterval |<, <=, = =, >=, >,! = |double |
| podwójny *operator* Double |&&,  &#124;&#124; |double |

Podczas testowania podwójnego przy użyciu operatora Trzyelementowy (`double ? statement1 : statement2`) niezerowego ma **wartość true**, a zero ma **wartość false**.

## <a name="functions"></a>Funkcje
Te wstępnie zdefiniowane **funkcje** są dostępne do użycia podczas definiowania formuły skalowania automatycznego.

| Funkcja | Zwracany typ | Opis |
| --- | --- | --- |
| Średnia (doubleVecList) |double |Zwraca średnią wartość dla wszystkich wartości w doubleVecList. |
| len (doubleVecList) |double |Zwraca długość wektora utworzonego na podstawie doubleVecList. |
| LG (Double) |double |Zwraca bazę logarytmiczną 2 z podwójnej precyzji. |
| LG (doubleVecList) |doubleVec |Zwraca bazę dziennika ze składnikiem 2 z doubleVecList. Element VEC (Double) musi być jawnie zakończony dla parametru. W przeciwnym razie przyjmowana jest podwójna wersja LG (Double). |
| ln (Double) |double |Zwraca naturalny dziennik z podwójnej precyzji. |
| ln (doubleVecList) |doubleVec |Zwraca naturalny dziennik z podwójnej precyzji. |
| Dziennik (Double) |double |Zwraca bazę dziennika o wartości 10 z podwójnej precyzji. |
| Dziennik (doubleVecList) |doubleVec |Zwraca bazę dziennika ze składnikami 10 doubleVecList. Element VEC (Double) musi być jawnie zakończony dla pojedynczego parametru Double. W przeciwnym razie przyjmowana jest wersja podwójnego rejestrowania (Double). |
| Max (doubleVecList) |double |Zwraca maksymalną wartość w doubleVecList. |
| min (doubleVecList) |double |Zwraca minimalną wartość w doubleVecList. |
| Norma (doubleVecList) |double |Zwraca dwie normy wektora, który jest tworzony na podstawie doubleVecList. |
| percentyl (doubleVec v, Double p) |double |Zwraca element percentylu wektora v. |
| rand() |double |Zwraca wartość losową z zakresu od 0,0 do 1,0. |
| zakres (doubleVecList) |double |Zwraca różnicę między wartościami minimalną i maksymalną w doubleVecList. |
| STD (doubleVecList) |double |Zwraca odchylenie standardowe próbki wartości w doubleVecList. |
| Stop () | |Kończy Obliczanie wyrażenia skalowania automatycznego. |
| Sum (doubleVecList) |double |Zwraca sumę wszystkich składników doubleVecList. |
| Time (ciąg dateTime = "") |sygnatura czasowa |Zwraca sygnaturę czasową bieżącego czasu, jeśli nie są spełnione żadne parametry lub sygnaturę czasową ciągu dateTime, jeśli został on zakończony. Obsługiwane formaty dateTime to W3C-DTF i RFC 1123. |
| Val (doubleVec v, podwójne i) |double |Zwraca wartość elementu, który znajduje się w lokalizacji i w wektorze v, z indeksem początkowym równym zero. |

Niektóre funkcje, które są opisane w poprzedniej tabeli, mogą akceptować listę jako argument. Rozdzielana przecinkami lista jest dowolną kombinacją wartości *Double* i *doubleVec*. Przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Wartość *doubleVecList* jest konwertowana na jedną *doubleVec* przed oceną. Na przykład, jeśli `v = [1,2,3]`, wywołanie `avg(v)` jest równoważne wywołaniu `avg(1,2,3)`. Wywołanie `avg(v, 7)` jest równoważne wywołaniu `avg(1,2,3,7)`.

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Uzyskiwanie przykładowych danych

Formuły skalowania automatycznego działają na danych metryk (próbek) dostarczanych przez usługę Batch. Formuła zwiększa lub zmniejsza rozmiar puli na podstawie wartości pobieranych z usługi. Opisane wcześniej zmienne zdefiniowane przez usługę to obiekty, które udostępniają różne metody uzyskiwania dostępu do danych, które są skojarzone z tym obiektem. Na przykład poniższe wyrażenie przedstawia żądanie pobrania ostatnich pięciu minut użycia procesora CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Opis |
| --- | --- |
| Getsample () |`GetSample()` Metoda zwraca wektor próbek danych.<br/><br/>Przykładem jest 30-sekundowa ilość danych metryk. Innymi słowy, próbki są uzyskiwane co 30 sekund. Jak pokazano poniżej, występuje opóźnienie między pobieraniem próbki a udostępnieniem jej w formule. W związku z tym nie wszystkie próbki dla danego okresu mogą być dostępne do oceny przez formułę.<ul><li>`doubleVec GetSample(double count)`<br/>Określa liczbę próbek do pobrania z najnowszych próbek, które zostały zebrane.<br/><br/>`GetSample(1)`Zwraca ostatni dostępny przykład. Nie należy jednak `$CPUPercent`używać takich metryk, ponieważ nie można wiedzieć, *kiedy* przykład został zebrany. Może być to Najnowsza wersja, lub z powodu problemów z systemem, może być ona znacznie starsza. W takich przypadkach lepszym rozwiązaniem jest użycie przedziału czasu, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Określa ramy czasowe do zbierania przykładowych danych. Opcjonalnie określa również procent próbek, które muszą być dostępne w żądanym przedziale czasowym.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`zwraca 20 próbek, jeśli wszystkie próbki w ciągu ostatnich 10 minut są obecne w historii CPUPercent. Jeśli Ostatnia minuta historii była niedostępna, zostaną zwrócone tylko 18 próbek. W takim przypadku:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`nie powiedzie się, ponieważ dostępne są tylko 90 procent próbek.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`powiedzie się.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Określa ramy czasowe do zbierania danych, zarówno czas rozpoczęcia, jak i godzinę zakończenia.<br/><br/>Jak wspomniano powyżej, występuje opóźnienie między pobieraniem próbki a udostępnieniem jej w formule. Należy wziąć pod uwagę to opóźnienie przy `GetSample` użyciu metody. Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres próbkowania, które zostały wykonane w historycznym zestawie danych przykładowych. |
| Count () |Zwraca łączną liczbę próbek w historii metryk. |
| HistoryBeginTime() |Zwraca sygnaturę czasową najstarszego dostępnego przykładu danych dla metryki. |
| GetSamplePercent() |Zwraca wartość procentową próbek, które są dostępne dla danego interwału czasu. Przykład:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Ponieważ `GetSample` Metoda kończy się niepowodzeniem, jeśli wartość procentowa zwracanych próbek jest `samplePercent` mniejsza niż określona, można użyć `GetSamplePercent` metody do sprawdzenia w pierwszej kolejności. Następnie można wykonać alternatywną akcję, jeśli istnieją niewystarczające próbki, bez zatrzymania automatycznej oceny skalowania. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Przykłady, przykładowa wartość procentowa i Metoda *getsample ()*
Podstawową operacją formułę skalowania automatycznego jest uzyskanie danych metryk zadania i zasobu, a następnie dopasowanie rozmiaru puli na podstawie tych danych. W związku z tym ważne jest, aby jasno zrozumieć, jak formuły skalowania automatycznego współdziałają z danymi metryk (przykładami).

**Samples**

Usługa Batch okresowo pobiera próbki metryk zadań i zasobów oraz udostępnia je dla formuł skalowania automatycznego. Te próbki są rejestrowane co 30 sekund przez usługę Batch. Jednak zazwyczaj występuje opóźnienie między zapisaniem tych próbek a udostępnieniem ich dla (i może być odczytywane przez) formuł skalowania automatycznego. Ponadto ze względu na różne czynniki, takie jak sieć lub inne problemy związane z infrastrukturą, próbki nie mogą być rejestrowane w określonym interwale.

**Procent próbki**

Gdy `samplePercent` jest przenoszona do `GetSample()` metody lub wywoływana `GetSamplePercent()` jest metoda, _wartość procentowa_ odnosi się do porównania między całkowitą możliwą liczbą próbek, które są rejestrowane przez usługę Batch, oraz liczbą próbek dostępnych dla formuły skalowania automatycznego.

Przyjrzyjmy się na przykład do 10-minutowego przedziału czasu. Ponieważ próbki są rejestrowane co 30 sekund w ramach przedziału czasu, Maksymalna łączna liczba próbek, które są rejestrowane przez partię, wynosi 20 próbek (2 na minutę). Jednak ze względu na niewłaściwe opóźnienie mechanizmu raportowania i innych problemów w ramach platformy Azure może istnieć tylko 15 próbek dostępnych dla formuły skalowania automatycznego. Tak więc, na przykład w przypadku okresu 10-minutowego, w formule może być dostępna tylko 75% łącznej liczby prób.

**Getsample () i zakresy próbek**

Formuły skalowania automatycznego będą zwiększać i zmniejszać pule &mdash; , dodając węzły lub usuwając węzły. Ze względu na to, że węzły są kosztowne, należy się upewnić, że formuły korzystają z inteligentnej metody analizy, która jest oparta na wystarczających danych. W związku z tym zalecamy użycie analizy trendów typów w formułach. Ten typ powiększa i zmniejsza pule w oparciu o zakres zebranych próbek.

Aby to zrobić, użyj `GetSample(interval look-back start, interval look-back end)` funkcji do zwrócenia wektora próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Gdy powyższy wiersz jest oceniany przez partię, zwraca zakres próbek jako wektor wartości. Przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po zebraniu wektora próbek można użyć funkcji, takich jak `min()`, `max()`, i `avg()` do wygenerowania istotnych wartości z zebranych zakresów.

Aby zwiększyć bezpieczeństwo, można wymusić niepowodzenie oceny formuły, jeśli mniej niż określony procent próbkowania jest dostępny w określonym przedziale czasu. Jeśli wymusisz niepowodzenie oceny formuły, nakazujesz przeprowadzenie dalszej oceny formuły, jeśli określony procent próbek nie jest dostępny. W takim przypadku nie wprowadzono żadnych zmian w rozmiarze puli. Aby określić wymaganą wartość procentową próbek do pomyślnej oceny, określ ją jako trzeci parametr `GetSample()`. W tym miejscu określono wymóg 75% próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ponieważ w przypadku przykładowej dostępności może wystąpić opóźnienie, ważne jest, aby zawsze określać zakres czasu o godzinie rozpoczęcia wyszukiwania starszej niż jedna minuta. Przekazanie próbek przez system trwa około minutę, więc próbki w zakresie `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogą być niedostępne. Ponownie można użyć parametru procentu, `GetSample()` aby wymusić określone przykładowe wymagania procentowe.

> [!IMPORTANT]
> **Zdecydowanie zalecamy** , aby **uniknąć używania *tylko* `GetSample(1)` w formułach skalowania automatycznego**. Wynika to z `GetSample(1)` faktu, że jest to ważne w przypadku usługi Batch, "Przekaż mi ostatni przykład, niezależnie od tego, jak długo zostało ono pobrane". Ponieważ jest tylko pojedynczym przykładem i może być starszym przykładem, może nie być reprezentatywny dla większego obrazu ostatniego stanu zadania lub zasobu. Jeśli używasz `GetSample(1)`, upewnij się, że jest częścią większej instrukcji, a nie tylko punktem danych, na którym bazuje formuła.
>
>

## <a name="metrics"></a>Metryki

Podczas definiowania formuły można użyć metryk zasobów i zadań. Dostosowana jest docelowa liczba węzłów dedykowanych w puli na podstawie zbieranych i obliczanych danych metryk. Aby uzyskać więcej informacji na temat każdej metryki, zobacz sekcję [zmienne](#variables) powyżej.

<table>
  <tr>
    <th>Metryka</th>
    <th>Opis</th>
  </tr>
  <tr>
    <td><b>Zasób</b></td>
    <td><p>Metryki zasobów bazują na PROCESORAch, przepustowości, użyciu pamięci węzłów obliczeniowych i liczbie węzłów.</p>
        <p> Te zmienne zdefiniowane przez usługę są przydatne do wprowadzania zmian na podstawie liczby węzłów:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Te zmienne zdefiniowane przez usługę są przydatne do wprowadzania zmian opartych na użyciu zasobów węzła:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Zadaniem</b></td>
    <td><p>Metryki zadań są oparte na stanie zadań, takich jak aktywne, oczekujące i ukończone. Następujące zmienne zdefiniowane przez usługę są przydatne do wprowadzania zmian rozmiaru puli na podstawie metryk zadania:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Napisz formułę skalowania automatycznego

Tworzysz formułę skalowania automatycznego przez utworzenie instrukcji wykorzystujących powyższe składniki, a następnie połącz te instrukcje w kompletnej formule. W tej sekcji utworzysz Przykładową formułę skalowania automatycznego, która może wykonywać pewne rzeczywiste decyzje dotyczące skalowania.

Najpierw zdefiniujemy wymagania dla nowej formuły automatycznego skalowania. Formuła powinna:

1. Zwiększ wartość docelową dedykowanych węzłów obliczeniowych w puli, jeśli użycie procesora CPU jest wysokie.
1. Zmniejsz liczbę elementów docelowych dedykowanych węzłów obliczeniowych w puli, gdy użycie procesora CPU jest niskie.
1. Zawsze Ogranicz maksymalną liczbę dedykowanych węzłów do 400.
1. Podczas zmniejszania liczby węzłów nie usuwaj węzłów, na których działają zadania; w razie potrzeby Zaczekaj na zakończenie zadań usuwania węzłów.

Aby zwiększyć liczbę węzłów podczas wysokiego użycia procesora CPU, zdefiniuj instrukcję wypełniającą zmienną zdefiniowaną przez użytkownika (`$totalDedicatedNodes`) o wartości 110 procent bieżącej docelowej liczby węzłów dedykowanych, ale tylko wtedy, gdy minimalne średnie użycie procesora CPU w ciągu ostatnich 10 minut było większe niż 70 procent. W przeciwnym razie użyj wartości dla bieżącej liczby dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Aby *zmniejszyć* liczbę węzłów dedykowanych podczas niskiego użycia procesora CPU, następna instrukcja w naszej formule ustawia tę `$totalDedicatedNodes` samą zmienną na 90% bieżącej docelowej liczby węzłów dedykowanych, jeśli średnie użycie procesora CPU w ostatnich 60 minutach było poniżej 20 procent. W przeciwnym razie Użyj bieżącej wartości, `$totalDedicatedNodes` która została wypełniona w powyższej instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz Ogranicz docelową liczbę dedykowanych węzłów obliczeniowych do maksymalnie 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Oto kompletna formuła:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Tworzenie puli z włączoną funkcją automatycznego skalowania przy użyciu zestawów SDK partii

Automatyczne skalowanie puli można skonfigurować przy użyciu dowolnego zestawu [SDK usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [poleceń cmdlet programu PowerShell](batch-powershell-cmdlets-get-started.md)dla usługi [Batch REST](https://docs.microsoft.com/rest/api/batchservice/) i interfejsu [wiersza polecenia usługi Batch](batch-cli-get-started.md). W tej sekcji można zobaczyć przykłady dla platformy .NET i języka Python.

### <a name="net"></a>.NET

Aby utworzyć pulę z włączonym skalowaniem automatycznym w programie .NET, wykonaj następujące kroki:

1. Utwórz pulę przy użyciu [BatchClient. PoolOperations. ispool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Dla `true`właściwości [CloudPool. AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) ustaw wartość.
1. Ustaw właściwość [CloudPool. AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) na formułę skalowania automatycznego.
1. Obowiązkowe Ustaw właściwość [CloudPool. AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (wartość domyślna to 15 minut).
1. Zatwierdź pulę z [CloudPool. Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Poniższy fragment kodu tworzy pulę z włączoną funkcją automatycznego skalowania w programie .NET. Formuła skalowania automatycznego puli ustawia liczbę docelową dedykowanych węzłów na 5 w poniedziałek i 1 na każdy inny dzień tygodnia. [Interwał skalowania automatycznego](#automatic-scaling-interval) jest ustawiany na 30 minut. W tym i innych fragmentach kodu C# w tym artykule `myBatchClient` jest prawidłowo zainicjowany wystąpienie klasy [BatchClient][net_batchclient] .

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Podczas tworzenia puli z włączoną funkcją automatycznego skalowania nie należy określać parametru _targetDedicatedNodes_ ani parametru _targetLowPriorityNodes_ w wywołaniu funkcji **ispool**. Zamiast tego należy określić właściwości **AutoScaleEnabled** i **AutoScaleFormula** w puli. Wartości tych właściwości określają docelowy numer każdego typu węzła. Ponadto, aby ręcznie zmienić rozmiar puli z włączoną funkcją automatycznego skalowania (na przykład z [BatchClient. PoolOperations. ResizePoolAsync][net_poolops_resizepoolasync]), najpierw **Wyłącz** automatyczne skalowanie w puli, a następnie zmień jego rozmiar.
>
>

#### <a name="automatic-scaling-interval"></a>Interwał automatycznego skalowania

Domyślnie usługa Batch dostosowuje rozmiar puli zgodnie z formułą automatycznego skalowania co 15 minut. Ten interwał można skonfigurować przy użyciu następujących właściwości puli:

* [CloudPool. AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (interfejs API REST)

Minimalny interwał wynosi pięć minut, a wartość maksymalna to 168 godzin. Jeśli określono interwał poza tym zakresem, usługa Batch zwraca błąd nieprawidłowego żądania (400).

> [!NOTE]
> Skalowanie automatyczne nie jest obecnie przeznaczone do reagowania na zmiany w czasie krótszym niż minutę, ale zamiast tego jest przeznaczone do stopniowego dostosowywania rozmiaru puli podczas uruchamiania obciążenia.
>
>

### <a name="python"></a>Python

Podobnie można utworzyć pulę z obsługą skalowania automatycznego za pomocą zestawu SDK języka Python, wykonując następujące polecenie:

1. Utwórz pulę i określ jej konfigurację.
1. Dodaj pulę do klienta usługi.
1. Włącz automatyczne skalowanie w puli przy użyciu zapisanej formuły.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Więcej przykładów użycia zestawu SDK języka Python można znaleźć w [repozytorium przewodnika Szybki Start](https://github.com/Azure-Samples/batch-python-quickstart) dla usługi Batch w witrynie GitHub.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włącz Skalowanie automatyczne w istniejącej puli

Każdy zestaw SDK w usłudze Batch umożliwia włączenie skalowania automatycznego. Przykład:

* [BatchClient. PoolOperations. EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Włącz automatyczne skalowanie w puli][rest_enableautoscale] (interfejs API REST)

Po włączeniu skalowania automatycznego w istniejącej puli należy pamiętać o następujących kwestiach:

* Jeśli automatyczne skalowanie jest obecnie wyłączone w puli po wydaniu żądania włączenia skalowania automatycznego, należy określić prawidłową formułę automatycznego skalowania po wydaniu żądania. Opcjonalnie można określić interwał oceny skalowania automatycznego. Jeśli nie określisz interwału, zostanie użyta wartość domyślna wynosząca 15 minut.
* Jeśli automatyczne skalowanie jest obecnie włączone w puli, można określić formułę skalowania automatycznego, interwał oceny lub oba te elementy. Należy określić co najmniej jedną z tych właściwości.

  * Jeśli określisz nowy interwał oceny automatycznego skalowania, istniejący harmonogram szacowania zostanie zatrzymany i zostanie uruchomiony nowy harmonogram. Godzina rozpoczęcia nowego harmonogramu to godzina, o której wydano żądanie włączenia skalowania automatycznego.
  * W przypadku pominięcia formuły automatycznego skalowania lub interwału oceny usługa Batch będzie używać bieżącej wartości tego ustawienia.

> [!NOTE]
> Jeśli określono wartości parametrów *targetDedicatedNodes* lub *targetLowPriorityNodes* **metody tworzenia puli w** programie .NET lub dla porównywalnych parametrów w innym języku, te wartości są ignorowane podczas obliczania formuły skalowania automatycznego.
>
>

Ten fragment kodu w języku C# używa biblioteki [Batch .NET][net_api] do włączenia skalowania automatycznego w istniejącej puli:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizowanie formuły skalowania automatycznego

Aby zaktualizować formułę w istniejącej puli z włączoną funkcją automatycznego skalowania, wywołaj operację, aby ponownie włączyć automatyczne skalowanie przy użyciu nowej formuły. Na przykład jeśli automatyczne skalowanie jest już włączone `myexistingpool` podczas wykonywania poniższego kodu platformy .NET, jego formuła automatycznego skalowania jest zastępowana zawartością. `myNewFormula`

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizowanie interwału automatycznego skalowania

Aby zaktualizować interwał oceny automatycznego skalowania istniejącej puli z włączoną funkcją automatycznego skalowania, należy wywołać operację, aby ponownie włączyć skalowanie automatyczne przy użyciu nowego interwału. Na przykład, aby ustawić interwał oceny automatycznego skalowania do 60 minut dla puli, która jest już automatycznie skalowana w programie .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Oceń formułę skalowania automatycznego

Można oszacować formułę przed zastosowaniem jej do puli. W ten sposób można przetestować formułę, aby zobaczyć, jak jej instrukcje są oceniane przed umieszczeniem formuły w środowisku produkcyjnym.

Aby oszacować formułę skalowania automatycznego, należy najpierw włączyć skalowanie automatyczne w puli przy użyciu prawidłowej formuły. Aby przetestować formułę w puli, która nie ma jeszcze włączonego skalowania automatycznego, należy użyć jednowierszowej `$TargetDedicatedNodes = 0` formuły przy pierwszym włączeniu skalowania automatycznego. Następnie użyj jednej z następujących wartości, aby oszacować formułę, którą chcesz przetestować:

* [BatchClient. PoolOperations. EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) lub [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody wsadowe .NET wymagają identyfikatora istniejącej puli i ciągu zawierającego formułę skalowania automatycznego do obliczenia.

* [Oceń formułę skalowania automatycznego](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądaniu interfejsu API REST Określ Identyfikator puli w identyfikatorze URI i formułę skalowania automatycznego w elemencie *autoScaleFormula* treści żądania. Odpowiedź operacji zawiera wszystkie informacje o błędzie, które mogą być powiązane z formułą.

W tym fragmencie kodu dla [platformy .NET partię][net_api] obliczamy formułę skalowania automatycznego. Jeśli w puli nie włączono automatycznego skalowania, zostanie ona najpierw włączona.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Pomyślne oszacowanie formuły pokazanej w tym fragmencie kodu daje wyniki podobne do:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Pobierz informacje o uruchomieniach automatycznego skalowania

Aby upewnić się, że formuła działa zgodnie z oczekiwaniami, zalecamy okresowe sprawdzanie wyników przebiegów skalowania automatycznego, które wykonuje partia na tej puli. Aby to zrobić, Pobierz (lub Odśwież) odwołanie do puli i przejrzyj właściwości ostatniego przebiegu automatycznego skalowania.

W usłudze Batch .NET Właściwość [CloudPool. AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) ma kilka właściwości, które zawierają informacje o najnowszym przebiegu skalowania automatycznego wykonanego w puli:

* [AutoScaleRun. timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun. results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun. Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST informacje o żądaniu [pobrania](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) puli zwracają informacje o puli, które obejmują najnowsze automatyczne skalowanie informacji o uruchomieniu we właściwości [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) .

Poniższy fragment kodu w języku C# używa biblioteki Batch .NET do drukowania informacji na temat ostatniego uruchomienia skalowania automatycznego w puli moja _Pula_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Przykładowe dane wyjściowe poprzedniego fragmentu kodu:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Przykładowe formuły skalowania automatycznego

Przyjrzyjmy się kilku formułom, które pokazują różne sposoby dostosowywania ilości zasobów obliczeniowych w puli.

### <a name="example-1-time-based-adjustment"></a>Przykład 1: korekta oparta na czasie

Załóżmy, że chcesz dostosować rozmiar puli w oparciu o dzień tygodnia i godzinę. Ten przykład pokazuje, jak zwiększyć lub zmniejszyć liczbę węzłów w puli odpowiednio do siebie.

Formuła najpierw uzyskuje bieżącą godzinę. Jeśli jest to dzień tygodnia (1-5) i w godzinach roboczych (od 8 do 6 PM), rozmiar puli docelowej jest ustawiany na 20 węzłów. W przeciwnym razie jest ustawiony na 10 węzłów.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`można dostosować w celu odzwierciedlenia lokalnej strefy czasowej przez `time()` dodanie do produktu `TimeZoneInterval_Hour` i przesunięcia czasu UTC. Na przykład użyj `$curTime = time() + (-6 * TimeInterval_Hour);` terminu górski czas letni (zestawu MDT). Należy pamiętać, że przesunięcie powinno być dostosowane na początku i na końcu czasu letniego (jeśli dotyczy).

### <a name="example-2-task-based-adjustment"></a>Przykład 2: korekta oparta na zadaniach

W tym przykładzie rozmiar puli jest dostosowywany na podstawie liczby zadań w kolejce. Komentarze i podziały wierszy są akceptowalne w ciągach formuły.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Przykład 3: ewidencjonowanie aktywności zadań równoległych

Ten przykład dostosowuje rozmiar puli na podstawie liczby zadań. Ta formuła uwzględnia również wartość [MaxTasksPerComputeNode][net_maxtasks] , która została ustawiona dla puli. Takie podejście jest przydatne w sytuacjach, w których włączono [równoległe wykonywanie zadań](batch-parallel-node-tasks.md) w puli.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Przykład 4: Ustawianie rozmiaru puli początkowej

W tym przykładzie przedstawiono fragment kodu C# z formułą skalowania automatycznego, która ustawia rozmiar puli na określoną liczbę węzłów w początkowym okresie. Następnie dostosowuje rozmiar puli na podstawie liczby uruchomionych i aktywnych zadań po upływie czasu początkowego.

Formuła w poniższym fragmencie kodu:

* Ustawia rozmiar puli początkowej na cztery węzły.
* Nie dopasowuje rozmiaru puli w ciągu pierwszych 10 minut cyklu życia puli.
* Po upływie 10 minut uzyskuje maksymalną wartość liczby uruchomionych i aktywnych zadań w ciągu ostatnich 60 minut.
  * Jeśli obie wartości są równe 0 (wskazuje, że żadne zadania nie były uruchomione lub aktywne w ciągu ostatnich 60 minut), rozmiar puli jest ustawiony na 0.
  * Jeśli jedna z wartości jest większa od zera, nie zostanie wprowadzona żadna zmiana.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Następne kroki

* [Maksymalizuj Azure Batch użycie zasobów obliczeniowych za pomocą współbieżnych zadań węzłów](batch-parallel-node-tasks.md) zawiera szczegółowe informacje na temat sposobu wykonywania wielu zadań jednocześnie w węzłach obliczeniowych w puli. Oprócz skalowania automatycznego ta funkcja może pomóc w obniżeniu czasu trwania zadań w przypadku niektórych obciążeń, co pozwala zaoszczędzić pieniądze.
* W przypadku innego detonatora wydajności upewnij się, że aplikacja usługi Batch wyśle do niej zapytanie w najbardziej optymalny sposób. Zobacz [wydajną pracę usługi Azure Batch,](batch-efficient-list-queries.md) aby dowiedzieć się, jak ograniczyć ilość danych przekreślonych w czasie wykonywania zapytania o stan potencjalnie tysięcy węzłów obliczeniowych lub zadań.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
