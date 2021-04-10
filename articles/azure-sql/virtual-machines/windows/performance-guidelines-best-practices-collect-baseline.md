---
title: 'Zbierz podstawy: najlepsze rozwiązania dotyczące wydajności & wytyczne'
description: Zawiera kroki umożliwiające zebranie linii bazowej wydajności jako wskazówek w celu zoptymalizowania wydajności SQL Server na maszynie wirtualnej platformy Azure.
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
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572449"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Zbierz podstawy: najlepsze rozwiązania dotyczące wydajności SQL Server na maszynie wirtualnej platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera informacje dotyczące zbierania linii bazowej wydajności jako szeregu najlepszych rozwiązań i wskazówek w celu zoptymalizowania wydajności SQL Server na platformie Azure Virtual Machines.

Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ta seria najlepszych rozwiązań dotyczących wydajności koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, może nie wymagać każdej zalecanej optymalizacji. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

## <a name="overview"></a>Omówienie

Aby zapoznać się z zaleceniami zalecanymi, należy zebrać liczniki wydajności przy użyciu narzędzia PerfMon/LogMan i przechwycić SQL Server dane statystyczne oczekiwania na lepsze zrozumienie ogólnych ciśnień i potencjalnych wąskich gardeł w środowisku źródłowym. 

Zacznij od zebrania procesora CPU, pamięci, operacji we [/wy na sekundę](../../../virtual-machines/premium-storage-performance.md#iops), [przepływności](../../../virtual-machines/premium-storage-performance.md#throughput)i [opóźnień](../../../virtual-machines/premium-storage-performance.md#latency) obciążenia źródłowego w godzinach szczytu po [liście kontrolnej wydajności aplikacji](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Zbieraj dane w godzinach szczytu, takich jak obciążenia w typowym dniu roboczym, ale również inne procesy wysokiego obciążenia, takie jak przetwarzanie końcowe i obciążenia w weekendy. Należy rozważyć skalowanie zasobów w górę dla nietypowych obciążeń, takich jak przetwarzanie końcowe, a następnie skalowanie wykonywane po zakończeniu obciążenia. 

Użyj analizy wydajności, aby wybrać [rozmiar maszyny wirtualnej](../../../virtual-machines/sizes-memory.md) , który można skalować do wymagań dotyczących wydajności obciążenia.


## <a name="storage"></a>Storage

SQL Server wydajność jest zależna od tego, czy podsystem we/wy i wydajność magazynu są mierzone przez operacje IOPS i przepływności. O ile baza danych nie mieści się w pamięci fizycznej, SQL Server ciągle przywraca strony bazy danych do i z puli buforów. Pliki danych dla SQL Server powinny być traktowane inaczej. Dostęp do plików dziennika jest sekwencyjny, z wyjątkiem sytuacji, gdy transakcja musi zostać wycofana w przypadku, gdy do przechowywania plików danych, w tym tempdb, są dostępne losowo. Jeśli masz wolny podsystem we/wy, mogą wystąpić problemy z wydajnością, takie jak czasy odpowiedzi i zadania, które nie zostały wykonane z powodu przekroczenia limitu czasu. 

Maszyny wirtualne portalu Azure Marketplace mają pliki dziennika na dysku fizycznym, który jest domyślnie oddzielony od plików danych. Liczba plików danych tempdb i rozmiar są zgodne z najlepszymi rozwiązaniami, które są związane z `D:\` dyskiem tymczasowych. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu przepływności we/wy wymaganej przez SQL Server: 
* **\LogicalDisk\Disk odczyty/s** (odczyt operacji we/wy)
* **\LogicalDisk\Disk/s** (Zapisz operacje we/wy) 
* **\LogicalDisk\Disk bajty odczytu/s** (Odczytaj wymagania dotyczące przepływności dla plików danych, dzienników i tempdb)
* **Bajty zapisu \LogicalDisk\Disk/s** (wymagania dotyczące przepływności zapisu dla plików, dzienników i tempdb)

Przy użyciu operacji IOPS i przepływności na poziomach szczytu należy oszacować rozmiary maszyn wirtualnych, które pasują do pojemności z pomiarów. 

Jeśli obciążenie wymaga 20 000 operacji zapisu IOPS i 10 000 operacji we/wy na sekundę, można wybrać opcję E16s_v3 (z maksymalnie 32 KB buforowanymi i 25600mi liczbami IOPS w pamięci podręcznej) lub M16_s (z maksymalnie 20 000 buforowanych i 10 tys. IOPS) z 2 dyskami P30 rozmieszczonymi przy użyciu funkcji miejsca do magazynowania. 

Upewnij się, że rozumiesz wymagania dotyczące przepływności i liczby operacji we/wy obciążenia, ponieważ maszyny wirtualne mają różne limity skalowania dla operacji we/wy na sekundę.

## <a name="memory"></a>Pamięć

Śledź zarówno pamięć zewnętrzną używaną przez system operacyjny, jak i pamięć używaną wewnętrznie przez SQL Server. Zidentyfikowanie nacisku dla każdego składnika ułatwi określenie rozmiaru maszyn wirtualnych i zidentyfikowanie możliwości dostrajania. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu kondycji pamięci SQL Server maszyny wirtualnej: 
* [\Memory\Available (MB)](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: pamięć serwera Manager\Target (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: pamięć serwera Manager\Total (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: bufory Manager\Lazy zapisy/s](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: bufor Menedżer odczytów stron](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Obliczenia na platformie Azure są zarządzane inaczej niż lokalnie. Serwery lokalne są tworzone w ciągu kilku lat bez uaktualnienia ze względu na koszty związane z zarządzaniem i koszt nabycia nowego sprzętu. Wirtualizacja ogranicza niektóre z tych problemów, ale aplikacje są zoptymalizowane pod kątem optymalnego sprzętu, co oznacza, że jakakolwiek znacząca zmiana zużycia zasobów wymaga ponownego zrównoważenia całego środowiska fizycznego. 

Nie jest to wyzwanie na platformie Azure, w którym Nowa maszyna wirtualna jest w innym szeregu sprzętu, a nawet w innym regionie, jest łatwa do osiągnięcia. 

Na platformie Azure chcesz korzystać z możliwie największej ilości zasobów maszyn wirtualnych, dlatego należy skonfigurować maszyny wirtualne platformy Azure tak, aby utrzymywać średnie użycie procesora jako możliwe bez wpływu na obciążenie. 

Następujące liczniki Monitora wydajności mogą pomóc w sprawdzeniu kondycji obliczeniowej maszyny wirtualnej SQL Server:
* **\Processor Information (_Total) — \% czas procesora**
* **\Process (sqlservr) — \% czas procesora**

> [!NOTE] 
> Najlepiej, staraj się, aby użyć 80% obliczeń z szczytem powyżej 90%, ale nie osiągnąć 100% przez dłuższy czas. Na bieżąco należy jedynie zapewnić obsługę obliczeń wymaganych przez aplikację, a następnie zaplanować skalowanie w górę lub w dół zgodnie z wymaganiami biznesowymi. 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz inne artykuły w tej serii:
- [Szybka lista kontrolna](performance-guidelines-best-practices-checklist.md)
- [Rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)


Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
