---
title: Macierz obsługi odzyskiwania po awarii maszyny wirtualnej platformy Azure z Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych platformy Azure w regionie pomocniczym z Azure Site Recovery.
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 522af9738cac098dcc9e8cb73183c0bd6b3b4902
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534683"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Macierz obsługi odzyskiwania po awarii maszyny wirtualnej platformy Azure między regionami platformy Azure

W tym artykule podsumowano obsługę i wymagania wstępne dotyczące odzyskiwania po awarii maszyn wirtualnych platformy Azure z jednego regionu świadczenia usługi Azure do innego przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


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
**Przenoszenie zasobów obliczeniowych/magazynowych/sieciowych między grupami zasobów** | Nieobsługiwane.<br/><br/> W przypadku przenoszenia maszyny wirtualnej lub skojarzonych składników, takich jak magazyn/sieć po zakończeniu replikacji maszyny wirtualnej, należy wyłączyć i ponownie włączyć replikację maszyny wirtualnej.
**Replikowanie maszyn wirtualnych platformy Azure z jednej subskrypcji do innej na potrzeby odzyskiwania po awarii** | Obsługiwane w ramach tej samej dzierżawy Azure Active Directory.
**Migrowanie maszyn wirtualnych między regionami w ramach obsługiwanych klastrów geograficznych (w ramach i między subskrypcjami)** | Obsługiwane w ramach tej samej dzierżawy Azure Active Directory.
**Migrowanie maszyn wirtualnych w tym samym regionie** | Nieobsługiwane.
**Dedykowane hosty platformy Azure** | Nieobsługiwane.

## <a name="region-support"></a>Obsługa regionów

Maszyny wirtualne można replikować i odzyskiwać między dwoma regionami w tym samym klastrze geograficznym. Klastry geograficzne są definiowane z uwzględnieniem opóźnień danych i suwerenności.


**Klaster geograficzny** | **Regiony platformy Azure**
-- | --
USA | Kanada Wschodnia, Kanada środkowa, Południowo-środkowe stany USA, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2, środkowe stany USA, Północno-środkowe stany USA
Europa | Zachodnie Zjednoczone Królestwo, Południowe Zjednoczone Królestwo, Europa Północna, Europa Zachodnia, Zachodnia Republika Południowej Afryki, Północna Republika Południowej Afryki, Norwegia Wschodnia, Francja środkowa, Szwajcaria Północna, Niemcy Środkowo-Zachodnie
Azja | Indie Południowe, Indie Środkowe, Indie Zachodnie, Azja Południowo-Wschodnia, Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa
Australia    | Australia Wschodnia, Australia Południowo-Wschodnia, Australia Środkowa, Australia Środkowa 2
Azure Government    | Stany USA GOV Wirginia, US GOV Iowa, US GOV Arizona, US GOV Texass, US DOD USA, US DOD USA
Niemcy    | Niemcy środkowe, Niemcy północno-wschodnie
Chiny | Chiny Wschodnie, Chiny Północne, Chiny North2, Chiny 2
Ograniczone regiony zarezerwowane na potrzeby odzyskiwania po awarii w kraju |Szwajcaria Zachodnia zarezerwowane dla Szwajcaria Północna, Francja Południowo-Wschodnia zastrzeżona dla francuskich Francji, w przypadku ZJEDNOCZONEgo Zjednoczonego, z ograniczeniami przeznaczonymi dla klientów z USA, Norwegia Zachodnia

>[!NOTE]
>
> - W regionie **Brazylia Południowa** można przeprowadzić replikację i przełączenie w tryb failover do tych regionów: Południowo-środkowe stany USA, zachodnie stany USA, wschodnie Stany Zjednoczone, Wschodnie stany USA 2, zachodnie stany USA, zachodnie stany USA 2 i środkowe stany USA.
> - Brazylia Południowa może być używana tylko jako region źródłowy, z którego maszyny wirtualne mogą replikować przy użyciu Site Recovery. Nie może działać jako region docelowy. Dzieje się tak z powodu problemów opóźnienia spowodowanych odległościami geograficznymi. Należy pamiętać, że w przypadku przełączenia w tryb failover z Brazylii Południowej jako regionu źródłowego do obiektu docelowego jest obsługiwane powrót po awarii do Brazylii południe z regionu docelowego.
> - Możesz korzystać z regionów, do których masz odpowiedni dostęp.
> - Jeśli region, w którym chcesz utworzyć magazyn, nie jest wyświetlany, upewnij się, że Twoja subskrypcja ma dostęp do tworzenia zasobów w tym regionie.
> - Jeśli podczas włączania replikacji nie widzisz regionu w klastrze geograficznym, upewnij się, że Twoja subskrypcja ma uprawnienia do tworzenia maszyn wirtualnych w tym regionie.



## <a name="cache-storage"></a>Magazyn pamięci podręcznej

Ta tabela zawiera podsumowanie obsługi konta magazynu pamięci podręcznej używanego przez Site Recovery podczas replikacji.

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa gorąca i chłodna) | Obsługiwane | Użycie GPv2 jest niezalecane, ponieważ koszty transakcji dla wersji 2 są znacznie wyższe niż konta magazynu w wersji 1.
Premium Storage | Nieobsługiwane | Konta magazynu w warstwie Standardowa są używane na potrzeby magazynu pamięci podręcznej w celu optymalizacji kosztów.
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli używasz konta magazynu pamięci podręcznej z włączoną obsługą zapory lub docelowego konta magazynu, upewnij się, że masz [opcję "Zezwalaj na zaufane usługi firmy Microsoft"](../storage/common/storage-network-security.md#exceptions).<br></br>Upewnij się również, że zezwolisz na dostęp do co najmniej jednej podsieci źródłowej sieci wirtualnej.

W poniższej tabeli wymieniono limity dotyczące liczby dysków, które można replikować na jedno konto magazynu.

**Typ konta magazynu**    |    **Liczba zmian = 4 MB/s na dysk**    |    **Liczba zmian = 8 MB/s na dysk**
---    |    ---    |    ---
Konto magazynu w wersji 1    |    dyski 300    |    dyski 150
Konto magazynu v2    |    dyski 750    |    dyski 375

Ze względu na to, że średnie zmiany na dyskach rosną, liczba dysków, które może obsłużyć konto magazynu. Powyższa tabela może służyć jako przewodnik podejmowania decyzji dotyczących liczby kont magazynu, które wymagają aprowizacji.

Należy pamiętać, że powyższe limity są specyficzne dla platformy Azure na platformę Azure i scenariusze odzyskiwania po awarii strefy. 

## <a name="replicated-machine-operating-systems"></a>Zreplikowane systemy operacyjne maszyn

Site Recovery obsługuje replikację maszyn wirtualnych platformy Azure z systemami operacyjnymi wymienionymi w tej sekcji. Należy pamiętać, że jeśli już trwa uaktualnianie lub zmiana na starszą maszynę, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.

### <a name="windows"></a>Windows


**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Obsługiwane dla serwera Server Core, serwera z funkcją środowisko pulpitu.
Windows Server 2016  | Obsługiwane serwery z funkcją środowisko pulpitu.
Windows Server 2012 z dodatkiem R2 | Obsługiwane.
Windows Server 2012 | Obsługiwane.
Windows Server 2008 R2 z dodatkiem SP1/SP2 | Obsługiwane.<br/><br/> W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługi mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację [stosu obsługi systemu Windows (SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419) na komputerach z systemem Windows Server 2008 R2 z dodatkiem SP1/SP2.  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Obsługiwane.
Windows 8.1 (x64) | Obsługiwane.
Windows 8 (x64) | Obsługiwane.
Windows 7 (x64) z dodatkiem SP1 lub nowszym | W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) rozszerzenia usługi mobilności dla maszyn wirtualnych platformy Azure należy zainstalować aktualizację [stosu obsługi systemu Windows (SSU)](https://support.microsoft.com/help/4490628) i [aktualizację SHA-2](https://support.microsoft.com/help/4474419) na komputerach z systemem Windows 7 z dodatkiem SP1.  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**System operacyjny** | **Szczegóły**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/), [8,3](https://support.microsoft.com/help/4597409/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10 </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), [7,9, wersja wstępna](https://support.microsoft.com/help/4578241/), 7,9 </br> 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609), [8,3](https://support.microsoft.com/help/4597409/)
Serwer Ubuntu 14,04 LTS | Obejmuje obsługę wszystkich 14,04. *x* wersje; [Obsługiwane wersje jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines); 
Serwer Ubuntu 16,04 LTS | Obejmuje obsługę wszystkich 16,04. *x* wersje; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania, a pakiet Cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w trybie failover (w zależności od konfiguracji cloudinit). Logowanie oparte na haśle można włączyć na maszynie wirtualnej przez zresetowanie hasła w menu Ustawienia > > Rozwiązywanie problemów z pomocą techniczną (w przypadku maszyny wirtualnej w trybie failover w Azure Portal.
Serwer Ubuntu 18,04 LTS | Obejmuje obsługę wszystkich 18,04. *x* wersje; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Serwery Ubuntu korzystające z uwierzytelniania opartego na hasłach i logowania, a pakiet Cloud-init do konfigurowania maszyn wirtualnych w chmurze mogą mieć wyłączone logowanie oparte na hasłach w trybie failover (w zależności od konfiguracji cloudinit). Logowanie oparte na haśle można włączyć na maszynie wirtualnej przez zresetowanie hasła w menu Ustawienia > > Rozwiązywanie problemów z pomocą techniczną (w przypadku maszyny wirtualnej w trybie failover w Azure Portal.
Serwer Ubuntu 20,04 LTS | Obejmuje obsługę wszystkich 20,04. *x* wersje; [Obsługiwana wersja jądra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Obejmuje obsługę wszystkich 7. *x* wersje [obsługiwane przez jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | Obejmuje obsługę wszystkich 8. *x* wersje [obsługiwane przez jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 9 | Obejmuje obsługę 9,1 do 9,13. Debian 9,0 nie jest obsługiwana. [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [Obsługiwane wersje jądra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5  [(obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15, SP1, SP2[(obsługiwane wersje jądra)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | USUWA<br/><br/> Uaktualnienie maszyn replikowanych z programu SP3 do wersji SP4 nie jest obsługiwane. Jeśli replikowana maszyna została uaktualniona, należy wyłączyć replikację i ponownie włączyć replikację po uaktualnieniu.
SUSE Linux Enterprise Server 11 | DODATEK
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/) (uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8,1 (uruchomione na wszystkich jądrach UEK i jądrze RedHat <= 3.10.0-1062. * są obsługiwane w [9,35](https://support.microsoft.com/help/4573888/). Obsługa pozostałych RedHat jądra jest dostępna w [9,36](https://support.microsoft.com/help/4578241/))

> [!NOTE]
> W przypadku wersji systemu Linux Azure Site Recovery nie obsługuje niestandardowych obrazów systemów operacyjnych. Obsługiwane są tylko jądra podstawowe, które są częścią wersji pomocniczej dystrybucji/aktualizacji.

* * Uwaga: aby obsłużyć najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery naprawia poprawkę poprawek na bieżąco na najnowszej wersji agenta mobilności. Ta poprawka jest wprowadzana między dwiema wersjami głównymi. Aby zaktualizować do najnowszej wersji agenta mobilności (łącznie z poprawkami gorącą), wykonaj kroki opisane w [tym artykule](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Ta poprawka jest obecnie wprowadzana dla agentów mobilności używanych na platformie Azure do scenariusza odzyskiwania po awarii platformy Azure.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Ubuntu dla usługi Azure Virtual Machines

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14,04 LTS | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
|||
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-Generic to 4.4.0-197-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-128-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1102-Azure </br> 4.15.0-132-Generic, 4.4.0-200-Generic, 4.15.0-1106-Azure, 4.15.0-133-Generic, 4.4.0-201-Generic do 9,40 poprawka gorącej poprawki * *|
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-Generic to 4.4.0-194-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-123-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1098-Azure </br> 4.4.0-197-Generic, 4.15.0-126-Generic, 4.15.0-128-Generic, 4.15.0-1100-Azure, 4.15.0-1102-Azure do 9,39 poprawka "Hot Fix" * *|
16,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-Generic to 4.4.0-190-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-118-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1096-Azure </br> 4.4.0-193-Generic, 4.15.0-120-Generic, 4.15.0-122-Generic, 4.15.0-1098-Azure do 9,38 poprawka gorącej poprawki * *|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-Generic do 4.4.0-189-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-115-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1093-Azure </br> 4.4.0-190-Generic, 4.15.0-117-Generic, 4.15.0-118-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure do 9,37 poprawka "Hot Fix" * *|
16,04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-Generic to 4.4.0-187-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-112-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1092-Azure |
|||
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-Generic do 4.15.0-129-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-63-Generic </br> 5.3.0-19-Generic do 5.3.0-69-Generic </br> 5.4.0-37-Generic to 5.4.0-59-Generic</br> 4.15.0-1009-Azure to 4.15.0-1103-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure do 5.4.0-1035-Azure </br> 4.15.0-1104-Azure, 4.15.0-130-Generic, 4.15.0-132-Generic, 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 4.15.0-1106-Azure, 4.15.0---Generic, 4.15.0-9,40 135-Generic, 5.4.0-1039-Azure|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-ogólny do 4.15.0-123-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-63-Generic </br> 5.3.0-19-Generic do 5.3.0-69-Generic </br> 5.4.0-37-Generic to 5.4.0-53-Generic</br> 4.15.0-1009-Azure to 4.15.0-1099-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure to 5.4.0-1031-Azure </br> 4.15.0-124-Generic, 5.4.0-54-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 4.15.0-1100-Azure, 4.15.0-126-Generic, 4.15.0-128-Generic, 5.4.0-58-Generic, 4.15.0-1102-Azure, 5.4.0-1034-Azure do 9,39 poprawka na gorąco * *|
18,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-Generic do 4.15.0-118-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-61-Generic </br> 5.3.0-19-Generic do 5.3.0-67-Generic </br> 5.4.0-37-Generic to 5.4.0-48-Generic</br> 4.15.0-1009-Azure to 4.15.0-1096-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure do 5.4.0-1026-Azure </br> 4.15.0-121-Generic, 4.15.0-122-Generic, 5.0.0-62-Generic, 5.3.0-68-Generic, 5.4.0-51-Generic, 5.4.0-52-Generic, 4.15.0-1099-Azure, 5.4.0-1031-Azure do 9,38 poprawka na gorąco * *|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-ogólny do 4.15.0-115-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-60-Generic </br> 5.3.0-19-Generic do 5.3.0-66-Generic </br> 5.4.0-37-Generic to 5.4.0-45-Generic</br> 4.15.0-1009-Azure to 4.15.0-1093-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure do 5.4.0-1023-Azure</br> 4.15.0-117-Generic, 4.15.0-118-Generic, 5.0.0-61-Generic, 5.3.0-67-Generic, 5.4.0-47-Generic, 5.4.0-48-Generic, 4.15.0-1095-Azure, 4.15.0-1096-Azure, 5.4.0-1025-Azure, 5.4.0-1026-Azure do 9,37 poprawka poprawek * *|
18,04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-ogólny do 4.15.0-112-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-58-Generic </br> 5.3.0-19-Generic do 5.3.0-65-Generic </br> 5.4.0-37-Generic to 5.4.0-42-Generic</br> 4.15.0-1009-Azure to 4.15.0-1092-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1032-Azure </br> 5.4.0-1020-Azure to 5.4.0-1022-Azure </br> 5.0.0-60-Generic & 5.3.0-1035-Azure do 9,36 poprawka gorącej poprawki * *|
|||
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-Generic do 5.4.0-59 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1035-Azure </br> 5.8.0-29-Generic to 5.8.0-34-Generic </br> 5.4.0-1036-Azure, 5.4.0-60-Generic, 5.4.0-62-Generic, 5.8.0-36-Generic, 5.8.0-38-Generic, 5.4.0-1039-Azure, 5.4.0-64-Generic, 5.4.0-65-Generic, 5.8.0-40-Generic, 5.8.0-41-ogólny do 9,40 poprawka poprawek * *|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-ogólny do 5.4.0-53 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-z platformą Azure do 9,39 poprawka poprawek * *
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-ogólny do 5.4.0-53 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1031-Azure </br> 5.4.0-54-Generic, 5.8.0-29-Generic, 5.4.0-1032-Azure, 5.4.0-56-Generic, 5.8.0-31-Generic, 5.8.0-33-Generic, 5.4.0-58-Generic, 5.4.0-1034-z platformą Azure do 9,39 poprawka poprawek * *
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-ogólny do 5.4.0-48 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1026-Azure </br> 5.4.0-51-Generic, 5.4.0-52-Generic, 5.8.0-23-Generic, 5.8.0-25-Generic, 5.4.0-1031-Azure do 9,38 poprawka na gorąco * *
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-ogólny do 5.4.0-45 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1023-Azure </br> 5.4.0-47-Generic, 5.4.0-48-Generic, 5.4.0-1025-Azure, 5.4.0-1026-Azure do 9,37 poprawka na gorąco * *
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-ogólny do 5.4.0-42 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1022-Azure

* * Uwaga: aby obsłużyć najnowsze jądra systemu Linux w ciągu 15 dni od wydania, Azure Site Recovery naprawia poprawkę poprawek na bieżąco na najnowszej wersji agenta mobilności. Ta poprawka jest wprowadzana między dwiema wersjami głównymi. Aby zaktualizować do najnowszej wersji agenta mobilności (łącznie z poprawkami gorącą), wykonaj kroki opisane w [tym artykule](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Ta poprawka jest obecnie wprowadzana dla agentów mobilności używanych na platformie Azure do scenariusza odzyskiwania po awarii platformy Azure.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane wersje jądra Debian dla usługi Azure Virtual Machines

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0 -0. BPO. 4-amd64 |
|||
Debian 8 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 3.16.0-4-amd64 do 3.16.0-11-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 11-amd64 |
|||
Debian 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1 — amd64 do 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-amd64 do 4.19.0 -0. BPO. 13-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 do 4.19.0 -0. BPO. 13-Cloud-amd64 </br> 
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1 — amd64 do 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-amd64 do 4.19.0 -0. BPO. 12-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 do 4.19.0 -0. BPO. 12-Cloud-amd64 </br> 4.19.0 -0. BPO. 13-amd64, 4.19.0 -0. BPO. 13-Cloud-amd64 do 9,39 poprawka Hot Fix * *</br> 
Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 do 4.9.0-13-amd64 </br> 4.19.0 -0. BPO. 1-amd64 do 4.19.0 -0. BPO. 11-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 do 4.19.0 -0. BPO. 11-Cloud-amd64 </br> 4.9.0-14-amd64, 4.19.0 -0. BPO. 12-amd64, 4.19.0 -0. BPO. 12-Cloud-amd64 do 9,38 poprawka na gorąco * *
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 do 4.9.0-13-amd64, 4.19.0 -0. BPO. 6-amd64 do 4.19.0 -0. BPO. 10-amd64, 4.19.0 -0. BPO. 6-Cloud-amd64 do 4.19.0 -0. BPO. 10-Cloud-amd64
|||
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 do 4.19.0-13-amd64 </br> 4.19.0-6-Cloud-amd64 do 4.19.0-13-Cloud-amd64 </br> 5.8.0 -0. BPO. 2 — amd64 </br> 5.8.0 -0. BPO. 2 — Cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane SUSE Linux Enterprise Server 12 wersji jądra dla usługi Azure Virtual Machines

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3, SP4 i SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.38 — Azure </br> 4.12.14-16.41-Azure do 9,40 poprawka na gorąco * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3, SP4 i SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.34 — Azure </br> 4.12.14-16.38-Azure do 9,39 poprawka na gorąco * *|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3, SP4 i SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.28 — Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3, SP4 i SP5](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.22 — Azure </br> 4.12.14-16.25-Azure, 4.12.14-16.28-Azure do 9,37 poprawka gorącej poprawki * *|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Obsługiwane SUSE Linux Enterprise Server 15 wersji jądra dla usługi Azure Virtual Machines

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Domyślnie obsługiwane są wszystkie [jądra SUSE 15, SP1 i SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.55 — Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure to 5.3.18-18.29-Azure </br> 5.3.18-18.32-Azure, 4.12.14-8.58-Azure do 9,40 poprawka gorącej poprawki * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Domyślnie obsługiwane są wszystkie [jądra SUSE 15, SP1 i SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.47 — Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure to 5.3.18-18.21-Azure </br> 4.12.14-8.52-Azure, 5.3.18-18.24-Azure, 4.12.14-8.55-Azure, 5.3.18-18.29-Azure do 9,39 poprawka na gorąco * *
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Domyślnie obsługiwane są wszystkie [jądra SUSE 15, SP1 i SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.44 — Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure to 5.3.18-18.18-Azure </br> 4.12.14-8.47-Azure, 5.3.18-18.21-Azure do 9,38 poprawka gorącej poprawki * *
SUSE Linux Enterprise Server 15 i 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Domyślnie obsługiwane są wszystkie [jądra SUSE 15, SP1 i SP2](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.38 — Azure </br> 4.12.14-8.41-Azure, 4.12.14-8.44-Azure do 9,37 poprawka gorącej poprawki * *


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Zreplikowane maszyny — system plików Linux/magazyn gościa

* Systemy plików: ext3, EXT4, XFS, BTRFS
* Menedżer woluminów: LVM2

> [!NOTE]
> Oprogramowanie wielościeżkowe nie jest obsługiwane.


## <a name="replicated-machines---compute-settings"></a>Zreplikowane maszyny — ustawienia obliczeń

**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Rozmiar | Rozmiar maszyny wirtualnej platformy Azure z co najmniej 2 rdzeniami procesora i 1 GB pamięci RAM | Sprawdź [rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/sizes.md).
Zestawy dostępności | Obsługiwane | Jeśli włączysz replikację dla maszyny wirtualnej platformy Azure z opcjami domyślnymi, zestaw dostępności zostanie utworzony automatycznie na podstawie ustawień regionu źródłowego. Te ustawienia można modyfikować.
Strefy dostępności | Obsługiwane |
Korzyść użycia hybrydowego (centrum) | Obsługiwane | Jeśli źródłowa maszyna wirtualna ma włączoną licencję centrum, testowej pracy w trybie failover lub maszyny wirtualnej przełączonej do sieci również korzysta z licencji centrum.
Zestawy skalowania maszyn wirtualnych | Nieobsługiwane |
Obrazy z galerii platformy Azure — opublikowane przez firmę Microsoft | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy z galerii platformy Azure — opublikowane przez inną firmę | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Obrazy niestandardowe — Opublikowano przez inną firmę | Obsługiwane | Obsługiwane, jeśli maszyna wirtualna jest uruchamiana w obsługiwanym systemie operacyjnym.
Maszyny wirtualne migrowane przy użyciu Site Recovery | Obsługiwane | Jeśli maszyna wirtualna VMware lub maszyna fizyczna została zmigrowana do platformy Azure przy użyciu Site Recovery, należy odinstalować starszą wersję usługi mobilności działającej na maszynie i ponownie uruchomić maszynę przed replikacją jej do innego regionu platformy Azure.
Zasady kontroli RBAC platformy Azure | Nieobsługiwane | Zasady kontroli dostępu opartej na rolach (Azure RBAC) na maszynach wirtualnych nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym.
Rozszerzenia | Nieobsługiwane | Rozszerzenia nie są replikowane do maszyny wirtualnej trybu failover w regionie docelowym. Należy ją zainstalować ręcznie po przejściu do trybu failover.
Grupy umieszczania zbliżeniowe | Obsługiwane | Maszyny wirtualne znajdujące się w grupie umieszczania w sąsiedztwie mogą być chronione przy użyciu Site Recovery.
Tagi  | Obsługiwane | Tagi wygenerowane przez użytkownika zastosowane na źródłowych maszynach wirtualnych są przenoszone do docelowych maszyn wirtualnych po przejściu do trybu failover lub przejścia w tryb failover. Tagi na maszynach wirtualnych są replikowane co 24 godziny przez cały czas, gdy maszyny wirtualne znajdują się w regionie docelowym.


## <a name="replicated-machines---disk-actions"></a>Zreplikowane maszyny — akcje dysku

**Akcja** | **Szczegóły**
-- | ---
Zmień rozmiar dysku na zreplikowanej maszynie wirtualnej | Obsługiwane przez źródłową maszynę wirtualną przed przełączeniem w tryb failover. Nie trzeba wyłączać/ponownie włączać replikacji.<br/><br/> Zmiana źródłowej maszyny wirtualnej po przejściu do trybu failover nie powoduje przechwycenia zmian.<br/><br/> Jeśli zmienisz rozmiar dysku maszyny wirtualnej platformy Azure po przejściu w tryb failover, zmiany nie zostaną przechwycone przez Site Recovery, a powrót po awarii zostanie zmieniony na oryginalny rozmiar maszyny wirtualnej.
Dodawanie dysku do zreplikowanej maszyny wirtualnej | Obsługiwane
Zmiany w trybie offline na dyskach chronionych | Odłączanie dysków i Dokonywanie przez nie modyfikacji w trybie offline wymaga wyzwalania pełnej ponownej synchronizacji.

## <a name="replicated-machines---storage"></a>Zreplikowane maszyny — magazyn

Ta tabela zawiera podsumowanie obsługi dysku systemu operacyjnego Azure VM, dysku danych i dysku tymczasowego.

- Należy przestrzegać limitów dysku maszyny wirtualnej i elementów docelowych dla [dysków zarządzanych](../virtual-machines/disks-scalability-targets.md) , aby uniknąć problemów z wydajnością.
- W przypadku wdrożenia z ustawieniami domyślnymi program Site Recovery automatycznie tworzy dyski i konta magazynu na podstawie ustawień źródłowych.
- W przypadku dostosowania programu upewnij się, że przestrzegasz wytycznych.

**Składnik** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Maksymalny rozmiar dysku systemu operacyjnego | 2048 GB | [Dowiedz się więcej](../virtual-machines/managed-disks-overview.md) o dyskach maszyn wirtualnych.
Dysk tymczasowy | Nieobsługiwane | Dysk tymczasowy jest zawsze wykluczony z replikacji.<br/><br/> Nie przechowuj żadnych trwałych danych na dysku tymczasowym. [Dowiedz się więcej](../virtual-machines/managed-disks-overview.md).
Maksymalny rozmiar dysku danych | 32 TB dla dysków zarządzanych<br></br>4 TB dla dysków niezarządzanych|
Minimalny rozmiar dysku danych | Brak ograniczeń dla dysków niezarządzanych. 2 GB dla dysków zarządzanych |
Maksymalna liczba dysków danych | Do 64, zgodnie z obsługą określonego rozmiaru maszyny wirtualnej platformy Azure | [Dowiedz się więcej](../virtual-machines/sizes.md) o rozmiarach maszyn wirtualnych.
Szybkość zmian dysku danych | Maksymalnie 20 MB/s na dysk dla usługi Premium Storage. Maksymalnie 2 MB/s na dysk w przypadku magazynu w warstwie Standardowa. | Jeśli średnia szybkość zmian danych na dysku jest ciągle wyższa niż wartość maksymalna, replikacja nie zostanie wystawiona.<br/><br/>  Jeśli jednak maksimum zostanie przekroczone sporadycznie, replikacja może zostać wyświetlona, ale może wystąpić nieco opóźnione punkty odzyskiwania.
Dysk danych — standardowe konto magazynu | Obsługiwane |
Dysk danych — konto magazynu w warstwie Premium | Obsługiwane | Jeśli maszyna wirtualna ma rozłożone dyski w warstwie Premium i w warstwie Standardowa, możesz wybrać inne docelowe konto magazynu dla każdego dysku, aby mieć pewność, że konfiguracja magazynu jest taka sama w regionie docelowym.
Dysk zarządzany — standardowa | Obsługiwane w regionach świadczenia usługi Azure, w których Azure Site Recovery jest obsługiwana. |
Dysk zarządzany — wersja Premium | Obsługiwane w regionach świadczenia usługi Azure, w których Azure Site Recovery jest obsługiwana. |
Dysk SSD w warstwie Standardowa | Obsługiwane |
Nadmiarowość | Obsługiwane są LRS i GRS.<br/><br/> ZRS nie jest obsługiwana.
Chłodna i gorąca pamięć masowa | Nieobsługiwane | Dyski maszyn wirtualnych nie są obsługiwane w chłodnym i gorącym magazynie
Miejsca do magazynowania | Obsługiwane |
Interfejs magazynu interfejsu NVMe | Nieobsługiwane
Szyfrowanie w spoczynku (SSE) | Obsługiwane | Funkcja SSE jest domyślnym ustawieniem na kontach magazynu.
Szyfrowanie w spoczynku (CMK) | Obsługiwane | Obsługiwane są zarówno oprogramowanie, jak i klucze HSM dla dysków zarządzanych
Podwójne szyfrowanie w spoczynku | Obsługiwane | Dowiedz się więcej na temat obsługiwanych regionów dla [systemów Windows](../virtual-machines/disk-encryption.md) i [Linux](../virtual-machines/disk-encryption.md)
Azure Disk Encryption (ADE) dla systemu operacyjnego Windows | Obsługiwane w przypadku maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezarządzanych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. <br/><br/> Szyfrowanie pojedynczych woluminów na pojedynczym dysku nie jest obsługiwane. |
Azure Disk Encryption (ADE) dla systemu operacyjnego Linux | Obsługiwane w przypadku maszyn wirtualnych z dyskami zarządzanymi. | Maszyny wirtualne korzystające z dysków niezarządzanych nie są obsługiwane. <br/><br/> Klucze chronione przez moduł HSM nie są obsługiwane. <br/><br/> Szyfrowanie pojedynczych woluminów na pojedynczym dysku nie jest obsługiwane. <br><br> Znany problem dotyczący włączania replikacji. [Dowiedz się więcej.](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
Obrót klucza SAS | Nieobsługiwane | Jeśli klucz sygnatury dostępu współdzielonego dla kont magazynu zostanie obrócony, klient musi wyłączyć i ponownie włączyć replikację. |
Dodawanie gorące    | Obsługiwane | W przypadku maszyn wirtualnych korzystających z dysków zarządzanych można włączyć replikację dla dysku danych dodawanego do zreplikowanej maszyny wirtualnej platformy Azure. <br/><br/> Tylko jeden dysk można dodać do maszyny wirtualnej platformy Azure jednocześnie. Równoległe Dodawanie wielu dysków nie jest obsługiwane. |
Gorąca usuwanie dysku    | Nieobsługiwane | Jeśli usuniesz dysk z danymi na maszynie wirtualnej, musisz wyłączyć replikację i ponownie włączyć replikację dla maszyny wirtualnej.
Wykluczanie dysku | Pomocy. Aby skonfigurować program, należy użyć [programu PowerShell](azure-to-azure-exclude-disks.md) . |    Dyski tymczasowe są domyślnie wykluczone.
Bezpośrednie miejsca do magazynowania  | Obsługiwane dla punktów odzyskiwania spójnych na poziomie awarii. Punkty odzyskiwania spójne z aplikacjami nie są obsługiwane. |
Serwer plików skalowalny w poziomie  | Obsługiwane dla punktów odzyskiwania spójnych na poziomie awarii. Punkty odzyskiwania spójne z aplikacjami nie są obsługiwane. |
DRBD | Dyski, które są częścią Instalatora DRBD, nie są obsługiwane. |
LRS | Obsługiwane |
GRS | Obsługiwane |
RA-GRS | Obsługiwane |
ZRS | Nieobsługiwane |
Chłodna i gorąca pamięć masowa | Nieobsługiwane | Dyski maszyny wirtualnej nie są obsługiwane w chłodnym i gorącym magazynie
Zapory usługi Azure Storage dla sieci wirtualnych  | Obsługiwane | Jeśli Ogranicz dostęp do sieci wirtualnej do kont magazynu, Włącz opcję [Zezwalaj na zaufane usługi firmy Microsoft](../storage/common/storage-network-security.md#exceptions).
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwa gorąca i chłodna) | Obsługiwane | Koszty transakcji rosną znacznie w porównaniu z kontami magazynu ogólnego przeznaczenia w wersji 1
Generacja 2 (rozruch UEFI) | Obsługiwane
Dyski interfejsu NVMe | Nieobsługiwane
Dyski udostępnione platformy Azure | Nieobsługiwane
Opcja bezpiecznego transferu | Obsługiwane
Dyski z włączonym akceleratorem zapisu | Nieobsługiwane
Tagi  | Obsługiwane | Tagi generowane przez użytkownika są replikowane co 24 godziny.

>[!IMPORTANT]
> Aby uniknąć problemów z wydajnością, należy się upewnić, że dla [dysków zarządzanych](../virtual-machines/disks-scalability-targets.md)są używane elementy docelowe skalowalności i wydajności dysków maszyny wirtualnej. Jeśli używasz ustawień domyślnych, Site Recovery tworzy wymagane dyski i konta magazynu na podstawie konfiguracji źródłowej. Jeśli dostosowujesz i wybierasz własne ustawienia, postępuj zgodnie z celami skalowalności i wydajności dysków dla źródłowych maszyn wirtualnych.

## <a name="limits-and-data-change-rates"></a>Limity i szybkości zmian danych

Poniższa tabela zawiera podsumowanie limitów Site Recovery.

- Limity te są oparte na naszych testach, ale oczywiście nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji.
- Rzeczywiste wyniki mogą się różnić w zależności od kombinacji operacji we/wy aplikacji.
- Istnieją dwa ograniczenia, które należy wziąć pod uwagę w przypadku zmian danych na dysku i zmian danych maszyny wirtualnej.
- Bieżący limit zmian danych maszyny wirtualnej to 54 MB/s, bez względu na rozmiar.

**Miejsce docelowe magazynu** | **Średnia liczba operacji we/wy dysku źródłowego** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk

## <a name="replicated-machines---networking"></a>Zreplikowane maszyny — sieć
**Ustawienie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Karta sieciowa | Maksymalna liczba obsługiwana dla określonego rozmiaru maszyny wirtualnej platformy Azure | Karty sieciowe są tworzone podczas pracy w trybie failover.<br/><br/> Liczba kart sieciowych na maszynie wirtualnej trybu failover zależy od liczby kart sieciowych na źródłowej maszynie wirtualnej, gdy replikacja została włączona. Po dodaniu lub usunięciu karty sieciowej po włączeniu replikacji nie ma to wpływu na liczbę kart sieciowych replikowanych maszyn wirtualnych po przejściu w tryb failover. <br/><br/> Kolejność kart sieciowych po przejściu w tryb failover nie gwarantuje, że jest taka sama jak oryginalna kolejność. <br/><br/> Można zmienić nazwy kart sieciowych w regionie docelowym na podstawie konwencji nazewnictwa w organizacji. Zmiana nazwy karty sieciowej jest obsługiwana przy użyciu programu PowerShell.
Internetowy moduł równoważenia obciążenia | Nieobsługiwane | W regionie podstawowym można skonfigurować usługi równoważenia obciążenia publicznego/Internetu. Usługi równoważenia obciążenia publicznego/Internetu nie są jednak obsługiwane przez Azure Site Recovery w regionie odzyskiwania po awarii.
Wewnętrzny moduł równoważenia obciążenia | Obsługiwane | Skojarz wstępnie skonfigurowany moduł równoważenia obciążenia za pomocą skryptu Azure Automation w planie odzyskiwania.
Publiczny adres IP | Obsługiwane | Skojarz istniejący publiczny adres IP z kartą sieciową. Lub Utwórz publiczny adres IP i skojarz go z kartą sieciową przy użyciu skryptu Azure Automation w planie odzyskiwania.
SIECIOWEJ grupy zabezpieczeń na karcie sieciowej | Obsługiwane | Skojarz sieciowej grupy zabezpieczeń z kartą sieciową przy użyciu skryptu Azure Automation w planie odzyskiwania.
SIECIOWEJ grupy zabezpieczeń w podsieci | Obsługiwane | Skojarz sieciowej grupy zabezpieczeń z podsiecią, używając skryptu Azure Automation w planie odzyskiwania.
Zarezerwowany (statyczny) adres IP | Obsługiwane | Jeśli karta sieciowa na źródłowej maszynie wirtualnej ma statyczny adres IP, a podsieć docelowa ma ten sam adres IP, jest on przypisany do maszyny wirtualnej przełączonej w tryb failover.<br/><br/> Jeśli podsieć docelowa nie ma dostępnego tego samego adresu IP, jeden z dostępnych adresów IP w podsieci jest zarezerwowany dla maszyny wirtualnej.<br/><br/> Można również określić stały adres IP i podsieć w ustawieniach **replikowanych elementów**  >  **Ustawienia** elementy  >  **obliczeniowe i**  >  **sieciowe**.
Dynamiczny adres IP | Obsługiwane | Jeśli karta sieciowa w źródle ma dynamiczne adresowanie IP, karta sieciowa na maszynie wirtualnej w trybie failover jest również domyślnie dynamiczna.<br/><br/> Jeśli jest to wymagane, można je zmodyfikować na stały adres IP.
Wiele adresów IP | Nieobsługiwane | Po przełączeniu maszyny wirtualnej w tryb failover, która ma kartę sieciową z wieloma adresami IP, zachowywany jest tylko podstawowy adres IP karty sieciowej w regionie źródłowym. Aby przypisać wiele adresów IP, można dodać maszyny wirtualne do [planu odzyskiwania](recovery-plan-overview.md) i dołączyć skrypt, aby przypisać dodatkowe adresy IP do planu, lub wprowadzić zmianę ręcznie lub za pomocą skryptu po przejściu do trybu failover.
Traffic Manager     | Obsługiwane | Można wstępnie skonfigurować Traffic Manager tak, aby ruch był kierowany do punktu końcowego w regionie źródłowym w regularnych odstępach czasu i do punktu końcowego w regionie docelowym w przypadku przejścia w tryb failover.
Azure DNS | Obsługiwane |
Niestandardowe DNS    | Obsługiwane |
Nieuwierzytelniony serwer proxy | Obsługiwane | [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Uwierzytelniony serwer proxy | Nieobsługiwane | Jeśli maszyna wirtualna używa uwierzytelnionego serwera proxy do łączności wychodzącej, nie może być replikowana przy użyciu Azure Site Recovery.
Połączenie sieci VPN typu lokacja-lokacja z lokalnym<br/><br/>(z lub bez ExpressRoute)| Obsługiwane | Upewnij się, że UDR i sieciowych grup zabezpieczeń są skonfigurowane w taki sposób, że ruch Site Recovery nie jest kierowany do lokalnego. [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Połączenie między sieciami WIRTUALNYmi    | Obsługiwane | [Dowiedz się więcej](./azure-to-azure-about-networking.md)
Punkty końcowe usługi dla sieci wirtualnej | Obsługiwane | W przypadku ograniczania dostępu do sieci wirtualnej do kont magazynu upewnij się, że zaufane usługi firmy Microsoft mają dostęp do konta magazynu.
Wydajniejsze sieci | Obsługiwane | Na źródłowej maszynie wirtualnej musi być włączona szybsza sieć. [Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).
Urządzenie sieciowe Palo Alto | Nieobsługiwane | W przypadku urządzeń innych firm istnieją często ograniczenia nakładane przez dostawcę w ramach maszyny wirtualnej. Azure Site Recovery wymaga, aby Agent, rozszerzenia i łączność wychodząca były dostępne. Ale urządzenie nie zezwala na skonfigurowanie żadnego działania wychodzącego w ramach maszyny wirtualnej.
Protokół IPv6  | Nieobsługiwane | Konfiguracje mieszane, które obejmują zarówno protokół IPv4, jak i IPv6, nie są również obsługiwane. Zwolnij podsieć zakresu adresów IPv6 przed jakąkolwiek operacją Site Recovery.
Prywatny dostęp do połączenia z usługą Site Recovery | Obsługiwane | [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tagi  | Obsługiwane | Tagi generowane przez użytkownika na kartach sieciowych są replikowane co 24 godziny.



## <a name="next-steps"></a>Następne kroki

- Odczytaj [wskazówki dotyczące sieci](./azure-to-azure-about-networking.md)  dotyczące replikacji maszyn wirtualnych platformy Azure.
- Wdróż odzyskiwanie po awarii przez [replikowanie maszyn wirtualnych platformy Azure](./azure-to-azure-quickstart.md).
