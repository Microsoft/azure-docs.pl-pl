---
title: 'Rozmiar maszyny wirtualnej: najlepsze rozwiązania dotyczące wydajności & wytyczne'
description: Zapewnia wskazówki dotyczące rozmiaru maszyny wirtualnej i najlepsze rozwiązania w celu zoptymalizowania wydajności SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572500"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Rozmiar maszyny wirtualnej: najlepsze rozwiązania w zakresie wydajności dla SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera wskazówki dotyczące rozmiaru maszyny wirtualnej, a także szereg najlepszych rozwiązań i wskazówek w celu zoptymalizowania wydajności SQL Server na platformie Azure Virtual Machines.

Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ta seria najlepszych rozwiązań dotyczących wydajności koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, może nie wymagać każdej zalecanej optymalizacji. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.


## <a name="checklist"></a>Lista kontrolna

Zapoznaj się z poniższą listą kontrolną, aby zapoznać się z krótkim omówieniem najlepszego rozwiązania dotyczącego rozmiaru maszyny wirtualnej: 

- Używaj rozmiarów maszyn wirtualnych z 4 lub więcej vCPU, takimi jak [Standard_M8-4 MS](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)lub [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) lub wyższy. 
- Rozmiary maszyn wirtualnych [zoptymalizowane pod kątem pamięci](../../../virtual-machines/sizes-memory.md) umożliwiają najlepszą wydajność obciążeń SQL Server. 
- Seria [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)i [Mv2](../../../virtual-machines/mv2-series.md) Series oferują optymalny stosunek pamięci do rdzeń wirtualny wymagany do obciążeń OLTP. Obie maszyny wirtualne serii M oferują największy współczynnik pamięci do rdzeń wirtualny wymagany dla obciążeń o znaczeniu krytycznym i są również idealnym rozwiązaniem w przypadku obciążeń magazynu danych. 
- Należy wziąć pod uwagę większy stosunek pamięci do rdzeń wirtualny dla obciążeń krytycznych i magazynów danych. 
- Skorzystaj z obrazów z portalu Marketplace maszyny wirtualnej platformy Azure, ponieważ ustawienia SQL Server i opcje magazynu są skonfigurowane pod kątem optymalnej wydajności SQL Server. 
- Zbieraj charakterystyki wydajności docelowej obciążenia i używaj ich w celu określenia odpowiedniego rozmiaru maszyny wirtualnej dla Twojej firmy.

Aby porównać listę kontrolną rozmiaru maszyny wirtualnej z innymi, zapoznaj się z [listą kontrolną](performance-guidelines-best-practices-checklist.md)kompleksowych najlepszych rozwiązań dotyczących wydajności. 

## <a name="overview"></a>Omówienie

Podczas tworzenia SQL Server na maszynie wirtualnej platformy Azure należy uważnie rozważyć typ niezbędnego obciążenia. W przypadku migrowania istniejącego środowiska należy [zebrać linię bazową wydajności](performance-guidelines-best-practices-collect-baseline.md) , aby określić SQL Server w wymaganiach maszyn wirtualnych platformy Azure. Jeśli jest to nowa maszyna wirtualna, Utwórz nową SQL Server maszynę wirtualną na podstawie wymagań dotyczących dostawcy. 

Jeśli tworzysz nową maszynę wirtualną SQL Server przy użyciu nowej aplikacji skompilowanej dla chmury, możesz łatwo zmienić rozmiar maszyny wirtualnej SQL Server w miarę rozwoju wymagań dotyczących danych i użycia.
Uruchamiaj środowiska deweloperskie z seriami serii D, B lub Av2 z niższą warstwą oraz Rozwijaj środowisko w miarę upływu czasu. 

Użyj obrazów z witryny Marketplace w witrynie SQL Server z konfiguracją magazynu w portalu. Ułatwi to prawidłowe tworzenie pul magazynu niezbędnych do uzyskania rozmiaru, operacji we/wy i przepływności wymaganych dla obciążeń. Ważne jest, aby wybrać SQL Server maszyny wirtualne obsługujące usługę Premium Storage i pamięć podręczną Premium Storage. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [magazynu](performance-guidelines-best-practices-storage.md) . 

Zalecane minimum dla produkcyjnego środowiska OLTP to 4 rdzeń wirtualny, 32 GB pamięci i stosunek pamięci do rdzeń wirtualny 8. W przypadku nowych środowisk należy zacząć od 4 maszyn rdzeń wirtualny i skalować do 8, 16, 32 rdzeni wirtualnych lub więcej, gdy zmieniają się dane i wymagania dotyczące obliczeń. W przypadku przepływności OLTP docelowa SQL Server maszyny wirtualne, które mają 5000 operacji we/wy dla każdego rdzeń wirtualnyu. 

SQL Server magazyny danych i krytyczne środowiska produkcyjne będą często wymagały skalowania poza przełożeniem 8 pamięci na rdzeń wirtualny. W przypadku średnich środowisk można wybrać przełożenie 16 pamięci na rdzeń wirtualny oraz współczynnik pamięci 32 do rdzeń wirtualny większych środowisk magazynu danych. 

SQL Server środowiska magazynu danych często korzystają z przetwarzania równoległego większych maszyn. Z tego powodu Seria M i seria Mv2 są silnymi opcjami dla większych środowisk magazynu danych.

Użyj konfiguracji vCPU i pamięci z maszyny źródłowej jako linii bazowej do migrowania bieżącej lokalnej bazy danych SQL Server, aby SQL Server na maszynach wirtualnych platformy Azure. Wprowadź licencję podstawową na platformę Azure, aby skorzystać z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) i zaoszczędzić SQL Server kosztów licencjonowania.

## <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

[Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](../../../virtual-machines/sizes-memory.md) są głównym miejscem docelowym dla SQL Server maszyn wirtualnych i zalecanym wyborem przez firmę Microsoft. Maszyny wirtualne zoptymalizowane pod kątem pamięci oferują większe proporcje między PROCESORAmi i średnią pamięcią podręczną. 

### <a name="m-mv2-and-mdsv2-series"></a>Seria M, Mv2 i Mdsv2

[Seria M](../../../virtual-machines/m-series.md) oferuje rdzeń wirtualny liczniki i pamięć dla niektórych największych obciążeń SQL Server.  

[Seria Mv2](../../../virtual-machines/mv2-series.md) ma największą liczbę rdzeń wirtualny i ilość pamięci i jest zalecana dla obciążeń magazynu o znaczeniu krytycznym i magazynem danych. Wystąpienia serii Mv2 to rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci, które zapewniają niezrównaną wydajność obliczeniową w celu obsługi dużych baz danych i obciążeń z dużą ilością pamięci, która jest idealnym rozwiązaniem w przypadku serwerów relacyjnych baz danych, dużych pamięci podręcznych i analizy w pamięci.

[Standard_M64ms](../../../virtual-machines/m-series.md) ma na przykład 28% do-rdzeń wirtualny współczynnika pamięci.

[Mdsv2 medium](../../..//virtual-machines/msv2-mdsv2-series.md) to nowa seria m, która jest obecnie dostępna w [wersji zapoznawczej](https://aka.ms/Mv2MedMemoryPreview) , która oferuje szereg serii m maszyn wirtualnych platformy Azure z ofertą pamięci MidTier. Te maszyny są odpowiednie dla SQL Server obciążeń z co najmniej 10 rdzeń wirtualny obsługi do 30.

Niektóre funkcje z serii M i Mv2 są atrakcyjne dla SQL Server wydajności obejmują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md) i pamięć [podręczną Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching) , obsługę [Ultra-Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) i [przyspieszenie pisania](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Seria Edsv4

[Seria Edsv4](../../../virtual-machines/edv4-edsv4-series.md) jest przeznaczona dla aplikacji intensywnie korzystających z pamięci. Te maszyny wirtualne mają duże pojemności dysków SSD magazynu lokalnego, silne operacje we/wy na dysku lokalnym, do 504 GiB pamięci RAM. W przypadku większości tych maszyn wirtualnych istnieje niemal spójna 8 GiB pamięci na rdzeń wirtualny, co jest idealne dla standardowych obciążeń SQL Server. 

W tej grupie znajduje się nowa maszyna wirtualna z [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) , która oferuje 80 rdzeni wirtualnych, 504 GB pamięci, z przełożeniem pamięci na rdzeń wirtualny 6. Ta maszyna wirtualna jest istotna, ponieważ jest [odizolowana](../../../virtual-machines/isolation.md) , co oznacza, że jest ona jedyną maszyną wirtualną uruchomioną na hoście i dlatego jest odizolowana od innych obciążeń klientów. Jest to współczynnik pamięci do rdzeń wirtualny, który jest mniejszy niż zalecany dla SQL Server, dlatego powinien być używany tylko w przypadku, gdy wymagana jest izolacja.

Maszyny wirtualne z serii Edsv4 obsługują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md)i pamięć [podręczną Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>DSv2 — Seria 11-15

[Seria DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) ma takie same konfiguracje pamięci i dysków jak w przypadku poprzedniej serii D. Ta seria ma spójny stosunek pamięci do procesora CPU wynoszący 7 między wszystkimi maszynami wirtualnymi. Jest to najmniejszy z serii zoptymalizowanych pod kątem pamięci i jest to dobra opcja niskiego kosztu dla obciążeń SQL Server na poziomie pozycji.

[Seria DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) obsługuje pamięć podręczną [Premium Storage](../../../virtual-machines/premium-storage-performance.md) i [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching), co zdecydowanie zaleca się w celu uzyskania optymalnej wydajności.

## <a name="general-purpose"></a>Ogólnego przeznaczenia

[Rozmiary maszyn wirtualnych ogólnego przeznaczenia](../../../virtual-machines/sizes-general.md) zaprojektowano w celu zapewnienia zrównoważonego współczynnika pamięci do rdzeń wirtualny dla mniejszych obciążeń poziomu wejścia, takich jak programowanie i testowanie, serwery sieci Web i mniejsze serwery baz danych. 

Ze względu na mniejszą liczbę współczynników rdzeń wirtualny z maszynami wirtualnymi ogólnego przeznaczenia ważne jest dokładne monitorowanie liczników wydajności opartych na pamięci, aby upewnić się, że SQL Server jest w stanie uzyskać potrzebną pamięć podręczną buforu. Aby uzyskać więcej informacji, zobacz [punkt odniesienia wydajności pamięci](performance-guidelines-best-practices-collect-baseline.md#memory) . 

Ze względu na to, że początkowe zalecenie dla obciążeń produkcyjnych jest stosunkiem pamięci do rdzeń wirtualny, minimalna zalecana konfiguracja dla maszyny wirtualnej ogólnego przeznaczenia, na której działa SQL Server, to 4 vCPU i 32 GB pamięci. 

### <a name="ddsv4-series"></a>Seria Ddsv4

[Seria Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) oferuje rzetelną kombinację vCPU, pamięci i dysku tymczasowego, ale z mniejszym wsparciem pamięci na rdzeń wirtualny. 

Maszyny wirtualne Ddsv4 obejmują małe opóźnienia i magazyn lokalny o większej szybkości.

Te maszyny doskonale nadają się do wdrożeń równoległych SQL i aplikacji, które wymagają szybkiego dostępu do magazynu tymczasowego i relacyjnych baz danych. Istnieje standardowy stosunek pamięci do rdzeń wirtualny dla wszystkich maszyn wirtualnych w tej serii. 

Z tego powodu zaleca się używanie D8ds_v4 jako startera maszyny wirtualnej w tej serii, która ma 8 rdzeni wirtualnych i 32 GB pamięci. Największą maszyną jest D64ds_v4, która ma 64 rdzeni wirtualnych i 256 GB pamięci.

Maszyny wirtualne z [serii Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) obsługują pamięć podręczną [Premium Storage](../../../virtual-machines/premium-storage-performance.md) i [Premium Storage](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> [Seria Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) nie ma współczynnika pamięci do rdzeń wirtualny, która jest zalecana dla obciążeń SQL Server. W związku z tym Rozważ użycie tych maszyn wirtualnych w przypadku mniejszych obciążeń aplikacji i programowania.

### <a name="b-series"></a>Seria B

Rozmiary maszyn wirtualnych z [serii B](../../../virtual-machines/sizes-b-series-burstable.md) są idealne dla obciążeń, które nie wymagają spójnej wydajności, takiej jak Weryfikacja koncepcji i bardzo małych serwerów aplikacji i programowania. 

Większość maszyn wirtualnych [serii B](../../../virtual-machines/sizes-b-series-burstable.md) z możliwością przełożenia ma współczynnik pamięci do rdzeń wirtualny 4. Największe z tych maszyn jest [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) z 20 rdzeni wirtualnych i 80 GB pamięci.

Ta seria jest unikatowa, ponieważ aplikacje mają możliwość rozbudowania w godzinach pracy, a kredyty do rozliczania są **różne w zależności** od rozmiaru maszyny. 

Gdy środki zostaną wyczerpane, maszyna wirtualna wraca do wydajności maszyny linii bazowej.

Zaletą serii B jest oszczędności wynikające z obliczeń w porównaniu z innymi rozmiarami maszyn wirtualnych w innych seriach, szczególnie w przypadku, gdy zapotrzebowanie na wydajność przetwarzania jest oszczędne przez cały dzień.

Ta seria obsługuje usługę [Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale **nie obsługuje** [buforowania magazynu w warstwie Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> [Seria B](../../../virtual-machines/sizes-b-series-burstable.md) z możliwością przełożenia nie ma współczynnika pamięci do rdzeń wirtualny, która jest zalecana dla obciążeń SQL Server. W związku z tym Rozważ użycie tych maszyn wirtualnych w przypadku mniejszych aplikacji, serwerów sieci Web i obciążeń programistycznych.

### <a name="av2-series"></a>Seria Av2

Maszyny wirtualne z [serii Av2](../../../virtual-machines/av2-series.md) najlepiej nadają się do obsługi obciążeń na poziomie wejścia, takich jak programowanie i testowanie, serwery sieci Web o małym ruchu, małe i średnie bazy danych aplikacji oraz sprawdzanie koncepcji.

Tylko [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 rdzeni wirtualnych i 16GBs pamięci), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 rdzeni wirtualnych i 32GBs pamięci), a [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 rdzeni wirtualnych i 64GBs pamięci) mają prawidłowy stosunek pamięci do rdzeń wirtualny 8 dla tych trzech najlepszych maszyn wirtualnych. 

Te maszyny wirtualne są dobrą opcją dla mniejszych maszyn deweloperskich i testowych SQL Server. 

8 rdzeń wirtualny [Standard_A8m_v2](../../../virtual-machines/av2-series.md) może być również dobrym rozwiązaniem dla małych serwerów aplikacji i sieci Web.

> [!NOTE] 
> Seria Av2 nie obsługuje magazynu w warstwie Premium i w związku z tym nie jest zalecana w przypadku obciążeń produkcyjnych SQL Server nawet z maszynami wirtualnymi, które mają stosunek pamięci do rdzeń wirtualny 8.

## <a name="storage-optimized"></a>Optymalizacja pod kątem magazynu

[Rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu](../../../virtual-machines/sizes-storage.md) są przeznaczone do określonych przypadków użycia. Te maszyny wirtualne zostały zaprojektowane z myślą o zoptymalizowanej przepływności dysku i operacji we/wy. 

### <a name="lsv2-series"></a>Seria Lsv2

[Seria Lsv2](../../../virtual-machines/lsv2-series.md) oferuje wysoką przepływność, małe opóźnienia i lokalny magazyn interfejsu NVMe. Maszyny wirtualne z serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej zamiast korzystania z trwałych dysków danych. 

Te maszyny wirtualne są silnymi opcjami dotyczącymi danych Big Data, magazynu danych, raportowania i obciążeń ETL. Wysoka przepływność i operacje we/wy lokalnego magazynu interfejsu NVMe to dobry przypadek użycia do przetwarzania plików, które zostaną załadowane do bazy danych programu, i innych scenariuszy, w których dane mogą być tworzone ponownie z systemu źródłowego lub innych repozytoriów, takich jak Azure Blob Storage czy Azure Data Lake. [Seria Lsv2](../../../virtual-machines/lsv2-series.md) Maszyny wirtualne mogą również naszeregować wydajność dysku przez maksymalnie 30 minut w danym momencie.

Rozmiar tych maszyn wirtualnych wynosił od 8 do 80 vCPU z 8 GiB pamięci na vCPU i dla każdego 8 procesorów wirtualnych vCPU istnieje 1,92 TB dysku SSD interfejsu NVMe. Oznacza to, że dla największych maszyn wirtualnych tej serii [L80s_v2](../../../virtual-machines/lsv2-series.md)istnieje 80 vCPU i 640 BIB pamięci z 10X 1.92 TB magazynu interfejsu NVMe.  Istnieje spójny stosunek pamięci do rdzeń wirtualny na wszystkich tych maszynach wirtualnych.

Magazyn interfejsu NVMe jest ulotny, co oznacza, że dane zostaną utracone na tych dyskach w przypadku cofnięcia alokacji maszyny wirtualnej lub przeniesieniu do innego hosta w celu naprawy usługi.

Serie Lsv2 i LS obsługują [Magazyn Premium Storage](../../../virtual-machines/premium-storage-performance.md), ale nie buforowanie magazynu w warstwie Premium. Tworzenie lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy nie jest obsługiwane. 

> [!WARNING]
> Przechowywanie plików danych w magazynie tymczasowych interfejsu NVMe może spowodować utratę danych podczas cofania przydziału maszyny wirtualnej. 

## <a name="constrained-vcores"></a>Ograniczone rdzeni wirtualnych

Wysokie obciążenia SQL Server często potrzebują większej ilości pamięci, operacji we/wy i przepływności bez większych rdzeń wirtualny. 

Większość obciążeń OLTP to bazy danych aplikacji, które są oparte na dużej liczbie mniejszych transakcji. W przypadku obciążeń OLTP, tylko niewielka ilość danych jest odczytywana lub modyfikowana, ale woluminy transakcji sterowane przez liczby użytkowników są znacznie wyższe. Ważne jest, aby SQL Server pamięci podręcznej, przechowywać ostatnio używane dane pod kątem wydajności i zapewnić, że odczyty fizyczne mogą być szybko odczytywane w pamięci. 

Te środowiska OLTP potrzebują większej ilości pamięci, szybkiego magazynu i przepustowości we/wy niezbędnej do optymalnego działania. 

W celu utrzymania tego poziomu wydajności bez wyższych SQL Server kosztów licencjonowania platforma Azure oferuje rozmiary maszyn wirtualnych z [ograniczoną liczbą vCPU](../../../virtual-machines/constrained-vcpu.md). 

Pomaga to kontrolować koszty licencjonowania przez zredukowanie dostępnego rdzeni wirtualnych przy zachowaniu tej samej przepustowości pamięci, magazynu i operacji we/wy nadrzędnej maszyny wirtualnej.

Liczba vCPU może być ograniczona do jednego kwartału oryginalnego rozmiaru maszyny wirtualnej. Zmniejszenie rdzeni wirtualnych dostępnego dla maszyny wirtualnej osiągnie przełożenie wyższego poziomu pamięci na rdzeń wirtualny, ale koszt obliczeń pozostanie bez zmian.

Te nowe rozmiary maszyn wirtualnych mają sufiks, który określa liczbę aktywnych procesorów wirtualnych vCPU, aby ułatwić ich identyfikację. 

Na przykład [M64-32ms](../../../virtual-machines/constrained-vcpu.md) wymaga licencjonowania tylko 32 SQL Server rdzeni wirtualnych z pamięcią, we/wy i przepływności [M64ms](../../../virtual-machines/m-series.md) , a [M64-16Ms](../../../virtual-machines/constrained-vcpu.md) wymaga licencjonowania tylko 16 rdzeni wirtualnych.  Chociaż [M64-16ms](../../../virtual-machines/constrained-vcpu.md) ma SQL Server kwartał kosztu licencjonowania usługi M64ms, koszt obliczeniowy maszyny wirtualnej będzie taki sam.

> [!NOTE] 
> - Średnie i duże obciążenia magazynu danych mogą nadal korzystać z [ograniczonych maszyn wirtualnych rdzeń wirtualny](../../../virtual-machines/constrained-vcpu.md), ale obciążenia magazynu danych często charakteryzują się mniejszą liczbą użytkowników i procesów obejmujących większe ilości danych za pośrednictwem planów zapytań, które działają równolegle. 
> - Koszt obliczeń, który obejmuje Licencjonowanie systemu operacyjnego, pozostaje taki sam jak nadrzędna maszyna wirtualna. 



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz inne artykuły w tej serii:
- [Szybka lista kontrolna](performance-guidelines-best-practices-checklist.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Zbierz linię bazową](performance-guidelines-best-practices-collect-baseline.md)

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
