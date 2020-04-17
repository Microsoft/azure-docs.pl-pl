---
title: Obsługa migracji VMware w usłudze Azure Migrate
description: Dowiedz się więcej o obsłudze migracji maszyn wirtualnych VMware w usłudze Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538141"
---
# <a name="support-matrix-for-vmware-migration"></a>Macierz wsparcia dla migracji VMware

W tym artykule podsumowano ustawienia pomocy technicznej i ograniczenia dotyczące migracji maszyn wirtualnych VMware za pomocą [programu Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceny maszyn wirtualnych VMware do migracji na platformę Azure, zapoznaj się z [macierzą pomocy technicznej oceny](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opcje migracji

Maszyny wirtualne VMware można migrować na kilka sposobów:

- Migracja bez agenta: migrowanie maszyn wirtualnych bez konieczności instalowania na nich niczego. Wdrażanie [urządzenia migracji platformy Azure](migrate-appliance.md) do migracji bez agenta.
- Z migracji opartej na agentach: Zainstaluj agenta na maszynie Wirtualnej do replikacji. W przypadku migracji opartej na agentach należy wdrożyć [urządzenie replikacji](migrate-replication-appliance.md).

Przejrzyj [ten artykuł,](server-migrate-overview.md) aby dowiedzieć się, której metody chcesz użyć.

## <a name="migration-limitations"></a>Ograniczenia migracji

- Do replikacji można wybrać maksymalnie 10 maszyn wirtualnych jednocześnie. Jeśli chcesz przeprowadzić migrację większej liczby komputerów, należy replikować w grupach po 10.
- W przypadku migracji bezagentowej VMware można jednocześnie uruchomić do 100 replikacji.

## <a name="agentless-vmware-servers"></a>Serwery bezgentowe-VMware

**Vmware** | **Szczegóły**
--- | ---
**Serwer VMware vCenter** | Wersja 5.5, 6.0, 6.5 lub 6.7.
**Host VMware vSphere ESXI** | Wersja 5.5, 6.0, 6.5 lub 6.7.
**Uprawnienia serwera vCenter** | Migracja bezgentowa używa [urządzenia migrujnego](migrate-appliance.md). Urządzenie potrzebuje następujących uprawnień:<br/><br/> - **Datastore.Browse**: Zezwalaj na przeglądanie plików dziennika maszyn wirtualnych w celu rozwiązywania problemów z tworzeniem i usuwaniem migawek.<br/><br/> - **Datastore.LowLevelFileOperations**: Zezwalaj na operacje odczytu/zapisu/usuwania/zmiany nazwy w przeglądarce magazynu danych, aby rozwiązać problem tworzenia i usuwania migawek.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Umożliwia włączenie lub wyłączenie śledzenia zmian dysków maszyn wirtualnych, aby wyciągnąć zmienione bloki danych między migawkami.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: Zezwalaj na operacje dzierżawy dysku dla maszyny Wirtualnej, aby odczytać dysk przy użyciu zestawu VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (specjalnie dla vSphere 6.0 i powyżej) Zezwalaj na otwieranie dysku na maszynie Wirtualnej dla losowego dostępu do odczytu na dysku przy użyciu VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Zezwalaj na otwieranie dysku na maszynie wirtualnej, aby odczytać dysk przy użyciu VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Zezwalaj na otwieranie dysku na maszynie wirtualnej, aby odczytać dysk przy użyciu VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Umożliwia operacje odczytu na plikach skojarzonych z maszyną wirtualną, aby pobrać dzienniki i rozwiązywać problemy w przypadku wystąpienia awarii.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Zezwalaj na tworzenie migawek maszyn wirtualnych i zarządzanie nimi do replikacji.<br/><br/> - **Maszyna wirtualna.Interaction.Power Off:** Zezwalaj na wyłączanie maszyny wirtualnej podczas migracji na platformę Azure.



## <a name="agentless-vmware-vms"></a>Maszyny wirtualne bez agenta-VMware

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane systemy operacyjne** | [Systemy](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operacyjne Windows i [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) które są obsługiwane przez platformę Azure, można migrować przy użyciu migracji bez agenta.
**Wymagane zmiany na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, dzięki czemu można je uruchomić na platformie Azure. Usługa Azure Migrate wprowadza te zmiany automatycznie dla następujących systemów operacyjnych:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> W przypadku innych systemów operacyjnych należy ręcznie wprowadzić zmiany przed migracją. Odpowiednie artykuły zawierają instrukcje dotyczące tego, jak to zrobić.
**Rozruch linuksa** | Jeśli /boot znajduje się na partycji dedykowanej, powinien znajdować się na dysku systemu operacyjnego i nie być rozłożony na wiele dysków.<br/> Jeśli /boot jest częścią głównej partycji (/), partycja '/' powinna znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski.
**Rozruch UEFI** | Maszyny wirtualne z rozruchem UEFI nie są obsługiwane w przypadku migracji.
**Rozmiar dysku** | Dysk systemu operacyjnego o pojemności 2 TB; 8 TB dla dysków z danymi.
**Limity dysków** |  Do 60 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z zaszyfrowanymi dyskami/woluminami nie są obsługiwane do migracji.
**Klaster dysków udostępnionych** | Bez pomocy technicznej.
**Dyski niezależne** | Bez pomocy technicznej.
**Dyski RDM/passthrough** | Jeśli maszyny wirtualne mają dyski RDM lub passthrough, te dyski nie będą replikowane na platformie Azure.
**NFS** | Woluminy nfs zainstalowane jako woluminy na maszynach wirtualnych nie będą replikowane.
**Cele iSCSI** | Maszyny wirtualne z obiekty docelowe iSCSI nie są obsługiwane w przypadku migracji bez agenta.
**Wielościeżkowy io** | Bez pomocy technicznej.
**Pamięć vMotion magazynu** | Bez pomocy technicznej. Replikacja nie będzie działać, jeśli maszyna wirtualna używa pamięci vMotion magazynu.
**Karty sieciowe z zespołami** | Bez pomocy technicznej.
**Protokół IPv6** | Bez pomocy technicznej.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko na dyski zarządzane (standardowy dysk twardy, dysk SSD w wersji premium) na platformie Azure.
**Replikacja jednoczesna** | 100 maszyn wirtualnych na serwer vCenter. Jeśli masz więcej, migruj je w partiach 100.


## <a name="agentless-azure-migrate-appliance"></a>Urządzenie migracji bez agenta-Azure 

Migracja bezgentowa korzysta z [urządzenia migracji platformy Azure](migrate-appliance.md). Urządzenie można wdrożyć jako maszynę wirtualną VMWare przy użyciu szablonu OVA, zaimportowanego do serwera vCenter Server lub [skryptu programu PowerShell](deploy-appliance-script.md).

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---vmware) dla VMware.
- Dowiedz się więcej o adresach URL, do których urządzenie musi mieć dostęp w chmurach [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)
- W usłudze Azure Government należy wdrożyć urządzenie przy użyciu skryptu.

## <a name="agentless-ports"></a>Porty bez agenta

**Urządzenie** | **Połączenia**
--- | ---
Urządzenia | Połączenia wychodzące na porcie 443 w celu przekazywania replikowanych danych na platformę Azure oraz komunikowania się z usługami migracji platformy Azure aranżujących replikację i migrację.
Serwer vCenter | Połączenia przychodzące na porcie 443, aby umożliwić urządzeniu organizowanie replikacji - tworzenie migawek, kopiowanie danych, zwalnianie migawek
Host vSphere/EXSI | Przychodzące na porcie TCP 902 dla urządzenia do replikowania danych z migawek.


## <a name="agent-based-vmware-servers"></a>Serwery VMware oparte na agentach
W tej tabeli podsumowano obsługę oceny i ograniczenia dotyczące serwerów wirtualizacji VMware.

**Wymagania dotyczące VMware** | **Szczegóły**
--- | ---
**Serwer VMware vCenter** | Wersja 5.5, 6.0, 6.5 lub 6.7.
**Host VMware vSphere ESXI** | Wersja 5.5, 6.0, 6.5 lub 6.7.
**Uprawnienia serwera vCenter** | Konto tylko do odczytu dla serwera vCenter Server.

## <a name="agent-based-vmware-vms"></a>Maszyny wirtualne oparte na agentach

W tabeli podsumowano obsługę maszyn wirtualnych VMware dla maszyn wirtualnych, które mają zostać migrowane przy użyciu migracji opartej na agentach.

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obciążenie komputera** | Usługa Azure Migrate obsługuje migrację dowolnego obciążenia (na przykład usługi Active Directory, serwera SQL itp.) uruchomionego na obsługiwanym komputerze.
**Systemy operacyjne** | Aby uzyskać najnowsze informacje, zapoznaj się z [obsługą systemu operacyjnego](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) dla usługi Site Recovery. Usługa Azure Migrate zapewnia identyczną obsługę systemu operacyjnego maszyny Wirtualnej.
**System plików Linux/pamięć gościnna** | Aby uzyskać najnowsze informacje, zapoznaj się z [obsługą systemu plików Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) dla usługi Site Recovery. Usługa Azure Migrate ma identyczną obsługę systemu plików Systemu Linux.
**Sieć/pamięć masowa** | Aby uzyskać najnowsze informacje, zapoznaj się z wymaganiami wstępnymi [sieci](../site-recovery/vmware-physical-azure-support-matrix.md#network) i [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#storage) dla odzyskiwania witryny. Usługa Azure Migrate zapewnia identyczne wymagania dotyczące sieci/magazynu.
**Wymagania platformy Azure** | Aby uzyskać najnowsze informacje, zapoznaj się z [wymaganiami](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)dotyczącymi sieci, [magazynu](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)i [danych obliczeniowych](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) platformy Azure dotyczącymi odzyskiwania witryny. Usługa Azure Migrate ma identyczne wymagania dotyczące migracji oprogramowania VMware.
**Usługa mobilności** | Agent usługi mobilności musi być zainstalowany na każdej maszynie wirtualnej, którą chcesz przeprowadzić migrację.
**Rozruch UEFI** | Zmigrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową systemu BIOS.<br/><br/> Dysk systemu operacyjnego powinien mieć maksymalnie cztery partycje, a woluminy powinny być sformatowane za pomocą systemu plików NTFS.
**Dysk docelowy** | Maszyny wirtualne można migrować tylko na dyski zarządzane (standardowy dysk twardy, dysk SSD w wersji premium) na platformie Azure.
**Rozmiar dysku** | Dysk systemu operacyjnego o pojemności 2 TB; 8 TB dla dysków z danymi.
**Limity dysków** |  Do 63 dysków na maszynę wirtualną.
**Zaszyfrowane dyski/woluminy** | Maszyny wirtualne z zaszyfrowanymi dyskami/woluminami nie są obsługiwane do migracji.
**Klaster dysków udostępnionych** | Bez pomocy technicznej.
**Dyski niezależne** | Obsługiwane.
**Dyski przekazywania** | Obsługiwane.
**NFS** | Woluminy nfs zainstalowane jako woluminy na maszynach wirtualnych nie będą replikowane.
**Cele iSCSI** | Maszyny wirtualne z obiekty docelowe iSCSI nie są obsługiwane w przypadku migracji bez agenta.
**Wielościeżkowy io** | Bez pomocy technicznej.
**Pamięć vMotion magazynu** | Obsługiwane
**Karty sieciowe z zespołami** | Bez pomocy technicznej.
**Protokół IPv6** | Bez pomocy technicznej.




## <a name="agent-based-replication-appliance"></a>Urządzenie replikacji oparte na agentach 

Podczas konfigurowania urządzenia replikacji przy użyciu szablonu ova dostępnego w centrum migracji platformy Azure urządzenie uruchamia system Windows Server 2016 i spełnia wymagania pomocy technicznej. Jeśli urządzenie replikacji zostanie skonfigurowane ręcznie na serwerze fizycznym, upewnij się, że jest ono zgodne z wymaganiami.

- Dowiedz się więcej o [wymaganiach urządzeń replikacji](migrate-replication-appliance.md#appliance-requirements) dla VMware.
- MySQL musi być zainstalowany na urządzeniu. Dowiedz się więcej o [opcjach instalacji](migrate-replication-appliance.md#mysql-installation).
- Dowiedz się więcej o adresach URL, do których urządzenie replikacji musi uzyskać dostęp w chmurach [publicznych](migrate-replication-appliance.md#url-access) i [rządowych.](migrate-replication-appliance.md#azure-government-url-access)
- Przejrzyj [porty,](migrate-replication-appliance.md#port-access) do które urządzenie replikacji musi uzyskać dostęp.

## <a name="agent-based-ports"></a>Porty oparte na agentach

**Urządzenie** | **Połączenia**
--- | ---
Maszyny wirtualne | Usługa mobilności uruchomiona na maszynach wirtualnych komunikuje się z lokalnym urządzeniem replikacji (serwerem konfiguracji) na porcie HTTPS 443 przychodzącym w celu zarządzania replikacją.<br/><br/> Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 przychodzącym. Ten port można modyfikować.
Urządzenie replikacji | Urządzenie replikacji organizuje replikację za pomocą platformy Azure za pośrednictwem portu HTTPS 443 wychodzącego.
Serwer przetwarzania | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do magazynu platformy Azure za pomocą portu 443 wychodzącego.<br/> Domyślnie serwer przetwarzania jest uruchamiany na urządzeniu replikacji.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy usługa Site Recovery uruchamia sprawdzanie wymagań wstępnych pod kątem replikacji, sprawdzanie zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Weryfikuje obsługiwane systemy operacyjne VMware VMm dla migracji.<br/> Można migrować dowolne obciążenie uruchomione w obsługiwanym systemie operacyjnym. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Architektura systemu operacyjnego gościa | 64-bitowy. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków systemu operacyjnego | 1 | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków danych | 64 lub mniej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku danych | Do 4095 GB | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Karty sieciowe | Obsługiwanych jest wiele kart. |
Udostępniony wirtualny dysk twardy | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Dysk FC | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
BitLocker | Bez pomocy technicznej. | Funkcja BitLocker musi być wyłączona przed włączeniem replikacji komputera.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa urządzenia musi zaczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w odzyskiwaniu witryny.
Łączenie się po migracji-Windows | Aby połączyć się z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> - Przed migracją włącza RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> Aby uzyskać dostęp do sieci VPN między lokacjami, włącz prow i zezwalaj na prow w **zaporze** -> systemu Windows**Dozwolone aplikacje i funkcje** dla sieci domen i sieci **prywatnych.** Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md). |
Połącz się po migracji-Linux | Aby połączyć się z maszynami wirtualnymi platformy Azure po migracji przy użyciu funkcji SSH:<br/> Przed migracją na komputerze lokalnym sprawdź, czy usługa Bezpieczne powłoki jest ustawiona na Start i czy reguły zapory zezwalają na połączenie SSH.<br/> Po przełączeniu awaryjnym na maszynie Wirtualnej platformy Azure zezwalaj na połączenia przychodzące z portem SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii i dla podsieci platformy Azure, z którą jest nawiązycona. Ponadto dodaj publiczny adres IP maszyny Wirtualnej. |  


## <a name="next-steps"></a>Następne kroki

[Wybierz](server-migrate-overview.md) opcję migracji VMware.
