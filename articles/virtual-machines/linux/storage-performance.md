---
title: Optymalizacja wydajności na maszynach wirtualnych z serii Lsv2 systemu Azure — magazyn
description: Dowiedz się, jak zoptymalizować wydajność rozwiązania na maszynach wirtualnych z serii Lsv2 przy użyciu przykładu systemu Linux.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 650164556223a73a722bc91ecb31491ee98cb8a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307105"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>Optymalizacja wydajności na maszynach wirtualnych z serii Lsv2 z systemem Linux

Maszyny wirtualne z serii Lsv2 obsługują różnorodne obciążenia, które wymagają wysokiego wejścia/wyjścia i przepływności w lokalnym magazynie dla szerokiego zakresu aplikacji i branż.  Seria Lsv2 doskonale nadaje się do obsługi danych Big Data, baz danych SQL, NoSQL, magazynów danych i dużych transakcyjnych baz danych, w tym Cassandra, MongoDB, Cloudera i Redis.

Projekt Virtual Machines serii Lsv2 (VM) maksymalizuje procesor AMD EPYC™ 7551, aby zapewnić najlepszą wydajność między procesorem, pamięcią, urządzeniami interfejsu NVMe i maszynami wirtualnymi. Pracując z partnerami w systemie Linux, dostępne są różne kompilacje w witrynie Azure Marketplace, które są zoptymalizowane pod kątem wydajności serii Lsv2 i obecnie obejmują:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Ten artykuł zawiera wskazówki i sugestie dotyczące zapewnienia, że obciążenia i aplikacje osiągają maksymalną wydajność zaprojektowaną na maszynach wirtualnych. Informacje na tej stronie będą stale aktualizowane, gdy więcej Lsv2 zoptymalizowanych obrazów zostanie dodanych do portalu Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura mikroukładu AMD EYPC™

Maszyny wirtualne z serii Lsv2 korzystają z procesorów AMD EYPC™ Server opartych na mikroarchitekturze Zen. Sieć szkieletowa AMD opracowała nieskończoność (jeśli) dla EYPC™ jako skalowalne połączenie z modelem NUMA, która może być używana do komunikacji w ramach platformy, w pakiecie i na wiele pakietów. W porównaniu z QPI (połączenie Quick-Path) i UPI (połączenie z Ultra-ścieżką) używane w przypadku procesorów Intel Modern monolitycznych o architekturze wieloarchitekturowej (AMD), architektura międzyplatformowych technologii NUMA z wieloma procesorami może przynieść korzyści dotyczące wydajności, a także wyzwania. Faktyczny wpływ przepustowości pamięci i ograniczeń opóźnienia może się różnić w zależności od typu wykonywanych obciążeń.

## <a name="tips-to-maximize-performance"></a>Porady dotyczące maksymalizowania wydajności

* Jeśli przekazujesz niestandardowe GuestOS systemu Linux dla obciążenia, pamiętaj, że przyspieszona sieć będzie domyślnie **wyłączona** . Jeśli planujesz włączenie przyspieszonej sieci, włącz ją w momencie tworzenia maszyny wirtualnej w celu uzyskania najlepszej wydajności.

* Sprzęt obsługujący maszyny wirtualne z serii Lsv2 korzysta z urządzeń interfejsu NVMe z ośmiu parami kolejki we/wy (wyciąganie) s. Każda kolejka we/wy urządzenia interfejsu NVMe jest w rzeczywistości parą: kolejką przesłania i kolejką ukończenia. Sterownik interfejsu NVMe został skonfigurowany tak, aby zoptymalizować wykorzystanie tych ośmiu zapytań we/wy przez dystrybuowanie operacji wejścia/wyjścia w harmonogramie okrężnym. Aby uzyskać maksymalną wydajność, należy uruchomić osiem zadań na urządzenie w celu dopasowania.

* Unikaj mieszania poleceń administratora interfejsu NVMe (na przykład zapytania o informacje inteligentne interfejsu NVMe itp.) przy użyciu poleceń we/wy interfejsu NVMe podczas aktywnych obciążeń. Urządzenia Lsv2 NVMe są obsługiwane przez technologię funkcji Hyper-V interfejsu NVMe Direct, która przełącza się w tryb wolny, za każdym razem, gdy wszystkie polecenia administratora interfejsu NVMe są w stanie oczekiwania. W takim przypadku użytkownicy Lsv2 mogą zobaczyć znaczną wydajność w przypadku operacji we/wy interfejsu NVMe.

* Lsv2 użytkownicy nie mogą polegać na informacjach NUMA urządzenia (wszystkie 0) raportowanych z poziomu maszyny wirtualnej dla dysków danych w celu podjęcia decyzji o koligacji NUMA dla swoich aplikacji. Zalecanym sposobem zapewnienia lepszej wydajności jest rozłożenie obciążeń między procesorami, jeśli to możliwe.

* Maksymalna obsługiwana głębokość kolejki dla pary kolejek we/wy dla Lsv2 maszyny wirtualnej interfejsu NVMe to 1024 (a Amazon i3 głębokość kolejki 32). Lsv2 użytkownicy powinni ograniczyć liczbę (syntetyczne) testu porównawczego do głębokości kolejki 1024 lub niższej, aby uniknąć wyzwalania pełnych warunków kolejki, co może zmniejszyć wydajność.

## <a name="utilizing-local-nvme-storage"></a>Korzystanie z lokalnego magazynu interfejsu NVMe

Magazyn lokalny na dysku interfejsu NVMe 1,92 TB na wszystkich maszynach wirtualnych Lsv2 jest nieulotny. Podczas pomyślnego standardowego ponownego uruchomienia maszyny wirtualnej dane na lokalnym dysku interfejsu NVMe będą utrwalane. Dane nie zostaną zachowane w programie NVMe, jeśli maszyna wirtualna zostanie ponownie wdrożona, cofnięta lub usunięta. Dane nie będą utrwalane, jeśli inny problem spowoduje wystąpienie problemu z maszyną wirtualną lub sprzęt, na którym działa, aby stał się w złej kondycji. W takim przypadku wszystkie dane na Starym hoście są bezpiecznie wymazane.

Zdarza się również, że maszyna wirtualna musi zostać przeniesiona na inną maszynę hosta, na przykład podczas zaplanowanej operacji konserwacji. Planowane operacje konserwacji i niektóre błędy sprzętu mogą być przewidywane w [Scheduled Events](scheduled-events.md). Scheduled Events należy używać do aktualizowania wszelkich przewidywanych operacji konserwacji i odzyskiwania.

W przypadku, gdy planowane zdarzenie konserwacji wymaga odtworzenia maszyny wirtualnej na nowym hoście z pustymi dyskami lokalnymi, należy ponownie zsynchronizować dane (z wszelkimi danymi na Starym hoście). Dzieje się tak, ponieważ maszyny wirtualne z serii Lsv2 nie obsługują obecnie migracji na żywo na lokalnym dysku interfejsu NVMe.

Istnieją dwa tryby planowanej konserwacji.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardowa maszyna wirtualna z obsługą kontroli klienta

- Maszyna wirtualna zostanie przeniesiona na zaktualizowany host w oknie 30-dniowym.
- Dane magazynu lokalnego Lsv2 mogły zostać utracone, dlatego zaleca się wykonanie kopii zapasowej danych przed zdarzeniem.

### <a name="automatic-maintenance"></a>Automatyczna konserwacja

- Występuje, gdy klient nie wykonuje konserwacji kontrolowanej przez klienta lub w przypadku procedur awaryjnych, takich jak wydarzenie o wartości zero.
- Przeznaczone do zachowywania danych klienta, ale istnieje niewielkie ryzyko zablokowania lub ponownego uruchomienia maszyny wirtualnej.
- Dane magazynu lokalnego Lsv2 mogły zostać utracone, dlatego zaleca się wykonanie kopii zapasowej danych przed zdarzeniem.

W przypadku dowolnych nadchodzących zdarzeń usługi należy użyć kontrolowanego procesu konserwacji, aby wybrać najbardziej odpowiedni czas dla aktualizacji. Przed wydarzeniem możesz utworzyć kopię zapasową danych w usłudze Premium Storage. Po zakończeniu zdarzenia konserwacji można zwrócić dane do odświeżonego lokalnego magazynu maszyn wirtualnych Lsv2.

Scenariusze, które utrzymują dane na lokalnych dyskach interfejsu NVMe, obejmują:

- Maszyna wirtualna jest uruchomiona i jest w dobrej kondycji.
- Maszyna wirtualna jest uruchamiana ponownie w miejscu (przez użytkownika lub na platformie Azure).
- Maszyna wirtualna jest wstrzymana (zatrzymana bez alokacji).
- Większość planowanych operacji obsługi konserwacji.

Scenariusze, które umożliwiają bezpieczne wymazywanie danych w celu ochrony klienta, obejmują:

- Maszyna wirtualna zostanie ponownie wdrożona, zatrzymana (cofnięta alokacja) lub usunięta (przez użytkownika).
- Maszyna wirtualna przechodzi w stan złej kondycji i musi zaistnieć w innym węźle, z powodu problemu ze sprzętem.
- Niewielka liczba planowanych operacji obsługi konserwacji, które wymagają, aby maszyna wirtualna została ponownie przypisana do innego hosta w celu obsługi.

Aby dowiedzieć się więcej na temat opcji tworzenia kopii zapasowych danych w magazynie lokalnym, zobacz [Tworzenie kopii zapasowych i odzyskiwanie po awarii dla dysków usługi Azure IaaS](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Jak mogę rozpocząć wdrażanie maszyn wirtualnych z serii Lsv2?**  
   Podobnie jak w przypadku każdej innej maszyny wirtualnej, można utworzyć maszynę wirtualną za pomocą [portalu](quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub [programu PowerShell](quick-create-powershell.md) .

* **Czy awaria jednego dysku interfejsu NVMe spowoduje niepowodzenie wszystkich maszyn wirtualnych na hoście?**  
   Jeśli na węźle sprzęt zostanie wykryta awaria dysku, sprzęt jest w stanie awarii. W takim przypadku wszystkie maszyny wirtualne w węźle są automatycznie cofane i przenoszone do węzła w dobrej kondycji. W przypadku maszyn wirtualnych z serii Lsv2 oznacza to, że dane klienta w niepowodzeniem węźle są również bezpiecznie wymazane i konieczne będzie ich odtworzenie przez klienta w nowym węźle. Jak wspomniano, przed rozpoczęciem migracji na żywo w witrynie Lsv2, dane w węźle, który uległ awarii, zostaną aktywnie przeniesione z maszynami wirtualnymi w miarę ich przenoszenia do innego węzła.

* **Czy muszę wprowadzić jakiekolwiek zmiany w rq_affinity na potrzeby wydajności?**  
   Ustawienie rq_affinity to niewielkie dopasowanie w przypadku używania bezwzględnych maksymalnej liczby operacji wejścia/wyjścia na sekundę (IOPS). Gdy wszystko inne działa prawidłowo, spróbuj ustawić rq_affinity na 0, aby sprawdzić, czy ma ona różnicę.

* **Czy muszę zmienić ustawienia blk_mq?**  
   RHEL/CentOS 7. x automatycznie używa BLK-MQ dla urządzeń interfejsu NVMe. Nie są wymagane żadne zmiany ani ustawienia konfiguracji. Ustawienie scsi_mod. use_blk_mq dotyczy tylko interfejsu SCSI i zostało użyte podczas korzystania z wersji zapoznawczej Lsv2, ponieważ urządzenia interfejsu NVMe były widoczne na maszynach wirtualnych gościa jako urządzenia SCSI. Obecnie urządzenia interfejsu NVMe są widoczne jako urządzenia interfejsu NVMe, więc ustawienie SCSI BLK-MQ jest nieistotne.

* **Czy muszę zmienić "Fio"?**  
   Aby uzyskać maksymalną liczbę operacji we/wy z użyciem narzędzia do mierzenia wydajności, takiego jak "Fio" w rozmiarach maszyn wirtualnych L64v2 i L80v2, ustaw wartość "rq_affinity" na 0 na każdym urządzeniu interfejsu NVMe.  Na przykład ten wiersz polecenia ustawi wartość "rq_affinity" na zero dla wszystkich 10 urządzeń interfejsu NVMe na maszynie wirtualnej L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Należy również pamiętać, że najlepszą wydajność uzyskuje się, gdy we/wy jest wykonywane bezpośrednio na wszystkich urządzeniach interfejsu NVMe bez partycjonowania, brak systemów plików, brak konfiguracji RAID 0 itd. Przed rozpoczęciem sesji testowania upewnij się, że konfiguracja jest w znanym stanie świeżym/czystym, uruchamiając `blkdiscard` polecenie na każdym z urządzeń interfejsu NVMe.
   
## <a name="next-steps"></a>Następne kroki

* Zobacz specyfikacje dla wszystkich [maszyn wirtualnych zoptymalizowanych pod kątem wydajności magazynu](../sizes-storage.md) na platformie Azure
