---
title: Macierz pomocy technicznej dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych platformy Azure do regionu pomocniczego za pomocą usługi Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 3e66d71011937c3e22c6f1537bcd448d4aa3bbf3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605519"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Macierz pomocy technicznej dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure między regionami platformy Azure

W tym artykule podsumowano pomoc techniczną i wymagania wstępne dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure z jednego regionu platformy Azure do innego przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Obsługa metod wdrażania

**Wdrożenie** |  **Pomoc techniczna**
--- | ---
**Azure Portal** | Obsługiwane.
**PowerShell** | Obsługiwane. [Dowiedz się więcej](azure-to-azure-powershell.md)
**Interfejs API REST** | Obsługiwane.
**Interfejs wiersza polecenia** | Nie jest obecnie obsługiwana.


## <a name="resource-support"></a>Pomoc techniczna dla zasobów

**Akcja zasobu** | **Szczegóły**
--- | ---
**Przenoszenie przechowalni między grupami zasobów** | Nieobsługiwane
**Przenoszenie zasobów obliczeniowych/magazynowych/sieciowych między grupami zasobów** | Bez pomocy technicznej.<br/><br/> W przypadku przenoszenia maszyny Wirtualnej lub skojarzonych składników, takich jak magazyn/sieć po replikacji maszyny Wirtualnej, należy wyłączyć, a następnie ponownie włączyć replikację maszyny Wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do drugiej w celu odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawy usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych między regionami w obsługiwanych klastrach geograficznych (w ramach subskrypcji i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawy usługi Azure Active Directory.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Bez pomocy technicznej.

## <a name="region-support"></a>Obsługa regionów

Maszyny wirtualne można replikować i odzyskiwać między dowolnymi dwoma regionami w obrębie tego samego klastra geograficznego. Klastry geograficzne są zdefiniowane, pamiętając o opóźnieniu i suwerenności danych.


**Klaster geograficzny** | **Regiony platformy Azure**
-- | --
Ameryce | Kanada Wschodnia, Kanada Środkowe, Południowo-Środkowe STANY USA, Środkowe Stany Zjednoczone, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Środkowe stany USA, Północno-Środkowe stany USA
Europa | Wielka Brytania Zachodnia, Wielka Brytania Południowa, Europa Północna, Europa Zachodnia, Republika Południowej Afryki Zachód, Republika Południowej Afryki Północ, Norwegia Wschodnia, Norwegia Zachodnia
Azja | Indie Południowe, Indie Środkowe, Indie Zachodnie, Azja Południowo-Wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa
Australia    | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Niemcy    | Niemcy Środkowe, Niemcy Północno-Wschodnie
Chiny | Chiny Wschodnie, Chiny Północne, Chiny Północne2, Chiny Wschodnie2
Regiony ograniczone zarezerwowane do odzyskiwania po awarii w kraju |Niemcy Północ zastrzeżone dla Niemiec Środkowo-Wschodnie, Szwajcaria Zachód zarezerwowane dla Szwajcarii Północnej, Francja Południowa zarezerwowane dla France Central, ZEA Central ograniczone dla klientów ZEA North

>[!NOTE]
>
> - W przypadku **Brazylii Południowej**można replikować i przewinąć w stan fail over do tych regionów: południowo-środkowe stany USA, środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i północno-środkowe stany USA.
> - Brazylia Południowa może służyć tylko jako region źródłowy, z którego maszyny wirtualne mogą replikować przy użyciu usługi Site Recovery. Nie może działać jako region docelowy. Wynika to z problemów z opóźnieniami spowodowanymi odległościami geograficznymi. Należy zauważyć, że jeśli nie powiodą się z Brazylii Południowej jako regionu źródłowego do obiektu docelowego, powrót po awarii do Brazylii Południowej z regionu docelowego jest obsługiwany.
> - Można pracować w regionach, dla których masz odpowiedni dostęp.
> - Jeśli region, w którym chcesz utworzyć magazyn, nie jest pokazyny, upewnij się, że subskrypcja ma dostęp do tworzenia zasobów w tym regionie.
> - Jeśli nie widzisz regionu w klastrze geograficznym po włączeniu replikacji, upewnij się, że subskrypcja ma uprawnienia do tworzenia maszyn wirtualnych w tym regionie.



## <a name="cache-storage"></a>Magazyn pamięci podręcznej

W tej tabeli podsumowano obsługę konta magazynu pamięci podręcznej używanego przez usługę Site Recovery podczas replikacji.

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Konta magazynu ogólnego przeznaczenia V2 (warstwa Gorąca i chłodna) | Obsługiwane | Użycie GPv2 nie jest zalecane, ponieważ koszty transakcji dla wersji 2 są znacznie wyższe niż konta magazynu V1.
Premium Storage | Nieobsługiwane | Standardowe konta magazynu są używane do przechowywania pamięci podręcznej, aby zoptymalizować koszty.
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli używasz konta magazynu pamięci podręcznej z włączoną zaporą lub docelowego konta magazynu, upewnij się, że [zezwalasz na zaufane usługi firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Upewnij się również, że zezwalasz na dostęp do co najmniej jednej podsieci źródłowej sieci wirtualnej.


## <a name="replicated-machine-operating-systems"></a>Systemy operacyjne z repliką maszyn

Usługa Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z uruchomionymi systemami operacyjnymi wymienionymi w tej sekcji. Należy pamiętać, że jeśli już replikująca się maszyna zostanie następnie uaktualniona (lub obniżona) do innego głównego jądra, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.

### <a name="windows"></a>Windows


**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Obsługiwane dla Server Core, Server with Desktop Experience.
Windows Server 2016  | Obsługiwany serwer Core, serwer z pulpitem.
Windows Server 2012 R2 | Obsługiwane.
Windows Server 2012 | Obsługiwane.
Windows Server 2008 R2 z sp1/SP2 | Obsługiwane.<br/><br/> Od wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługi mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację stosu obsługi systemu Windows [(SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419) na komputerach z systemem Windows Server 2008 R2 z sp1/SP2.  SHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Obsługiwane.
Windows 8.1 (x64) | Obsługiwane.
Windows 8 (x64) | Obsługiwane.
Windows 7 (x64) z dodatkem SP1 | Od wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługi mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację stosu obsługi systemu Windows [(SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419) na komputerach z systemem Windows 7 z sp1.  SHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami.. Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Serwer Ubuntu 14.04 LTS | [Obsługiwane wersje jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Serwer Ubuntu 16.04 LTS | [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu używające uwierzytelniania opartego na hasłach i logowania oraz pakiet cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach podczas pracy awaryjnej (w zależności od konfiguracji cloudinit). Logowanie oparte na hasłach można ponownie włączyć na maszynie wirtualnej, resetując hasło z menu > rozwiązywanie problemów z > ustawienia (nieudanej maszyny Wirtualnej w witrynie Azure portal.
Serwer Ubuntu 18.04 LTS | [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 i 15 SP1. [(Obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | Dodatek SP3<br/><br/> Uaktualnienie maszyn replikujących z dodatku SP3 do dodatku SP4 nie jest obsługiwane. Jeśli zreplikowana maszyna została uaktualniona, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | Sp4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Uruchamianie jądra zgodnego z Red Hat lub unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Ubuntu dla maszyn wirtualnych platformy Azure

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-ogólny do 4.4.0-171-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-74-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-ogólny do 4.4.0-170-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-72-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-ogólny do 4.4.0-166-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-66-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-ogólny do 4.4.0-164-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-64-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-ogólny do 4.15.0-74-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-37-ogólny </br> 5.3.0-19-ogólny do 5.3.0-24-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1028-azure </br> 5.3.0-1007-azure do 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-ogólny do 4.15.0-72-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-37-ogólny </br> 5.3.0-19-ogólny do 5.3.0-24-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1025-azure </br> 5.3.0-1007-lazur|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-ogólny do 4.15.0-66-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-32-ogólny </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-ogólny do 4.15.0-64-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-29-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Debiana dla maszyn wirtualnych platformy Azure

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra SUSE Linux Enterprise Server 12 dla maszyn wirtualnych platformy Azure

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.32 | Obsługiwane są [wszystkie jądra SUSE 12 SP1, SP2, SP3,SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>4.12.14-6.3-lazurowe do 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.31 | Obsługiwane są [wszystkie jądra SUSE 12 SP1, SP2, SP3,SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>4.12.14-6.3-lazurowe do 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.30 | Obsługiwane są [wszystkie jądra SUSE 12 SP1, SP2, SP3,SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>4.12.14-6.3-lazurowe do 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.29 | Obsługiwane są [wszystkie jądra SUSE 12 SP1, SP2, SP3,SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>4.12.14-6.3-azure do 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra SUSE Linux Enterprise Server 15 dla maszyn wirtualnych platformy Azure

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 i 15 SP1 | 9.32 | Wszystkie zapasy SUSE 15 i 15 jądra są obsługiwane.</br></br> 4.12.14-5.5-lazurowe do 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Maszyny replikowane — system plików Linux/magazyn gościa

* Systemy plików: ext3, ext4, XFS, BTRFS
* Menedżer woluminów: LVM2
* Oprogramowanie multipath: Maper urządzeń


## <a name="replicated-machines---compute-settings"></a>Maszyny replikowane — ustawienia obliczeniowe

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Dowolny rozmiar maszyny Wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora i 1 GB pamięci RAM | Sprawdź [rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/windows/sizes.md).
Zestawy dostępności | Obsługiwane | Jeśli włączysz replikację maszyny Wirtualnej platformy Azure z opcjami domyślnymi, zestaw dostępności jest tworzony automatycznie na podstawie ustawień regionu źródłowego. Można zmodyfikować te ustawienia.
Strefy dostępności | Obsługiwane |
Korzyści z użytkowania hybrydowego (HUB) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma włączoną licencję HUB, testowa wersja awaryjna lub przełączona w stan failover maszyna wirtualna również używa licencji HUB.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy galerii platformy Azure — opublikowano przez firmę Microsoft | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Obrazy usługi Azure Gallery — opublikowano w witrynie innej firmy | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe — opublikowano przez stronę trzecią | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Maszyny wirtualne zmigrowane przy użyciu usługi Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub komputer fizyczny została zmigrowana na platformę Azure przy użyciu usługi Site Recovery, należy odinstalować starszą wersję usługi mobilności uruchomionej na komputerze i ponownie uruchomić komputer przed replikowaniem go do innego regionu platformy Azure.
Zasady RBAC | Nieobsługiwane | Zasady kontroli dostępu opartej na rolach (RBAC) na maszynach wirtualnych nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym.
Rozszerzenia | Nieobsługiwane | Rozszerzenia nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym. Musi być zainstalowany ręcznie po pracy awaryjnej.

## <a name="replicated-machines---disk-actions"></a>Maszyny replikowane — akcje dysku

**Akcja** | **Szczegóły**
-- | ---
Ponowne rozmiary dysku na zreplikowanej maszynie wirtualnej | Obsługiwane na źródłowej maszynie wirtualnej przed przejściem w stan failover. Nie ma potrzeby wyłączania/ponownego włączania replikacji.<br/><br/> Jeśli zmienisz źródłowej maszyny Wirtualnej po pracy awaryjnej, zmiany nie są przechwytywane.<br/><br/> Jeśli zmienisz rozmiar dysku na maszynie Wirtualnej platformy Azure po przejściu w stan failover, zmiany nie są przechwytywane przez usługę Site Recovery, a powrót po awarii będzie do oryginalnego rozmiaru maszyny Wirtualnej.
Dodawanie dysku do replikowanej maszyny Wirtualnej | Obsługiwane

## <a name="replicated-machines---storage"></a>Maszyny replikowane - magazynowanie

W tej tabeli podsumowano obsługę dysku systemu operacyjnego maszyny Wirtualnej platformy Azure, dysku danych i dysku tymczasowego.

- Ważne jest, aby przestrzegać limitów dysków maszyny Wirtualnej i obiektów docelowych dla maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/disk-scalability-targets.md) i [Windows,](../virtual-machines/windows/disk-scalability-targets.md) aby uniknąć problemów z wydajnością.
- Jeśli wdrożenie z ustawieniami domyślnymi spowoduje automatyczne utworzenie dysków i kont magazynu na podstawie ustawień źródła.
- Jeśli dostosujesz, upewnij się, że przestrzegasz wskazówek.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md) o dyskach maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy jest zawsze wykluczony z replikacji.<br/><br/> Nie przechowuj żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/windows/managed-disks-overview.md).
Maksymalny rozmiar dysku danych | 8192 GB dla dysków zarządzanych<br></br>4095 GB dla dysków niezarządzanych|
Minimalny rozmiar dysku danych | Brak ograniczeń dla dysków niezarządzanych. 2 GB dla dysków zarządzanych |
Maksymalna liczba dysku danych | Do 64, zgodnie z obsługą określonego rozmiaru maszyny wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/windows/sizes.md) o rozmiarach maszyn wirtualnych.
Szybkość zmiany dysku danych | Maksymalnie 10 MB/s na dysk dla magazynu w wersji premium. Maksymalna liczba 2 MB/s na dysk dla magazynu standardowego. | Jeśli średnia szybkość zmiany danych na dysku jest stale wyższa niż maksymalna, replikacja nie nadrobi zaległości.<br/><br/>  Jeśli jednak maksymalna wartość zostanie przekroczona sporadycznie, replikacja może nadrobić zaległości, ale mogą pojawić się nieco opóźnione punkty odzyskiwania.
Dysk danych — standardowe konto magazynu | Obsługiwane |
Dysk danych — konto magazynu w wersji premium | Obsługiwane | Jeśli maszyna wirtualna ma dyski rozłożone na konta magazynu premium i standardowe, można wybrać inne konto magazynu docelowego dla każdego dysku, aby upewnić się, że masz taką samą konfigurację magazynu w regionie docelowym.
Dysk zarządzany — standardowy | Obsługiwane w regionach platformy Azure, w których usługa Azure Site Recovery jest obsługiwana. |
Dysk zarządzany — premium | Obsługiwane w regionach platformy Azure, w których usługa Azure Site Recovery jest obsługiwana. |
Dysk SSD w warstwie Standardowa | Obsługiwane |
Nadmiarowość | LRS i GRS są obsługiwane.<br/><br/> ZRS nie jest obsługiwany.
Chłodne i gorące przechowywanie | Nieobsługiwane | Dyski maszyn wirtualnych nie są obsługiwane w chłodnej i gorącej pamięci masowej
Miejsca do magazynowania | Obsługiwane |
Szyfrowanie w spoczynku (SSE) | Obsługiwane | SSE jest ustawieniem domyślnym na kontach magazynu.
Szyfrowanie w spoczynku (CMK) | Obsługiwane | Zarówno oprogramowanie, jak i klucze HSM są obsługiwane dla dysków zarządzanych
Szyfrowanie dysków platformy Azure (ADE) dla systemu operacyjnego Windows | Obsługiwane dla maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezarządzanych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. |
Szyfrowanie dysków platformy Azure (ADE) dla systemu operacyjnego Linux | Obsługiwane dla maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezarządzanych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. |
Gorący dodatek    | Obsługiwane | Włączanie replikacji dysku danych dodawanych do replikowanej maszyny wirtualnej platformy Azure jest obsługiwane dla maszyn wirtualnych korzystających z dysków zarządzanych.
Dysk do usuwania na gorąco    | Nieobsługiwane | W przypadku usunięcia dysku z danymi na maszynie wirtualnej należy wyłączyć replikację i ponownie włączyć replikację dla maszyny Wirtualnej.
Wykluczanie dysku | Wsparcie. Aby skonfigurować, należy użyć programu [PowerShell.](azure-to-azure-exclude-disks.md) |    Dyski tymczasowe są domyślnie wykluczane.
Bezpośrednie miejsca do magazynowania  | Obsługiwane dla punktów odzyskiwania spójne awarie. Punkty odzyskiwania spójne aplikacji nie są obsługiwane. |
Skalowany w poziomie serwer plików  | Obsługiwane dla punktów odzyskiwania spójne awarie. Punkty odzyskiwania spójne aplikacji nie są obsługiwane. |
DRBD (drbd) | Dyski, które są częścią konfiguracji DRBD nie są obsługiwane. |
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |
Chłodna i gorąca pamięć masowa | Nieobsługiwane | Dyski maszyn wirtualnych nie są obsługiwane w chłodnym i gorącym magazynie
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli ograniczysz dostęp do sieci wirtualnej do kont magazynu, włącz [opcję Zezwalaj na zaufane usługi firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Konta magazynu ogólnego przeznaczenia V2 (warstwa Gorąca i chłodna) | Obsługiwane | Koszty transakcji znacznie wzrosną w porównaniu z kontami magazynu ogólnego przeznaczenia V1
Generacja 2 (rozruch UEFI) | Obsługiwane

>[!IMPORTANT]
> Aby uniknąć problemów z wydajnością, upewnij się, że należy przestrzegać celów skalowalności dysku maszyny Wirtualnej i wydajności dla maszyn wirtualnych [z systemem Linux](../virtual-machines/linux/disk-scalability-targets.md) lub [Windows.](../virtual-machines/windows/disk-scalability-targets.md) Jeśli używasz ustawień domyślnych, usługa Site Recovery tworzy wymagane dyski i konta magazynu na podstawie konfiguracji źródła. Jeśli dostosujesz i wybierzesz własne ustawienia, postępuj zgodnie ze skalowalnością dysku i docelowymi wydajnością dla źródłowych maszyn wirtualnych.

## <a name="limits-and-data-change-rates"></a>Limity i współczynniki zmiany danych

W poniższej tabeli podsumowano limity odzyskiwania witryny.

- Limity te są oparte na naszych testach, ale oczywiście nie obejmują wszystkich możliwych kombinacji we/wy aplikacji.
- Rzeczywiste wyniki mogą się różnić w zależności od połączenia we/wy aplikacji.
- Istnieją dwa limity do rozważenia, na zmiany danych dysku i na zmiany danych maszyny wirtualnej.

**Miejsce docelowe magazynu** | **Średni dysk źródłowy We/Wy** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="replicated-machines---networking"></a>Maszyny replikowane - sieć
**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Karta sieciowa | Maksymalna liczba obsługiwana dla określonego rozmiaru maszyny Wirtualnej platformy Azure | Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas pracy awaryjnej.<br/><br/> Liczba kart sieciowych na maszynie wirtualnej trybu failover zależy od liczby kart sieciowych w źródłowej maszynie wirtualnej, gdy replikacja została włączona. Jeśli dodasz lub usuniesz kartę sieciową po włączeniu replikacji, nie wpłynie to na liczbę kart sieciowych na zreplikowanej maszynie wirtualnej po przełączeniu w błąd. Należy również zauważyć, że kolejność kart sieciowych po pracy awaryjnej nie jest gwarantowana taka sama jak w pierwotnym zamówieniu.
Internetowy moduł równoważenia obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany moduł równoważenia obciążenia przy użyciu skryptu usługi Azure Automation w planie odzyskiwania.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany moduł równoważenia obciążenia przy użyciu skryptu usługi Azure Automation w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Skojarz istniejący publiczny adres IP z kartą sieciową. Lub utworzyć publiczny adres IP i skojarzyć go z karty sieciowej przy użyciu skryptu usługi Azure Automation w planie odzyskiwania.
NSG na nic | Obsługiwane | Skojarz sieciowej sieciowej sieciowej z kartą sieciową przy użyciu skryptu usługi Azure Automation w planie odzyskiwania.
NSG w podsieci | Obsługiwane | Skojarz sieciowej sieciowej z podsieci przy użyciu skryptu usługi Azure Automation w planie odzyskiwania.
Zastrzeżony (statyczny) adres IP | Obsługiwane | Jeśli karta sieciowa na źródłowej maszynie wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP, jest przypisana do maszyny wirtualnej awaryjnej.<br/><br/> Jeśli podsieć docelowa nie ma tego samego adresu IP, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny Wirtualnej.<br/><br/> W interfejsach obliczeniowych ustawienia ustawień i sieci sieci **replikowane** > **ustawienia i** > **Compute and Network** > sieci można również określić stały adres IP i**podsieć**.
Dynamiczny adres IP | Obsługiwane | Jeśli karta sieciowa w źródle ma dynamiczne adresowanie ADRESÓW IP, karta sieciowa na maszynie wirtualnej po awarii jest domyślnie dynamiczna.<br/><br/> W razie potrzeby można zmodyfikować ten adres do stałego adresu IP.
Wiele adresów IP | Nieobsługiwane | Po przełączeniu maszyny wirtualnej, która ma kartę sieciową z wieloma adresami IP, zachowany jest tylko podstawowy adres IP karty sieciowej w regionie źródłowym. Aby przypisać wiele adresów IP, można dodać maszyny wirtualne do [planu odzyskiwania](recovery-plan-overview.md) i dołączyć skrypt, aby przypisać dodatkowe adresy IP do planu lub można wprowadzić zmianę ręcznie lub za pomocą skryptu po przełączeniu w tryb failover.
Traffic Manager     | Obsługiwane | Można wstępnie skonfigurować usługi Traffic Manager, tak aby ruch był regularnie kierowany do punktu końcowego w regionie źródłowym oraz do punktu końcowego w regionie docelowym w przypadku pracy awaryjnej.
System DNS platformy Azure | Obsługiwane |
Niestandardowe DNS    | Obsługiwane |
Nieuwierzytywy serwer proxy | Obsługiwane | [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)
Uwierzytelniony serwer proxy | Nieobsługiwane | Jeśli maszyna wirtualna używa uwierzytelnionego serwera proxy dla łączności wychodzącej, nie można jej replikować przy użyciu usługi Azure Site Recovery.
Połączenie lokacji z lokacją sieci VPN z lokalnymi<br/><br/>(z programem ExpressRoute lub bez niego)| Obsługiwane | Upewnij się, że UDR i NSGs są skonfigurowane w taki sposób, że ruch odzyskiwania witryny nie jest kierowany do lokalnych. [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)
Połączenie sieci wirtualnej z siecią wirtualną    | Obsługiwane | [Dowiedz się więcej](site-recovery-azure-to-azure-networking-guidance.md)
Punkty końcowe usługi dla sieci wirtualnej | Obsługiwane | Jeśli ograniczasz dostęp do kont magazynu w sieci wirtualnej, upewnij się, że zaufane usługi firmy Microsoft mają dostęp do konta magazynu.
Wydajniejsze sieci | Obsługiwane | Przyspieszona sieć musi być włączona na źródłowej maszynie wirtualnej. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Następne kroki
- Przeczytaj [wskazówki dotyczące sieci do](site-recovery-azure-to-azure-networking-guidance.md) replikowania maszyn wirtualnych platformy Azure.
- Wdrażanie odzyskiwania po awarii przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
