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
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ba30436b363353ad183396e07111b33ca912dbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737442"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Wytyczne dotyczące wydajności programu SQL Server na maszynach wirtualnych Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności SQL Server w programie Microsoft Azure Virtual Machines.

## <a name="overview"></a>Omówienie

W trakcie działania SQL Server na platformie Azure Virtual Machines zalecamy dalsze korzystanie z tych samych opcji dostrajania wydajności bazy danych, które mają zastosowanie do SQL Server w lokalnych środowiskach serwerów. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków z danymi.

[SQL Server obrazy obsługiwane w Azure Portal](sql-vm-create-portal-quickstart.md) postępuj zgodnie z ogólnymi [najlepszymi rozwiązaniami konfiguracji](storage-configuration.md)magazynu. Po zainicjowaniu obsługi należy rozważyć zastosowanie innych optymalizacji omówionych w tym artykule. Podstawowe możliwości wyboru w obciążeniu i sprawdź, czy testowanie zostało przeprowadzone.

> [!TIP]
> Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ten artykuł koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, możesz nie wymagać każdej optymalizacji wymienionej poniżej. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

## <a name="quick-checklist"></a>Szybka lista kontrolna

Poniżej przedstawiono szybką listę kontrolną w celu uzyskania optymalnej wydajności SQL Server na platformie Azure Virtual Machines:

| Warstwowy | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny wirtualnej](#vm-size-guidance) | — Użyj rozmiarów maszyn wirtualnych z 4 lub więcej vCPU, takimi jak [Standard_M8-4 MS](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)lub do [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) lub wyższych. <br/><br/> — Użyj [zoptymalizowanych pod kątem pamięci](../../../virtual-machines/sizes-memory.md) rozmiarów maszyn wirtualnych, aby zapewnić najlepszą wydajność obciążeń SQL Server. <br/><br/> — Seria [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)i [Mv2](../../../virtual-machines/mv2-series.md) Series oferują optymalny stosunek pamięci do rdzeń wirtualny wymagany do obciążeń OLTP. Obie maszyny wirtualne serii M oferują największy współczynnik pamięci do rdzeń wirtualny wymagany dla obciążeń o znaczeniu krytycznym i jest również idealnym rozwiązaniem w przypadku obciążeń magazynu danych. <br/><br/> — Większy współczynnik pamięci do rdzeń wirtualny może być wymagany w przypadku obciążeń o kluczowym znaczeniu i magazynem danych. <br/><br/> — Skorzystaj z obrazów z witryny Marketplace platformy Azure Virtual Machines, ponieważ ustawienia SQL Server i opcje magazynu są skonfigurowane pod kątem optymalnej wydajności SQL Server. <br/><br/> — Zbieraj charakterystyki wydajności docelowej obciążenia i używaj ich w celu określenia odpowiedniego rozmiaru maszyny wirtualnej dla Twojej firmy.|
| [Storage](#storage-guidance) | — Aby uzyskać szczegółowe informacje na temat testowania wydajności SQL Server na platformie Azure Virtual Machines z użyciem testów TPC-E i TPC_C, zapoznaj się z blogiem [Optymalizacja wydajności OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> — Użyj [dysków SSD Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) , aby uzyskać najlepsze korzyści z cen/wydajności. Skonfiguruj [pamięć podręczną tylko do odczytu](../../../virtual-machines/premium-storage-performance.md#disk-caching) dla plików danych i brak pamięci podręcznej dla pliku dziennika. <br/><br/> -Użyj [Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) , jeśli obciążenie wymaga mniej niż 1-ms magazynu. Aby dowiedzieć się więcej, zobacz [Migrowanie do programu Ultra Disk](storage-migrate-to-ultradisk.md) . <br/><br/> — Zbierz wymagania dotyczące opóźnień magazynu dla plików SQL Server, dzienników i tymczasowych baz danych, [monitorując aplikację](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist) przed wybraniem typu dysku. Jeśli wymagane są < 1-ms magazynu, użyj polecenia Ultra disks, w przeciwnym razie użyj dysku SSD Premium. Jeśli małe opóźnienia są wymagane tylko dla pliku dziennika, a nie dla plików danych, [należy zastanowić się, że na dysku jest](../../../virtual-machines/disks-enable-ultra-ssd.md) wymagana liczba IOPS i poziomy przepływności tylko dla pliku dziennika. <br/><br/>  — Magazyn w warstwie Standardowa jest zalecany tylko w celach deweloperskich i testowych lub dla plików kopii zapasowej i nie powinien być używany w przypadku obciążeń produkcyjnych. <br/><br/> — Zachowaj [konto magazynu](../../../storage/common/storage-account-create.md) i SQL Server maszynę wirtualną w tym samym regionie.<br/><br/> -Wyłącz [Magazyn Geograficznie nadmiarowy](../../../storage/common/storage-redundancy.md) platformy Azure (replikacja geograficzna) na koncie magazynu.  |
| [Dyski](#disks-guidance) | — Należy użyć co najmniej dwóch [dysków SSD w warstwie Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 dla plików dziennika i 1 dla plików danych). <br/><br/> -Dla obciążeń wymagających < 1-ms operacji we/wy, Włącz akcelerator zapisu dla serii M i Rozważ użycie dysków SSD w warstwie Ultra dla programu es i serii DS. <br/><br/> -Włącz [buforowanie tylko do odczytu](../../../virtual-machines/premium-storage-performance.md#disk-caching) na dyskach, na których znajdują się pliki danych.<br/><br/> -Dodaj dodatkową wydajność operacji we/wy na poziomie 20% Premium, która jest wymagana [w przypadku konfigurowania magazynu dla SQL Server danych, dzienników i plików tempdb](storage-configuration.md) <br/><br/> — Unikaj używania systemu operacyjnego lub dysków tymczasowych do przechowywania i rejestrowania baz danych.<br/><br/> -Nie włączaj buforowania na dyskach, które obsługują plik dziennika.  **Ważne**: zatrzymaj usługę SQL Server podczas zmieniania ustawień pamięci podręcznej dla dysku Virtual Machines platformy Azure.<br/><br/> -Rozpoznaj wiele dysków danych platformy Azure, aby zwiększyć przepustowość magazynu.<br/><br/> -Format z udokumentowanymi rozmiarami alokacji. <br/><br/> -Należy umieścić bazę danych TempDB na lokalnym `D:\` dysku SSD dla obciążeń o krytycznym znaczeniu SQL Server (po wybraniu prawidłowego rozmiaru maszyny wirtualnej). Jeśli utworzysz maszynę wirtualną na podstawie Azure Portal lub szablonów szybkiego startu platformy Azure i [umieścisz tymczasową bazę danych na dysku lokalnym](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), nie potrzebujesz żadnych dalszych akcji. we wszystkich innych przypadkach postępuj zgodnie z instrukcjami w blogu dotyczącymi  [używania dysków SSD do przechowywania bazy danych tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , aby zapobiec błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru tymczasowej bazy danych, umieść tymczasową bazę danych [w puli](../../../virtual-machines/premium-storage-performance.md) magazynów na dyskach SSD w warstwie Premium z [buforowaniem tylko do odczytu](../../../virtual-machines/premium-storage-performance.md#disk-caching). |
| [WE/WY](#io-guidance) |-Włącz kompresję strony bazy danych.<br/><br/> -Włącz natychmiastowe inicjowanie plików danych.<br/><br/> -Ograniczanie automatycznego zwiększania rozmiaru bazy danych.<br/><br/> — Wyłącz Autozmniejszanie bazy danych.<br/><br/> — Przenoszenie wszystkich baz danych do dysków danych, w tym systemowych baz danych.<br/><br/> — Przechodź SQL Server dzienniku błędów i śledzenia katalogów plików na dyskach danych.<br/><br/> -Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych.<br/><br/> - [Włącz blokowane strony w pamięci](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).<br/><br/> — Oceń i Zastosuj [najnowsze aktualizacje zbiorcze](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) dla zainstalowanej wersji programu SQL Server. |
| [Specyficzne dla funkcji](#feature-specific-guidance) | — Wykonaj kopię zapasową bezpośrednio w usłudze Azure Blob Storage.<br/><br/>— Użyj [kopii zapasowych migawek plików](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) dla baz danych o rozmiarze większym niż 12 TB. <br/><br/>— Użyj wielu plików tymczasowych baz danych, 1 pliku na rdzeń, do 8 plików.<br/><br/>-Ustaw maksymalną ilość pamięci serwera na 90% lub do 50 GB w lewo dla systemu operacyjnego. <br/><br/>-Enable soft NUMA. |


<br/>
Aby uzyskać więcej informacji na temat tego, *jak* i *dlaczego* należy wykonać te optymalizacje, zapoznaj się ze szczegółami i wskazówkami podanymi w poniższych sekcjach.
<br/><br/>

## <a name="getting-started"></a>Wprowadzenie

Jeśli tworzysz nowe SQL Server na maszynie wirtualnej platformy Azure i nie migrujesz bieżącego systemu źródłowego, Utwórz nową SQL Server maszynę wirtualną na podstawie wymagań dotyczących dostawcy.  Wymagania dotyczące dostawcy SQL Server maszyny wirtualnej są takie same, jak w przypadku wdrożenia lokalnego. 

Jeśli tworzysz nową maszynę wirtualną SQL Server przy użyciu nowej aplikacji skompilowanej dla chmury, możesz łatwo zmienić rozmiar maszyny wirtualnej SQL Server w miarę rozwoju wymagań dotyczących danych i użycia.
Uruchamiaj środowiska deweloperskie z seriami serii D, B lub Av2 z niższą warstwą oraz Rozwijaj środowisko w miarę upływu czasu. 

Zalecane minimum dla produkcyjnego środowiska OLTP to 4 rdzeń wirtualny, 32 GB pamięci i stosunek pamięci do rdzeń wirtualny 8. W przypadku nowych środowisk należy zacząć od 4 maszyn rdzeń wirtualny i skalować do 8, 16, 32 rdzeni wirtualnych lub więcej, gdy zmieniają się dane i wymagania dotyczące obliczeń. W przypadku przepływności OLTP docelowa SQL Server maszyny wirtualne, które mają 5000 operacji we/wy dla każdego rdzeń wirtualnyu. 

Użyj obrazów z witryny Marketplace w witrynie SQL Server z konfiguracją magazynu w portalu. Ułatwi to prawidłowe tworzenie pul magazynu niezbędnych do uzyskania rozmiaru, operacji we/wy i przepływności wymaganych dla obciążeń. Ważne jest, aby wybrać SQL Server maszyny wirtualne obsługujące usługę Premium Storage i pamięć podręczną Premium Storage. Aby dowiedzieć się więcej, zobacz sekcję dotyczącą [magazynu](#storage-guidance) . 

SQL Server magazyny danych i krytyczne środowiska produkcyjne będą często wymagały skalowania poza przełożeniem 8 pamięci na rdzeń wirtualny. W przypadku średnich środowisk można wybrać przeznaczenie 16 rdzeni na pamięć i współczynnik 32 rdzeń-pamięć dla większych środowisk magazynu danych. 

SQL Server środowiska magazynu danych często korzystają z przetwarzania równoległego większych maszyn. Z tego powodu Seria M i seria Mv2 są silnymi opcjami dla większych środowisk magazynu danych.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny wirtualnej

Użyj konfiguracji vCPU i pamięci z maszyny źródłowej jako linii bazowej do migrowania bieżącej lokalnej bazy danych SQL Server, aby SQL Server na maszynach wirtualnych platformy Azure. Wprowadź licencję podstawową na platformę Azure, aby skorzystać z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) i zaoszczędzić SQL Server kosztów licencjonowania.

**Firma Microsoft zaleca przełożenie pamięci na rdzeń wirtualny 8 jako punkt wyjścia dla obciążeń produkcyjnych SQL Server.** Mniejsze wskaźniki są akceptowalne dla obciążeń nieprodukcyjnych. 

Wybierz rozmiar maszyny wirtualnej [zoptymalizowanej](../../../virtual-machines/sizes-memory.md)pod kątem pamięci, [ogólnego przeznaczenia](../../../virtual-machines/sizes-general.md), [zoptymalizowanej pod kątem magazynu](../../../virtual-machines/sizes-storage.md)lub [ograniczonego rdzeń wirtualny](../../../virtual-machines/constrained-vcpu.md) , optymalny dla wydajności SQL Server na podstawie obciążenia (OLTP lub magazynu danych). 

### <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

[Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](../../../virtual-machines/sizes-memory.md) są głównym miejscem docelowym dla SQL Server maszyn wirtualnych i zalecanym wyborem przez firmę Microsoft. Maszyny wirtualne zoptymalizowane pod kątem pamięci oferują większe proporcje między PROCESORAmi i średnią pamięcią podręczną. 

#### <a name="m-and-mv2-series"></a>Seria M i Mv2

[Seria M](../../../virtual-machines/m-series.md) oferuje rdzeń wirtualny liczniki i pamięć dla niektórych największych obciążeń SQL Server.  

[Seria Mv2](../../../virtual-machines/mv2-series.md) ma największą liczbę rdzeń wirtualny i ilość pamięci i jest zalecana dla obciążeń magazynu o znaczeniu krytycznym i magazynem danych. Wystąpienia serii Mv2 to rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci, które zapewniają niezrównaną wydajność obliczeniową w celu obsługi dużych baz danych i obciążeń z dużą ilością pamięci, która jest idealnym rozwiązaniem w przypadku serwerów relacyjnych baz danych, dużych pamięci podręcznych i analizy w pamięci.

[Standard_M64ms](../../../virtual-machines/m-series.md) ma na przykład 28% do-rdzeń wirtualny współczynnika pamięci.

Niektóre funkcje z serii M i Mv2 są atrakcyjne dla SQL Server wydajności obejmują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md) i pamięć [podręczną Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) , obsługę [Ultra-Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) i [przyspieszenie pisania](../../../virtual-machines/how-to-enable-write-accelerator.md).

#### <a name="edsv4-series"></a>Seria Edsv4

[Seria Edsv4](../../../virtual-machines/edv4-edsv4-series.md) jest przeznaczona dla aplikacji intensywnie korzystających z pamięci. Te maszyny wirtualne mają duże pojemności SSD magazynu lokalnego, silne liczby operacji we/wy na dysku lokalnym, do 504 GiB pamięci RAM i ulepszone obliczenia w porównaniu z poprzednimi rozmiarami EV3/Esv3 z maszynami wirtualnymi Gen2. Istnieje niemal spójny stosunek pamięci do rdzeń wirtualny w ramach tych maszyn wirtualnych, co jest idealne dla standardowych obciążeń SQL Server. 

Ta seria maszyn wirtualnych jest idealna dla aplikacji i aplikacji dla przedsiębiorstw intensywnie korzystających z pamięci, które korzystają z małych opóźnień i dużych magazynów lokalnych.

Maszyny wirtualne z serii Edsv4 obsługują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md)i pamięć [podręczną Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

#### <a name="dsv2-series-11-15"></a>DSv2 — Seria 11-15

[Seria DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ma takie same konfiguracje pamięci i dysków jak w przypadku poprzedniej serii D. Ta seria ma spójny stosunek pamięci do procesora CPU wynoszący 7 między wszystkimi maszynami wirtualnymi. 

[Seria DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) obsługuje pamięć podręczną [Premium Storage](../../../virtual-machines/premium-storage-performance.md) i [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching), co zdecydowanie zaleca się w celu uzyskania optymalnej wydajności.

### <a name="general-purpose"></a>Ogólnego przeznaczenia

[Rozmiary maszyn wirtualnych ogólnego przeznaczenia](../../../virtual-machines/sizes-general.md) zaprojektowano w celu zapewnienia zrównoważonego współczynnika pamięci do rdzeń wirtualny dla mniejszych obciążeń poziomu wejścia, takich jak programowanie i testowanie, serwery sieci Web i mniejsze serwery baz danych. 

Ze względu na mniejszą liczbę współczynników rdzeń wirtualny z maszynami wirtualnymi ogólnego przeznaczenia ważne jest dokładne monitorowanie liczników wydajności opartych na pamięci, aby upewnić się, że SQL Server jest w stanie uzyskać potrzebną pamięć podręczną buforu. Aby uzyskać więcej informacji, zobacz [punkt odniesienia wydajności pamięci](#memory) . 

Ze względu na to, że początkowe zalecenie dla obciążeń produkcyjnych jest stosunkiem pamięci do rdzeń wirtualny, minimalna zalecana konfiguracja dla maszyny wirtualnej ogólnego przeznaczenia, na której działa SQL Server, to 4 vCPU i 32 GB pamięci. 

#### <a name="ddsv4-series"></a>Seria Ddsv4

[Seria Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferuje rzetelną kombinację vCPU, pamięci i dysku tymczasowego, ale z mniejszym wsparciem pamięci na rdzeń wirtualny. 

Maszyny wirtualne Ddsv4 obejmują małe opóźnienia i magazyn lokalny o większej szybkości.

Te maszyny doskonale nadają się do wdrożeń równoległych SQL i aplikacji, które wymagają szybkiego dostępu do magazynu tymczasowego i relacyjnych baz danych. Istnieje standardowy stosunek pamięci do rdzeń wirtualny dla wszystkich maszyn wirtualnych w tej serii. 

Z tego powodu zaleca się używanie D8ds_v4 jako startera maszyny wirtualnej w tej serii, która ma 8 rdzeni wirtualnych i 32 GB pamięci. Największą maszyną jest D64ds_v4, która ma 64 rdzeni wirtualnych i 256 GB pamięci.

Maszyny wirtualne z [serii Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) obsługują pamięć podręczną [Premium Storage](../../../virtual-machines/premium-storage-performance.md) i [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Seria Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) nie ma współczynnika pamięci do rdzeń wirtualny, która jest zalecana dla obciążeń SQL Server. W związku z tym Rozważ użycie tych maszyn wirtualnych w przypadku mniejszych obciążeń aplikacji i programowania.

#### <a name="b-series"></a>Seria B

Rozmiary maszyn wirtualnych z [serii B](../../../virtual-machines/sizes-b-series-burstable.md) są idealne dla obciążeń, które nie wymagają spójnej wydajności, takiej jak Weryfikacja koncepcji i bardzo małych serwerów aplikacji i programowania. 

Większość maszyn wirtualnych [serii B](../../../virtual-machines/sizes-b-series-burstable.md) z możliwością przełożenia ma współczynnik pamięci do rdzeń wirtualny 4. Największe z tych maszyn jest [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) z 20 rdzeni wirtualnych i 80 GB pamięci.

Ta seria jest unikatowa, ponieważ aplikacje mają możliwość rozbudowania w godzinach pracy, a kredyty do rozliczania są **różne w zależności** od rozmiaru maszyny. 

Gdy środki zostaną wyczerpane, maszyna wirtualna wraca do wydajności maszyny linii bazowej.

Zaletą serii B jest oszczędności wynikające z obliczeń w porównaniu z innymi rozmiarami maszyn wirtualnych w innych seriach, szczególnie w przypadku, gdy zapotrzebowanie na wydajność przetwarzania jest oszczędne przez cały dzień.

Ta seria obsługuje usługę [Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale **nie obsługuje** [buforowania magazynu w warstwie Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> [Seria B](../../../virtual-machines/sizes-b-series-burstable.md) z możliwością przełożenia nie ma współczynnika pamięci do rdzeń wirtualny, która jest zalecana dla obciążeń SQL Server. W związku z tym Rozważ użycie tych maszyn wirtualnych w przypadku mniejszych aplikacji, serwerów sieci Web i obciążeń programistycznych.

#### <a name="av2-series"></a>Seria Av2

Maszyny wirtualne z [serii Av2](../../../virtual-machines/av2-series.md) najlepiej nadają się do obsługi obciążeń na poziomie wejścia, takich jak programowanie i testowanie, serwery sieci Web o małym ruchu, małe i średnie bazy danych aplikacji oraz sprawdzanie koncepcji.

Tylko [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 rdzeni wirtualnych i 16GBs pamięci), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 rdzeni wirtualnych i 32GBs pamięci), a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 rdzeni wirtualnych i 64GBs pamięci) mają prawidłowy stosunek pamięci do rdzeń wirtualny 8 dla tych trzech najlepszych maszyn wirtualnych. 

Te maszyny wirtualne są dobrą opcją dla mniejszych maszyn deweloperskich i testowych SQL Server. 

8 rdzeń wirtualny [Standard_A8m_v2](../../../virtual-machines/av2-series.md) może być również dobrym rozwiązaniem dla małych serwerów aplikacji i sieci Web.

> [!NOTE] 
> Seria Av2 nie obsługuje magazynu w warstwie Premium i w związku z tym nie jest zalecana w przypadku obciążeń produkcyjnych SQL Server nawet z maszynami wirtualnymi, które mają stosunek pamięci do rdzeń wirtualny 8.

### <a name="storage-optimized"></a>Optymalizacja pod kątem magazynu

[Rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../../../virtual-machines/sizes-storage.md) są przeznaczone do określonych przypadków użycia. Te maszyny wirtualne zostały zaprojektowane z myślą o zoptymalizowanej przepływności dysku i operacji we/wy. Ta seria maszyn wirtualnych jest przeznaczona dla scenariuszy danych Big Data, magazynowania danych i dużych transakcyjnych baz danych. 

#### <a name="lsv2-series"></a>Seria Lsv2

[Seria Lsv2](../../../virtual-machines/lsv2-series.md) oferuje wysoką przepływność, małe opóźnienia i lokalny magazyn interfejsu NVMe. Maszyny wirtualne z serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej zamiast korzystania z trwałych dysków danych. 

Te maszyny wirtualne są silnymi opcjami dotyczącymi danych Big Data, magazynu danych, raportowania i obciążeń ETL. Wysoka przepływność i operacje we/wy lokalnego magazynu interfejsu NVMe to dobry przypadek użycia do przetwarzania plików, które zostaną załadowane do bazy danych programu, i innych scenariuszy, w których dane źródłowe mogą być tworzone ponownie z systemu źródłowego lub innych repozytoriów, takich jak Azure Blob Storage czy Azure Data Lake. [Seria Lsv2](../../../virtual-machines/lsv2-series.md) Maszyny wirtualne mogą również naszeregować wydajność dysku przez maksymalnie 30 minut w danym momencie.

Rozmiar tych maszyn wirtualnych wynosił od 8 do 80 vCPU z 8 GiB pamięci na vCPU i dla każdego 8 procesorów wirtualnych vCPU istnieje 1,92 TB dysku SSD interfejsu NVMe. Oznacza to, że dla największych maszyn wirtualnych tej serii [L80s_v2](../../../virtual-machines/lsv2-series.md)istnieje 80 vCPU i 640 BIB pamięci z 10X 1.92 TB magazynu interfejsu NVMe.  Istnieje spójny stosunek pamięci do rdzeń wirtualny na wszystkich tych maszynach wirtualnych.

Magazyn interfejsu NVMe jest nieulotny, co oznacza, że dane zostaną utracone na tych dyskach po ponownym uruchomieniu maszyny wirtualnej.

Serie Lsv2 i LS obsługują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale nie buforowanie magazynu w warstwie Premium. Tworzenie lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy nie jest obsługiwane. 

> [!WARNING]
> Przechowywanie plików danych w magazynie tymczasowych interfejsu NVMe może spowodować utratę danych podczas cofania przydziału maszyny wirtualnej. 

### <a name="constrained-vcores"></a>Ograniczone rdzeni wirtualnych

Duże wydajności obciążeń SQL Server często potrzebują większej ilości pamięci, operacji we/wy i przepływności bez większych rdzeń wirtualny. 

Większość obciążeń OLTP to bazy danych aplikacji, które są oparte na dużej liczbie mniejszych transakcji. W przypadku obciążeń OLTP, tylko niewielka ilość danych jest odczytywana lub modyfikowana, ale woluminy transakcji sterowane przez liczby użytkowników są znacznie wyższe. Ważne jest, aby SQL Server pamięci podręcznej, przechowywać ostatnio używane dane pod kątem wydajności i zapewnić, że odczyty fizyczne mogą być szybko odczytywane w pamięci. 

Te środowiska OLTP potrzebują większej ilości pamięci, szybkiego magazynu i przepustowości we/wy niezbędnej do optymalnego działania. 

W celu utrzymania tego poziomu wydajności bez wyższych SQL Server kosztów licencjonowania platforma Azure oferuje rozmiary maszyn wirtualnych z [ograniczoną liczbą vCPU](../../../virtual-machines/constrained-vcpu.md). 

Pomaga to kontrolować koszty licencjonowania przez zredukowanie dostępnego rdzeni wirtualnych przy zachowaniu tej samej przepustowości pamięci, magazynu i operacji we/wy nadrzędnej maszyny wirtualnej.

Liczba vCPU może być ograniczona do jednego kwartału oryginalnego rozmiaru maszyny wirtualnej. Zmniejszenie rdzeni wirtualnych dostępnego dla maszyny wirtualnej spowoduje osiągnięcie większej liczby przełożeń pamięci do rdzeń wirtualny.

Te nowe rozmiary maszyn wirtualnych mają sufiks, który określa liczbę aktywnych procesorów wirtualnych vCPU, aby ułatwić ich identyfikację. 

Na przykład [M64-32ms](../../../virtual-machines/constrained-vcpu.md) wymaga licencjonowania tylko 32 SQL Server rdzeni wirtualnych z pamięcią, we/wy i przepływności [M64ms](../../../virtual-machines/m-series.md) , a [M64-16Ms](../../../virtual-machines/constrained-vcpu.md) wymaga licencjonowania tylko 16 rdzeni wirtualnych.  Chociaż [M64-16ms](../../../virtual-machines/constrained-vcpu.md) ma SQL Server kwartał kosztu licencjonowania usługi M64ms, koszt obliczeniowy maszyny wirtualnej będzie taki sam.

> [!NOTE] 
> - Średnie i duże obciążenia magazynu danych mogą nadal korzystać z [ograniczonych maszyn wirtualnych rdzeń wirtualny](../../../virtual-machines/constrained-vcpu.md), ale obciążenia magazynu danych często charakteryzują się mniejszą liczbą użytkowników i procesów obejmujących większe ilości danych za pośrednictwem planów zapytań, które działają równolegle. 
> - Koszt obliczeń, który obejmuje Licencjonowanie systemu operacyjnego, pozostaje taki sam jak nadrzędna maszyna wirtualna. 

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Aby uzyskać szczegółowe informacje na temat testowania wydajności SQL Server na platformie Azure Virtual Machines z użyciem testów TPC-E i TPC-C, zapoznaj się z blogiem [Optymalizacja wydajności OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

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

Umieść bazę danych TempDB na lokalnym `D:\` dysku SSD dla obciążeń o krytycznym znaczeniu SQL Server (po wybraniu prawidłowego rozmiaru maszyny wirtualnej). Jeśli utworzysz maszynę wirtualną na podstawie Azure Portal lub szablonów szybkiego startu platformy Azure i [umieścisz tymczasową bazę danych na dysku lokalnym](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), nie potrzebujesz żadnych dalszych akcji. we wszystkich innych przypadkach postępuj zgodnie z instrukcjami w blogu dotyczącymi  [używania dysków SSD do przechowywania bazy danych tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-TempDB-and-buffer-pool-extensions/) , aby zapobiec błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru tymczasowej bazy danych, umieść tymczasową bazę danych [w puli](../../../virtual-machines/premium-storage-performance.md) magazynów na dyskach SSD w warstwie Premium z [buforowaniem tylko do odczytu](../../../virtual-machines/premium-storage-performance.md#disk-caching).

W przypadku maszyn wirtualnych, które obsługują usługę Premium dysków SSD, można również przechowywać bazę danych TempDB na dysku obsługującym usługę Premium dysków SSD z włączonym buforowaniem odczytu.


### <a name="data-disks"></a>Dyski danych

* **Korzystanie z dysków SSD w warstwie Premium dla plików danych i dziennika**: Jeśli nie używasz rozłożenia dysku, użyj dwóch dysków SSD w warstwie Premium, w których jeden dysk zawiera plik dziennika, a drugi zawiera dane. Każdy dysk SSD w warstwie Premium zapewnia wiele operacji we/wy na sekundę (MB/s), w zależności od jego rozmiaru, zgodnie z opisem w artykule [Wybierz typ dysku](../../../virtual-machines/disks-types.md). Jeśli używasz techniki rozkładania dysku, takiej jak miejsca do magazynowania, możesz uzyskać optymalną wydajność, mając dwie pule — jeden dla plików dziennika, a drugi dla plików danych. Jeśli jednak planujesz używać SQL Server wystąpienia klastra trybu failover (FCI), musisz skonfigurować jedną pulę lub użyć [udziałów plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md) .

   > [!TIP]
   > - W przypadku wyników testów na różnych konfiguracjach dysków i obciążeń zobacz następujący wpis w blogu: [wytyczne konfiguracji magazynu dla SQL Server w usłudze Azure Virtual Machines](/archive/blogs/sqlserverstorageengine/storage-configuration-guidelines-for-sql-server-on-azure-vm).
   > - W przypadku wydajności o krytycznym znaczeniu dla serwerów SQL, które wymagają ~ 50 000 operacji we/wy na sekundę, rozważ zastępowanie dysków 10-P30 SSD w warstwie Ultra. Aby uzyskać więcej informacji, zobacz następujący wpis w blogu: [wydajność o kluczowym znaczeniu w SSD w warstwie Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Podczas aprowizacji SQL Server maszyny wirtualnej w portalu dostępna jest opcja edytowania konfiguracji magazynu. W zależności od konfiguracji platforma Azure konfiguruje co najmniej jeden dysk. Wiele dysków jest połączonych w jedną pulę magazynów z funkcją Stripe. Oba pliki danych i dziennika znajdują się w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja magazynu dla maszyn wirtualnych SQL Server](storage-configuration.md).

* **Rozkładanie dysku**: Aby uzyskać większą przepływność, można dodać dodatkowe dyski danych i użyć pasków dyskowych. Aby określić liczbę dysków danych, należy analizować liczbę operacji we/wy i przepustowość wymaganą dla plików dziennika oraz plików danych i TempDB. Należy zauważyć, że różne rozmiary maszyn wirtualnych mają różne limity liczby operacji we/wy na sekundę, a które są obsługiwane, Zobacz tabele w bitach na sekundę na [maszynę wirtualną](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Weź pod uwagę następujące wytyczne:

  * W przypadku systemu Windows 8/Windows Server 2012 lub nowszego Użyj funkcji [miejsca do magazynowania](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) z następującymi wskazówkami:

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

  * W przypadku systemu Windows 2008 R2 lub starszego można użyć dysków dynamicznych (woluminów rozłożonych systemu operacyjnego), a rozmiar paska jest zawsze 64 KB. Ta opcja jest przestarzała w przypadku systemu Windows 8/Windows Server 2012. Aby uzyskać więcej informacji, zobacz Instrukcja obsługi w [usłudze dysk wirtualny przechodzi do interfejsu API zarządzania magazynem systemu Windows](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * Jeśli używasz funkcji [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpieniami klastra trybu failover SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md), musisz skonfigurować jedną pulę. Mimo że w tej pojedynczej puli można utworzyć różne woluminy, będą one miały te same właściwości, takie jak te same zasady buforowania.

  * Określ liczbę dysków skojarzonych z pulą magazynów na podstawie oczekiwań obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych dopuszczają różne liczby dołączonych dysków danych. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie korzystasz z usługi Premium dysków SSD (scenariusze tworzenia i testowania), zaleca się dodanie maksymalnej liczby dysków danych obsługiwanych przez [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użycie funkcji rozkładania dysków.

* **Zasady buforowania**: zanotuj następujące zalecenia dotyczące buforowania zasad w zależności od konfiguracji magazynu.

  * Jeśli używasz oddzielnych dysków dla plików danych i dziennika, Włącz buforowanie odczytu na dyskach danych, na których znajdują się pliki danych i pliki danych TempDB. Może to skutkować znaczącą korzyścią dla wydajności. Nie należy włączać buforowania na dysku zawierającym plik dziennika, ponieważ powoduje to niewielki spadek wydajności.

  * Jeśli korzystasz z rozkładu dysku w jednej puli magazynów, większość obciążeń będzie korzystać z buforowania odczytu. Jeśli masz oddzielne pule magazynów dla plików dziennika i danych, Włącz buforowanie odczytu tylko w puli magazynów dla plików danych. W niektórych dużych obciążeniach zapisu lepsza wydajność może zostać osiągnięta bez buforowania. Można to określić wyłącznie przez testowanie.

  * Poprzednie zalecenia dotyczą dysków SSD Premium. Jeśli nie korzystasz z dysków SSD Premium, nie włączaj buforowania na żadnym dysku z danymi.

  * Instrukcje dotyczące konfigurowania buforowania dysku znajdują się w poniższych artykułach. W przypadku klasycznego modelu wdrażania (ASM) Zobacz: [Set-AzureOSDisk](/previous-versions/azure/jj152847(v=azure.100)) i [Set-AzureDataDisk](/previous-versions/azure/jj152851(v=azure.100)). Aby uzyskać Azure Resource Manager model wdrażania, zobacz: [Set-AzOSDisk](/powershell/module/az.compute/set-azvmosdisk) i [Set-AzVMDataDisk](/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Zatrzymaj usługę SQL Server w przypadku zmiany ustawienia pamięci podręcznej dysków Azure Virtual Machines, aby uniknąć wystąpienia uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji systemu plików NTFS**: podczas formatowania dysku z danymi zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dziennika, a także tempdb. Jeśli baza danych TempDB jest umieszczona na dysku tymczasowym (D:\ napęd) wydajność uzyskaną przez wykorzystanie tego dysku spowoduje, że rozmiar jednostki alokacji 64 KB jest konieczny. 

* **Najlepsze rozwiązania dotyczące zarządzania dyskami**: podczas usuwania dysku z danymi lub zmiany jego typu pamięci podręcznej zatrzymaj usługę SQL Server podczas zmiany. Gdy ustawienia buforowania zostaną zmienione na dysku systemu operacyjnego, platforma Azure zatrzyma maszynę wirtualną, zmieni typ pamięci podręcznej i ponownie uruchomi maszynę wirtualną. Gdy ustawienia pamięci podręcznej dysku danych zostaną zmienione, maszyna wirtualna nie zostanie zatrzymana, ale dysk danych zostanie odłączony od maszyny wirtualnej podczas zmiany, a następnie ponownie dołączony.

  > [!WARNING]
  > Niepowodzenie zatrzymania usługi SQL Server podczas wykonywania tych operacji może spowodować uszkodzenie bazy danych.


## <a name="io-guidance"></a>Wskazówki we/wy

* Najlepsze wyniki w dysków SSD Premium są osiągane w przypadku zrównoleglanie aplikacji i żądań. Dysków SSD Premium są przeznaczone dla scenariuszy, w których głębokość kolejki we/wy jest większa niż 1, więc zobaczysz nieco lub brak zysków dla jednowątkowych żądań szeregowych (nawet jeśli są to duże obciążenie magazynu). Na przykład może to mieć wpływ na wyniki testów pojedynczego wątku narzędzia do analizy wydajności, takie jak SQLIO.

* Należy rozważyć użycie [kompresji strony bazy danych](/sql/relational-databases/data-compression/data-compression) , ponieważ może to pomóc w zwiększeniu wydajności intensywnych obciążeń we/wy. Jednak kompresja danych może zwiększyć użycie procesora CPU na serwerze bazy danych.

* Rozważ włączenie natychmiastowej inicjalizacji plików, aby skrócić czas wymagany do wstępnego przydzielenia plików. Aby skorzystać z funkcji natychmiastowego inicjowania plików, przyznaj konto usługi SQL Server (MSSQLSERVER) SE_MANAGE_VOLUME_NAME i Dodaj je do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Jeśli używasz obrazu platformy SQL Server dla platformy Azure, domyślne konto usługi (NT Service\MSSQLSERVER) nie zostanie dodane do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Innymi słowy, funkcja natychmiastowego inicjowania plików nie jest włączona w obrazie platformy SQL Server platformy Azure. Po dodaniu konta usługi SQL Server do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** Uruchom ponownie usługę SQL Server. W przypadku korzystania z tej funkcji mogą wystąpić zagadnienia dotyczące zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Inicjalizacja plików bazy danych](/sql/relational-databases/databases/database-instant-file-initialization).

* Należy pamiętać, że **Automatyczne zwiększanie** jest uznawany za tylko sytuacje awaryjne w przypadku nieoczekiwanego wzrostu. Nie Zarządzaj danymi i przyrostami dzienników w codziennym okresie za pomocą automatyczne zwiększanie. Jeśli automatyczne zwiększanie jest używany, należy wstępnie zwiększyć plik przy użyciu przełącznika rozmiaru.

* Upewnij się, że **Autozmniejszanie** jest wyłączone, aby uniknąć niepotrzebnych obciążeń, które mogą negatywnie wpłynąć na wydajność.

* Przenieś wszystkie bazy danych na dyski danych, w tym systemowe bazy danych. Aby uzyskać więcej informacji, zobacz [przenoszenie systemowych baz danych](/sql/relational-databases/databases/move-system-databases).

* Przenoszenie SQL Server dzienników błędów i plików śledzenia do dysków danych. Można to zrobić w SQL Server Configuration Manager przez kliknięcie prawym przyciskiem myszy wystąpienia SQL Server i wybranie właściwości. Ustawienia dziennika błędów i pliku śledzenia można zmienić na karcie **parametry uruchamiania** . Katalog zrzutu jest określony na karcie **Zaawansowane** . Poniższy zrzut ekranu przedstawia, gdzie szukać parametru uruchamiania dziennika błędów.

    ![Zrzut ekranu dziennika błędów SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych. Użyj zaleceń opisanych w tym artykule i wprowadź zmiany w oknie właściwości serwera. Aby uzyskać instrukcje, zobacz [Wyświetlanie lub Zmienianie domyślnych lokalizacji plików danych i dziennika (SQL Server Management Studio)](/sql/database-engine/configure-windows/view-or-change-the-default-locations-for-data-and-log-files). Poniższy zrzut ekranu pokazuje, gdzie wprowadzić te zmiany.

    ![Dzienniki danych SQL i pliki kopii zapasowej](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Włącz blokowanie stron, aby zmniejszyć liczbę operacji we/wy i wszystkie działania stronicowania. Aby uzyskać więcej informacji, zobacz [Włączanie opcji Zablokuj strony w pamięci (Windows)](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).

* W przypadku korzystania z SQL Server 2012 Zainstaluj zbiorczą aktualizację programu Service Pack 1. Ta aktualizacja zawiera poprawkę dla niskiej wydajności we/wy podczas wykonywania instrukcji SELECT INTO tabeli tymczasowej w SQL Server 2012. Aby uzyskać więcej informacji, zobacz ten [artykuł z bazy wiedzy](https://support.microsoft.com/kb/2958012).

* Podczas przenoszenia danych z platformy Azure na sekundę

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące funkcji

Niektóre wdrożenia mogą uzyskać dodatkowe korzyści z wydajności przy użyciu bardziej zaawansowanych technik konfiguracyjnych. Na poniższej liście przedstawiono niektóre funkcje SQL Server, które mogą pomóc w osiągnięciu lepszej wydajności:

### <a name="back-up-to-azure-storage"></a>Tworzenie kopii zapasowej w usłudze Azure Storage
Podczas wykonywania kopii zapasowych SQL Server uruchomionych na platformie Azure Virtual Machines można użyć [SQL Server kopii zapasowej do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url). Ta funkcja jest dostępna od SQL Server 2012 SP1 ZASTOSUJESZ pakietu CU2 i jest zalecana do tworzenia kopii zapasowych na podłączonych dyskach danych. W przypadku tworzenia kopii zapasowej/przywracania do/z usługi Azure Storage postępuj zgodnie z zaleceniami podanymi w [temacie SQL Server Tworzenie i rozwiązywanie problemów z kopiami zapasowymi w usłudze Azure Storage](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting). Możesz również zautomatyzować te kopie zapasowe przy użyciu [zautomatyzowanej kopii zapasowej SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Przed SQL Server 2012 można użyć [SQL Server kopii zapasowej do narzędzia Azure](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc w zwiększeniu przepływności kopii zapasowych przy użyciu wielu celów tworzenia kopii zapasowych.

### <a name="sql-server-data-files-in-azure"></a>SQL Server plików danych na platformie Azure

Ta nowa funkcja, [SQL Server pliki danych na platformie Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure), jest dostępna od SQL Server 2014. Uruchamianie SQL Server za pomocą plików danych na platformie Azure przedstawia porównywalne charakterystyki wydajności w przypadku korzystania z usługi Azure Data Disks.

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



## <a name="collect-performance-baseline"></a>Zbierz linię bazową wydajności

Aby uzyskać bardziej szczegółowe podejście, należy zebrać liczniki wydajności przy użyciu narzędzia PerfMon/LogMan i przechwycić SQL Server dane statystyczne oczekiwania w celu lepszego zrozumienia ogólnych ciśnień i potencjalnych wąskich gardeł w środowisku źródłowym. 

Zacznij od zebrania procesora CPU, pamięci, operacji we [/wy na sekundę](../../../virtual-machines/premium-storage-performance.md#iops), [przepływności](../../../virtual-machines/premium-storage-performance.md#throughput)i [opóźnień](../../../virtual-machines/premium-storage-performance.md#latency) obciążenia źródłowego w godzinach szczytu po [liście kontrolnej wydajności aplikacji](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Zbieraj dane w godzinach szczytu, takich jak obciążenia w typowym dniu roboczym, ale również inne procesy wysokiego obciążenia, takie jak przetwarzanie końcowe i obciążenia w weekendy. Należy rozważyć skalowanie zasobów w górę dla nietypowych obciążeń, takich jak przetwarzanie końcowe, a następnie skalowanie wykonywane po zakończeniu obciążenia. 

Użyj analizy wydajności, aby wybrać [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes-memory.md) , który można skalować do wymagań dotyczących wydajności obciążenia.


### <a name="iops-and-throughput"></a>Operacje we/wy i przepływność

SQL Server wydajność zależy w przypadku podsystemu we/wy. O ile baza danych nie mieści się w pamięci fizycznej, SQL Server ciągle przywraca strony bazy danych do i z puli buforów. Pliki danych dla SQL Server powinny być traktowane inaczej. Dostęp do plików dziennika jest sekwencyjny, z wyjątkiem sytuacji, gdy transakcja musi zostać wycofana w przypadku, gdy do przechowywania plików danych, w tym TempDB, są dostępne losowo. Jeśli masz wolny podsystem we/wy, mogą wystąpić problemy z wydajnością, takie jak czasy odpowiedzi i zadania, które nie zostały wykonane z powodu przekroczenia limitu czasu. 

Maszyny wirtualne portalu Azure Marketplace mają pliki dziennika na dysku fizycznym, który jest domyślnie oddzielony od plików danych. Liczba plików danych TempDB i rozmiar są zgodne z najlepszymi rozwiązaniami, które są związane z elementem tymczasowych D:/ dysk.. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu przepływności we/wy wymaganej przez SQL Server: 
* **\LogicalDisk\Disk odczyty/s (operacje we/** wy odczytu i zapisu)
* **\LogicalDisk\Disk/s** (liczba operacji we/wy odczytu i zapisu) 
* **\LogicalDisk\Disk bajtów/s** (wymagania dotyczące przepływności dla plików danych, dzienników i tempdb)

Przy użyciu operacji IOPS i przepływności na poziomach szczytu należy oszacować rozmiary maszyn wirtualnych, które pasują do pojemności z pomiarów. 

Jeśli obciążenie wymaga 20 kilobitów operacji odczytu i 10 000 operacji wejścia/wyjścia na sekundę, można wybrać E16s_v3 (z maksymalnie 32 K pamięcią podręczną w pamięci podręcznej i 25600 operacji we/wy) lub M16_s (z maksymalnie 20 dyskami w pamięci podręcznej i 10 tys.) przy użyciu funkcji miejsca do magazynowania. 

Upewnij się, że rozumiesz wymagania dotyczące przepływności i liczby operacji we/wy obciążenia, ponieważ maszyny wirtualne mają różne limity skalowania dla operacji we/wy na sekundę.

### <a name="memory"></a>Pamięć

Śledź zarówno pamięć zewnętrzną używaną przez system operacyjny, jak i pamięć używaną wewnętrznie przez SQL Server. Zidentyfikowanie nacisku dla każdego składnika ułatwi określenie rozmiaru maszyn wirtualnych i zidentyfikowanie możliwości dostrajania. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu kondycji pamięci SQL Server maszyny wirtualnej: 
* [\Memory\Available (MB)](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: pamięć serwera Manager\Target (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: pamięć serwera Manager\Total (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: bufory Manager\Lazy zapisy/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: bufor Menedżer odczytów stron](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

### <a name="compute--processing"></a>Obliczenia/przetwarzanie

Obliczenia na platformie Azure są zarządzane inaczej niż lokalnie. Serwery lokalne są tworzone w ciągu kilku lat bez uaktualnienia ze względu na koszty związane z zarządzaniem i koszt nabycia nowego sprzętu. Wirtualizacja ogranicza niektóre z tych problemów, ale aplikacje są zoptymalizowane pod kątem optymalnego sprzętu, co oznacza, że jakakolwiek znacząca zmiana zużycia zasobów wymaga ponownego zrównoważenia całego środowiska fizycznego. 

Nie jest to wyzwanie na platformie Azure, w którym Nowa maszyna wirtualna jest w innym szeregu sprzętu, a nawet w innym regionie, jest łatwa do osiągnięcia. 

Na platformie Azure chcesz korzystać z możliwie największej ilości zasobów maszyn wirtualnych, dlatego należy skonfigurować maszyny wirtualne platformy Azure tak, aby utrzymywać średnie użycie procesora jako możliwe bez wpływu na obciążenie. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu kondycji obliczeniowej maszyny wirtualnej SQL Server:
* **\Processor Information (_Total) — \% czas procesora**
* **\Process (sqlservr) — \% czas procesora**

> [!NOTE] 
> Najlepiej, staraj się, aby użyć 80% obliczeń z szczytem powyżej 90%, ale nie osiągnąć 100% przez dłuższy czas. Na bieżąco należy jedynie zapewnić obsługę obliczeń wymaganych przez aplikację, a następnie zaplanować skalowanie w górę lub w dół zgodnie z wymaganiami biznesowymi. 

## <a name="next-steps"></a>Następne kroki

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).