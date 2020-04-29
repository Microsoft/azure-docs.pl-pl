---
title: Przygotuj maszyny do migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować maszyny lokalne do migracji za pomocą Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927469"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Przygotowywanie maszyn lokalnych do migracji na platformę Azure

W tym artykule opisano sposób przygotowania maszyn lokalnych przed rozpoczęciem migracji na platformę Azure przy użyciu [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool).

W tym artykule opisano następujące zagadnienia:
> [!div class="checklist"]
> * Sprawdź ograniczenia migracji.
> * Sprawdź wymagania systemu operacyjnego i ograniczenia dotyczące obsługi.
> * Przejrzyj dostęp do adresów URL i portów dla maszyn, które chcesz zmigrować.
> * Przejrzyj zmiany, które należy wykonać przed rozpoczęciem migracji.
> * Skonfiguruj ustawienia, aby litery dysku były zachowywane po migracji.
> * Przygotuj maszyny, aby można było połączyć się z maszynami wirtualnymi platformy Azure po migracji.

## <a name="verify-migration-limitations"></a>Weryfikuj ograniczenia migracji

- W jednym Azure Migrate projekcie można ocenić do 35 000 maszyn wirtualnych VMware/maszyn wirtualnych funkcji Hyper-V, korzystając z migracji Azure Migrate Server. Projekt może łączyć maszyny wirtualne VMware i maszyny wirtualne funkcji Hyper-V, a także limity dla każdej z nich.
- Do migracji można wybrać maksymalnie 10 maszyn wirtualnych. Jeśli chcesz przeprowadzić replikację więcej, Replikuj w grupach o wartości 10.
- W przypadku migracji programu VMware bez agenta można jednocześnie uruchomić do 100 replikacji.

## <a name="verify-operating-system-requirements"></a>Sprawdź wymagania dotyczące systemu operacyjnego

- Sprawdź, czy [systemy operacyjne Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) są obsługiwane na platformie Azure.
- Sprawdź, czy [dystrybucje systemu Linux](../virtual-machines/linux/endorsed-distros.md) są obsługiwane na platformie Azure.

## <a name="see-whats-supported"></a>Zobacz, co jest obsługiwane

W przypadku maszyn wirtualnych VMware Migracja serwera obsługuje funkcję [bezobsługowego agenta lub migrację opartą na agencie](server-migrate-overview.md).

- **Maszyny wirtualne VMware**: Weryfikuj [wymagania dotyczące migracji i obsługa](migrate-support-matrix-vmware-migration.md) maszyn wirtualnych VMware.
- **Maszyny wirtualne funkcji Hyper-v**: Weryfikuj [wymagania dotyczące migracji i obsługa](migrate-support-matrix-hyper-v-migration.md) maszyn wirtualnych funkcji Hyper-v.
- **Maszyny fizyczne**: Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-physical-migration.md) lokalnych maszyn fizycznych i innych zwirtualizowanych serwerów. 

## <a name="review-url-and-port-access"></a>Przeglądanie dostępu do adresu URL i portów

Podczas migracji maszyny mogą potrzebować dostępu do Internetu.

- **Urządzenie Azure Migrate**: Przejrzyj [adresy URL](migrate-appliance.md#url-access) i [porty](migrate-support-matrix-vmware-migration.md#agentless-ports) , do których urządzenie Azure Migrate musi uzyskać dostęp podczas migracji bez agenta.
- **Migracja oparta na agentach maszyn wirtualnych VMware**: przegląd [adresów URL](migrate-replication-appliance.md#url-access) i [portów](migrate-replication-appliance.md#port-access) używanych przez urządzenie do replikacji podczas migracji opartej na agentach maszyn wirtualnych VMware. 
- **Hosty funkcji Hyper-v**: przeglądanie [adresów URL i portów](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) hosty funkcji Hyper-v muszą uzyskać dostęp podczas migracji. 
- **Serwery fizyczne**: przegląd [adresów URL](migrate-replication-appliance.md#url-access) i [portów](migrate-replication-appliance.md#port-access) używanych przez urządzenie do replikacji podczas migracji serwera fizycznego.

## <a name="verify-required-changes-before-migrating"></a>Sprawdź wymagane zmiany przed migracją

Niektóre maszyny wirtualne mogą wymagać zmian, aby mogły być uruchamiane na platformie Azure. Azure Migrate automatycznie wprowadza te zmiany dla maszyn wirtualnych, na których są uruchomione następujące systemy operacyjne:

- Red Hat Enterprise Linux 7.0 +, 6.5 +
- CentOS 7.0 +, 6.5 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

W przypadku innych systemów operacyjnych należy ręcznie przygotować maszyny przed migracją. 

### <a name="prepare-windows-machines"></a>Przygotowywanie maszyn z systemem Windows

W przypadku migrowania maszyny z systemem Windows przed migracją wprowadź następujące zmiany. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmian może się zdarzyć, że maszyna wirtualna nie uruchomi się na platformie Azure.

1. [Włącz konsolę seryjną platformy Azure](../virtual-machines/troubleshooting/serial-console-windows.md) dla maszyny wirtualnej platformy Azure. Włączenie konsoli ułatwia rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure zostanie uruchomiona przy użyciu obrazu dysku. Rozruch obrazu dysku jest równoznaczny z ponownym uruchomieniem nowej maszyny wirtualnej. 
2. W przypadku migrowania maszyn z systemem Windows Server 2003 zainstaluj usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Przygotowywanie maszyn z systemem Linux

1. Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux. Większość nowych wersji dystrybucji systemu Linux domyślnie obejmuje usługi integracji funkcji Hyper-V w systemie Linux.
2. Ponownie skompiluj obraz inicjowania systemu Linux, aby zawierał niezbędne sterowniki funkcji Hyper-V. Ponowne skompilowanie obrazu init gwarantuje, że maszyna wirtualna zostanie przeuruchamiana na platformie Azure (wymagana tylko w przypadku niektórych dystrybucji).
3. [Włącz rejestrowanie konsoli szeregowej platformy Azure](../virtual-machines/troubleshooting/serial-console-linux.md). Włączenie rejestrowania konsoli ułatwia rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure zostanie uruchomiona przy użyciu obrazu dysku. Rozruch obrazu dysku jest równoznaczny z ponownym uruchomieniem nowej maszyny wirtualnej.
4. Zaktualizuj plik mapy urządzeń za pomocą skojarzeń nazw urządzeń z systemem, aby korzystać z trwałych identyfikatorów urządzeń.
5. Zaktualizuj wpisy w pliku fstab, aby użyć trwałych identyfikatorów woluminów.
6. Usuń wszystkie reguły udev, które zastrzegają nazwy interfejsów na podstawie adresu MAC i tak dalej.
7. Zaktualizuj interfejsy sieciowe, aby odbierać adresy IP z serwera DHCP.

Dowiedz się więcej na temat [kroków umożliwiających uruchomienie maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/create-upload-generic.md)i uzyskiwanie instrukcji dotyczących niektórych popularnych dystrybucji systemu Linux.

## <a name="preserve-drive-letters-after-migration"></a>Zachowaj litery dysku po migracji

W przypadku migrowania maszyny lokalnej do Microsoft Azure litery dysku dodatkowych dysków z danymi mogą ulec zmianie z oryginalnych wartości. 

Domyślnie maszyny wirtualne platformy Azure są przypisane do dysku D, który ma być używany jako magazyn tymczasowy. To przypisanie dysku powoduje, że wszystkie pozostałe przydziały przyłączone do dysku magazynu są zwiększane o jedną literę. Na przykład jeśli instalacja lokalna używa dysku z danymi, który jest przypisany do dysku D dla instalacji aplikacji, przypisanie dla tego dysku jest zwiększane do dysku E po przeprowadzeniu migracji maszyny wirtualnej na platformę Azure. Aby zapobiec automatycznemu przypisaniu i upewnić się, że system Azure przypisuje kolejną literę wolnego dysku do jego woluminu tymczasowego, ustaw zasady sieci magazynowania (SAN) na **OnlineAll**:

1. Na maszynie lokalnej (nie na serwerze hosta) Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Wprowadź polecenie **diskpart**.
3. Wprowadź **Sieć San**. Jeśli litera dysku systemu operacyjnego gościa nie jest zachowywana, zwracany jest **tryb offline wszystkie** lub **offline udostępnione** .
4. W wierszu polecenia **diskpart** wprowadź wartość **zasady sieci San = OnlineAll**. To ustawienie gwarantuje, że dyski będą przełączane w tryb online i zapewnia możliwość odczytu i zapisu na obu dyskach.
5. Podczas migracji testów można sprawdzić, czy litery dysku są zachowywane.

## <a name="check-azure-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej platformy Azure

Maszyny lokalne replikowane na platformę Azure muszą być zgodne z wymaganiami maszyny wirtualnej platformy Azure dotyczącymi systemu operacyjnego i architektury, dysków, ustawień sieciowych i nazw maszyn wirtualnych. Przed przeprowadzeniem migracji Sprawdź wymagania dotyczące [maszyn wirtualnych VMware i serwerów fizycznych](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)oraz [maszyn wirtualnych funkcji Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) .

## <a name="prepare-to-connect-after-migration"></a>Przygotowywanie do nawiązywania połączenia po migracji

Maszyny wirtualne platformy Azure są tworzone podczas migracji na platformę Azure. Po migracji musisz mieć możliwość nawiązania połączenia z nowymi maszynami wirtualnymi platformy Azure. Do pomyślnego nawiązania połączenia wymagane jest wykonanie wielu kroków.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Przygotowywanie się do łączenia się z maszynami wirtualnymi systemu Windows Azure

Na maszynach lokalnych z systemem Windows:

1. Skonfiguruj ustawienia systemu Windows. Ustawienia obejmują usunięcie wszelkich statycznych tras stałych lub serwera proxy WinHTTP.
2. Upewnij się, że [wymagane usługi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) są uruchomione.
3. Włącz funkcję pulpitu zdalnego (RDP), aby zezwolić na zdalne połączenia z maszyną lokalną. Dowiedz się [, jak włączyć protokół RDP przy użyciu programu PowerShell](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem Internetu po migracji, w zaporze systemu Windows na maszynie lokalnej Zezwalaj na protokoły TCP i UDP w profilu publicznym i ustaw protokół RDP jako dozwoloną aplikację dla wszystkich profilów.
5. Jeśli chcesz uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem sieci VPN typu lokacja-lokacja po migracji, w zaporze systemu Windows na maszynie lokalnej, Zezwól na protokół RDP dla domeny i profilów prywatnych. Dowiedz się, jak [zezwolić na ruch RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na lokalnej maszynie wirtualnej podczas migracji. W takim przypadku aktualizacje mogą rozpocząć instalację na maszynie wirtualnej platformy Azure po migracji i nie będzie można zalogować się do maszyny wirtualnej do momentu zakończenia aktualizacji.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Przygotowywanie się do łączenia się z maszynami wirtualnymi systemu Linux Azure

Na lokalnych maszynach z systemem Linux:

1. Sprawdź, czy usługa Secure Shell jest uruchamiana automatycznie przy rozruchu systemu.
2. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurowanie maszyn wirtualnych platformy Azure po migracji

Po przeprowadzeniu migracji wykonaj następujące kroki na maszynach wirtualnych platformy Azure, które zostały utworzone:

1. Aby nawiązać połączenie z maszyną wirtualną za pośrednictwem Internetu, przypisz publiczny adres IP do maszyny wirtualnej. Musisz użyć innego publicznego adresu IP dla maszyny wirtualnej platformy Azure niż używany przez maszynę lokalną. [Dowiedz się więcej](../virtual-network/virtual-network-public-ip-address.md).
2. Sprawdź, czy reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) na maszynie wirtualnej zezwalają na połączenia przychodzące do portu RDP lub SSH.
3. Sprawdź [diagnostykę rozruchu](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) , aby wyświetlić maszynę wirtualną.

> [!NOTE]
> Usługa Azure bastionu oferuje prywatny dostęp RDP i SSH do maszyn wirtualnych platformy Azure. [Dowiedz się więcej](../bastion/bastion-overview.md) o tej usłudze.

## <a name="next-steps"></a>Następne kroki

Zdecyduj, która metoda ma być używana do [migrowania maszyn wirtualnych VMware](server-migrate-overview.md) na platformę Azure, lub Rozpocznij Migrowanie [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [serwerów fizycznych lub maszyn wirtualnych zwirtualizowanych lub chmurowych](tutorial-migrate-physical-virtual-machines.md).
