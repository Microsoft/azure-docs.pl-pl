---
title: Azure Monitor konfiguracji monitorów kondycji kontenerów | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące szczegółowej konfiguracji monitorów kondycji w Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80055697"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Przewodnik konfigurowania monitora kondycji kontenerów Azure Monitor

Monitory to podstawowy element służący do mierzenia kondycji i wykrywania błędów w Azure Monitor dla kontenerów. Ten artykuł ułatwia zrozumienie koncepcji mierzonych kondycji oraz elementów wchodzących w skład modelu kondycji do monitorowania i raportowania kondycji klastra Kubernetes za pomocą funkcji [kondycji (wersja zapoznawcza)](container-insights-health.md) .

>[!NOTE]
>W tej chwili funkcja kondycji jest w publicznej wersji zapoznawczej.
>

## <a name="monitors"></a>Monitory

Monitor mierzy kondycję pewnego aspektu obiektu zarządzanego. Każdy monitor ma dwa lub trzy stany kondycji. Monitor będzie w jednym i tylko jednym z jego potencjalnych stanów w danym momencie. Gdy monitor ładowany przez agenta kontenera, jest on zainicjowany w dobrej kondycji. Stan zmienia się tylko wtedy, gdy zostaną wykryte określone warunki dla innego stanu.

Ogólna kondycja konkretnego obiektu jest określana na podstawie kondycji każdego z jego monitorów. Ta hierarchia jest przedstawiana w okienku hierarchia kondycji w Azure Monitor dla kontenerów. Zasady tworzenia kondycji są częścią konfiguracji monitorów agregujących.

## <a name="types-of-monitors"></a>Typy monitorów

|Monitorowanie | Opis | 
|--------|-------------|
| Monitor jednostkowy |Monitor jednostkowy mierzy część zasobu lub aplikacji. Może to spowodować sprawdzenie licznika wydajności w celu określenia wydajności zasobu lub jego dostępności. |
|Monitor zagregowany | Zagregowane monitory grupują wiele monitorów, aby zapewnić zagregowany stan kondycji o pojedynczej kondycji. Monitory jednostkowe są zwykle konfigurowane pod określonym monitorem agregującym. Na przykład monitor zagregowany węzła zbiera informacje o stanie użycia procesora CPU w węzłach, wykorzystaniu pamięci i stanie węzła.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Zasady zbiorcze kondycji monitora agregacji

Każdy monitor agregacji definiuje zasady zestawienia kondycji, które są logiką używaną do określania kondycji monitora zagregowanego na podstawie kondycji monitorów w tym obszarze. Możliwe są następujące zasady dotyczące kondycji monitora zagregowanego:

#### <a name="worst-state-policy"></a>Zasady najgorszego stanu

Stan monitora zagregowanego jest zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji. Jest to najpopularniejsze zasady używane przez monitory agregowane.

![Przykład najgorszego stanu zbiorczej agregacji monitora](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Zasady dotyczące wartości procentowych

Obiekt źródłowy jest zgodny ze najgorszym stanem pojedynczego elementu członkowskiego określonej wartości procentowej obiektów docelowych w najlepszym stanie. Te zasady są używane, gdy określony procent obiektów docelowych musi być w dobrej kondycji, aby obiekt docelowy był traktowany jako w dobrej kondycji. Zasada procentowa sortuje monitory w kolejności malejącej o ważności stanu, a stan monitora agregacji jest obliczany jako najgorszy stan N% (N jest określany przez parametr konfiguracji *StateThresholdPercentage*).

Załóżmy na przykład, że istnieje pięć wystąpień kontenera obrazu kontenera, a ich poszczególne Stany są **krytyczne**, **ostrzegawcze** **, w**dobrej kondycji **, w dobrej** **kondycji.**  Stan monitora użycia procesora CPU kontenerów będzie **krytyczny**, ponieważ najgorszy stan 90% kontenerów ma **krytyczne znaczenie** w kolejności malejącej według ważności.

## <a name="understand-the-monitoring-configuration"></a>Omówienie konfiguracji monitorowania

Azure Monitor kontenerów zawiera kilka kluczowych scenariuszy monitorowania, które są skonfigurowane w następujący sposób.

### <a name="unit-monitors"></a>Monitory jednostkowe

|**Nazwa monitora** | Typ monitora | **Opis** | **Konstruktora** | **Wartościami** |
|-----------------|--------------|-----------------|---------------|-----------|
|Użycie pamięci przez węzeł |Monitor jednostkowy |Ten monitor szacuje użycie pamięci przez węzeł co minutę przy użyciu danych raportowanych przez cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Użycie procesora CPU przez węzeł |Monitor jednostkowy |Ten monitor sprawdza użycie procesora CPU przez węzeł co minutę przy użyciu danych raportowanych przez cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Stan węzła |Monitor jednostkowy |Ten monitor sprawdza warunki węzła zgłoszone przez Kubernetes.<br> Obecnie sprawdzane są następujące warunki węzła: ciśnienie dysku, wykorzystanie pamięci, ciśnienie PID, brak dysku, niedostępność sieci, stan gotowości dla węzła.<br> Jeśli z powyższych **warunków nie jest**dostępny żaden *dysk* lub sieć jest *niedostępna* , stan monitora zmieni się na **krytyczny** .<br> Jeśli jakiekolwiek inne warunki są równe **true**, inne niż stan **gotowości** , monitor zmienia stan na **ostrzegawczy** . | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Użycie pamięci kontenera |Monitor jednostkowy |Ten monitor zgłasza łączny stan kondycji wykorzystania pamięci (RSS) wystąpień kontenera.<br> Wykonuje proste porównanie, które porównuje każdy przykład do pojedynczego progu i określonego przez parametr konfiguracji **ConsecutiveSamplesForStateTransition**.<br> Jego stan jest obliczany jako najgorszy stan 90% wystąpień kontenera (StateThresholdPercentage), posortowany w kolejności malejącej o ważności stanu kondycji kontenera (to znaczy krytyczne, ostrzegawcze, w dobrej kondycji).<br> Jeśli żaden rekord nie zostanie odebrany z wystąpienia kontenera, stan kondycji wystąpienia kontenera jest raportowany jako **nieznany**i ma wyższy priorytet w kolejności sortowania względem stanu **krytycznego** .<br> Każdy stan poszczególnych wystąpień kontenera jest obliczany przy użyciu progów określonych w konfiguracji. Jeśli użycie przekracza próg krytyczny (90%), wystąpienie jest w stanie **krytycznym** , jeśli jest mniejsze niż próg krytyczny (90%) ale większym niż próg ostrzeżenia (80%), wystąpienie jest w stanie **ostrzegawczym** . W przeciwnym razie jest w **dobrej kondycji** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Użycie procesora CPU kontenera |Monitor jednostkowy |Ten monitor zgłasza łączny stan kondycji użycia procesora CPU przez wystąpienia kontenera.<br> Wykonuje proste porównanie, które porównuje każdy przykład do pojedynczego progu i określonego przez parametr konfiguracji **ConsecutiveSamplesForStateTransition**.<br> Jego stan jest obliczany jako najgorszy stan 90% wystąpień kontenera (StateThresholdPercentage), posortowany w kolejności malejącej o ważności stanu kondycji kontenera (to znaczy krytyczne, ostrzegawcze, w dobrej kondycji).<br> Jeśli żaden rekord nie zostanie odebrany z wystąpienia kontenera, stan kondycji wystąpienia kontenera jest raportowany jako **nieznany**i ma wyższy priorytet w kolejności sortowania względem stanu **krytycznego** .<br> Każdy stan poszczególnych wystąpień kontenera jest obliczany przy użyciu progów określonych w konfiguracji. Jeśli użycie przekracza próg krytyczny (90%), wystąpienie jest w stanie **krytycznym** , jeśli jest mniejsze niż próg krytyczny (90%) ale większym niż próg ostrzeżenia (80%), wystąpienie jest w stanie **ostrzegawczym** . W przeciwnym razie jest w **dobrej kondycji** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Gotowe do obciążenia systemowe zasobniki |Monitor jednostkowy |Ten monitor zgłasza stan na podstawie procentu wartości w stanie gotowe w danym obciążeniu. Jego stan jest ustawiony na **krytyczny** , jeśli mniej niż 100% liczby zasobników jest w **dobrej kondycji** |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Stan interfejsu API polecenia |Monitor jednostkowy |Ten monitor zgłasza stan usługi interfejsu API polecenia. Monitor jest w stanie krytycznym w przypadku, gdy punkt końcowy interfejsu API polecenia jest niedostępny. Dla tego określonego monitora stan jest określany przez utworzenie zapytania do punktu końcowego "nodes" dla serwera polecenia-API. Każdy inny niż kod odpowiedzi OK zmienia stan monitora na **krytyczny** . | Brak właściwości konfiguracji |||

### <a name="aggregate-monitors"></a>Monitory zagregowane

|**Nazwa monitora** | **Opis** | **Algorytm** |
|-----------------|-----------------|---------------|
|Węzeł |Ten monitor jest zagregowany ze wszystkich monitorów węzłów. Jest on zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji:<br> Użycie procesora CPU przez węzeł<br> Użycie pamięci przez węzeł<br> Stan węzła | Najgorszy z|
|Pula węzłów |Ten monitor zgłasza stan połączonej kondycji wszystkich węzłów w puli węzłów *nieznanej obiektu agentpool*. Jest to trójstanowy Monitor stanu, którego stan jest oparty na najgorszym stanie 80% węzłów w puli węzłów, posortowanych w kolejności malejącej ważności Stanów węzłów (to jest, krytyczne, ostrzeżenie, w dobrej kondycji).|Procentowe |
|Węzły (element nadrzędny puli węzłów) |Jest to zagregowany monitor wszystkich pul węzłów. Jego stan jest oparty na najgorszym stanie jego monitorów podrzędnych (czyli pul węzłów znajdujących się w klastrze). |Najgorszy z |
|Klaster (element nadrzędny węzłów/<br> Infrastruktura Kubernetes) |Jest to monitor nadrzędny, który jest zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji, który jest Kubernetes infrastrukturą i węzłami. |Najgorszy z |
|Infrastruktura Kubernetes |Ten monitor zgłasza łączny stan kondycji składników infrastruktury zarządzanej w klastrze. jego stan jest obliczany jako wartość "najgorszy" Stanów monitora podrzędnego, np. obciążeń systemu polecenia i stanu serwera interfejsu API. |Najgorszy z|
|Obciążenie systemu |Ten monitor zgłasza stan kondycji obciążenia systemu polecenia. Ten monitor jest zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji, czyli **zasobników w stanie gotowe** (monitor i kontenery w obciążeniu). |Najgorszy z |
|Kontener |Ten monitor zgłasza ogólny stan kondycji kontenera w danym obciążeniu. Ten monitor jest zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji, który jest monitorem użycia **procesora** i **wykorzystania pamięci** . |Najgorszy z |

## <a name="next-steps"></a>Następne kroki

Wyświetl [monitorowanie kondycji klastra](container-insights-health.md) , aby dowiedzieć się więcej na temat wyświetlania stanu kondycji klastra Kubernetes.
