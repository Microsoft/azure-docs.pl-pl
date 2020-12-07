---
title: Przygotuj maszyny do migracji za pomocą Azure Migrate
description: Dowiedz się, jak przygotować maszyny lokalne do migracji za pomocą Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 979f40e13aab71f02a316e4ddf60306170166845
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753930"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Przygotowywanie maszyn lokalnych do migracji na platformę Azure

W tym artykule opisano sposób przygotowania maszyn lokalnych przed przeprowadzeniem migracji na platformę Azure za pomocą narzędzia do [migracji Azure Migrate: Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

W tym artykule opisano następujące zagadnienia:
> [!div class="checklist"]
> * Przejrzyj ograniczenia migracji.
> * Wybierz metodę migrowania maszyn wirtualnych VMware
> * Sprawdź wymagania funkcji hypervisor i systemu operacyjnego dla maszyn, które chcesz zmigrować.
> * Przejrzyj dostęp do adresów URL i portów dla maszyn, które chcesz zmigrować.
> * Przejrzyj zmiany, które należy wykonać przed rozpoczęciem migracji.
> * Sprawdź wymagania dotyczące maszyn wirtualnych platformy Azure dla zmigrowanych maszyn
> * Przygotuj maszyny, aby można było połączyć się z maszynami wirtualnymi platformy Azure po migracji.



## <a name="verify-migration-limitations"></a>Weryfikuj ograniczenia migracji

W tabeli zestawiono limity odnajdywania, oceny i migracji dla Azure Migrate. Zalecamy przeprowadzenie oceny maszyn przed migracją, ale nie jest to konieczne.

**Scenariusz** | **Project** | **Odnajdywanie/Ocena** | **Migracja**
--- | --- | --- | ---
**Maszyny wirtualne VMware** | Odkrywaj i oceniaj do 35 000 maszyn wirtualnych w jednym projekcie Azure Migrate. | Odkryj do 10 000 maszyn wirtualnych VMware z jednym [Azure Migrate urządzeniem](common-questions-appliance.md) dla programu VMware. | **Migracja bez wykorzystania agentów**: można jednocześnie replikować maksymalnie 300 maszyn wirtualnych. W celu uzyskania najlepszej wydajności zalecamy utworzenie wielu partii maszyn wirtualnych, jeśli masz więcej niż 50.<br/><br/> **Migracja oparta na agentach**: można [skalować](./agent-based-migration-architecture.md#performance-and-scaling) w poziomie [urządzenie replikacji](migrate-replication-appliance.md) , aby replikować dużą liczbę maszyn wirtualnych.<br/><br/> W portalu można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Aby replikować więcej maszyn, Dodaj w partiach 10.
**Maszyny wirtualne funkcji Hyper-V** | Odkrywaj i oceniaj do 35 000 maszyn wirtualnych w jednym projekcie Azure Migrate. | Odkryj do 5 000 maszyn wirtualnych funkcji Hyper-V z jednym urządzeniem Azure Migrate | Urządzenie nie jest używane na potrzeby migracji funkcji Hyper-V. Zamiast tego dostawca replikacji funkcji Hyper-V jest uruchamiany na każdym hoście funkcji Hyper-V.<br/><br/> Na wydajność replikacji ma wpływ czynniki wydajności, takie jak postęp maszyny wirtualnej, i przekazywanie przepustowości dla danych replikacji.<br/><br/> W portalu można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Aby replikować więcej maszyn, Dodaj w partiach 10.
**Maszyny fizyczne** | Odnajdź i Oceń do 35 000 maszyn w jednym projekcie Azure Migrate. | Odkryj do 250 serwerów fizycznych z jednym Azure Migrate urządzeniem dla serwerów fizycznych. | [Urządzenie replikacji](migrate-replication-appliance.md) można [skalować w poziomie](./agent-based-migration-architecture.md#performance-and-scaling) w celu replikowania dużej liczby serwerów.<br/><br/> W portalu można wybrać maksymalnie 10 maszyn na potrzeby replikacji. Aby replikować więcej maszyn, Dodaj w partiach 10.

## <a name="select-a-vmware-migration-method"></a>Wybierz metodę migracji VMware

W przypadku migrowania maszyn wirtualnych VMware na platformę Azure należy [porównać](server-migrate-overview.md#compare-migration-methods) metody migracji bez agenta i oparte na agentach, aby zdecydować, co z nich działa.

## <a name="verify-hypervisor-requirements"></a>Weryfikuj wymagania funkcji hypervisor

- Sprawdź, czy są spełnione wymagania programu [VMware Agent](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)lub [agenta VMware](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) .
- Sprawdź wymagania dotyczące [hosta funkcji Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) .


## <a name="verify-operating-system-requirements"></a>Sprawdź wymagania dotyczące systemu operacyjnego

Sprawdź Obsługiwane systemy operacyjne na potrzeby migracji:

- W przypadku migrowania maszyn wirtualnych VMware lub maszyn wirtualnych funkcji Hyper-V należy sprawdzić wymagania dotyczące maszyn wirtualnych programu VMware pod kątem [wykorzystania agentów](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)i migracji [opartej na agentach](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) oraz wymagania dotyczące [maszyn wirtualnych funkcji Hyper-v](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Sprawdź, czy [systemy operacyjne Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) są obsługiwane na platformie Azure.
- Sprawdź, czy [dystrybucje systemu Linux](../virtual-machines/linux/endorsed-distros.md) są obsługiwane na platformie Azure.

## <a name="review-url-and-port-access"></a>Przeglądanie dostępu do adresu URL i portów

Sprawdź, które adresy URL i porty są dostępne podczas migracji.

**Scenariusz** | **Szczegóły** |  **Adresy URL** | **Porty**
--- | --- | --- | ---
**Migracja bez agenta VMware** | Program używa [urządzenia Azure Migrate](migrate-appliance-architecture.md) do migracji. Nic nie jest zainstalowane na maszynach wirtualnych VMware. | Przejrzyj publiczną i służbową [adresy URL](migrate-appliance.md#url-access) do odnajdowania, oceny i migracji z urządzeniem. | [Przejrzyj](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) wymagania dotyczące portów dla migracji bez agentów.
**Migracja oparta na agencie VMware** | Używa [urządzenia replikacji](migrate-replication-appliance.md) do migracji. Agent usługi mobilności jest instalowany na maszynach wirtualnych. | Przejrzyj [chmurę publiczną](migrate-replication-appliance.md#url-access) i adresy URL [Azure Government](migrate-replication-appliance.md#azure-government-url-access) , do których urządzenie replikacji musi uzyskać dostęp. | [Przejrzyj](migrate-replication-appliance.md#port-access) porty używane podczas migracji opartej na agencie.
**Migracja funkcji Hyper-V** | Program używa dostawcy zainstalowanego na hostach funkcji Hyper-V na potrzeby migracji. Nic nie jest zainstalowane na maszynach wirtualnych funkcji Hyper-V. | Przejrzyj [chmurę publiczną](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) i adresy URL [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) , których dostawca replikacji działa na hostach musi uzyskać dostęp. | Dostawca replikacji na hoście funkcji Hyper-V używa połączeń wychodzących na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej.
**Maszyny fizyczne** | Używa [urządzenia replikacji](migrate-replication-appliance.md) do migracji. Agent usługi mobilności jest instalowany na komputerach fizycznych. | Przejrzyj [chmurę publiczną](migrate-replication-appliance.md#url-access) i adresy URL [Azure Government](migrate-replication-appliance.md#azure-government-url-access) , do których urządzenie replikacji musi uzyskać dostęp. | [Zapoznaj](migrate-replication-appliance.md#port-access) się z portami używanymi podczas migracji fizycznej.

## <a name="verify-required-changes-before-migrating"></a>Sprawdź wymagane zmiany przed migracją

Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.

- W niektórych systemach operacyjnych Azure Migrate wprowadza zmiany automatycznie podczas procesu replikacji/migracji.
- W przypadku innych systemów operacyjnych należy skonfigurować ustawienia ręcznie.
- Ważne jest, aby skonfigurować ustawienia ręcznie przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.

Przejrzyj tabele, aby zidentyfikować zmiany, które należy wprowadzić.

### <a name="windows-machines"></a>Maszyny z systemem Windows

Wymagane zmiany zostały podsumowane w tabeli.

**Akcja** | **VMware (migracja bez agentów)** | **Maszyny/Physical (oparte na agentach)** | **System Windows w funkcji Hyper-V** 
--- | --- | --- | ---
**Skonfiguruj zasady sieci SAN jako wszystkie**<br/><br/> Zapewnia to, że woluminy systemu Windows na maszynie wirtualnej platformy Azure używają tych samych przypisań liter dysku co lokalna maszyna wirtualna. | Ustawiaj automatycznie dla maszyn z systemem Windows Server 2008 R2 lub nowszym.<br/><br/> Skonfiguruj ręcznie w przypadku starszych systemów operacyjnych. | Ustawiana automatycznie w większości przypadków. | Skonfiguruj ręcznie.
**Zainstaluj integrację gościa funkcji Hyper-V** | [Zainstaluj ręcznie](prepare-windows-server-2003-migration.md#install-on-vmware-vms) na komputerach z systemem Windows Server 2003. | [Zainstaluj ręcznie](prepare-windows-server-2003-migration.md#install-on-vmware-vms) na komputerach z systemem Windows Server 2003. | [Zainstaluj ręcznie](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) na komputerach z systemem Windows Server 2003.
**Włącz konsolę seryjną platformy Azure**.<br/><br/>[Włącz konsolę](../virtual-machines/troubleshooting/serial-console-windows.md) programu na maszynach wirtualnych platformy Azure, aby pomóc w rozwiązywaniu problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure zostanie uruchomiona przy użyciu obrazu dysku. Rozruch obrazu dysku jest równoznaczny z ponownym uruchomieniem nowej maszyny wirtualnej. | Włącz ręcznie | Włącz ręcznie | Włącz ręcznie
**Połącz po migracji**<br/><br/> Aby nawiązać połączenie po migracji, należy wykonać kilka czynności przed przeprowadzeniem migracji. | [Skonfiguruj](#prepare-to-connect-to-azure-windows-vms) ręcznie. | [Skonfiguruj](#prepare-to-connect-to-azure-windows-vms) ręcznie. | [Skonfiguruj](#prepare-to-connect-to-azure-windows-vms) ręcznie.


#### <a name="configure-san-policy"></a>Konfigurowanie zasad sieci SAN

Domyślnie maszyny wirtualne platformy Azure są przypisane do dysku D, który ma być używany jako magazyn tymczasowy.

- To przypisanie dysku powoduje, że wszystkie pozostałe przydziały przyłączone do dysku magazynu są zwiększane o jedną literę.
- Na przykład jeśli instalacja lokalna używa dysku z danymi, który jest przypisany do dysku D dla instalacji aplikacji, przypisanie dla tego dysku jest zwiększane do dysku E po przeprowadzeniu migracji maszyny wirtualnej na platformę Azure. 
- Aby zapobiec automatycznemu przypisaniu i upewnić się, że system Azure przypisuje kolejną literę wolnego dysku do jego woluminu tymczasowego, ustaw zasady sieci magazynowania (SAN) na **OnlineAll**:

Skonfiguruj to ustawienie ręcznie w następujący sposób:

1. Na maszynie lokalnej (nie na serwerze hosta) Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Wprowadź polecenie **diskpart**.
3. Wprowadź **Sieć San**. Jeśli litera dysku systemu operacyjnego gościa nie jest zachowywana, zwracany jest **tryb offline wszystkie** lub **offline udostępnione** .
4. W wierszu polecenia **diskpart** wprowadź wartość **zasady sieci San = OnlineAll**. To ustawienie gwarantuje, że dyski będą przełączane w tryb online i zapewnia możliwość odczytu i zapisu na obu dyskach.
5. Podczas migracji testów można sprawdzić, czy litery dysku są zachowywane.


### <a name="linux-machines"></a>Maszyny z systemem Linux

Azure Migrate automatycznie wykonuje te akcje dla tych wersji

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x
- Cent OS 7,7, 7,6, 7,5, 7,4, 6. x
- SUSE Linux Enterprise Server 12 SP1 +
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7
- Oracle Linux 7,7, 7,7-CI

W przypadku innych wersji Przygotuj maszyny zgodnie z podsumowaniem w tabeli.  


**Akcja** | **Szczegóły** | **Wersja systemu Linux**
--- | --- | ---
**Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux** | Ponownie skompiluj obraz inicjowania systemu Linux, aby zawierał niezbędne sterowniki funkcji Hyper-V. Ponowne skompilowanie obrazu init gwarantuje, że maszyna wirtualna zostanie przeprowadzony na platformie Azure. | Większość nowych wersji dystrybucji systemu Linux jest domyślnie uwzględnionych w tym systemie.<br/><br/> Jeśli nie jest uwzględniony, zainstaluj ją ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej.
**Włącz rejestrowanie w konsoli szeregowej platformy Azure** | Włączenie rejestrowania konsoli ułatwia rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure zostanie uruchomiona przy użyciu obrazu dysku. Rozruch obrazu dysku jest równoznaczny z ponownym uruchomieniem nowej maszyny wirtualnej.<br/><br/> Postępuj zgodnie z [tymi instrukcjami](../virtual-machines/troubleshooting/serial-console-linux.md) , aby włączyć.
**Zaktualizuj plik mapy urządzeń** | Zaktualizuj plik mapy urządzeń za pomocą skojarzeń nazw urządzeń z systemem, aby korzystać z trwałych identyfikatorów urządzeń. | Instaluj ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej. (Dotyczy tylko w scenariuszu VMware opartym na agentach)
**Aktualizowanie wpisów fstab** |  Aktualizowanie wpisów w celu używania identyfikatorów woluminów trwałych.    | Aktualizuj ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej.
**Usuń regułę udev** | Usuń wszystkie reguły udev, które rezerwują nazwy interfejsów na podstawie adresu MAC itp. | Usuń ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej.
**Aktualizowanie interfejsów sieciowych** | Aktualizowanie interfejsów sieciowych do odbierania adresów IP na podstawie protokołu DHCP. NST | Aktualizuj ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej.
**Włączanie protokołu SSH** | Upewnij się, że protokół SSH jest włączony i że usługa SSHD jest uruchomiona automatycznie po ponownym uruchomieniu.<br/><br/> Upewnij się, że przychodzące żądania połączenia SSH nie są blokowane przez zaporę systemu operacyjnego ani reguły obsługujące skrypty.| Włącz ręcznie dla wszystkich wersji z wyjątkiem tych, które zostały wywołane powyżej.

Poniższa tabela zawiera podsumowanie kroków wykonywanych automatycznie w przypadku systemów operacyjnych wymienionych powyżej.


| Akcja                                      | \-Migracja VMware oparta na agencie | Migracja VMware bez agentów | Funkcja Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Zainstaluj usługi integracji funkcji Hyper-V w systemie \- Linux | Tak                           | Tak                        | Nie jest wymagany |
| Włącz rejestrowanie w konsoli szeregowej platformy Azure         | Tak                           | Tak                        | Nie         |
| Zaktualizuj plik mapy urządzeń                      | Tak                           | Nie                         | Nie         |
| Aktualizowanie wpisów fstab                        | Tak                           | Tak                        | Nie         |
| Usuń regułę udev                            | Tak                           | Tak                        | Nie         |
| Aktualizowanie interfejsów sieciowych                   | Tak                           | Tak                        | Nie         |
| Włączanie protokołu SSH                                  | Nie                            | Nie                         | Nie         |

Dowiedz się więcej o krokach [uruchamiania maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/create-upload-generic.md)i uzyskaj instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.


## <a name="check-azure-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej platformy Azure

Maszyny lokalne replikowane na platformę Azure muszą być zgodne z wymaganiami maszyny wirtualnej platformy Azure dotyczącymi systemu operacyjnego i architektury, dysków, ustawień sieciowych i nazw maszyn wirtualnych.

Przed przeprowadzeniem migracji zapoznaj się z wymaganiami dotyczącymi maszyn wirtualnych platformy Azure dla programu [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [funkcji Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)i migracji [serwera fizycznego](migrate-support-matrix-physical-migration.md#azure-vm-requirements) .



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


## <a name="next-steps"></a>Następne kroki

Zdecyduj, która metoda ma być używana do [migrowania maszyn wirtualnych VMware](server-migrate-overview.md) na platformę Azure, lub Rozpocznij Migrowanie [maszyn wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [serwerów fizycznych lub maszyn wirtualnych zwirtualizowanych lub chmurowych](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Zobacz, co jest obsługiwane

W przypadku maszyn wirtualnych VMware Migracja serwera obsługuje funkcję [bezobsługowego agenta lub migrację opartą na agencie](server-migrate-overview.md).

- **Maszyny wirtualne VMware**: Weryfikuj [wymagania dotyczące migracji i obsługa](migrate-support-matrix-vmware-migration.md) maszyn wirtualnych VMware.
- **Maszyny wirtualne funkcji Hyper-v**: Weryfikuj [wymagania dotyczące migracji i obsługa](migrate-support-matrix-hyper-v-migration.md) maszyn wirtualnych funkcji Hyper-v.
- **Maszyny fizyczne**: Sprawdź [wymagania dotyczące migracji i obsługę](migrate-support-matrix-physical-migration.md) lokalnych maszyn fizycznych i innych zwirtualizowanych serwerów. 
