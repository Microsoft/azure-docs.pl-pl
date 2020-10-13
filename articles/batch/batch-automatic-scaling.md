---
title: Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch
description: Włącz automatyczne skalowanie w puli w chmurze, aby dynamicznie dostosować liczbę węzłów obliczeniowych w puli.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 5774acbfc035ab61267dddb31b01b0e82689f690
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849796"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Utwórz automatyczną formułę skalowania węzłów obliczeniowych w puli usługi Batch

Azure Batch może automatycznie skalować pule w oparciu o zdefiniowane przez siebie parametry, co pozwala zaoszczędzić czas i pieniądze. W przypadku automatycznego skalowania przetwarzanie wsadowe dynamicznie dodaje węzły do puli w miarę wzrostu wymagań zadań i usuwa węzły obliczeniowe w miarę zmniejszania się potrzeb zadań.

Aby włączyć automatyczne skalowanie w puli węzłów obliczeniowych, należy skojarzyć pulę z *formułą skalowania* automatycznego, która została zdefiniowana. Usługa Batch używa formuły skalowania automatycznego, aby określić, ile węzłów jest potrzebnych do wykonania obciążenia. Mogą to być węzły dedykowane lub [węzły o niskim priorytecie](batch-low-pri-vms.md). Następnie usługa Batch będzie okresowo przeglądać dane metryk usługi i używać jej do dostosowywania liczby węzłów w puli na podstawie formuły i zdefiniowanego interwału.

Skalowanie automatyczne można włączyć podczas tworzenia puli lub zastosować je do istniejącej puli. Funkcja Batch umożliwia ocenę formuł przed przypisaniem ich do pul i monitorowanie stanu automatycznych uruchomień skalowania. Po skonfigurowaniu puli ze skalowaniem automatycznym można wprowadzić zmiany w formule później.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Batch można określić [tryb alokacji puli](accounts.md), który określa, czy pule są przydzielane w ramach subskrypcji usługi Batch (wartość domyślna) czy w ramach subskrypcji użytkownika. Jeśli konto usługi Batch zostało utworzone z domyślną konfiguracją usług Batch, konto jest ograniczone do maksymalnej liczby rdzeni, które mogą być używane do przetwarzania. Usługa Batch skaluje węzły obliczeniowe tylko do tego limitu podstawowego. Z tego powodu usługa Batch może nie dotrzeć do docelowej liczby węzłów obliczeniowych określonych przez formułę skalowania automatycznego. Zobacz [przydziały i limity dla usługi Azure Batch,](batch-quota-limit.md) Aby uzyskać informacje na temat przeglądania i zwiększania limitów przydziału konta.
>
>Jeśli konto zostało utworzone przy użyciu trybu subskrypcji użytkownika, Twoje konto ma udział w podstawowym limicie przydziału dla subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Automatyczne skalowanie formuł

Formuła skalowania automatycznego jest wartością ciągu, która została zdefiniowana, która zawiera jedną lub więcej instrukcji. Formuła skalowania automatycznego jest przypisana do elementu [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) puli (Batch REST) lub [CloudPool. autoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). Usługa Batch używa formuły do określenia docelowej liczby węzłów obliczeniowych w puli dla następnego interwału przetwarzania. Ciąg formuły nie może przekroczyć 8 KB, może zawierać do 100 instrukcji, które są rozdzielone średnikami i mogą zawierać podziały wierszy i komentarze.

Można myśleć o automatycznym skalowaniu formuł jako języka wsadowego skalowania automatycznego. Instrukcje formuł są niesformatowanymi wyrażeniami, które mogą zawierać zarówno zmienne zdefiniowane przez usługę, jak i zmienne zdefiniowane przez użytkownika. Formuły mogą wykonywać różne operacje na tych wartościach przy użyciu wbudowanych typów, operatorów i funkcji. Na przykład instrukcja może mieć następującą postać:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zwykle zawierają wiele instrukcji, które wykonują operacje na wartościach, które są uzyskiwane w poprzednich instrukcjach. Na przykład najpierw uzyskamy wartość dla `variable1` , a następnie przekazujemy ją do funkcji, aby wypełnić `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Uwzględnij te instrukcje w formule skalowania automatycznego, aby dotrzeć do docelowej liczby węzłów obliczeniowych. Węzły dedykowane i węzły o niskim priorytecie mają własne ustawienia docelowe. Formuła automatycznego skalowania może zawierać wartość docelową dla węzłów dedykowanych, wartość docelową dla węzłów o niskim priorytecie lub obie te metody.

Docelowa liczba węzłów może być wyższa, niższa lub taka sama jak bieżąca liczba węzłów tego typu w puli. Funkcja Batch szacuje formułę skalowania automatycznego puli w określonych [odstępach czasu automatycznej skalowania](#automatic-scaling-interval). Sekwencja wsadowa dostosowuje liczbę docelową każdego typu węzła w puli do liczby, która jest określana przez formułę skalowania automatycznego w czasie szacowania.

### <a name="sample-autoscale-formulas"></a>Przykładowe formuły automatycznego skalowania

Poniżej przedstawiono przykłady dwóch formuł automatycznego skalowania, które można dostosować do pracy w większości scenariuszy. Zmienne `startingNumberOfVMs` i `maxNumberofVMs` w przykładowych formułach można dostosować do własnych potrzeb.

#### <a name="pending-tasks"></a>Oczekujące zadania

W przypadku tej formuły automatycznego skalowania Pula jest początkowo tworzona z jedną maszyną wirtualną. `$PendingTasks`Metryka definiuje liczbę zadań, które są uruchomione lub umieszczone w kolejce. Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i `$TargetDedicatedNodes` odpowiednio ustawia zmienną. Formuła zapewnia, że docelowa liczba węzłów dedykowanych nigdy nie przekracza 25 maszyn wirtualnych. W miarę przesyłania nowych zadań Pula automatycznie rośnie. Po zakończeniu zadań maszyny wirtualne stają się wolne, a formuła skalowania automatycznego zmniejsza pulę.

Ta formuła skaluje dedykowane węzły, ale można ją zmodyfikować, aby zastosować ją również do skalowania węzłów o niskim priorytecie.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Zastępujące węzły

Ten przykład tworzy pulę, która rozpoczyna się od 25 węzłów o niskim priorytecie. Za każdym razem, gdy węzeł o niskim priorytecie jest zastępowany, zostanie on zastąpiony przez dedykowany węzeł. Podobnie jak w przypadku pierwszego przykładu `maxNumberofVMs` zmienna zapobiega przekraczaniu 25 maszyn wirtualnych. Ten przykład jest przydatny do korzystania z maszyn wirtualnych o niskim priorytecie, a także w przypadku, gdy tylko stała liczba przeniesień będzie miała miejsce w okresie istnienia puli.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Dowiesz się więcej [na temat tworzenia formuł automatycznego skalowania](#write-an-autoscale-formula) i zobacz dodatkowe [przykładowe formuły automatycznego skalowania](#example-autoscale-formulas) w dalszej części tego tematu.

## <a name="variables"></a>Zmienne

W formułach skalowania automatycznego można używać zarówno zmiennych **zdefiniowanych przez usługę** , jak i **zdefiniowanych przez użytkownika** .

Zmienne zdefiniowane przez usługę są wbudowane w usługę Batch. Niektóre zmienne zdefiniowane przez usługę są do odczytu i zapisu, a niektóre z nich są tylko do odczytu.

Zmienne zdefiniowane przez użytkownika to zmienne, które definiujesz. W przykładowej formule pokazane powyżej `$TargetDedicatedNodes` i `$PendingTasks` są zmiennymi zdefiniowanymi przez usługę, a `startingNumberOfVMs` i `maxNumberofVMs` są zmiennymi zdefiniowanymi przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługę zawsze poprzedzają znak dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalny.

W poniższych tabelach przedstawiono zmienne do odczytu i zapisu, które są zdefiniowane przez usługę Batch.

### <a name="read-write-service-defined-variables"></a>Zmienne zdefiniowane przez usługę do odczytu i zapisu

Można pobrać i ustawić wartości tych zmiennych zdefiniowanych przez usługę w celu zarządzania liczbą węzłów obliczeniowych w puli.

| Zmienna | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowa liczba dedykowanych węzłów obliczeniowych w puli. Ta wartość jest określana jako obiekt docelowy, ponieważ pula może nie zawsze osiągnąć żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów dedykowanych jest modyfikowana przez ocenę automatycznego skalowania, zanim pula osiągnie początkowy cel, Pula może nie dotrzeć do obiektu docelowego. <br /><br /> Pula na koncie utworzonym w trybie usługi wsadowej nie może osiągnąć swojego celu, jeśli element docelowy przekracza węzeł konta usługi Batch lub przydział podstawowy. Pula na koncie utworzonym w trybie subskrypcji użytkownika może nie osiągnąć swojego celu, jeśli wartość docelowa przekroczy współużytkowany przydział rdzeni dla subskrypcji.|
| $TargetLowPriorityNodes |Docelowa liczba węzłów obliczeniowych o niskim priorytecie dla puli. Określono jako element docelowy, ponieważ pula nie zawsze osiągnie żądaną liczbę węzłów. Na przykład jeśli docelowa liczba węzłów o niskim priorytecie jest modyfikowana przez ocenę automatycznego skalowania, zanim pula osiągnie początkowy cel, Pula może nie dotrzeć do obiektu docelowego. Pula może być również nieosiągalna, jeśli element docelowy przekracza węzeł konta wsadowego lub przydział podstawowy. <br /><br /> Aby uzyskać więcej informacji na temat węzłów obliczeniowych o niskim priorytecie, zobacz [Korzystanie z maszyn wirtualnych o niskim priorytecie w usłudze Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja wykonywana po usunięciu węzłów obliczeniowych z puli. Możliwe wartości:<ul><li>**przekolejka**: wartość domyślna. Natychmiast zamyka zadania i umieszcza je w kolejce zadań, aby zostały ponownie zaplanowane. Ta akcja zapewnia, że docelowa liczba węzłów zostanie osiągnięta tak szybko, jak to możliwe. Jednak może być mniej wydajny, ponieważ wszystkie uruchomione zadania zostaną przerwane i będą musiały zostać całkowicie ponownie uruchomione. <li>**Przerwij**: kończy wykonywanie zadań natychmiast i usuwa je z kolejki zadań.<li>**taskcompletion**: czeka na zakończenie działania aktualnie wykonywanych zadań, a następnie usuwa węzeł z puli. Użyj tej opcji, aby uniknąć przerywania i ponownie kolejkowania zadań w trakcie wykonywania zadań.<li>**retaineddata**: czeka, aż wszystkie lokalne dane przechowywane w węźle zostaną oczyszczone przed usunięciem węzła z puli.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes`Zmienną można również określić przy użyciu aliasu `$TargetDedicated` . Podobnie `$TargetLowPriorityNodes` zmienna może być określona za pomocą aliasu `$TargetLowPriority` . Jeśli zarówno w pełni nazwana zmienna, jak i jej alias są ustawiane przez formułę, pierwszeństwo ma wartość przypisana do pełnej nazwanej zmiennej.

### <a name="read-only-service-defined-variables"></a>Zmienne zdefiniowane przez usługę tylko do odczytu

Możesz uzyskać wartość tych zmiennych zdefiniowanych przez usługę, aby wprowadzić zmiany, które są oparte na metrykach usługi Batch.

> [!IMPORTANT]
> Zadania zwolnienia zadań nie są obecnie uwzględniane w zmiennych, które dostarczają liczby zadań, takich jak $ActiveTasks i $PendingTasks. W zależności od formuły automatycznego skalowania może to spowodować usunięcie węzłów bez węzłów dostępnych do uruchamiania zadań zwolnienia zadań.

| Zmienna | Opis |
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
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale jeszcze nie są wykonywane. Dotyczy to wszystkich zadań, które są w stanie aktywnym, których zależności zostały spełnione. Wszystkie zadania, które znajdują się w stanie aktywnym, ale których zależności nie zostały spełnione, są wykluczone z liczby $ActiveTasks. W przypadku zadania o wielu wystąpieniach $ActiveTasks będzie zawierać liczbę wystąpień ustawionych dla zadania.|
| $RunningTasks |Liczba zadań w stanie uruchomienia. |
| $PendingTasks |Suma $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyły się pomyślnie. |
| $FailedTasks |Liczba zadań, które zakończyły się niepowodzeniem. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzłów obliczeniowych. |
| $CurrentLowPriorityNodes |Bieżąca liczba węzłów obliczeniowych o niskim priorytecie, w tym wszystkich węzłów, które zostały przeniesiona. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie przeniesiona. |

> [!TIP]
> Te zmienne zdefiniowane przez usługę tylko do odczytu są *obiektami* , które udostępniają różne metody uzyskiwania dostępu do danych skojarzonych z każdym z nich. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie przykładowych danych](#obtain-sample-data) w dalszej części tego artykułu.

## <a name="types"></a>Types

Formuły skalowania automatycznego obsługują następujące typy:

- double
- doubleVec
- doubleVecList
- ciąg
- timestamp — struktura złożona, która zawiera następujące elementy członkowskie:
  - rok
  - miesiąc (1-12)
  - dzień (1-31)
  - dzień tygodnia (w formacie liczbowym, na przykład 1 dla poniedziałku)
  - godzina (w formacie 24-godzinnym, na przykład 13 oznacza 1 PM)
  - minuta (00-59)
  - sekunda (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

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
| *operator* Double |-, ! |double |
| TimeInterval *operatora* |- |timeinterval |
| podwójny *operator* Double |<, <=, = =, >=, >,! = |double |
| ciąg *operatora* ciągu |<, <=, = =, >=, >,! = |double |
| Sygnatura czasowa *operatora* timestamp |<, <=, = =, >=, >,! = |double |
| *operator* TimeInterval TimeInterval |<, <=, = =, >=, >,! = |double |
| podwójny *operator* Double |&&,  &#124;&#124; |double |

Podczas testowania podwójnego przy użyciu operatora Trzyelementowy ( `double ? statement1 : statement2` ) niezerowego ma **wartość true**, a zero ma **wartość false**.

## <a name="functions"></a>Funkcje

Możesz użyć tych wstępnie zdefiniowanych **funkcji** podczas definiowania formuły skalowania automatycznego.

| Funkcja | Typ zwracany | Opis |
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
| Time (ciąg dateTime = "") |sygnatura czasowa |Zwraca sygnaturę czasową bieżącego czasu, jeśli nie są spełnione żadne parametry lub sygnaturę czasową ciągu dateTime, jeśli jest zakończony. Obsługiwane formaty dateTime to W3C-DTF i RFC 1123. |
| Val (doubleVec v, podwójne i) |double |Zwraca wartość elementu, który znajduje się w lokalizacji i w wektorze v, z indeksem początkowym równym zero. |

Niektóre funkcje, które są opisane w poprzedniej tabeli, mogą akceptować listę jako argument. Rozdzielana przecinkami lista jest dowolną kombinacją wartości *Double* i *doubleVec*. Na przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Wartość *doubleVecList* jest konwertowana na jedną *doubleVec* przed oceną. Na przykład, jeśli `v = [1,2,3]` , wywołanie `avg(v)` jest równoważne wywołaniu `avg(1,2,3)` . Wywołanie `avg(v, 7)` jest równoważne wywołaniu `avg(1,2,3,7)` .

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
    <td><b>Zadanie</b></td>
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

## <a name="obtain-sample-data"></a>Uzyskiwanie przykładowych danych

Podstawową operacją formułę skalowania automatycznego jest uzyskanie danych metryk zadania i zasobu (próbek), a następnie dopasowanie rozmiaru puli na podstawie tych danych. W związku z tym ważne jest, aby jasno zrozumieć, jak formuły skalowania automatycznego współdziałają z przykładami.

### <a name="methods"></a>Metody

Formuły skalowania automatycznego działają na próbkach danych metryk udostępnianych przez usługę Batch. Formuła spowoduje zwiększenie lub zmniejszenie rozmiaru puli na podstawie wartości, które uzyskuje. Zmienne zdefiniowane przez usługę to obiekty, które zapewniają metody dostępu do danych, które są skojarzone z tym obiektem. Na przykład poniższe wyrażenie przedstawia żądanie pobrania ostatnich pięciu minut użycia procesora CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Poniższe metody mogą służyć do uzyskiwania przykładowych danych dotyczących zmiennych zdefiniowanych przez usługę.

| Metoda | Opis |
| --- | --- |
| Getsample () |`GetSample()`Metoda zwraca wektor próbek danych.<br/><br/>Przykładem jest 30-sekundowa ilość danych metryk. Innymi słowy, próbki są uzyskiwane co 30 sekund. Jak pokazano poniżej, występuje opóźnienie między pobieraniem próbki a udostępnieniem jej w formule. W związku z tym nie wszystkie próbki dla danego okresu mogą być dostępne do oceny przez formułę.<ul><li>`doubleVec GetSample(double count)`: Określa liczbę próbek do pobrania z najnowszych próbek, które zostały zebrane. `GetSample(1)` Zwraca ostatni dostępny przykład. W przypadku metryk takich jak `$CPUPercent` `GetSample(1)` nie należy używać, ponieważ nie można wiedzieć, *kiedy* przykład został zebrany. Może to być Najnowsza wersja, lub z powodu problemów z systemem, może być ona znacznie starsza. W takich przypadkach lepiej jest używać przedziału czasowego, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Określa przedział czasu na potrzeby zbierania przykładowych danych. Opcjonalnie określa również procent próbek, które muszą być dostępne w żądanym przedziale czasowym. Przykładowo `$CPUPercent.GetSample(TimeInterval_Minute * 10)` zwróci 20 próbek, jeśli wszystkie próbki w ciągu ostatnich 10 minut są obecne w `CPUPercent` historii. Jeśli Ostatnia minuta historii była niedostępna, zwracane są tylko 18 próbek. W takim przypadku `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` może się nie powieść, ponieważ dostępne są tylko 90 procent próbek, ale to `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` się powiedzie.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Określa ramy czasowe do zbierania danych, zarówno czas rozpoczęcia, jak i godzinę zakończenia. Jak wspomniano powyżej, występuje opóźnienie między pobieraniem próbki a udostępnieniem jej w formule. Należy wziąć pod uwagę to opóźnienie przy użyciu `GetSample` metody. Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres próbkowania, które zostały wykonane w historycznym zestawie danych przykładowych. |
| Count () |Zwraca łączną liczbę próbek w historii metryk. |
| HistoryBeginTime() |Zwraca sygnaturę czasową najstarszego dostępnego przykładu danych dla metryki. |
| GetSamplePercent() |Zwraca wartość procentową próbek, które są dostępne dla danego interwału czasu. Na przykład `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Ponieważ `GetSample` Metoda kończy się niepowodzeniem, jeśli wartość procentowa zwracanych próbek jest mniejsza niż `samplePercent` określona, można użyć `GetSamplePercent` metody do sprawdzenia w pierwszej kolejności. Następnie można wykonać alternatywną akcję, jeśli istnieją niewystarczające próbki, bez zatrzymania automatycznej oceny skalowania. |

### <a name="samples"></a>Samples

Usługa Batch okresowo pobiera próbki metryk zadań i zasobów oraz udostępnia je dla formuł skalowania automatycznego. Te próbki są rejestrowane co 30 sekund przez usługę Batch. Jednak zazwyczaj występuje opóźnienie między zapisaniem tych próbek a udostępnieniem ich dla (i może być odczytywane przez) formuł skalowania automatycznego. Ponadto próbki nie mogą być rejestrowane w określonym interwale z powodu czynników takich jak sieć lub inne problemy z infrastrukturą.

### <a name="sample-percentage"></a>Procent próbki

Gdy `samplePercent` jest przenoszona do `GetSample()` metody lub `GetSamplePercent()` wywoływana jest metoda, _wartość procentowa_ odnosi się do porównania między całkowitą możliwą liczbą próbek, które są rejestrowane przez usługę Batch, oraz liczbą próbek dostępnych dla formuły skalowania automatycznego.

Przyjrzyjmy się na przykład do 10-minutowego przedziału czasu. Ponieważ próbki są rejestrowane co 30 sekund w tym 10-minutowym przedziale czasu, Maksymalna łączna liczba próbek rejestrowanych przez partię wynosi 20 próbek (2 na minutę). Jednak ze względu na niewłaściwe opóźnienie mechanizmu raportowania i innych problemów w ramach platformy Azure może istnieć tylko 15 próbek dostępnych dla formuły skalowania automatycznego. Tak więc, na przykład w przypadku okresu 10-minutowego, w formule może być dostępna tylko 75% łącznej liczby prób.

### <a name="getsample-and-sample-ranges"></a>Getsample () i zakresy próbek

Formuły skalowania automatycznego powiększają i zmniejszają pule przez dodawanie lub usuwanie węzłów. Ponieważ węzły są kosztowne, należy się upewnić, że formuły korzystają z inteligentnej metody analizy, która jest oparta na wystarczających danych. Zalecamy używanie analizy trendów typów w formułach. Ten typ powiększa i zmniejsza pule w oparciu o zakres zebranych próbek.

Aby to zrobić, użyj funkcji `GetSample(interval look-back start, interval look-back end)` do zwrócenia wektora próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Gdy powyższy wiersz jest oceniany przez partię, zwraca zakres próbek jako wektor wartości. Na przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po zebraniu wektora próbek można użyć funkcji, takich jak `min()` , `max()` , i `avg()` do wygenerowania istotnych wartości z zebranych zakresów.

Aby zwiększyć bezpieczeństwo, można wymusić niepowodzenie oceny formuły, jeśli mniej niż określony procent próbkowania jest dostępny w określonym przedziale czasu. Jeśli wymusisz niepowodzenie oceny formuły, nakazujesz przeprowadzenie dalszej oceny formuły, jeśli określony procent próbek nie jest dostępny. W takim przypadku nie wprowadzono żadnych zmian w rozmiarze puli. Aby określić wymaganą wartość procentową próbek do pomyślnej oceny, określ ją jako trzeci parametr `GetSample()` . W tym miejscu określono wymóg 75% próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ponieważ w przypadku przykładowej dostępności może wystąpić opóźnienie, należy zawsze określić zakres czasu, który jest wcześniejszy niż jedna minuta. Przekazanie próbek przez system trwa około minutę, więc próbki w zakresie `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` mogą być niedostępne. Ponownie można użyć parametru procentu, `GetSample()` Aby wymusić określone przykładowe wymagania procentowe.

> [!IMPORTANT]
> Zdecydowanie zalecamy, aby **uniknąć używania *tylko* `GetSample(1)` w formułach skalowania automatycznego**. Wynika to z faktu, że jest to ważne w przypadku `GetSample(1)` usługi Batch, "Przekaż mi ostatni przykład, niezależnie od tego, jak długo zostało ono pobrane". Ponieważ jest tylko pojedynczym przykładem i może być starszym przykładem, może nie być reprezentatywny dla większego obrazu ostatniego stanu zadania lub zasobu. Jeśli używasz `GetSample(1)` , upewnij się, że jest częścią większej instrukcji, a nie tylko punktem danych, na którym bazuje formuła.

## <a name="write-an-autoscale-formula"></a>Napisz formułę skalowania automatycznego

Tworzysz formułę skalowania automatycznego przez utworzenie instrukcji wykorzystujących powyższe składniki, a następnie połącz te instrukcje w kompletnej formule. W tej sekcji utworzysz Przykładową formułę skalowania automatycznego, która może wykonywać rzeczywiste decyzje dotyczące skalowania i wprowadzać zmiany.

Najpierw zdefiniujemy wymagania dla nowej formuły automatycznego skalowania. Formuła powinna:

- Zwiększ wartość docelową dedykowanych węzłów obliczeniowych w puli, jeśli użycie procesora CPU jest wysokie.
- Zmniejsz liczbę elementów docelowych dedykowanych węzłów obliczeniowych w puli, gdy użycie procesora CPU jest niskie.
- Zawsze Ogranicz maksymalną liczbę dedykowanych węzłów do 400.
- Podczas zmniejszania liczby węzłów nie usuwaj węzłów, na których działają zadania; w razie potrzeby poczekaj na zakończenie zadań przed usunięciem węzłów.

Pierwsza instrukcja w naszej formule spowoduje zwiększenie liczby węzłów podczas wysokiego użycia procesora CPU. Zdefiniujemy instrukcję, która wypełnia zmienną zdefiniowaną przez użytkownika ( `$totalDedicatedNodes` ) o wartości 110 procent bieżącej docelowej liczby węzłów dedykowanych, ale tylko wtedy, gdy minimalne użycie procesora CPU w ciągu ostatnich 10 minut było większe niż 70 procent. W przeciwnym razie używa wartości dla bieżącej liczby dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Aby zmniejszyć liczbę węzłów dedykowanych podczas niskiego użycia procesora CPU, następna instrukcja w naszej formule ustawia tę samą `$totalDedicatedNodes` zmienną na 90% bieżącej docelowej liczby węzłów dedykowanych, jeśli średnie użycie procesora CPU w ostatnich 60 minutach było poniżej 20 procent. W przeciwnym razie używa bieżącej wartości `$totalDedicatedNodes` , która została wypełniona w powyższej instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz ograniczą docelową liczbę dedykowanych węzłów obliczeniowych do maksymalnie 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Na koniec upewnimy się, że węzły nie zostaną usunięte do momentu zakończenia ich zadań.

```
$NodeDeallocationOption = taskcompletion;
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
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Jeśli wybierzesz opcję, możesz uwzględnić zarówno komentarze, jak i podziały wierszy w ciągach formuły.

## <a name="automatic-scaling-interval"></a>Interwał automatycznego skalowania

Domyślnie usługa Batch dostosowuje rozmiar puli zgodnie z formułą automatycznego skalowania co 15 minut. Ten interwał można skonfigurować przy użyciu następujących właściwości puli:

- [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (interfejs API REST)

Minimalny interwał wynosi pięć minut, a wartość maksymalna to 168 godzin. Jeśli określono interwał poza tym zakresem, usługa Batch zwraca błąd nieprawidłowego żądania (400).

> [!NOTE]
> Skalowanie automatyczne nie jest obecnie przeznaczone do reagowania na zmiany w czasie krótszym niż minutę, ale zamiast tego jest przeznaczone do stopniowego dostosowywania rozmiaru puli podczas uruchamiania obciążenia.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Tworzenie puli z włączoną funkcją automatycznego skalowania przy użyciu zestawów SDK partii

Automatyczne skalowanie puli można skonfigurować przy użyciu dowolnego zestawu [SDK usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [poleceń cmdlet programu PowerShell](batch-powershell-cmdlets-get-started.md)dla usługi [Batch REST](/rest/api/batchservice/) i interfejsu [wiersza polecenia usługi Batch](batch-cli-get-started.md). W tej sekcji można zobaczyć przykłady dla platformy .NET i języka Python.

### <a name="net"></a>.NET

Aby utworzyć pulę z włączonym skalowaniem automatycznym w programie .NET, wykonaj następujące kroki:

1. Utwórz pulę przy użyciu [BatchClient. PoolOperations. ispool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Dla właściwości [CloudPool. AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) ustaw wartość `true` .
1. Ustaw właściwość [CloudPool. AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) na formułę skalowania automatycznego.
1. Obowiązkowe Ustaw właściwość [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (wartość domyślna to 15 minut).
1. Zatwierdź pulę z [CloudPool. Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Poniższy przykład tworzy pulę z włączoną funkcją automatycznego skalowania na platformie .NET. Formuła skalowania automatycznego puli ustawia liczbę elementów docelowych dedykowanych węzłów na 5 w poniedziałek i do 1 w każdym innym dniu tygodnia. [Interwał skalowania automatycznego](#automatic-scaling-interval) jest ustawiany na 30 minut. W tym i innych fragmentach kodu C# w tym artykule `myBatchClient` jest prawidłowo zainicjowany wystąpienie klasy [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

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
> Podczas tworzenia puli z włączoną funkcją automatycznego skalowania nie należy określać parametru _targetDedicatedNodes_ ani parametru _targetLowPriorityNodes_ w wywołaniu funkcji **ispool**. Zamiast tego należy określić właściwości **AutoScaleEnabled** i **AutoScaleFormula** w puli. Wartości tych właściwości określają docelowy numer każdego typu węzła.
>
> Aby ręcznie zmienić rozmiar puli z włączoną funkcją automatycznego skalowania (na przykład z [BatchClient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)), należy najpierw wyłączyć automatyczne skalowanie w puli, a następnie zmienić jego rozmiar.

### <a name="python"></a>Python

Aby utworzyć pulę z obsługą skalowania automatycznego przy użyciu zestawu SDK języka Python:

1. Utwórz pulę i określ jej konfigurację.
1. Dodaj pulę do klienta usługi.
1. Włącz automatyczne skalowanie w puli przy użyciu zapisanej formuły.

Poniższy przykład ilustruje te kroki.

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

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włącz Skalowanie automatyczne w istniejącej puli

Każdy zestaw SDK w usłudze Batch umożliwia włączenie automatycznego skalowania. Na przykład:

- [BatchClient. PoolOperations. EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Włącz automatyczne skalowanie w puli](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (interfejs API REST)

Po włączeniu skalowania automatycznego w istniejącej puli należy pamiętać o następujących kwestiach:

- Jeśli automatyczne skalowanie jest obecnie wyłączone w puli, podczas wysyłania żądania należy określić prawidłową formułę skalowania automatycznego. Opcjonalnie można określić interwał skalowania automatycznego. Jeśli nie określisz interwału, zostanie użyta wartość domyślna wynosząca 15 minut.
- Jeśli automatyczne skalowanie jest obecnie włączone w puli, można określić nową formułę, nowy interwał lub oba te elementy. Należy określić co najmniej jedną z tych właściwości.
  - W przypadku określenia nowego interwału automatycznego skalowania istniejący harmonogram zostanie zatrzymany i zostanie uruchomiony nowy harmonogram. Godzina rozpoczęcia nowego harmonogramu to godzina, o której wydano żądanie włączenia skalowania automatycznego.
  - Jeśli pominięto formułę lub interwał automatycznego skalowania, usługa Batch będzie nadal używać bieżącej wartości tego ustawienia.

> [!NOTE]
> Jeśli określono wartości parametrów *targetDedicatedNodes* lub *targetLowPriorityNodes* **metody tworzenia puli w** programie .NET lub dla porównywalnych parametrów w innym języku, te wartości są ignorowane podczas obliczania formuły skalowania automatycznego.

Ten przykład w języku C# używa biblioteki [Batch .NET](/dotnet/api/microsoft.azure.batch) do włączenia skalowania automatycznego w istniejącej puli.

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

Aby zaktualizować formułę w istniejącej puli z włączoną funkcją automatycznego skalowania, wywołaj operację, aby ponownie włączyć automatyczne skalowanie przy użyciu nowej formuły. Na przykład jeśli automatyczne skalowanie jest już włączone `myexistingpool` podczas wykonywania poniższego kodu platformy .NET, jego formuła automatycznego skalowania jest zastępowana zawartością `myNewFormula` .

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

Można oszacować formułę przed zastosowaniem jej do puli. Dzięki temu można testować wyniki formuły przed umieszczeniem jej w środowisku produkcyjnym.

Aby można było oszacować formułę skalowania automatycznego, należy najpierw włączyć skalowanie automatyczne w puli przy użyciu prawidłowej formuły, takiej jak formuła jednowierszowa `$TargetDedicatedNodes = 0` . Następnie użyj jednej z następujących wartości, aby oszacować formułę, którą chcesz przetestować:

- [BatchClient. PoolOperations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) lub [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody wsadowe .NET wymagają identyfikatora istniejącej puli i ciągu zawierającego formułę skalowania automatycznego do obliczenia.

- [Oceń formułę skalowania automatycznego](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądaniu interfejsu API REST Określ Identyfikator puli w identyfikatorze URI i formułę skalowania automatycznego w elemencie *autoScaleFormula* treści żądania. Odpowiedź operacji zawiera wszystkie informacje o błędzie, które mogą być powiązane z formułą.

Ten przykład [programu Batch .NET](/dotnet/api/microsoft.azure.batch) oblicza formułę skalowania automatycznego. Jeśli pula nie korzysta już z automatycznego skalowania, zostanie ona najpierw włączona.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
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

Aby upewnić się, że formuła działa zgodnie z oczekiwaniami, zalecamy okresowe sprawdzanie wyników przebiegów skalowania automatycznego, które wykonuje partia na tej puli. Aby to zrobić, Pobierz (lub Odśwież) odwołanie do puli, a następnie zapoznaj się z właściwościami ostatniego przebiegu automatycznego skalowania.

W usłudze Batch .NET Właściwość [CloudPool. AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) ma kilka właściwości, które zawierają informacje o najnowszym przebiegu skalowania automatycznego wykonanego w puli:

- [AutoScaleRun. timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun. results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun. Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST informacje o żądaniu [pobrania](/rest/api/batchservice/get-information-about-a-pool) puli zwracają informacje o puli, które obejmują najnowsze automatyczne skalowanie informacji o uruchomieniu we właściwości [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) .

Poniższy przykład w języku C# używa biblioteki Batch .NET do drukowania informacji o ostatnim uruchomieniu skalowania _puli._

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Przykładowe dane wyjściowe z powyższego przykładu:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Pobierz historię uruchamiania automatycznego skalowania przy użyciu zdarzeń automatycznego skalowania puli
Możesz także sprawdzić historię skalowania automatycznego, badając [PoolAutoScaleEvent](batch-pool-autoscale-event.md). To zdarzenie jest emitowane przez usługę Batch do rejestrowania każdego wystąpienia oceny i wykonywania formuł automatycznego skalowania, co może pomóc w rozwiązywaniu potencjalnych problemów.

Przykładowe zdarzenie dla PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
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

`$curTime` można dostosować w celu odzwierciedlenia lokalnej strefy czasowej przez dodanie `time()` do produktu `TimeZoneInterval_Hour` i przesunięcia czasu UTC. Na przykład użyj `$curTime = time() + (-6 * TimeInterval_Hour);` terminu górski czas letni (zestawu MDT). Należy pamiętać, że przesunięcie powinno być dostosowane na początku i na końcu czasu letniego (jeśli dotyczy).

### <a name="example-2-task-based-adjustment"></a>Przykład 2: korekta oparta na zadaniach

W tym przykładzie w języku C# rozmiar puli jest dostosowywany na podstawie liczby zadań w kolejce. W ciągach formuł dołączono zarówno komentarze, jak i podziały wierszy.

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

Ten przykład w języku C# dostosowuje rozmiar puli na podstawie liczby zadań. Ta formuła uwzględnia również wartość [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) , która została ustawiona dla puli. Takie podejście jest przydatne w sytuacjach, w których włączono [równoległe wykonywanie zadań](batch-parallel-node-tasks.md) w puli.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
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

W tym przykładzie przedstawiono przykład w języku C# z formułą skalowania automatycznego, która ustawia rozmiar puli na określoną liczbę węzłów w początkowym okresie. Następnie dostosowuje rozmiar puli na podstawie liczby uruchomionych i aktywnych zadań.

W programie Ta formuła wykonuje następujące czynności:

- Ustawia rozmiar puli początkowej na cztery węzły.
- Nie dopasowuje rozmiaru puli w ciągu pierwszych 10 minut cyklu życia puli.
- Po upływie 10 minut uzyskuje maksymalną wartość liczby uruchomionych i aktywnych zadań w ciągu ostatnich 60 minut.
  - Jeśli obie wartości są równe 0 (wskazuje, że żadne zadania nie były uruchomione lub aktywne w ciągu ostatnich 60 minut), rozmiar puli jest ustawiony na 0.
  - Jeśli jedna z wartości jest większa od zera, nie zostanie wprowadzona żadna zmiana.

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

- Dowiedz się, jak [wykonywać wiele zadań jednocześnie w węzłach obliczeniowych w puli](batch-parallel-node-tasks.md). Wraz z skalowaniem automatycznym może to pomóc w obniżeniu czasu trwania zadania w przypadku niektórych obciążeń, co pozwala zaoszczędzić pieniądze.
- Dowiedz się [, jak wydajnie wykonywać zapytania dotyczące usługi Azure Batch](batch-efficient-list-queries.md) .
