---
title: Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch
description: Włącz automatyczne skalowanie w puli chmury, aby dynamicznie dostosowywać liczbę węzłów obliczeniowych w puli.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: f70c29f0e8a313233991c7363dc4b8a41a1b1cd5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389370"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Tworzenie automatycznej formuły do skalowania węzłów obliczeniowych w puli usługi Batch

Azure Batch automatycznie skalować pule na podstawie parametrów, które definiujesz, co pozwala zaoszczędzić czas i pieniądze. Dzięki automatycznemu skalowaniu usługa Batch dynamicznie dodaje węzły do puli w przypadku wzrostu zapotrzebowania na zadania i usuwa węzły obliczeniowe w przypadku spadku zapotrzebowania na zadania.

Aby włączyć automatyczne skalowanie w puli węzłów obliczeniowych, należy skojarzyć pulę z zdefiniowaną formułą autoskalowania.  Usługa Batch używa formuły autoskalowania, aby określić, ile węzłów jest potrzebnych do wykonania obciążenia. Te węzły mogą być węzłami dedykowanymi lub [węzłami o niskim priorytecie.](batch-low-pri-vms.md) Usługa Batch będzie następnie okresowo przeglądać dane metryk usługi i używać ich do dostosowania liczby węzłów w puli na podstawie formuły i w interwałach, które zdefiniujesz.

Skalowanie automatyczne można włączyć podczas tworzenia puli lub zastosować do istniejącej puli. Usługa Batch umożliwia ocenę formuł przed przypisaniem ich do pul i monitorowanie stanu przebiegów automatycznego skalowania. Po skonfigurowaniu puli przy użyciu automatycznego skalowania można później wprowadzić zmiany w formule.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Batch można określić tryb alokacji puli [,](accounts.md)który określa, czy pule są przydzielane w subskrypcji usługi Batch (ustawienie domyślne), czy w subskrypcji użytkownika. Jeśli konto usługi Batch utworzono przy użyciu domyślnej konfiguracji usługi Batch, twoje konto jest ograniczone do maksymalnej liczby rdzeni, których można użyć do przetwarzania. Usługa Batch skaluje węzły obliczeniowe tylko do tego limitu rdzeni. Z tego powodu usługa Batch może nie osiągać docelowej liczby węzłów obliczeniowych określonej przez formułę skalowania automatycznego. Zobacz [Limity przydziału i limity dla usługi Azure Batch,](batch-quota-limit.md) aby uzyskać informacje na temat wyświetlania i zwiększania limitów przydziału konta.
>
>Jeśli konto utworzono w trybie subskrypcji użytkownika, konto ma udziały w podstawowym limitze przydziału dla subskrypcji. Aby uzyskać więcej informacji, zobacz sekcję [Virtual Machines limits (Limity maszyn wirtualnych)](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) w artykule [Azure subscription and service limits, quotas, and constraints (Ograniczenia, przydziały i limity usług i subskrypcji platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Formuły skalowania automatycznego

Formuła autoskalowania to zdefiniowana przez Ciebie wartość ciągu zawierająca co najmniej jedną instrukcje. Formuła autoskalowania jest przypisywana do elementu [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) puli (Batch REST) lub właściwości [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (batch .NET). Usługa Batch używa formuły do określenia docelowej liczby węzłów obliczeniowych w puli na następny interwał przetwarzania. Ciąg formuły nie może przekraczać 8 KB, może zawierać do 100 instrukcji rozdzielonych średnikami oraz może zawierać podziały wierszy i komentarze.

Formuły automatycznego skalowania można myśleć jak o "języku" automatycznego skalowania usługi Batch. Instrukcje formuł są wyrażeniami swobodnymi, które mogą zawierać zarówno zmienne zdefiniowane przez usługę (zdefiniowane przez usługę Batch), jak i zmienne zdefiniowane przez użytkownika. Formuły mogą wykonywać różne operacje na tych wartościach przy użyciu wbudowanych typów, operatorów i funkcji. Na przykład instrukcja może mieć następującą postać:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formuły zwykle zawierają wiele instrukcji, które wykonują operacje na wartościach uzyskanych w poprzednich instrukcjach. Na przykład najpierw uzyskujemy wartość dla , a `variable1` następnie przekażemy ją do funkcji w celu wypełnienia `variable2` wartości :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Dołącz te instrukcje do formuły autoskalowania, aby dotrzeć do docelowej liczby węzłów obliczeniowych. Węzły dedykowane i węzły o niskim priorytecie mają własne ustawienia docelowe. Formuła autoskalowania może zawierać wartość docelową dla dedykowanych węzłów, wartość docelową dla węzłów o niskim priorytecie lub oba te węzły.

Docelowa liczba węzłów może być większa, niższa lub taka sama jak bieżąca liczba węzłów tego typu w puli. Usługa Batch ocenia formułę automatycznego skalowania puli w określonych interwałach [automatycznego skalowania.](#automatic-scaling-interval) Usługa Batch dostosowuje liczbę docelową każdego typu węzła w puli na liczbę określaną przez formułę autoskalowania w czasie oceny.

### <a name="sample-autoscale-formulas"></a>Przykładowe formuły autoskalowania

Poniżej przedstawiono przykłady dwóch formuł autoskalowania, które można dostosować tak, aby działały w większości scenariuszy. Zmienne i `startingNumberOfVMs` `maxNumberofVMs` w przykładowych formułach można dostosować do swoich potrzeb.

#### <a name="pending-tasks"></a>Oczekujące zadania

W przypadku tej formuły autoskalowania pula jest początkowo tworzona z jedną maszyną wirtualną. Metryka `$PendingTasks` definiuje liczbę zadań, które są uruchomione lub znajdują się w kolejce. Formuła znajduje średnią liczbę oczekujących zadań w ciągu ostatnich 180 sekund i odpowiednio `$TargetDedicatedNodes` ustawia zmienną. Formuła gwarantuje, że docelowa liczba dedykowanych węzłów nigdy nie przekracza 25 maszyn wirtualnych. W miarę jak są przesyłane nowe zadania, pula automatycznie się zwiększa. Po ukończeniu zadań maszyny wirtualne stają się wolne, a formuła skalowania automatycznego zmniejsza pulę.

Ta formuła skaluje węzły dedykowane, ale można ją zmodyfikować, aby stosować ją również do węzłów o niskim priorytecie.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Wywłaszcze węzły

Ten przykład tworzy pulę, która rozpoczyna się od 25 węzłów o niskim priorytecie. Za każdym razem, gdy węzeł o niskim priorytecie zostanie wywłaszowany, jest zastępowany dedykowanym węzłem. Podobnie jak w pierwszym przykładzie, zmienna uniemożliwia `maxNumberofVMs` puli przekroczenie 25 maszyn wirtualnych. Ten przykład jest przydatny do korzystania z maszyn wirtualnych o niskim priorytecie, a jednocześnie zapewnienia, że przez cały okres istnienia puli wystąpi tylko stała liczba wywłaściwości.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

W dalszej części tego tematu dowiesz się więcej na temat tworzenia formuł [autoskalowania](#write-an-autoscale-formula) i zobacz dodatkowe przykładowe formuły skalowania automatycznego. [](#example-autoscale-formulas)

## <a name="variables"></a>Zmienne

W **formułach** autoskalowania można używać zmiennych zdefiniowanych przez usługę i zdefiniowanych przez użytkownika. 

Zmienne zdefiniowane przez usługę są wbudowane w usłudze Batch. Niektóre zmienne zdefiniowane przez usługę są do odczytu i zapisu, a niektóre są tylko do odczytu.

Zdefiniowane przez użytkownika zmienne to zdefiniowane przez Ciebie zmienne. W przykładowej formule przedstawionej powyżej zmienne i są zmiennymi zdefiniowanymi przez usługę, natomiast zmienne i są zmiennymi `$TargetDedicatedNodes` `$PendingTasks` `startingNumberOfVMs` `maxNumberofVMs` zdefiniowanymi przez użytkownika.

> [!NOTE]
> Zmienne zdefiniowane przez usługę są zawsze poprzedzone znakiem dolara ($). W przypadku zmiennych zdefiniowanych przez użytkownika znak dolara jest opcjonalny.

W poniższych tabelach przedstawiono zmienne tylko do odczytu i zapisu zdefiniowane przez usługę Batch.

### <a name="read-write-service-defined-variables"></a>Zmienne zdefiniowane przez usługę do odczytu i zapisu

Możesz pobrać i ustawić wartości tych zmiennych zdefiniowanych przez usługę, aby zarządzać liczbą węzłów obliczeniowych w puli.

| Zmienna | Opis |
| --- | --- |
| $TargetDedicatedNodes |Docelowa liczba dedykowanych węzłów obliczeniowych dla puli. Jest on określony jako element docelowy, ponieważ pula nie zawsze może osiągnąć żądaną liczbę węzłów. Jeśli na przykład docelowa liczba dedykowanych węzłów zostanie zmodyfikowana przez ocenę skalowania automatycznego, zanim pula osiągnie początkowy element docelowy, pula może nie dotrzeć do obiektu docelowego. <br /><br /> Pula na koncie utworzonym w trybie usługi Batch może nie osiągnąć celu, jeśli element docelowy przekroczy limit przydziału węzła konta usługi Batch lub limitu przydziału rdzeni. Pula w ramach konta utworzonego w trybie subskrypcji użytkownika może nie osiągnąć celu, jeśli element docelowy przekroczy udostępniony limit przydziału rdzeni dla subskrypcji.|
| $TargetLowPriorityNodes |Docelowa liczba węzłów obliczeniowych o niskim priorytecie dla puli. Jest to określone jako element docelowy, ponieważ pula nie zawsze może osiągnąć żądaną liczbę węzłów. Jeśli na przykład docelowa liczba węzłów o niskim priorytecie zostanie zmodyfikowana przez ocenę autoskalowania, zanim pula osiągnie początkowy element docelowy, pula może nie osiągnąć wartości docelowej. Pula może również nie osiągnąć celu, jeśli element docelowy przekracza limit przydziału węzła konta usługi Batch lub limitu przydziału rdzeni. <br /><br /> Aby uzyskać więcej informacji na temat węzłów obliczeniowych o niskim priorytecie, zobacz Używanie maszyn wirtualnych o niskim priorytecie [z usługą Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akcja, która występuje, gdy węzły obliczeniowe są usuwane z puli. Możliwe wartości:<ul><li>**requeue:** wartość domyślna. Natychmiast kończy zadania i umieszcza je z powrotem w kolejce zadań, aby były ponownie wykonywane. Ta akcja gwarantuje, że docelowa liczba węzłów zostanie osiągnięta tak szybko, jak to możliwe. Jednak może to być mniej wydajne, ponieważ wszystkie uruchomione zadania zostaną przerwane, a następnie konieczne będzie całkowite ponowne uruchomienie. <li>**terminate:** natychmiast kończy zadania i usuwa je z kolejki zadań.<li>**taskcompletion:** czeka na zakończenie aktualnie uruchomionych zadań, a następnie usuwa węzeł z puli. Użyj tej opcji, aby uniknąć przerywania i ponownego kolejkowania zadań oraz marnowania pracy wykonanej przez zadanie.<li>**retaineddata:** czeka na wyczyszczenie wszystkich lokalnych danych zachowywanych przez zadania w węźle przed usunięciem węzła z puli.</ul> |

> [!NOTE]
> Zmienną `$TargetDedicatedNodes` można również określić przy użyciu aliasu `$TargetDedicated` . Podobnie `$TargetLowPriorityNodes` zmienną można określić przy użyciu aliasu `$TargetLowPriority` . Jeśli zarówno w pełni nazwana zmienna, jak i jej alias zostaną ustawione przez formułę, pierwszeństwo będzie miała wartość przypisana do w pełni nazwanej zmiennej.

### <a name="read-only-service-defined-variables"></a>Zmienne zdefiniowane przez usługę tylko do odczytu

Możesz uzyskać wartość tych zmiennych zdefiniowanych przez usługę, aby wprowadzić korekty oparte na metrykach z usługi Batch.

> [!IMPORTANT]
> Zadania zwolnienia zadania nie są obecnie zawarte w zmiennych, które zapewniają liczbę zadań, takich jak $ActiveTasks i $PendingTasks. W zależności od formuły autoskalowania może to spowodować usunięcie węzłów bez dostępnych węzłów do uruchamiania zadań podrzędnych zwolnienia zadania.

| Zmienna | Opis |
| --- | --- |
| $CPUPercent |Średni procent użycia procesora CPU. |
| $WallClockSeconds |Liczba zużytych sekund. |
| $MemoryBytes |Średnia liczba używanych megabajtów. |
| $DiskBytes |Średnia liczba gigabajtów używanych na dyskach lokalnych. |
| $DiskReadBytes |Liczba odczytanych bajtów. |
| $DiskWriteBytes |Liczba bajtów napisano. |
| $DiskReadOps |Liczba wykonanych operacji odczytu na dysku. |
| $DiskWriteOps |Liczba wykonanych operacji zapisu na dysku. |
| $NetworkInBytes |Liczba bajtów przychodzących. |
| $NetworkOutBytes |Liczba bajtów wychodzących. |
| $SampleNodeCount |Liczba węzłów obliczeniowych. |
| $ActiveTasks |Liczba zadań, które są gotowe do wykonania, ale nie zostały jeszcze wykonane. Obejmuje to wszystkie zadania, które są w stanie aktywnym i których zależności zostały spełnione. Wszystkie zadania, które są w stanie aktywnym, ale których zależności nie zostały spełnione, są wykluczone z $ActiveTasks liczby. W przypadku zadania o wielu wystąpieniach $ActiveTasks będzie zawierać liczbę wystąpień ustawionych w zadaniu.|
| $RunningTasks |Liczba zadań w stanie uruchomienia. |
| $PendingTasks |Suma wartości $ActiveTasks i $RunningTasks. |
| $SucceededTasks |Liczba zadań, które zakończyły się pomyślnie. |
| $FailedTasks |Liczba zadań, które zakończyły się niepowodzeniem. |
| $TaskSlotsPerNode |Liczba miejsc zadań, które mogą służyć do uruchamiania współbieżnych zadań w jednym węźle obliczeniowym w puli. |
| $CurrentDedicatedNodes |Bieżąca liczba dedykowanych węzłów obliczeniowych. |
| $CurrentLowPriorityNodes |Bieżąca liczba węzłów obliczeniowych o niskim priorytecie, w tym wszystkie węzły, które zostały wywłaszone. |
| $PreemptedNodeCount | Liczba węzłów w puli, które są w stanie wywłasznia. |

> [!TIP]
> Te zmienne zdefiniowane przez usługę tylko do odczytu *to* obiekty, które zapewniają różne metody uzyskiwania dostępu do danych skojarzonych z każdą z nich. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie przykładowych danych](#obtain-sample-data) w dalszej części tego artykułu.

> [!NOTE]
> Użyj w przypadku skalowania na podstawie liczby zadań uruchomionych w punkcie w czasie oraz podczas skalowania na podstawie liczby zadań, które są w kolejce do `$RunningTasks` `$ActiveTasks` uruchomienia.

## <a name="types"></a>Typy

Formuły autoskalowania obsługują następujące typy:

- double
- doubleVec
- doubleVecList
- ciąg
- sygnatura czasowa — złożona struktura, która zawiera następujące elementy członkowskie:
  - rok
  - month (1–12)
  - dzień (1–31)
  - dzień tygodnia (w formacie liczby, na przykład 1 w poniedziałek)
  - hour (w formacie liczbowym 24-godzinnym, na przykład 13 oznacza 13:00)
  - minuta (00–59)
  - sekunda (00–59)
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

Te operacje są dozwolone dla typów wymienionych w poprzedniej sekcji.

| Operacja | Obsługiwane operatory | Typ wyniku |
| --- | --- | --- |
| double *operator* double |+, -, *, / |double |
| double *operator* timeinterval |* |timeinterval |
| doubleVec, *operator* double |+, -, *, / |doubleVec |
| DoubleVec, *operator* doubleVec |+, -, *, / |doubleVec |
| timeinterval, *operator* double |*, / |timeinterval |
| timeinterval *operator* timeinterval |+, - |timeinterval |
| znacznik czasu *operatora* czasu |+ |sygnatura czasowa |
| timestamp *operator* timeinterval |+ |sygnatura czasowa |
| sygnatura *czasowa operatora* sygnatury czasowej |- |timeinterval (czas) |
| *operator* double |-, ! |double |
| *operator* timeinterval |- |timeinterval (czas) |
| double *operator* double |<, <=, ==, >=, >, != |double |
| string, *operator* string |<, <=, ==, >=, >, != |double |
| znacznik czasu *operatora* |<, <=, ==, >=, >, != |double |
| timeinterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| double *operator* double |&&,  &#124;&#124; |double |

Podczas testowania podwójnej wartości za pomocą operatoraternarnego ( ), wartość niezerowa ma wartość `double ? statement1 : statement2` **true,** a zero ma wartość **false**.

## <a name="functions"></a>Funkcje

Tych wstępnie zdefiniowanych funkcji można **używać podczas** definiowania formuły autoskalowania.

| Funkcja | Typ zwracany | Opis |
| --- | --- | --- |
| avg(doubleVecList) |double |Zwraca średnią wartość dla wszystkich wartości w doubleVecList. |
| len(doubleVecList) |double |Zwraca długość wektora utworzonego na podstawie obiektu doubleVecList. |
| lg(double) |double |Zwraca bazę dziennika 2 podwójnej. |
| lg(doubleVecList) |doubleVec |Zwraca bazę dziennika z 2 składnikiem doubleVecList. Vec(double) musi być jawnie przekazany dla parametru. W przeciwnym razie zakładana jest wersja double lg(double). |
| ln(double) |double |Zwraca dziennik naturalny podwójnej. |
| ln(doubleVecList) |doubleVec |Zwraca dziennik naturalny podwójnej. |
| log(double) |double |Zwraca bazę dziennika 10 z double. |
| log(doubleVecList) |doubleVec |Zwraca bazę dziennika 10 dla składnika doubleVecList. Vec(double) musi być jawnie przekazany dla pojedynczego podwójnego parametru. W przeciwnym razie zakładana jest wersja podwójnej log(double). |
| max(doubleVecList) |double |Zwraca maksymalną wartość w doubleVecList. |
| min(doubleVecList) |double |Zwraca minimalną wartość w doubleVecList. |
| norm(doubleVecList) |double |Zwraca dwie normy wektora utworzonego na podstawie obiektu doubleVecList. |
| percentyl(doubleVec v, double p) |double |Zwraca element percentyla wektora v. |
| rand() |double |Zwraca wartość losową z 0,0 do 1,0. |
| range(doubleVecList) |double |Zwraca różnicę między wartościami min i maksimum w doubleVecList. |
| std(doubleVecList) |double |Zwraca odchylenie standardowe próbki wartości z doubleVecList. |
| stop() | |Zatrzymuje ocenę wyrażenia skalowania automatycznego. |
| sum(doubleVecList) |double |Zwraca sumę wszystkich składników doubleVecList. |
| time(string dateTime="") |sygnatura czasowa |Zwraca sygnaturę czasową bieżącego czasu, jeśli nie przekazano żadnych parametrów, lub sygnaturę czasową ciągu dateTime, jeśli został przekazany. Obsługiwane formaty dateTime to W3C-DTF i RFC 1123. |
| val(doubleVec v, double i) |double |Zwraca wartość elementu, który znajduje się w lokalizacji i w wektorze v, z indeksem początkowym o wartości zero. |

Niektóre funkcje opisane w poprzedniej tabeli mogą akceptować listę jako argument. Rozdzielana przecinkami lista jest dowolną kombinacją *typu double* *i doubleVec.* Na przykład:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Wartość *doubleVecList* jest konwertowana na pojedynczy *doubleVec* przed oceną. Jeśli na przykład , `v = [1,2,3]` wywołanie funkcji jest równoważne `avg(v)` wywołaniu `avg(1,2,3)` . Wywołanie `avg(v, 7)` jest równoważne wywołaniu `avg(1,2,3,7)` .

## <a name="metrics"></a>Metryki

Podczas definiowania formuły można używać metryk zasobów i zadań. Docelową liczbę dedykowanych węzłów w puli dostosowuje się na podstawie danych metryk, które można uzyskać i ocenić. Aby uzyskać więcej informacji na temat każdej metryki, zobacz [sekcję Zmienne](#variables) powyżej.

<table>
  <tr>
    <th>Metric</th>
    <th>Opis</th>
  </tr>
  <tr>
    <td><b>Zasób</b></td>
    <td><p>Metryki zasobów są oparte na procesorze CPU, przepustowości, użyciu pamięci węzłów obliczeniowych i liczbie węzłów.</p>
        <p> Te zmienne zdefiniowane przez usługę są przydatne do dokonywania korekt na podstawie liczby węzłów:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Te zmienne zdefiniowane przez usługę są przydatne do dokonywania korekt na podstawie użycia zasobów węzła:</p>
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
    <td><p>Metryki zadań są oparte na stanie zadań, takich jak Aktywne, Oczekujące i Ukończone. Następujące zmienne zdefiniowane przez usługę są przydatne do dokonywania korekt rozmiaru puli na podstawie metryk zadań:</p>
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

Podstawową operacją formuły autoskalowania jest uzyskanie danych metryk zadań i zasobów (przykłady), a następnie dostosowanie rozmiaru puli na podstawie tych danych. W związku z tym ważne jest, aby mieć wiedzę na temat interakcji formuł autoskalowania z przykładami.

### <a name="methods"></a>Metody

Formuły autoskalowania działają na przykładach danych metryk dostarczanych przez usługę Batch. Formuła będzie powiększać lub zmniejszać rozmiar puli na podstawie wartości, które uzyskuje. Zmienne zdefiniowane przez usługę to obiekty, które zapewniają metody dostępu do danych skojarzonych z tym obiektem. Na przykład następujące wyrażenie przedstawia żądanie uzyskania użycia procesora CPU z ostatnich pięciu minut:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Następujące metody mogą służyć do uzyskiwania przykładowych danych dotyczących zmiennych zdefiniowanych przez usługę.

| Metoda | Opis |
| --- | --- |
| GetSample() |Metoda `GetSample()` zwraca wektor próbek danych.<br/><br/>Przykład to dane metryk o wartości 30 sekund. Innymi słowy, próbki są uzyskiwane co 30 sekund. Jak wspomniano poniżej, istnieje opóźnienie między zebraniem próbki a jej dostępem do formuły. W związku z tym nie wszystkie próbki dla danego okresu mogą być dostępne do oceny za pomocą formuły.<ul><li>`doubleVec GetSample(double count)`: określa liczbę próbek do uzyskania z najnowszych zebranych próbek. `GetSample(1)` Zwraca ostatni dostępny przykład. Jednak w przypadku metryk, takich jak , nie należy ich używać, ponieważ nie można wiedzieć, kiedy `$CPUPercent` `GetSample(1)` pobrano próbkę.  Może być to wersja najnowsza lub, ze względu na problemy z systemem, być może znacznie starsza. W takich przypadkach lepiej jest użyć przedziału czasu, jak pokazano poniżej.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: określa ramy czasowe zbierania przykładowych danych. Opcjonalnie określa również wartość procentową próbek, które muszą być dostępne w żądanym okresie. Na przykład zwróci 20 próbek, jeśli wszystkie próbki z ostatnich 10 minut są `$CPUPercent.GetSample(TimeInterval_Minute * 10)` obecne w `CPUPercent` historii. Gdyby ostatnia minuta historii nie była dostępna, zwróconoby tylko 18 próbek. W takim przypadku `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` nie powiedzie się, ponieważ tylko 90% próbek jest dostępnych, ale `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` zakończyłoby się pomyślnie.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: określa ramy czasowe zbierania danych, zarówno w czasie rozpoczęcia, jak i czasie zakończenia. Jak wspomniano powyżej, istnieje opóźnienie między zebraniem próbki a jej dostępem do formuły. To opóźnienie należy wziąć pod uwagę podczas korzystania z `GetSample` metody . Zobacz `GetSamplePercent` poniżej. |
| GetSamplePeriod() |Zwraca okres próbek pobranych w zestawie danych historycznych próbki. |
| Count() |Zwraca łączną liczbę próbek w historii metryk. |
| HistoryBeginTime() |Zwraca sygnaturę czasową najstarszej dostępnej próbki danych dla metryki. |
| GetSamplePercent() |Zwraca wartość procentową próbek dostępnych w danym przedziale czasu. Na przykład `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Ponieważ metoda kończy się niepowodzeniem, jeśli wartość procentowa zwracanych próbek jest mniejsza niż określona, możesz użyć metody , `GetSample` `samplePercent` aby najpierw ją `GetSamplePercent` sprawdzić. Następnie możesz wykonać akcję alternatywną, jeśli nie ma wystarczających próbek, bez zatrzymania automatycznej oceny skalowania. |

### <a name="samples"></a>Samples

Usługa Batch okresowo pobiera przykłady metryk zadań i zasobów oraz udostępnia je formułom autoskalowania. Te przykłady są rejestrowane co 30 sekund przez usługę Batch. Jednak zazwyczaj występuje opóźnienie między tym, kiedy te próbki zostały zarejestrowane, a kiedy zostały udostępnione formułom autoskalowania (i mogą być przez nie odczytywane). Ponadto próbki mogą nie być rejestrowane w określonym interwale ze względu na czynniki takie jak problemy z siecią lub innymi problemami z infrastrukturą.

### <a name="sample-percentage"></a>Procent próby

Gdy metoda jest przekazywana do metody lub metoda jest wywoływana, wartość procentowa odnosi się do porównania łącznej możliwej liczby próbek zarejestrowanych przez usługę Batch i liczby próbek dostępnych dla formuły `samplePercent` `GetSample()` `GetSamplePercent()` autoskalowania. 

Przyjrzyjmy się przykładowi 10-minutowego czasu. Ponieważ próbki są rejestrowane co 30 sekund w ciągu tego 10-minutowego czasu, maksymalna łączna liczba próbek zarejestrowanych przez partię wynosi 20 próbek (2 na minutę). Jednak ze względu na naturalne opóźnienie mechanizmu raportowania i inne problemy na platformie Azure do odczytu może być dostępnych tylko 15 przykładów dla formuły skalowania automatycznego. Na przykład dla tego 10-minutowego okresu formuła może mieć dostęp tylko do 75% całkowitej liczby zarejestrowanych próbek.

### <a name="getsample-and-sample-ranges"></a>GetSample() i przykładowe zakresy

Formuły autoskalowania będą powiększać i zmniejszać pule przez dodawanie lub usuwanie węzłów. Ponieważ węzły są kosztowne, upewnij się, że formuły używają inteligentnej metody analizy opartej na wystarczającej ilości danych. Zalecamy używanie analizy trendów w formułach. Ten typ zwiększa i zmniejsza pule na podstawie zakresu zebranych próbek.

Aby to zrobić, użyj funkcji `GetSample(interval look-back start, interval look-back end)` , aby zwrócić wektor próbek:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Gdy powyższy wiersz jest oceniany przez usługę Batch, zwraca zakres próbek jako wektor wartości. Na przykład:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po zebrania wektora próbek można użyć funkcji, takich jak , i , aby uzyskać znaczące wartości `min()` `max()` z `avg()` zebranego zakresu.

Aby zapewnić dodatkowe bezpieczeństwo, możesz wymusić niepowodzenie oceny formuły, jeśli w danym okresie dostępna jest mniej niż pewna wartość procentowa próbki. Gdy wymusz niepowodzenie oceny formuły, instruuj usługę Batch, aby zaprzestała dalszej oceny formuły, jeśli określony procent próbek jest niedostępny. W takim przypadku rozmiar puli nie jest zmieniany. Aby określić wymaganą wartość procentową próbek do oceny, określ ją jako trzeci parametr dla parametru `GetSample()` . W tym miejscu określono wymaganie 75 procent próbek:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ze względu na to, że może wystąpić opóźnienie w dostępności próbki, zawsze należy określić zakres czasu z czasem rozpoczęcia wyszukiwania starszym niż jedna minuta. Propagacja próbek w systemie trwa około minuty, więc próbki z zakresu mogą `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` być niedostępne. Ponownie możesz użyć parametru procentu , `GetSample()` aby wymusić konkretne wymaganie procentowe próbki.

> [!IMPORTANT]
> Zdecydowanie zalecamy unikanie **polegania  tylko na formułach `GetSample(1)` autoskalowania.** Wynika to z tego, że usługa Batch informuje o tym: "Give me the last sample you have, matter how a `GetSample(1)` long ago you retrieved it". Ponieważ jest to tylko jedna próbka i może to być starsza próbka, może nie być reprezentatywna dla większego obrazu ostatniego zadania lub stanu zasobu. Jeśli używasz funkcji , upewnij się, że jest ona częścią większej instrukcji, a nie jedynym punktem danych, na którym `GetSample(1)` opiera się formuła.

## <a name="write-an-autoscale-formula"></a>Pisanie formuły autoskalowania

Formułę autoskalowania tworzy się, tworząc instrukcje korzystające z powyższych składników, a następnie łącząc te instrukcje w kompletną formułę. W tej sekcji utworzymy przykładową formułę autoskalowania, która umożliwia podejmowanie rzeczywistych decyzji dotyczących skalowania i przeprowadzanie korekt.

Najpierw zdefiniujmy wymagania dla naszej nowej formuły autoskalowania. Formuła powinna:

- Zwiększ docelową liczbę dedykowanych węzłów obliczeniowych w puli, jeśli użycie procesora CPU jest wysokie.
- Zmniejsz docelową liczbę dedykowanych węzłów obliczeniowych w puli, gdy użycie procesora CPU będzie niskie.
- Zawsze ograniczaj maksymalną liczbę dedykowanych węzłów do 400.
- Zmniejszając liczbę węzłów, nie usuwaj węzłów, w których są uruchomione zadania podrzędne. W razie potrzeby zaczekaj na zakończenie zadań podrzędnych przed usunięciem węzłów.

Pierwsza instrukcja w naszej formule zwiększy liczbę węzłów podczas wysokiego użycia procesora CPU. Zdefiniujemy instrukcje, które wypełnią zmienną zdefiniowaną przez użytkownika ( ) wartością, która jest 110 procentem bieżącej docelowej liczby dedykowanych węzłów, ale tylko wtedy, gdy minimalne średnie użycie procesora CPU w ciągu ostatnich `$totalDedicatedNodes` 10 minut przekraczało 70 procent. W przeciwnym razie używa wartości dla bieżącej liczby dedykowanych węzłów.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Aby zmniejszyć liczbę dedykowanych węzłów podczas niskiego użycia procesora CPU, następna instrukcja w naszej formule ustawia tę samą zmienną na 90% bieżącej docelowej liczby dedykowanych węzłów, jeśli średnie użycie procesora CPU w ciągu ostatnich 60 minut było poniżej `$totalDedicatedNodes` 20 procent. W przeciwnym razie używa bieżącej wartości `$totalDedicatedNodes` wypełnionej w powyższej instrukcji.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Teraz ograniczymy docelową liczbę dedykowanych węzłów obliczeniowych do maksymalnie 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Na koniec upewnimy się, że węzły nie zostaną usunięte, dopóki ich zadania nie zostaną zakończone.

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
> Jeśli wybierzesz tę opcję, w ciągach formuł można uwzględnić zarówno komentarze, jak i podziały wierszy. Należy również pamiętać, że brakujące średniki mogą powodować błędy oceny.

## <a name="automatic-scaling-interval"></a>Interwał automatycznego skalowania

Domyślnie usługa Batch dostosowuje rozmiar puli zgodnie z formułą autoskalowania co 15 minut. Ten interwał można skonfigurować przy użyciu następujących właściwości puli:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (interfejs API REST)

Minimalny interwał wynosi pięć minut, a maksymalny 168 godzin. Jeśli interwał poza tym zakresem jest określony, usługa Batch zwraca błąd Bad Request (400).

> [!NOTE]
> Skalowanie automatyczne nie jest obecnie przeznaczone do reagowania na zmiany w czasie krótszym niż minuta, ale ma na celu stopniowe dostosowywanie rozmiaru puli podczas uruchamiania obciążenia.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Tworzenie puli z obsługą autoskalowania przy użyciu zestawów SDK usługi Batch

Autoskalowanie puli można skonfigurować przy użyciu dowolnego zestawu SDK usługi [Batch,](batch-apis-tools.md#azure-accounts-for-batch-development)poleceń [cmdlet](batch-powershell-cmdlets-get-started.md)programu PowerShell interfejsu [API REST](/rest/api/batchservice/) usługi Batch i interfejsu wiersza polecenia [usługi Batch.](batch-cli-get-started.md) W tej sekcji przedstawiono przykłady dla platform .NET i Python.

### <a name="net"></a>.NET

Aby utworzyć pulę z włączonym skalowaniem automatycznym na platformie .NET, wykonaj następujące kroki:

1. Utwórz pulę za pomocą [puli BatchClient.PoolOperations.CreatePool.](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)
1. Ustaw właściwość [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) na `true` wartość .
1. Ustaw właściwość [CloudPool.AutoScaleFormula za](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) pomocą formuły autoskalowania.
1. (Opcjonalnie) Ustaw właściwość [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (wartość domyślna to 15 minut).
1. Zat zatwierdzanie puli za pomocą [cloudpool.commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) lub [commitAsync.](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)

Poniższy przykład tworzy pulę z obsługą autoskalowania na .NET. Formuła automatycznego skalowania puli ustawia docelową liczbę dedykowanych węzłów na 5 w poniedziałek i na 1 w każdy drugi dzień tygodnia. Interwał [automatycznego skalowania](#automatic-scaling-interval) jest ustawiony na 30 minut. W tym i innych fragmentach kodu języka C# w tym artykule jest prawidłowo zainicjowanym wystąpieniem `myBatchClient` [klasy BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    VirtualMachineConfiguration: new VirtualMachineConfiguration(
                        imageReference: new ImageReference(
                                            publisher: "MicrosoftWindowsServer",
                                            offer: "WindowsServer",
                                            sku: "2019-datacenter-core",
                                            version: "latest"),
                        nodeAgentSkuId: "batch.node.windows amd64");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Podczas tworzenia puli z włączoną skalą automatyczną nie należy określać _parametru targetDedicatedNodes_ ani _parametru targetLowPriorityNodes_ w wywołaniu **puli CreatePool.** Zamiast tego określ właściwości **AutoScaleEnabled** i **AutoScaleFormula** w puli. Wartości tych właściwości określają liczbę docelową każdego typu węzła.
>
> Aby ręcznie zmienić rozmiar puli z obsługą skalowania automatycznego (na przykład za pomocą [funkcji BatchClient.PoolOperations.ResizePoolAsync),](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)należy najpierw wyłączyć automatyczne skalowanie w puli, a następnie zmienić jego rozmiar.

### <a name="python"></a>Python

Aby utworzyć pulę z obsługą skalowania automatycznego przy użyciu zestawu SDK języka Python:

1. Utwórz pulę i określ jej konfigurację.
1. Dodaj pulę do klienta usługi.
1. Włącz automatyczne skalowanie w puli przy użyciu formuły, która zostanie napisana.

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
> Więcej przykładów użycia zestawu SDK języka Python można znaleźć w repozytorium Szybki start [usługi Batch dla języka Python w](https://github.com/Azure-Samples/batch-python-quickstart) witrynie GitHub.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Włączanie skalowania automatycznego w istniejącej puli

Każdy zestaw SDK usługi Batch umożliwia włączenie automatycznego skalowania. Na przykład:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Włączanie automatycznego skalowania w puli (interfejs](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) API REST)

Po włączeniu skalowania automatycznego w istniejącej puli należy pamiętać o:

- Jeśli skalowanie automatyczne jest obecnie wyłączone w puli, podczas wydawania żądania należy określić prawidłową formułę autoskalowania. Opcjonalnie możesz określić interwał automatycznego skalowania. Jeśli nie określisz interwału, zostanie użyta wartość domyślna 15 minut.
- Jeśli skalowanie automatyczne jest obecnie włączone w puli, możesz określić nową formułę, nowy interwał lub oba te ustawienia. Należy określić co najmniej jedną z tych właściwości.
  - Jeśli określisz nowy interwał automatycznego skalowania, istniejący harmonogram zostanie zatrzymany i zostanie uruchomiony nowy harmonogram. Godzina rozpoczęcia nowego harmonogramu to godzina, o której wydano żądanie włączenia skalowania automatycznego.
  - W przypadku pominięcia formuły lub interwału autoskalowania usługa Batch będzie nadal używać bieżącej wartości tego ustawienia.

> [!NOTE]
> Jeśli określono wartości parametrów *targetDedicatedNodes* lub *targetLowPriorityNodes* metody **CreatePool** podczas tworzenia puli na platformie .NET lub dla porównywalnych parametrów w innym języku, te wartości są ignorowane podczas obliczania formuły skalowania automatycznego.

W tym przykładzie w języku C# użyto biblioteki [.NET usługi Batch,](/dotnet/api/microsoft.azure.batch) aby włączyć skalowanie automatyczne w istniejącej puli.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizowanie formuły autoskalowania

Aby zaktualizować formułę w istniejącej puli z obsługą skalowania automatycznego, wywołaj operację , aby ponownie włączyć skalowanie automatyczne przy użyciu nowej formuły. Jeśli na przykład autoskalowanie jest już włączone po wykonaniu poniższego kodu .NET, jego formuła autoskalowania jest zastępowana `myexistingpool` zawartością . `myNewFormula`

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizowanie interwału autoskalowania

Aby zaktualizować interwał oceny autoskalowania istniejącej puli z obsługą skalowania automatycznego, wywołaj operację , aby ponownie włączyć skalowanie automatyczne z nowym interwałem. Aby na przykład ustawić interwał oceny autoskalowania na 60 minut dla puli, dla których włączono już skalowanie automatyczne na platformie .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Ocena formuły autoskalowania

Formułę można ocenić przed zastosowaniem jej do puli. Dzięki temu można przetestować wyniki formuły przed jej włożyciem do produkcji.

Aby można było ocenić formułę autoskalowania, należy najpierw włączyć skalowanie automatyczne w puli przy użyciu prawidłowej formuły, takiej jak jedno wierszowa formuła `$TargetDedicatedNodes = 0` . Następnie użyj jednej z następujących wartości, aby ocenić formułę, którą chcesz przetestować:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) lub [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Te metody .NET usługi Batch wymagają identyfikatora istniejącej puli i ciągu zawierającego formułę autoskalowania do oceny.

- [Ocena formuły automatycznego skalowania](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    W tym żądaniu interfejsu API REST określ identyfikator puli w identyfikatorze URI i formułę autoskalowania w elemencie *autoScaleFormula* treści żądania. Odpowiedź operacji zawiera wszelkie informacje o błędzie, które mogą być związane z formułą.

Ten [przykład usługi Batch dla programu .NET](/dotnet/api/microsoft.azure.batch) oblicza formułę autoskalowania. Jeśli pula nie używa jeszcze skalowania automatycznego, najpierw ją włączymy.

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

Pomyślna ocena formuły pokazanej w tym fragmencie kodu daje wyniki podobne do:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Uzyskiwanie informacji o przebiegach autoskalowania

Aby upewnić się, że formuła działa zgodnie z oczekiwaniami, zalecamy okresowe sprawdzanie wyników przebiegów skalowania automatycznego, które usługa Batch wykonuje w puli. W tym celu pobierz (lub odśwież) odwołanie do puli, a następnie sprawdź właściwości jej ostatniego uruchomienia skalowania automatycznego.

W przypadku platformy .NET usługi Batch właściwość [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) ma kilka właściwości, które zawierają informacje o najnowszym uruchomieniu automatycznego skalowania wykonywanego w puli:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

W interfejsie API REST funkcja [Pobierz](/rest/api/batchservice/get-information-about-a-pool) informacje o żądaniu puli zwraca informacje o puli, w tym najnowsze informacje o uruchomieniu automatycznego skalowania we właściwości [autoScaleRun.](/rest/api/batchservice/get-information-about-a-pool)

Poniższy przykład w języku C# używa biblioteki .NET usługi Batch do drukowania informacji o ostatnim uruchomieniu skalowania automatycznego w puli _myPool._

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Przykładowe dane wyjściowe z poprzedniego przykładu:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Uzyskiwanie historii uruchamiania autoskalowania przy użyciu zdarzeń automatycznego skalowania puli
Historię automatycznego skalowania można również sprawdzić, [odpytując o wartość PoolAutoScaleEvent.](batch-pool-autoscale-event.md) To zdarzenie jest emitowane przez usługę Batch, aby rejestrować każde wystąpienie oceny i wykonania formuły autoskalowania, co może być pomocne w rozwiązywaniu potencjalnych problemów.

Przykładowe zdarzenie poolAutoScaleEvent:
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

Przyjrzyjmy się kilku formułom, które pokazują różne sposoby dostosowania ilości zasobów obliczeniowych w puli.

### <a name="example-1-time-based-adjustment"></a>Przykład 1: Korekta oparta na czasie

Załóżmy, że chcesz dostosować rozmiar puli na podstawie dnia tygodnia i o porze dnia. W tym przykładzie pokazano, jak odpowiednio zwiększyć lub zmniejszyć liczbę węzłów w puli.

Formuła najpierw uzyskuje bieżący czas. Jeśli jest to dzień tygodnia (1–5) i w godzinach pracy (od 8:00 do 18:00), rozmiar puli docelowej jest ustawiony na 20 węzłów. W przeciwnym razie zostanie ustawiona wartość 10 węzłów.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` Można dostosować, aby odzwierciedlała lokalną strefę czasową, dodając wartość do `time()` `TimeZoneInterval_Hour` i/lub przesunięcia czasu UTC. Na przykład użyj ciągu `$curTime = time() + (-6 * TimeInterval_Hour);` dla czasu letni (MDT, Mountain Daylight Time). Należy pamiętać, że przesunięcie musi zostać dostosowane na początku i na końcu czasu letniego (jeśli ma to zastosowanie).

### <a name="example-2-task-based-adjustment"></a>Przykład 2: Korekta oparta na zadaniach

W tym przykładzie w języku C# rozmiar puli jest dostosowywany na podstawie liczby zadań w kolejce. Do ciągów formuł dołączona jest zarówno komentarze, jak i podziały wierszy.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Przykład 3: Księgowość zadań równoległych

W tym przykładzie w języku C# rozmiar puli jest dostosowywany na podstawie liczby zadań. Ta formuła uwzględnia również wartość [TaskSlotsPerNode,](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) która została ustawiona dla puli. Takie podejście jest przydatne w [sytuacjach,](batch-parallel-node-tasks.md) w których włączono równoległe wykonywanie zadań w puli.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Przykład 4. Ustawianie początkowego rozmiaru puli

W tym przykładzie pokazano przykład w języku C# z formułą autoskalowania, która ustawia rozmiar puli na określoną liczbę węzłów w początkowym okresie. Następnie dostosowuje rozmiar puli na podstawie liczby uruchomionych i aktywnych zadań.

W szczególności ta formuła ma następujące działania:

- Ustawia początkowy rozmiar puli na cztery węzły.
- Nie dostosowuje rozmiaru puli w ciągu pierwszych 10 minut od cyklu życia puli.
- Po upływie 10 minut program uzyskuje maksymalną wartość liczby uruchomionych i aktywnych zadań w ciągu ostatnich 60 minut.
  - Jeśli obie wartości mają wartość 0 (co oznacza, że żadne zadania nie były uruchomione ani aktywne w ciągu ostatnich 60 minut), rozmiar puli jest ustawiony na 0.
  - Jeśli któraś z wartości jest większa niż zero, żadna zmiana nie jest zmieniana.

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

- Dowiedz się, jak [jednocześnie wykonywać wiele zadań w węzłach obliczeniowych w puli.](batch-parallel-node-tasks.md) Oprócz skalowania automatycznego może to pomóc w obniżce czasu trwania zadań w przypadku niektórych obciążeń, co pozwala zaoszczędzić pieniądze.
- Dowiedz się, [jak wydajnie Azure Batch usługę zarządzania,](batch-efficient-list-queries.md) aby uzyskać dalszą wydajność.
