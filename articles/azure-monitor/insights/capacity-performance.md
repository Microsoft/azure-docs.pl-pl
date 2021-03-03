---
title: Capacity and Performance rozwiązanie w Azure Monitor | Microsoft Docs
description: Użyj rozwiązania Capacity and Performance w monitorze, aby ułatwić zrozumienie pojemności serwerów funkcji Hyper-V.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704297"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Zaplanuj pojemność maszyny wirtualnej funkcji Hyper-V za pomocą rozwiązania Capacity and Performance (przestarzałe)

![Symbol Capacity and Performance](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Rozwiązanie Capacity and Performance zostało wycofane.  Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać, ale nie można dodać Capacity and Performance do żadnych nowych obszarów roboczych.

Możesz użyć rozwiązania Capacity and Performance w monitorze, aby ułatwić zrozumienie pojemności serwerów funkcji Hyper-V. Rozwiązanie zapewnia wgląd w środowisko funkcji Hyper-V, wyświetlając całkowite wykorzystanie (procesor, pamięć i dysk) hostów oraz maszyny wirtualne działające na tych hostach funkcji Hyper-V. Metryki są zbierane dla procesora CPU, pamięci i dysków na wszystkich hostach i działających na nich maszynach wirtualnych.

Rozwiązanie:

-   Pokazuje hosty o największym i najniższym wykorzystaniu procesora i pamięci
-   Pokazuje maszyny wirtualne o najwyższej i najniższym wykorzystaniu procesora i pamięci
-   Pokazuje maszyny wirtualne o najwyższej i najniższej liczbie operacji we/wy i przepływności
-   Pokazuje, które maszyny wirtualne są uruchomione na których hostach
-   Pokazuje najpopularniejsze dyski o wysokiej przepływności, IOPS i opóźnieniu w udostępnionych woluminach klastra
- Umożliwia dostosowywanie i filtrowanie na podstawie grup

> [!NOTE]
> Poprzednia wersja rozwiązania Capacity and Performanceowego o nazwie zarządzanie pojemnością wymagała zarówno System Center Operations Manager, jak i System Center Virtual Machine Manager. To zaktualizowane rozwiązanie nie ma tych zależności.


## <a name="connected-sources"></a>Połączone źródła

W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
|---|---|---|
| [Agenci dla systemu Windows](../agents/agent-windows.md) | Tak | Rozwiązanie zbiera informacje o pojemności i wydajności z agentów systemu Windows. |
| [Agenci dla systemu Linux](../vm/quick-collect-linux-computer.md) | Nie    | Rozwiązanie nie zbiera danych o pojemności i wydajności z bezpośrednich agentów systemu Linux.|
| [Grupa zarządzania programu SCOM](../agents/om-agents.md) | Tak |Rozwiązanie zbiera dane o pojemności i wydajności z agentów w połączonej grupie zarządzania programu SCOM. Bezpośrednie połączenie z agentem programu SCOM do Log Analytics nie jest wymagane.|
| [Konto usługi Azure Storage](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Nie | Usługa Azure Storage nie obejmuje danych dotyczących pojemności i wydajności.|

## <a name="prerequisites"></a>Wymagania wstępne

- Agenty systemu Windows lub Operations Manager muszą być zainstalowane na komputerach z systemem Windows Server 2012 lub nowszym, a nie maszyn wirtualnych.


## <a name="configuration"></a>Konfigurowanie

Wykonaj następujące kroki, aby dodać rozwiązanie Capacity and Performance do obszaru roboczego.

- Dodaj Capacity and Performance rozwiązanie do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań log Analytics z Galeria rozwiązań](./solutions.md).

## <a name="management-packs"></a>Pakiety administracyjne

Jeśli grupa zarządzania programu SCOM jest połączona z obszarem roboczym Log Analytics, po dodaniu tego rozwiązania zostaną zainstalowane następujące pakiety administracyjne w programie SCOM. Nie jest wymagana żadna konfiguracja ani obsługa tych pakietów administracyjnych.

- Microsoft. IntelligencePacks. CapacityPerformance

1201 zdarzeń przypomina:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Gdy Capacity and Performance rozwiązanie zostanie zaktualizowane, numer wersji zostanie zmieniony.

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../agents/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="using-the-solution"></a>Użycie rozwiązania

Dodanie rozwiązania Capacity and Performance do obszaru roboczego powoduje dodanie Capacity and Performance do pulpitu nawigacyjnego przegląd. Ten kafelek przedstawia liczbę aktywnych hostów funkcji Hyper-V oraz liczbę aktywnych maszyn wirtualnych monitorowanych w wybranym okresie.

![Kafelek Capacity and Performance](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Przeglądanie wykorzystania

Kliknij kafelek Capacity and Performance, aby otworzyć pulpit nawigacyjny Capacity and Performance. Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera do dziesięciu elementów spełniających jej kryteria dla podanego zakresu i przedziału czasu. Można uruchomić wyszukiwanie w dzienniku, które zwróci wszystkie rekordy. W tym celu kliknij przycisk **Zobacz wszystko** na dole kolumny lub kliknij nagłówek kolumny.

- **Hosts**
    - **Użycie procesora CPU hosta** Przedstawia graficzny trend użycia procesora CPU przez komputery hosta i listę hostów w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika procesora dla hostowanych maszyn wirtualnych.
    - **Użycie pamięci hosta** Przedstawia graficzny trend wykorzystania pamięci przez komputery hosta i listę hostów w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę hosta, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegóły licznika pamięci dla hostowanych maszyn wirtualnych.
- **Virtual Machines**
    - **Użycie procesora CPU maszyny wirtualnej** Przedstawia graficzny trend użycia procesora CPU przez maszyny wirtualne i listę maszyn wirtualnych w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 pierwszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę maszyny wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić zagregowane szczegóły licznika procesora dla maszyny wirtualnej.
    - **Wykorzystanie pamięci maszyny wirtualnej** Przedstawia graficzny trend wykorzystania pamięci maszyn wirtualnych i listy maszyn wirtualnych w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 pierwszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę maszyny wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegółowe informacje o liczniku pamięci dla maszyny wirtualnej.
    - **Całkowita liczba operacji we/wy dysku maszyny wirtualnej** Przedstawia graficzny trend łącznej liczby operacji we/wy na sekundę dla maszyn wirtualnych i listy maszyn wirtualnych z każdą liczbą operacji we/wy na sekundę w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 pierwszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę maszyny wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić szczegółowe informacje o zagregowanym liczniku liczby operacji we/wy dysku dla maszyny wirtualnej.
    - **Całkowita przepływność dysku maszyny wirtualnej** Przedstawia graficzny trend łącznej przepływności dysków dla maszyn wirtualnych i listy maszyn wirtualnych z łączną przepływność dysku dla każdej z nich w oparciu o wybrany okres. Zatrzymaj wskaźnik myszy na wykresie liniowym, aby wyświetlić szczegóły określonego punktu w czasie dla 3 pierwszych maszyn wirtualnych. Kliknij wykres, aby wyświetlić więcej szczegółów w przeszukiwaniu dzienników. Kliknij dowolną nazwę maszyny wirtualnej, aby otworzyć wyszukiwanie w dzienniku i wyświetlić zagregowane sumaryczne szczegóły licznika przepływności dysku dla maszyny wirtualnej.
- **Udostępnione woluminy klastrowane**
    - **Łączna przepływność** Pokazuje sumę operacji odczytu i zapisu na udostępnionych woluminach klastra.
    - **Całkowita liczba operacji we/wy** Pokazuje sumę operacji wejścia/wyjścia na sekundę w klastrowanych woluminach udostępnionych.
    - **Łączne opóźnienie** Pokazuje łączne opóźnienie na udostępnionych woluminach klastra.
- **Gęstość hosta** Na górnym kafelku przedstawiono łączną liczbę hostów i maszyn wirtualnych dostępnych dla rozwiązania. Kliknij górny kafelek, aby wyświetlić dodatkowe szczegóły w przeszukiwaniu dzienników. Wyświetla również listę wszystkich hostów i liczbę hostowanych maszyn wirtualnych. Kliknij hosta, aby przejść do szczegółów w wyniku przeszukiwania dzienników.


![blok pulpitów nawigacyjnych](./media/capacity-performance/dashboard-hosts.png)

![blok maszyny wirtualne pulpitu nawigacyjnego](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Ocena wydajności

Produkcyjne środowiska obliczeniowe różnią się znacznie od jednej organizacji do innej. Ponadto obciążenia pojemności i wydajności mogą być zależne od sposobu działania maszyn wirtualnych i co należy wziąć pod uwagę. Konkretne procedury ułatwiające pomiar wydajności prawdopodobnie nie mają zastosowania w danym środowisku. W związku z tym bardziej uogólnione wskazówki dotyczące opisu są lepiej dopasowane do pomocy. Firma Microsoft publikuje różne artykuły ze wskazówkami dotyczącymi skryptów, które ułatwiają pomiar wydajności.

Podsumowując, rozwiązanie zbiera dane pojemności i wydajności z różnych źródeł, w tym liczników wydajności. Użyj tej pojemności i danych wydajności, które są prezentowane na różnych urządzeniach w rozwiązaniu, a następnie Porównaj wyniki z wynikami [pomiaru wydajności w artykule dotyczącym funkcji Hyper-V](https://www.microsoft.com/en-us/download/details.aspx?id=56495) . Mimo że artykuł został opublikowany jakiś czas temu, metryki, zagadnienia i wytyczne są nadal ważne. Artykuł zawiera linki do innych przydatnych zasobów.


## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników pod kątem pojemności i danych wydajności zebranych i obliczonych przez to rozwiązanie.


| Zapytanie | Opis |
|:--- |:--- |
| Wszystkie konfiguracje pamięci hosta | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i CounterName = = "host przypisany do pamięci MB" &#124; podsumowuje MB = średnia (CounterValue) przez InstanceName |
| Wszystkie konfiguracje pamięci maszyn wirtualnych | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i CounterName = = "pamięć maszyny wirtualnej przypisanych MB" &#124; podsumowania MB = średnia (CounterValue) przez InstanceName |
| Podział całkowitej liczby operacji we/wy na sekundę na wszystkie maszyny wirtualne | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i (CounterName = = "VHD odczyty/s" lub CounterName = = "zapisy wirtualnego dysku twardego/s") &#124; podsumowywania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), CounterName, InstanceName |
| Podział całkowitej przepływności dysku na wszystkie maszyny wirtualne | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i (CounterName = = "dysk VHD odczytu MB/s" lub CounterName = = "zapis dysku VHD MB/s") &#124; podsumowywania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), CounterName, InstanceName |
| Podział całkowitej liczby operacji we/wy na wszystkie CSV | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i (CounterName = = "CSV odczyty/s" lub CounterName = = "CSV Writes/s") &#124; podsumowywania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), CounterName, InstanceName |
| Podział całkowitej przepływności na wszystkie CSV | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i (CounterName = = "CSV odczyty/s" lub CounterName = = "CSV Writes/s") &#124; podsumowywania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), CounterName, InstanceName |
| Podział całkowitego opóźnienia we wszystkich CSV | &#124; wydajności, gdzie ObjectName = = "Capacity and Performance" i (CounterName = = "opóźnienie odczytu woluminu CSV" lub CounterName = = "opóźnienie zapisu w formacie CSV") &#124; podsumowywania AggregatedValue = AVG (CounterValue) według bin (TimeGenerated, 1 godz), CounterName, InstanceName |


## <a name="next-steps"></a>Następne kroki
* Użyj [przeszukiwania dzienników w log Analytics](../logs/log-query-overview.md) , aby wyświetlić szczegółowe dane Capacity and Performance.

