---
title: Macierz obsługi dla oprogramowania VMware/fizycznego odzyskiwania po awarii w Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych programu VMware i serwera fizycznego na platformie Azure przy użyciu Azure Site Recovery.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: dead1d29392f203f5617c9caf430ff952f02f9bc
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317507"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure

W tym artykule opisano obsługiwane składniki i ustawienia odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

- [Dowiedz się więcej](vmware-azure-architecture.md) o architekturze odzyskiwania po awarii maszyny wirtualnej VMware/serwera fizycznego.
- Postępuj zgodnie z naszymi [samouczkami](tutorial-prepare-azure.md) , aby wypróbować odzyskiwanie po awarii.

> [!NOTE]
> Site Recovery nie przenosi ani nie zapisuje danych klienta poza regionem docelowym, w którym Konfiguracja odzyskiwania po awarii została skonfigurowana dla maszyn źródłowych. Klienci mogą wybrać magazyn Recovery Services z innego regionu, jeśli tak się wybierzesz. Magazyn Recovery Services zawiera metadane, ale nie dane rzeczywiste klienta.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

**Scenariusz** | **Szczegóły**
--- | ---
Odzyskiwanie po awarii maszyn wirtualnych VMware | Replikacja lokalnych maszyn wirtualnych programu VMware na platformę Azure. Ten scenariusz można wdrożyć w Azure Portal lub przy użyciu [programu PowerShell](vmware-azure-disaster-recovery-powershell.md).
Odzyskiwanie po awarii serwerów fizycznych | Replikacja lokalnych serwerów z systemem Windows/Linux do platformy Azure. Ten scenariusz można wdrożyć w Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Lokalne serwery wirtualizacji

**Server** (Serwer) | **Wymagania** | **Szczegóły**
--- | --- | ---
Program vCenter Server | W wersji 7,0 & kolejne aktualizacje w tej wersji, 6,7, 6,5, 6,0 lub 5,5 | Zalecamy używanie serwera vCenter w ramach wdrożenia odzyskiwania po awarii.
hosty vSphere | W wersji 7,0 & kolejne aktualizacje w tej wersji, 6,7, 6,5, 6,0 lub 5,5 | Zalecamy, aby hosty vSphere i serwery vCenter znajdowały się w tej samej sieci co serwer przetwarzania. Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. [Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md).

## <a name="site-recovery-configuration-server"></a>Serwer konfiguracji Site Recovery

Serwer konfiguracji to komputer lokalny, na którym są uruchamiane składniki Site Recovery, w tym serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.

- W przypadku maszyn wirtualnych VMware należy ustawić serwer konfiguracji, pobierając szablon OVF, aby utworzyć maszynę wirtualną VMware.
- W przypadku serwerów fizycznych należy ręcznie skonfigurować maszynę serwera konfiguracji.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | 3 dyski<br/><br/> Dyski obejmują dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca w pamięci podręcznej serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca na dysk przechowywania.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 z funkcją środowisko pulpitu <br/><br> Jeśli planujesz użycie wbudowanego głównego serwera docelowego tego urządzenia do powrotu po awarii, upewnij się, że wersja systemu operacyjnego jest taka sama lub wyższa niż zreplikowane elementy.|
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Niewymagane w przypadku serwera konfiguracji w wersji [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) lub nowszej.
Role systemu Windows Server | Nie włączaj Active Directory Domain Services; Internet Information Services (IIS) lub Hyper-V.
Zasady grupy| -Zapobiegaj dostępowi do wiersza polecenia. <br/> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br/> — Logika zaufania dla plików załączników. <br/> — Włącz wykonywanie skryptu. <br/> - [Dowiedz się więcej](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | Upewnij się, że:<br/><br/> -Nie ma wstępnie istniejącej domyślnej witryny sieci Web <br/> -Włącz  [uwierzytelnianie anonimowe](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br/> -Włącz ustawienie [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br/> -Nie masz wstępnie istniejącej witryny sieci Web/aplikacja nasłuchujący na porcie 443<br/>
Typ karty sieciowej | VMXNET3 (po wdrożeniu jako maszyny wirtualnej VMware)
Typ adresu IP | Static
Porty | 443 używane na potrzeby aranżacji kanału kontroli<br/>9443 do transportu danych

## <a name="replicated-machines"></a>Zreplikowane maszyny

Site Recovery obsługuje replikację wszystkich obciążeń uruchomionych na obsługiwanej maszynie.

**Składnik** | **Szczegóły**
--- | ---
Ustawienia maszyny | Maszyny, które są replikowane na platformę Azure, muszą spełniać [wymagania dotyczące platformy Azure](#azure-vm-requirements).
Obciążenie maszyny | Site Recovery obsługuje replikację wszystkich obciążeń uruchomionych na obsługiwanej maszynie. [Dowiedz się więcej](./site-recovery-workload.md).
Nazwa maszyny | Upewnij się, że nazwa wyświetlana maszyny nie należy do [nazw zarezerwowanych zasobów platformy Azure](../azure-resource-manager/templates/error-reserved-resource-name.md)<br/><br/> W nazwach woluminów logicznych nie jest rozróżniana wielkość liter. Upewnij się, że żadne dwa woluminy na urządzeniu nie mają tej samej nazwy. Przykład: woluminy z nazwami "voLUME1" i "voLUME1" nie mogą być chronione za pomocą Azure Site Recovery.

### <a name="for-windows"></a>W przypadku systemu Windows

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2019 | Obsługiwane przez [pakiet zbiorczy aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9,22 usługi mobilności) lub nowszy.
Windows Server 2016 64 — bit | Obsługiwane dla serwera Server Core, serwera z funkcją środowisko pulpitu.
Windows Server 2012 R2/Windows Server 2012 | Obsługiwane.
System Windows Server 2008 R2 z dodatkiem SP1 lub nowszym. | Obsługiwane.<br/><br/> W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności wymagana jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4490628) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na maszynach z systemem Windows 2008 R2 z dodatkiem SP1 lub nowszym. Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 z dodatkiem SP2 lub nowszym (64-bitowy/32-bitowy) |  Obsługiwane tylko w przypadku migracji. [Dowiedz się więcej](migrate-tutorial-windows-server-2008.md).<br/><br/> W wersji [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności wymagana jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4493730) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 2008 SP2. ISHA-1 nie jest obsługiwane z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Obsługiwany jest tylko system 64-bitowy. 32 — system bitowy nie jest obsługiwany.
Windows 7 z dodatkiem SP1 64 — bit | Obsługiwane przez [pakiet zbiorczy aktualizacji 36](https://support.microsoft.com/help/4503156) (wersja 9,22 usługi mobilności) lub nowszy. </br></br> Od [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności potrzebna jest obsługa aktualizacji [stosu (SSU)](https://support.microsoft.com/help/4490628) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 7 z dodatkiem SP1.  Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).

### <a name="for-linux"></a>W przypadku systemu Linux

**System operacyjny** | **Szczegóły**
--- | ---
Linux | Obsługiwany jest tylko system 64-bitowy. 32 — system bitowy nie jest obsługiwany.<br/><br/>Każdy serwer z systemem Linux powinien mieć zainstalowane [składniki systemu Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Jest to wymagane do uruchomienia serwera na platformie Azure po przetestowaniu trybu failover/przejścia w tryb failover. Jeśli brakuje wbudowanych składników LIS, przed włączeniem replikacji maszyn na platformie Azure upewnij się, że zostały zainstalowane [składniki](https://www.microsoft.com/download/details.aspx?id=55106) . <br/><br/> Site Recovery organizuje przejście w tryb failover na potrzeby uruchamiania serwerów z systemem Linux na platformie Azure. Jednak dostawcy systemu Linux mogą ograniczyć obsługę tylko wersji dystrybucji, które nie dotarły do końca cyklu życia.<br/><br/> W przypadku dystrybucji systemu Linux obsługiwane są tylko jądra, które są częścią wersji pomocniczej dystrybucji/aktualizacji.<br/><br/> Uaktualnianie chronionych maszyn w ramach głównych wersji dystrybucji systemu Linux nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację, Uaktualnij system operacyjny, a następnie ponownie Włącz replikację.<br/><br/> [Dowiedz się więcej](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) o pomocy technicznej dla systemów Linux i technologii open source na platformie Azure.
Linux Red Hat Enterprise | 5,2 do 5,11</b><br/> 6,1 do 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9, wersja beta](https://support.microsoft.com/help/4578241/), [7,9](https://support.microsoft.com/help/4590304/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/> Kilka starszych jądra na serwerach z systemem Red Hat Enterprise Linux 5.2 — 5.11 & 6.1-6.10 nie mają wstępnie zainstalowanych [składników usług integracji systemu Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Jeśli brakuje wbudowanych składników LIS, przed włączeniem replikacji maszyn na platformie Azure upewnij się, że zostały zainstalowane [składniki](https://www.microsoft.com/download/details.aspx?id=55106) .
Linux: CentOS | 5,2 do 5,11</b><br/> 6,1 do 6,10</b><br/> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/><br/> Niektóre starsze jądra na serwerach z systemem CentOS 5.2-5.11 & 6.1-6.10 nie mają wstępnie zainstalowanych  [składników usług integracji systemu Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Jeśli brakuje wbudowanych składników LIS, przed włączeniem replikacji maszyn na platformie Azure upewnij się, że zostały zainstalowane [składniki](https://www.microsoft.com/download/details.aspx?id=55106) .
Ubuntu | Ubuntu 14,04 * serwer LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions)<br/>Ubuntu 16,04 * serwer LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 * serwer LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions) </br> Ubuntu 20,04 * serwer LTS [(Przejrzyj obsługiwane wersje jądra)](#ubuntu-kernel-versions) </br> (*obejmuje obsługę wszystkich 14,04.* x *, 16,04.* x *, 18,04.* x *, 20,04.* x * wersje)
Debian | Debian 7/Debian 8 (obejmuje obsługę wszystkich 7. *x*, 8. *x* wersje); Debian 9 (w tym obsługa 9,1 do 9,13. Debian 9,0 nie jest obsługiwana.) [(Przejrzyj obsługiwane wersje jądra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 z dodatkiem SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(Przejrzyj obsługiwane wersje jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(Przejrzyj obsługiwane wersje jądra)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 z dodatkiem SP3. [Upewnij się, że na serwerze konfiguracji jest pobrany najnowszy Instalator agenta mobilności](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> SUSE Linux Enterprise Server 11 z dodatkiem SP4 </br> **Uwaga**: uaktualnianie zreplikowanych maszyn z programu SUSE Linux Enterprise Server 11 SP3 do wersji SP4 nie jest obsługiwane. Aby przeprowadzić uaktualnienie, wyłącz replikację i włącz ją ponownie po uaktualnieniu. <br/>|
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409/), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/), [8,2](https://support.microsoft.com/help/4573888/), [8,3](https://support.microsoft.com/help/4597409/)  <br/> Uruchamianie jądra zgodnego z systemem Red Hat lub nieprzerwane wydanie jądra 3, 4 & 5 (UEK3, UEK4, UEK5)<br/><br/>8.1<br/>Uruchamianie na wszystkich jądrach UEK i jądrze RedHat <= 3.10.0-1062. * są obsługiwane w [9,35](https://support.microsoft.com/help/4573888/) obsłudze dla pozostałych jądra RedHat jest dostępny w [9,36](https://support.microsoft.com/help/4578241/)

> [!Note]
>- Dla każdej wersji systemu Windows Azure Site Recovery obsługuje tylko kompilacje [LTSC (Long-Term Servicing Channel)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  W tej chwili nie są obecnie obsługiwane [półroczne wersje kanałów](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) .
>- Upewnij się, że w przypadku wersji systemu Linux Azure Site Recovery nie obsługuje niestandardowych obrazów systemów operacyjnych. Obsługiwane są tylko jądra podstawowe, które są częścią wersji pomocniczej dystrybucji/aktualizacji.

### <a name="ubuntu-kernel-versions"></a>Wersje jądra Ubuntu

**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14,04 LTS | [9,35](https://support.microsoft.com/help/4573888/), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/) | 3.13.0-24-ogólny do 3.13.0-170-Generic,<br/>3.16.0-25-ogólny do 3.16.0-77-Generic,<br/>3.19.0-18-Generic do 3.19.0-80-Generic,<br/>4.2.0-18-Generic do 4.2.0-42-Generic,<br/>4.4.0-21-Generic to 4.4.0-148-Generic,<br/>4.15.0-1023-Azure do 4.15.0-1045 — Azure |
|||
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-Generic to 4.4.0-194-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-123-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1098-Azure|
16,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-Generic to 4.4.0-190-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic do 4.15.0-118-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1096-Azure|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-Generic do 4.4.0-189-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-115-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1093-Azure |
16,04 LTS | [9,36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-Generic to 4.4.0-186-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-112-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1092-Azure |
16,04 LTS | [9,35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-Generic to 4.4.0-184-Generic,<br/>4.8.0-34-Generic do 4.8.0-58-Generic,<br/>4.10.0-14-Generic to 4.10.0-42-Generic,<br/>4.11.0-13-Generic do 4.11.0-14-Generic,<br/>4.13.0-16-ogólny do 4.13.0-45-Generic,<br/>4.15.0-13-Generic to 4.15.0-106-Generic<br/>4.11.0-1009-Azure to 4.11.0-1016-Azure,<br/>4.13.0-1005-Azure to 4.13.0-1018-Azure <br/>4.15.0-1012-Azure to 4.15.0-1089-Azure |
|||
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-ogólny do 4.15.0-123-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-63-Generic </br> 5.3.0-19-Generic do 5.3.0-69-Generic </br> 5.4.0-37-Generic to 5.4.0-53-Generic</br> 4.15.0-1009-Azure to 4.15.0-1099-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure to 5.4.0-1031-Azure|
18,04 LTS | [9,38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-Generic do 4.15.0-118-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-61-Generic </br> 5.3.0-19-Generic do 5.3.0-67-Generic </br> 5.4.0-37-Generic to 5.4.0-48-Generic</br> 4.15.0-1009-Azure to 4.15.0-1096-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure do 5.4.0-1026-Azure|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-ogólny do 4.15.0-115-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-60-Generic </br> 5.3.0-19-Generic do 5.3.0-66-Generic </br> 5.4.0-37-Generic to 5.4.0-45-Generic</br> 4.15.0-1009-Azure to 4.15.0-1093-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1035-Azure </br> 5.4.0-1020-Azure do 5.4.0-1023-Azure|
18,04 LTS | [9,36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-ogólny do 4.15.0-112-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-58-Generic </br> 5.3.0-19-Generic do 5.3.0-64-Generic </br> 5.4.0-37-Generic to 5.4.0-42-Generic</br> 4.15.0-1009-Azure to 4.15.0-1092-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1032-Azure </br> 5.4.0-1020-Azure to 5.4.0-1022-Azure|
18,04 LTS | [9,35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-ogólny do 4.15.0-108-Generic </br> 4.18.0-13-Generic to 4.18.0-25-Generic </br> 5.0.0-15-ogólny do 5.0.0-52-Generic </br> 5.3.0-19-Generic do 5.3.0-61-Generic </br> 4.15.0-1009-Azure to 4.15.0-1089-Azure </br> 4.18.0-1006-Azure do 4.18.0-1025-Azure </br> 5.0.0-1012-Azure to 5.0.0-1036-Azure </br> 5.3.0-1007-Azure to 5.3.0-1031-Azure|
|||
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-ogólny do 5.4.0-53 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1031-Azure
20,04 LTS |[9,38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-ogólny do 5.4.0-48 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1026-Azure
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-ogólny do 5.4.0-45 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1023-Azure
20,04 LTS |[9,36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-ogólny do 5.4.0-42 </br> -Generic 5.4.0-1010-Azure to 5.4.0-1022-Azure

### <a name="debian-kernel-versions"></a>Wersje jądra Debian


**Obsługiwana wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9,35](https://support.microsoft.com/help/4573888/), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/)| 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0 -0. BPO. 4-amd64 |
|||
Debian 8 | [9,35](https://support.microsoft.com/help/4573888/), [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/) | 3.16.0-4-amd64 do 3.16.0-11-amd64, 4.9.0 -0. BPO. 4-amd64 do 4.9.0 -0. BPO. 11-amd64 |
|||
Debian 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1 — amd64 do 4.9.0-14-amd64 </br> 4.19.0 -0. BPO. 1-amd64 do 4.19.0 -0. BPO. 12-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 do 4.19.0 -0. BPO. 12-Cloud-amd64 </br> Debian 9,1 | [9,38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 do 4.9.0-13-amd64 </br> 4.19.0 -0. BPO. 1-amd64 do 4.19.0 -0. BPO. 11-amd64 </br> 4.19.0 -0. BPO. 1-Cloud-amd64 do 4.19.0 -0. BPO. 11-Cloud-amd64 </br> 
Debian 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 do 4.9.0-13-amd64, 4.19.0 -0. BPO. 6-amd64 do 4.19.0 -0. BPO. 10-amd64, 4.19.0 -0. BPO. 6-Cloud-amd64 do 4.19.0 -0. BPO. 10-Cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 obsługiwanych wersji jądra

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.34 — Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,38](https://support.microsoft.com/help/4590304/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.28 — Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/),  | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.22 — Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,35](https://support.microsoft.com/help/4573888/) | Obsługiwane są wszystkie [Zapasy SUSE 12 SP1, SP2, SP3 i SP4](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.4.138-4,7-Azure do 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure do 4.12.14-6.43 — Azure </br> 4.12.14-16,7-Azure do 4.12.14-16.19 — Azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 obsługiwanych wersji jądra

**Wersja** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Domyślnie obsługiwane są wszystkie [jądra systemu SUSE 15 i 15](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.47 — Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure to 5.3.18-18.21-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,38](https://support.microsoft.com/help/4590304/)  | Domyślnie obsługiwane są wszystkie [jądra systemu SUSE 15 i 15](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.44 — Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure to 5.3.18-18.18-Azure
SUSE Linux Enterprise Server 15 i 15 SP1 | [9,36](https://support.microsoft.com/help/4578241/), [9,37](https://support.microsoft.com/help/4582666/)  | Domyślnie obsługiwane są wszystkie [jądra systemu SUSE 15 i 15](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.38 — Azure
SUSE Linux Enterprise Server 15 i 15 SP1 | [9,35](https://support.microsoft.com/help/4573888/)  | Domyślnie obsługiwane są wszystkie [jądra systemu SUSE 15 i 15](https://www.suse.com/support/kb/doc/?id=000019587) .</br></br> 4.12.14-5,5-Azure do 4.12.14-5.47 — Azure </br></br> 4.12.14-8,5-Azure do 4.12.14-8.33 — Azure 

## <a name="linux-file-systemsguest-storage"></a>Systemy plików Linux/magazyn gościa

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, EXT4, XFS, BTRFS (warunki odpowiednie dla tej tabeli)
Inicjowanie obsługi zarządzania woluminami logicznymi (LVM)| Grube udostępnianie — tak <br></br> Alokowanie elastyczne — nie
Menedżer woluminów | -LVM jest obsługiwana.<br/> -/Boot on LVM jest obsługiwany z [pakietu zbiorczego aktualizacji 31](https://support.microsoft.com/help/4478871/) (wersja 9,20 usługi mobilności) lub nowszej. Nie jest to obsługiwane we wcześniejszych wersjach usługi mobilności.<br/> — Wiele dysków systemu operacyjnego nie jest obsługiwanych.
Parawirtualne urządzeń magazynujących | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Wiele kolejek blokowych urządzeń we/wy | Nieobsługiwane.
Serwery fizyczne z kontrolerem magazynu HP CCISS | Nieobsługiwane.
Konwencja nazewnictwa urządzeń/punktów instalacji | Nazwa urządzenia lub nazwa punktu instalacji powinna być unikatowa.<br/> Upewnij się, że żadne dwa urządzenia/punkty instalacji nie mają nazw z rozróżnianiem wielkości liter. Na przykład nadawanie nazw urządzeniom dla tej samej maszyny wirtualnej jako *device1* i *device1* nie jest obsługiwane.
Katalogi | W przypadku korzystania z wersji usługi mobilności wcześniejszej niż wersja 9,20 (wydana w [pakiecie zbiorczym aktualizacji 31](https://support.microsoft.com/help/4478871/)) obowiązują następujące ograniczenia:<br/><br/> — Te katalogi (jeśli zostały skonfigurowane jako osobne partycje/systemy plików) muszą znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym:/(root),/Boot,/usr,/usr/local,/var,/etc.</br> -Katalog/Boot powinien znajdować się na partycji dysku, a nie na woluminie LVM.<br/><br/> Te ograniczenia nie mają zastosowania w wersji 9,20 lub nowszej. 
Katalog rozruchowy | -Dyski rozruchowe nie może być w formacie partycji GPT. Jest to ograniczenie architektury platformy Azure. Dyski GPT są obsługiwane jako dyski danych.<br/><br/> Wiele dysków rozruchowych na maszynie wirtualnej nie jest obsługiwanych<br/><br/> -/Boot na woluminie LVM na więcej niż jeden dysk nie jest obsługiwany.<br/> -Nie można zreplikować maszyny bez dysku rozruchowego.
Wymagania dotyczące wolnego miejsca| 2 GB na partycji/root <br/><br/> 250 MB w folderze instalacyjnym
XFSv5 | Obsługiwane są funkcje XFSv5 w systemach plików XFS, takie jak suma kontrolna metadanych (usługa mobilności w wersji 9,10 lub nowszej).<br/> Użyj narzędzia xfs_info, aby sprawdzić blok XFS dla partycji. Jeśli `ftype` jest ustawiona na 1, funkcje XFSv5 są używane.
BTRFS | BTRFS jest obsługiwana z [pakietu zbiorczego aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9,22 usługi mobilności) lub nowszej. BTRFS nie jest obsługiwane, jeśli:<br/><br/> -Wolumin systemu plików BTRFS jest zmieniany po włączeniu ochrony.</br> — System plików BTRFS jest rozłożony na wiele dysków.</br> — System plików BTRFS obsługuje macierz RAID.

## <a name="vmdisk-management"></a>Zarządzanie MASZYNami wirtualnymi/dyskami

**Akcja** | **Szczegóły**
--- | ---
Zmień rozmiar dysku na zreplikowanej maszynie wirtualnej | Obsługiwane przez źródłową maszynę wirtualną przed przełączeniem w tryb failover bezpośrednio we właściwościach maszyny wirtualnej. Nie trzeba wyłączać/ponownie włączać replikacji.<br/><br/> Zmiana źródłowej maszyny wirtualnej po przejściu do trybu failover powoduje, że zmiany nie będą przechwytywane.<br/><br/> W przypadku zmiany rozmiaru dysku maszyny wirtualnej platformy Azure po przejściu w tryb failover program Site Recovery tworzy nową maszynę wirtualną z aktualizacjami.
Dodawanie dysku do zreplikowanej maszyny wirtualnej | Nieobsługiwane.<br/> Wyłącz replikację dla maszyny wirtualnej, Dodaj dysk, a następnie ponownie Włącz replikację.

> [!NOTE]
> Jakakolwiek zmiana tożsamości dysku nie jest obsługiwana. Na przykład, jeśli partycjonowanie dysku zostało zmienione z GPT na MBR lub odwrotnie, spowoduje to zmianę tożsamości dysku. W takim scenariuszu replikacja zostanie przerwana i konieczne będzie skonfigurowanie nowej instalacji. 

## <a name="network"></a>Sieć

**Składnik** | **Obsługiwane**
--- | ---
Tworzenie zespołu kart interfejsu sieciowego hosta | Obsługiwane przez maszyny wirtualne VMware. <br/><br/>Nieobsługiwane w przypadku replikacji maszyn fizycznych.
Sieć VLAN hosta | Tak.
Adres IPv4 sieci hosta | Tak.
Adres IPv6 sieci hosta | Nie.
Tworzenie zespołu kart interfejsu sieciowego gościa/serwera | Nie.
Adres IPv4 sieci gościa/serwera | Tak.
Adres IPv6 sieci gościa/serwera | Nie.
Statyczny adres IP gościa/serwera (system Windows) | Tak.
Statyczny adres IP gościa/serwera (Linux) | Tak. <br/><br/>Maszyny wirtualne są skonfigurowane do korzystania z protokołu DHCP podczas powrotu po awarii.
Sieć gościa/serwer z wieloma kartami sieciowymi | Tak.
Prywatny dostęp do połączenia z usługą Site Recovery | Tak. [Dowiedz się więcej](hybrid-how-to-enable-replication-private-endpoints.md).


## <a name="azure-vm-network-after-failover"></a>Sieć maszyn wirtualnych platformy Azure (po przejściu w tryb failover)

**Składnik** | **Obsługiwane**
--- | ---
Azure ExpressRoute | Tak
ILB | Tak
ELB | Tak
Azure Traffic Manager | Tak
Wiele kart sieciowych | Tak
Adres Zastrzeżony adres IP | Tak
Protokół IPv4 | Tak
Zachowaj źródłowy adres IP | Tak
Punkty końcowe usługi dla sieci wirtualnej platformy Azure<br/> | Tak
Wydajniejsze sieci | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Dysk dynamiczny | Dysk systemu operacyjnego musi być dyskiem podstawowym. <br/><br/>Dyski danych mogą być dyskami dynamicznymi
Konfiguracja dysku platformy Docker | Nie
System plików NFS hosta | Tak dla oprogramowania VMware<br/><br/> Nie dla serwerów fizycznych
Sieć SAN hosta (iSCSI/FC) | Tak
Sieci vSAN hosta | Tak dla oprogramowania VMware<br/><br/> N/A dla serwerów fizycznych
Wielościeżkowa hosta (MPIO) | Tak, przetestowano przy użyciu modułu Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM dla CLARiiON
Woluminy wirtualne hosta (VVols) | Tak dla oprogramowania VMware<br/><br/> N/A dla serwerów fizycznych
Gość/serwer VMDK | Tak
Udostępniony dysk klastra gościa/serwera | Nie
Dysk szyfrowany gościa/serwera | Nie
System plików NFS gościa/serwera | Nie
Interfejs iSCSI gościa/serwera | Dla migracji — tak<br/>W przypadku odzyskiwania po awarii — nie, iSCSI spowoduje powrót po awarii jako dołączony dysk do maszyny wirtualnej
Gość/serwer SMB 3,0 | Nie
RDM gościa/serwera | Tak<br/><br/> N/A dla serwerów fizycznych
Dysk gościa/serwer > 1 TB | Tak, dysk musi być większy niż 1024 MB<br/><br/>Do 8 192 GB podczas replikowania na dyski zarządzane (wersja 9,26)<br></br> Do 4 095 GB podczas replikowania na konta magazynu
Dysk gościa/serwer z rozmiarem sektora fizycznego i 4 KB w kilobajtach | Nie
Dysk gościa/serwer z logicznymi i 512mi 4K rozmiar sektora fizycznego | Nie
Wolumin gościa/serwer z dyskiem rozłożonym >4 TB | Tak
Zarządzanie woluminami logicznymi (LVM)| Obsługa szerokiej alokacji — tak <br></br> Alokowanie elastyczne — nie
Gość/serwer — miejsca do magazynowania | Nie
Gość/serwer gorąca Dodaj/Usuń dysk | Nie
Gość/serwer — wykluczanie dysku | Tak
Wielościeżkowa gość/serwer (MPIO) | Nie
Partycje typu GPT/serwer | Z [pakietem zbiorczym aktualizacji 37](https://support.microsoft.com/help/4508614/) są obsługiwane pięć partycji (wersja 9,25 usługi mobilności). Cztery dawniej były obsługiwane.
ReFS | System plików jest odporny na błędy w wersji 9,23 lub nowszej
Gość/serwer EFI/rozruch UEFI | -Obsługiwane dla wszystkich [systemów operacyjnych Site Recovery w portalu Azure Marketplace](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace) z agentem mobilności w wersji 9,30 lub nowszej. <br/> -Bezpieczny typ rozruchu UEFI nie jest obsługiwany. [Dowiedz się więcej.](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>Kanały replikacji

|**Typ replikacji**   |**Obsługiwane**  |
|---------|---------|
|Odciążone transfery danych (ODX)    |       Nie  |
|Rozmieszczanie w trybie offline        |   Nie      |
| Azure Data Box | Nie

## <a name="azure-storage"></a>Azure Storage

**Składnik** | **Obsługiwane**
--- | ---
Magazyn lokalnie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy | Tak
Magazyn geograficznie nadmiarowy dostępny do odczytu | Tak
Chłodny magazyn | Nie
Magazyn gorąca| Nie
Blokowe obiekty blob | Nie
Szyfrowanie — w spoczynku (SSE)| Tak
Szyfrowanie na poziomie spoczynku (CMK)| Tak (za pośrednictwem programu PowerShell AZ 3.3.0 module lub nowszym)
Podwójne szyfrowanie w spoczynku | Tak (za pomocą programu PowerShell AZ 3.3.0 module). Dowiedz się więcej na temat obsługiwanych regionów dla [systemów Windows](../virtual-machines/windows/disk-encryption.md) i [Linux](../virtual-machines/linux/disk-encryption.md).
Premium Storage | Tak
Opcja bezpiecznego transferu | Tak
Usługa importu/eksportu | Nie
Zapory usługi Azure Storage dla sieci wirtualnych | Tak.<br/> Skonfigurowane na docelowym koncie magazynu/pamięci podręcznej (używane do przechowywania danych replikacji).
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwy gorąca i chłodna) | Tak (koszt transakcji jest znacznie wyższy dla wersji 2 w porównaniu do v1)

## <a name="azure-compute"></a>Obliczenia na platformie Azure

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Tak
Strefy dostępności | Nie
Centralny | Tak
Dyski zarządzane | Tak

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania dotyczące maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy Site Recovery uruchamia sprawdzanie wymagań wstępnych dotyczących replikacji, sprawdzenie zakończy się niepowodzeniem, jeśli niektóre wymagania nie zostaną spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź [obsługiwane systemy operacyjne](#replicated-machines) dla replikowanych maszyn. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Architektura systemu operacyjnego gościa | 64-bitowa. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku systemu operacyjnego | Do 2 048 GB. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków systemu operacyjnego | 1 </br> partycja rozruchowa i systemowa na różnych dyskach nie jest obsługiwana | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Liczba dysków danych | 64 lub mniej. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Rozmiar dysku danych | Do 8 192 GB podczas replikowania na dysk zarządzany (wersja 9,26)<br></br>Do 4 095 GB podczas replikowania na konto magazynu| Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Karty sieciowe | Obsługiwane są wiele kart. |
Udostępniony wirtualny dysk twardy | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Dysk FC | Nieobsługiwane. | Sprawdzanie kończy się niepowodzeniem, jeśli nie jest obsługiwane.
Funkcja BitLocker | Nieobsługiwane. | Aby włączyć replikację dla maszyny, należy wyłączyć funkcję BitLocker. |
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa maszyny musi rozpoczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w Site Recovery.

## <a name="resource-group-limits"></a>Limity grupy zasobów

Aby zrozumieć liczbę maszyn wirtualnych, które mogą być chronione w ramach jednej grupy zasobów, zapoznaj się z artykułem dotyczącym [limitów subskrypcji i przydziałów](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).

## <a name="churn-limits"></a>Limity zmian

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery.
- Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji operacji we/wy aplikacji.
- Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.
- Aby uzyskać najlepsze wyniki, zdecydowanie zalecamy uruchomienie [narzędzia planista wdrażania](site-recovery-deployment-planner.md)i przeprowadzenie testowania aplikacji przy użyciu testu pracy w trybie failover w celu uzyskania prawdziwego obrazu wydajności dla aplikacji.

**Lokalizacja docelowa replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 8 KB    | 2 MB/s | 168 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 16 KB | 4 MB/s |    336 GB na dysk
Dysk w warstwie Premium P10 lub P15 | 32 KB lub większy | 8 MB/s | 672 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 8 KB    | 5 MB/s | 421 GB na dysk
Dysk w warstwie Premium P20, P30, P40 lub P50 | 16 KB lub większy |20 MB/s | 1684 GB na dysk


**Źródłowy współczynnik zmian danych** | **Limit maksymalny**
---|---
Szczytowy współczynnik zmian danych na wszystkich dyskach na maszynie wirtualnej | 54 MB/s
Maksymalny współczynnik zmian danych dziennie obsługiwany przez serwer przetwarzania | 2 TB

- Są to średnie wartości przy założeniu 30-procentowego nakładania się operacji we/wy.
- Usługa Site Recovery może obsługiwać większą przepływność na podstawie zakresu nakładania się na siebie, większego rozmiaru operacji zapisu i rzeczywistego zachowania związanego z obciążeniem operacji we/wy.
- Te liczby zakładają typowy zaległości około pięciu minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="vault-tasks"></a>Zadania magazynu

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie magazynu między grupami zasobów | Nie
Przenoszenie magazynu w ramach subskrypcji i między subskrypcjami | Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów | Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure w ramach i między subskrypcjami. | Nie


## <a name="obtain-latest-components"></a>Uzyskaj najnowsze składniki

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | ---
Serwer konfiguracji | Zainstalowany lokalnie.<br/> Koordynuje komunikację między lokalnymi serwerami VMware lub maszynami fizycznymi i platformą Azure. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwerze konfiguracji.<br/> - [Dowiedz się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uaktualnianiu do najnowszej wersji.<br/> - [Dowiedz się więcej na temat](vmware-azure-deploy-configuration-server.md) konfigurowania serwera konfiguracji.
Serwer przetwarzania | Domyślnie instalowany na serwerze konfiguracji.<br/> Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do platformy Azure.<br/> Wraz z rozwojem wdrożenia można dodać kolejne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwerze przetwarzania.<br/> - [Dowiedz się więcej o](vmware-azure-manage-process-server.md#upgrade-a-process-server) uaktualnianiu do najnowszej wersji.<br/> - [Dowiedz się więcej o](vmware-physical-large-deployment.md#set-up-a-process-server) konfigurowaniu serwerów przetwarzania skalowalnego w poziomie.
Usługa Mobility Service | Zainstalowane na maszynie wirtualnej VMware lub serwerach fizycznych, które chcesz replikować.<br/> Koordynuje replikację między lokalnymi serwerami VMware/serwerami fizycznymi i platformą Azure.| - [Dowiedz się więcej o](vmware-physical-mobility-service-overview.md) usłudze mobilności.<br/> - [Dowiedz się więcej o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) uaktualnianiu do najnowszej wersji.<br/>



## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak](tutorial-prepare-azure.md) przygotować platformę Azure do odzyskiwania po awarii maszyn wirtualnych VMware.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery