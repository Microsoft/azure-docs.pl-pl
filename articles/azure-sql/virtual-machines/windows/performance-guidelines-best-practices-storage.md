---
title: 'Magazyn: najlepsze rozwiązania dotyczące wydajności & wytycznych'
description: Zawiera najlepsze rozwiązania i wytyczne dotyczące magazynu w celu zoptymalizowania wydajności SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389727"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Magazyn: najlepsze rozwiązania w zakresie wydajności SQL Server maszyn wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera najlepsze rozwiązania i wytyczne dotyczące magazynu w celu zoptymalizowania wydajności SQL Server na platformie Azure Virtual Machines (maszyn wirtualnych).

Zazwyczaj istnieje związek między optymalizacją kosztów i optymalizacją pod celu utrzymania wydajności. Ta seria najlepszych rozwiązań w zakresie  wydajności koncentruje się na uzyskiwaniu najlepszej wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, może nie wymagać każdej zalecanej optymalizacji. Podczas oceniania tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, koszty i wzorce obciążeń.

Aby dowiedzieć się więcej, zobacz inne artykuły z tej serii: [Lista](performance-guidelines-best-practices-checklist.md)kontrolna wydajności, rozmiar [maszyny](performance-guidelines-best-practices-vm-size.md)wirtualnej i [Punkt odniesienia zbierania](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Lista kontrolna

Zapoznaj się z następującą listą kontrolną, aby zapoznać się z krótkim omówieniem najlepszych rozwiązań w zakresie magazynowania, które bardziej szczegółowo opisano w pozostałej części artykułu: 

- Przed wybraniem [](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) typu dysku należy monitorować aplikację i określać wymagania dotyczące przepustowości i opóźnienia magazynu SQL Server danych, dziennika i bazy danych tempdb. 
- Aby zoptymalizować wydajność magazynu, należy zaplanować najwyższą dostępną liczbę operacji we/wy na odczytu bez buforowania i używać buforowania danych jako funkcji wydajności dla operacji odczytu danych przy jednoczesnym uniknięciu ograniczania liczby maszyn wirtualnych [i dysków.](../../../virtual-machines/premium-storage-performance.md#throttling)
- Umieść pliki danych, dziennika i bazy danych tempdb na oddzielnych dyskach.
    - W przypadku dysku danych używaj tylko [dysków Premium P30 i P40,](../../../virtual-machines/disks-types.md#premium-ssd) aby zapewnić dostępność obsługi pamięci podręcznej
    - W przypadku planu dysku dziennika wydajności i wydajności testu w porównaniu z kosztami podczas oceny [dysków Premium P30–P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Jeśli wymagane jest opóźnienie magazynu w milisekundach, użyj dysków w warstwie Ultra platformy [Azure](../../../virtual-machines/disks-types.md#ultra-disk) dla dziennika transakcji. 
      - W przypadku wdrożeń maszyn wirtualnych serii M należy [rozważyć](../../../virtual-machines/how-to-enable-write-accelerator.md) użycie akceleratora zapisu w przypadku korzystania z dysków w warstwie Ultra platformy Azure.
    - Umieść [program tempdb](/sql/relational-databases/databases/tempdb-database) na lokalnym efemerskim dysku SSD dla `D:\` SQL Server obciążeń po wybraniu optymalnego rozmiaru maszyny wirtualnej. 
      - Jeśli pojemność dysku lokalnego nie jest wystarczająca dla bazy danych tempdb, rozważ rozmiar maszyny wirtualnej. Aby [uzyskać więcej informacji,](#data-file-caching-policies) zobacz Zasady buforowania plików danych.
- Rozsuń wiele dysków danych platformy Azure przy użyciu funkcji [Miejsca](/windows-server/storage/storage-spaces/overview) do magazynowania, aby zwiększyć przepustowość we/wy do limitów przepływności i liczby we/wy docelowej maszyny wirtualnej.
- Ustaw [buforowanie hosta na](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) wartość tylko do odczytu dla dysków plików danych.
- Dla [dysków z plikami](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) dziennika ustaw wartość brak buforowania hosta.
    - Nie należy włączać buforowania odczytu/zapisu na dyskach zawierających SQL Server plików. 
    - Zawsze należy zatrzymać SQL Server przed zmianą ustawień pamięci podręcznej dysku.
- W przypadku obciążeń programistyczych i testowych oraz długoterminowego archiwizowania kopii zapasowych należy rozważyć użycie magazynu w standardowych magazynach. Nie zaleca się używania HDD w warstwie Standardowa/SDD dla obciążeń produkcyjnych.
- [Skalowanie dysków](../../../virtual-machines/disk-bursting.md#credit-based-bursting) oparte na środków (P1–P20) powinno być rozważane tylko w przypadku mniejszych obciążeń tworzenia i testowania oraz systemów działów.
- Aprowizowanie konta magazynu w tym samym regionie, w SQL Server wirtualnej. 
- Wyłącz magazyn geograficznie nadmiarowy platformy Azure (replikacja geograficzna) i użyj magazynu LRS (magazyn lokalnie nadmiarowy) na koncie magazynu.
- Sformatuj dysk danych do użycia rozmiaru bloku 64 KB (rozmiar jednostki alokacji) dla wszystkich plików danych umieszczonych na dysku innym niż dysk tymczasowy `D:\` (domyślnie 4 KB). SQL Server wirtualne wdrożone za pośrednictwem usługi Azure Marketplace są wyposażone w dyski danych sformatowane z rozmiarem bloku i przepłyną dla puli magazynów ustawionej na 64 KB. 

Aby porównać listę kontrolną magazynu z innymi, zobacz kompleksową listę kontrolną [najlepszych rozwiązań w zakresie wydajności.](performance-guidelines-best-practices-checklist.md) 

## <a name="overview"></a>Omówienie

Aby znaleźć najbardziej efektywną konfigurację SQL Server na maszynie wirtualnej platformy Azure, zacznij od pomiaru wydajności magazynu [aplikacji biznesowej.](performance-guidelines-best-practices-collect-baseline.md#storage) Gdy wymagania dotyczące magazynu są znane, wybierz maszynę wirtualną, która obsługuje niezbędną ilość IOPS i przepływność z odpowiednim współczynnikiem pamięci do liczby rdzeni wirtualnych. 

Wybierz rozmiar maszyny wirtualnej z wystarczającą skalowalnością magazynu dla obciążenia i mieszanką dysków (zazwyczaj w puli magazynów), które spełniają wymagania dotyczące pojemności i wydajności twojej firmy. 

Typ dysku zależy zarówno od typu pliku, który jest hostowany na dysku, jak i od wymagań dotyczących szczytowej wydajności.

> [!TIP]
> Aprowizowanie maszyny wirtualnej z programem SQL Server za pośrednictwem usługi Azure Portal ułatwia przewodnik przez proces konfiguracji magazynu i implementuje większość najlepszych rozwiązań dotyczących magazynu, takich jak tworzenie oddzielnych pul magazynu dla plików danych i dzienników, kierowanie bazy danych tempdb do dysku i włączanie optymalnych zasad `D:\` buforowania. Aby uzyskać więcej informacji na temat aprowizowania i konfigurowania magazynu, zobacz [Konfiguracja magazynu maszyny wirtualnej SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Możesz wybrać poziom wydajności dysków. Typy dysków zarządzanych dostępne jako podstawowy magazyn (wymienione przez zwiększenie możliwości wydajności) to standardowe dyski twarde (HDD), dyski SSD w warstwie Standardowa, dyski PÓŁPRZEWODNIK (SSD) w warstwie Premium i dyski w warstwie Ultra. 

Wydajność dysku zwiększa się wraz z pojemnością pogrupowane według etykiet dysków w chmurze [Premium,](../../../virtual-machines/disks-types.md#premium-ssd) takich jak P1 z 4 GiB miejsca i 120 IOPS do P80 z 32 TiB magazynu i 20 000 IOPS. Usługa Premium Storage obsługuje pamięć podręczną magazynu, która pomaga zwiększyć wydajność odczytu i zapisu dla niektórych obciążeń. Aby uzyskać więcej informacji, zobacz [Omówienie funkcji Dyski zarządzane.](../../../virtual-machines/managed-disks-overview.md) 

Istnieją również trzy główne [typy](../../../virtual-machines/managed-disks-overview.md#disk-roles) dysków, które należy wziąć pod uwagę SQL Server maszynie wirtualnej platformy Azure — dysk systemu operacyjnego, dysk tymczasowy i dyski danych. Starannie wybierz, co jest przechowywane na dysku systemu operacyjnego i `(C:\)` efemerowym dysku `(D:\)` tymczasowym. 

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego to dysk VHD, który można uruchomić i zainstalowany jako uruchomiona wersja systemu operacyjnego i jest oznaczony jako `C:\` dysk. Podczas tworzenia maszyny wirtualnej platformy Azure platforma dołączy do maszyny wirtualnej co najmniej jeden dysk dla dysku systemu operacyjnego. Dysk `C:\` jest domyślną lokalizacją instalacji aplikacji i konfiguracji plików. 

W środowiskach SQL Server nie należy używać dysku systemu operacyjnego dla plików danych, plików dziennika i dzienników błędów. 

### <a name="temporary-disk"></a>Dysk tymczasowy

Wiele maszyn wirtualnych platformy Azure zawiera inny typ dysku o nazwie dysk tymczasowy (oznaczony jako `D:\` dysk). Pojemność tego dysku zależy od serii maszyn wirtualnych i rozmiaru. Dysk tymczasowy jest efemeralny, co oznacza, że magazyn dyskowy jest ponownie utworzony (tak jak w programie, jest cofany [](/troubleshoot/azure/virtual-machines/understand-vm-reboot)i przydzielany ponownie), gdy maszyna wirtualna jest ponownie uruchamiana lub przenoszony na innego hosta (na przykład w celu zaprawiania usługi). 

Dysk magazynu tymczasowego nie jest utrwalany w magazynie zdalnym i dlatego nie powinien przechowywać plików bazy danych użytkownika, plików dziennika transakcji ani żadnych informacji, które muszą zostać zachowane. 

Umieść bazy danych tempdb na lokalnym tymczasowym dysku SSD dla SQL Server obciążeń, chyba że zużycie `D:\` lokalnej pamięci podręcznej jest problemem. Jeśli używasz maszyny wirtualnej, która nie ma dysku tymczasowego, zaleca się umieścić program tempdb na własnym izolowanym dysku lub puli magazynów z buforowaniem ustawionym na tylko do odczytu. [](../../../virtual-machines/azure-vms-no-temp-disk.md) Aby dowiedzieć się więcej, zobacz zasady buforowania danych bazy danych [tempdb.](performance-guidelines-best-practices-storage.md#data-file-caching-policies)

### <a name="data-disks"></a>Dyski danych

Dyski danych są zdalnymi dyskami [](/windows-server/storage/storage-spaces/overview) magazynu, które są często tworzone w pulach magazynów w celu przekroczenia pojemności i wydajności, które każdy pojedynczy dysk może zaoferować maszynie wirtualnej.

Dołącz minimalną liczbę dysków, która spełnia wymagania dotyczące liczby IOPS, przepływności i pojemności obciążenia. Nie należy przekraczać maksymalnej liczby dysków danych najmniejszej maszyny wirtualnej, do których planujesz zmienić rozmiar.

Umieść pliki danych i dzienników na dyskach danych aprowowanych w celu najlepszego spełniania wymagań dotyczących wydajności. 

Sformatuj dysk danych, aby używać rozmiaru jednostki alokacji 64 KB dla wszystkich plików danych umieszczonych na dysku innym niż dysk tymczasowy `D:\` (domyślnie 4 KB). SQL Server wirtualne wdrożone za pośrednictwem usługi Azure Marketplace mają dyski danych sformatowane z rozmiarem jednostki alokacji i przepłyną dla puli magazynów ustawione na 64 KB. 

## <a name="premium-disks"></a>Dyski w warstwie Premium

Użyj dysków SSD w warstwie Premium na potrzeby plików danych i dzienników SQL Server obciążeń produkcyjnych. SSD w warstwie Premium IOPS i przepustowość różnią się w zależności od [rozmiaru dysku i typu](../../../virtual-machines/disks-types.md). 

W przypadku obciążeń produkcyjnych użyj dysków P30 i/lub P40 dla plików danych programu SQL Server, aby zapewnić obsługę buforowania i użyć P30 do P80 na potrzeby SQL Server plików dziennika transakcji.  Aby uzyskać najlepszy całkowity koszt posiadania, rozpocznij od P30s (5000 IOPS/200 MB/s) dla plików danych i dziennika, a następnie wybierz wyższe pojemności tylko wtedy, gdy musisz kontrolować liczbę dysków maszyny wirtualnej.

W przypadku obciążeń OLTP dopasuj docelową wartość IOPS na dysk (lub pulę magazynów) do wymagań dotyczących wydajności, używając obciążeń w godzinach szczytu i `Disk Reads/sec`  +  `Disk Writes/sec` liczników wydajności. W przypadku obciążeń magazynu danych i raportowania dopasuj docelową przepływność przy użyciu obciążeń w godzinach szczytu i `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Użyj funkcji Miejsca do magazynowania, aby osiągnąć optymalną wydajność, konfiguruj dwie pule, jedną dla plików dziennika i drugą dla plików danych. Jeśli nie używasz usuwania dysków, użyj dwóch dysków SSD w warstwie Premium zamapowanych na oddzielne dyski, gdzie jeden dysk zawiera plik dziennika, a drugi zawiera dane.

[Aprowizowana i przepływność na](../../../virtual-machines/disks-types.md#premium-ssd) dysk, które są używane jako część puli magazynów. Łączna liczba IOPS i możliwości przepływności dysków to maksymalna możliwość do granic przepływności maszyny wirtualnej.

Najlepszym rozwiązaniem jest użycie jak najmniejszej liczby dysków przy jednoczesnym spełnianiu minimalnych wymagań dotyczących liczby we/wy na wy nas (i przepływności) oraz pojemności. Jednak saldo cen i wydajności jest zwykle lepsze w przypadku dużej liczby małych dysków, a nie niewielkiej liczby dużych dysków.

### <a name="scaling-premium-disks"></a>Skalowanie dysków w warstwie Premium

Podczas pierwszego wdrożenia dysku zarządzanego platformy Azure warstwa wydajności dla tego dysku jest oparta na rozmiarze aprowizowanych dysków. Wyznaczanie warstwy wydajności podczas wdrażania lub zmiana jej później, bez zmiany rozmiaru dysku. W przypadku wzrostu zapotrzebowania można zwiększyć poziom wydajności, aby zaspokoić potrzeby biznesowe. 

Zmiana warstwy wydajności umożliwia administratorom przygotowanie się do wyższego zapotrzebowania i spełnienie go bez konieczności polegania na zsyłaniu [dysku.](../../../virtual-machines/disk-bursting.md#credit-based-bursting) 

Korzystaj z wyższej wydajności tak długo, jak długo jest to konieczne, jeśli rozliczenia są przeznaczone do spełnienia wymagań warstwy wydajności magazynu. Uaktualnij warstwę, aby spełniała wymagania dotyczące wydajności bez zwiększania pojemności. Wróć do oryginalnej warstwy, gdy dodatkowa wydajność nie jest już wymagana.

To ekonomiczne i tymczasowe zwiększenie wydajności to silny przypadek użycia dla zdarzeń docelowych, takich jak zakupy, testowanie wydajności, zdarzenia szkoleniowe i inne krótkie okna, w których większa wydajność jest potrzebna tylko na krótki okres. 

Aby uzyskać więcej informacji, zobacz [Performance tiers for managed disks (Warstwy wydajności dla dysków zarządzanych).](../../../virtual-machines/disks-change-performance.md) 

## <a name="azure-ultra-disk"></a>Dysk w chmurze Azure Ultra

Jeśli istnieje potrzeba skrócenia czasu odpowiedzi w milisekundach z małym opóźnieniem, rozważ użycie dysku [Azure Ultra](../../../virtual-machines/disks-types.md#ultra-disk) dla dysku dziennika usługi SQL Server, a nawet dysku danych dla aplikacji, które są bardzo wrażliwe na opóźnienia we/wy. 

Dysk w kącie Ultra można skonfigurować w taki sposób, aby pojemność i IOPS można skalować niezależnie. Administratorzy dysków w ultra mogą aprowizować dysk z wymaganiami w zakresie pojemności, IOPS i przepływności w zależności od potrzeb aplikacji. 

Dysk w kącie Ultra nie jest obsługiwany we wszystkich seriach maszyn wirtualnych i ma inne ograniczenia, takie jak dostępność regionów, nadmiarowość i obsługa Azure Backup. Aby dowiedzieć się więcej, zobacz [Używanie dysków w warstwie Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) platformy Azure, aby uzyskać pełną listę ograniczeń. 

## <a name="standard-hdds-and-ssds"></a>Dyski HDD i SSD w standardowych standardach

[Dyski HDD i](../../../virtual-machines/disks-types.md#standard-hdd) SSD w standardowych standardach mają różne opóźnienia i przepustowość i są zalecane tylko w przypadku obciążeń tworzenia i testowania. Obciążenia produkcyjne powinny używać dysków SSD w wersjach Premium. Jeśli używasz usługi SSD w warstwie Standardowa (scenariusze tworzenia i testowania), zaleca się dodanie maksymalnej [](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) liczby dysków danych obsługiwanych przez rozmiar maszyny wirtualnej i użycie funkcji rozsyłania dysków z miejscami do magazynowania, aby uzyskać najlepszą wydajność.

## <a name="caching"></a>Buforowanie

Maszyny wirtualne, które obsługują buforowanie magazynu w chmurze w chmurze w witrynie Premium, mogą korzystać z dodatkowej funkcji o nazwie Azure BlobCache lub buforowania hosta w celu rozszerzenia możliwości operacji we/wy na/wy i przepływności maszyny wirtualnej. Maszyny wirtualne z włączoną obsługą magazynu w chmurze w chmurze Premium i Premium Storage mają te dwa różne limity przepustowości magazynu, których można używać razem w celu zwiększenia wydajności magazynu.

Przepływność IOPS i MB/s bez buforowania jest wliczane do limitów przepływności dysków bez buforowania maszyny wirtualnej. Maksymalne limity buforowane zapewniają dodatkowy bufor dla odczytów, który pomaga rozwiązać ten wzrost i nieoczekiwane wzrosty.

Włącz buforowanie Premium zawsze, gdy opcja jest obsługiwana, aby znacząco poprawić wydajność odczytu na dysku danych bez dodatkowych kosztów. 

Odczyty i zapis w usłudze Azure BlobCache (buforowana liczba IOPS i przepływność) nie są wliczane do niecachiowanych limitów liczby IOPS i przepływności maszyny wirtualnej.

> [!NOTE]
> Buforowanie dysku nie jest obsługiwane w przypadku dysków 4 TiB i większych (P50 i większych). Jeśli do maszyny wirtualnej jest dołączonych wiele dysków, każdy dysk mniejszy niż 4 TiB będzie obsługiwać buforowanie. Aby uzyskać więcej informacji, zobacz [Buforowanie dysku](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Niecachowana przepływność

Maksymalna liczba niecachowanych liczby we/wy na dysku i przepływność to maksymalny limit magazynu zdalnego, który może obsłużyć maszyna wirtualna. Ten limit jest definiowany na maszynie wirtualnej i nie jest limitem bazowego magazynu dyskowego. Ten limit dotyczy tylko we/wy dysków danych zdalnie dołączonych do maszyny wirtualnej, a nie lokalnych we/wy względem dysku tymczasowego (dysku) lub dysku `D:\` systemu operacyjnego.

Ilość niecachowanych wartości IOPS i przepływności dostępnej dla maszyny wirtualnej można zweryfikować w dokumentacji maszyny wirtualnej.

Na przykład w dokumentacji serii [M](../../../virtual-machines/m-series.md) pokazano, że maksymalna bezcachowana przepływność maszyny wirtualnej z programem Standard_M8ms wynosi 5000 IOPS i 125 MB/s przepływności dysku bez pamięci. 

![Zrzut ekranu przedstawiający dokumentację przepływności dysków bez pamięci serii M.](./media/performance-guidelines-best-practices/m-series-table.png)

Podobnie można zobaczyć, że Standard_M32ts obsługuje 20 000 niecachowanych dysków IOPS i 500 MB/s bezcachowanej przepływności dysku. Ten limit jest zarządzany na poziomie maszyny wirtualnej niezależnie od podstawowego magazynu dysków w chmurze w chmurze Premium.

Aby uzyskać więcej informacji, zobacz [Uncached and cached limits (Limity niecachowane i buforowane).](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits)


### <a name="cached-and-temp-storage-throughput"></a>Przepływność magazynu buforowanych i tymczasowych

Maksymalny limit przepływności magazynu w pamięci podręcznej i magazynu tymczasowego jest osobnym limitem od limitu przepływności bez buforowania na maszynie wirtualnej. Usługa Azure BlobCache składa się z kombinacji pamięci hosta maszyny wirtualnej o dostępie losowym i lokalnie dołączonego dysku SSD. Dysk tymczasowy `D:\` (dysk) na maszynie wirtualnej jest również hostowany na tym lokalnym dysku SSD.

Maksymalny limit przepływności pamięci podręcznej i tymczasowego magazynu określa, czy we/wy na lokalnym dysku tymczasowym (dysku) i usłudze Azure BlobCache jest włączone `D:\` buforowanie hosta.  

Gdy buforowanie jest włączone w magazynie Premium Storage, maszyny wirtualne mogą być skalowane poza ograniczenia limitów przepływności i bez buforowania maszyny wirtualnej magazynu zdalnego.  

Tylko niektóre maszyny wirtualne obsługują zarówno buforowanie magazynu Premium Storage, jak i premium storage (które należy zweryfikować w dokumentacji maszyny wirtualnej). Na przykład dokumentacja serii [M wskazuje,](../../../virtual-machines/m-series.md) że obsługiwane jest zarówno buforowanie magazynu w chmurze Premium Storage, jak i magazynu w chmurze w chmurze Premium: 

![Zrzut ekranu przedstawiający obsługę Premium Storage serii M.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Limity pamięci podręcznej różnią się w zależności od rozmiaru maszyny wirtualnej. Na przykład maszyna wirtualna Standard_M8ms obsługuje 10000 buforowanych dysków IOPS i 1000 MB/s buforowanej przepływności dysku o łącznym rozmiarze pamięci podręcznej 793 GiB. Podobnie maszyna wirtualna Standard_M32ts obsługuje 40000 buforowanych dysków IOPS i 400 MB/s buforowanej przepływności dysku o łącznym rozmiarze pamięci podręcznej 3174 GiB. 

![Zrzut ekranu przedstawiający dokumentację buforowanej przepływności dysku serii M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Buforowanie hosta można włączyć ręcznie na istniejącej maszynie wirtualnej. Zatrzymaj wszystkie obciążenia aplikacji i usługi SQL Server przed zmianą zasad buforowania maszyny wirtualnej. Zmiana dowolnego ustawienia pamięci podręcznej maszyny wirtualnej powoduje odłączenie dysku docelowego i ponowne dołączenia po zastosowaniu ustawień.

### <a name="data-file-caching-policies"></a>Zasady buforowania plików danych

Zasady buforowania magazynu różnią się w zależności od typu SQL Server plików danych hostowanych na dysku. 

W poniższej tabeli przedstawiono podsumowanie zalecanych zasad buforowania na podstawie typu SQL Server danych: 

|SQL Server dysku |Zalecenie |
|---------|---------|
| **Dysk danych** | Włącz `Read-only` buforowanie dla dysków host SQL Server plików danych. <br/> Odczyty z pamięci podręcznej będą szybsze niż odczyty bez buforowania z dysku danych. <br/> Uncached IOPS and throughput plus Cached IOPS and throughput will yield the total possible performance available from the virtual machine within the VMs limits, but actual performance will vary on the workload's ability to use the cache (cache hit ratio). <br/>|
|**Dysk dziennika transakcji**|Dla dysków hostjących dziennik transakcji ustaw zasady `None` buforowania na wartość .  Włączenie buforowania dla dysku dziennika transakcji nie ma korzyści z wydajności, a w rzeczywistości włączenie buforowania na dysku dziennika może obniżyć wydajność operacji zapisu na dysku i zmniejszyć ilość pamięci podręcznej dostępnej dla operacji odczytu na `Read-only` `Read/Write` dysku danych.  |
|**Dysk systemu operacyjnego** | Domyślne zasady buforowania mogą być lub `Read-only` `Read/write` dla dysku systemu operacyjnego. <br/> Nie zaleca się zmiany poziomu buforowania dysku systemu operacyjnego.  |
| **tempdb**| Jeśli nie można umieścić bazy danych tempdb na dysku efemeryjną ze względu na pojemność, zmień rozmiar maszyny wirtualnej, aby uzyskać większy dysk efemeralny, lub umieść tempdb na oddzielnym dysku danych ze skonfigurowanym `D:\` `Read-only` buforowaniem. <br/> Zarówno pamięć podręczna maszyny wirtualnej, jak i dysk efemertyczny używają lokalnego dysku SSD, dlatego należy o tym pamiętać, gdy rozmiar we/wy bazy danych tempdb będzie wliczać się do buforowanych limitów liczby we/wy i przepływności maszyny wirtualnej, gdy są hostowane na dysku efemeryjskim.| 
| | | 


Aby dowiedzieć się więcej, zobacz [Buforowanie dysku.](../../../virtual-machines/premium-storage-performance.md#disk-caching) 


## <a name="disk-striping"></a>Rozłożone dyski

Przeanalizuj przepływność i przepustowość wymaganą dla plików danych SQL, aby określić liczbę dysków danych, w tym plik dziennika i bazę danych tempdb. Limity przepływności i przepustowości różnią się w zależności od rozmiaru maszyny wirtualnej. Aby dowiedzieć się więcej, zobacz [Rozmiar maszyny wirtualnej](../../../virtual-machines/sizes.md)

Dodaj dodatkowe dyski danych i użyj funkcji usuwania dysków, aby uzyskać większą przepływność. Na przykład aplikacja, która wymaga 12 000 IOPS i 180 MB/s przepływności, może użyć trzech rozłożonych dysków P30, aby dostarczyć 15 000 IOPS i 600 MB/s przepływności. 

Aby skonfigurować rozbieranie dysków, zobacz [informacje na temat usuwania dysków.](storage-configuration.md#disk-striping) 

## <a name="disk-capping"></a>Limity dysków 

Istnieją limity przepływności zarówno na poziomie dysku, jak i maszyny wirtualnej. Maksymalne limity liczby IOPS na maszynę wirtualną i na dysk różnią się i są od siebie niezależne.

Aplikacje, które zużywają zasoby wykraczające poza te limity, zostaną ograniczone (nazywane również ograniczeniami). Wybierz maszynę wirtualną i rozmiar dysku w pasie dysku, który spełnia wymagania aplikacji i nie będzie podlegać ograniczeniom. Aby rozwiązać ten temat, należy użyć buforowania lub dostroić aplikację tak, aby wymagana jest mniejsza przepływność.

Na przykład aplikacja, która wymaga 12 000 IOPS i 180 MB/s, może: 
- Użyj [Standard_M32ms,](../../../virtual-machines/m-series.md) która ma maksymalną przepływność dysku bez pamięci 20 000 IOPS i 500 MB/s.
- Trzy dyski P30 zapewniają przepływność 15 000 IOPS i 600 MB/s.
- Użyj maszyny [Standard_M16ms](../../../virtual-machines/m-series.md) wirtualnej i użyj buforowania hosta, aby wykorzystać lokalną pamięć podręczną przez zużywanie przepływności. 

Maszyny wirtualne skonfigurowane do skalowania w górę w czasie wysokiego wykorzystania powinny aprowizować magazyn z wystarczającą liczbą IOPS i przepływnością do obsługi maksymalnego rozmiaru maszyny wirtualnej przy zachowaniu ogólnej liczby dysków mniejszej lub równej maksymalnej liczbie obsługiwanej przez najmniejszą docelową jednostkę SKU maszyny wirtualnej.

Aby uzyskać więcej informacji na temat ograniczeń ograniczeń dotyczących dysków i używania buforowania w celu uniknięcia ograniczania, zobacz Disk IO limiting (Limity [we/wy dysku).](../../../virtual-machines/disks-performance.md)

> [!NOTE] 
> Niektóre ograniczenia dysków mogą nadal powodować zadowalającą wydajność dla użytkowników; Dostrajanie i obsługa obciążeń zamiast zmieniania rozmiaru na większą maszynę wirtualną w celu zrównoważenia kosztów i wydajności w firmie. 


## <a name="write-acceleration"></a>Przyspieszanie zapisu

Przyspieszanie zapisu to funkcja dysku, która jest dostępna tylko dla maszyn wirtualnych Virtual Machines [serii M.](https://docs.microsoft.com/azure/virtual-machines/m-series) Celem przyspieszania zapisu jest zwiększenie opóźnienia operacji we/wy zapisu w usłudze Azure Premium Storage, gdy potrzebujesz opóźnienia we/wy o pojedynczej cyfrze z powodu dużych obciążeń OLTP o znaczeniu krytycznym dla misji lub środowisk magazynu danych. 

Użyj przyspieszania zapisu, aby poprawić opóźnienie zapisu na dysku hostującym pliki dziennika. Nie używaj przyspieszania zapisu dla SQL Server danych. 

akcelerator zapisu mają ten sam limit liczby IOPS co maszyna wirtualna. Dołączone dyski nie mogą przekroczyć akcelerator zapisu liczby IOPS dla maszyny wirtualnej.  

W poniższej tabeli przedstawiono liczbę dysków danych i liczbę obsługiwanych we/wy na sekundę na maszynę wirtualną: 

| Jednostka SKU maszyny wirtualnej  | Liczba akcelerator zapisu dysków  | akcelerator zapisu we/wy dysku na maszynę wirtualną  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10 000  |
| M64ms, M64ls, M64s  |  8 | 10 000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Istnieje wiele ograniczeń dotyczących używania przyspieszania zapisu. Aby dowiedzieć się więcej, zobacz [Ograniczenia podczas korzystania z akcelerator zapisu](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Porównanie z dyskiem w chmurze Azure Ultra

Największa różnica między akceleracjam zapisu a dyskami w warstwie Ultra platformy Azure polega na tym, że przyspieszanie zapisu jest funkcją maszyny wirtualnej dostępną tylko dla dysków serii M i Azure Ultra. Przyspieszanie zapisu to zoptymalizowana pod kątem zapisu pamięć podręczna z własnymi ograniczeniami w zależności od rozmiaru maszyny wirtualnej. Dyski w warstwie Ultra platformy Azure to opcja magazynu dysków o małym opóźnieniu dla usługi Azure Virtual Machines. 

Jeśli to możliwe, użyj przyspieszenia zapisu na dyskach w warstwie Ultra dla dysku dziennika transakcji. W przypadku maszyn wirtualnych, które nie obsługują przyspieszania zapisu, ale wymagają małych opóźnień w dzienniku transakcji, użyj dysków w warstwie Ultra platformy Azure. 

## <a name="monitor-storage-performance"></a>Monitorowanie wydajności magazynu

Aby ocenić potrzeby magazynu i określić, jak dobrze działa magazyn, musisz zrozumieć, co mierzyć i co oznaczają te wskaźniki. 

[Liczba IOPS (dane wejściowe/wyjściowe](../../../virtual-machines/premium-storage-performance.md#iops) na sekundę) to liczba żądań, które aplikacja zwraca do magazynu na sekundę. Zmierz IOPS przy użyciu monitor wydajności liczników `Disk Reads/sec` i `Disk Writes/sec` . [Aplikacje OLTP (przetwarzanie transakcji online)](/azure/architecture/data-guide/relational-data/online-transaction-processing) muszą zapewnić wyższą wartość operacji we/wy na poziomie operacji we/wy, aby osiągnąć optymalną wydajność. Aplikacje, takie jak systemy przetwarzania płatności, zakupy online i systemy punktów sprzedaży detalicznej, są przykładami aplikacji OLTP.

[Przepływność](../../../virtual-machines/premium-storage-performance.md#throughput) to ilość danych wysyłanych do bazowego magazynu, często mierzona w megabajtach na sekundę. Zmierz przepływność za pomocą monitor wydajności liczników `Disk Read Bytes/sec` i `Disk Write Bytes/sec` . [Magazynowanie danych jest zoptymalizowane pod](/azure/architecture/data-guide/relational-data/data-warehousing) kątem maksymalizowania przepływności przez IOPS. Aplikacje, takie jak magazyny danych do analizy, raportowania, strumienia roboczego ETL i innych celów analizy biznesowej, są przykładami aplikacji do magazynowania danych.

Rozmiary jednostek We/Wy mają wpływ na możliwości we/wy i przepływności, ponieważ mniejsze rozmiary we/wy dają większą wartość we/wy, a większe rozmiary we/wy dają wyższą przepływność. SQL Server automatycznie wybiera optymalny rozmiar we/wy. Aby uzyskać więcej informacji, zobacz [Optimize IOPS, throughput, and latency for your applications (Optymalizowanie](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)liczby operacji we/wy na zapisu na dane, przepływności i opóźnienia dla aplikacji). 

Istnieją określone Azure Monitor, które są bezcenne w przypadku odnajdywania limitów na poziomie maszyny wirtualnej i dysku, a także zużycia i kondycji pamięci podręcznej AzureBlob. Aby zidentyfikować kluczowe liczniki do dodania do rozwiązania do monitorowania i pulpitu nawigacyjnego Azure Portal, zobacz [Metryki wykorzystania magazynu](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor obecnie nie oferuje metryk na poziomie dysku dla efemerycznego dysku `(D:\)` tymczasowego. Procent użycia pamięci podręcznej maszyny wirtualnej i procent zużytej przepustowości pamięci podręcznej maszyny wirtualnej będą odzwierciedlać wartość IOPS i przepływność zarówno z efemeralnego dysku tymczasowego, jak i buforowania `(D:\)` hosta razem.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących wydajności, zobacz inne artykuły w tej serii:
- [Szybka lista kontrolna](performance-guidelines-best-practices-checklist.md)
- [Rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md)
- [Zbieranie planu bazowego](performance-guidelines-best-practices-collect-baseline.md)

Najlepsze rozwiązania w zakresie zabezpieczeń można znaleźć [w tesłudze Security considerations for SQL Server on Azure Virtual Machines](security-considerations-best-practices.md)(Zagadnienia dotyczące zabezpieczeń w usłudze Azure Virtual Machines).

Aby uzyskać szczegółowe informacje na temat SQL Server wydajności na platformie Azure za pomocą protokołu TPC-E i testów porównawczych TPC_C, zapoznaj się z blogiem Optimize OLTP performance (Optymalizowanie wydajności [OLTP).](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)

Zapoznaj się z innymi SQL Server maszyny wirtualnej na [stronie SQL Server w witrynie Azure Virtual Machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md)(Omówienie usługi Azure Virtual Machines). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
