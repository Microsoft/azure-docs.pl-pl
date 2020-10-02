---
title: Obsługa migracji oprogramowania VMware w Azure Migrate
description: Dowiedz się więcej o obsłudze migracji maszyn wirtualnych VMware w Azure Migrate.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 87733cac23d0336e4b9319f2a325e8d844e6e5b2
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651959"
---
# <a name="support-matrix-for-vmware-migration"></a>Macierz obsługi migracji VMware

W tym artykule przedstawiono podsumowanie ustawień i ograniczeń dotyczących migracji maszyn wirtualnych VMware z [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceniania maszyn wirtualnych VMware na potrzeby migracji na platformę Azure, zapoznaj się z [matrycą pomocy technicznej](migrate-support-matrix-vmware.md)dotyczącej oceny.


## <a name="migration-options"></a>Opcje migracji

Maszyny wirtualne programu VMware można migrować na kilka sposobów:

- **Korzystanie z migracji bez wykorzystania agentów**: Migruj maszyny wirtualne, aby nie trzeba było instalować żadnych elementów. Należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md) na potrzeby migracji bez agentów.
- **Przy użyciu migracji opartej na agentach**: Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji. W przypadku migracji opartej na agentach należy wdrożyć [urządzenie do replikacji](migrate-replication-appliance.md).

Zapoznaj się z [tym artykułem](server-migrate-overview.md) , aby ustalić, która metoda ma być używana.

## <a name="migration-limitations"></a>Ograniczenia migracji

- Można wybrać maksymalnie 10 maszyn wirtualnych na potrzeby replikacji. Jeśli chcesz migrować więcej maszyn, wykonaj replikację w grupach o wartości 10.
- W przypadku migracji programu VMware bez agenta można jednocześnie uruchomić do 300 replikacji.

## <a name="agentless-migration"></a>Migracja bez wykorzystania agentów 

Ta sekcja podsumowuje wymagania dotyczące migracji bez wykorzystania agentów.

### <a name="vmware-requirements-agentless"></a>Wymagania dotyczące oprogramowania VMware (bez wykorzystania agentów)

Tabela podsumowuje wymagania funkcji hypervisor programu VMware.

**VMware** | **Szczegóły**
--- | ---
**VMware vCenter Server** | Wersja 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere hosta ESXI** | Wersja 5,5, 6,0, 6,5, 6,7, 7,0.
**uprawnienia vCenter Server** | Migracja bez agentów używa [urządzenia migracji](migrate-appliance.md). Urządzenie musi mieć następujące uprawnienia w vCenter Server:<br/><br/> - **Magazyn danych. Browse**: umożliwia przeglądanie plików dzienników maszyn wirtualnych w celu rozwiązywania problemów z tworzeniem i usuwaniem migawki.<br/><br/> - **Datastore. usługi filemanagement**: Zezwalaj na operacje odczytu/zapisu/usuwania/zmiany nazwy w przeglądarce magazynu danych, aby rozwiązywać problemy z tworzeniem i usuwaniem migawki.<br/><br/> - **VirtualMachine.Config. Śledzenia zmian**: Zezwalaj na włączanie lub wyłączanie śledzenia zmian dysków maszyn wirtualnych w celu ściągania zmienionych bloków danych między migawkami.<br/><br/> - **VirtualMachine.Config. DiskLease**: Zezwalaj na operacje dzierżawy dysku dla maszyny wirtualnej w celu odczytania dysku przy użyciu VMware vSphere wirtualnego dysku twardego (VDDK).<br/><br/> - **VirtualMachine. Provisioning. DiskAccess**: (w odniesieniu do vSphere 6,0 i nowsze) Zezwalaj na otwieranie dysku na maszynie wirtualnej w celu uzyskania losowego dostępu do odczytu na dysku przy użyciu VDDK.<br/><br/> - **VirtualMachine. Provisioning. DiskRandomRead**: Zezwól na otwieranie dysku na maszynie wirtualnej w celu odczytania dysku przy użyciu VDDK.<br/><br/> - **VirtualMachine. Provisioning. DiskRandomAccess**: Zezwól na otwieranie dysku na maszynie wirtualnej w celu odczytania dysku przy użyciu VDDK.<br/><br/> - **VirtualMachine. Provisioning. GetVmFiles**: zezwala na operacje odczytu plików SKOJARZONYCH z maszyną wirtualną, pobieranie dzienników i rozwiązywanie problemów w przypadku wystąpienia błędu.<br/><br/> - **VirtualMachine. State. \* **: umożliwia tworzenie migawek maszyn wirtualnych i zarządzanie nimi na potrzeby replikacji.<br/><br/> - **VirtualMachine. wyłączenie**: umożliwia wyłączenie maszyny wirtualnej podczas migracji na platformę Azure.



### <a name="vm-requirements-agentless"></a>Wymagania dotyczące maszyny wirtualnej (bez wykorzystania agentów)

W tabeli zestawiono wymagania dotyczące migracji bez agentów dla maszyn wirtualnych VMware.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane systemy operacyjne** | Można migrować systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](../virtual-machines/linux/endorsed-distros.md) , które są obsługiwane przez platformę Azure.
**Maszyny wirtualne z systemem Windows na platformie Azure** | Przed rozpoczęciem migracji może być konieczne [wprowadzenie pewnych zmian](prepare-for-migration.md#verify-required-changes-before-migrating) na maszynach wirtualnych. 
**Maszyny wirtualne z systemem Linux na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure.<br/><br/> W przypadku systemu Linux Azure Migrate automatycznie wprowadza zmiany w następujących systemach operacyjnych:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8 <br/> Oracle Linux 7,7, 7,7-CI<br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić [wymagane zmiany](prepare-for-migration.md#verify-required-changes-before-migrating) .
**Rozruch systemu Linux** | Jeśli/Boot znajduje się na dedykowanej partycji, powinien znajdować się na dysku systemu operacyjnego i nie można go rozłożyć na wiele dysków.<br/> Jeśli/boot jest częścią partycji głównej (/), partycja "/" powinna znajdować się na dysku systemu operacyjnego i nie może obejmować innych dysków.
**Rozruch z interfejsem UEFI** | Obsługiwane. Maszyny wirtualne oparte na interfejsie UEFI zostaną zmigrowane do maszyn wirtualnych generacji 2 platformy Azure. 
**Rozmiar dysku** | dysk systemu operacyjnego 2 TB (rozruch w systemie BIOS); dysk systemu operacyjnego 4 TB (UEFI); 8 TB dla dysków z danymi.
**Limity dysku** |  Do 60 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji.
**Udostępniony klaster dysków** | Nieobsługiwane.
**Dyski niezależne** | Nieobsługiwane.
**RDM/przekazywanie dysków** | Jeśli maszyny wirtualne mają dyski RDM lub przekazujących, te dyski nie będą replikowane do platformy Azure.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane.
**obiekty docelowe iSCSI** | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane w przypadku migracji bez wykorzystania agentów.
**Wielościeżkowe we/wy** | Nieobsługiwane.
**VMotion magazynu** | Nieobsługiwane. Replikacja nie będzie działała, jeśli maszyna wirtualna korzysta z vMotion magazynu.
**Zespoły kart sieciowych** | Nieobsługiwane.
**Protokół IPv6** | Nieobsługiwane.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko do dysków zarządzanych (dysk twardy w warstwie Standardowa, dysk SSD w warstwie Premium) na platformie Azure.
**Równoczesna replikacja** | 300 maszyn wirtualnych na vCenter Server. Jeśli masz więcej, Migruj je w partiach 300.


### <a name="appliance-requirements-agentless"></a>Wymagania dotyczące urządzenia (bez agentów)

Migracja bez agentów używa [urządzenia Azure Migrate](migrate-appliance.md). Urządzenie można wdrożyć jako maszynę wirtualną VMware przy użyciu szablonu komórki jajowe zaimportowanego do vCenter Server lub przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) w oprogramowaniu VMware.
- Uzyskaj informacje o adresach URL, do których urządzenie musi uzyskać dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [administracji rządowej](migrate-appliance.md#government-cloud-urls) .
- W Azure Government należy wdrożyć urządzenie [przy użyciu skryptu](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Wymagania dotyczące portów (bez wykorzystania agentów)

**Urządzenie** | **Połączenie**
--- | ---
Wprowadzony | Połączenia wychodzące na porcie 443 do przekazywania replikowanych danych na platformę Azure oraz do komunikowania się z usługami Azure Migrate organizowanie replikacji i migracji.
Serwer vCenter | Połączenia przychodzące na porcie 443, aby umożliwić organizowanie replikacji — tworzenie migawek, kopiowanie danych i migawki wersji
Host vSphere/ESXI | Ruch przychodzący na porcie TCP 902 dla urządzenia do replikowania danych z migawek.

## <a name="agent-based-migration"></a>Migracja oparta na agencie 


Ta sekcja podsumowuje wymagania dotyczące migracji opartej na agencie.


### <a name="vmware-requirements-agent-based"></a>Wymagania dotyczące oprogramowania VMware (oparte na agentach)

W tej tabeli zestawiono wsparcie oceny i ograniczenia dotyczące serwerów wirtualizacji VMware.

**Wymagania dotyczące oprogramowania VMware** | **Szczegóły**
--- | ---
**VMware vCenter Server** | Wersja 5,5, 6,0, 6,5 lub 6,7.
**VMware vSphere hosta ESXI** | Wersja 5,5, 6,0, 6,5 lub 6,7.
**uprawnienia vCenter Server** | Konto tylko do odczytu dla vCenter Server.

### <a name="vm-requirements-agent-based"></a>Wymagania dotyczące maszyny wirtualnej (oparte na agentach)

Ta tabela zawiera podsumowanie obsługi maszyn wirtualnych VMware na potrzeby migracji na maszynę wirtualną VMware przy użyciu migracji opartej na agentach.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie maszyny** | Azure Migrate obsługuje migrację dowolnego obciążenia (Powiedz Active Directory, SQL Server itp.) uruchomionego na obsługiwanej maszynie.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu operacyjnego](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) Site Recovery. Azure Migrate zapewnia identyczną obsługę systemu operacyjnego maszyny wirtualnej.
**System plików Linux/magazyn gościa** | Aby uzyskać najnowsze informacje, zapoznaj się z tematem [Obsługa systemu plików Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) dla Site Recovery. Azure Migrate ma identyczną obsługę systemu plików Linux.
**Sieć/magazyn** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami wstępnymi dotyczącymi [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) Site Recovery. Azure Migrate zapewnia identyczne wymagania dotyczące sieci/magazynu.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami dotyczącymi sieci, [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)i [mocy obliczeniowej](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) [platformy Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)dla Site Recovery. Azure Migrate ma identyczne wymagania dotyczące migracji oprogramowania VMware.
**Usługa mobilności** | Agent usługi mobilności musi być zainstalowany na każdej maszynie wirtualnej, która ma zostać poddana migracji.
**Rozruch z interfejsem UEFI** | Obsługiwane. Maszyny wirtualne oparte na interfejsie UEFI zostaną zmigrowane do maszyn wirtualnych generacji 2 platformy Azure. 
**Interfejs UEFI — bezpieczny rozruch**         | Migracja nie jest obsługiwana.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko do dysków zarządzanych (dysk twardy w warstwie Standardowa, dysk SSD w warstwie Premium) na platformie Azure.
**Rozmiar dysku** | dysk systemu operacyjnego 2 TB (rozruch w systemie BIOS); dysk systemu operacyjnego 4 TB (UEFI); 8 TB dla dysków z danymi.
**Limity dysku** |  Do 63 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z szyfrowanymi dyskami/woluminami nie są obsługiwane na potrzeby migracji.
**Udostępniony klaster dysków** | Nieobsługiwane.
**Dyski niezależne** | Obsługiwane.
**Przekazywanie dysków** | Obsługiwane.
**NFS** | Woluminy NFS zainstalowane jako woluminy na maszynach wirtualnych nie zostaną zreplikowane.
**obiekty docelowe iSCSI** | Obsługiwane.
**Wielościeżkowe we/wy** | Nieobsługiwane.
**VMotion magazynu** | Obsługiwane
**Zespoły kart sieciowych** | Nieobsługiwane.
**Protokół IPv6** | Nieobsługiwane.




### <a name="appliance-requirements-agent-based"></a>Wymagania dotyczące urządzeń (oparte na agentach)

Po skonfigurowaniu urządzenia do replikacji przy użyciu szablonu komórki jajowe dostarczonego w centrum Azure Migrate na urządzeniu jest uruchomiony system Windows Server 2016 i jest zgodny z wymaganiami dotyczącymi obsługi. Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami.

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń replikacji](migrate-replication-appliance.md#appliance-requirements) dla programu VMware.
- Na urządzeniu musi być zainstalowany pakiet MySQL. Dowiedz się więcej o [opcjach instalacji](migrate-replication-appliance.md#mysql-installation).
- Informacje o adresach URL, których urządzenie replikacji musi uzyskać dostęp w chmurach publicznych i [administracji](migrate-replication-appliance.md#azure-government-url-access) [publicznej](migrate-replication-appliance.md#url-access) .
- Zapoznaj się z [portami](migrate-replication-appliance.md#port-access) , do których urządzenie replikacji musi uzyskać dostęp.

### <a name="port-requirements-agent-based"></a>Wymagania dotyczące portów (oparte na agentach)

**Urządzenie** | **Połączenie**
--- | ---
Maszyny wirtualne | Usługa mobilności działająca na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwer konfiguracji) na porcie HTTPS 443 przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure (z obsługą agenta lub migracji opartej na agentach) muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure, które zostały podsumowane w tej tabeli. 

**Składnik** | **Wymagania** 
--- | --- | ---
System operacyjny gościa | Weryfikuje obsługiwane systemy operacyjne VMware VM do migracji.<br/> Możliwe jest Migrowanie dowolnego obciążenia działającego w obsługiwanym systemie operacyjnym. 
Architektura systemu operacyjnego gościa | 64-bitowa. 
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. 
Liczba dysków systemu operacyjnego | 1 
Liczba dysków danych | 64 lub mniej. 
Rozmiar dysku danych | Do 8 095 GB
Karty sieciowe | Obsługiwane są wiele kart.
Udostępniony wirtualny dysk twardy | Nieobsługiwane. 
Dysk FC | Nieobsługiwane. 
BitLocker | Nieobsługiwane.<br/><br/> Funkcja BitLocker musi być wyłączona przed przeprowadzeniem migracji maszyny.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. 
Połącz po migracji — Windows | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/><br/> — Przed migracją Włącz protokół RDP na lokalnej maszynie wirtualnej.<br/><br/> Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/><br/> W celu uzyskania dostępu do sieci VPN typu lokacja-lokacja Włącz protokół RDP i Zezwalaj na używanie protokołu RDP w **zaporze systemu Windows**  ->  **dozwolone aplikacje i funkcje** dla sieci **i** połączeń sieciowych.<br/><br/> Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md).
Połącz po migracji — system Linux | Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure po migracji przy użyciu protokołu SSH:<br/><br/> Przed migracją na maszynie lokalnej Sprawdź, czy usługa Secure Shell jest ustawiona do uruchamiania, oraz czy reguły zapory zezwalają na połączenie SSH.<br/><br/> Po przejściu w tryb failover na maszynie wirtualnej platformy Azure Zezwól na połączenia przychodzące do portu SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover oraz dla podsieci platformy Azure, do której jest podłączona.<br/><br/> Dodatkowo Dodaj publiczny adres IP dla maszyny wirtualnej.  


## <a name="next-steps"></a>Następne kroki

[Wybierz](server-migrate-overview.md) opcję migracji VMware.
