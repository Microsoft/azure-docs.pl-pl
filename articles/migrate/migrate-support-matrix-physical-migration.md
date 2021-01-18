---
title: Obsługa migracji serwera fizycznego w Azure Migrate
description: Dowiedz się więcej o obsłudze migracji serwera fizycznego w Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/14/2020
ms.openlocfilehash: a3e8b19810a58031b4d11cc6e361215afbc85de2
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562645"
---
# <a name="support-matrix-for-physical-server-migration"></a>Macierz obsługi dla migracji serwera fizycznego

Ten artykuł zawiera podsumowanie ustawień i ograniczeń dotyczących migracji serwerów fizycznych na platformę Azure przy użyciu [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceniania serwerów fizycznych do migracji na platformę Azure, zapoznaj się z [matrycą pomocy technicznej](migrate-support-matrix-physical.md)dotyczącej oceny.

## <a name="migrating-machines-as-physical"></a>Migrowanie maszyn jako fizycznych

Maszyny lokalne można migrować jako serwery fizyczne przy użyciu replikacji opartej na agentach. Za pomocą tego narzędzia można migrować szeroką gamę maszyn na platformę Azure:

- Lokalne serwery fizyczne.
- Maszyny wirtualne zwirtualizowane przez platformy takie jak Xen, KVM.
- Maszyny wirtualne funkcji Hyper-V lub maszyny wirtualne VMware, jeśli z jakiegoś powodu nie chcesz używać standardowych przepływów [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [VMware](server-migrate-overview.md) .
- Maszyny wirtualne działające w chmurach prywatnych.
- Maszyny wirtualne działające w chmurach publicznych, w tym Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Ograniczenia migracji

Można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Jeśli chcesz migrować więcej maszyn, wykonaj replikację w grupach o wartości 10.


## <a name="physical-server-requirements"></a>Wymagania dotyczące serwera fizycznego

W tabeli zestawiono obsługę serwerów fizycznych, które mają być migrowane przy użyciu migracji opartej na agentach.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie maszyny** | Azure Migrate obsługuje migrację dowolnego obciążenia (Powiedz Active Directory, SQL Server itp.) uruchomionego na obsługiwanej maszynie.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu operacyjnego](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Site Recovery. Azure Migrate zapewnia identyczną obsługę systemu operacyjnego.
**System plików Linux/magazyn gościa** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu plików Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) dla Site Recovery. Azure Migrate zapewnia identyczną obsługę systemu plików Linux.
**Sieć/magazyn** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami wstępnymi dotyczącymi [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) Site Recovery. Azure Migrate zapewnia identyczne wymagania dotyczące sieci/magazynu.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami dotyczącymi sieci, [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)i [mocy obliczeniowej](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) [platformy Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)dla Site Recovery. Azure Migrate ma identyczne wymagania dotyczące migracji serwera fizycznego.
**Usługa mobilności** | Agent usługi mobilności musi być zainstalowany na każdej maszynie, która ma zostać poddana migracji.
**Rozruch z interfejsem UEFI** | Obsługiwane. Maszyny z interfejsem UEFI zostaną zmigrowane do maszyn wirtualnych generacji 2 platformy Azure.  <br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane w systemie plików NTFS.
**Interfejs UEFI — bezpieczny rozruch**         | Migracja nie jest obsługiwana.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko do dysków zarządzanych (dysk twardy w warstwie Standardowa, dysk SSD w warstwie Premium) na platformie Azure.
**Rozmiar dysku** | dysk systemu operacyjnego: 2 TB; 32 TB dla dysków z danymi.
**Limity dysku** |  Do 63 dysków na maszynę.
**Zaszyfrowane dyski/woluminy** |  Maszyny z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji.
**Udostępniony klaster dysków** | Nieobsługiwane.
**Dyski niezależne** | Obsługiwane.
**Przekazywanie dysków** | Obsługiwane.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach nie zostaną zreplikowane.
**obiekty docelowe iSCSI** | Maszyny z obiektami docelowymi iSCSI nie są obsługiwane w przypadku migracji bez wykorzystania agentów.
**Wielościeżkowe we/wy** | Nieobsługiwane.
**Zespoły kart sieciowych** | Nieobsługiwane.
**Protokół IPv6** | Nieobsługiwane.



## <a name="replication-appliance-requirements"></a>Wymagania urządzenia replikacji

Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami przedstawionymi w tabeli. Po skonfigurowaniu urządzenia replikacji Azure Migrate jako maszyny wirtualnej VMware przy użyciu szablonu komórki jajowe dostarczonego w centrum Azure Migrate, urządzenie zostanie skonfigurowane z systemem Windows Server 2016 i spełnia wymagania dotyczące pomocy technicznej. 

- Dowiedz się więcej o [wymaganiach dotyczących urządzenia replikacji](migrate-replication-appliance.md#appliance-requirements).
- Na urządzeniu musi być zainstalowany pakiet MySQL. Dowiedz się więcej o [opcjach instalacji](migrate-replication-appliance.md#mysql-installation).
- Informacje o [adresach URL](migrate-replication-appliance.md#url-access) , do których urządzenie replikacji musi uzyskać dostęp.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy Site Recovery uruchamia sprawdzanie wymagań wstępnych dotyczących replikacji, sprawdzenie zakończy się niepowodzeniem, jeśli niektóre wymagania nie zostaną spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Weryfikuje obsługiwane systemy operacyjne.<br/> Możliwe jest Migrowanie dowolnego obciążenia działającego w obsługiwanym systemie operacyjnym. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Architektura systemu operacyjnego gościa | 64-bitowa. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 64 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku danych | Do 4 095 GB | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwane są wiele kart. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
BitLocker | Nieobsługiwane. | Aby włączyć replikację dla maszyny, należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w Site Recovery.
Połącz po migracji — Windows | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> -Przed migracją włącza protokół RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> W celu uzyskania dostępu do sieci VPN typu lokacja-lokacja Włącz protokół RDP i Zezwalaj na używanie protokołu RDP w **zaporze systemu Windows**  ->  **dozwolone aplikacje i funkcje** dla sieci **i** połączeń sieciowych. Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md). |
Połącz po migracji — system Linux | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure po migracji przy użyciu protokołu SSH:<br/> Przed migracją na maszynie lokalnej Sprawdź, czy usługa Secure Shell jest ustawiona do uruchamiania, oraz czy reguły zapory zezwalają na połączenie SSH.<br/> Po przejściu w tryb failover na maszynie wirtualnej platformy Azure Zezwól na połączenia przychodzące do portu SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover oraz dla podsieci platformy Azure, do której jest podłączona. Dodatkowo Dodaj publiczny adres IP dla maszyny wirtualnej. |  


## <a name="next-steps"></a>Następne kroki

[Migrowanie](tutorial-migrate-physical-virtual-machines.md) serwerów fizycznych.
