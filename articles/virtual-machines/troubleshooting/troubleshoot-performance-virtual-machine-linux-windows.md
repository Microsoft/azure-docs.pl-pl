---
title: Rozwiązywanie problemów z wydajnością maszyny wirtualnej platformy Azure w systemie Linux lub Windows
description: W tym artykule opisano ogólne Rozwiązywanie problemów z wydajnością maszyny wirtualnej za pośrednictwem monitorowania i poszanowania wąskich gardeł oraz możliwe Korygowanie problemów, które mogą wystąpić.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 53fd2332224d903c5a4b33563470cf3569f82b13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526660"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Rozwiązywanie problemów z wydajnością maszyny wirtualnej platformy Azure w systemie Linux lub Windows

W tym artykule opisano ogólne Rozwiązywanie problemów z wydajnością maszyny wirtualnej za pośrednictwem monitorowania i poszanowania wąskich gardeł oraz możliwe Korygowanie problemów, które mogą wystąpić. Oprócz monitorowania można również użyć że program perfinsights, który może dostarczyć raport z zaleceniami dotyczącymi najlepszych rozwiązań i kluczowych wąskich gardeł wokół operacji we/wy/procesora/pamięci. Że program perfinsights jest dostępna dla maszyn wirtualnych z [systemami Windows](./how-to-use-perfinsights.md) i [Linux](./how-to-use-perfinsights-linux.md) na platformie Azure.

Ten artykuł przeprowadzi Cię przez proces monitorowania w celu zdiagnozowania wąskich gardeł wydajności.

## <a name="enabling-monitoring"></a>Włączanie monitorowania

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorowanie maszyn wirtualnych IAAS platformy Azure

Aby monitorować maszynę wirtualną gościa, należy użyć monitorowania maszyn wirtualnych platformy Azure, które będzie powiadamiać o określonych warunkach zasobów wysokiego poziomu. Aby sprawdzić, czy włączono diagnostykę maszyny wirtualnej, zobacz [Omówienie dzienników zasobów platformy Azure](../../azure-monitor/learn/tutorial-resource-logs.md). Jeśli zobaczysz następujące polecenie, najprawdopodobniej nie masz włączonej diagnostyki:

![Monitorowanie nie jest włączone](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Włącz diagnostykę maszyny wirtualnej za poorednictwem Azure Portal firmy Microsoft

Aby włączyć diagnostykę maszyny wirtualnej:

1. Przejdź do maszyny wirtualnej
2. Kliknij pozycję **Ustawienia diagnostyki**
3. Wybierz konto magazynu, a następnie kliknij pozycję **Włącz monitorowanie na poziomie gościa**.

   ![Kliknij kolejno pozycje Ustawienia, Diagnostyka](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Konto magazynu używane do konfigurowania diagnostyki można sprawdzić na karcie **Agent** w obszarze **Ustawienia diagnostyki**.

![Sprawdź konto magazynu](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Włącz diagnostykę konta magazynu za Azure Portal

Magazyn jest bardzo ważną warstwą, gdy zamierzamy analizować wydajność operacji we/wy dla maszyny wirtualnej na platformie Azure. W przypadku metryk związanych z magazynem należy włączyć diagnostykę jako dodatkowy krok. Można to również włączyć, jeśli chcemy analizować tylko liczniki powiązane z magazynem.

1. Określ konto magazynu (lub konta) używane przez maszynę wirtualną, wybierając maszynę wirtualną. Kliknij pozycję **Ustawienia**, a następnie kliknij pozycję **dyski**:

   ![Kliknij przycisk Ustawienia, a następnie dysków](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. W portalu przejdź do konta magazynu (lub kont) dla maszyny wirtualnej i wykonaj następujące czynności:

   1. Kliknij pozycję przegląd dla konta magazynu znalezionego w powyższym kroku.
   2. Zostaną wyświetlone metryki domyślne. 

    ![Metryki domyślne](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Kliknij dowolną z metryk, co spowoduje wyświetlenie innego bloku zawierającego więcej opcji konfigurowania i dodawania metryk.

   ![Dodawanie metryk](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Aby skonfigurować te opcje:

1.  Wybierz pozycję **Metryki**.
2.  Wybierz **zasób** (konto magazynu).
3.  Wybierz **przestrzeń nazw**
4.  Wybierz **metrykę**.
5.  Wybierz typ **agregacji**
6.  Możesz przypiąć ten widok na pulpicie nawigacyjnym.

## <a name="observing-bottlenecks"></a>Obserwowanie wąskich gardeł

Po przeprowadzeniu wstępnego procesu instalacji wymaganych metryk i po włączeniu diagnostyki dla maszyny wirtualnej i powiązanego konta magazynu możemy przejść do fazy analizy.

### <a name="accessing-the-monitoring"></a>Uzyskiwanie dostępu do monitorowania

Wybierz maszynę wirtualną platformy Azure, którą chcesz zbadać, i wybierz pozycję **monitorowanie**.

![Wybierz monitorowanie](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Osie czasu obserwacji

Aby określić, czy masz jakieś wąskie gardła zasobów, Przejrzyj dane. Jeśli okaże się, że maszyna działała prawidłowo, ale została zgłoszona, że wydajność była ostatnio obniżona, przejrzyj przedział czasu, w którym znajdują się dane metryki wydajności przed zgłoszoną zmianą, podczas i po tym problemie.

### <a name="check-for-cpu-bottleneck"></a>Sprawdzanie wąskiego gardła procesora

![Sprawdzanie wąskiego gardła procesora](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Edytuj Graf.
2. Ustaw zakres czasu.
3. Następnie należy dodać w liczniku: procent użycia procesora CPU gościa
4. Zapisz zmiany.

### <a name="cpu-observe-trends"></a>Trendy dotyczące procesora CPU

Analizując problemy z wydajnością, weź pod uwagę trendy i zapoznaj się z nimi. W następnych sekcjach użyjemy wykresów monitorowania z portalu do wyświetlania trendów. Mogą być również przydatne w przypadku zachowań krzyżowego odwołujących się do różnic w tym samym okresie czasu. Aby dostosować wykresy, kliknij pozycję [Azure monitor Data Platform](../../azure-monitor/platform/data-platform.md).

Skok — skok może być powiązany z zaplanowanym zadaniem/znanym zdarzeniem. Jeśli możesz zidentyfikować zadanie, ustal, czy zadanie jest uruchamiane na wymaganym poziomie wydajności. W przypadku akceptowalnej wydajności może nie być konieczne zwiększenie ilości zasobów.

Zwiększanie i stałe — często wskazuje nowe obciążenie. Jeśli nie jest to rozpoznane obciążenie, Włącz monitorowanie na maszynie wirtualnej, aby dowiedzieć się, jakiego procesu (lub procesów) powoduje zachowanie. Po rozpoznaniu procesu Ustal, czy zwiększone zużycie jest spowodowane przez nieefektywny kod lub normalne użycie. W przypadku normalnego zużycia należy zdecydować, czy proces działa na wymaganym poziomie wydajności.

Stała — Określ, czy maszyna wirtualna ma być zawsze uruchamiana na tym poziomie, czy ma działać tylko na tym poziomie, ponieważ Diagnostyka została włączona. Jeśli tak, zidentyfikuj proces (procesy) powodujący problem i rozważ dodanie większej ilości tego zasobu.

Stale rośnie — stałe zwiększenie zużycia jest często niewydajnego kodu lub procesu, który zajmuje więcej obciążenia użytkownikami.

### <a name="high-cpu-utilization-remediation"></a>Duże korygowanie użycia procesora CPU

Jeśli aplikacja lub proces nie działa na poprawnym poziomie wydajności i widzisz 95% + moc użycia procesora CPU, można wykonać jedno z następujących zadań:

* W celu natychmiastowego zwolnienia — zwiększenie rozmiaru maszyny wirtualnej do rozmiaru o większej liczbie rdzeni
* Zapoznaj się z problemem — zlokalizuj aplikację/proces i Rozwiąż odpowiednie problemy.

Jeśli zwiększono maszynę wirtualną, a procesor CPU nadal działa 95%, ustal, czy to ustawienie zapewnia lepszą wydajność lub wyższą przepływność aplikacji do akceptowalnego poziomu. W przeciwnym razie Rozwiązywanie problemów z indywidualnym application\process.

Możesz użyć że program perfinsights dla [systemu Windows](./how-to-use-perfinsights.md) lub [Linux](./how-to-use-perfinsights-linux.md) , aby przeanalizować, który proces zapewnia użycie procesora CPU. 

## <a name="check-for-memory-bottleneck"></a>Sprawdzanie wąskiego gardła pamięci

Aby wyświetlić metryki:

1. Dodaj sekcję.
2. Dodaj kafelek.
3. Otwórz galerię.
4. Wybierz użycie pamięci i przeciągnij ją. Gdy kafelek jest zadokowany, kliknij prawym przyciskiem myszy i wybierz pozycję **6x4**.

### <a name="memory-observe-trends"></a>Trendy dotyczące pamięci

Użycie pamięci pokazuje, ile pamięci jest zużywanej z maszyną wirtualną. Zapoznaj się z trendem i czy jest on mapowany na czas, w którym widzisz problemy. Zawsze powinna być dostępna więcej niż 100 MB dostępnej pamięci.

Powiększanie i stałe/stałe — duże użycie pamięci może nie być przyczyną złej wydajności, ponieważ niektóre aplikacje, takie jak aparaty relacyjnych baz danych, przydzielają duże ilości pamięci, a wykorzystanie może nie być znaczące. Jeśli jednak istnieje wiele aplikacji zasobożernych pamięci, może zostać wyświetlona niska wydajność z rywalizacji o pamięć powodująca przycinanie i stronicowanie na dysku. Ta niska wydajność jest często zauważalną przyczyną wpływu na wydajność aplikacji.

Stałe zwiększenie zużycia — możliwą aplikację "rozgrzewanie", to zużycie jest wspólne między aparatami bazy danych. Jednak może to również być oznaką przecieku pamięci w aplikacji. Zidentyfikuj aplikację i sprawdź, czy zachowanie jest oczekiwane.

Użycie strony lub pliku wymiany — Sprawdź, czy używasz pliku stronicowania systemu Windows (znajdującego się w \) pliku z rozszerzeniem D: lub Linux (znajdującego się na `/dev/sdb` ). Jeśli na tych woluminach nie ma niczego z wyjątkiem tych plików, sprawdź, czy na tych dyskach są dostępne duże operacje odczytu i zapisu. Ten problem jest wskaźnikiem niewielkich warunków pamięci.

### <a name="high-memory-utilization-remediation"></a>Duże korygowanie wykorzystania pamięci

Aby rozwiązać duże wykorzystanie pamięci, wykonaj dowolne z następujących zadań:

* W celu natychmiastowego zwolnienia lub użycia pliku lub wymiany plików — zwiększenie rozmiaru maszyny wirtualnej do jednego z większą ilością pamięci, a następnie monitorowanie.
* Opis problemu — lokalizowanie aplikacji/procesu i rozwiązywanie problemów w celu identyfikowania wysoce zużywających się aplikacji pamięci.
* Jeśli znasz aplikację, sprawdź, czy alokacja pamięci może być ograniczona.

Jeśli po przeprowadzeniu uaktualnienia do większej maszyny wirtualnej okaże się, że nadal masz stały wzrost ciągły do 100%, zidentyfikuj aplikację/proces i rozwiązywanie problemów.

Możesz użyć że program perfinsights dla [systemu Windows](./how-to-use-perfinsights.md) lub [Linux](./how-to-use-perfinsights-linux.md) , aby przeanalizować, który proces zwiększa zużycie pamięci. 

## <a name="check-for-disk-bottleneck"></a>Sprawdź, czy nie ma wąskiego gardła dysku

Aby sprawdzić podsystem magazynowania dla maszyny wirtualnej, sprawdź diagnostykę na poziomie maszyny wirtualnej platformy Azure przy użyciu liczników w diagnostyce maszyny wirtualnej, a także diagnostyki konta magazynu.

W przypadku rozwiązywania problemów związanych z maszyną wirtualną można użyć programu że program perfinsights dla [systemu Windows](./how-to-use-perfinsights.md) lub [Linux](./how-to-use-perfinsights-linux.md), który może pomóc w analizie, który proces prowadzi do operacji we/wy. 

Należy pamiętać, że nie mamy liczników dla stref nadmiarowych i kont Premium Storage. W przypadku problemów związanych z tymi licznikami Zgłoś zgłoszenie do pomocy technicznej.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Wyświetlanie diagnostyki konta magazynu w ramach monitorowania

Aby obejść poniższe elementy, przejdź do konta magazynu dla maszyny wirtualnej w portalu:

![Wyświetlanie diagnostyki konta magazynu w ramach monitorowania](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Edytuj wykres monitorowania.
2. Ustaw zakres czasu.
3. Dodaj liczniki opisane w poniższych krokach.
4. Zapisz zmiany.

### <a name="disk-observe-trends-standard-storage-only"></a>Trendy na dysku (tylko w przypadku magazynu w warstwie Standardowa)

Aby zidentyfikować problemy związane z magazynem, zapoznaj się z metrykami wydajności dotyczącymi diagnostyki konta magazynu i diagnostyki maszyn wirtualnych.

Dla każdego poniższego sprawdzenia poszukaj najważniejszych trendów, gdy występują problemy w przedziale czasowym problemu.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Sprawdź dostępność usługi Azure Storage — Dodaj metrykę konta magazynu: dostępność

Jeśli pojawi się lista dostępności, może wystąpić problem z platformą, sprawdź [Stan platformy Azure](https://azure.microsoft.com/status/). Jeśli w tym miejscu nie jest wyświetlany żaden problem, zgłoś nowe żądanie obsługi.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Sprawdź limit czasu usługi Azure Storage — Dodaj metryki konta magazynu:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Wartości w metrykach * TimeOutError wskazują, że operacja we/wy trwała zbyt długo i przekroczyła limit czasu. Wykonanie kolejnych kroków pomoże w zidentyfikowaniu potencjalnych przyczyn.

Wartość averageserverlatency zwiększa się w tym samym czasie na TimeOutErrors może to być problem z platformą. W tej sytuacji Zgłoś nowe żądanie obsługi.

Niską averagee2elatency reprezentuje opóźnienie klienta. Sprawdź, w jaki sposób operacje we/wy są wykonywane przez aplikację. Poszukaj wzrostu lub ciągle dużej TotalRequests metryki. Ta Metryka reprezentuje liczbę IOPS. Jeśli zaczynasz obowiązywać limity konta magazynu lub pojedynczego wirtualnego dysku twardego, opóźnienie może być związane z ograniczaniem.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Sprawdź ograniczenia dotyczące usługi Azure Storage — Dodaj metryki konta magazynu: ThrottlingError

Wartości ograniczania wydajności wskazują, że są ograniczane na poziomie konta magazynu, co oznacza, że naciśnięcie limitu liczby IOPS dla konta. Możesz określić, czy osiągasz próg IOPs, sprawdzając **TotalRequests**metryki.

Należy pamiętać, że każdy wirtualny dysk twardy ma limit 500 operacji we/wy lub 60 megabitów, ale jest związany z łącznym limitem liczby operacji 20000 we/wy na konto magazynu.

Korzystając z tej metryki, nie można określić, który obiekt BLOB powoduje ograniczenie i które ma to wpływ. Jest to jednak szybkość operacji we/wy na sekundę lub limity ruchu przychodzącego/wychodzącego.

Aby ustalić, czy zbliżasz się do limitu liczby operacji we/wy, przejdź do diagnostyki konta magazynu i sprawdź TotalRequests, aby sprawdzić, czy dojdziesz do 20000 TotalRequests. Zidentyfikuj zmianę wzorca, niezależnie od tego, czy widzisz limit po raz pierwszy, czy ten limit występuje w określonym czasie.

Dzięki nowym oferowanym dyskom w ramach magazynu w warstwie Standardowa przepustowość i limity przepływności mogą się różnić, ale łączny limit konta magazynu to 20000 IOPS (magazyn w warstwie Premium ma inne limity na poziomie konta lub dysku). Dowiedz się więcej o różnych ofertach dysków magazynu w warstwie Standardowa i limitach na dysku:

* [Elementy docelowe skalowalności i wydajności dla dysków maszyn wirtualnych w systemie Windows](../windows/disk-scalability-targets.md).

#### <a name="references"></a>Odwołania

* [Cele skalowalności i wydajności dla kont usługi BLOB Storage na stronie Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

Przepustowość konta magazynu jest mierzona przez metryki konta magazynu: TotalIngress i TotalEgress. Istnieją różne progi przepustowości w zależności od typu nadmiarowości i regionów.

* [Cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../../storage/common/scalability-targets-standard-account.md)

Sprawdź TotalIngress i TotalEgress względem limitów ruchu przychodzącego i wychodzącego dla opcji Typ i region nadmiarowości konta magazynu.

Sprawdź limity przepływności wirtualnych dysków twardych dołączonych do maszyny wirtualnej. Dodaj dysk metryk maszyny wirtualnej odczyt i zapis.

Nowe oferty dysków w ramach magazynu w warstwie Standardowa mają różne liczby IOPS i limity przepływności (operacje we/wy nie są widoczne dla wirtualnego dysku twardego). Sprawdź dane, aby sprawdzić, czy są używane limity łącznej przepływności dysków VHD na poziomie maszyny wirtualnej przy użyciu odczytu i zapisu na dysku, a następnie Zoptymalizuj konfigurację magazynu maszyn wirtualnych w celu skalowania ostatnich limitów jednego dysku VHD. Dowiedz się więcej o różnych ofertach dysków magazynu w warstwie Standardowa i limitach na dysku:

* [Elementy docelowe skalowalności i wydajności dla dysków maszyn wirtualnych w systemie Windows](../windows/disk-scalability-targets.md).

### <a name="high-disk-utilizationlatency-remediation"></a>Duże użycie dysku/korygowanie opóźnień

Zmniejszenie opóźnienia klienta i zoptymalizowanie operacji we/wy maszyny wirtualnej w celu skalowania limitów ostatnich dysków VHD

* [Optymalizacja operacji we/wy dla systemu Windows na platformie Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Optymalizowanie operacji we/wy dla systemu Linux na platformie Azure](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>Zmniejsz ograniczenie przepustowości

W przypadku górnego limitu kont magazynu należy ponownie zrównoważyć wirtualne dyski twarde między kontami magazynu. Zapoznaj się z [celami skalowalności i wydajności usługi Azure Storage](../../storage/common/scalability-targets-standard-account.md).

### <a name="increase-throughput-and-reduce-latency"></a>Zwiększanie przepływności i zmniejszanie opóźnień

Jeśli masz poufną aplikację, która wymaga dużej przepływności, dokonaj migracji wirtualnych dysków twardych do usługi Azure Premium Storage przy użyciu maszyny wirtualnej z serii DS i GS.

W tych artykułach omówiono konkretne scenariusze:

* [Migracja do usługi Azure Premium Storage](../windows/migrate-to-managed-disks.md)

* [Korzystanie z usługi Azure Premium Storage z usługą SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, skontaktuj się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
