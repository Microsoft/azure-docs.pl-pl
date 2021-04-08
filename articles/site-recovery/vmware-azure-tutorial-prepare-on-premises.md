---
title: Przygotowywanie do odzyskiwania po awarii maszyny wirtualnej VMware z Azure Site Recovery
description: Dowiedz się, jak przygotować lokalne serwery VMware do odzyskiwania po awarii na platformie Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8fd34c8f1e3a32a8252074941a49d61aa540207
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134900"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Przygotowywanie lokalnych serwerów VMware do odzyskiwania po awarii na platformie Azure

W tym artykule opisano sposób przygotowania lokalnych serwerów VMware do odzyskiwania po awarii na platformie Azure przy użyciu usług [Azure Site Recovery](site-recovery-overview.md) . 

Z drugiego samouczka z serii dowiesz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware. W pierwszym samouczku [skonfigurujemy składniki platformy Azure, które](tutorial-prepare-azure.md) są związane z odzyskiwaniem po awarii programu VMware.


W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotuj konto na serwerze vCenter lub hoście vSphere ESXi, aby zautomatyzować odnajdywanie maszyn wirtualnych.
> * Przygotuj konto do automatycznej instalacji usługi mobilności na maszynach wirtualnych VMware.
> * Przejrzyj wymagania i pomoc techniczną dla oprogramowania VMware Server i VM.
> * Przygotuj się do łączenia się z maszynami wirtualnymi platformy Azure po przejściu

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Jeśli to możliwe używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem w sekcji jak to zrobić w spisie treści Site Recovery.

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że przygotowano platformę Azure zgodnie z opisem w [pierwszym samouczku w tej serii](tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Przygotowywanie konta do automatycznego odnajdowania

Usługa Site Recovery musi mieć dostęp do serwerów VMware w następujących celach:

- Automatyczne odnajdywanie maszyn wirtualnych. Wymagane jest co najmniej konto tylko do odczytu.
- Organizowanie replikacji, trybu failover i powrotu po awarii. Potrzebne jest konto, na którym można uruchamiać operacje, takie jak tworzenie i usuwanie dysków, a także włączanie maszyn wirtualnych.

Utwórz konto w następujący sposób:

1. Aby użyć dedykowanego konta, utwórz rolę na poziomie vCenter. Nadaj roli nazwę, taką jak **Azure_Site_Recovery**.
2. Przypisz do roli uprawnienia podsumowane w poniższej tabeli.
3. Utwórz użytkownika na serwerze vCenter lub hoście vSphere. Przypisz tę rolę temu użytkownikowi.

### <a name="vmware-account-permissions"></a>Uprawnienia konta VMware

**Zadanie** | **Rola/uprawnienia** | **Szczegóły**
--- | --- | ---
**Odnajdowanie maszyn wirtualnych** | Co najmniej użytkownik tylko do odczytu<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).
**Pełna replikacja, tryb failover i powrót po awarii** |  Utwórz rolę (Azure_Site_Recovery) z wymaganymi uprawnieniami, a następnie przypisz ją użytkownikowi lub grupie VMware<br/><br/> Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = Azure_Site_Recovery<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Przygotowywanie konta do instalacji usługi Mobility

Usługę Mobility należy zainstalować na każdej maszynie, która ma być replikowana. Usługa Site Recovery może wykonać instalację typu push tej usługi po włączeniu replikacji dla maszyny. Można też zainstalować usługę ręcznie lub przy użyciu narzędzi instalacji.

- W tym samouczku zamierzamy zainstalować usługę Mobility przy użyciu instalacji typu push.
- Ta instalacja typu push wymaga przygotowania konta, za pomocą którego usługa Site Recovery może uzyskiwać dostęp do maszyny wirtualnej. To konto określa się podczas konfigurowania odzyskiwania po awarii w konsoli platformy Azure.

Przygotuj konto w następujący sposób:

Przygotuj domenę lub konto lokalne z uprawnieniami do instalowania na maszynie wirtualnej.

- **Maszyny wirtualne Windows**: Aby zainstalować na maszynach wirtualnych z systemem Windows, jeśli nie korzystasz z konta domeny, wyłącz kontrolę dostępu użytkowników zdalnych na komputerze lokalnym. Aby to zrobić, w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** dodaj wpis DWORD **LocalAccountTokenFilterPolicy** o wartości 1.
- **Maszyny wirtualne Linux**: Aby zainstalować na maszynach wirtualnych z systemem Linux, przygotuj konto superużytkownika na serwerze źródłowym z systemem Linux.


## <a name="check-vmware-requirements"></a>Sprawdzanie wymagań dotyczących programu VMware

Upewnij się, że serwery VMware i maszyny wirtualne spełniają wymagania.

1. [Zweryfikuj](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) wymagania dotyczące serwerów VMware.
2. W przypadku systemu Linux [sprawdź](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) wymagania dotyczące systemu plików i magazynu. 
3. Sprawdź obsługę lokalnej [sieci](vmware-physical-azure-support-matrix.md#network) i [magazynu](vmware-physical-azure-support-matrix.md#storage). 
4. Sprawdź obsługę [sieci](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [magazynu](vmware-physical-azure-support-matrix.md#azure-storage) i [usług obliczeniowych](vmware-physical-azure-support-matrix.md#azure-compute) platformy Azure po przejściu do trybu failover.
5. Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać [wymagania dotyczące maszyn wirtualnych platformy Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. W przypadku maszyn wirtualnych z systemem Linux nazwa urządzenia lub nazwa punktu instalacji powinny być unikatowe. Upewnij się, że żadna para urządzeń/punktów instalacji nie ma takich samych nazw. Należy pamiętać, że w tej nazwie nie jest rozróżniana wielkość liter. Na przykład nie jest dozwolone nadanie dwóm urządzeniom dla tej samej maszyny wirtualnej nazw _urządzenie1_ i _Urządzenie1_.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Przygotowanie do połączenia z maszynami wirtualnymi Azure po przejściu do trybu failover

Po przejściu do trybu failover można połączyć się z maszynami wirtualnymi Azure z sieci lokalnej.

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Windows przy użyciu protokołu RDP po przejściu do trybu failover, wykonaj następujące czynności:

- **Dostęp do Internetu**. Przed włączeniem trybu failover włącz protokół RDP na lokalnej maszynie wirtualnej. Upewnij się, że reguły TCP i UDP zostały dodane do profilu **publicznego** oraz że w pozycji **Zapora systemu Windows** > **Dozwolone aplikacje** zezwolono na użycie protokołu RDP we wszystkich profilach.
- **Dostęp do sieci VPN typu lokacja-lokacja**:
    - Przed przejściem do trybu failover włącz protokół RDP na maszynie lokalnej.
    - Protokół RDP powinien być dozwolony w   ->  przypadku **aplikacji i funkcji dozwolonych** przez zaporę systemu Windows w przypadku sieci **z domeną i siecią prywatną** .
    - Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).
- Podczas wyzwalania trybu failover na maszynie wirtualnej nie powinno być żadnych oczekujących aktualizacji systemu Windows. Jeśli tak się stanie, nie będzie można zalogować się do maszyny wirtualnej do momentu ukończenia aktualizacji.
- Na maszynie wirtualnej platformy Azure z systemem Windows po przejściu do trybu failover sprawdź **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, upewnij się, że maszyna wirtualna jest uruchomiona, i przejrzyj te [porady dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Aby nawiązać połączenie z maszynami wirtualnymi z systemem Linux przy użyciu powłoki SSH po przejściu do trybu failover, wykonaj następujące czynności:

- Na komputerze lokalnym przed włączeniem trybu failover upewnij się, że skonfigurowano automatyczne uruchamianie usługi Secure Shell przy rozruchu systemu.
- Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
- Na maszynie wirtualnej platformy Azure po przejściu do trybu failover zezwól na połączenia przychodzące do portu SSH w regułach grupy zabezpieczeń sieci na maszynie wirtualnej w trybie failover i w podsieci platformy Azure.
- [Dodaj publiczny adres IP](./site-recovery-monitor-and-troubleshoot.md) dla maszyny wirtualnej.
- Możesz sprawdzić **diagnostykę rozruchu**, aby wyświetlić zrzut ekranu maszyny wirtualnej.


## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii
Jeśli planujesz powrót po awarii do lokacji lokalnej, istnieje kilka [wymagań wstępnych dotyczących powrotu po awarii](vmware-azure-reprotect.md#before-you-begin). Możesz je przygotować teraz, ale nie musisz. Po przełączeniu w tryb failover na platformę Azure można przygotować.



## <a name="next-steps"></a>Następne kroki

Skonfiguruj odzyskiwanie po awarii. Jeśli wykonujesz replikację wielu maszyn wirtualnych, Zaplanuj pojemność.
> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych VMware](vmware-azure-tutorial.md) 
>  [Wykonaj planowanie pojemności](site-recovery-deployment-planner.md).
