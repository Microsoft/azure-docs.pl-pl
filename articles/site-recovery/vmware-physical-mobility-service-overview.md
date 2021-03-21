---
title: Informacje o usłudze mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych przy użyciu Azure Site Recovery | Microsoft Docs
description: Dowiedz się więcej o agencie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 8c90427e2dd4ecf23cb803c4f75dbee34c38f992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898583"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Informacje o usłudze mobilności dla maszyn wirtualnych VMware i serwerów fizycznych

Po skonfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware i serwerów fizycznych przy użyciu [Azure Site Recovery](site-recovery-overview.md)należy zainstalować usługę mobilności Site Recovery na wszystkich lokalnych maszynach wirtualnych VMware i na serwerze fizycznym. Usługa mobilności przechwytuje operacje zapisu danych na komputerze i przekazuje je do serwera przetwarzania Site Recovery. Usługa mobilności jest instalowana przez oprogramowanie agenta usługi mobilności, które można wdrożyć przy użyciu następujących metod:

- [Instalacja wypychana](#push-installation): gdy ochrona zostanie włączona za pośrednictwem Azure Portal, Site Recovery zainstaluje usługę mobilności na serwerze.
- Instalacja ręczna: można zainstalować usługę mobilności ręcznie na poszczególnych maszynach za pomocą [interfejsu użytkownika](#install-the-mobility-service-using-ui) lub [wiersza polecenia](#install-the-mobility-service-using-command-prompt).
- [Automatyczne wdrażanie](vmware-azure-mobility-install-configuration-mgr.md): można zautomatyzować instalację usługi mobilności za pomocą narzędzi do wdrażania oprogramowania, takich jak Configuration Manager.

> [!NOTE]
> Usługa mobilności używa około 6%-10% pamięci na maszynach źródłowych dla maszyn wirtualnych VMware lub maszyn fizycznych.

## <a name="antivirus-on-replicated-machines"></a>Program antywirusowy na replikowanych maszynach

Jeśli na maszynach, które mają być replikowane, działa oprogramowanie antywirusowe, należy wykluczyć folder instalacji usługi mobilności _C:\ProgramData\ASR\agent_ z operacji oprogramowania antywirusowego. To wykluczenie gwarantuje, że replikacja będzie działała zgodnie z oczekiwaniami.

## <a name="push-installation"></a>Instalacja wypychana

Instalacja wypychana jest integralną częścią zadania uruchamianego z Azure Portal, aby [włączyć replikację](vmware-azure-enable-replication.md#enable-replication). Po wybraniu zestawu maszyn wirtualnych do ochrony i włączenia replikacji serwer konfiguracji wypycha agenta usługi mobilności do serwerów, zainstaluje agenta i ukończy rejestrację agenta na serwerze konfiguracji.

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że spełniono wszystkie [wymagania wstępne](vmware-azure-install-mobility-service.md) instalacji wypychanej.
- Upewnij się, że wszystkie konfiguracje serwera spełniają kryteria w [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure](vmware-physical-azure-support-matrix.md).
- Począwszy od wersji 9,36, w przypadku SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 upewnij się, że [na serwerze konfiguracji i skalowalnym w poziomie serwerze przetwarzania jest dostępny](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server) najnowszy Instalator

Przepływ pracy instalacji wypychanej został opisany w następujących sekcjach:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agent usługi mobilności w wersji 9,23 lub nowszej

Aby uzyskać więcej informacji na temat wersji 9,23, zobacz [pakiet zbiorczy aktualizacji 35 dla Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Podczas instalacji wypychanej usługi mobilności wykonywane są następujące czynności:

1. Agent jest wypychany do maszyny źródłowej. Kopiowanie agenta na maszynę źródłową może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Odwiedź [nasze wskazówki](vmware-azure-troubleshoot-push-install.md) , aby rozwiązać problemy z błędami instalacji wypychanej.
1. Po pomyślnym skopiowaniu agenta na serwer jest przeprowadzane sprawdzanie wymagań wstępnych na serwerze.
   - Jeśli spełniono wszystkie wymagania wstępne, rozpocznie się instalacja.
   - Instalacja nie powiedzie się, jeśli nie spełniono co najmniej jednego z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) .
1. W ramach instalacji agenta Usługa kopiowania woluminów w tle (VSS) Provider for Azure Site Recovery jest zainstalowany. Dostawca usługi VSS jest używany do generowania punktów odzyskiwania spójnych na poziomie aplikacji. Jeśli instalacja dostawcy usługi VSS nie powiedzie się, ten krok zostanie pominięty, a Instalacja agenta będzie kontynuowana.
1. Jeśli instalacja agenta zakończy się pomyślnie, ale instalacja dostawcy VSS nie powiedzie się, stan zadania zostanie oznaczony jako **Ostrzeżenie**. Nie ma to wpływu na generowanie punktu odzyskiwania spójnego na poziomie awarii.

    - Aby wygenerować punkty odzyskiwania spójne z aplikacją, zapoznaj się z [naszymi wskazówkami](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) , aby przeprowadzić ręczną instalację dostawcy usługi VSS Site Recovery.
    - Aby nie generować punktów odzyskiwania spójnych na poziomie aplikacji, należy [zmodyfikować zasady replikacji](vmware-azure-set-up-replication.md#create-a-policy) w celu wyłączenia punktów odzyskiwania spójnych na poziomie aplikacji.

### <a name="mobility-service-agent-version-922-and-below"></a>Agent usługi mobilności w wersji 9,22 i starszej

1. Agent jest wypychany do maszyny źródłowej. Kopiowanie agenta na maszynę źródłową może zakończyć się niepowodzeniem z powodu wielu błędów środowiska. Zapoznaj się z [naszymi wskazówkami dotyczącymi](vmware-azure-troubleshoot-push-install.md) rozwiązywania problemów z instalacją wypychaną.
1. Po pomyślnym skopiowaniu agenta na serwer jest przeprowadzane sprawdzanie wymagań wstępnych na serwerze.
   - Jeśli spełniono wszystkie wymagania wstępne, rozpocznie się instalacja.
   - Instalacja nie powiedzie się, jeśli nie spełniono co najmniej jednego z [wymagań wstępnych](vmware-physical-azure-support-matrix.md) .

1. W ramach instalacji agenta Usługa kopiowania woluminów w tle (VSS) Provider for Azure Site Recovery jest zainstalowany. Dostawca usługi VSS jest używany do generowania punktów odzyskiwania spójnych na poziomie aplikacji.
   - Jeśli instalacja dostawcy VSS nie powiedzie się, Instalacja agenta zakończy się niepowodzeniem. Aby uniknąć niepowodzenia instalacji agenta, należy użyć [wersji 9,23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) lub nowszej w celu wygenerowania punktów odzyskiwania spójnych na poziomie awarii i ręcznej instalacji dostawcy usługi VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instalowanie usługi mobilności przy użyciu interfejsu użytkownika

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwera spełniają kryteria w [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure](vmware-physical-azure-support-matrix.md).
- [Zlokalizuj Instalatora](#locate-installer-files) systemu operacyjnego serwera.

>[!IMPORTANT]
> Nie używaj metody instalacji interfejsu użytkownika, jeśli replikujesz maszynę wirtualną usługi Azure Infrastructure as (IaaS) z jednego regionu platformy Azure do innego. Użyj instalacji [wiersza polecenia](#install-the-mobility-service-using-command-prompt) .

1. Skopiuj plik instalacyjny na komputer i uruchom go.
1. W obszarze **Instalacja** wybierz opcję **Zainstaluj usługę mobilności**.
1. Wybierz lokalizację instalacji i wybierz pozycję **Zainstaluj**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Strona opcji instalacji usługi mobilności.":::

1. Monitoruj instalację w **trakcie instalacji**. Po zakończeniu instalacji wybierz pozycję **Kontynuuj do konfiguracji** , aby zarejestrować usługę na serwerze konfiguracji.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Zrzut ekranu pokazujący postęp instalacji i aktywny przycisk Zastosuj do konfiguracji po zakończeniu instalacji.":::

1. W obszarze **Szczegóły serwera konfiguracji** Określ adres IP i hasło, które zostały skonfigurowane.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Strona rejestracji usługi mobilności.":::

1. Wybierz pozycję **zarejestruj** , aby zakończyć rejestrację.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Ostatnia strona rejestracji usługi mobilności.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instalowanie usługi mobilności przy użyciu wiersza polecenia

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że wszystkie konfiguracje serwera spełniają kryteria w [macierzy pomocy technicznej dotyczące odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure](vmware-physical-azure-support-matrix.md).
- [Zlokalizuj Instalatora](#locate-installer-files) systemu operacyjnego serwera.

### <a name="windows-machine"></a>Komputer z systemem Windows

- W wierszu polecenia Uruchom następujące polecenia, aby skopiować Instalatora do folderu lokalnego, takiego jak _C:\Temp_, na serwerze, który ma być chroniony. Zastąp nazwę pliku Instalatora rzeczywistą nazwą pliku.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Uruchom to polecenie, aby zainstalować agenta.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Uruchom te polecenia, aby zarejestrować agenta na serwerze konfiguracji.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Ustawienia instalacji

Ustawienie | Szczegóły
--- | ---
Składnia | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Dzienniki instalacji | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Obowiązkowy parametr instalacji. Określa, czy należy zainstalować usługę mobilności (MS), czy główny cel (MT).
`/InstallLocation`| Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności (dowolny folder).
`/Platform` | Obowiązkowy. Określa platformę, na której zainstalowano usługę mobilności: <br/> **Oprogramowanie VMware** dla maszyn wirtualnych VMware/serwerów fizycznych. <br/> **Platforma Azure** dla maszyn wirtualnych platformy Azure.<br/><br/> W przypadku traktowania maszyn wirtualnych platformy Azure jako maszyn fizycznych należy określić program **VMware**.
`/Silent`| Opcjonalny. Określa, czy Instalator ma być uruchamiany w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji

Ustawienie | Szczegóły
--- | ---
Składnia | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Dzienniki konfiguracji agenta | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Obowiązkowy parametr. `<CSIP>` Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
`/PassphraseFilePath` |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki UNC lub pliku lokalnego.

### <a name="linux-machine"></a>Komputer z systemem Linux

1. W sesji terminalu Skopiuj Instalator do folderu lokalnego, takiego jak _/tmp_ na serwerze, który ma być chroniony. Zastąp nazwę pliku Instalatora rzeczywistą nazwą pliku dystrybucji systemu Linux, a następnie uruchom polecenia.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Zainstaluj w następujący sposób:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Po zakończeniu instalacji usługa mobilności musi być zarejestrowana na serwerze konfiguracji. Uruchom następujące polecenie, aby zarejestrować usługę mobilności na serwerze konfiguracji.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Ustawienia instalacji

Ustawienie | Szczegóły
--- | ---
Składnia | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Obowiązkowy parametr instalacji. Określa, czy należy zainstalować usługę mobilności (MS), czy główny cel (MT).
`-d` | Parametr opcjonalny. Określa lokalizację instalacji usługi mobilności: `/usr/local/ASR` .
`-v` | Obowiązkowy. Określa platformę, na której zainstalowano usługę mobilności. <br/> **Oprogramowanie VMware** dla maszyn wirtualnych VMware/serwerów fizycznych. <br/> **Platforma Azure** dla maszyn wirtualnych platformy Azure.
`-q` | Opcjonalny. Określa, czy Instalator ma być uruchamiany w trybie dyskretnym.

#### <a name="registration-settings"></a>Ustawienia rejestracji

Ustawienie | Szczegóły
--- | ---
Składnia | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Obowiązkowy parametr. `<CSIP>` Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
`-P` |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisano hasło. Użyj dowolnego prawidłowego folderu.

## <a name="azure-virtual-machine-agent"></a>Agent maszyny wirtualnej platformy Azure

- **Maszyny wirtualne z systemem Windows**: z wersji 9.7.0.0 usługi mobilności [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) jest instalowany przez Instalatora usługi mobilności. Dzięki temu w przypadku przełączenia maszyny do trybu failover na platformie Azure maszyna wirtualna platformy Azure spełnia wymagania wstępne instalacji agenta dotyczące korzystania z dowolnego rozszerzenia maszyny wirtualnej.
- **Maszyny wirtualne z systemem Linux**:  [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) należy instalować ręcznie na maszynie wirtualnej platformy Azure po przejściu do trybu failover.

## <a name="locate-installer-files"></a>Lokalizowanie plików Instalatora

Na serwerze konfiguracji przejdź do folderu _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Sprawdź, jakiego Instalatora potrzebujesz w zależności od systemu operacyjnego. Poniższa tabela zawiera podsumowanie plików Instalatora dla każdej maszyny wirtualnej VMware i systemu operacyjnego serwera fizycznego. Przed rozpoczęciem można przejrzeć [obsługiwane systemy operacyjne](vmware-physical-azure-support-matrix.md#replicated-machines).

> [!NOTE]
> Nazwy plików używają składni pokazanej w poniższej tabeli z _wersjami_ i _datami_ jako symbolami zastępczymi dla rzeczywistych wartości. Rzeczywiste nazwy plików będą wyglądać podobnie jak w następujących przykładach:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Plik Instalatora | System operacyjny (tylko 64-bitowy)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 z dodatkiem R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[Do pobrania i umieszczenia w tym folderze ręcznie](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 z dodatkiem SP1 </br> Obejmuje dodatki SP2 i SP3.
[Do pobrania i umieszczenia w tym folderze ręcznie](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 z dodatkiem SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 z dodatkiem SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6,4 </br> Oracle Enterprise Linux 6,5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14,04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16,04 LTS Server
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux 18,04 LTS Server
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux 20,04 LTS Server
[Do pobrania i umieszczenia w tym folderze ręcznie](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>Pobierz najnowszą wersję Instalatora agenta mobilności dla programu SUSE 11 SP3, RHEL 5, Debian 7

### <a name="suse-11-sp3-server"></a>Serwer SUSE 11 z dodatkiem SP3

Jako **warunek wstępny do aktualizacji lub ochrony maszyn SUSE Linux Enterprise Server 11 z dodatkiem SP3** z wersji 9,36 lub nowszej:

1. Upewnij się, że najnowszy Instalator agenta mobilności został pobrany z centrum pobierania firmy Microsoft i umieszczony w repozytorium instalacji wypychanej na serwerze konfiguracji i na wszystkich serwerach procesów skalowania w poziomie
2. [Pobierz](site-recovery-whats-new.md) najnowszą wersję Instalatora agenta programu SUSE Linux Enterprise Server 11 z dodatkiem SP3.
3. Przejdź do serwera konfiguracji, skopiuj instalatora agenta SUSE Linux Enterprise Server 11 SP3 na ścieżce INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Po skopiowaniu najnowszego Instalatora Uruchom ponownie usługę InMage PushInstall. 
1. Teraz przejdź do skojarzonych serwerów przetwarzania skalowalnego w poziomie, powtórz kroki 3 i 4.
1. **Na przykład** Jeśli ścieżka instalacji to C:\Program Files (x86) \Microsoft Azure Site Recovery, powyższe wyżej wymienione katalogi będą
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>Serwer RHEL 5 lub CentOS 5

Jako **warunek wstępny do aktualizowania lub ochrony RHEL 5 maszyn** z wersji 9,36 lub nowszej:

1. Upewnij się, że najnowszy Instalator agenta mobilności został pobrany z centrum pobierania firmy Microsoft i umieszczony w repozytorium instalacji wypychanej na serwerze konfiguracji i na wszystkich serwerach procesów skalowania w poziomie
2. [Pobierz](site-recovery-whats-new.md) najnowszą wersję Instalatora agenta programu RHEL 5 lub CentOS 5. 
3. Przejdź do serwera konfiguracji, skopiuj instalatora agenta RHEL 5 lub CentOS 5 na ścieżce-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Po skopiowaniu najnowszego Instalatora Uruchom ponownie usługę InMage PushInstall. 
1. Teraz przejdź do skojarzonych serwerów przetwarzania skalowalnego w poziomie, powtórz kroki 3 i 4.
1. **Na przykład** Jeśli ścieżka instalacji to C:\Program Files (x86) \Microsoft Azure Site Recovery, powyższe wyżej wymienione katalogi będą
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Serwer Debian 7

Jako **warunek wstępny do aktualizowania lub ochrony maszyn Debian 7** w wersji 9,36 lub nowszej:

1. Upewnij się, że najnowszy Instalator agenta mobilności został pobrany z centrum pobierania firmy Microsoft i umieszczony w repozytorium instalacji wypychanej na serwerze konfiguracji i na wszystkich serwerach procesów skalowania w poziomie
2. [Pobierz](site-recovery-whats-new.md) najnowszą wersję Instalatora agenta programu Debian 7.
3. Przejdź do serwera konfiguracji, skopiuj instalatora agenta Debian 7 na ścieżce INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Po skopiowaniu najnowszego Instalatora Uruchom ponownie usługę InMage PushInstall. 
1. Teraz przejdź do skojarzonych serwerów przetwarzania skalowalnego w poziomie, powtórz kroki 3 i 4.
1. **Na przykład** Jeśli ścieżka instalacji to C:\Program Files (x86) \Microsoft Azure Site Recovery, powyższe wyżej wymienione katalogi będą
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj instalację wypychaną dla usługi mobilności](vmware-azure-install-mobility-service.md).
