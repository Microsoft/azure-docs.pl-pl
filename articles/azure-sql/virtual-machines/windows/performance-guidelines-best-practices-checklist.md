---
title: 'Lista kontrolna: najlepsze rozwiązania dotyczące wydajności & wytyczne'
description: Zawiera szybką listę kontrolną, aby zapoznać się z najlepszymi rozwiązaniami i wskazówkami dotyczącymi optymalizacji wydajności SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572456"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Lista kontrolna: najlepsze rozwiązania dotyczące wydajności SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ten artykuł zawiera szybką listę kontrolną jako szereg najlepszych rozwiązań i wskazówek w celu zoptymalizowania wydajności SQL Server na platformie Azure Virtual Machines. 

Aby uzyskać szczegółowe informacje, zobacz inne artykuły w tej serii: [rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md), [Magazyn](performance-guidelines-best-practices-storage.md), [zbieranie linii bazowej](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Omówienie

Podczas uruchamiania SQL Server na platformie Azure Virtual Machines Kontynuuj korzystanie z tych samych opcji dostrajania wydajności bazy danych, które mają zastosowanie do SQL Server w lokalnych środowiskach serwerów. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków danych.

Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ta seria najlepszych rozwiązań dotyczących wydajności koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na platformie Azure Virtual Machines. Jeśli obciążenie jest mniej wymagające, może nie wymagać każdej zalecanej optymalizacji. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

## <a name="vm-size"></a>Rozmiar maszyny wirtualnej

Poniżej przedstawiono krótką listę najlepszych rozwiązań dotyczących rozmiaru maszyn wirtualnych służących do uruchamiania SQL Server na maszynie wirtualnej platformy Azure: 

- Używaj rozmiarów maszyn wirtualnych z 4 lub więcej vCPU, takimi jak [Standard_M8-4 MS](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series)lub [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) lub wyższy. 
- Rozmiary maszyn wirtualnych [zoptymalizowane pod kątem pamięci](../../../virtual-machines/sizes-memory.md) umożliwiają najlepszą wydajność obciążeń SQL Server. 
- Seria [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) , [M-](../../../virtual-machines/m-series.md)i [Mv2](../../../virtual-machines/mv2-series.md) Series oferują optymalny stosunek pamięci do rdzeń wirtualny wymagany do obciążeń OLTP. Obie maszyny wirtualne serii M oferują największy współczynnik pamięci do rdzeń wirtualny wymagany dla obciążeń o znaczeniu krytycznym i są również idealnym rozwiązaniem w przypadku obciążeń magazynu danych. 
- Należy wziąć pod uwagę większy stosunek pamięci do rdzeń wirtualny dla obciążeń krytycznych i magazynów danych. 
- Użyj obrazów z witryny Marketplace platformy Azure Virtual Machines, ponieważ ustawienia SQL Server i opcje magazynu są skonfigurowane pod kątem optymalnej wydajności SQL Server. 
- Zbieraj charakterystyki wydajności docelowej obciążenia i używaj ich w celu określenia odpowiedniego rozmiaru maszyny wirtualnej dla Twojej firmy.

Aby dowiedzieć się więcej, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi rozmiaru maszyn wirtualnych](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Storage

Poniżej przedstawiono szybką listę kontrolną najważniejszych wskazówek dotyczących uruchamiania SQL Server na maszynie wirtualnej platformy Azure: 

- Przed wybraniem typu dysku należy monitorować aplikację i [określić wymagania dotyczące przepustowości magazynu i opóźnień](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) dla SQL Server danych, dzienników i tempdb. 
- Aby zoptymalizować wydajność magazynu, należy zaplanować najwyższą liczbę operacji wejścia/wyjścia w pamięci podręcznej i korzystać z buforowania danych jako funkcji wydajności do odczytywania danych przy jednoczesnym uniknięciu [ograniczania przepustowości maszyn wirtualnych i dysków](../../../virtual-machines/premium-storage-performance.md#throttling).
- Umieszczaj pliki danych, dzienników i tempdb na oddzielnych dyskach.
    - W przypadku dysku danych do zapewnienia dostępności obsługi pamięci podręcznej Używaj tylko [dysków Premium P30 i P40](../../../virtual-machines/disks-types.md#premium-ssd) .
    - W przypadku planu dysku dziennika pod kątem pojemności i wydajności testowej w porównaniu z kosztami podczas oceniania [dysków w warstwie Premium P30-P80](../../../virtual-machines/disks-types.md#premium-ssd).
      - Jeśli wymagane jest opóźnienie magazynu, użyj [usługi Azure Ultra disks](../../../virtual-machines/disks-types.md#ultra-disk) dla dziennika transakcji. 
      - W przypadku wdrożeń maszyn wirtualnych z serii M należy wziąć pod uwagę [Akcelerator zapisu](../../../virtual-machines/how-to-enable-write-accelerator.md) przy użyciu usługi Azure Ultra Disks.
    - W przypadku większości obciążeń SQL Server należy umieścić [bazę danych tempdb](/sql/relational-databases/databases/tempdb-database) na lokalnym `D:\` dysku SSD. 
      - Jeśli pojemność dysku lokalnego nie jest wystarczająca dla bazy danych tempdb, należy rozważyć zmianę rozmiarów maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zasady buforowania plików danych](performance-guidelines-best-practices-storage.md#data-file-caching-policies) .
- Rozdziel wiele dysków danych platformy Azure przy użyciu funkcji [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview) , aby zwiększyć przepustowość we/wy do wartości IOPS i limity przepływności docelowej maszyny wirtualnej.
- Ustaw [buforowanie hosta](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) na tylko do odczytu dla dysków plików danych.
- W obszarze [buforowanie hosta](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) ustaw wartość Brak dla dysków w pliku dziennika.
    - Nie włączaj buforowania odczytu/zapisu na dyskach, które zawierają pliki SQL Server. 
    - Przed zmianą ustawień pamięci podręcznej na dysku zawsze Zatrzymaj usługę SQL Server.
- W przypadku obciążeń związanych z programowaniem i testowaniem Rozważ użycie magazynu w warstwie Standardowa. Nie zaleca się używania HDD w warstwie Standardowa/SDD do obciążeń produkcyjnych.
- Zmniejszenie obciążeń [dysków opartych na kredycie](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) powinno być brane pod uwagę tylko w przypadku mniejszego obciążenia i systemów działowych.
- Zainicjuj obsługę konta magazynu w tym samym regionie co maszyna wirtualna SQL Server. 
- Wyłącz magazyn Geograficznie nadmiarowy platformy Azure (replikację geograficzną) i użyj LRS (Magazyn lokalnie nadmiarowy) na koncie magazynu.
- Sformatuj dysk danych, aby używał rozmiaru jednostki alokacji 64 KB dla wszystkich plików danych umieszczonych na dysku innym niż dysk tymczasowy `D:\` (o wartości domyślnej 4 KB). SQL Server maszyny wirtualne wdrożone za pośrednictwem portalu Azure Marketplace są dostarczane z dyskami danych sformatowanymi przy użyciu rozmiaru jednostki alokacji i przeplotu dla puli magazynu ustawionej na 64 KB. 

Aby dowiedzieć się więcej, zobacz kompleksowe [najlepsze rozwiązania dotyczące magazynu](performance-guidelines-best-practices-storage.md). 


## <a name="azure--sql-feature-specific"></a>Specyficzne dla funkcji SQL & platformy Azure

Poniżej przedstawiono szybką listę najlepszych rozwiązań dotyczących SQL Server i konfiguracji specyficznych dla platformy Azure podczas uruchamiania SQL Server na maszynie wirtualnej platformy Azure: 

- Zarejestruj się w [rozszerzeniu SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md) , aby odblokować szereg [korzyści z funkcji](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Włącz kompresję strony bazy danych.
- Włącz natychmiastowe inicjowanie plików dla plików danych.
- Ogranicz autozwiększanie rozmiaru bazy danych.
- Wyłącz Autozmniejszanie bazy danych.
- Przenieś wszystkie bazy danych na dyski danych, w tym systemowe bazy danych.
- Przenoszenie SQL Server dzienników błędów i plików śledzenia do dysków danych.
- Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych.
- [Włącz blokowane strony w pamięci](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Oceń i Zastosuj [najnowsze aktualizacje zbiorcze](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) dla zainstalowanej wersji programu SQL Server.
- Utwórz kopię zapasową bezpośrednio w usłudze Azure Blob Storage.
- Używaj wielu plików [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) , 1 pliku na rdzeń, do 8 plików.



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz inne artykuły w tej serii:
- [Rozmiar maszyny wirtualnej](performance-guidelines-best-practices-vm-size.md)
- [Storage](performance-guidelines-best-practices-storage.md)
- [Zbierz linię bazową](performance-guidelines-best-practices-collect-baseline.md)

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie [zagadnienia dotyczące zabezpieczeń SQL Server w usłudze Azure Virtual Machines](security-considerations-best-practices.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](sql-server-on-azure-vm-iaas-what-is-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](frequently-asked-questions-faq.md).
