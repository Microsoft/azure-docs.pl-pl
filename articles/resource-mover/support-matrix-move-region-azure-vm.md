---
title: Macierz obsługi przenoszenia maszyn wirtualnych platformy Azure do innego regionu za pomocą usługi Azure Resource przenoszącej
description: Zapoznaj się z obsługą przenoszenia maszyn wirtualnych platformy Azure między regionami przy użyciu funkcji przenoszenia zasobów platformy Azure.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 3022b2d4954ffaef71e17ed28dd9b6f141d4da70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980494"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Obsługa przemieszczania maszyn wirtualnych platformy Azure między regionami platformy Azure

W tym artykule podsumowano obsługę i wymagania wstępne dotyczące przenoszenia maszyn wirtualnych i powiązanych zasobów sieciowych w regionach platformy Azure przy użyciu funkcji przenoszenia zasobów.

> [!IMPORTANT]
> Usługa przenoszenia zasobów platformy Azure jest obecnie dostępna w wersji zapoznawczej.


## <a name="windows-vm-support"></a>Obsługa maszyn wirtualnych z systemem Windows

Program do przenoszenia zasobów obsługuje maszyny wirtualne platformy Azure z tymi systemami operacyjnymi Windows.

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Obsługiwane dla serwera Server Core, serwera z funkcją środowisko pulpitu.
Windows Server 2016  | Obsługiwane serwery z funkcją środowisko pulpitu.
Windows Server 2012 z dodatkiem R2 | Obsługiwane.
Windows Server 2012 | Obsługiwane.
Windows Server 2008 R2 z dodatkiem SP1/SP2 | Obsługiwane.<br/><br/> W przypadku maszyn z systemem Windows Server 2008 R2 z dodatkiem SP1/SP2 należy zainstalować aktualizację [stosu obsługi systemu Windows (SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419).  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Obsługiwane.
Windows 8.1 (x64) | Obsługiwane.
Windows 8 (x64) | Obsługiwane.
Windows 7 (x64) z dodatkiem SP1 lub nowszym | Zainstaluj aktualizację [stosu obsługi systemu Windows (SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419) na komputerach z systemem Windows 7 z dodatkiem SP1.  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, krok "Przygotuj" zakończy się niepowodzeniem. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).


## <a name="linux-vm-support"></a>Obsługa maszyn wirtualnych z systemem Linux

Przenoszenie zasobów obsługuje maszyny wirtualne platformy Azure z tymi systemami operacyjnymi Linux.

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Serwer Ubuntu 14,04 LTS | [Obsługiwane wersje jądra](#supported-ubuntu-kernel-versions)
Serwer Ubuntu 16,04 LTS | [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania oraz pakiet Cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w trybie failover (w zależności od konfiguracji usługi Cloud-init). Logowanie oparte na haśle można włączyć ponownie na maszynie wirtualnej przez zresetowanie hasła w menu Ustawienia > > Rozwiązywanie problemów z pomocą techniczną (w przypadku maszyny wirtualnej w trybie failover w Azure Portal.
Serwer Ubuntu 18,04 LTS | [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions).
Debian 7 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions).
Debian 8 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions).
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3 I SP4. [Obsługiwane wersje jądra](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 i 15 SP1. [(Obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | USUWA
SUSE Linux Enterprise Server 11 | DODATEK
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3, 4 & 5 (UEK3, UEK4, UEK5)


### <a name="supported-ubuntu-kernel-versions"></a>Obsługiwane wersje jądra Ubuntu

**Wersja** | **Wersja jądra** 
--- | --- 
14,04 LTS |  3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure 
16,04 LTS |  4.4.0-21-Generic to 4.4.0-171-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-74-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1066-Azure
18,04 LTS | 4.15.0-20-ogólny do 4.15.0-74-generyczny </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-37-Generic </br> 5.3.0-19-Generic do 5.3.0-24-Generic </br> 4.15.0-1009-Azure to 4.15.0-1037-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1028-Azure </br> 5.3.0-1007-Azure to 5.3.0-1009-Azure


### <a name="supported-debian-kernel-versions"></a>Obsługiwane wersje jądra Debian 

**Wersja** |  **Wersja jądra** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0 -0. BPO. 4-amd64 
Debian 8 |  3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 11-amd64 
Debian 8 |  3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>Obsługiwane SUSE Linux Enterprise Server 12 wersji jądra 

**Wersja** | **Wersja jądra** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.34 — Azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>Obsługiwane SUSE Linux Enterprise Server 15 wersji jądra

**Wersja** | **Wersja jądra** |
--- |  --- |
SUSE Linux Enterprise Server 15 i 15 SP1 |  Obsługiwane są wszystkie jądra systemu SUSE 15 i 15.</br></br> 4.12.14-5,5-Azure do 4.12.14-8.22 — Azure |

## <a name="supported-linux-file-systemguest-storage"></a>Obsługiwany system plików Linux/magazyn gościa

* Systemy plików: ext3, EXT4, XFS, BTRFS
* Menedżer woluminów: LVM2
* Oprogramowanie wielościeżkowe: mapowanie urządzeń


## <a name="supported-vm-compute-settings"></a>Obsługiwane ustawienia obliczeniowe maszyny wirtualnej

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Rozmiar maszyny wirtualnej platformy Azure z co najmniej dwoma rdzeniami procesora i 1 GB pamięci RAM | Sprawdź [rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/sizes-general.md).
Zestawy dostępności | Obsługiwane | Obsługiwane.
Strefy dostępności | Obsługiwane | Obsługiwane, w zależności od obsługi regionu docelowego.
Obrazy galerii platformy Azure (opublikowane przez firmę Microsoft) | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy galerii platformy Azure (opublikowane przez inną firmę)  | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe (opublikowane przez inną firmę)| Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Maszyny wirtualne używające Site Recovery | Nieobsługiwane | Przenoszenie zasobów między regionami dla maszyn wirtualnych przy użyciu Site Recovery w zapleczu. Jeśli używasz już Site Recovery, wyłącz replikację, a następnie uruchom proces przygotowywania.
Zasady kontroli RBAC platformy Azure | Nieobsługiwane | Zasady kontroli dostępu opartej na rolach (Azure RBAC) na maszynach wirtualnych nie są kopiowane do maszyny wirtualnej w regionie docelowym.
Rozszerzenia | Nieobsługiwane | Rozszerzenia nie są kopiowane do maszyny wirtualnej w regionie docelowym. Zainstaluj je ręcznie po zakończeniu przenoszenia.


## <a name="supported-vm-storage-settings"></a>Obsługiwane ustawienia magazynu maszyny wirtualnej

Ta tabela zawiera podsumowanie obsługi dysku systemu operacyjnego Azure VM, dysku danych i dysku tymczasowego. Należy przestrzegać limitów dysku maszyny wirtualnej i elementów docelowych dla [dysków zarządzanych](../virtual-machines/disks-scalability-targets.md) , aby uniknąć problemów z wydajnością.

> [!NOTE]
> Docelowy rozmiar maszyny wirtualnej powinien być równy lub większy niż źródłowa maszyna wirtualna. Parametry używane do walidacji: liczba dysków danych, liczba kart sieciowych, dostępne procesory CPU, pamięć w GB. Jeśli sn't, zostanie wystawiony błąd.


**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/managed-disks-overview.md) o dyskach maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy jest zawsze wykluczony z procesu przygotowywania.<br/><br/> Nie przechowuj żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/managed-disks-overview.md#temporary-disk).
Maksymalny rozmiar dysku danych | 8192 GB dla dysków zarządzanych
Minimalny rozmiar dysku danych |  2 GB dla dysków zarządzanych |
Maksymalna liczba dysków danych | Do 64, zgodnie z obsługą określonego rozmiaru maszyny wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/sizes.md) o rozmiarach maszyn wirtualnych.
Szybkość zmian dysku danych | Maksymalnie 10 MB/s na dysk dla usługi Premium Storage. Maksymalnie 2 MB/s na dysk w przypadku magazynu w warstwie Standardowa. | Jeśli średnia szybkość zmian danych na dysku jest ciągle wyższa niż wartość maksymalna, przygotowanie nie zostanie przełożone.<br/><br/>  Jeśli jednak maksimum zostanie przekroczone sporadycznie, przygotowanie może przechwycić, ale mogą być widoczne nieco opóźnione punkty odzyskiwania.
Dysk danych (konto magazynu w warstwie Standardowa) | Nieobsługiwane. | Zmień typ magazynu na dysk zarządzany, a następnie spróbuj przenieść maszynę wirtualną.
Dysk danych (konto magazynu Premium Storage) | Nieobsługiwane | Zmień typ magazynu na dysk zarządzany, a następnie spróbuj przenieść maszynę wirtualną.
Dysk zarządzany (warstwa standardowa) | Obsługiwane  |
Dysk zarządzany (Premium) | Obsługiwane |
Dysk SSD w warstwie Standardowa | Obsługiwane |
Generacja 2 (rozruch UEFI) | Obsługiwane
Konto magazynu diagnostyki rozruchu | Nieobsługiwane | Włącz je ponownie po przeniesieniu maszyny wirtualnej do regionu docelowego.
Maszyny wirtualne z włączonym szyfrowaniem dysków Azure | Obsługiwane | [Dowiedz się więcej](tutorial-move-region-encrypted-virtual-machines.md)
Maszyny wirtualne korzystające z szyfrowania po stronie serwera z kluczem zarządzanym przez klienta | Obsługiwane | [Dowiedz się więcej](tutorial-move-region-encrypted-virtual-machines.md)

### <a name="limits-and-data-change-rates"></a>Limity i szybkości zmian danych

W poniższej tabeli zestawiono limity, które są oparte na naszych testach. Te nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji. Rzeczywiste wyniki różnią się w zależności od kombinacji operacji we/wy aplikacji. Istnieją dwa ograniczenia, które należy wziąć pod uwagę w przypadku zmian danych na dysku i zmian danych maszyny wirtualnej.

**Miejsce docelowe magazynu** | **Średnia liczba operacji we/wy dysku źródłowego** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="supported-vm-networking-settings"></a>Obsługiwane ustawienia sieci maszyn wirtualnych

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Karta sieciowa | Obsługiwane | Określ istniejący zasób w regionie docelowym lub Utwórz nowy zasób podczas procesu przygotowywania. 
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Określ istniejący zasób w regionie docelowym lub Utwórz nowy zasób podczas procesu przygotowywania.  
Publiczny moduł równoważenia obciążenia | Obsługiwane | Określ istniejący zasób w regionie docelowym lub Utwórz nowy zasób podczas procesu przygotowywania.  
Publiczny adres IP | Obsługiwane | Określ istniejący zasób w regionie docelowym lub Utwórz nowy zasób podczas procesu przygotowywania.<br/><br/> Publiczny adres IP jest specyficzny dla regionu i nie będzie przechowywany w regionie docelowym po przeniesieniu. Należy pamiętać o modyfikacji ustawień sieci (w tym reguł równoważenia obciążenia) w lokalizacji docelowej.
Sieciowa grupa zabezpieczeń | Obsługiwane | Określ istniejący zasób w regionie docelowym lub Utwórz nowy zasób podczas procesu przygotowywania.  
Zarezerwowany (statyczny) adres IP | Obsługiwane | Obecnie nie można tego skonfigurować. Wartość domyślna wartości to wartość źródłowa. <br/><br/> Jeśli karta sieciowa na źródłowej maszynie wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP, jest on przypisany do docelowej maszyny wirtualnej.<br/><br/> Jeśli podsieć docelowa nie ma dostępnego tego samego adresu IP, inicjowanie przenoszenia dla maszyny wirtualnej zakończy się niepowodzeniem.
Dynamiczny adres IP | Obsługiwane | Obecnie nie można tego skonfigurować. Wartość domyślna wartości to wartość źródłowa.<br/><br/> Jeśli karta sieciowa w źródle ma dynamiczne adresowanie IP, karta sieciowa na docelowej maszynie wirtualnej również jest domyślnie dynamiczna.
Konfiguracje protokołu IP | Obsługiwane | Obecnie nie można tego skonfigurować. Wartość domyślna wartości to wartość źródłowa.

## <a name="outbound-access-requirements"></a>Wymagania dotyczące dostępu wychodzącego

Maszyny wirtualne platformy Azure, które chcesz przenieść, wymagają dostępu wychodzącego.


### <a name="url-access"></a>Dostęp do adresu URL

 Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

**Nazwa** | **Chmura publiczna Azure** | **Szczegóły** 
--- | --- | --- 
Storage | `*.blob.core.windows.net`  | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. 
Azure Active Directory | `login.microsoftonline.com`  | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. 
Replikacja | `*.hypervrecoverymanager.windowsazure.com` | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. 
Service Bus | `*.servicebus.windows.net` | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. 

## <a name="nsg-rules"></a>Reguły NSG
Jeśli używasz reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej, Utwórz te reguły [tagów usług](../virtual-network/service-tags-overview.md) . Każda reguła powinna zezwalać na dostęp wychodzący przy użyciu protokołu HTTPS (443).
- Utwórz regułę tagu magazynu dla regionu źródłowego.
- Utwórz regułę tagu *AzureSiteRecovery* , aby zezwolić na dostęp do usługi Site Recovery w dowolnym regionie. Ten tag ma zależności od innych tagów, dlatego należy utworzyć reguły dla nich:
    - *Usługi azureactivedirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- Zalecamy przetestowanie reguł w środowisku nieprodukcyjnym. Zapoznaj się z [przykładami](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags). 


## <a name="next-steps"></a>Następne kroki

Spróbuj [przenieść maszynę wirtualną platformy Azure](tutorial-move-region-virtual-machines.md) do innego regionu za pomocą przenoszenia zasobów.
