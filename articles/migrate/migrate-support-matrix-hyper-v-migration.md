---
title: Obsługa migracji funkcji Hyper-V w programie Azure Migrate
description: Dowiedz się więcej na temat obsługi migracji funkcji Hyper-V za pomocą Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 33e34e777a78e1c609d2eacdcb501c0bce1f5c9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714924"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Macierz obsługi migracji funkcji Hyper-V

Ten artykuł zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dotyczących migrowania maszyn wirtualnych funkcji Hyper-V za [pomocą Azure Migrate: Migracja serwera.](migrate-services-overview.md#azure-migrate-server-migration-tool) Jeśli szukasz informacji na temat oceny maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure, zapoznaj się z [macierzą obsługi oceny.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>Ograniczenia migracji

Do replikacji można wybrać maksymalnie 10 maszyn wirtualnych jednocześnie. Jeśli chcesz migrować więcej maszyn, zreplikuj je w grupach po 10.


## <a name="hyper-v-host-requirements"></a>Wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. <br/>Azure Migrate replikacji (dostawca replikacji funkcji Hyper-V) jest zainstalowane na hostach funkcji Hyper-V.|
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2 z najnowszymi aktualizacjami. Należy pamiętać, że obsługiwana jest również instalacja Server Core tych systemów operacyjnych. |
| **Inne wymagania dotyczące oprogramowania** | .NET Framework 4.7 lub nowszy |
| **Dostęp do portów** |  Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej.
| **Wolne miejsce na dysku (pamięć podręczna)** |  600 GB |
| **Wolne miejsce na dysku (dysk przechowywania)** |  600 GB |


## <a name="hyper-v-vms"></a>Maszyny wirtualne funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie [systemy operacyjne Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](../virtual-machines/linux/endorsed-distros.md) obsługiwane przez platformę Azure. |
**Windows Server 2003** | W przypadku maszyn wirtualnych z systemem Windows Server 2003 należy zainstalować usługi integracji funkcji [Hyper-V](prepare-windows-server-2003-migration.md) przed migracją. | 
**Maszyny wirtualne z systemem Linux na platformie Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby można je było uruchamiać na platformie Azure.<br/><br/> W przypadku systemu Linux Azure Migrate automatycznie wprowadza zmiany dla tych systemów operacyjnych:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> — SUSE Linux Enterprise Server 12 z dodatkiem SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> W przypadku innych systemów operacyjnych wymagane [zmiany są wprowadzane](prepare-for-migration.md#verify-required-changes-before-migrating) ręcznie.
| **Wymagane zmiany dla platformy Azure** | Niektóre maszyny wirtualne mogą wymagać zmian, aby można je było uruchamiać na platformie Azure. Przed migracją należy ręcznie wprowadzić zmiany. Odpowiednie artykuły zawierają instrukcje dotyczące tego, jak to zrobić. |
| **Rozruch w systemie Linux**                 | Jeśli /boot znajduje się na partycji dedykowanej, powinien znajdować się na dysku systemu operacyjnego i nie powinny być rozłożone na wiele dysków.<br/> Jeśli /boot jest częścią partycji głównej (/), partycja "/" powinna być na dysku systemu operacyjnego i nie obejmować innych dysków. |
| **Rozruch UEFI**                  | Obsługiwane. Maszyny wirtualne oparte na interfejsie UEFI zostaną zmigrowane do maszyn wirtualnych 2. generacji na platformie Azure.  |
| **UEFI — bezpieczny rozruch**         | Nie jest obsługiwana w przypadku migracji.|
| **Rozmiar dysku**                  | 2 TB dla dysku systemu operacyjnego (rozruch z systemu BIOS), 4 TB dla dysku systemu operacyjnego (rozruch UEFI), 4 TB dla dysków danych.|
| **Numer dysku** | Maksymalnie 16 dysków na maszynę wirtualną.|
| **Zaszyfrowane dyski/woluminy**    | Nie jest obsługiwana w przypadku migracji.|
| **Dyski RDM/passthrough**      | Nie jest obsługiwana w przypadku migracji.|
| **Dysk udostępniony** | Maszyny wirtualne korzystające z dysków udostępnionych nie są obsługiwane w przypadku migracji.|
| **NFS**                        | Woluminy NFS zainstalowane jako woluminy na maszyn wirtualnych nie będą replikowane.|
| **Iscsi**                      | Maszyny wirtualne z obiektami docelowymi iSCSI nie są obsługiwane w przypadku migracji.
| **Dysk docelowy**                | Migrację na maszyny wirtualne platformy Azure można przeprowadzić tylko przy użyciu dysków zarządzanych. |
| **Protokół IPv6** | Nieobsługiwane.|
| **Kart sieciowych** | Nieobsługiwane.|
| **Azure Site Recovery** | Nie można replikować przy użyciu migracji Azure Migrate Server, jeśli maszyna wirtualna jest włączona do replikacji z Azure Site Recovery.|
| **Porty** | Połączenia wychodzące na porcie HTTPS 443 do wysyłania danych replikacji maszyny wirtualnej.|

### <a name="url-access-public-cloud"></a>Dostęp do adresu URL (chmura publiczna)

Oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebować dostępu do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
login.microsoftonline.com | Kontrola dostępu i zarządzanie tożsamościami przy użyciu usługi Active Directory.
backup.windowsazure.com | Transfer i koordynacja danych replikacji.
*.hypervrecoverymanager.windowsazure.com | Służy do zarządzania replikacją.
*.blob.core.windows.net | Przekazywanie danych na konta magazynu. 
dc.services.visualstudio.com | Przekazywanie dzienników aplikacji używanych do monitorowania wewnętrznego.
time.windows.com | Weryfikuje synchronizację czasu między czasem systemowym i globalnym.

### <a name="url-access-azure-government"></a>Dostęp do adresu URL (Azure Government)

Oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebować dostępu do tych adresów URL.

**Adres URL** | **Szczegóły**
--- | ---
login.microsoftonline.us | Kontrola dostępu i zarządzanie tożsamościami przy użyciu usługi Active Directory.
backup.windowsazure.us | Transfer i koordynacja danych replikacji.
*.hypervrecoverymanager.windowsazure.us | Służy do zarządzania replikacją.
*.blob.core.usgovcloudapi.net | Przekazywanie danych na konta magazynu.
dc.services.visualstudio.com | Przekazywanie dzienników aplikacji używanych do monitorowania wewnętrznego.
time.nist.gov | Weryfikuje synchronizację czasu między czasem systemowym i globalnym.   

>[!Note]
>
> Jeśli projekt migracji ma łączność **z** prywatnym punktem końcowym, oprogramowanie dostawcy replikacji na hostach funkcji Hyper-V będzie potrzebować dostępu do tych adresów URL w celu obsługi linków prywatnych. 
> - *.blob.core.windows.com — aby uzyskać dostęp do konta magazynu, które przechowuje replikowane dane. Jest to opcjonalne i nie jest wymagane, jeśli konto magazynu ma dołączony prywatny punkt końcowy. 
> - login.windows.net kontroli dostępu i zarządzania tożsamościami przy użyciu usługi Active Directory.

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Wszystkie lokalne maszyny wirtualne replikowane do platformy Azure muszą spełniać wymagania dotyczące maszyn wirtualnych platformy Azure podsumowane w tej tabeli.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli jest nieobsługiwane.
Liczba dysków systemu operacyjnego | 1 | Sprawdzanie kończy się niepowodzeniem, jeśli jest nieobsługiwane.
Liczba dysków danych | 16 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli jest nieobsługiwane.
Rozmiar dysku danych | Do 4095 GB | Sprawdzanie kończy się niepowodzeniem, jeśli jest nieobsługiwane.
Karty sieciowe | Obsługiwanych jest wiele kart. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
BitLocker | Nieobsługiwane. | Przed włączeniem replikacji dla maszyny należy wyłączyć funkcję BitLocker.
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa komputera musi zaczynać się i kończyć literą lub numerem. |  Zaktualizuj wartość we właściwościach maszyny w Site Recovery.
Nawiązywanie połączenia po migracji z systemem Windows | Aby nawiązać połączenie z maszynami wirtualnych platformy Azure z systemem Windows po migracji:<br/><br/> — Przed migracją włącz obsługę protokołu RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.<br/><br/> — Aby uzyskać dostęp do sieci VPN typu lokacja-lokacja, włącz protokoły RDP i zezwalaj na RDP w aplikacjach i funkcjach Zapory systemu **Windows** dozwolonych dla sieci  ->   **domenowych i** prywatnych. Ponadto sprawdź, czy zasady sieci SAN systemu operacyjnego mają ustawioną wartość **OnlineAll.** [Dowiedz się więcej](prepare-for-migration.md). |
Nawiązywanie połączenia po migracji z systemem Linux | Aby nawiązać połączenie z maszynami wirtualnych platformy Azure po migracji przy użyciu połączenia SSH:<br/><br/> — Przed migracją na maszynie lokalnej sprawdź, czy usługa Secure Shell jest ustawiona na start i czy reguły zapory zezwalają na połączenie SSH.<br/><br/> — Po migracji na maszynie wirtualnej platformy Azure zezwalaj na połączenia przychodzące do portu SSH dla reguł sieciowej grupy zabezpieczeń na maszynie wirtualnej w stanie fail over i dla podsieci platformy Azure, z którą jest połączona. Ponadto dodaj publiczny adres IP dla maszyny wirtualnej. |  

## <a name="next-steps"></a>Następne kroki

[Migrowanie maszyn wirtualnych funkcji Hyper-V do](tutorial-migrate-hyper-v.md) migracji.
