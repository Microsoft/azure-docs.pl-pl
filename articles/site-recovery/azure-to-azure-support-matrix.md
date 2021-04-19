---
title: Macierz obsługi odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu Azure Site Recovery
description: Podsumowanie obsługi odzyskiwania po awarii maszyn wirtualnych platformy Azure do regionu pomocniczego za Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.openlocfilehash: 79cde5957b2090b8337a3af424dbda7e42608398
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713340"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Macierz obsługi odzyskiwania po awarii maszyny wirtualnej platformy Azure między regionami platformy Azure

Ten artykuł zawiera podsumowanie pomocy technicznej i wymagań wstępnych w zakresie odzyskiwania po [awarii](site-recovery-overview.md) maszyn wirtualnych platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu Azure Site Recovery platformy Azure.


## <a name="deployment-method-support"></a>Obsługa metod wdrażania

**Wdrożenie** |  **Pomoc techniczna**
--- | ---
**Witryna Azure Portal** | Obsługiwane.
**Program PowerShell** | Obsługiwane. [Dowiedz się więcej](azure-to-azure-powershell.md)
**Interfejs API REST** | Obsługiwane.
**Interfejs wiersza polecenia** | Nie jest obecnie obsługiwana.


## <a name="resource-support"></a>Pomoc techniczna dla zasobów

**Akcja zasobu** | **Szczegóły**
--- | ---
**Przenoszenie magazynów między grupami zasobów** | Nieobsługiwane
**Przenoszenie zasobów obliczeniowych/magazynu/sieciowych między grupami zasobów** | Nieobsługiwane.<br/><br/> W przypadku przenoszenia maszyny wirtualnej lub skojarzonych składników, takich jak magazyn/sieć po replikacji maszyny wirtualnej, należy wyłączyć, a następnie ponownie włączyć replikację dla maszyny wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej w celu odzyskiwania po awarii** | Obsługiwane w ramach tej samej Azure Active Directory dzierżawy.
**Migrowanie maszyn wirtualnych między regionami w ramach obsługiwanych klastrów geograficznych (w ramach i między subskrypcjami)** | Obsługiwane w ramach tej samej Azure Active Directory dzierżawy.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.
**Dedykowane hosty platformy Azure** | Nieobsługiwane.

## <a name="region-support"></a>Obsługa regionów

Maszyny wirtualne można replikować i odzyskiwać między dowolnymi dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z zachowaniem opóźnień i niezależności danych.


**Klaster geograficzny** | **Regiony platformy Azure**
-- | --
Ameryce | Kanada Wschodnia, Kanada Środkowa, Południowo-środkowe stany USA, Zachodnio-środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Środkowe stany USA, Północno-środkowe stany USA
Europa | Zachodnie Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Zachodnia Republika Południowej Afryki, Północna Republika Południowej Afryki, Azja Wschodnia, Francja Środkowa, Korea Północna, Niemcy Zachodnio-środkowe
Azja | Indie Południowe, Indie Środkowe, Indie Zachodnie, Azja Południowo-Wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa
JIO | JIO Indie Zachodnie
Australia    | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | US GOV Wirginia, US GOV Iowa, US GOV Arizona, US GOV Teksas, WSCHODNIE STANY USA DOD, US DOD (region środkowy)
Niemcy    | Niemcy Środkowe, Niemcy Północno-Wschodnie
Chiny | Chiny Wschodnie, Chiny Północne, Chiny Północne2, Chiny Wschodnie 2
Brazylia | Brazylia Południowa
Regiony z ograniczeniami zarezerwowane do odzyskiwania po awarii w kraju |Brazylia Zachodnia zarezerwowana dla Brazylii Północnej, Francja Południowa zarezerwowana dla Francji Środkowej, Środkowe Zjednoczone Emiraty Zjednoczonych z ograniczeniami dla klientów z Północnej ZjednoczoneGo Emiratu Północnego, Argentyna Zachodnia dla klientów z Regionu Wschodniego, JIO Indie Środkowe dla klientów JIO Indie Zachodnie, Brazylia Południowo-Wschodnia dla Brazylii Południowej

>[!NOTE]
>
> - W **przypadku Brazylii Południowej** można replikować i przejść w stan fail over do tych regionów: Brazylia Południowo-Wschodnia, Południowo-środkowe stany USA, Zachodnio-środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2 i Północno-środkowe stany USA.
> - Brazylia Południowa może być używana tylko jako region źródłowy, z którego maszyny wirtualne mogą być replikowane przy użyciu Site Recovery. Nie może działać jako region docelowy. Jest to spowodowane problemami z opóźnieniami spowodowanymi odległościami geograficznymi. Należy pamiętać, że w przypadku pracy awaryjnej z Brazylii Południowej jako regionu źródłowego do regionu docelowego jest obsługiwany powrot po awarii do Brazylii Południowej z regionu docelowego. Brazylia Południowo-Wschodnia może być używana tylko jako region docelowy.
> - Możesz pracować w regionach, do których masz odpowiedni dostęp.
> - Jeśli region, w którym chcesz utworzyć magazyn, nie jest pokazywany, upewnij się, że subskrypcja ma dostęp do tworzenia zasobów w tym regionie.
> - Jeśli po włączeniu replikacji nie widzisz regionu w klastrze geograficznym, upewnij się, że Twoja subskrypcja ma uprawnienia do tworzenia maszyn wirtualnych w tym regionie.

## <a name="cache-storage"></a>Magazyn pamięci podręcznej

Ta tabela zawiera podsumowanie obsługi konta magazynu pamięci podręcznej używanego przez Site Recovery podczas replikacji.

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa Gorąca i Chłodna) | Obsługiwane | Użycie konta GPv2 nie jest zalecane, ponieważ koszty transakcji w wersji 2 są znacznie wyższe niż w przypadku kont magazynu w wersji 1.
Premium Storage | Nieobsługiwane | Konta magazynu w standardowych magazynach są używane do przechowywania pamięci podręcznej w celu optymalizacji kosztów.
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli używasz konta magazynu pamięci podręcznej z włączoną zaporą lub docelowego konta magazynu, upewnij się, że opcja "Zezwalaj na zaufane [usługi firmy Microsoft".](../storage/common/storage-network-security.md#exceptions)<br></br>Upewnij się również, że zezwalasz na dostęp do co najmniej jednej podsieci źródłowej sieci wirtualnej.

W poniższej tabeli wymieniono limity liczby dysków, które można replikować do pojedynczego konta magazynu.

**Typ konta magazynu**    |    **Współczynnik zmian = 4 MB/s na dysk**    |    **Współczynnik zmian = 8 MB/s na dysk**
---    |    ---    |    ---
Konto magazynu w wersji 1    |    300 dysków    |    150 dysków
Konto magazynu w wersji 2    |    750 dysków    |    375 dysków

Wraz ze wzrostem średniego współczynnika zmian na dyskach zmniejsza się liczba dysków, które może obsługiwać konto magazynu. Powyższej tabeli można użyć jako przewodnika do podejmowania decyzji dotyczących liczby kont magazynu, które należy aprowizować.

Należy pamiętać, że powyższe limity są specyficzne dla platformy Azure i scenariuszy strefowego testowania po awarii. 

## <a name="replicated-machine-operating-systems"></a>Zreplikowane systemy operacyjne maszyn

Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z systemami operacyjnymi wymienionymi w tej sekcji. Należy pamiętać, że jeśli maszyna replikowana jest następnie uaktualniana (lub obniżona) do innego ważnego jądra, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.

### <a name="windows"></a>Windows


**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Obsługiwane w przypadku server core, serwer ze środowiskom pulpitu.
Windows Server 2016  | Obsługiwana wersja Server Core, serwer ze środowiskom pulpitu.
Windows Server 2012 z dodatkiem R2 | Obsługiwane.
Windows Server 2012 | Obsługiwane.
Windows Server 2008 R2 z dodatkiem SP1/SP2 | Obsługiwane.<br/><br/> W wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługa mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację stosu obsługi systemu Windows [(SSU)](https://support.microsoft.com/help/4490628) i aktualizację [SHA-2](https://support.microsoft.com/help/4474419) na maszynach z systemem Windows Server 2008 R2 z dodatkiem SP1/SP2.  Funkcja SHA-1 nie jest obsługiwana od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zainstaluje/uaktualni zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu SHA-2 i wymaganiach](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Obsługiwane.
Windows 8.1 (x64) | Obsługiwane.
Windows 8 (x64) | Obsługiwane.
Windows 7 (x64) z dodatkiem SP1 lub jego wersja | W wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługa mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację stosu obsługi systemu Windows [(SSU)](https://support.microsoft.com/help/4490628) i aktualizację [SHA-2](https://support.microsoft.com/help/4474419) na maszynach z systemem Windows 7 z dodatkiem SP1.  Funkcja SHA-1 nie jest obsługiwana od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zainstaluje/uaktualni zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu SHA-2 i wymaganiach.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [7.8,](https://support.microsoft.com/help/4564347/) [7.9,](https://support.microsoft.com/help/4578241/) [8.0,](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)8.1, [8.2,](https://support.microsoft.com/help/4570609/) [8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10 </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, [7.8,](https://support.microsoft.com/help/4564347/) [7.9,](https://support.microsoft.com/help/4578241/)wersja wstępna GA, wersja 7.9 GA jest obsługiwana z poprawki poprawki 9.37** </br> 8.0, 8.1, [8.2,](https://support.microsoft.com/en-us/help/4570609) [8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | Obejmuje obsługę wszystkich 14.04. *wersje x;* [Obsługiwane wersje jądra;](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) 
Ubuntu 16.04 LTS Server | Obejmuje obsługę wszystkich 16.04. *x* wersji; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania oraz pakiet cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w przypadku trybu failover (w zależności od konfiguracji cloudinit). Logowanie oparte na hasłach można ponownie włączyć na maszynie wirtualnej, resetując hasło z menu Ustawienia usługi > pomocy technicznej usługi > (maszyny wirtualnej w trybie fail over w Azure Portal).
Ubuntu 18.04 LTS Server | Obejmuje obsługę wszystkich 18.04. *x* wersji; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania oraz pakiet cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w przypadku trybu failover (w zależności od konfiguracji cloudinit). Logowanie oparte na hasłach można ponownie włączyć na maszynie wirtualnej, resetując hasło z menu Ustawienia usługi > pomocy technicznej usługi > (maszyny wirtualnej w trybie fail over w Azure Portal).
Serwer Ubuntu 20.04 LTS | Obejmuje obsługę wszystkich 20.04. *x* wersji; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Obejmuje obsługę wszystkich 7. *Wersje x* [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Obejmuje obsługę wszystkich 8. *Wersje x* [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Obejmuje obsługę od 9.1 do 9.13. Debian 9.0 nie jest obsługiwany. [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Uaktualnianie replikujących maszyn z dodatkiem SP3 do SP4 nie jest obsługiwane. Jeśli replikowana maszyna została uaktualniona, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [7.8,](https://support.microsoft.com/help/4573888/) [7.9,](https://support.microsoft.com/help/4597409) [8.0,](https://support.microsoft.com/help/4573888/) [8.1](https://support.microsoft.com/help/4573888/) (uruchamianie jądra zgodnego z systemem Red Hat lub nierozdzielnego jądra przedsiębiorstwa w wersji 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1 (uruchomione na wszystkich jądrach UEK i jądrze RedHat <= 3.10.0-1062.* są obsługiwane w [wersji 9.35.](https://support.microsoft.com/help/4573888/) Obsługa pozostałych jąder RedHat jest dostępna w wersji [9.36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> W przypadku wersji systemu Linux Azure Site Recovery nie obsługuje niestandardowych obrazów systemu operacyjnego. Obsługiwane są tylko jądra zapasów, które są częścią wydania/aktualizacji wersji pomocniczej dystrybucji.

**Uwaga: aby obsługiwać najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery poprawki poprawki dla najnowszej wersji agenta mobilności. Ta poprawka jest wycofywała się między dwiema wersjami głównymi. Aby zaktualizować agenta mobilności do najnowszej wersji (w tym poprawki poprawki), wykonaj kroki wymienione w [tym artykule.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ta poprawka jest obecnie stosowana dla agentów mobilności używanych w scenariuszu azure-Azure DR.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Ubuntu dla maszyn wirtualnych platformy Azure

**Wersja** | **usługa mobilności wersji** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | [9,37,](https://support.microsoft.com/help/4582666/) [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-generic do 3.13.0-170-generic,<br/>3.16.0-25-generic do 3.16.0-77-generic,<br/>od 3.19.0 do 18-generic do 3.19.0-80-generic,<br/>od 4.2.0 do 18-generic do 4.2.0-42-generic,<br/>4.4.0-21-generic do 4.4.0-148-generic,<br/>Od 4.15.0-1023-azure do 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | od 4.4.0 do 21-generic do 4.4.0-201-generic,<br/>od 4.8.0 do 34-generic do 4.8.0-58-generic,<br/>4.10.0-14-generic do 4.10.0-42-generic,<br/>4.11.0-13-generic do 4.11.0-14-generic,<br/>4.13.0-16-generic do 4.13.0-45-generic,<br/>Od 4.15.0-13-generic do 4.15.0-133-generic<br/>od 4.11.0-1009-azure do 4.11.0-1016-azure,<br/>Od 4.13.0-1005-azure do 4.13.0-1018-azure <br/>Od 4.15.0-1012-azure do 4.15.0-1106-azure <br/> 4.4.0-203-generic, 4.4.0-204-generic, 4.4.0-206-generic, 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 4.15.0-1108-azure, 4.15.0-1109-azure, 4.15.0-1110-azure, 4.15.0-1111-azure do 9.41 poprawka poprawek**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | od 4.4.0 do 21-generic do 4.4.0-197-generic,<br/>4.8.0-34-generic do 4.8.0-58-generic,<br/>4.10.0-14-generic do 4.10.0-42-generic,<br/>4.11.0-13-generic do 4.11.0-14-generic,<br/>4.13.0-16-generic do 4.13.0-45-generic,<br/>Od 4.15.0-13-generic do 4.15.0-128-generic<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>Od 4.13.0-1005-azure do 4.13.0-1018-azure <br/>Od 4.15.0-1012-azure do 4.15.0-1102-azure </br> 4.15.0-132-generic, 4.4.0-200-generic, 4.15.0-1106-azure, 4.15.0-133-generic, 4.4.0-201-generic do 9.40 hot fix patch**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic do 4.4.0-194-generic,<br/>4.8.0-34-generic do 4.8.0-58-generic,<br/>4.10.0-14-generic do 4.10.0-42-generic,<br/>4.11.0-13-generic do 4.11.0-14-generic,<br/>4.13.0-16-generic do 4.13.0-45-generic,<br/>Od 4.15.0-13-generic do 4.15.0-123-generic<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>Od 4.13.0-1005-azure do 4.13.0-1018-azure <br/>Od 4.15.0-1012-azure do 4.15.0-1098-azure </br> 4.4.0-197-generic, 4.15.0-126-generic, 4.15.0-128-generic, 4.15.0-1100-azure, 4.15.0-1102-azure do 9.39 hot fix patch**|
16.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | od 4.4.0 do 21-generic do 4.4.0-190-generic,<br/>4.8.0-34-generic do 4.8.0-58-generic,<br/>4.10.0-14-generic do 4.10.0-42-generic,<br/>4.11.0-13-generic do 4.11.0-14-generic,<br/>4.13.0-16-generic do 4.13.0-45-generic,<br/>Od 4.15.0-13-generic do 4.15.0-118-generic<br/>od 4.11.0-1009-azure do 4.11.0-1016-azure,<br/>Od 4.13.0-1005-azure do 4.13.0-1018-azure <br/>Od 4.15.0-1012-azure do 4.15.0-1096-azure </br> 4.4.0-193-generic, 4.15.0-120-generic, 4.15.0-122-generic, 4.15.0-1098-azure do 9.38 hot fix patch**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic do 4.4.0-189-generic,<br/>4.8.0-34-generic do 4.8.0-58-generic,<br/>4.10.0-14-generic do 4.10.0-42-generic,<br/>4.11.0-13-generic do 4.11.0-14-generic,<br/>4.13.0-16-generic do 4.13.0-45-generic,<br/>Od 4.15.0-13-generic do 4.15.0-115-generic<br/>od 4.11.0-1009-azure do 4.11.0-1016-azure,<br/>Od 4.13.0-1005-azure do 4.13.0-1018-azure <br/>Od 4.15.0-1012-azure do 4.15.0-1093-azure </br> 4.4.0-190-generic, 4.15.0-117-generic, 4.15.0-118-generic, 4.15.0-1095-azure, 4.15.0-1096-azure do 9.37 hot fix patch**|
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Od 4.15.0-20-generic do 4.15.0-135-generic </br> Od 4.18.0-13-generic do 4.18.0-25-generic </br> Od 5.0.0-15-generic do 5.0.0-65-generic </br> Od 5.3.0-19-generic do 5.3.0-70-generic </br> Od 5.4.0-37-generic do 5.4.0-59-generic</br> Od 5.4.0-60-generic do 5.4.0-65-generic </br> Od 4.15.0-1009-azure do 4.15.0-1106-azure </br> Od 4.18.0-1006-azure do 4.18.0-1025-azure </br> Od 5.0.0-1012-azure do 5.0.0-1036-azure </br> Od 5.3.0-1007-azure do 5.3.0-1035-azure </br> Od 5.4.0-1020-azure do 5.4.0-1039-azure </br> 4.15.0-136-generic, 4.15.0-137-generic, 4.15.0-139-generic, 4.15.0-140-generic, 5.3.0-72-generic, 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 4.15.0-1108-azure, 4.15.0-1111-azure, 5.4.0-1040-azure, 5.4.0-1041 -azure, 5.4.0-1043-azure, 4.15.0-1109-azure, 4.15.0-1110-azure do 9.41 hot fix patch**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Od 4.15.0-20-generic do 4.15.0-129-generic </br> Od 4.18.0-13-generic do 4.18.0-25-generic </br> Od 5.0.0 do 15-generic do 5.0.0-63-generic </br> Od 5.3.0 do 19-generic do 5.3.0-69-generic </br> Od 5.4.0-37-generic do 5.4.0-59-generic</br> Od 4.15.0-1009-azure do 4.15.0-1103-azure </br> Od 4.18.0-1006-azure do 4.18.0-1025-azure </br> Od 5.0.0-1012-azure do 5.0.0-1036-azure </br> Od 5.3.0-1007-azure do 5.3.0-1035-azure </br> Od 5.4.0-1020-azure do 5.4.0-1035-azure </br> 4.15.0-1104-azure, 4.15.0-130-generic, 4.15.0-132-generic, 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 4.15.0-1106-azure, 4.15.0-134-generic, 4.15.0-135-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic do 9.40 hot fix patch**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | Od 4.15.0-20-generic do 4.15.0-123-generic </br> Od 4.18.0-13-generic do 4.18.0-25-generic </br> Od 5.0.0 do 15-generic do 5.0.0-63-generic </br> Od 5.3.0 do 19-generic do 5.3.0-69-generic </br> Od 5.4.0-37-generic do 5.4.0-53-generic</br> Od 4.15.0-1009-azure do 4.15.0-1099-azure </br> Od 4.18.0-1006-azure do 4.18.0-1025-azure </br> Od 5.0.0-1012-azure do 5.0.0-1036-azure </br> Od 5.3.0-1007-azure do 5.3.0-1035-azure </br> Od 5.4.0-1020-azure do 5.4.0-1031-azure </br> 4.15.0-124-generic, 5.4.0-54-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 4.15.0-1100-azure, 4.15.0-126-generic, 4.15.0-128-generic, 5.4.0-58-generic, 4.15.0-1102-azure, 5.4.0-1034-azure do 9.39 hot fix patch**|
18.04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | Od 4.15.0-20-generic do 4.15.0-118-generic </br> Od 4.18.0-13-generic do 4.18.0-25-generic </br> Od 5.0.0-15-generic do 5.0.0-61-generic </br> Od 5.3.0-19-generic do 5.3.0-67-generic </br> Od 5.4.0-37-generic do 5.4.0-48-generic</br> Od 4.15.0-1009-azure do 4.15.0-1096-azure </br> Od 4.18.0-1006-azure do 4.18.0-1025-azure </br> Od 5.0.0-1012-azure do 5.0.0-1036-azure </br> Od 5.3.0-1007-azure do 5.3.0-1035-azure </br> Od 5.4.0-1020-azure do 5.4.0-1026-azure </br> 4.15.0-121-generic, 4.15.0-122-generic, 5.0.0-62-generic, 5.3.0-68-generic, 5.4.0-51-generic, 5.4.0-52-generic, 4.15.0-1099-azure, 5.4.0-1031-azure through 9.38 hot fix patch**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | Od 4.15.0-20-generic do 4.15.0-115-generic </br> Od 4.18.0-13-generic do 4.18.0-25-generic </br> Od 5.0.0-15-generic do 5.0.0-60-generic </br> Od 5.3.0 do 19-generic do 5.3.0-66-generic </br> Od 5.4.0-37-generic do 5.4.0-45-generic</br> Od 4.15.0-1009-azure do 4.15.0-1093-azure </br> Od 4.18.0-1006-azure do 4.18.0-1025-azure </br> Od 5.0.0-1012-azure do 5.0.0-1036-azure </br> Od 5.3.0-1007-azure do 5.3.0-1035-azure </br> Od 5.4.0-1020-azure do 5.4.0-1023-azure</br> 4.15.0-117-generic, 4.15.0-118-generic, 5.0.0-61-generic, 5.3.0-67-generic, 5.4.0-47-generic, 5.4.0-48-generic, 4.15.0-1095-azure, 4.15.0-1096-azure, 5.4.0-1025-azure, 5.4.0-1026-azure do 9.37 Hot fix patch**|
|||
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| Od 5.4.0 do 26-generic do 5.4.0-65 </br> -generic 5.4.0-1010-azure do 5.4.0-1039-azure </br> Od 5.8.0 do 29-generic do 5.8.0-43-generic </br> 5.4.0-66-generic, 5.4.0-67-generic, 5.4.0-70-generic, 5.8.0-44-generic, 5.8.0-45-generic, 5.8.0-48-generic, 5.4.0-1040-azure, 5.4.0-1041-azure, 5.4.0-1043-azure through 9.41 Hot fix patch**|
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| Od 5.4.0 do 26-generic do 5.4.0-59 </br> -generic 5.4.0-1010-azure do 5.4.0-1035-azure </br> Od 5.8.0 do 29-generic do 5.8.0-34-generic </br> 5.4.0-1036-azure, 5.4.0-60-generic, 5.4.0-62-generic, 5.8.0-36-generic, 5.8.0-38-generic, 5.4.0-1039-azure, 5.4.0-64-generic, 5.4.0-65-generic, 5.8.0-40-generic, 5.8.0-41-generic do 9.40 hot fix patch**|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic do 5.4.0-53 </br> -generic 5.4.0-1010-azure do 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic do 5.4.0-53 </br> -generic 5.4.0-1010-azure do 5.4.0-1031-azure </br> 5.4.0-54-generic, 5.8.0-29-generic, 5.4.0-1032-azure, 5.4.0-56-generic, 5.8.0-31-generic, 5.8.0-33-generic, 5.4.0-58-generic, 5.4.0-1034-azure through 9.39 hot fix patch**
20.04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic do 5.4.0-48 </br> -generic 5.4.0-1010-azure do 5.4.0-1026-azure </br> 5.4.0-51-generic, 5.4.0-52-generic, 5.8.0-23-generic, 5.8.0-25-generic, 5.4.0-1031-azure do 9.38 hot fix patch**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | Od 5.4.0-26-generic do 5.4.0-45 </br> -generic 5.4.0-1010-azure to 5.4.0-1023-azure </br> 5.4.0-47-generic, 5.4.0-48-generic, 5.4.0-1025-azure, 5.4.0-1026-azure through 9.37 hot fix patch**

**Uwaga: aby obsługiwać najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery poprawki poprawki dla najnowszej wersji agenta mobilności. Ta poprawka jest dostępna w dwóch wersjach głównych. Aby zaktualizować agenta mobilności do najnowszej wersji (w tym poprawki hot fix), wykonaj kroki opisane w [tym artykule.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ta poprawka jest obecnie stosowana dla agentów mobilności używanych w scenariuszu Azure–Azure DR.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Debian dla maszyn wirtualnych platformy Azure

**Wersja** | **usługa mobilności wersji** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9,37,](https://support.microsoft.com/help/4582666/) [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| Od 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,37,](https://support.microsoft.com/help/4582666/) [9,38,](https://support.microsoft.com/help/4590304/) [9,39,](https://support.microsoft.com/help/4597409/) [9,40,](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Od 3.16.0-4-amd64 do 3.16.0-11-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Od 4.9.0-1-amd64 do 4.9.0-14-amd64 </br> Od 4.19.0-0.bpo.1-amd64 do 4.19.0-0.bpo.14-amd64 </br> Od 4.19.0-0.bpo.1-cloud-amd64 do 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64, 4.19.0-0.bpo.16-amd64, 4.19.0-0.bpo.16-cloud-amd64 do 9.41 poprawki hot fix**
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Od 4.9.0-1-amd64 do 4.9.0-14-amd64 </br> Od 4.19.0-0.bpo.1-amd64 do 4.19.0-0.bpo.13-amd64 </br> Od 4.19.0-0.bpo.1-cloud-amd64 do 4.19.0-0.bpo.13-cloud-amd64 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | Od 4.9.0-1-amd64 do 4.9.0-14-amd64 </br> Od 4.19.0-0.bpo.1-amd64 do 4.19.0-0.bpo.12-amd64 </br> Od 4.19.0-0.bpo.1-cloud-amd64 do 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64, 4.19.0-0.bpo.13-cloud-amd64 do 9.39 poprawki poprawek**</br> 
Debian 9.1 | [9,38](https://support.microsoft.com/help/4590304/) | Od 4.9.0-1-amd64 do 4.9.0-13-amd64 </br> Od 4.19.0-0.bpo.1-amd64 do 4.19.0-0.bpo.11-amd64 </br> Od 4.19.0-0.bpo.1-cloud-amd64 do 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0-0.bpo.12-amd64, 4.19.0-0.bpo.12-cloud-amd64 do 9.38 hot fix patch**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | Od 4.9.0-3-amd64 do 4.9.0-13-amd64, Od 4.19.0-0.bpo.6-amd64 do 4.19.0-0.bpo.10-amd64, 4.19.0-0.bpo.6-cloud-amd64 do 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Od 4.19.0-5-amd64 do 4.19.0-14-amd64 </br> Od 4.19.0-6-cloud-amd64 do 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64, 4.19.0-16-amd64, 4.19.0-16-cloud-amd64 do 9.41 poprawki hot fix**
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Od 4.19.0-5-amd64 do 4.19.0-13-amd64 </br> Od 4.19.0-6-cloud-amd64 do 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

**Uwaga: aby obsługiwać najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery poprawkę poprawki dla najnowszej wersji agenta mobilności. Ta poprawka jest dostępna w dwóch wersjach głównych. Aby zaktualizować agenta mobilności do najnowszej wersji (w tym poprawki hot fix), wykonaj kroki opisane w [tym artykule.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ta poprawka jest obecnie stosowana dla agentów mobilności używanych w scenariuszu Azure–Azure DR.

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane SUSE Linux Enterprise Server 12 wersji jądra dla maszyn wirtualnych platformy Azure

**Wersja** | **usługa mobilności wersji** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Obsługiwane [są wszystkie jądra SUSE 12 SP1, SP2, SP3, SP4 i SP5.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> od 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>Od 4.12.14-6.3-azure do 4.12.14-6.43-azure </br> Od 4.12.14-16.7-azure do 4.12.14-16.44-azure </br> 4.12.14-16.47-azure do 9.41 poprawka poprawna**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Obsługiwane są wszystkie jądra [SUSE 12 SP1, SP2, SP3, SP4 i SP5.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>Od 4.12.14-6.3-azure do 4.12.14-6.43-azure </br> Od 4.12.14-16.7-azure do 4.12.14-16.38-azure </br> 4.12.14-16.41-azure do 9.40 hot fix patch**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.39](https://support.microsoft.com/help/4597409/) | Obsługiwane są wszystkie jądra [SUSE 12 SP1, SP2, SP3, SP4 i SP5.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>4.12.14-6.3-azure do 4.12.14-6.43-azure </br> 4.12.14-16.7-azure do 4.12.14-16.34-azure </br> Od 4.12.14-16.38-azure do 9.39 poprawki poprawek**|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Obsługiwane są wszystkie jądra [SUSE 12 SP1, SP2, SP3, SP4 i SP5.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>Od 4.12.14-6.3-azure do 4.12.14-6.43-azure </br> Od 4.12.14-16.7-azure do 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.37](https://support.microsoft.com/help/4582666/) | Obsługiwane [są wszystkie jądra SUSE 12 SP1, SP2, SP3, SP4 i SP5.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> od 4.4.138-4.7-azure do 4.4.180-4.31-azure,</br>Od 4.12.14-6.3-azure do 4.12.14-6.43-azure </br> Od 4.12.14-16.7-azure do 4.12.14-16.22-azure </br> 4.12.14-16.25-azure, 4.12.14-16.28-azure do 9.37 hot fix patch**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane SUSE Linux Enterprise Server 15 wersji jądra dla maszyn wirtualnych platformy Azure

**Wersja** | **usługa mobilności wersji** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Domyślnie obsługiwane są [wszystkie jądra SUSE 15, SP1 i SP2.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> Od 4.12.14-5.5-azure do 4.12.14-5.47-azure </br></br> Od 4.12.14-8.5-azure do 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> Od 5.3.18-18.5-azure do 5.3.18-18.35-azure </br> Od 5.3.18-18.38-azure do 9.41 poprawki hot fix**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Domyślnie obsługiwane są [wszystkie jądra SUSE 15, SP1 i SP2.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> Od 4.12.14-5.5-azure do 4.12.14-5.47-azure </br></br> Od 4.12.14-8.5-azure do 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> Od 5.3.18-18.5-azure do 5.3.18-18.29-azure </br> 5.3.18-18.32-azure, 4.12.14-8.58-azure do 9.40 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | Domyślnie obsługiwane są wszystkie [jądra SUSE 15, SP1 i SP2.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure do 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure do 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> Od 5.3.18-18.5-azure do 5.3.18-18.21-azure </br> 4.12.14-8.52-azure, 5.3.18-18.24-azure, 4.12.14-8.55-azure, 5.3.18-18.29-azure do 9.39 hot fix patch**
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Domyślnie obsługiwane są [wszystkie jądra SUSE 15, SP1 i SP2.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> 4.12.14-5.5-azure do 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure do 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> Od 5.3.18-18.5-azure do 5.3.18-18.18-azure </br> 4.12.14-8.47-azure, 5.3.18-18.21-azure do 9.38 hot fix patch**
SUSE Linux Enterprise Server 15 i 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | Domyślnie obsługiwane są [wszystkie jądra SUSE 15, SP1 i SP2.](https://www.suse.com/support/kb/doc/?id=000019587)</br></br> Od 4.12.14-5.5-azure do 4.12.14-5.47-azure </br></br> Od 4.12.14-8.5-azure do 4.12.14-8.38-azure </br> 4.12.14-8.41-azure, 4.12.14-8.44-azure do 9.37 hot fix patch**

**Uwaga: aby obsługiwać najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery poprawkę poprawki dla najnowszej wersji agenta mobilności. Ta poprawka jest dostępna w dwóch wersjach głównych. Aby zaktualizować agenta mobilności do najnowszej wersji (w tym poprawki hot fix), wykonaj kroki opisane w [tym artykule.](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure) Ta poprawka jest obecnie stosowana dla agentów mobilności używanych w scenariuszu Azure–Azure DR.

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikowane maszyny — system plików z systemem Linux/magazyn gościa

* Systemy plików: ext3, ext4, XFS, BTRFS
* Menedżer woluminów: LVM2

> [!NOTE]
> Oprogramowanie wielościeżowe nie jest obsługiwane.


## <a name="replicated-machines---compute-settings"></a>Replikowane maszyny — ustawienia obliczeniowe

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Dowolny rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora CPU i 1 GB pamięci RAM | Sprawdź [rozmiary maszyn wirtualnych platformy Azure.](../virtual-machines/sizes.md)
Zestawy dostępności | Obsługiwane | Jeśli włączysz replikację dla maszyny wirtualnej platformy Azure przy użyciu opcji domyślnych, zestaw dostępności zostanie utworzony automatycznie na podstawie ustawień regionu źródłowego. Możesz zmodyfikować te ustawienia.
Strefy dostępności | Obsługiwane |
Korzyść użycia hybrydowego (HUB) | Obsługiwane | Jeśli źródłową maszynę wirtualną ma włączoną licencję CENTRUM, test pracy w trybie failover lub maszyna wirtualna w trybie failover również używa licencji HUB.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy galerii platformy Azure — opublikowana przez firmę Microsoft | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Obrazy z galerii platformy Azure — opublikowane przez inne firmy | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe — opublikowane przez inne firmy | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna działa w obsługiwanym systemie operacyjnym.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub maszyna fizyczna została zmigrowana na platformę Azure przy użyciu programu Site Recovery, należy odinstalować starszą wersję programu usługa mobilności uruchomiono na maszynie, a następnie ponownie uruchomić maszynę przed jej replikacją do innego regionu świadczenia usługi Azure.
Zasady RBAC platformy Azure | Nieobsługiwane | Zasady kontroli dostępu opartej na rolach (RBAC) platformy Azure na maszynach wirtualnych nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym.
Rozszerzenia | Nieobsługiwane | Rozszerzenia nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym. Należy go zainstalować ręcznie po zakończeniu pracy w trybu failover.
Grupy umieszczania w pobliżu | Obsługiwane | Maszyny wirtualne znajdujące się wewnątrz grupy umieszczania w pobliżu mogą być chronione przy użyciu Site Recovery.
Tagi  | Obsługiwane | Tagi wygenerowane przez użytkownika stosowane na źródłowych maszynach wirtualnych są przenoszone do docelowych maszyn wirtualnych po testowym pracy w trybu failover lub pracy w trybu failover. Tagi na maszynach wirtualnych są replikowane co 24 godziny, o ile maszyny wirtualne znajdują się w regionie docelowym.


## <a name="replicated-machines---disk-actions"></a>Replikowane maszyny — akcje dysku

**Akcja** | **Szczegóły**
-- | ---
Zmienianie rozmiaru dysku na replikowanej maszynie wirtualnej | Obsługiwane na źródłowej maszynie wirtualnej przed rozpoczęciem pracy w trybu failover. Nie ma potrzeby wyłączania/ponownego włączania replikacji.<br/><br/> Jeśli zmienisz źródłową maszynę wirtualną po zakończeniu pracy w trybie failover, zmiany nie zostaną przechwycone.<br/><br/> Jeśli zmienisz rozmiar dysku na maszynie wirtualnej platformy Azure po zakończeniu pracy w trybie failover, zmiany nie zostaną przechwycone przez Site Recovery, a powrotu po awarii do oryginalnego rozmiaru maszyny wirtualnej.
Dodawanie dysku do replikowanej maszyny wirtualnej | Obsługiwane
Zmiany w trybie offline na dyskach chronionych | Odłączanie dysków i modyfikowanie ich w trybie offline wymaga wyzwolenia pełnej ponownej synchronizacji.

## <a name="replicated-machines---storage"></a>Replikowane maszyny — magazyn

W tej tabeli podsumowano obsługę dysku systemu operacyjnego, dysku danych i dysku tymczasowego maszyny wirtualnej platformy Azure.

- Ważne jest, aby obserwować limity dysków maszyn wirtualnych i cele dla dysków zarządzanych, [aby](../virtual-machines/disks-scalability-targets.md) uniknąć problemów z wydajnością.
- W przypadku wdrożenia z ustawieniami domyślnymi program Site Recovery automatycznie tworzy dyski i konta magazynu na podstawie ustawień źródłowych.
- W przypadku dostosowywania upewnij się, że przestrzegasz wytycznych.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej o](../virtual-machines/managed-disks-overview.md) dyskach maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy jest zawsze wykluczany z replikacji.<br/><br/> Nie przechowuj żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/managed-disks-overview.md).
Maksymalny rozmiar dysku danych | 32 TB dla dysków zarządzanych<br></br>4 TB w przypadku dysków niezamaniowych|
Minimalny rozmiar dysku danych | Brak ograniczeń dotyczących dysków niezamaniowych. 2 GB dla dysków zarządzanych |
Maksymalna liczba dysków danych | Do 64 zgodnie z obsługą określonego rozmiaru maszyny wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/sizes.md) o rozmiarach maszyn wirtualnych.
Szybkość zmian dysku danych | Maksymalnie 20 MB/s na dysk w przypadku magazynu w chmurze Premium. Maksymalnie 2 MB/s na dysk dla magazynu w chmurze Standardowa. | Jeśli średni szybkość zmian danych na dysku jest stale większa niż wartość maksymalna, replikacja nie nadrobić zaległości.<br/><br/>  Jeśli jednak wartość maksymalna zostanie przekroczona sporadycznie, replikacja może nadrobić zaległości, ale punkty odzyskiwania mogą być nieco opóźnione.
Dysk danych — standardowe konto magazynu | Obsługiwane |
Dysk danych — konto magazynu w chmurze Premium | Obsługiwane | Jeśli maszyna wirtualna ma dyski rozłożone na konta magazynu w warstwie Premium i Standardowa, możesz wybrać inne docelowe konto magazynu dla każdego dysku, aby upewnić się, że w regionie docelowym jest taka sama konfiguracja magazynu.
Dysk zarządzany — standardowa | Obsługiwane w regionach świadczenia usługi Azure, Azure Site Recovery są obsługiwane. |
Dysk zarządzany — premium | Obsługiwane w regionach świadczenia usługi Azure, Azure Site Recovery są obsługiwane. |
Dysk SSD w warstwie Standardowa | Obsługiwane |
Nadmiarowość | Obsługiwane są LRS i GRS.<br/><br/> ZRS nie jest obsługiwane.
Chłodny i gorący magazyn | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnym i gorącym magazynie
Miejsca do magazynowania | Obsługiwane |
Interfejs magazynu NVMe | Nieobsługiwane
Szyfrowanie danych spoczynku (SSE) | Obsługiwane | SSE jest ustawieniem domyślnym na kontach magazynu.
Szyfrowanie danych spoczynku (CMK) | Obsługiwane | W przypadku dysków zarządzanych są obsługiwane zarówno klucze oprogramowania, jak i klucze HSM
Podwójne szyfrowanie w spoczynku | Obsługiwane | Dowiedz się więcej na temat obsługiwanych regionów dla [systemów Windows](../virtual-machines/disk-encryption.md) i [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Obsługiwane w przypadku maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezaładowych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. <br/><br/> Szyfrowanie pojedynczych woluminów na jednym dysku nie jest obsługiwane. |
Azure Disk Encryption (ADE) dla systemu operacyjnego Linux | Obsługiwane w przypadku maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezaładowych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. <br/><br/> Szyfrowanie pojedynczych woluminów na jednym dysku nie jest obsługiwane. <br><br> Znany problem z włączaniem replikacji. [Dowiedz się więcej.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Rotacja klucza sygnatury dostępu współdzielonego | Nieobsługiwane | Jeśli klucz sygnatury dostępu współdzielonego dla kont magazynu zostanie obrócony, klient musi wyłączyć i ponownie włączyć replikację. |
Buforowanie hosta | Obsługiwane
Dodawanie w cieple    | Obsługiwane | Włączanie replikacji dla dysku danych, który można dodać do replikowanej maszyny wirtualnej platformy Azure, jest obsługiwane w przypadku maszyn wirtualnych, które korzystają z dysków zarządzanych. <br/><br/> Jednocześnie do maszyny wirtualnej platformy Azure można dodać tylko jeden dysk na gorąco. Równoległe dodawanie wielu dysków nie jest obsługiwane. |
Usuwanie dysku na gorąco    | Nieobsługiwane | Jeśli usuniesz dysk danych na maszynie wirtualnej, musisz wyłączyć replikację i ponownie włączyć replikację dla maszyny wirtualnej.
Wykluczanie dysku | Wsparcie. Do skonfigurowania należy użyć programu [PowerShell.](azure-to-azure-exclude-disks.md) |    Dyski tymczasowe są domyślnie wykluczone.
Bezpośrednie miejsca do magazynowania  | Obsługiwane w przypadku punktów odzyskiwania spójnych na awarii. Punkty odzyskiwania spójne z aplikacją nie są obsługiwane. |
Serwer plików skalowania w celu skalowania w zewnątrz  | Obsługiwane w przypadku punktów odzyskiwania spójnych na awarii. Punkty odzyskiwania spójne z aplikacją nie są obsługiwane. |
DRBD | Dyski, które są częścią konfiguracji drbd nie są obsługiwane. |
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |
Chłodny i gorący magazyn | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnym i gorącym magazynie
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli ogranicz dostęp sieci wirtualnej do kont magazynu, włącz opcję [Zezwalaj](../storage/common/storage-network-security.md#exceptions)na zaufane usługi firmy Microsoft .
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa Gorąca i Chłodna) | Obsługiwane | Koszty transakcji znacznie wzrastają w porównaniu z kontami magazynu ogólnego przeznaczenia w wersji 1
Generacja 2 (rozruch UEFI) | Obsługiwane
Dyski NVMe | Nieobsługiwane
Dyski udostępnione platformy Azure | Nieobsługiwane
Opcja bezpiecznego transferu | Obsługiwane
Dyski z włączonym akceleratorem zapisu | Nieobsługiwane
Tagi  | Obsługiwane | Tagi generowane przez użytkownika są replikowane co 24 godziny.

>[!IMPORTANT]
> Aby uniknąć problemów z wydajnością, upewnij się, że przestrzegasz celów dotyczących skalowalności i wydajności dysków zarządzanych na dysku [maszyny wirtualnej.](../virtual-machines/disks-scalability-targets.md) Jeśli używasz ustawień domyślnych, program Site Recovery wymagane dyski i konta magazynu na podstawie konfiguracji źródła. Jeśli dostosujesz i wybierzesz własne ustawienia, postępuj zgodnie z celami skalowalności i wydajności dysków dla źródłowych maszyn wirtualnych.

## <a name="limits-and-data-change-rates"></a>Limity i wskaźniki zmian danych

W poniższej tabeli podsumowano Site Recovery limity.

- Te limity są oparte na naszych testach, ale oczywiście nie obejmują wszystkich możliwych kombinacji we/wy aplikacji.
- Rzeczywiste wyniki mogą się różnić w zależności od kombinacji we/wy aplikacji.
- Istnieją dwa limity, które należy wziąć pod uwagę, dotyczące współczynnika zmian danych na dysku i współczynnika zmian danych na maszynę wirtualną.
- Bieżący limit współczynnika zmian danych na maszynę wirtualną wynosi 54 MB/s, niezależnie od rozmiaru.

**Miejsce docelowe magazynu** | **Średnia liczba źródłowych we/wy dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="replicated-machines---networking"></a>Replikowane maszyny — sieć
**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Karta sieciowa | Maksymalna liczba obsługiwana dla określonego rozmiaru maszyny wirtualnej platformy Azure | Karty sieciowe są tworzone podczas tworzenia maszyny wirtualnej podczas pracy w trybu failover.<br/><br/> Liczba karty sieciowe na maszynie wirtualnej trybu failover zależy od liczby karty sieciowej na źródłowej maszynie wirtualnej, gdy włączono replikację. Dodanie lub usunięcie karty sieciowej po włączeniu replikacji nie ma wpływu na liczbę kart sieciowych na zreplikowanej maszynie wirtualnej po włączeniu trybu failover. <br/><br/> Kolejność karty sieciowej po zakończeniu pracy w awarii nie musi być taka sama jak w przypadku oryginalnego zamówienia. <br/><br/> Nazwy karty sieciowej w regionie docelowym można zmienić na podstawie konwencji nazewnictwa organizacji. Zmiana nazwy karty sieciowej jest obsługiwana przy użyciu programu PowerShell.
Internetowy moduł równoważenia obciążenia | Nieobsługiwane | W regionie podstawowym można skonfigurować publiczne/internetowe usługi równoważenia obciążenia. Jednak publiczne/internetowe usługi równoważenia obciążenia nie są obsługiwane Azure Site Recovery w regionie dr.
Wewnętrzny równoważenie obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany równoważenie obciążenia przy użyciu Azure Automation skryptu w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Skojarz istniejący publiczny adres IP z kartą sieciową. Możesz też utworzyć publiczny adres IP i skojarzyć go z kartą sieciową Azure Automation skryptu w planie odzyskiwania.
Sieciowa sieciowa sieciowa sieciowa karta sieciowa | Obsługiwane | Kojarzenie sieciowej sieciowej Azure Automation sieciową przy użyciu skryptu Azure Automation w planie odzyskiwania.
Sieciowa grupa danych w podsieci | Obsługiwane | Kojarzenie sieciowej sieciowej Azure Automation podsiecią przy użyciu skryptu Azure Automation w planie odzyskiwania.
Zastrzeżony (statyczny) adres IP | Obsługiwane | Jeśli karta sieciowa na źródłowej maszynie wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam dostępny adres IP, zostanie przypisana do maszyny wirtualnej w stanie fail over.<br/><br/> Jeśli docelowa podsieć nie ma tego samego adresu IP, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny wirtualnej.<br/><br/> Możesz również określić stały adres IP i podsieć w pozycji **Zreplikowane elementy**  >  **Ustawienia Interfejsy**  >  **obliczeniowe** i  >  **sieciowe**.
Dynamiczny adres IP | Obsługiwane | Jeśli karta sieciowa w źródle ma dynamiczne adresowanie IP, karta sieciowa na maszynie wirtualnej w trybie fail over jest domyślnie dynamiczna.<br/><br/> W razie potrzeby można zmodyfikować go na stały adres IP.
Wiele adresów IP | Nieobsługiwane | W przypadku pracy w trybie fail over maszyny wirtualnej z kartą sieciową z wieloma adresami IP jest zachowywany tylko podstawowy adres IP karty sieciowej w regionie źródłowym. Aby przypisać wiele adresów IP, możesz dodać [](recovery-plan-overview.md) maszyny wirtualne do planu odzyskiwania i dołączyć skrypt w celu przypisania dodatkowych adresów IP do planu lub wprowadzić zmianę ręcznie lub za pomocą skryptu po zakończeniu pracy w trybie failover.
Traffic Manager     | Obsługiwane | Możesz wstępnie skonfigurować Traffic Manager tak, aby ruch był regularnie przekierowywał ruch do punktu końcowego w regionie źródłowym i do punktu końcowego w regionie docelowym w przypadku trybu failover.
Azure DNS | Obsługiwane |
Niestandardowe DNS    | Obsługiwane |
Nieuwierzytowany serwer proxy | Obsługiwane | [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Uwierzytelniony serwer proxy | Nieobsługiwane | Jeśli maszyna wirtualna używa uwierzytelnionego serwera proxy na potrzeby łączności wychodzącej, nie można jej replikować przy użyciu Azure Site Recovery.
Połączenie sieci VPN typu lokacja-lokacja z siecią lokalną<br/><br/>(z lub bez expressRoute)| Obsługiwane | Upewnij się, że trasy zdefiniowane przez użytkownika i sieciowe sieciowe sieci są skonfigurowane w taki sposób, aby ruch Site Recovery nie był przekierowyny do sieci lokalnej. [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Połączenie sieć wirtualna-sieć wirtualna    | Obsługiwane | [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Punkty końcowe usługi dla sieci wirtualnej | Obsługiwane | Jeśli ograniczasz dostęp sieci wirtualnej do kont magazynu, upewnij się, że zaufane usługi firmy Microsoft mają dostęp do konta magazynu.
Wydajniejsze sieci | Obsługiwane | Przyspieszona sieć musi być włączona na źródłowej maszynie wirtualnej. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).
Urządzenie sieciowe Palo Alto | Nieobsługiwane | W przypadku urządzeń innych firm dostawca w maszynie wirtualnej często nakłada ograniczenia. Azure Site Recovery agent, rozszerzenia i łączność wychodząca muszą być dostępne. Jednak urządzenie nie umożliwia skonfigurowania żadnych działań wychodzących wewnątrz maszyny wirtualnej.
Protokół IPv6  | Nieobsługiwane | Konfiguracje mieszane, które obejmują zarówno protokół IPv4, jak i IPv6, również nie są obsługiwane. Przed dowolną operacją Site Recovery podsieć zakresu IPv6.
Dostęp do usługi Site Recovery prywatnej | Obsługiwane | [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tagi  | Obsługiwane | Tagi generowane przez użytkownika na karty sieciowe są replikowane co 24 godziny.



## <a name="next-steps"></a>Następne kroki

- Przeczytaj [wskazówki dotyczące sieci](./azure-to-azure-about-networking.md)  replikowania maszyn wirtualnych platformy Azure.
- Wdrażanie odzyskiwania po awarii przez [replikowanie maszyn wirtualnych platformy Azure.](./azure-to-azure-quickstart.md)
