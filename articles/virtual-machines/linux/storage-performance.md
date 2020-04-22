---
title: Optymalizuj wydajność na maszynach wirtualnych z serii Lsv2 platformy Azure — Magazyn
description: Dowiedz się, jak zoptymalizować wydajność rozwiązania na maszynach wirtualnych serii Lsv2.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 7a0d5e29097bc9a672e142fcffb0ebe879fe2475
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757687"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optymalizacja wydajności na maszynach wirtualnych serii Lsv2

Maszyny wirtualne serii Lsv2 obsługują różne obciążenia, które wymagają wysokiej wydajności we/wy i przepustowości w lokalnej pamięci masowej w wielu różnych aplikacjach i branżach.  Seria Lsv2 jest idealna dla baz danych Big Data, SQL, NoSQL, hurtowni danych i dużych transakcyjnych baz danych, w tym Cassandra, MongoDB, Cloudera i Redis.

Konstrukcja maszyn wirtualnych serii Lsv2 (VMs) maksymalizuje procesor AMD EPYC™ 7551, aby zapewnić najlepszą wydajność między procesorem, pamięcią, urządzeniami NVMe i maszynami wirtualnymi. Współpracując z partnerami w systemie Linux, kilka kompilacji są dostępne w portalu Azure Marketplace, które są zoptymalizowane pod kątem wydajności serii Lsv2 i obecnie obejmują:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

Ten artykuł zawiera wskazówki i sugestie, aby zapewnić, że obciążenia i aplikacje osiągną maksymalną wydajność zaprojektowaną na maszynach wirtualnych. Informacje na tej stronie będą stale aktualizowane, ponieważ więcej obrazów zoptymalizowanych za pomocą Lsv2 zostanie dodanych do portalu Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura chipsetu AMD EYPC™

Maszyny wirtualne z serii Lsv2 wykorzystują procesory AMD EYPC™ oparte na mikroarchitekturze Zen. Firma AMD opracowała infinity fabric (IF) dla EYPC™ jako skalowalne połączenie dla swojego modelu NUMA, który może być używany do komunikacji on-die, on-package i multi-package. W porównaniu z QPI (Quick-Path Interconnect) i UPI (Ultra-Path Interconnect) używanymi w nowoczesnych monolitycznych procesorach firmy Intel, architektura AMD o małej matrycy NUMA może przynieść zarówno korzyści wydajnościowe, jak i wyzwania. Rzeczywisty wpływ przepustowości pamięci i ograniczenia opóźnienia mogą się różnić w zależności od typu uruchomionych obciążeń.

## <a name="tips-to-maximize-performance"></a>Wskazówki dotyczące maksymalizacji wydajności

* Jeśli przesyłasz niestandardowy system Linux GuestOS dla obciążenia, należy pamiętać, że przyspieszona sieć będzie domyślnie **wyłączona.** Jeśli zamierzasz włączyć przyspieszoną sieć, włącz ją w momencie tworzenia maszyny Wirtualnej, aby uzyskać najlepszą wydajność.

* Sprzęt, który zasila maszyny wirtualne z serii Lsv2, wykorzystuje urządzenia NVMe z ośmioma parami kolejek we/wy (QP). Każda kolejka we/wy urządzenia NVMe jest w rzeczywistości parą: kolejką przesyłania i kolejką zakończenia. Sterownik NVMe jest skonfigurowany do optymalizacji wykorzystania tych ośmiu K/WE/Op poprzez dystrybucję we/wy w harmonogramie okrężnym. Aby uzyskać maksymalną wydajność, uruchom osiem zadań na urządzenie, aby dopasować.

* Unikaj mieszania poleceń administratora NVMe (na przykład zapytania informacyjnego NVMe SMART itp.) z poleceniami We/Wy NVMe podczas aktywnych obciążeń. Urządzenia Lsv2 NVMe są wspierane przez technologię Hyper-V NVMe Direct, która przełącza się w tryb "powolny", gdy zajdą w czasie oczekiwania jakiekolwiek polecenia administratora NVMe. Użytkownicy Lsv2 mogą zobaczyć dramatyczny spadek wydajności we/wy NVMe, jeśli tak się stanie.

* Użytkownicy Lsv2 nie powinni polegać na informacjach NUMA urządzenia (wszystkie 0) zgłoszonych z poziomu maszyny Wirtualnej dla dysków danych, aby zdecydować o koligacji NUMA dla swoich aplikacji. Zalecanym sposobem na lepszą wydajność jest rozłożenie obciążeń na procesory, jeśli to możliwe.

* Maksymalna obsługiwana głębokość kolejki na parę kolejek We/Wy dla urządzenia Lsv2 VM NVMe wynosi 1024 (w porównaniu z limitem Amazon i3 QD 32). Użytkownicy Lsv2 powinni ograniczyć swoje (syntetyczne) obciążenia porównawcze do głębokości kolejki 1024 lub niższej, aby uniknąć wyzwalania pełnych warunków kolejki, co może zmniejszyć wydajność.

## <a name="utilizing-local-nvme-storage"></a>Wykorzystanie lokalnej pamięci masowej NVMe

Pamięć lokalna na dysku NVMe o pojemności 1,92 TB na wszystkich maszynach wirtualnych Lsv2 jest ulotna. Podczas pomyślnego standardowego ponownego uruchomienia maszyny Wirtualnej dane na lokalnym dysku NVMe będą się powtarzać. Dane nie będą zachowywane na NVMe, jeśli maszyna wirtualna jest ponownie wcielona, de-przydzielone lub usunięte. Dane nie będą się powtarzać, jeśli inny problem powoduje, że maszyna wirtualna lub sprzęt, na którym jest uruchomiona, stają się w złej kondycji. W takim przypadku wszystkie dane na starym hoście są bezpiecznie usuwane.

Będą również przypadki, gdy maszyna wirtualna musi zostać przeniesiona na inny komputer-host, na przykład podczas planowanej operacji konserwacji. Planowane operacje konserwacji i niektóre awarie sprzętu można przewidzieć za pomocą [zaplanowanych zdarzeń](scheduled-events.md). Zaplanowane zdarzenia powinny być używane do aktualizacji na wszelkie przewidywane operacje konserwacji i odzyskiwania.

W przypadku, gdy zdarzenie planowanej konserwacji wymaga maszyny Wirtualnej do odtworzenia na nowym hoście z pustymi dyskami lokalnymi, dane będą musiały zostać ponownie zsynchronizowane (ponownie, z wszystkich danych na starym hoście są bezpiecznie wymazane). Dzieje się tak, ponieważ maszyny wirtualne z serii Lsv2 nie obsługują obecnie migracji na żywo na lokalnym dysku NVMe.

Istnieją dwa tryby planowanej konserwacji.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardowa konserwacja sterowana przez klienta

- Maszyna wirtualna jest przenoszona do zaktualizowanego hosta podczas okna 30-dniowego.
- Dane magazynu lokalnego Lsv2 mogą zostać utracone, dlatego zaleca się tworzenie kopii zapasowych danych przed zdarzeniem.

### <a name="automatic-maintenance"></a>Automatyczna konserwacja

- Występuje, jeśli klient nie wykonuje konserwacji kontrolowanej przez klienta lub w przypadku procedur awaryjnych, takich jak zdarzenie zero-day zabezpieczeń.
- Przeznaczone do zachowania danych klientów, ale istnieje niewielkie ryzyko zamrożenia lub ponownego uruchomienia maszyny Wirtualnej.
- Dane magazynu lokalnego Lsv2 mogą zostać utracone, dlatego zaleca się tworzenie kopii zapasowych danych przed zdarzeniem.

W przypadku nadchodzących zdarzeń usługi użyj kontrolowanego procesu konserwacji, aby wybrać czas najwygodniejszy dla aktualizacji. Przed zdarzeniem możesz uzyskać zapas zapasów danych w magazynie w wersji premium. Po zakończeniu zdarzenia konserwacji można zwrócić dane do odświeżanej lokalnej pamięci masowej NVMe maszyn wirtualnych Lsv2.

Scenariusze, które przechowują dane na lokalnych dyskach NVMe obejmują:

- Maszyna wirtualna jest uruchomiona i w dobrej kondycji.
- Maszyna wirtualna jest ponownie uruchamiana w miejscu (przez Ciebie lub platformy Azure).
- Maszyna wirtualna jest wstrzymana (zatrzymana bez delokacji).
- Większość planowanych operacji serwisowych.

Scenariusze, które bezpiecznie wymazywać dane w celu ochrony klienta obejmują:

- Maszyna wirtualna jest ponownie wcielona, zatrzymana (de-przydzielona) lub usunięta (przez Ciebie).
- Maszyna wirtualna staje się w złej kondycji i musi obsługiwać leczyć do innego węzła z powodu problemu ze sprzętem.
- Niewielka liczba planowanych operacji obsługi konserwacji, która wymaga ponownego przydzielenia maszyny Wirtualnej do innego hosta w celu obsługi.

Aby dowiedzieć się więcej o opcjach tworzenia kopii zapasowych danych w magazynie lokalnym, zobacz [Tworzenie kopii zapasowych i odzyskiwanie po awarii dysków usługi Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Jak rozpocząć wdrażanie maszyn wirtualnych z serii Lsv2?**  
   Podobnie jak każda inna maszyna wirtualna, użyj [portalu,](quick-create-portal.md) [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub programu [PowerShell,](quick-create-powershell.md) aby utworzyć maszynę wirtualną.

* **Czy pojedynczy błąd dysku NVMe spowoduje, że wszystkie maszyny wirtualne na hoście nie powiodą się?**  
   Jeśli w węźle sprzętowym zostanie wykryta awaria dysku, sprzęt jest w stanie awarii. W takim przypadku wszystkie maszyny wirtualne w węźle są automatycznie de-przydzielone i przeniesione do węzła w dobrej kondycji. W przypadku maszyn wirtualnych z serii Lsv2 oznacza to, że dane klienta w węźle awarii również są bezpiecznie usuwane i muszą zostać odtworzone przez klienta w nowym węźle. Jak wspomniano, zanim migracja na żywo stanie się dostępna na Lsv2, dane w węźle awarii będą aktywnie przenoszone z maszynami wirtualnymi, ponieważ są one przesyłane do innego węzła.

* **Czy muszę wprowadzić jakiekolwiek zmiany w rq_affinity wydajności?**  
   Ustawienie rq_affinity jest niewielką korektą podczas korzystania z bezwzględnej maksymalnej operacji wejścia/wyjścia na sekundę (IOPS). Gdy wszystko inne działa dobrze, a następnie spróbuj ustawić rq_affinity do 0, aby zobaczyć, czy to robi różnicę.

* **Czy muszę zmienić ustawienia blk_mq?**  
   RHEL/CentOS 7.x automatycznie używa blk-mq dla urządzeń NVMe. Nie są konieczne żadne zmiany konfiguracji ani ustawienia. Ustawienie scsi_mod.use_blk_mq jest przeznaczone tylko dla interfejsu SCSI i było używane podczas wersji zapoznawczej Lsv2, ponieważ urządzenia NVMe były widoczne na maszynach wirtualnych gościa jako urządzenia SCSI. Obecnie urządzenia NVMe są widoczne jako urządzenia NVMe, więc ustawienie SCSI blk-mq nie ma znaczenia.

* **Czy muszę zmienić "fio"?**  
   Aby uzyskać maksymalne we/wy dzięki narzędziu do pomiaru wydajności, takiemu jak "fio" w rozmiarach maszyn wirtualnych L64v2 i L80v2, ustaw "rq_affinity" na 0 na każdym urządzeniu NVMe.  Na przykład ten wiersz polecenia ustawi "rq_affinity" na zero dla wszystkich urządzeń 10 NVMe w maszynie wirtualnej L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Należy również pamiętać, że najlepszą wydajność uzyskuje się, gdy we/wy odbywa się bezpośrednio do każdego z surowych urządzeń NVMe bez partycjonowania, bez systemów plików, bez RAID 0 config, itp. Przed rozpoczęciem sesji testowej upewnij się, że konfiguracja `blkdiscard` jest w znanym stanie świeżości/czyszczenia, uruchamiając na każdym z urządzeń NVMe.
   
## <a name="next-steps"></a>Następne kroki

* Zobacz specyfikacje wszystkich [maszyn wirtualnych zoptymalizowanych pod kątem wydajności magazynu](sizes-storage.md) na platformie Azure
