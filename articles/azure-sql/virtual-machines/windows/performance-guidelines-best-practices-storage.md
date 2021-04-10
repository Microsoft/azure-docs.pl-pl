---
title: 'Magazyn: najlepsze rozwiązania dotyczące wydajności & wytyczne'
description: Zapewnia najlepsze rozwiązania i wskazówki dotyczące magazynu w celu zoptymalizowania wydajności SQL Server na maszynie wirtualnej platformy Azure.
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
ms.openlocfilehash: 001a9a15c259d0b0d73eec9c9a39ad7c27f26721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572521"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Magazyn: najlepsze rozwiązania dotyczące wydajności SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule przedstawiono najlepsze rozwiązania i wskazówki dotyczące usługi Storage w celu zoptymalizowania wydajności SQL Server na platformie Azure Virtual Machines.

Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ta seria najlepszych rozwiązań dotyczących wydajności koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, może nie wymagać każdej zalecanej optymalizacji. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

Aby dowiedzieć się więcej, zobacz inne artykuły w tej serii: [Lista kontrolna wydajności](performance-guidelines-best-practices-checklist.md), [rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md)i [zbieranie danych bazowych](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Lista kontrolna

Zapoznaj się z poniższą listą kontrolną, aby zapoznać się z krótkim omówieniem najlepszych rozwiązań dotyczących magazynu, które szczegółowo omówiono w dalszej części artykułu: 

- Przed wybraniem typu dysku należy monitorować aplikację i [określić wymagania dotyczące przepustowości magazynu i opóźnień](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) dla SQL Server danych, dzienników i tempdb. 
- Aby zoptymalizować wydajność magazynu, należy zaplanować najwyższą liczbę operacji we/wy niebuforowanej pamięci podręcznej, a także użyć buforowania danych jako funkcji wydajności dla operacji odczytywania danych przy jednoczesnym uniknięciu [pułapek maszyn wirtualnych i dysków](../../../virtual-machines/premium-storage-performance.md#throttling).
- Umieszczaj pliki danych, dzienników i tempdb na oddzielnych dyskach.
    - W przypadku dysku danych do zapewnienia dostępności obsługi pamięci podręcznej Używaj tylko [dysków Premium P30 i P40](../../../virtual-machines/disks-types.md#premium-ssd) .
    - W przypadku planu dysku dziennika pod kątem pojemności i wydajności testowej w porównaniu z kosztami podczas oceniania [dysków w warstwie Premium P30-P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Jeśli wymagane jest opóźnienie magazynu, użyj [usługi Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) dla dziennika transakcji. 
      - W przypadku wdrożeń maszyn wirtualnych z serii M należy wziąć pod uwagę możliwość [zapisu akceleratora](../../../virtual-machines/how-to-enable-write-accelerator.md) przy użyciu usługi Azure Ultra Disks.
    - W przypadku większości obciążeń SQL Server należy umieścić [bazę danych tempdb](/sql/relational-databases/databases/tempdb-database) na lokalnym `D:\` dysku SSD. 
      - Jeśli pojemność dysku lokalnego nie jest wystarczająca dla bazy danych tempdb, należy rozważyć zmianę rozmiarów maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zasady buforowania plików danych](#data-file-caching-policies) .
- Rozdziel wiele dysków danych platformy Azure przy użyciu funkcji [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview) , aby zwiększyć przepustowość we/wy do wartości IOPS i limity przepływności docelowej maszyny wirtualnej.
- Ustaw [buforowanie hosta](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) na tylko do odczytu dla dysków plików danych.
- W obszarze [buforowanie hosta](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) ustaw wartość Brak dla dysków w pliku dziennika.
    - Nie włączaj buforowania odczytu/zapisu na dyskach, które zawierają pliki SQL Server. 
    - Przed zmianą ustawień pamięci podręcznej na dysku zawsze Zatrzymaj usługę SQL Server.
- W przypadku obciążeń związanych z programowaniem i testowaniem oraz długoterminowej archiwizacji kopii zapasowych należy rozważyć użycie magazynu w warstwie Standardowa. Nie zaleca się używania HDD w warstwie Standardowa/SDD do obciążeń produkcyjnych.
- Zmniejszenie obciążeń [dysków opartych na kredycie](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) powinno być brane pod uwagę tylko w przypadku mniejszego obciążenia i systemów działowych.
- Zainicjuj obsługę konta magazynu w tym samym regionie co maszyna wirtualna SQL Server. 
- Wyłącz magazyn Geograficznie nadmiarowy platformy Azure (replikację geograficzną) i użyj LRS (Magazyn lokalnie nadmiarowy) na koncie magazynu.
- Sformatuj dysk danych, aby używał rozmiaru jednostki alokacji 64 KB dla wszystkich plików danych umieszczonych na dysku innym niż dysk tymczasowy `D:\` (o wartości domyślnej 4 KB). SQL Server maszyny wirtualne wdrożone za pośrednictwem portalu Azure Marketplace są dostarczane z dyskami danych sformatowanymi przy użyciu rozmiaru jednostki alokacji i przeplotu dla puli magazynu ustawionej na 64 KB. 

Aby porównać listę kontrolną magazynu z innymi, zapoznaj się z [listą kontrolną](performance-guidelines-best-practices-checklist.md)kompleksowych najlepszych rozwiązań dotyczących wydajności. 

## <a name="overview"></a>Omówienie

Aby znaleźć najbardziej efektywną konfigurację dla SQL Server obciążeń na maszynie wirtualnej platformy Azure, Zacznij od [mierzenia wydajności magazynu aplikacji biznesowej](performance-guidelines-best-practices-collect-baseline.md#storage). Po znalezieniu wymagań dotyczących magazynu wybierz maszynę wirtualną, która obsługuje zbędną liczbę IOPS i przepływność przy użyciu odpowiedniego współczynnika pamięci do rdzeń wirtualny. 

Wybierz rozmiar maszyny wirtualnej z wystarczającą skalowalnością magazynu dla obciążenia i kombinację dysków (zazwyczaj w puli magazynów), które spełniają wymagania dotyczące pojemności i wydajności Twojej firmy. 

Typ dysku zależy od typu pliku, który jest hostowany na dysku, oraz wymagań dotyczących wydajności szczytowej.

> [!TIP]
> Inicjowanie obsługi maszyny wirtualnej SQL Server za pomocą Azure Portal ułatwia przeprowadzenie procesu konfiguracji magazynu oraz implementację większości najlepszych rozwiązań dotyczących magazynu, takich jak Tworzenie oddzielnych pul magazynów dla plików danych i dziennika, kierowanie tempdb na `D:\` dysk i włączanie optymalnych zasad buforowania. Aby uzyskać więcej informacji na temat aprowizacji i konfigurowania magazynu, zobacz [Konfiguracja magazynu maszyny wirtualnej SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Możesz wybrać poziom wydajności dla dysków. Typy zarządzanych dysków dostępne jako magazyn podstawowy (na przykład zwiększające wydajność wydajności) to standardowe dyski twarde (dysk twardy), standardowe dysków SSD, dyski półprzewodnikowe (SSD) i Ultra Disks. 

Wydajność dysku zwiększa się wraz z pojemnością, pogrupowaną według [etykiet dysków Premium](../../../virtual-machines/disks-types.md#premium-ssd) , takich jak P1 z 4 GiB miejsca i 120 operacji we/wy na P80 z 32 TIB magazynu i 20 000 operacji we/wy na sekundę. Usługa Premium Storage obsługuje pamięć podręczną magazynu, która ułatwia zwiększenie wydajności odczytu i zapisu w przypadku niektórych obciążeń. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Managed disks](../../../virtual-machines/managed-disks-overview.md). 

Istnieją również trzy główne [typy dysków](../../../virtual-machines/managed-disks-overview.md#disk-roles) , które należy wziąć pod uwagę w SQL Server na maszynie wirtualnej platformy Azure — dysku systemu operacyjnego, dysku tymczasowym i dyskach danych. Starannie wybierz, co ma być przechowywane na dysku systemu operacyjnego `(C:\)` i tymczasowe dysk tymczasowy `(D:\)` . 

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego to wirtualny dysk twardy, który można uruchomić i zainstalować jako działającą wersję systemu operacyjnego i oznaczony jako `C:\` dysk. Podczas tworzenia maszyny wirtualnej platformy Azure platforma dołączy co najmniej jeden dysk do maszyny wirtualnej dla dysku systemu operacyjnego. `C:\`Dysk jest domyślną lokalizacją instalacji aplikacji i konfiguracji pliku. 

W przypadku środowisk SQL Server produkcyjnych nie należy używać dysku systemu operacyjnego dla plików danych, plików dziennika i dzienników błędów. 

### <a name="temporary-disk"></a>Dysk tymczasowy

Wiele maszyn wirtualnych platformy Azure zawiera inny typ dysku nazywany dyskiem tymczasowym (oznaczonym jako `D:\` dysk). W zależności od serii maszyn wirtualnych i rozmiaru pojemności tego dysku będą się różnić. Dysk tymczasowy jest nieulotny, co oznacza, że magazyn dyskowy jest ponownie tworzony (w programie, zostaje cofnięty przydział i przydzieloną), po ponownym uruchomieniu maszyny wirtualnej lub przeniesieniu na inny host (na przykład na potrzeby [naprawy usługi](/troubleshoot/azure/virtual-machines/understand-vm-reboot)). 

Tymczasowy dysk magazynu nie jest utrwalony w magazynie zdalnym i dlatego nie powinien przechowywać plików bazy danych użytkownika, plików dziennika transakcji ani wszystkich elementów, które muszą zostać zachowane. 

Umieść bazę danych tempdb na lokalnym tymczasowym `D:\` dysku SSD dla obciążeń SQL Server, chyba że użycie lokalnej pamięci podręcznej nie jest problemem. W przypadku korzystania z maszyny wirtualnej, która nie [ma dysku tymczasowego](../../../virtual-machines/azure-vms-no-temp-disk.md) , zaleca się umieszczenie bazy danych tempdb na osobnym dysku izolowanym lub puli magazynów z ustawionym buforowaniem tylko do odczytu. Aby dowiedzieć się więcej, zobacz [Zasady buforowania danych tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Dyski danych

Dyski z danymi to dyski magazynu zdalnego, które są często tworzone w [pulach magazynów](/windows-server/storage/storage-spaces/overview) w celu przekroczenia pojemności i wydajności, które każdy pojedynczy dysk może zaoferować maszynie wirtualnej.

Dołącz minimalną liczbę dysków, która spełnia wymagania operacji we/wy na sekundę, przepływności i pojemności. Nie przekraczaj maksymalnej liczby dysków danych z najmniejszej maszyny wirtualnej, do której chcesz zmienić rozmiar.

Umieszczaj pliki danych i dziennika na dyskach z danymi, które są obsługiwane w celu uzyskania najlepszych wymagań dotyczących wydajności. 

Sformatuj dysk danych, aby używał rozmiaru jednostki alokacji 64 KB dla wszystkich plików danych umieszczonych na dysku innym niż dysk tymczasowy `D:\` (o wartości domyślnej 4 KB). SQL Server maszyny wirtualne wdrożone za pośrednictwem portalu Azure Marketplace są dostarczane z dyskami danych sformatowanymi przy użyciu rozmiaru jednostki alokacji i przeplotu dla puli magazynu ustawionej na 64 KB. 

## <a name="premium-disks"></a>Dyski w warstwie Premium

Używaj dysków SSD w warstwie Premium dla plików danych i dziennika dla obciążeń SQL Server produkcyjnych. SSD w warstwie Premium IOPS i przepustowość różnią się w zależności od [rozmiaru i typu dysku](../../../virtual-machines/disks-types.md). 

W przypadku obciążeń produkcyjnych należy użyć dysków P30 i/lub P40 do SQL Server plików danych, aby zapewnić obsługę buforowania i użyć P30 do P80 dla SQL Server plików dziennika transakcji.  Aby uzyskać najlepszy łączny koszt posiadania, Zacznij od P30s (5000 IOPS/200 MB/s) dla plików danych i dzienników i wybieraj tylko wyższe pojemności, gdy musisz kontrolować liczbę dysków maszyny wirtualnej.

W przypadku obciążeń OLTP dopasowanie do docelowej liczby operacji we/wy na dysk (lub w puli magazynu) z wymaganiami dotyczącymi wydajności przy użyciu obciążeń w godzinach szczytu i `Disk Reads/sec`  +  `Disk Writes/sec` liczników wydajności. W przypadku obciążeń hurtowni danych i raportowania dopasowanie przepływności docelowej przy użyciu obciążeń w godzinach szczytu i `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

Użyj funkcji miejsca do magazynowania, aby osiągnąć optymalną wydajność, skonfigurować dwie pule, jedną dla plików dziennika, a drugą dla plików danych. Jeśli nie korzystasz z rozłożenia dysku, użyj dwóch dysków SSD w warstwie Premium mapowanych na oddzielne dyski, w których jeden dysk zawiera plik dziennika, a drugi zawiera dane.

Alokowane operacje we [/wy](../../../virtual-machines/disks-types.md#premium-ssd) na dysk, które są używane jako część puli magazynu. Połączone możliwości operacji we/wy i przepływności dysków to maksymalna zdolność do ograniczeń przepływności maszyny wirtualnej.

Najlepszym rozwiązaniem jest użycie możliwie najmniejszej liczby dysków, które spełniają minimalne wymagania dotyczące operacji we/wy na sekundę (przepływności) i pojemności. Jednak równowaga ceny i wydajności jest lepsza dzięki dużej liczbie małych dysków, a nie na małej liczbie dużych dysków.

### <a name="scaling-premium-disks"></a>Skalowanie dysków w warstwie Premium

Po pierwszym wdrożeniu dysku zarządzanego na platformie Azure warstwa wydajności dla tego dysku zależy od rozmiaru dysku. Wyznacz warstwę wydajności w ramach wdrożenia lub Zmień ją później, bez zmiany rozmiaru dysku. W przypadku zwiększenia zapotrzebowania można zwiększyć poziom wydajności, aby spełnić potrzeby biznesowe. 

Zmiana warstwy wydajności pozwala administratorom na przygotowanie się do i spełnienie wyższego popytu bez polegania na [dyskach](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Wyższą wydajność należy uzyskać tak długo, jak jest to konieczne, gdzie rozliczenia zaprojektowano w celu spełnienia warstwy wydajności magazynu. Uaktualnij warstwę, aby była zgodna z wymaganiami dotyczącymi wydajności bez zwiększania wydajności. Wróć do oryginalnej warstwy, gdy dodatkowa wydajność nie jest już wymagana.

To ekonomiczne i tymczasowe rozszerzanie wydajności jest silnym przypadkiem użycia dla zdarzeń kierowanych, takich jak zakupy, testowanie wydajności, zdarzenia szkoleniowe i inne krótkie okna, w przypadku których większa wydajność jest wymagana tylko w krótkim czasie. 

Aby uzyskać więcej informacji, zobacz [warstwy wydajności dla dysków zarządzanych](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra Disk

Jeśli istnieje potrzeba czasu reakcji na podmilisekundy z ograniczonym opóźnieniem, rozważ użycie [usługi Azure Ultra Disk](../../../virtual-machines/disks-types.md#ultra-disk) do SQL Server dysku dziennika, a nawet dysku danych dla aplikacji, które są niezwykle wrażliwe na opóźnienia we/wy. 

Można skonfigurować Ultra dysk, gdy pojemność i liczba operacji we/wy na sekundę mogą być skalowane niezależnie. Administratorzy systemu Ultra Disk mogą udostępniać dysk z pojemnością, IOPSm i wymaganiami dotyczącymi przepływności w zależności od potrzeb aplikacji. 

Program Ultra Disk nie jest obsługiwany przez całą serię maszyn wirtualnych i ma inne ograniczenia, takie jak dostępność regionów, nadmiarowość i pomoc techniczna dla Azure Backup. Aby dowiedzieć się więcej, zobacz [Korzystanie z usługi Azure Ultra disks](../../../virtual-machines/disks-enable-ultra-ssd.md) w celu uzyskania pełnej listy ograniczeń. 

## <a name="standard-hdds-and-ssds"></a>Standardowe HDD i dysków SSD

[Standardowe HDD](../../../virtual-machines/disks-types.md#standard-hdd) i dysków SSD mają różne opóźnienia i przepustowość i są zalecane tylko w przypadku obciążeń deweloperskich/testowych. Obciążenia produkcyjne powinny używać Premium dysków SSD. W przypadku korzystania z SSD w warstwie Standardowa (scenariusze tworzenia i testowania) zaleca się dodanie maksymalnej liczby dysków danych obsługiwanych przez [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) i użycie rozłożenia dysku z funkcją miejsca do magazynowania w celu uzyskania najlepszej wydajności.

## <a name="caching"></a>Buforowanie

Maszyny wirtualne obsługujące pamięć podręczną Premium Storage mogą korzystać z dodatkowej funkcji o nazwie Azure BlobCache lub buforowania hosta, aby zwiększyć liczbę operacji we/wy na sekundę i przepływność maszyny wirtualnej. W przypadku maszyn wirtualnych włączonych zarówno do magazynu Premium Storage, jak i pamięci podręcznej Premium Storage dostępne są dwa różne limity przepustowości magazynu, które mogą być używane razem w celu zwiększenia wydajności magazynu.

Przepływy operacji we/wy i MB/s nie są wliczane do limitów przepływności dysku w pamięci podręcznej maszyny wirtualnej. Maksymalne limity pamięci podręcznej zapewniają dodatkowy bufor dla operacji odczytu, który pomaga zwiększyć i nieoczekiwane szczyty.

Włącz buforowanie w warstwie Premium, gdy opcja jest obsługiwana, aby znacząco poprawić wydajność operacji odczytu na dysku danych bez dodatkowych kosztów. 

Odczytuje i zapisuje dane w usłudze Azure BlobCache (buforowane operacje we/wy i przepływność) nie są wliczane do limitów operacji we/wy niebuforowanej maszyny wirtualnej.

> [!NOTE]
> Buforowanie dysków nie jest obsługiwane w przypadku dysków 4 TiB i większych (P50 i większych). Jeśli do maszyny wirtualnej są dołączone wiele dysków, każdy dysk o rozmiarze mniejszym niż 4 TiB będzie obsługiwał buforowanie. Aby uzyskać więcej informacji, zobacz [buforowanie dysków](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Niebuforowana przepływność

Maksymalna liczba operacji we/wy dysku w pamięci podręcznej to maksymalny limit magazynu zdalnego, który może obsłużyć maszyna wirtualna. Ten limit jest zdefiniowany na maszynie wirtualnej i nie jest limitem magazynu dyskowego. Ten limit dotyczy tylko dysków z danymi połączonymi zdalnie z maszyną wirtualną, a nie lokalnego wejścia/wyjścia na dysk tymczasowy ( `D:\` dysk) lub dysk systemu operacyjnego.

Liczba operacji we/wy pamięci podręcznej, które są dostępne dla maszyny wirtualnej, można zweryfikować w dokumentacji maszyny wirtualnej.

Na przykład dokumentacja [serii M](../../../virtual-machines/m-series.md) pokazuje, że maksymalna niebuforowana przepływność dla maszyny wirtualnej Standard_M8ms wynosi 5000 operacji we/wy i 125 MB/s z niebuforowanej przepływności dysku. 

![Zrzut ekranu przedstawiający niebuforowaną dokumentację przepływności dysku z serii M.](./media/performance-guidelines-best-practices/m-series-table.png)

Podobnie, można zobaczyć, że Standard_M32ts obsługuje 20 000 operacji we/wy na dysku w pamięci podręcznej i 500 MB/s pamięci podręcznej. Ten limit reguluje się na poziomie maszyny wirtualnej, niezależnie od bazowego magazynu dyskowego w warstwie Premium.

Aby uzyskać więcej informacji, zobacz [limity pamięci podręcznej i buforowania](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Przepływność magazynu w pamięci podręcznej i tymczasowej

Maksymalny limit przepływności pamięci podręcznej i tymczasowej jest oddzielnym limitem od niebuforowanego limitu przepływności na maszynie wirtualnej. Usługa Azure BlobCache składa się z kombinacji pamięci RAM dostępu swobodnego hosta maszyny wirtualnej i lokalnie dołączonego dysku SSD. Dysk tymczasowy ( `D:\` dysk) w ramach maszyny wirtualnej jest również hostowany na tym lokalnym dysku SSD.

Maksymalny limit przepływności pamięci podręcznej i tymczasowej umożliwia zarządzanie we/wy na lokalnym dysku tymczasowym ( `D:\` dysk) i na platformie Azure BlobCache tylko wtedy, **gdy** buforowanie hosta jest włączone. 

Gdy buforowanie jest włączone w usłudze Premium Storage, maszyny wirtualne można skalować poza ograniczeniami liczby operacji we/wy niebuforowanej pamięci podręcznej dla magazynu zdalnego.  

Tylko niektóre maszyny wirtualne obsługują pamięć podręczną Premium Storage i Premium Storage (co należy zweryfikować w dokumentacji maszyny wirtualnej). Na przykład dokumentacja [serii M](../../../virtual-machines/m-series.md) wskazuje, że obsługiwane są zarówno buforowanie Premium Storage, jak i pamięć podręczna Premium Storage: 

![Zrzut ekranu przedstawiający obsługę Premium Storage serii M.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Limity pamięci podręcznej różnią się w zależności od rozmiaru maszyny wirtualnej. Na przykład maszyna wirtualna Standard_M8ms obsługuje 10000 operacji we/wy na dysku w pamięci podręcznej i 1000 MB/s pamięci podręcznej, z łącznym 793 rozmiarem pamięci przeGiB. Podobnie maszyna wirtualna Standard_M32ts obsługuje 40000 operacji we/wy na dysku w pamięci podręcznej i 400 MB/s pamięci podręcznej, z łącznym rozmiarem pamięci po 3174 GiB 

![Zrzut ekranu przedstawiający załączoną dokumentację przepływności dysku w pamięci podręcznej.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Buforowanie hosta można włączyć ręcznie na istniejącej maszynie wirtualnej. Zatrzymaj wszystkie obciążenia aplikacji i usługi SQL Server przed wprowadzeniem jakichkolwiek zmian w zasadach buforowania maszyny wirtualnej. Zmiana ustawień pamięci podręcznej maszyny wirtualnej spowoduje odłączenie dysku docelowego i jego odłączenie po zastosowaniu ustawień.

### <a name="data-file-caching-policies"></a>Zasady buforowania plików danych

Zasady buforowania magazynu różnią się w zależności od typu plików danych SQL Server, które są hostowane na dysku. 

Poniższa tabela zawiera podsumowanie zalecanych zasad buforowania na podstawie typu danych SQL Server: 

|Dysk SQL Server |Zalecenie |
|---------|---------|
| **Dysk danych** | Włącz `Read-only` buforowanie dysków, na których znajdują się pliki danych SQL Server. <br/> Odczyty z pamięci podręcznej będą szybsze niż odczyty z dysku z danymi w pamięci podręcznej. <br/> Niebuforowane operacje we/wy i przepływność oraz buforowane operacje we/wy i przepływność umożliwiają uzyskanie całkowitej możliwej wydajności dostępnej z maszyny wirtualnej w ramach limitów maszyn wirtualnych, ale rzeczywista wydajność będzie się różnić w zależności od możliwości obciążenia pamięci podręcznej (Współczynnik trafień pamięci podręcznej). <br/>|
|**Dysk dziennika transakcji**|Ustaw zasady buforowania na `None` dyski obsługujące dziennik transakcji.  Włączenie pamięci podręcznej dla dysku dziennika transakcji nie przynosi żadnej korzyści z wydajności, a w rzeczywistości, gdy `Read-only` `Read/Write` buforowanie jest włączone na dysku dziennika, może obniżyć wydajność zapisu na dysku i zmniejszyć ilość pamięci podręcznej dostępnej do odczytu na dysku danych.  |
|**Dysk systemu operacyjnego operacyjnego** | Domyślne zasady buforowania mogą być `Read-only` lub `Read/write` dla dysku systemu operacyjnego. <br/> Nie zaleca się zmiany poziomu buforowania dysku systemu operacyjnego.  |
| **tempdb**| Jeśli baza danych tempdb nie może zostać umieszczona na dysku tymczasowych ze względu `D:\` na pojemność, należy zmienić rozmiar maszyny wirtualnej, aby uzyskać większy dysk tymczasowych lub umieścić bazę tempdb na osobnym dysku z danymi `Read-only` skonfigurowanymi do buforowania. <br/> W pamięci podręcznej maszyn wirtualnych i dysku tymczasowych używany jest lokalny dysk SSD, dlatego należy pamiętać o tym, gdy rozmiar w ramach operacji we/wy w formacie tempdb będzie uwzględniać limity limitów maszyn wirtualnych operacji wejścia/wyjścia na sekundę, gdy są hostowane na dysku tymczasowych.| 
| | | 


Aby dowiedzieć się więcej, zobacz [buforowanie dysków](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Rozkładanie dysku

Analizuj przepustowość i przepustowość wymaganą dla plików danych SQL, aby określić liczbę dysków z danymi, w tym plik dziennika i bazę danych tempdb. Limity przepływności i przepustowości różnią się w zależności od rozmiaru maszyny wirtualnej. Aby dowiedzieć się więcej, zobacz [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes.md)

Dodawanie dodatkowych dysków danych i używanie rozkładu dysku w celu uzyskania większej przepływności. Na przykład aplikacja wymagająca przepływności 12 000 operacji we/wy i 180 MB/s może korzystać z trzech rozłożonych dysków P30, aby 15 000 zapewnić przepływność operacji we/wy i 600 MB/s. 

Aby skonfigurować rozłożenie dysku, zobacz artykuł [rozkładanie dysku](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Przygórność dysku 

Istnieją limity przepływności zarówno na dysku, jak i na poziomie maszyny wirtualnej. Maksymalne limity IOPS na maszynę wirtualną i na dysk różnią się od siebie nawzajem.

Aplikacje korzystające z zasobów wykraczające poza te limity zostaną ograniczone (nazywane również limitem ograniczonym). Wybierz maszynę wirtualną i rozmiar dysku w obszarze dysku, który spełnia wymagania aplikacji i nie będzie określać ograniczeń ograniczenia. Aby zająć się ograniczeniami, należy użyć buforowania lub dostosować aplikację tak, aby była wymagana mniejsza przepływność.

Na przykład aplikacja wymagająca 12 000 operacji we/wy i 180 MB/s może: 
- Użyj [Standard_M32ms](../../../virtual-machines/m-series.md) , która ma maksymalną przepływność dysku w pamięci podręcznej wynoszącą 20 000 IOPS i 500 MB/s.
- Rozdziel trzy dyski P30, aby zapewnić przepływność 15 000 operacji we/wy i 600 MB/s.
- Użyj maszyny wirtualnej [Standard_M16ms](../../../virtual-machines/m-series.md) i użyj buforowania hosta, aby użyć pamięci podręcznej w celu wykorzystania nadmiernej przepływności. 

Maszyny wirtualne skonfigurowane do skalowania w górę w czasie wysokiego użycia powinny obsługiwać magazyn z wystarczającą liczbą operacji we/wy na sekundę, aby zapewnić obsługę maksymalnego rozmiaru maszyny wirtualnej, zachowując jednocześnie ogólną liczbę dysków mniejszą lub równą maksymalnej liczbie obsługiwaną przez najmniejszy magazyn maszyny wirtualnej przeznaczony do użycia.

Aby uzyskać więcej informacji na temat ograniczeń ograniczenia liczby dysków i korzystania z pamięci podręcznej w celu uniknięcia pułapek, zobacz [Limit operacji we/wy dysku](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Niektóre ograniczenia dysku mogą nadal powodować zadowalającą wydajność dla użytkowników; Dostrajaj i konserwowanie obciążeń zamiast zmieniać rozmiar na większą maszynę wirtualną, aby zrównoważyć koszt i wydajność firmy. 


## <a name="write-acceleration"></a>Przyspieszenie zapisu

Przyspieszenie zapisu to funkcja dyskowa, która jest dostępna tylko dla Virtual Machines [serii M](https://docs.microsoft.com/azure/virtual-machines/m-series) (maszyny wirtualne). Przyspieszenie zapisu polega na zwiększeniu opóźnienia operacji we/wy na platformie Azure Premium Storage, gdy wymagane jest opóźnienie jednocyfrowe we/wy ze względu na duże obciążenia OLTP lub środowiska magazynu danych. 

Użyj przyspieszenia zapisu, aby zwiększyć opóźnienie zapisu na dysku hostującym pliki dziennika. Nie używaj przyspieszenia zapisu dla plików danych SQL Server. 

Akcelerator zapisu dyski mają ten sam limit operacji we/wy co maszyna wirtualna. Dołączone dyski nie mogą przekroczyć limitu liczby operacji we/wy akcelerator zapisu dla maszyny wirtualnej.  

W poniższej tabeli przedstawiono liczbę dysków danych i liczby operacji we/wy na sekundę obsługiwanych na maszynę wirtualną: 

| Jednostka SKU maszyny wirtualnej  | Liczba dysków akcelerator zapisu  | akcelerator zapisu liczba operacji we/wy dysku na maszynę wirtualną  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10 000  |
| M64ms, M64ls, M64s  |  8 | 10 000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Istnieje szereg ograniczeń dotyczących używania przyspieszenia zapisu. Aby dowiedzieć się więcej, zobacz [ograniczenia podczas korzystania z akcelerator zapisu](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Porównywanie z usługą Azure Ultra Disk

Największą różnicą między przyspieszeniem zapisu a usługą Azure Ultra disks jest to, że przyspieszenie zapisu jest funkcją maszyny wirtualnej dostępną tylko dla serii M i Azure Ultra Disks. Przyspieszenie zapisu to pamięć podręczna zoptymalizowana pod kątem zapisu z własnymi ograniczeniami w zależności od rozmiaru maszyny wirtualnej. Usługa Azure Ultra disks to opcja magazynowania na dyskach o małych opóźnieniach dla usługi Azure Virtual Machines. 

Jeśli to możliwe, użyj przyspieszenia zapisu dla Ultra disks dla dysku dziennika transakcji. W przypadku maszyn wirtualnych, które nie obsługują przyspieszenia zapisu, ale wymagają małych opóźnień do dziennika transakcji, Użyj usługi Azure Ultra Disks. 

## <a name="monitor-storage-performance"></a>Monitorowanie wydajności magazynu

Aby ocenić potrzeby związane z magazynem i określić, jak działa magazyn, należy zrozumieć, co należy zmierzyć, i co oznaczają te wskaźniki. 

Operacje we [/wy na sekundę (dane wejściowe/wyjściowe w ciągu sekundy)](../../../virtual-machines/premium-storage-performance.md#iops) to liczba żądań przesyłanych przez aplikację do magazynu na sekundę. Zmierz operacje we/wy przy użyciu liczników monitora wydajności `Disk Reads/sec` i `Disk Writes/sec` . Aplikacje [OLTP (online Transaction Processing)](/azure/architecture/data-guide/relational-data/online-transaction-processing) wymagają większej liczby operacji we/wy, aby osiągnąć optymalną wydajność. Takie aplikacje jak systemy przetwarzania płatności, zakupy online i systemy sprzedaży detalicznej to wszystkie przykłady aplikacji OLTP.

[Przepływność](../../../virtual-machines/premium-storage-performance.md#throughput) to ilość danych wysyłanych do magazynu bazowego, często mierzona przez megabajty na sekundę. Zmierz przepływność przy użyciu liczników monitora wydajności `Disk Read Bytes/sec` i `Disk Write Bytes/sec` . [Magazynowanie danych](/azure/architecture/data-guide/relational-data/data-warehousing) jest zoptymalizowane wokół maksymalizacji przepływności za pośrednictwem operacji we/wy na sekundę. Wszystkie przykłady aplikacji do magazynowania danych, takich jak magazyny danych do analizy, raportowania, ETL workstreams oraz inne cele analizy biznesowej.

Rozmiary jednostek we/wy mają wpływ na liczbę operacji wejścia/wyjścia na sekundę i przepływność, ponieważ mniejsze rozmiary operacji we/wy zapewniają wyższą przepustowość. SQL Server automatycznie wybiera optymalny rozmiar operacji we/wy. Aby uzyskać więcej informacji na temat, zobacz [Optymalizacja operacji we/wy na sekundę, przepływności i opóźnień dla aplikacji](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Istnieją konkretne Azure Monitor metryki, które są niecenne do odnajdowania pułapek na poziomie maszyny wirtualnej i dysku, jak również zużycie i kondycja pamięci podręcznej AzureBlob. Aby zidentyfikować liczniki kluczy do dodania do rozwiązania monitorowania i pulpitu nawigacyjnego Azure Portal, zobacz [metryki wykorzystania magazynu](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor obecnie nie oferuje metryk na poziomie dysku dla tymczasowego dysku temp `(D:\)` . Liczba operacji wejścia/wyjścia w buforze maszyny wirtualnej wykorzystana procentowo, a wartość procentowa wykorzystanej przepustowości w pamięci podręcznej maszyny wirtualnej będzie odzwierciedlać operacje wejścia/wyjścia na sekundę `(D:\)`


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących wydajności, zobacz inne artykuły w tej serii:
- [Szybka lista kontrolna](performance-guidelines-best-practices-checklist.md)
- [Rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md)
- [Zbierz linię bazową](performance-guidelines-best-practices-collect-baseline.md)

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Aby uzyskać szczegółowe informacje na temat SQL Server wydajności na maszynach wirtualnych platformy Azure za pomocą testów TPC-E i TPC_C, zapoznaj się z blogiem [Optymalizacja wydajności platformy OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
