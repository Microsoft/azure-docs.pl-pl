---
title: Kubernetes monitorowanie za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób wyświetlania i analizowania wydajności klastra Kubernetes za pomocą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: de61e8e5b2716a3ca212a0a830a4d48b8bd2c3ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011085"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorowanie wydajności klastra Kubernetes za pomocą Azure Monitor dla kontenerów

Za pomocą Azure Monitor dla kontenerów można użyć wykresów wydajności i stanu kondycji do monitorowania obciążenia klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), Azure Stack lub innych środowiskach z dwóch perspektyw. Można monitorować bezpośrednio z klastra lub wyświetlać wszystkie klastry w ramach subskrypcji z Azure Monitor. Przeglądanie Azure Container Instances jest również możliwe podczas monitorowania określonego klastra AKS.

Ten artykuł pomaga zrozumieć dwie perspektywy i jak Azure Monitor pomaga szybko ocenić, zbadać i rozwiązać wykryte problemy.

Informacje o sposobie włączania Azure Monitor dla kontenerów znajdują się w sekcji Dołączanie [Azure monitor dla kontenerów](container-insights-onboard.md).

Azure Monitor zawiera widok z obsługą wielu klastrów, który pokazuje stan kondycji wszystkich monitorowanych klastrów Kubernetes z systemem Linux i Windows Server 2019 wdrożonych w grupach zasobów w Twoich subskrypcjach. Pokazuje ona klastry odnalezione we wszystkich środowiskach, które nie są monitorowane przez rozwiązanie. Możesz natychmiast zrozumieć kondycję klastra, a w tym miejscu możesz przejść do szczegółów na stronie wydajność węzła i kontrolera lub przejść do sekcji wykresy wydajności dla klastra. W przypadku klastrów AKS, które zostały odnalezione i zidentyfikowane jako niemonitorowane, można je włączyć w dowolnym momencie.

Główne różnice w monitorowaniu klastra systemu Windows Server z Azure Monitor dla kontenerów w porównaniu z klastrem z systemem Linux [zostały opisane w](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) artykule Omówienie.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Widok z obsługą wiele klastrów z Azure Monitor

Aby wyświetlić stan kondycji wszystkich wdrożonych klastrów Kubernetes, wybierz pozycję **monitor** w lewym okienku w Azure Portal. W sekcji **szczegółowe informacje** wybierz pozycję **kontenery**.

![Przykład Azure Monitor pulpitu nawigacyjnego z obsługą kilku klastrów](./media/container-insights-analyze/azmon-containers-multiview.png)

Można zakres wyników przedstawionych w siatce, aby pokazać klastry, które są:

* Klastry **Azure** -AKS i AKS-Engine hostowane w usłudze Azure Kubernetes Service
* **Azure Stack (wersja zapoznawcza)** — AKS-Engine klastrów hostowanych w Azure Stack
* **Platformy inne niż Azure (wersja zapoznawcza)** — klastry Kubernetes hostowane lokalnie
* **Wszystkie** — Wyświetl wszystkie klastry Kubernetes hostowane na platformie Azure, Azure Stack i środowiskach lokalnych, które są dołączane do Azure monitor dla kontenerów

Aby wyświetlić klastry z określonego środowiska, wybierz je ze **środowisk** pill w lewym górnym rogu strony.

![Przykład pill środowiska](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na karcie **monitorowane klastry** przedstawiono następujące informacje:

- Liczba klastrów w stanie krytycznym lub złej kondycji, a w przypadku których wiele jest w dobrej kondycji lub nie jest zgłaszanych (nazywanych nieznanym stanem).
- Czy wszystkie wdrożenia [aparatu Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) są w dobrej kondycji.
- Liczba węzłów i zasobników użytkowników i systemów wdrożonych na klaster.
- Ilość dostępnego miejsca na dysku oraz ewentualny problem z pojemnością.

Uwzględnione są następujące stany kondycji:

* **Dobra kondycja**: nie wykryto żadnych problemów dla maszyny wirtualnej i działa ona zgodnie z wymaganiami.
* **Krytyczny**: wykryto co najmniej jeden krytyczny problem, który należy rozwiązać, aby przywrócić normalny stan działania zgodnie z oczekiwaniami.
* **Ostrzeżenie**: wykryto co najmniej jeden problem, który musi zostać rozwiązany lub kondycja może stać się krytyczna.
* **Nieznane**: Jeśli usługa nie mogła nawiązać połączenia z węzłem lub pod, stan zmieni się na nieznany.
* **Nie znaleziono**: Usunięto obszar roboczy, grupę zasobów lub subskrypcję zawierającą obszar roboczy tego rozwiązania.
* **Nieautoryzowane**: użytkownik nie ma wymaganych uprawnień do odczytu danych w obszarze roboczym.
* **Błąd**: Wystąpił błąd podczas próby odczytania danych z obszaru roboczego.
* **Błędna konfiguracja**: Azure monitor dla kontenerów nie zostały poprawnie skonfigurowane w określonym obszarze roboczym.
* **Brak danych**: dane nie zostały zgłoszone do obszaru roboczego w ciągu ostatnich 30 minut.

Stan kondycji oblicza ogólny stan klastra jako *najgorszy* z trzech stanów z jednym wyjątkiem. Jeśli którykolwiek z trzech stanów jest nieznany, ogólny stan klastra pokazuje **nieznane**.

W poniższej tabeli przedstawiono podział obliczeń kontrolujących Stany kondycji monitorowanego klastra w widoku wiele klastrów.

| Monitorowany klaster |Stan |Dostępność |
|-------|-------|-----------------|
|**Użytkownik pod**| | |
| |Dobra kondycja |100% |
| |Ostrzeżenie |90 – 99% |
| |Krytyczne |<90% |
| |Nieznany |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |
|**System pod**| | |
| |Dobra kondycja |100% |
| |Ostrzeżenie |Nie dotyczy |
| |Krytyczne |<100% |
| |Nieznany |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |
|**Węzeł** | | |
| |Dobra kondycja |>85% |
| |Ostrzeżenie |60 – 84% |
| |Krytyczne |<60% |
| |Nieznany |Jeśli nie zgłoszono w ciągu ostatnich 30 minut |

Z listy klastrów możesz przejść do szczegółów na stronie **klaster** , wybierając nazwę klastra. Następnie przejdź do strony wydajność **węzłów** , wybierając pakiet zbiorczy węzłów w kolumnie **węzły** dla danego klastra. Możesz też przejść do szczegółów na stronie wydajność **kontrolerów** , wybierając zestawienie z kolumną **zasobników użytkownika** lub **system** .

## <a name="view-performance-directly-from-a-cluster"></a>Wyświetlanie wydajności bezpośrednio z klastra

Dostęp do Azure monitor dla kontenerów jest dostępny bezpośrednio w klastrze AKS przez wybranie klastra usługi **Insights**  >  **Cluster** w okienku po lewej stronie lub po wybraniu klastra z widoku wiele klastrów. Informacje o klastrze są zorganizowane w czterech perspektyw:

- Klaster
- Węzły
- Kontrolery
- Kontenery

>[!NOTE]
>Środowisko opisane w dalszej części tego artykułu dotyczy również wyświetlania stanu wydajności i kondycji klastrów Kubernetes hostowanych w Azure Stack lub innym środowisku w przypadku wybrania z widoku wiele klastrów.

Zostanie otwarta strona domyślna i zostaną wyświetlone cztery liniowe wykresy wydajności, które pokazują kluczowe metryki wydajności klastra.

![Przykładowe wykresy wydajności na karcie klaster](./media/container-insights-analyze/containers-cluster-perfview.png)

Na wykresach wydajności są wyświetlane cztery metryki wydajności:

- **&nbsp; Użycie % procesora CPU przez węzeł**: zagregowana perspektywa użycia procesora CPU dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie.
- **&nbsp; Użycie % pamięci przez węzeł**: zagregowana perspektywa wykorzystania pamięci dla całego klastra. Aby przefiltrować wyniki dla zakresu czasu, wybierz opcję **średnie**, **minimum**, **pięćdziesiąt**, **90**, **używany 95.** lub **Max** w selektorze percentyly powyżej wykresu. Filtry mogą być używane pojedynczo lub łącznie.
- **Liczba węzłów**: liczba węzłów i stan z Kubernetes. Stanem reprezentowanego węzła klastra są łącznie, gotowe i niegotowe. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu.
- **Liczba aktywnych pod**: liczba i stan z Kubernetes. Stany reprezentowanego obszaru są całkowite, oczekujące, uruchomione, nieznane, zakończone powodzeniem lub niepowodzeniem. Można je filtrować pojedynczo lub łączyć w selektorze powyżej wykresu.

Użyj klawiszy strzałek w lewo i w prawo, aby przechodzić przez każdy punkt danych na wykresie. Użyj klawiszy strzałek w górę i w dół, aby przechodzić do kolejnych wierszy percentylu. Wybierz ikonę pinezki w prawym górnym rogu dowolnego z wykresów, aby przypiąć wybrany wykres do ostatniego wyświetlonego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje Cię do Azure Monitor kontenerów i ładuje prawidłowy zakres i widok.

Azure Monitor for Containers obsługują również [Eksploratora metryk](../platform/metrics-getting-started.md)Azure monitor, w którym można tworzyć własne wykresy wykresów, skorelować i badać trendy oraz przypinać do pulpitów nawigacyjnych. W Eksploratorze metryk można również użyć kryteriów ustawionych do wizualizacji metryk jako podstawy [reguły alertu opartej na metrykach](../platform/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Wyświetlanie metryk kontenera w Eksploratorze metryk

W Eksploratorze metryk można wyświetlić zagregowane metryki dotyczące węzła i użycia z Azure Monitor dla kontenerów. W poniższej tabeli zestawiono szczegółowe informacje ułatwiające zrozumienie sposobu używania wykresów metryk do wizualizacji metryk kontenera.

|Przestrzeń nazw | Metric | Opis |
|----------|--------|-------------|
| Szczegółowe informacje. kontenery/węzły | |
| | cpuUsageMillicores | Zagregowane pomiary użycia procesora CPU w klastrze. Jest to rdzeń procesora CPU podzielony na 1000 jednostek (Milli = 1000). Służy do określania użycia rdzeni w kontenerze, w którym wiele aplikacji może korzystać z jednego rdzenia.|
| | cpuUsagePercentage | Zagregowane średnie użycie procesora CPU wyrażone w procentach w klastrze.|
| | memoryRssBytes | Pamięć RSS kontenera użyta w bajtach.|
| | memoryRssPercentage | Pamięć RSS kontenera użyta w procentach.|
| | memoryWorkingSetBytes | Użyta pamięć zestawu roboczego kontenera.|
| | memoryWorkingSetPercentage | Pamięć zestawu roboczego kontenera użyta w procentach. |
| | nodesCount | Liczba węzłów z Kubernetes.|
| Szczegółowe informacje. kontenery/zasobniki | |
| | PodCount | Liczba pod z Kubernetes.|

Można [podzielić](../platform/metrics-charts.md#apply-splitting-to-a-chart) metrykę, aby wyświetlić ją w wymiarze, i wizualizować, jak różne segmenty są porównywane ze sobą. Dla węzła można podzielić wykres na wymiar *hosta* . Z poziomu usługi można podzielić ją na segmenty według następujących wymiarów:

* Kontroler
* Kubernetes przestrzeń nazw
* Węzeł
* Etap

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizowanie węzłów, kontrolerów i kondycji kontenera

Po przełączeniu do kart **węzły**, **Kontrolery** i **kontenery** okienko właściwości zostanie automatycznie wyświetlone po prawej stronie. Wyświetla właściwości wybranego elementu, w tym etykiety zdefiniowane do organizowania obiektów Kubernetes. Po wybraniu węzła systemu Linux w sekcji **pojemność dysku lokalnego** wyświetlana jest również ilość dostępnego miejsca na dysku oraz wartość procentowa użyta dla każdego dysku prezentowanego w węźle. Wybierz **>>** łącze w okienku, aby wyświetlić lub ukryć okienko.

Po rozwinięciu obiektów w hierarchii okienko właściwości jest aktualizowane w zależności od wybranego obiektu. W okienku można także wyświetlać dzienniki kontenerów Kubernetes (stdout/stderr), zdarzenia i metryki pod, wybierając link **Wyświetl dane na żywo (wersja zapoznawcza)** w górnej części okienka. Aby uzyskać więcej informacji na temat konfiguracji wymaganej do udzielenia i kontroli dostępu do wyświetlania tych danych, zobacz [Konfigurowanie danych na żywo (wersja zapoznawcza)](container-insights-livedata-setup.md). Podczas przeglądania zasobów klastra można zobaczyć te dane z kontenera w czasie rzeczywistym. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [jak wyświetlać dzienniki Kubernetes, zdarzenia i metryki pod w czasie rzeczywistym](container-insights-livedata-overview.md). Aby wyświetlić dane dziennika Kubernetes przechowywane w obszarze roboczym w oparciu o wstępnie zdefiniowane przeszukiwania dzienników, wybierz opcję **Wyświetl dzienniki kontenerów** z listy rozwijanej **Widok w analizie** . Aby uzyskać dodatkowe informacje na temat tego tematu, zobacz [dzienniki wyszukiwania w celu przeanalizowania danych](container-insights-log-search.md#search-logs-to-analyze-data).

Użyj opcji **+ Dodaj filtr** w górnej części strony, aby odfiltrować wyniki widoku według **usługi**, **węzła**, **przestrzeni nazw** lub **puli węzłów**. Po wybraniu zakresu filtru wybierz jedną z wartości wyświetlanych w polu **Wybierz wartości** . Po skonfigurowaniu filtru jest on stosowany globalnie podczas wyświetlania dowolnej perspektywy klastra AKS. Formuła obsługuje tylko znak równości. Możesz dodać dodatkowe filtry od pierwszego z nich, aby jeszcze bardziej zawęzić wyniki. Na przykład, jeśli określisz **węzeł** Filtruj według, możesz wybrać tylko **usługę** lub **przestrzeń nazw** dla drugiego filtru.

Określanie filtru na jednej karcie jest kontynuowane po wybraniu innej. Jest ona usuwana po wybraniu symbolu **x** obok określonego filtru.

Przejdź do karty **węzły** i hierarchia wierszy jest zgodna z modelem obiektów Kubernetes, który rozpoczyna się od węzła w klastrze. Rozwiń węzeł, aby wyświetlić jeden lub więcej zasobników uruchomionych w węźle. Jeśli więcej niż jeden kontener jest zgrupowany na pod, są one wyświetlane jako ostatni wiersz w hierarchii. Można także zobaczyć, ile obciążeń związanych z różnymi firmami nie są uruchomione na hoście, Jeśli host ma wykorzystanie procesora lub pamięci.

![Przykład hierarchii węzłów Kubernetes w widoku wydajności](./media/container-insights-analyze/containers-nodes-view.png)

Kontenery systemu Windows Server, na których działa system operacyjny Windows Server 2019, są wyświetlane po wszystkich węzłach na liście z systemem Linux. Po rozwinięciu węzła systemu Windows Server można wyświetlić jeden lub więcej zasobników i kontenerów, które są uruchamiane w węźle. Po zaznaczeniu węzła w okienku właściwości zostanie wyświetlona informacja o wersji.

![Przykładowa hierarchia węzłów z węzłami systemu Windows Server na liście](./media/container-insights-analyze/nodes-view-windows.png)

Azure Container Instances węzły wirtualne z systemem operacyjnym Linux są wyświetlane po ostatnim węźle klastra AKS na liście. Po rozszerzeniu Container Instances węzła wirtualnego można wyświetlić jeden lub więcej Container Instances i zasobników i kontenerów, które są uruchamiane w węźle. Metryki nie są zbierane i raportowane dla węzłów tylko dla zasobników.

![Przykładowa hierarchia węzłów z Container Instances na liście](./media/container-insights-analyze/nodes-view-aci.png)

Z rozwiniętego węzła możesz przejść do szczegółów z lub kontenera, który jest uruchamiany w węźle, do kontrolera, aby wyświetlić dane wydajności odfiltrowane dla tego kontrolera. Wybierz wartość w kolumnie **kontroler** dla określonego węzła.

![Zrzut ekranu przedstawia przechodzenie do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-node-controller.png)

Wybierz pozycję Kontrolery lub kontenery w górnej części strony, aby przejrzeć stan i wykorzystanie zasobów dla tych obiektów. Aby przejrzeć użycie pamięci, na liście rozwijanej **Metryka** wybierz pozycję **pamięć RSS** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwana tylko dla Kubernetes w wersji 1,8 lub nowszej. W przeciwnym razie można wyświetlić wartości **dla &nbsp; % minimum** jako *NaN &nbsp; %*, czyli liczbowej wartości typu danych, która reprezentuje niezdefiniowaną lub niereprezentującą wartość.

![Widok wydajności węzłów kontenera](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Zestaw roboczy pamięci** pokazuje zawartą pamięć i pamięć wirtualną (pamięć podręczną) oraz łączną zawartość używaną przez aplikację. W obszarze **RSS pamięci** jest wyświetlana tylko pamięć główna (czyli Nothing, ale w innych wyrazach). Ta Metryka przedstawia rzeczywistą pojemność dostępnej pamięci. Jaka jest różnica między pamięcią rezydentną a pamięcią wirtualną?

- Pamięć rezydentna lub pamięć główna to rzeczywista ilość pamięci maszyny dostępna dla węzłów klastra.

- Pamięć wirtualna jest zarezerwowaną ilością miejsca na dysku twardym (pamięć podręczna) używaną przez system operacyjny do wymiany danych z pamięci na dysk, gdy jest on używany, a następnie pobiera z powrotem do pamięci, jeśli jest to konieczne.

Domyślnie dane wydajności bazują na ostatnich sześciu godzinach, ale można zmienić okno przy użyciu opcji **TimeRange** w lewym górnym rogu. Można również filtrować wyniki w zakresie czasu, wybierając wartość **min**, **AVG**, **pięćdziesiąt**, **90**, **używany 95.** i **Max** w selektorze percentylu.

![Zaznaczenie percentylu do filtrowania danych](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Po umieszczeniu wskaźnika myszy na wykresie słupkowym w kolumnie **trend** każdy pasek pokazuje użycie procesora lub pamięci, w zależności od tego, która Metryka została wybrana, w ciągu 15 minut. Po wybraniu wykresu trendu za pomocą klawiatury Użyj klawisza Alt + Page Up lub klawisza Alt + Page Down, aby przełączać poszczególne paski osobno. Te same szczegóły można uzyskać po umieszczeniu wskaźnika myszy na pasku.

![Przykład aktywowania wykresu słupkowego trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

W następnym przykładzie dla pierwszego węzła na liście *AKS-nodepool1-*, wartość dla **kontenerów** wynosi 9. Ta wartość to pakiet zbiorczy całkowitej liczby wdrożonych kontenerów.

![Zestawienie kontenerów — przykład na węzeł](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Te informacje mogą pomóc w szybkim ustaleniu, czy masz odpowiednie równoważenie kontenerów między węzłami w klastrze.

Informacje przedstawione podczas wyświetlania karty **węzły** są opisane w poniższej tabeli.

| Kolumna | Opis |
|--------|-------------|
| Nazwa | Nazwa hosta. |
| Stan | Widok Kubernetes stanu węzła. |
| Minimum &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;%  | Średni procent węzła w oparciu o percentyl w wybranym czasie. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Średnia wartość rzeczywista węzłów oparta na percentylu podczas wybranego czasu trwania. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla węzła. W przypadku zasobników i kontenerów jest to średnia wartość raportowana przez hosta. |
| Kontenery | Liczba kontenerów. |
| Czas pracy | Przedstawia czas od momentu uruchomienia lub ponownego uruchomienia węzła. |
| Kontroler | Tylko dla kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. Nie wszystkie zasobniki znajdują się w kontrolerze, dlatego niektóre mogą wyświetlać **N/a**. |
| Minimum trendu &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;% | Trend wykresu słupkowego reprezentuje procentową metrykę percentylości kontrolera. |

Obciążenie może być zauważalne po rozwinięciu węzła o nazwie **inny proces**. Reprezentuje procesy niekontenerowe, które są uruchamiane w węźle, i zawiera:

* Samozarządzane lub zarządzane Kubernetes procesy bez kontenera

* Procesy czasu wykonywania kontenera

* Kubelet

* Procesy systemowe uruchomione w węźle

* Inne obciążenia inne niż Kubernetes uruchomione na sprzęcie lub maszynie wirtualnej węzła

Jest on obliczany przez: *całkowite użycie z CAdvisor*  -  *użycie z procesu kontenera*.

W selektorze wybierz pozycję **Kontrolery**.

![Wybierz widok kontrolerów](./media/container-insights-analyze/containers-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów, Container Instances kontrolery węzłów wirtualnych lub zasobniki wirtualnego węzła nie są połączone z kontrolerem.

![\<Nazwa> kontroleruje widok wydajności](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchia wierszy rozpoczyna się od kontrolera. Po rozszerzeniu kontrolera należy wyświetlić jeden lub więcej zasobników. Rozwiń węzeł pod, a w ostatnim wierszu jest wyświetlany kontener pogrupowany pod względem elementu. Na rozszerzonym kontrolerze możesz przejść do węzła, w którym jest uruchomiona, aby wyświetlić dane wydajności odfiltrowane dla tego węzła. Container Instances z kontrolerami nie połączono z kontrolerem na liście poniżej.

![Przykładowa hierarchia kontrolerów z Container Instancesymi identyfikatorami na liście](./media/container-insights-analyze/controllers-view-aci.png)

Wybierz wartość w kolumnie **węzeł** dla określonego kontrolera.

![Przykład przechodzenia do szczegółów z węzła do kontrolera w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas przeglądania kontrolerów są opisane w poniższej tabeli.

| Kolumna | Opis |
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan zbiorczy kontenerów po zakończeniu działania z stanem takim jak *OK*, *przerwany*, *Niepowodzenie*, *zatrzymano* lub *wstrzymano*. Jeśli kontener jest uruchomiony, ale stan nie był prawidłowo wyświetlany lub nie został pobrany przez agenta i nie odpowiedział przez dłużej niż 30 minut, stan jest *nieznany*. Dodatkowe szczegóły ikony stanu znajdują się w poniższej tabeli.|
| Minimum &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;%| Średnia Zbiorcza średniej wartości procentowej każdej jednostki dla wybranej metryki i percentylu. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max  | Rzutowanie średniej millicore procesora CPU lub wydajności pamięci kontenera dla wybranego percentylu. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla elementu. |
| Kontenery | Łączna liczba kontenerów dla kontrolera lub pod. |
| Uruchamiania | Zestawienie liczby ponownych uruchomień z kontenerów. |
| Czas pracy | Przedstawia czas od momentu rozpoczęcia kontenera. |
| Węzeł | Tylko dla kontenerów i zasobników. Pokazuje, który kontroler znajduje się w. |
| Minimum trendu &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;% | Trend wykresu słupkowego przedstawia średnią metrykę percentylu kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów.

| Ikona | Stan |
|--------|-------------|
| ![Ikona stanu gotowości do uruchomienia](./media/container-insights-analyze/containers-ready-icon.png) | Uruchomione (gotowe)|
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|
| ![Ikona ostatnio zgłoszonego stanu uruchomienia](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie udzielono odpowiedzi przez ponad 30 minut|
| ![Ikona stanu pomyślnego](./media/container-insights-analyze/containers-green-icon.png) | Zatrzymanie zostało zatrzymane lub zakończyło się niepowodzeniem|

Ikona stanu wyświetla liczbę w zależności od tego, co zapewnia. Pokazuje najgorsze dwa stany, a po umieszczeniu wskaźnika myszy na stanie zostanie wyświetlony stan zestawienia ze wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, zostanie wyświetlona wartość stanu **(0)**.

W selektorze wybierz pozycję **Containers (kontenery**).

![Wybierz widok kontenerów](./media/container-insights-analyze/containers-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów platformy Azure Kubernetes i Azure Container Instances.

![\<Nazwa> — widok wydajności kontenerów](./media/container-insights-analyze/containers-containers-view.png)

Z kontenera można przejść do szczegółów na węzeł lub, aby wyświetlić dane wydajności odfiltrowane dla tego obiektu. Wybierz wartość w kolumnie **pod** lub **węzeł** dla określonego kontenera.

![Przykład przechodzenia do szczegółów z węzła do kontenerów w widoku wydajności](./media/container-insights-analyze/drill-down-controller-node.png)

Informacje wyświetlane podczas wyświetlania kontenerów są opisane w poniższej tabeli.

| Kolumna | Opis |
|--------|-------------|
| Nazwa | Nazwa kontrolera.|
| Stan | Stan kontenerów (jeśli istnieją). Dodatkowe szczegóły ikony stanu znajdują się w następnej tabeli.|
| Minimum &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;% | Pakiet zbiorczy średniej wartości procentowej każdej jednostki dla wybranej metryki i percentylu. |
| Minimum, AVG, pięćdziesiąt, 90, używany 95., Max | Rzutowanie średniej millicore procesora CPU lub wydajności pamięci kontenera dla wybranego percentylu. Średnia wartość jest mierzona na podstawie limitu procesora CPU/pamięci ustawionego dla elementu. |
| Pod | Kontener, w którym znajduje się pod.|
| Węzeł |  Węzeł, w którym znajduje się kontener. |
| Uruchamiania | Przedstawia czas od momentu rozpoczęcia kontenera. |
| Czas pracy | Reprezentuje godzinę uruchomienia lub ponownego uruchomienia kontenera. |
| Minimum trendu &nbsp; %, średnia &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, używany 95. &nbsp; %, maksimum&nbsp;% | Trend wykresu słupkowego reprezentuje procentową metrykę percentylu kontenera. |

Ikony w polu Stan wskazują stan online, zgodnie z opisem w poniższej tabeli.

| Ikona | Stan |
|--------|-------------|
| ![Ikona stanu gotowości do uruchomienia](./media/container-insights-analyze/containers-ready-icon.png) | Uruchomione (gotowe)|
| ![Ikona stanu oczekiwania lub wstrzymania](./media/container-insights-analyze/containers-waiting-icon.png) | Oczekiwanie lub wstrzymane|
| ![Ikona ostatnio zgłoszonego stanu uruchomienia](./media/container-insights-analyze/containers-grey-icon.png) | Ostatnio zgłoszone uruchomione, ale nie udzielono odpowiedzi w ciągu ponad 30 minut|
| ![Ikona stanu przerwania](./media/container-insights-analyze/containers-terminated-icon.png) | Zatrzymanie zostało zatrzymane lub zakończyło się niepowodzeniem|
| ![Ikona stanu niepowodzenia](./media/container-insights-analyze/containers-failed-icon.png) | Stan niepowodzenia |

## <a name="monitor-and-visualize-network-configurations"></a>Monitorowanie i wizualizacja konfiguracji sieci
Menedżer zasad sieciowych platformy Azure zawiera Prometheus metryki, które umożliwiają monitorowanie i lepsze zrozumienie konfiguracji sieci. Udostępnia wbudowane wizualizacje w Azure Portal lub Grafana Labs. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie i wizualizacja konfiguracji sieci za pomocą usługi Azure npm](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm).


## <a name="workbooks"></a>Skoroszyty

Skoroszyty łączą tekst, [kwerendy dzienników](/azure/data-explorer/kusto/query/), [metryki](../platform/data-platform-metrics.md)i parametry w rozbudowanych raportach interaktywnych. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Azure Monitor kontenerów zawiera cztery skoroszyty umożliwiające rozpoczęcie pracy:

- **Pojemność dysku**: przedstawia wykresy interaktywnego użycia dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Procent użycia dysku dla wszystkich dysków.
    - Wolne miejsce na dysku dla wszystkich dysków.
    - Siatka pokazująca dysk każdego węzła, jego procent zajętego miejsca, Trend procentu zajętego miejsca, wolne miejsce na dysku (GiB) i trend wolnego miejsca na dysku (GiB). Gdy wiersz jest zaznaczony w tabeli, procent zajętego miejsca i wolnego miejsca na dysku (GiB) jest pokazywany poniżej wiersza.

- **We/wy dysku**: przedstawia wykresy interaktywnego wykorzystania dysku dla każdego dysku prezentowanego w węźle w kontenerze przez następujące perspektywy:

    - Dyskowe operacje we/wy są sumowane na wszystkich dyskach przez odczyt bajtów/s, liczba bajtów/s oraz trendy odczytu i zapisu bajtów/s.
    - Osiem wykresów wydajnościowych przedstawia kluczowe wskaźniki wydajności, które pomagają mierzyć i identyfikować wąskie gardła dyskowych operacji we/wy.

- **Kubelet**: obejmuje dwie siatki pokazujące statystyki operacyjne węzła klucza:

    - Przegląd według siatki węzła podsumowuje całkowitą operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu dla każdego węzła.
    - Przegląd według typu operacji podsumowuje dla każdej operacji łączną operację, łączną liczbę błędów i operacji zakończonych powodzeniem według procentu i trendu.

- **Sieć**: przedstawia wykresy interaktywnego wykorzystania sieci dla każdej karty sieciowej każdego węzła, a siatka przedstawia kluczowe wskaźniki wydajności, które ułatwiają mierzenie wydajności kart sieciowych.

Możesz uzyskać dostęp do tych skoroszytów, wybierając je z listy rozwijanej **Wyświetl skoroszyty** .

![Lista rozwijana Wyświetl skoroszyty](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z tematem [tworzenie alertów wydajności za pomocą Azure monitor dla kontenerów](./container-insights-log-alerts.md) , aby dowiedzieć się, jak tworzyć alerty dotyczące wysokiego użycia procesora i pamięci w celu obsługi procesów i procedur operacyjnych DevOps.

- Wyświetl [przykłady zapytań dotyczących dzienników](container-insights-log-search.md#search-logs-to-analyze-data) , aby wyświetlić wstępnie zdefiniowane zapytania i przykłady do oszacowania lub dostosowania do alertów, wizualizacji lub analizowania klastrów.

- Wyświetl [monitorowanie kondycji klastra](./container-insights-overview.md) , aby dowiedzieć się więcej na temat wyświetlania stanu kondycji klastra Kubernetes.