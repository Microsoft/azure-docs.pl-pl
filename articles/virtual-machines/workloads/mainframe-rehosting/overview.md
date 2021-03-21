---
title: Rehostowanie komputera mainframe na maszynach wirtualnych platformy Azure
description: Hostowanie obciążeń mainframe, takich jak systemy oparte na firmie IBM Z, przy użyciu maszyn wirtualnych na Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7ae20b484b4c22c19b856c237d85efe31dc01407
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553548"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Rehostowanie komputera mainframe na maszynach wirtualnych platformy Azure

Migrowanie obciążeń ze środowisk mainframe do chmury umożliwia modernizację infrastruktury i często pozwala zaoszczędzić na kosztach. Wiele obciążeń można przenieść na platformę Azure z uwzględnieniem tylko drobnych zmian kodu, takich jak aktualizacja nazw baz danych.

Ogólnie mówiąc, termin *mainframe* oznacza duży system komputerowy. W odróżnieniu od tego większość większość jest obecnie używana w systemie IBM System Z lub systemami zgodnymi z technologią IBM, które działają w systemu MVS, DOS, VSE, OS/390 lub Z/OS.

Maszyna wirtualna platformy Azure służy do izolowania zasobów dla określonej aplikacji i zarządzania nimi w jednym wystąpieniu. Komputery mainframe, takie jak IBM z/OS, wykorzystują partycje logiczne (LPARS) do tego celu. Komputery mainframe mogą używać jednego LPAR dla regionu CICS ze skojarzonymi programami COBOL oraz osobnym LPAR dla bazy danych programu IBM DB2. Typowa [aplikacja n-warstwowa na platformie Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) wdraża maszyny wirtualne platformy Azure w sieci wirtualnej, które mogą być podzielone na podsieci dla każdej warstwy.

Maszyny wirtualne platformy Azure mogą uruchamiać środowiska emulacji mainframe i kompilatory obsługujące scenariusze podnoszenia i przesunięcia. Projektowanie i testowanie są często między pierwszymi obciążeniami do migracji z komputera mainframe do środowiska deweloperskiego/testowego platformy Azure. Typowe składniki serwera, które można emulować, obejmują systemy przetwarzania transakcji online (OLTP), Batch i pozyskiwania danych, jak pokazano na poniższej ilustracji.

![Środowiska emulacji na platformie Azure umożliwiają uruchamianie systemów z/OS.](media/01-overview.png)

Niektóre obciążenia komputerów mainframe można migrować na platformę Azure z relatywną łatwością, podczas gdy inne mogą być hostowane na platformie Azure przy użyciu rozwiązania partnerskiego. Aby uzyskać szczegółowe wskazówki dotyczące wybierania rozwiązania partnerskiego, [centrum migracji systemu Azure mainframe](https://azure.microsoft.com/migration/mainframe/) może pomóc.

## <a name="mainframe-migration"></a>Migracja komputera mainframe

Ponownie hostować, odbudować, zastąpić lub wycofać? IaaS lub PaaS? Aby określić odpowiednią strategię migracji dla aplikacji typu mainframe, zobacz Przewodnik [migracji komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  w centrum architektury platformy Azure.

## <a name="micro-focus-rehosting-platform"></a>Platforma rehostowania mikrozespołów

Serwer z mikrofokusem jest jednym z największych dostępnych platform do udostępniania komputerów mainframe. Możesz użyć jej do uruchomienia obciążeń z/OS na tańszej platformie x86 na platformie Azure.

Aby rozpocząć:

- [Instalowanie programu Enterprise Server i Enterprise Developer na platformie Azure](./microfocus/set-up-micro-focus-azure.md)
- [Konfigurowanie programu CICS BankDemo for Enterprise Developer na platformie Azure](./microfocus/demo.md)
- [Uruchamianie programu Enterprise Server w kontenerze platformy Docker na platformie Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame na platformie Azure

TmaxSoft OpenFrame to popularne rozwiązanie do ponownego hostowania komputerów mainframe używane w scenariuszach podnoszenia i przesunięcia. Środowisko OpenFrame na platformie Azure jest odpowiednie do tworzenia, demonstracji, testowania lub obciążeń produkcyjnych.

Aby rozpocząć:

- [Wprowadzenie do TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Pobierz książkę elektroniczną](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

Środowisko deweloperskie i testowe firmy IBM Z (IBM zD&T) konfiguruje Środowisko nieprodukcyjne na platformie Azure, za pomocą którego można programistycznie, testować i demonstracje aplikacji opartych na systemie operacyjnym.

Środowisko emulacji na platformie Azure może hostować różne rodzaje wystąpień Z za pomocą dystrybucji kontrolowanych przez deweloperów aplikacji (ADCDs). Możesz uruchomić zD&T Personal Edition, zD&T Parallel Sysplex i zD&T Enterprise Edition na platformie Azure i Azure Stack.

Aby rozpocząć:

- [Konfigurowanie usług IBM zD&T 12,0 na platformie Azure](./ibm/install-ibm-z-environment.md)
- [Konfigurowanie ADCD na zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko IBM DB2 pureScale udostępnia klaster bazy danych dla platformy Azure. Nie jest on identyczny z oryginalnym środowiskiem, ale zapewnia podobną dostępność i skalowanie jako IBM DB2 dla systemu z/OS działającego w ramach równoległej konfiguracji Sysplex.

Aby rozpocząć, zobacz temat [IBM DB2 pureScale na platformie Azure](.//ibm/ibm-db2-purescale-azure.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas migrowania obciążeń programu mainframe do infrastruktury platformy Azure jako usługi (IaaS) można wybrać spośród kilku typów skalowalnych zasobów obliczeniowych na żądanie, w tym maszyn wirtualnych platformy Azure. Platforma Azure oferuje wiele maszyn wirtualnych z [systemami](../../windows/overview.md) [Linux](../../linux/overview.md) i Windows.

### <a name="compute"></a>Compute

Usługa Azure COMPUTE pobór porównuje favorably z pojemnością komputera mainframe. Jeśli zastanawiasz się nad przeniesieniem obciążenia komputera mainframe na platformę Azure, porównaj metrykę komputera mainframe z 1 000 000 instrukcjami na sekundę (MIPS) do wirtualnych procesorów CPU. 

Dowiedz się, jak [przenieść obliczeniowe komputerów mainframe na platformę Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Wysoka dostępność i tryb failover

Platforma Azure oferuje umowy dotyczące poziomu usług oparte na zobowiązaniach (umowy SLA). Wartość domyślna to wiele dziewięciu, a umowy SLA można zoptymalizować przy użyciu lokalnej lub geograficznie replikacji usług. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Dzięki platformie Azure IaaS, takiej jak maszyna wirtualna, konkretne funkcje systemowe zapewniają obsługę trybu failover, na przykład wystąpienia klastra trybu failover i zestawy dostępności. Gdy korzystasz z zasobów platformy Azure jako usługi (PaaS), platforma automatycznie obsługuje tryb failover. Przykłady obejmują [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) i [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Skalowalność

Komputery mainframe zazwyczaj skalują w górę, a środowiska chmury skalują w poziomie. Platforma Azure oferuje wiele rozmiarów systemów [Linux](../../sizes.md) i [Windows](../../sizes.md) spełniających Twoje potrzeby. Chmura również skaluje się w górę lub w dół, aby dopasować dokładne specyfikacje użytkownika. [Skalowanie](/azure/architecture/best-practices/auto-scaling) mocy obliczeniowej, magazynu i usług na żądanie w ramach modelu rozliczania opartego na użyciu.

### <a name="storage"></a>Storage

W chmurze masz szereg elastycznych, skalowalnych opcji magazynu i płacisz tylko za to, czego potrzebujesz. Usługa [Azure Storage](../../../storage/common/storage-introduction.md) oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, niezawodny magazyn komunikatów i Magazyn NoSQL. W przypadku maszyn wirtualnych dyski zarządzane i niezarządzane zapewniają trwały, bezpieczny magazyn dyskowy.

Dowiedz się, jak [przenieść magazyn mainframe na platformę Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

Utrzymywanie własnej witryny odzyskiwania po awarii może być kosztowne. Platforma Azure oferuje łatwe w implementacji i ekonomiczne opcje [tworzenia kopii zapasowych](../../../backup/backup-overview.md), [odzyskiwania](../../../site-recovery/site-recovery-overview.md)i [nadmiarowości](../../../storage/common/storage-redundancy.md) na poziomach lokalnym lub regionalnym lub za pośrednictwem nadmiarowości geograficznej.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government migracji komputera mainframe

Wiele jednostek sektora publicznego przyniesie Cię do przenoszenia aplikacji typu mainframe do bardziej nowoczesnej, elastycznej platformy. Microsoft Azure Government to fizyczne rozdzielone wystąpienie globalnej Microsoft Azure platformy — spakowane dla federalnych, stanowych i lokalnych systemów rządowych. Zapewnia ona światowej klasy usługi zabezpieczeń, ochrony i zgodności przeznaczone dla Stany Zjednoczone instytucji rządowych i ich partnerów.

Azure Government uzyskała tymczasowy organ do obsługi (P-ATO) w celu uzyskania wysokiego wpływu na systemy, które wymagają tego typu środowiska.

Aby rozpocząć, Pobierz [Microsoft Azure Government chmurę dla aplikacji dla komputerów mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Następne kroki

Podawaj naszych [partnerów](partner-workloads.md) , aby ułatwić migrację lub ponowne hostowanie aplikacji typu mainframe. 

Zobacz też:

- [Oficjalne dokumenty dotyczące tematów mainframe](mainframe-white-papers.md)
- [Migracja komputera mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rozwiązywanie problemów](../../troubleshooting/index.yml)
- [Sztuczna komputera mainframe do migracji na platformę Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md