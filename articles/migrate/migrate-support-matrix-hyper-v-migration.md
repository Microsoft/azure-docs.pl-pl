---
title: Obsługa migracji funkcji Hyper-V w programie Azure Migrate
description: Dowiedz się więcej o obsłudze migracji funkcji Hyper-V za pomocą usługi Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538192"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Macierz obsługi migracji funkcji Hyper-V

W tym artykule podsumowano ustawienia pomocy technicznej i ograniczenia dotyczące migracji maszyn wirtualnych z funkcji Hyper-V za pomocą [programu Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Jeśli szukasz informacji na temat oceny maszyn wirtualnych funkcji Hyper-V w celu migracji na platformę Azure, zapoznaj się z [macierzą pomocy technicznej oceny.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>Ograniczenia migracji

Do replikacji można wybrać maksymalnie 10 maszyn wirtualnych jednocześnie. Jeśli chcesz przeprowadzić migrację większej liczby maszyn, należy replikować w grupach po 10.


## <a name="hyper-v-hosts"></a>Hosty funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. <br/>Oprogramowanie do replikacji migracji platformy Azure (dostawca replikacji funkcji Hyper-V) jest zainstalowane na hostach funkcji Hyper-V.|
| **Uprawnienia**           | Potrzebne są uprawnienia administratora do hosta funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2. |
| **Dostęp do portu** |  Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny Wirtualnej.

### <a name="url-access-public-cloud"></a>Dostęp do adresów URL (chmura publiczna)

Oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebowało dostępu do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
login.microsoftonline.com | Kontrola dostępu i zarządzanie tożsamościami przy użyciu usługi Active Directory.
backup.windowsazure.com | Transfer danych replikacji i koordynacja.
*.hypervrecoverymanager.windowsazure.com | Używany do migracji.
*.blob.core.windows.net | Przekazywanie danych do kont magazynu. 
dc.services.visualstudio.com | Przekaż dzienniki aplikacji używane do monitorowania wewnętrznego.
time.windows.com | Weryfikuje synchronizację czasu między czasem systemowym a globalnym.

### <a name="url-access-azure-government"></a>Dostęp do adresów URL (Azure Government)

Oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebowało dostępu do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
login.microsoftonline.us | Kontrola dostępu i zarządzanie tożsamościami przy użyciu usługi Active Directory.
backup.windowsazure.us | Transfer danych replikacji i koordynacja.
*.hypervrecoverymanager.windowsazure.us | Używany do migracji.
*.blob.core.usgovcloudapi.net | Przekazywanie danych do kont magazynu.
dc.services.visualstudio.com | Przekaż dzienniki aplikacji używane do monitorowania wewnętrznego.
time.nist.gov | Weryfikuje synchronizację czasu między czasem systemowym a globalnym.


## <a name="hyper-v-vms"></a>Maszyny wirtualne funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) które są obsługiwane przez platformę Azure. |
| **Wymagane zmiany na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, dzięki czemu można je uruchomić na platformie Azure. Przed migracją należy ręcznie wprowadzać zmiany. Odpowiednie artykuły zawierają instrukcje dotyczące tego, jak to zrobić. |
| **Rozruch linuksa**                 | Jeśli /boot znajduje się na partycji dedykowanej, powinien znajdować się na dysku systemu operacyjnego i nie być rozłożony na wiele dysków.<br/> Jeśli /boot jest częścią głównej partycji (/), partycja '/' powinna znajdować się na dysku systemu operacyjnego, a nie obejmować inne dyski. |
| **Rozruch UEFI**                  | Zmigrowana maszyna wirtualna na platformie Azure zostanie automatycznie przekonwertowana na maszynę wirtualną rozruchową systemu BIOS. Maszyna wirtualna powinna być uruchomiona tylko w systemie Windows Server 2012 i nowszych. Dysk systemu operacyjnego powinien mieć maksymalnie pięć partycji lub mniej, a rozmiar dysku systemu operacyjnego powinien być mniejszy niż 300 GB.
  |
| **Rozmiar dysku**                  | 2 TB dla dysku systemu operacyjnego, 4 TB dla dysków z danymi.
| **Numer dysku** | Maksymalnie 16 dysków na maszynę wirtualną.
| **Zaszyfrowane dyski/woluminy**    | Nie jest obsługiwana dla migracji. |
| **Dyski RDM/passthrough**      | Nie jest obsługiwana dla migracji. |
| **Dysk udostępniony** | Maszyny wirtualne przy użyciu dysków udostępnionych nie są obsługiwane w celu migracji.
| **NFS**                        | Woluminy nfs zainstalowane jako woluminy na maszynach wirtualnych nie będą replikowane. |
| **Iscsi**                      | Maszyny wirtualne z celami iSCSI nie są obsługiwane w przypadku migracji.
| **Dysk docelowy**                | Można przeprowadzić migrację do maszyn wirtualnych platformy Azure tylko z dyskami zarządzanymi. |
| **Protokół IPv6** | Bez pomocy technicznej.
| **Tworzenie zespołu sieciowej** | Bez pomocy technicznej.
| **Azure Site Recovery** | Nie można replikować przy użyciu migracji serwera migracji usługi Azure, jeśli maszyna wirtualna jest włączona do replikacji za pomocą usługi Azure Site Recovery.
| **Porty** | Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny Wirtualnej.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania maszyny wirtualnej platformy Azure podsumowane w tej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków systemu operacyjnego | 1 | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków danych | 16 lub mniej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku danych | Do 4095 GB | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Karty sieciowe | Obsługiwanych jest wiele kart. |
Udostępniony wirtualny dysk twardy | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Dysk FC | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
BitLocker | Bez pomocy technicznej. | Funkcja BitLocker musi być wyłączona przed włączeniem replikacji komputera.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa urządzenia musi zaczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w odzyskiwaniu witryny.
Łączenie się po migracji-Windows | Aby połączyć się z maszynami wirtualnymi platformy Azure z systemem Windows po migracji:<br/> - Przed migracją włącza RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/> Aby uzyskać dostęp do sieci VPN między lokacjami, włącz prow i zezwalaj na prow w **zaporze** -> systemu Windows**Dozwolone aplikacje i funkcje** dla sieci domen i sieci **prywatnych.** Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](prepare-for-migration.md). |
Połącz się po migracji-Linux | Aby połączyć się z maszynami wirtualnymi platformy Azure po migracji przy użyciu funkcji SSH:<br/> Przed migracją na komputerze lokalnym sprawdź, czy usługa Bezpieczne powłoki jest ustawiona na Start i czy reguły zapory zezwalają na połączenie SSH.<br/> Po przełączeniu awaryjnym na maszynie Wirtualnej platformy Azure zezwalaj na połączenia przychodzące z portem SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej po awarii i dla podsieci platformy Azure, z którą jest nawiązycona. Ponadto dodaj publiczny adres IP maszyny Wirtualnej. |  

## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych funkcji Hyper-V w](tutorial-migrate-hyper-v.md) celu migracji.
