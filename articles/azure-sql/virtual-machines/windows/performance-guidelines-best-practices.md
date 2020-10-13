---
title: Wskazówki dotyczące wydajności SQL Server na platformie Azure | Microsoft Docs
description: Zawiera wskazówki dotyczące optymalizacji wydajności SQL Server w Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d720fdd05a4356b8fec2c007a4e5a3d1ea1cf00b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293309"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Wytyczne dotyczące wydajności programu SQL Server na maszynach wirtualnych Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności SQL Server w programie Microsoft Azure Virtual Machines.

## <a name="overview"></a>Omówienie

 W trakcie działania SQL Server na platformie Azure Virtual Machines zalecamy dalsze korzystanie z tych samych opcji dostrajania wydajności bazy danych, które mają zastosowanie do SQL Server w lokalnych środowiskach serwerów. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków z danymi.

[SQL Server obrazy obsługiwane w Azure Portal](sql-vm-create-portal-quickstart.md) postępuj zgodnie z ogólnymi najlepszymi rozwiązaniami konfiguracji magazynu (Aby uzyskać więcej informacji na temat konfigurowania magazynu, zobacz [Konfiguracja magazynu dla maszyn wirtualnych SQL Server](storage-configuration.md)). Po zainicjowaniu obsługi należy rozważyć zastosowanie innych optymalizacji omówionych w tym artykule. Podstawowe możliwości wyboru w obciążeniu i sprawdź, czy testowanie zostało przeprowadzone.

> [!TIP]
> Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ten artykuł koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, możesz nie wymagać każdej optymalizacji wymienionej poniżej. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

## <a name="quick-checklist"></a>Szybka lista kontrolna

Poniżej przedstawiono szybką listę kontrolną w celu uzyskania optymalnej wydajności SQL Server na platformie Azure Virtual Machines:

| Warstwowy | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny wirtualnej](#vm-size-guidance) | — Użyj rozmiarów maszyn wirtualnych z 4 lub więcej vCPU, takimi jak [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) lub wyższy lub [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) lub wyższy.<br/><br/> - [ES, EAS, DS i Das Series](../../../virtual-machines/sizes-general.md) oferują optymalną pamięć do vCPU współczynnika wymaganą dla wydajności obciążeń OLTP. <br/><br/> - [Seria M](../../../virtual-machines/m-series.md) oferuje największą ilość pamięci do vCPU współczynnika wymaganą dla wydajności o krytycznym znaczeniu i jest idealnym rozwiązaniem w przypadku obciążeń magazynu danych. <br/><br/> — Zbieraj dane o liczbie operacji we [/wy na sekundę](../../../virtual-machines/windows/premium-storage-performance.md#iops), wymagania dotyczące [przepływności](../../../virtual-machines/windows/premium-storage-performance.md#throughput)  i [opóźnień](../../../virtual-machines/windows/premium-storage-performance.md#latency) w godzinach szczytu, postępując zgodnie z [listą wymagań dotyczących wydajności aplikacji](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) , a następnie wybierz [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes-general.md) , który można skalować do wymagań dotyczących wydajności obciążeń.|
| [Storage](#storage-guidance) | — Aby uzyskać szczegółowe informacje na temat testowania wydajności SQL Server na platformie Azure Virtual Machines z użyciem testów TPC-E i TPC_C, zapoznaj się z blogiem [Optymalizacja wydajności OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> — Użyj [dysków SSD Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) , aby uzyskać najlepsze korzyści z cen/wydajności. Skonfiguruj [pamięć podręczną tylko do odczytu](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) dla plików danych i brak pamięci podręcznej dla pliku dziennika. <br/><br/> -Użyj [Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) , jeśli obciążenie wymaga mniej niż 1 ms magazynu. Aby dowiedzieć się więcej, zobacz [Migrowanie do programu Ultra Disk](storage-migrate-to-ultradisk.md) . <br/><br/> — Zbierz wymagania dotyczące opóźnień magazynu dla plików SQL Server, dzienników i tymczasowych baz danych, [monitorując aplikację](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) przed wybraniem typu dysku. Jeśli <są wymagane opóźnienia magazynu 1 ms, użyj Ultra disks, w przeciwnym razie użyj SSD w warstwie Premium. Jeśli małe opóźnienia są wymagane tylko dla pliku dziennika, a nie dla plików danych, [należy zastanowić się, że na dysku jest](../../../virtual-machines/disks-enable-ultra-ssd.md) wymagana liczba IOPS i poziomy przepływności tylko dla pliku dziennika. <br/><br/> -  [Udziały plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md) są zalecane jako magazyn udostępniony dla SQL Server wystąpienia klastra trybu failover. Udziały plików w warstwie Premium nie obsługują buforowania i oferują ograniczoną wydajność w porównaniu z dyskami SSD w warstwie Premium. Wybierz opcję Premium dyski zarządzane dyskami SSD za pośrednictwem udziałów plików w warstwie Premium dla autonomicznych wystąpień programu SQL Server; Jednak korzystanie z udziałów plików Premium dla magazynu klastra trybu failover w celu ułatwienia konserwacji i elastycznej skalowalności. <br/><br/> — Magazyn w warstwie Standardowa jest zalecany tylko w celach deweloperskich i testowych lub dla plików kopii zapasowej i nie powinien być używany w przypadku obciążeń produkcyjnych. <br/><br/> — Zachowaj [konto magazynu](../../../storage/common/storage-create-storage-account.md) i SQL Server maszynę wirtualną w tym samym regionie.<br/><br/> -Wyłącz [Magazyn Geograficznie nadmiarowy](../../../storage/common/storage-redundancy.md) platformy Azure (replikacja geograficzna) na koncie magazynu.  |
| [Dyski](#disks-guidance) | — Należy użyć co najmniej dwóch [dysków SSD w warstwie Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 dla plików dziennika i 1 dla plików danych). <br/><br/> — W przypadku obciążeń wymagających <1 ms operacji we/wy, Włącz akcelerator zapisu dla serii M i Rozważ użycie dysków SSD w warstwie Ultra dla programu es i serii DS. <br/><br/> -Włącz [buforowanie tylko do odczytu](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) na dyskach, na których znajdują się pliki danych.<br/><br/> -Dodaj dodatkową wydajność operacji we/wy na poziomie 20% Premium, która jest wymagana [w przypadku konfigurowania magazynu dla SQL Server danych, dzienników i plików tempdb](storage-configuration.md) <br/><br/> — Unikaj używania systemu operacyjnego lub dysków tymczasowych do przechowywania i rejestrowania baz danych.<br/><br/> -Nie włączaj buforowania na dyskach, które obsługują plik dziennika.  **Ważne**: zatrzymaj usługę SQL Server podczas zmieniania ustawień pamięci podręcznej dla dysku Virtual Machines platformy Azure.<br/><br/> -Rozpoznaj wiele dysków danych platformy Azure, aby zwiększyć przepustowość magazynu.<br/><br/> -Format z udokumentowanymi rozmiarami alokacji. <br/><br/> -Należy umieścić bazę danych TempDB na lokalnym `D:\` dysku SSD dla obciążeń o krytycznym znaczeniu SQL Server (po wybraniu prawidłowego rozmiaru maszyny wirtualnej). Jeśli tworzysz maszynę wirtualną na podstawie Azure Portal lub szablonów szybkiego startu platformy Azure i [umieścisz tymczasową bazę danych na dysku lokalnym](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) , nie potrzebujesz żadnych dalszych akcji. we wszystkich innych przypadkach postępuj zgodnie z instrukcjami w blogu dotyczącymi  [używania dysków SSD do przechowywania bazy danych tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) , aby zapobiec błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru tymczasowej bazy danych, umieść tymczasową bazę danych [w puli](../../../virtual-machines/windows/premium-storage-performance.md) magazynów na dyskach SSD w warstwie Premium z [buforowaniem tylko do odczytu](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching). |
| [WE/WY](#io-guidance) |-Włącz kompresję strony bazy danych.<br/><br/> -Włącz natychmiastowe inicjowanie plików danych.<br/><br/> -Ograniczanie automatycznego zwiększania rozmiaru bazy danych.<br/><br/> — Wyłącz Autozmniejszanie bazy danych.<br/><br/> — Przenoszenie wszystkich baz danych do dysków danych, w tym systemowych baz danych.<br/><br/> — Przechodź SQL Server dzienniku błędów i śledzenia katalogów plików na dyskach danych.<br/><br/> -Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych.<br/><br/> - [Włącz blokowane strony w pamięci](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> -Zastosuj SQL Server poprawki wydajnościowe. |
| [Specyficzne dla funkcji](#feature-specific-guidance) | — Wykonaj kopię zapasową bezpośrednio w usłudze Azure Blob Storage.<br/><br/>— Użyj [kopii zapasowych migawek plików](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) dla baz danych o rozmiarze większym niż 12 TB. <br/><br/>— Użyj wielu plików tymczasowych baz danych, 1 pliku na rdzeń, do 8 plików.<br/><br/>-Ustaw maksymalną ilość pamięci serwera na 90% lub do 50 GB w lewo dla systemu operacyjnego. <br/><br/>-Enable soft NUMA. |

Aby uzyskać więcej informacji na temat tego, *jak* i *dlaczego* należy wykonać te optymalizacje, zapoznaj się ze szczegółami i wskazówkami podanymi w poniższych sekcjach.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny wirtualnej

Zacznij od zebrania wymagań dotyczących wydajności procesora CPU, pamięci i magazynu dla obciążenia w godzinach szczytu. Liczniki wydajności odczyty \LogicalDisk\Disk/s i \LogicalDisk\Disk/s mogą służyć do zbierania danych dotyczących liczby operacji we/wy odczytu i zapisu, a także do zbierania wymagań dotyczących [przepływności magazynu](../../../virtual-machines/premium-storage-performance.md#disk-caching) dla plików, dzienników i tymczasowych baz danych. Po zdefiniowaniu liczby operacji we/wy i przepływności w szczycie należy oszacować rozmiary maszyn wirtualnych. Jeśli na przykład obciążenie wymaga 20 operacji wejścia/wyjścia odczytu i 10 000 operacji wejścia/wyjścia na sekundę, można wybrać opcję E16s_v3 (z maksymalnie 32 K pamięcią podręczną w pamięci podręcznej i 25600 operacji we/wy na sekundę) lub M16_s (z maksymalnie 20 dyskami buforowanymi w pamięci podręcznej i 10 tys.) przy użyciu dwóch dysków P30 Upewnij się, że rozumiesz wymagania dotyczące przepływności i liczby operacji we/wy obciążenia, ponieważ maszyny wirtualne mają różne limity skalowania dla operacji we/wy na sekundę.<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) i [Seria Es_v3](../../../virtual-machines/ev3-esv3-series.md) są hostowane na sprzęcie ogólnego przeznaczenia z procesorami Intel Haswell lub Broadwell. [Seria M](../../../virtual-machines/m-series.md) oferuje największą liczbę vCPU i pamięć dla największych obciążeń SQL Server i jest obsługiwana na urządzeniach zoptymalizowanych pod kątem pamięci z rodziną procesorów Skylake. Te serie maszyn wirtualnych obsługują usługę Premium Storage, która jest zalecana dla najlepszej wydajności w przypadku pamięci podręcznej odczytu na poziomie hosta. Zarówno Es_v3, jak i serii M są również dostępne w [ograniczonych rozmiarach podstawowych](../../../virtual-machines/constrained-vcpu.md), co pozwala zaoszczędzić pieniądze w przypadku obciążeń o niższych wymaganiach dotyczących pojemności obliczeniowej i dużego magazynu. 

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Aby uzyskać szczegółowe informacje na temat testów wydajności SQL Server na platformie Azure Virtual Machines z użyciem testów TPC-E i TPC_C, zapoznaj się z blogiem [Optymalizacja wydajności OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Pamięć podręczna Azure Blob with Premium dysków SSD jest zalecana dla wszystkich obciążeń produkcyjnych. 

> [!WARNING]
> Standardowe HDD i dysków SSD mają różne opóźnienia i przepustowość i są zalecane tylko w przypadku obciążeń deweloperskich/testowych. Obciążenia produkcyjne powinny używać Premium dysków SSD.

Ponadto zalecamy utworzenie konta usługi Azure Storage w tym samym centrum danych co SQL Server maszyny wirtualne w celu zmniejszenia opóźnień transferu. Podczas tworzenia konta magazynu należy wyłączyć replikację geograficzną zgodnie ze spójną kolejnością zapisu na wielu dyskach. Zamiast tego należy rozważyć skonfigurowanie SQL Server technologii odzyskiwania po awarii między dwoma centrami danych platformy Azure. Więcej informacji znajduje się w temacie [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w usłudze Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy główne typy dysków na maszynach wirtualnych platformy Azure:

* **Dysk systemu operacyjnego**: podczas tworzenia maszyny wirtualnej platformy Azure platforma dołączy co najmniej jeden dysk (oznaczony jako dysk **C** ) do maszyny wirtualnej dla dysku systemu operacyjnego. Ten dysk jest wirtualnym dyskiem twardym przechowywanym jako stronicowy obiekt BLOB w magazynie.
* **Dysk tymczasowy**: maszyny wirtualne platformy Azure zawierają inny dysk o nazwie dysk tymczasowy (oznaczony jako dysk **D**:). Jest to dysk w węźle, który może być używany na potrzeby miejsca na pliki tymczasowe.
* **Dyski danych**: można także dołączyć dodatkowe dyski do maszyny wirtualnej jako dyski danych, które będą przechowywane w magazynie jako stronicowe obiekty blob.

W poniższych sekcjach opisano zalecenia dotyczące korzystania z tych różnych dysków.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego to wirtualny dysk twardy, który można uruchomić i zainstalować jako działającą wersję systemu operacyjnego i jest oznaczony jako dysk **C** .

Domyślne zasady buforowania na dysku systemu operacyjnego są **Odczyt/zapis**. W przypadku aplikacji z uwzględnieniem wydajności zaleca się używanie dysków danych zamiast dysku systemu operacyjnego. Zapoznaj się z sekcją dotyczącej dysków z danymi poniżej.

### <a name="temporary-disk"></a>Dysk tymczasowy

Dysk magazynu tymczasowego oznaczony jako dysk **D** nie jest utrwalany w usłudze Azure Blob Storage. Na dysku **D**: nie należy przechowywać plików bazy danych użytkownika ani plików dziennika transakcji użytkownika.

Umieść bazę danych TempDB na lokalnym `D:\` dysku SSD dla obciążeń o krytycznym znaczeniu SQL Server (po wybraniu prawidłowego rozmiaru maszyny wirtualnej). Jeśli utworzysz maszynę wirtualną na podstawie Azure Portal lub szablonów szybkiego startu platformy Azure i [umieścisz tymczasową bazę danych na dysku lokalnym](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), nie potrzebujesz żadnych dalszych akcji. we wszystkich innych przypadkach postępuj zgodnie z instrukcjami w blogu dotyczącymi  [używania dysków SSD do przechowywania bazy danych tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) , aby zapobiec błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru tymczasowej bazy danych, umieść tymczasową bazę danych [w puli](../../../virtual-machines/premium-storage-performance.md) magazynów na dyskach SSD w warstwie Premium z [buforowaniem tylko do odczytu](../../../virtual-machines/premium-storage-performance.md#disk-caching).

W przypadku maszyn wirtualnych, które obsługują usługę Premium dysków SSD, można również przechowywać bazę danych TempDB na dysku obsługującym usługę Premium dysków SSD z włączonym buforowaniem odczytu.


### <a name="data-disks"></a>Dyski danych

* **Korzystanie z dysków SSD w warstwie Premium dla plików danych i dziennika**: Jeśli nie używasz rozłożenia dysku, użyj dwóch dysków SSD w warstwie Premium, w których jeden dysk zawiera plik dziennika, a drugi zawiera dane. Każdy dysk SSD w warstwie Premium zapewnia wiele operacji we/wy na sekundę (MB/s), w zależności od jego rozmiaru, zgodnie z opisem w artykule [Wybierz typ dysku](../../../virtual-machines/disks-types.md). Jeśli używasz techniki rozkładania dysku, takiej jak miejsca do magazynowania, możesz uzyskać optymalną wydajność, mając dwie pule — jeden dla plików dziennika, a drugi dla plików danych. Jeśli jednak planujesz używać SQL Server wystąpienia klastra trybu failover (FCI), musisz skonfigurować jedną pulę lub użyć [udziałów plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md) .

   > [!TIP]
   > - W przypadku wyników testów na różnych konfiguracjach dysków i obciążeń zobacz następujący wpis w blogu: [wytyczne konfiguracji magazynu dla SQL Server w usłudze Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - W przypadku wydajności o krytycznym znaczeniu dla serwerów SQL, które wymagają ~ 50 000 operacji we/wy na sekundę, rozważ zastępowanie dysków 10-P30 SSD w warstwie Ultra. Aby uzyskać więcej informacji, zobacz następujący wpis w blogu: [wydajność o kluczowym znaczeniu w SSD w warstwie Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Podczas aprowizacji SQL Server maszyny wirtualnej w portalu dostępna jest opcja edytowania konfiguracji magazynu. W zależności od konfiguracji platforma Azure konfiguruje co najmniej jeden dysk. Wiele dysków jest połączonych w jedną pulę magazynów z funkcją Stripe. Oba pliki danych i dziennika znajdują się w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja magazynu dla maszyn wirtualnych SQL Server](storage-configuration.md).

* **Rozkładanie dysku**: Aby uzyskać większą przepływność, można dodać dodatkowe dyski danych i użyć pasków dyskowych. Aby określić liczbę dysków danych, należy analizować liczbę operacji we/wy i przepustowość wymaganą dla plików dziennika oraz plików danych i TempDB. Należy zauważyć, że różne rozmiary maszyn wirtualnych mają różne limity liczby operacji we/wy na sekundę, a które są obsługiwane, Zobacz tabele w bitach na sekundę na [maszynę wirtualną](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Weź pod uwagę następujące wytyczne:

  * W przypadku systemu Windows 8/Windows Server 2012 lub nowszego Użyj funkcji [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wskazówkami:

      1. Ustaw przeplot (rozmiar paska) na 64 KB (65 536 bajtów) dla obciążeń OLTP i 256 KB (262 144 bajty), aby uniknąć wpływu na wydajność z powodu nieodpowiedniego wyrównania partycji. Ta wartość musi być ustawiona przy użyciu programu PowerShell.
      2. Ustaw liczbę kolumn = liczba dysków fizycznych. Użyj programu PowerShell podczas konfigurowania więcej niż 8 dysków (nie Menedżer serwera interfejsie użytkownika). 

    Na przykład poniższy program PowerShell tworzy nową pulę magazynów o rozmiarze przeplotu do 64 KB i liczbę kolumn równą ilości dysku fizycznego w puli magazynów:

    ```powershell
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
    
    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
        -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
        -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
        –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
        -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
        -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * W przypadku systemu Windows 2008 R2 lub starszego można użyć dysków dynamicznych (woluminów rozłożonych systemu operacyjnego), a rozmiar paska jest zawsze 64 KB. Ta opcja jest przestarzała w przypadku systemu Windows 8/Windows Server 2012. Aby uzyskać więcej informacji, zobacz Instrukcja obsługi w [usłudze dysk wirtualny przechodzi do interfejsu API zarządzania magazynem systemu Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Jeśli używasz funkcji [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpieniami klastra trybu failover SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md), musisz skonfigurować jedną pulę. Mimo że w tej pojedynczej puli można utworzyć różne woluminy, będą one miały te same właściwości, takie jak te same zasady buforowania.

  * Określ liczbę dysków skojarzonych z pulą magazynów na podstawie oczekiwań obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych dopuszczają różne liczby dołączonych dysków danych. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie korzystasz z usługi Premium dysków SSD (scenariusze tworzenia i testowania), zaleca się dodanie maksymalnej liczby dysków danych obsługiwanych przez [rozmiar maszyny wirtualnej](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użycie funkcji rozkładania dysków.

* **Zasady buforowania**: zanotuj następujące zalecenia dotyczące buforowania zasad w zależności od konfiguracji magazynu.

  * Jeśli używasz oddzielnych dysków dla plików danych i dziennika, Włącz buforowanie odczytu na dyskach danych, na których znajdują się pliki danych i pliki danych TempDB. Może to skutkować znaczącą korzyścią dla wydajności. Nie należy włączać buforowania na dysku zawierającym plik dziennika, ponieważ powoduje to niewielki spadek wydajności.

  * Jeśli korzystasz z rozkładu dysku w jednej puli magazynów, większość obciążeń będzie korzystać z buforowania odczytu. Jeśli masz oddzielne pule magazynów dla plików dziennika i danych, Włącz buforowanie odczytu tylko w puli magazynów dla plików danych. W niektórych dużych obciążeniach zapisu lepsza wydajność może zostać osiągnięta bez buforowania. Można to określić wyłącznie przez testowanie.

  * Poprzednie zalecenia dotyczą dysków SSD Premium. Jeśli nie korzystasz z dysków SSD Premium, nie włączaj buforowania na żadnym dysku z danymi.

  * Instrukcje dotyczące konfigurowania buforowania dysku znajdują się w poniższych artykułach. W przypadku klasycznego modelu wdrażania (ASM) Zobacz: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) i [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Aby uzyskać Azure Resource Manager model wdrażania, zobacz: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) i [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Zatrzymaj usługę SQL Server w przypadku zmiany ustawienia pamięci podręcznej dysków Azure Virtual Machines, aby uniknąć wystąpienia uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji systemu plików NTFS**: podczas formatowania dysku z danymi zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dziennika, a także tempdb. Jeśli baza danych TempDB jest umieszczona na dysku tymczasowym (D:\ napęd) wydajność uzyskaną przez wykorzystanie tego dysku spowoduje, że nie ma potrzeby rozmiaru jednostki alokacji o rozmiarze 64 KB. 

* **Najlepsze rozwiązania dotyczące zarządzania dyskami**: podczas usuwania dysku z danymi lub zmiany jego typu pamięci podręcznej zatrzymaj usługę SQL Server podczas zmiany. Gdy ustawienia buforowania zostaną zmienione na dysku systemu operacyjnego, platforma Azure zatrzyma maszynę wirtualną, zmieni typ pamięci podręcznej i ponownie uruchomi maszynę wirtualną. Gdy ustawienia pamięci podręcznej dysku danych zostaną zmienione, maszyna wirtualna nie zostanie zatrzymana, ale dysk danych zostanie odłączony od maszyny wirtualnej podczas zmiany, a następnie ponownie dołączony.

  > [!WARNING]
  > Niepowodzenie zatrzymania usługi SQL Server podczas wykonywania tych operacji może spowodować uszkodzenie bazy danych.


## <a name="io-guidance"></a>Wskazówki we/wy

* Najlepsze wyniki w dysków SSD Premium są osiągane w przypadku zrównoleglanie aplikacji i żądań. Dysków SSD Premium są przeznaczone dla scenariuszy, w których głębokość kolejki we/wy jest większa niż 1, więc zobaczysz nieco lub brak zysków dla jednowątkowych żądań szeregowych (nawet jeśli są to duże obciążenie magazynu). Na przykład może to mieć wpływ na wyniki testów pojedynczego wątku narzędzia do analizy wydajności, takie jak SQLIO.

* Należy rozważyć użycie [kompresji strony bazy danych](https://msdn.microsoft.com/library/cc280449.aspx) , ponieważ może to pomóc w zwiększeniu wydajności intensywnych obciążeń we/wy. Jednak kompresja danych może zwiększyć użycie procesora CPU na serwerze bazy danych.

* Rozważ włączenie natychmiastowej inicjalizacji plików, aby skrócić czas wymagany do wstępnego przydzielenia plików. Aby skorzystać z funkcji natychmiastowego inicjowania plików, przyznaj konto usługi SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME i Dodaj je do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Jeśli używasz obrazu platformy SQL Server dla platformy Azure, domyślne konto usługi (NT Service\MSSQLSERVER) nie zostanie dodane do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Innymi słowy, funkcja natychmiastowego inicjowania plików nie jest włączona w obrazie platformy SQL Server platformy Azure. Po dodaniu konta usługi SQL Server do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** Uruchom ponownie usługę SQL Server. W przypadku korzystania z tej funkcji mogą wystąpić zagadnienia dotyczące zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Inicjalizacja plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).

* Należy pamiętać, że **Automatyczne zwiększanie** jest uznawany za tylko sytuacje awaryjne w przypadku nieoczekiwanego wzrostu. Nie Zarządzaj danymi i przyrostami dzienników w codziennym okresie za pomocą automatyczne zwiększanie. Jeśli automatyczne zwiększanie jest używany, należy wstępnie zwiększyć plik przy użyciu przełącznika rozmiaru.

* Upewnij się, że **Autozmniejszanie** jest wyłączone, aby uniknąć niepotrzebnych obciążeń, które mogą negatywnie wpłynąć na wydajność.

* Przenieś wszystkie bazy danych na dyski danych, w tym systemowe bazy danych. Aby uzyskać więcej informacji, zobacz [przenoszenie systemowych baz danych](https://msdn.microsoft.com/library/ms345408.aspx).

* Przenoszenie SQL Server dzienników błędów i plików śledzenia do dysków danych. Można to zrobić w SQL Server Configuration Manager przez kliknięcie prawym przyciskiem myszy wystąpienia SQL Server i wybranie właściwości. Ustawienia dziennika błędów i pliku śledzenia można zmienić na karcie **parametry uruchamiania** . Katalog zrzutu jest określony na karcie **Zaawansowane** . Poniższy zrzut ekranu przedstawia, gdzie szukać parametru uruchamiania dziennika błędów.

    ![Zrzut ekranu dziennika błędów SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych. Użyj zaleceń opisanych w tym artykule i wprowadź zmiany w oknie właściwości serwera. Aby uzyskać instrukcje, zobacz [Wyświetlanie lub Zmienianie domyślnych lokalizacji plików danych i dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie wprowadzić te zmiany.

    ![Dzienniki danych SQL i pliki kopii zapasowej](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Włącz blokowanie stron, aby zmniejszyć liczbę operacji we/wy i wszystkie działania stronicowania. Aby uzyskać więcej informacji, zobacz [Włączanie opcji Zablokuj strony w pamięci (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* W przypadku korzystania z SQL Server 2012 Zainstaluj zbiorczą aktualizację programu Service Pack 1. Ta aktualizacja zawiera poprawkę dla niskiej wydajności we/wy podczas wykonywania instrukcji SELECT INTO tabeli tymczasowej w SQL Server 2012. Aby uzyskać więcej informacji, zobacz ten [artykuł z bazy wiedzy](https://support.microsoft.com/kb/2958012).

* Podczas przenoszenia danych z platformy Azure na sekundę

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące funkcji

Niektóre wdrożenia mogą uzyskać dodatkowe korzyści z wydajności przy użyciu bardziej zaawansowanych technik konfiguracyjnych. Na poniższej liście przedstawiono niektóre funkcje SQL Server, które mogą pomóc w osiągnięciu lepszej wydajności:

### <a name="back-up-to-azure-storage"></a>Tworzenie kopii zapasowej w usłudze Azure Storage
Podczas wykonywania kopii zapasowych SQL Server uruchomionych na platformie Azure Virtual Machines można użyć [SQL Server kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Ta funkcja jest dostępna od SQL Server 2012 SP1 ZASTOSUJESZ pakietu CU2 i jest zalecana do tworzenia kopii zapasowych na podłączonych dyskach danych. W przypadku tworzenia kopii zapasowej/przywracania do/z usługi Azure Storage postępuj zgodnie z zaleceniami podanymi w [temacie SQL Server Tworzenie i rozwiązywanie problemów z kopiami zapasowymi w usłudze Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Możesz również zautomatyzować te kopie zapasowe przy użyciu [zautomatyzowanej kopii zapasowej SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Przed SQL Server 2012 można użyć [SQL Server kopii zapasowej do narzędzia Azure](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc w zwiększeniu przepływności kopii zapasowych przy użyciu wielu celów tworzenia kopii zapasowych.

### <a name="sql-server-data-files-in-azure"></a>SQL Server plików danych na platformie Azure

Ta nowa funkcja, [SQL Server pliki danych na platformie Azure](https://msdn.microsoft.com/library/dn385720.aspx), jest dostępna od SQL Server 2014. Uruchamianie SQL Server za pomocą plików danych na platformie Azure przedstawia porównywalne charakterystyki wydajności w przypadku korzystania z usługi Azure Data Disks.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Wystąpienie klastra trybu failover i miejsca do magazynowania

Jeśli używasz funkcji miejsca do magazynowania, podczas dodawania węzłów do klastra na stronie **potwierdzenie** Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**. 

![Usuń zaznaczenie kwalifikującego się magazynu](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Jeśli używasz funkcji miejsca do magazynowania i nie zaznaczaj **żadnych opcji Dodaj wszystkie odpowiednie magazyny do klastra**, system Windows odłącza dyski wirtualne podczas procesu klastrowania. W związku z tym nie są one wyświetlane w Menedżerze dysków ani w Eksploratorze, dopóki nie zostaną usunięte miejsca do magazynowania z klastra i ponownie dołączone przy użyciu programu PowerShell. Funkcja miejsca do magazynowania grupuje wiele dysków w puli magazynów. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Wiele wystąpień 

Podczas wdrażania wielu wystąpień SQL Server na jednej maszynie wirtualnej należy wziąć pod uwagę następujące najlepsze rozwiązania: 

- Ustaw maksymalną ilość pamięci serwera dla każdego wystąpienia SQL Server, co gwarantuje, że pamięć jest pozostawiona do systemu operacyjnego. Pamiętaj, aby zaktualizować ograniczenia pamięci dla wystąpień SQL Server, jeśli zmienisz ilość pamięci przydzielanej do maszyny wirtualnej. 
- Mają oddzielne jednostki LUN dla danych, dzienników i TempDB, ponieważ wszystkie mają różne wzorce obciążeń i nie mają wpływu na siebie nawzajem. 
- Dokładnie Przetestuj środowisko pod dużym obciążeniem podobnym do produkcji, aby upewnić się, że może obsłużyć szczytową pojemność obciążenia w ramach aplikacji umowy SLA. 

Znaki przeciążonych systemów mogą obejmować, ale nie są ograniczone do, wyczerpania wątków roboczych, czasy wolnej reakcji i/lub pamięć systemowa dyspozytorów. 




## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat magazynu i wydajności, zobacz [wytyczne dotyczące konfiguracji magazynu dla SQL Server na platformie Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
