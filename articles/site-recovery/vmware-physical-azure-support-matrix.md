---
title: Macierz obsługi odzyskiwania po awarii VMware/fizycznej w usłudze Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych VMware i serwera fizycznego na platformie Azure przy użyciu usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: 02448754abd92eab9e095a5eaff10861f8b5e5e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606047"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure

W tym artykule podsumowano obsługiwane składniki i ustawienia odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

- [Dowiedz się więcej](vmware-azure-architecture.md) o architekturze odzyskiwania po awarii VMware VMware/server.
- Postępuj zgodnie z naszymi [samouczkami,](tutorial-prepare-azure.md) aby wypróbować odzyskiwanie po awarii.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

**Scenariusz** | **Szczegóły**
--- | ---
Odzyskiwanie po awarii maszyn wirtualnych VMware | Replikacja lokalnych maszyn wirtualnych VMware na platformie Azure. Ten scenariusz można wdrożyć w witrynie Azure portal lub przy użyciu programu [PowerShell.](vmware-azure-disaster-recovery-powershell.md)
Odzyskiwanie po awarii serwerów fizycznych | Replikacja lokalnych serwerów fizycznych systemu Windows/Linux na platformę Azure. Można wdrożyć ten scenariusz w witrynie Azure portal.

## <a name="on-premises-virtualization-servers"></a>Lokalne serwery wirtualizacji

**Serwer** | **Wymagania** | **Szczegóły**
--- | --- | ---
Program vCenter Server | Wersja 6.7, 6.5, 6.0 lub 5.5 | Zaleca się użycie serwera vCenter we wdrożeniu odzyskiwania po awarii.
Hosty vSphere | Wersja 6.7, 6.5, 6.0 lub 5.5 | Zaleca się, aby hosty vSphere i serwery vCenter znajdowały się w tej samej sieci co serwer przetwarzania. Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji. [Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Serwer konfiguracji odzyskiwania lokacji

Serwer konfiguracji jest komputerem lokalnym, na który są uruchamiane składniki usługi Site Recovery, w tym serwer konfiguracji, serwer przetwarzania i główny serwer docelowy.

- W przypadku maszyn wirtualnych VMware serwer konfiguracji można ustawić, pobierając szablon OVF w celu utworzenia maszyny wirtualnej VMware.
- W przypadku serwerów fizycznych komputer konfiguracyjny jest ręczny.

**Składnik** | **Wymagania**
--- |---
Rdzenie procesora CPU | 8
Pamięć RAM | 16 GB
Liczba dysków | 3 dyski<br/><br/> Dyski obejmują dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania w celu powrotu po awarii.
Wolne miejsce na dysku | 600 GB miejsca na pamięć podręczną serwera przetwarzania.
Wolne miejsce na dysku | 600 GB miejsca na dysk retencyjny.
System operacyjny  | Windows Server 2012 R2 lub Windows Server 2016 z pulpitem <br/><br> Jeśli planujesz użyć wbudowanego głównego obiektu docelowego tego urządzenia do powrotu po awarii, upewnij się, że wersja systemu operacyjnego jest taka sama lub wyższa niż elementy replikowane.|
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
[PowerCLI (WYCHOWA)](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nie jest potrzebny do konfiguracji serwera w wersji [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) lub nowszej.
Role systemu Windows Server | Nie włączaj Usług domenowych Active Directory; internetowych usług informacyjnych (IIS) lub funkcji Hyper-V.
Zasady grupy| - Uniemożliwić dostęp do wiersza polecenia. <br/> - Uniemożliwić dostęp do narzędzi do edycji rejestru. <br/> - Logika zaufania dla załączników plików. <br/> - Włącz wykonywanie skryptów. <br/> - [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Upewnij się, że:<br/><br/> - Nie masz wcześniej istniejącej domyślnej witryny <br/> - Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Nie masz wcześniej istniejącej strony internetowej / aplikacji nasłuchując na porcie 443<br/>
Typ karty sieciowej | VMXNET3 (po wdrożeniu jako maszyna wirtualna VMware)
Typ adresu IP | Statyczny
Porty | 443 używany do aranżacji kanału sterującego<br/>9443 dla transportu danych

## <a name="replicated-machines"></a>Zreplikowane maszyny

Usługa Site Recovery obsługuje replikację dowolnego obciążenia uruchomionego na obsługiwanym komputerze.

> [!Note]
> W poniższej tabeli wymieniono obsługę komputerów z rozruchem systemu BIOS. Aby uzyskać pomoc techniczną na komputerach opartych na UEFI, można znaleźć w sekcji [Magazynowanie.](#storage)

**Składnik** | **Szczegóły**
--- | ---
Ustawienia komputera | Maszyny replikowane na platformę Azure muszą spełniać [wymagania platformy Azure.](#azure-vm-requirements)
Obciążenie komputera | Usługa Site Recovery obsługuje replikację dowolnego obciążenia uruchomionego na obsługiwanym komputerze. [Dowiedz się więcej](https://aka.ms/asr_workload).
Nazwa maszyny | Upewnij się, że wyświetlana nazwa komputera nie należeć do [nazw zastrzeżonych zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name)<br/><br/> W nazwach woluminów logicznych nie rozróżnia się wielkość liter. Upewnij się, że żadne dwa woluminy na urządzeniu nie mają tej samej nazwy. Przykład: Woluminy o nazwach "voLUME1", "volume1" nie mogą być chronione za pośrednictwem usługi Azure Site Recovery.
Windows Server 2019 | Obsługiwane z [pakietu zbiorczego aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9.22 usługi mobilności) i dalej.
Windows Server 2016 64-bitowy | Obsługiwane dla Server Core, Server with Desktop Experience.
Windows Server 2012 R2 / Windows Server 2012 | Obsługiwane.
System Windows Server 2008 R2 z dodatkem SP1. | Obsługiwane.<br/><br/> Od wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności, trzeba [obsługi aktualizacji stosu (SSU)](https://support.microsoft.com/help/4490628) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowane na komputerach z systemem Windows 2008 R2 z sp1 lub nowszym. SHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://aka.ms/SHA-2KB).
Windows Server 2008 z sp2 lub nowszym (64-bitowy/32-bitowy) |  Obsługiwane tylko dla migracji. [Dowiedz się więcej](migrate-tutorial-windows-server-2008.md).<br/><br/> Od wersji [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności, trzeba [obsługi aktualizacji stosu (SSU)](https://support.microsoft.com/help/4493730) i [aktualizacji SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 2008 SP2. ISHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Obsługiwane.
Windows 7 z 64-bitowym sp1 | Obsługiwane z [pakietu zbiorczego aktualizacji 36](https://support.microsoft.com/help/4503156) (wersja 9.22 usługi mobilności) i dalej. </br></br> Od [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta usługi mobilności, trzeba [obsługi aktualizacji stosu (SSU)](https://support.microsoft.com/help/4490628) i aktualizacji [SHA-2](https://support.microsoft.com/help/4474419) zainstalowanych na komputerach z systemem Windows 7 SP1.  SHA-1 nie jest obsługiwany od września 2019 r., a jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami. Dowiedz się więcej o [aktualizacji SHA-2 i wymaganiach](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Linux | Obsługiwany jest tylko system 64-bitowy. System 32-bitowy nie jest obsługiwany.<br/><br/>Każdy serwer systemu Linux powinien mieć zainstalowane [składniki usług integracji systemu Linux (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Jest to wymagane do uruchomienia serwera na platformie Azure po przetestowaniu pracy awaryjnej/pracy awaryjnej. Jeśli brakuje składników LIS, upewnij się, że składniki zostaną zainstalowane [przed](https://www.microsoft.com/download/details.aspx?id=55106) włączeniem replikacji dla komputerów do rozruchu na platformie Azure. <br/><br/> Usługa Site Recovery organizuje przebłaję w pracy awaryjnej w celu uruchomienia serwerów systemu Linux na platformie Azure. Jednak dostawcy systemu Linux mogą ograniczyć obsługę tylko do wersji dystrybucyjnych, które nie osiągnęły końca reszty.<br/><br/> W dystrybucjach systemu Linux obsługiwane są tylko jądra akcji, które są częścią wersji/aktualizacji wersji pomocniczej dystrybucji.<br/><br/> Uaktualnianie chronionych maszyn w głównych wersjach dystrybucji systemu Linux nie jest obsługiwane. Aby uaktualnić, wyłącz replikację, uaktualnij system operacyjny, a następnie ponownie włącz replikację.<br/><br/> [Dowiedz się więcej](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) o obsłudze systemu Linux i technologii open source na platformie Azure.
Linux Red Hat Enterprise | od 5,2 do 5,11</b><br/> od 6,1 do 6,10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Serwery z systemem Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 nie mają wstępnie [zainstalowanych składników Usług integracji linuksa (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Upewnij się, że należy zainstalować [składniki](https://www.microsoft.com/download/details.aspx?id=55106) przed włączeniem replikacji dla maszyn do rozruchu na platformie Azure.
Linux: CentOS | od 5,2 do 5,11</b><br/> od 6,1 do 6,10</b><br/> od 7,0 do 7,6<br/> <br/> od 8,0 do 8,1<br/><br/> Serwery z systemem CentOS 5.2-5.11 & 6.1-6.10 nie mają wstępnie [zainstalowanych składników usług integracji systemu Linux (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Upewnij się, że należy zainstalować [składniki](https://www.microsoft.com/download/details.aspx?id=55106) przed włączeniem replikacji dla maszyn do rozruchu na platformie Azure.
Ubuntu | Serwer Ubuntu 14.04 LTS [(przegląd obsługiwanych wersji jądra)](#ubuntu-kernel-versions)<br/><br/>Serwer Ubuntu 16.04 LTS [(przegląd obsługiwanych wersji jądra)](#ubuntu-kernel-versions) </br> Serwer Ubuntu 18.04 LTS [(przegląd obsługiwanych wersji jądra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(recenzja obsługiwanych wersji jądra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(przegląd obsługiwanych wersji jądra)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(przegląd obsługiwanych wersji jądra)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 zw.<br/> Uaktualnianie zreplikowanych komputerów z dodatku SP3 dla systemu SUSE Linux Enterprise Server 11 do dodatku SP4 nie jest obsługiwane. Aby uaktualnić, należy wyłączyć replikację i ponownie włączyć po uaktualnieniu.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Uruchamianie jądra zgodnego z Red Hat lub unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Dla każdej z wersji systemu Windows usługa Azure Site Recovery obsługuje tylko [kompilacje kanału obsługi długoterminowej (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  [Półroczne](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) wydania kanału są obecnie nieobsługiwały w tej chwili.

### <a name="ubuntu-kernel-versions"></a>Wersje jądra Ubuntu

**Obsługiwane wydanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-ogólny do 3.13.0-170-rodzajowy,<br/>3.16.0-25-ogólny do 3.16.0-77-rodzajowy,<br/>3.19.0-18-ogólny do 3.19.0-80-rodzajowy,<br/>4.2.0-18-ogólny do 4.2.0-42-rodzajowy,<br/>4.4.0-21-ogólny do 4.4.0-148-rodzajowy,<br/>4.15.0-1023-azure do 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-ogólny do 4.4.0-171-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-74-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-ogólny do 4.4.0-170-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-72-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-ogólny do 4.4.0-166-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-66-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-ogólny do 4.4.0-164-rodzajowy,<br/>4.8.0-34-ogólny do 4.8.0-58-rodzajowy,<br/>4.10.0-14-ogólny do 4.10.0-42-rodzajowy,<br/>4.11.0-13-ogólny do 4.11.0-14-rodzajowy,<br/>4.13.0-16-ogólny do 4.13.0-45-rodzajowy,<br/>4.15.0-13-ogólny do 4.15.0-64-rodzajowy<br/>4.11.0-1009-azure do 4.11.0-1016-azure,<br/>4.13.0-1005-azure do 4.13.0-1018-azure <br/>4.15.0-1012-azure do 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-ogólny do 4.15.0-74-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-37-ogólny </br> 5.3.0-19-ogólny do 5.3.0-24-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1028-azure </br> 5.3.0-1007-azure do 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-ogólny do 4.15.0-72-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-37-ogólny </br> 5.3.0-19-ogólny do 5.3.0-24-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1025-azure </br> 5.3.0-1007-lazur|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-ogólny do 4.15.0-66-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-32-ogólny </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-ogólny do 4.15.0-62-rodzajowy </br> 4.18.0-13-ogólny do 4.18.0-25-rodzajowy </br> 5.0.0-15-ogólny do 5.0.0-27-rodzajowy </br> 4.15.0-1009-azure do 4.15.0-1037-azure </br> 4.18.0-1006-azure do 4.18.0-1025-azure </br> 5.0.0-1012-azure do 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Wersje jądra Debiana


**Obsługiwane wydanie** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| 3.2.0-4-amd64 do 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR] | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 do 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 do 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Obsługiwane jądra w systemie SUSE Linux Enterprise Server 12

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | Sp1 3.12.49-11-domyślnie 3.12.74-60.64.40 domyślnie</br></br> SP1(LTSS) 3.12.74-60.64.45 domyślnie dla 3.12.74-60.64.118 domyślnie</br></br> Sp2 4.4.21-69 domyślnie 4.4.120-92.70 domyślnie</br></br>SP2(LTSS) 4.4.121-92.73 domyślnie dla 4.4.121-92.117 domyślnie</br></br>Sp3 4.4.73-5 domyślnie dla 4.4.180-94.100-default</br></br>Sp3 4.4.138-4.7-azure do 4.4.180-4.31-azure</br></br>Sp4 4.12.14-94.41 domyślnie dla 4.12.14-95.29 domyślnie</br>Sp4 4.12.14-6.3-azure do 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | Sp1 3.12.49-11-domyślnie 3.12.74-60.64.40 domyślnie</br></br> SP1(LTSS) 3.12.74-60.64.45 domyślnie dla 3.12.74-60.64.115 domyślnie</br></br> Sp2 4.4.21-69 domyślnie 4.4.120-92.70 domyślnie</br></br>SP2(LTSS) 4.4.121-92.73 domyślnie dla 4.4.121-92.114 domyślnie</br></br>Sp3 4.4.73-5 domyślnie dla 4.4.180-94.97 domyślnie</br></br>Sp3 4.4.138-4.7-azure do 4.4.180-4.31-azure</br></br>Sp4 4.12.14-94.41 domyślnie dla 4.12.14-95.19 domyślnie</br>Sp4 4.12.14-6.3-azure do 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | Sp1 3.12.49-11-domyślnie 3.12.74-60.64.40 domyślnie</br></br> SP1(LTSS) 3.12.74-60.64.45 domyślnie dla 3.12.74-60.64.110 domyślnie</br></br> Sp2 4.4.21-69 domyślnie 4.4.120-92.70 domyślnie</br></br>SP2(LTSS) 4.4.121-92.73 domyślnie dla 4.4.121-92.109 domyślnie</br></br>Sp3 4.4.73-5 domyślnie dla 4.4.178-94.91 domyślnie</br></br>Sp3 4.4.138-4.7-azure do 4.4.178-4.28-azure</br></br>Sp4 4.12.14-94.41 domyślnie dla 4.12.14-95.16 domyślnie</br>Sp4 4.12.14-6.3-azure do 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | Sp1 3.12.49-11-domyślnie 3.12.74-60.64.40 domyślnie</br></br> SP1(LTSS) 3.12.74-60.64.45 domyślnie dla 3.12.74-60.64.107 domyślnie</br></br> Sp2 4.4.21-69 domyślnie 4.4.120-92.70 domyślnie</br></br>SP2(LTSS) 4.4.121-92.73 domyślnie dla 4.4.121-92.104 domyślnie</br></br>Sp3 4.4.73-5 domyślnie dla 4.4.176-94.88 domyślnie</br></br>Sp3 4.4.138-4.7-azure do 4.4.176-4.25-azure</br></br>Sp4 4.12.14-94.41 domyślnie dla 4.12.14-95.13 domyślnie</br>Sp4 4.12.14-6.3-azure do 4.12.14-6.9-azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 obsługiwane wersje jądra

**Release** | **Wersja usługi mobilności** | **Wersja jądra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 i 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Wszystkie zapasy SUSE 15 i 15 jądra są obsługiwane. </br></br> 4.12.14-5.5-lazurowe do 4.12.14-8.22-azure

## <a name="linux-file-systemsguest-storage"></a>Systemy plików Linux/pamięć gościnna

**Składnik** | **Obsługiwane**
--- | ---
Systemy plików | ext3, ext4, XFS, BTRFS (warunki obowiązujące zgodnie z niniejszą tabelą)
Menedżer woluminów | - LVM jest obsługiwany.<br/> - /boot na LVM jest obsługiwany z [aktualizacji pakietu zbiorczego 31](https://support.microsoft.com/help/4478871/) (wersja 9.20 usługi mobilności) i dalej. Nie jest obsługiwana we wcześniejszych wersjach usługi mobilności.<br/> - Wiele dysków systemu operacyjnego nie są obsługiwane.
Parawirualizowane urządzenia pamięci masowej | Urządzenia eksportowane przez sterowniki parawirtualne nie są obsługiwane.
Urządzenia we/wy bloków wielokanlatowych | Bez pomocy technicznej.
Serwery fizyczne z kontrolerem pamięci masowej HP CCISS | Bez pomocy technicznej.
Konwencja nazewnictwa punktów instalacji/instalacji | Nazwa urządzenia lub nazwa punktu instalacji powinny być unikatowe.<br/> Upewnij się, że żadne dwa urządzenia/punkty instalacji nie mają nazw z uwzględnieniem wielkości liter. Na przykład nazewnictwo urządzeń dla tej samej maszyny Wirtualnej jako *device1* i *Device1* nie jest obsługiwany.
Katalogi | Jeśli korzystasz z wersji usługi mobilności wcześniejszej niż wersja 9.20 (wydana w [zestawie zbiorczym aktualizacji 31),](https://support.microsoft.com/help/4478871/)obowiązują następujące ograniczenia:<br/><br/> - Te katalogi (jeśli są skonfigurowane jako oddzielne partycje/systemy plików) muszą znajdować się na tym samym dysku systemu operacyjnego na serwerze źródłowym: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - Katalog /boot powinien znajdować się na partycji dysku i nie być woluminem LVM.<br/><br/> Od wersji 9.20 ograniczenia te nie mają zastosowania. 
Katalog rozruchowy | - Dyski rozruchowe nie mogą być w formacie partycji GPT. Jest to ograniczenie architektury platformy Azure. Dyski GPT są obsługiwane jako dyski danych.<br/><br/> Wiele dysków rozruchowych na maszynie wirtualnej nie jest obsługiwanych<br/><br/> - /boot na woluminie LVM na więcej niż jednym dysku nie jest obsługiwany.<br/> - Nie można replikować komputera bez dysku rozruchowego.
Wymagania dotyczące wolnego miejsca| 2 GB na partycji /root <br/><br/> 250 MB w folderze instalacyjnym
XFSv5 | Funkcje XFSv5 w systemach plików XFS, takie jak suma kontrolna metadanych, są obsługiwane (usługa mobilności w wersji 9.10).<br/> Użyj narzędzia xfs_info, aby sprawdzić superblok XFS dla partycji. Jeśli `ftype` jest ustawiona na 1, funkcje XFSv5 są w użyciu.
Btrfs | BTRFS jest obsługiwany od [pakietu zbiorczego aktualizacji 34](https://support.microsoft.com/help/4490016) (wersja 9.22 usługi mobilności) i począwszy od. BTRFS nie jest obsługiwany, jeśli:<br/><br/> - Podvolume systemu plików BTRFS jest zmieniany po włączeniu ochrony.</br> - System plików BTRFS jest rozłożony na wiele dysków.</br> - System plików BTRFS obsługuje RAID.

## <a name="vmdisk-management"></a>Zarządzanie maszyną wirtualną/dyskiem

**Akcja** | **Szczegóły**
--- | ---
Ponowne rozmiary dysku na zreplikowanej maszynie wirtualnej | Obsługiwane na źródłowej maszynie wirtualnej przed przejściem w stan failover, bezpośrednio we właściwościach maszyny Wirtualnej. Nie ma potrzeby wyłączania/ponownego włączania replikacji.<br/><br/> Jeśli zmienisz źródłowej maszyny Wirtualnej po pracy awaryjnej, zmiany nie są przechwytywać.<br/><br/> Jeśli zmienisz rozmiar dysku na maszynie Wirtualnej platformy Azure po przejściu w stan failover, po zakończeniu pracy po awarii usługa Site Recovery utworzy nową maszynę wirtualną z aktualizacjami.
Dodawanie dysku na zreplikowanej maszynie wirtualnej | Bez pomocy technicznej.<br/> Wyłącz replikację maszyny Wirtualnej, dodaj dysk, a następnie włącz ponownie replikację.

## <a name="network"></a>Sieć

**Składnik** | **Obsługiwane**
--- | ---
Tworzenie zespołu sieci hostów sieci hostów | Obsługiwane dla maszyn wirtualnych VMware. <br/><br/>Nie jest obsługiwana w przypadku replikacji komputera fizycznego.
Sieć hostów VLAN | Tak.
Sieć hostów IPv4 | Tak.
Sieć hostów IPv6 | Nie.
Tworzenie zespołu sieci gości/serwerów | Nie.
Sieć gości/serwerów IPv4 | Tak.
Sieć gości/serwerów IPv6 | Nie.
Statyczny adres IP sieci gościa/serwera (Windows) | Tak.
Statyczny adres IP sieci gości/serwerów (Linux) | Tak. <br/><br/>Maszyny wirtualne są skonfigurowane do używania usługi DHCP po awarii.
Sieć gości/serwerów wiele kart sieciowych | Tak.


## <a name="azure-vm-network-after-failover"></a>Sieć maszyn wirtualnych platformy Azure (po przemijaniu awaryjnym)

**Składnik** | **Obsługiwane**
--- | ---
Azure ExpressRoute | Tak
Ilb | Tak
Elb | Tak
Azure Traffic Manager | Tak
Wiele kart sieciowych | Tak
Zastrzeżony adres IP | Tak
Protokół IPv4 | Tak
Zachowaj źródłowy adres IP | Tak
Punkty końcowe usługi dla sieci wirtualnej platformy Azure<br/> | Tak
Wydajniejsze sieci | Nie

## <a name="storage"></a>Magazyn
**Składnik** | **Obsługiwane**
--- | ---
Dysk dynamiczny | Dysk systemu operacyjnego musi być dyskiem podstawowym. <br/><br/>Dyski danych mogą być dyskami dynamicznymi
Konfiguracja dysku platformy Docker | Nie
NFS hosta | Tak dla VMware<br/><br/> Nie dla serwerów fizycznych
Host SAN (iSCSI/FC) | Tak
Host vSAN | Tak dla VMware<br/><br/> Nie dotyczy serwerów fizycznych
Wielościeżka hosta (MPIO) | Tak, testowany z systemem Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM dla CLARiiON
Woluminy wirtualne hosta (VVols) | Tak dla VMware<br/><br/> Nie dotyczy serwerów fizycznych
VMDK gościa/serwera | Tak
Udostępniony dysk klastrowy gościa/serwera | Nie
Dysk zaszyfrowany gościa/serwera | Nie
NFS gościa/serwera | Nie
ISCSI gościa/serwera | Dla migracji - Tak<br/>W przypadku odzyskiwania po awarii — nie, iSCSI powróci po awarii jako dołączony dysk do maszyny Wirtualnej
Gość/serwer SMB 3.0 | Nie
Serwer gości/serwer RDM | Tak<br/><br/> Nie dotyczy serwerów fizycznych
Dysk gościa/serwera > 1 TB | Tak, dysk musi być większy niż 1024 MB<br/><br/>Do 8192 GB podczas replikowania na dyskach zarządzanych (wersja 9.26)<br></br> Do 4095 GB podczas replikowania na konta magazynu
Dysk gość/serwer z logicznym 4K i rozmiarem sektora fizycznego 4k | Nie
Dysk gościa/serwera z rozmiarem sektora logicznego 4K i 512 bajtów | Nie
Wolumin gościa/serwera z rozłożonym dyskiem >4 TB <br/><br/>Logiczne zarządzanie woluminami (LVM)| Tak
Gość/serwer — miejsca do magazynowania | Nie
Dodawanie/usuwanie dysku przez gorąca serwer | Nie
Gość/serwer - wyklucz dysk | Tak
Wielościeżka gościa/serwera (MPIO) | Nie
Partycje GPT gościa/serwera | Pięć partycji są obsługiwane z [pakietu zbiorczego aktualizacji 37](https://support.microsoft.com/help/4508614/) (wersja 9.25 usługi mobilności) i począwszy od. Wcześniej wsparcie poparły cztery.
ReFS | Elastyczny system plików jest obsługiwany przez usługę mobilności w wersji 9.23 lub nowszej
Uruchamianie serwera gościa/serwera EFI/UEFI | - Obsługiwane dla systemów Windows Server 2012 lub nowszych, SLES 12 SP4 i RHEL 8.0 z agentem mobilności w wersji 9.30 i nowszych<br/> - Bezpieczny typ rozruchu UEFI nie jest obsługiwany.

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
Chłodne przechowywanie | Nie
Przechowywanie na gorąco| Nie
Blokowe obiekty blob | Nie
Szyfrowanie w spoczynku (SSE)| Tak
Szyfrowanie w spoczynku (CMK)| Tak (za pośrednictwem modułu PowerShell Az 3.3.0)
Premium Storage | Tak
Usługa importu/eksportu | Nie
Zapory usługi Azure Storage dla sieci wirtualnych | Tak.<br/> Skonfigurowany na docelowym koncie magazynu/pamięci podręcznej (używanym do przechowywania danych replikacji).
Konta magazynu ogólnego przeznaczenia w wersji 2 (warstwy gorące i chłodne) | Tak (koszty transakcji są znacznie wyższe dla V2 w porównaniu do V1)

## <a name="azure-compute"></a>Obliczenia platformy Azure

**Funkcja** | **Obsługiwane**
--- | ---
Zestawy dostępności | Tak
Strefy dostępności | Nie
Koncentratora | Tak
Dyski zarządzane | Tak

## <a name="azure-vm-requirements"></a>Wymagania dotyczące maszyny wirtualnej platformy Azure

Lokalne maszyny wirtualne replikowane na platformę Azure muszą spełniać wymagania maszyny wirtualnej platformy Azure podsumowane w tej tabeli. Gdy usługa Site Recovery uruchamia sprawdzanie wymagań wstępnych pod kątem replikacji, sprawdzanie zakończy się niepowodzeniem, jeśli niektóre wymagania nie są spełnione.

**Składnik** | **Wymagania** | **Szczegóły**
--- | --- | ---
System operacyjny gościa | Sprawdź [obsługiwane systemy operacyjne](#replicated-machines) dla komputerów replikowanych. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Architektura systemu operacyjnego gościa | 64-bitowy. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku systemu operacyjnego | Do 2048 GB. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków systemu operacyjnego | 1 | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Liczba dysków danych | 64 lub mniej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Rozmiar dysku danych | Do 8192 GB podczas replikowania na dysk zarządzany (wersja 9.26)<br></br>Do 4095 GB podczas replikowania do konta magazynu| Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Karty sieciowe | Obsługiwanych jest wiele kart. |
Udostępniony wirtualny dysk twardy | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
Dysk FC | Bez pomocy technicznej. | Sprawdź nie powiedzie się, jeśli nie jest nieobsługiwał.
BitLocker | Bez pomocy technicznej. | Funkcja BitLocker musi być wyłączona przed włączeniem replikacji komputera. |
Nazwa maszyny wirtualnej | Od 1 do 63 znaków.<br/><br/> Ograniczone do liter, cyfr i łączników.<br/><br/> Nazwa urządzenia musi zaczynać się i kończyć literą lub cyfrą. |  Zaktualizuj wartość we właściwościach komputera w odzyskiwaniu witryny.

## <a name="resource-group-limits"></a>Limity grup zasobów

Aby zrozumieć liczbę maszyn wirtualnych, które mogą być chronione w ramach jednej grupy zasobów, zapoznaj się z artykułem na temat [limitów subskrypcji i przydziałów](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Limity zmian

W poniższej tabeli przedstawiono limity usługi Azure Site Recovery.
- Limity te są oparte na naszych testach, ale nie obejmują wszystkich możliwych kombinacji we/wy aplikacji.
- Rzeczywiste wyniki mogą różnić w zależności od kombinacji operacji we/wy aplikacji.
- Aby uzyskać najlepsze wyniki, zdecydowanie zaleca się uruchomienie [narzędzia Planowanie wdrażania](site-recovery-deployment-planner.md)i przeprowadzenie szeroko zakrojonych testów aplikacji przy użyciu testowych testów pracy awaryjnej w celu uzyskania obrazu prawdziwej wydajności aplikacji.

**Miejsce docelowe replikacji** | **Średni rozmiar źródłowych operacji we/wy na dysku** |**Średni źródłowy współczynnik zmian danych na dysku** | **Łączny współczynnik zmian danych na dysku dziennie**
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
- Liczby te zakładają typowe zaległości około pięciu minut. Oznacza to, że przekazane dane są przetwarzane i punkt odzyskiwania jest tworzony w ciągu pięciu minut.

## <a name="vault-tasks"></a>Zadania przechowalni

**Akcja** | **Obsługiwane**
--- | ---
Przenoszenie przechowalni między grupami zasobów | Nie
Przenoszenie magazynu wewnątrz i między subskrypcjami | Nie
Przenoszenie pamięci masowej, sieci, maszyn wirtualnych platformy Azure między grupami zasobów | Nie
Przenoszenie pamięci masowej, sieci, maszyn wirtualnych platformy Azure w ramach subskrypcji i między subskrypcjami. | Nie


## <a name="obtain-latest-components"></a>Uzyskiwanie najnowszych komponentów

**Nazwa** | **Opis** | **Szczegóły**
--- | --- | ---
Serwer konfiguracji | Zainstalowany lokalnie.<br/> Koordynuje komunikację między lokalnymi serwerami VMware lub komputerami fizycznymi a platformą Azure. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwerze konfiguracji.<br/> - [Dowiedz się więcej o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uaktualnianiu do najnowszej wersji.<br/> - [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md) konfigurowaniu serwera konfiguracji.
Serwer przetwarzania | Domyślnie instalowany na serwerze konfiguracji.<br/> Odbiera dane replikacji, optymalizuje je za pomocą buforowania, kompresji i szyfrowania i wysyła je na platformę Azure.<br/> W miarę rozwoju wdrożenia można dodać dodatkowe serwery procesów do obsługi większych ilości ruchu replikacji. | - [Dowiedz się więcej o](vmware-physical-azure-config-process-server-overview.md) serwerze przetwarzania.<br/> - [Dowiedz się więcej o](vmware-azure-manage-process-server.md#upgrade-a-process-server) uaktualnianiu do najnowszej wersji.<br/> - [Dowiedz się więcej o](vmware-physical-large-deployment.md#set-up-a-process-server) konfigurowaniu serwerów procesów skalowanych w poziomie.
Usługa Mobility Service | Zainstalowany na maszynie wirtualnej VMware lub serwerach fizycznych, które chcesz replikować.<br/> Koordynuje replikację między lokalnymi serwerami VMware/serwerami fizycznymi a platformą Azure.| - [Dowiedz się więcej o](vmware-physical-mobility-service-overview.md) usłudze Mobilności.<br/> - [Dowiedz się więcej o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) uaktualnianiu do najnowszej wersji.<br/>



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
