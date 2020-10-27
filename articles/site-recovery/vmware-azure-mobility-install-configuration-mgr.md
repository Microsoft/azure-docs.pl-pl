---
title: Automatyzacja usługi mobilności na potrzeby odzyskiwania po awarii instalacji w Azure Site Recovery
description: Sposób automatycznej instalacji usługi mobilności na potrzeby odzyskiwania po awarii serwera VMware/Physical Server z Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544821"
---
# <a name="automate-mobility-service-installation"></a>Automatyzowanie instalacji usługi mobilności

W tym artykule opisano sposób automatyzowania instalacji i aktualizacji agenta usługi mobilności w programie [Azure Site Recovery](site-recovery-overview.md).

Podczas wdrażania Site Recovery na potrzeby odzyskiwania po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure należy zainstalować agenta usługi mobilności na każdej maszynie, która ma być replikowana. Usługa mobilności przechwytuje operacje zapisu danych na komputerze i przekazuje je do serwera przetwarzania Site Recovery na potrzeby replikacji. Usługę mobilności można wdrożyć na kilka sposobów:

- **Instalacja wypychana** : Pozwól Site Recovery zainstalować agenta usługi mobilności po włączeniu replikacji dla maszyny w Azure Portal.
- **Instalacja ręczna** : należy ręcznie zainstalować usługę mobilności na poszczególnych komputerach. [Dowiedz się więcej](vmware-physical-mobility-service-overview.md) o instalacji wypychanej i ręcznej.
- **Zautomatyzowane wdrażanie** : Automatyzowanie instalacji przy użyciu narzędzi do wdrażania oprogramowania, takich jak Microsoft Endpoint Configuration Manager, lub narzędzi innych firm, takich jak JetPatch.

Automatyczne instalowanie i aktualizowanie zapewnia rozwiązanie, jeśli:

- Twoja organizacja nie zezwala na instalację wypychaną na serwerach chronionych.
- Zasady firmy wymagają okresowego zmieniania haseł. Musisz określić hasło instalacji wypychanej.
- Zasady zabezpieczeń nie zezwalają na dodawanie wyjątków zapory dla określonych maszyn.
- Działa jako dostawca usług hostingowych i nie chce podawać poświadczeń komputera klienta, które są potrzebne do instalacji wypychanej za pomocą Site Recovery.
- Konieczne jest skalowanie instalacji agentów na wielu serwerach jednocześnie.
- Chcesz zaplanować instalacje i uaktualnienia podczas planowanych okien obsługi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zautomatyzować instalację, potrzebne są następujące elementy:

- Wdrożone rozwiązanie do instalacji oprogramowania, takie jak [Configuration Manager](/configmgr/) lub [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Wymagania wstępne dotyczące wdrażania na [platformie Azure](tutorial-prepare-azure.md) i [lokalnie](vmware-azure-tutorial-prepare-on-premises.md) w przypadku odzyskiwania po awarii oprogramowania VMware lub na potrzeby odzyskiwania po awarii [serwera fizycznego](physical-azure-disaster-recovery.md) . Zapoznaj się z wymaganiami dotyczącymi [obsługi](vmware-physical-azure-support-matrix.md) odzyskiwania po awarii.

## <a name="prepare-for-automated-deployment"></a>Przygotowanie do automatycznego wdrażania

W poniższej tabeli zestawiono narzędzia i procesy służące do automatyzowania wdrażania usługi mobilności.

**Narzędzie** | **Szczegóły** | **Instrukcje**
--- | --- | ---
**Configuration Manager** | 1. Sprawdź, czy masz wymienione powyżej [wymagania wstępne](#prerequisites) . <br/><br/> 2. Wdróż odzyskiwanie po awarii przez skonfigurowanie środowiska źródłowego, w tym pobranie pliku komórki jajowe do wdrożenia serwera konfiguracji Site Recovery jako maszyny wirtualnej VMware przy użyciu szablonu OVF.<br/><br/> 3. Zarejestruj serwer konfiguracji w usłudze Site Recovery, skonfiguruj docelowe środowisko platformy Azure i skonfiguruj zasady replikacji.<br/><br/> 4. Aby wdrożyć Automatyczne wdrażanie usługi mobilności, należy utworzyć udział sieciowy zawierający hasło serwera konfiguracji i pliki instalacyjne usługi mobilności.<br/><br/> 5. utworzysz pakiet Configuration Manager zawierający instalację lub aktualizacje i przygotowuje się do wdrożenia usługi mobilności.<br/><br/> 6. następnie można włączyć replikację na platformie Azure dla maszyn, na których zainstalowano usługę mobilności. | [Automatyzacja za pomocą Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Sprawdź, czy masz wymienione powyżej [wymagania wstępne](#prerequisites) . <br/><br/> 2. Wdróż odzyskiwanie po awarii przez skonfigurowanie środowiska źródłowego, w tym pobieranie i wdrażanie Menedżera agentów JetPatch na potrzeby Azure Site Recovery w środowisku Site Recovery przy użyciu szablonu OVF.<br/><br/> 3. Zarejestruj serwer konfiguracji przy użyciu Site Recovery, skonfiguruj docelowe środowisko platformy Azure i skonfiguruj zasady replikacji.<br/><br/> 4. w przypadku automatycznego wdrażania zainicjuj i Ukończ konfigurację Menedżera agentów JetPatch.<br/><br/> 5. w programie JetPatch można utworzyć zasady Site Recovery, aby zautomatyzować wdrażanie i uaktualnianie agenta usługi mobilności. <br/><br/> 6. następnie można włączyć replikację na platformie Azure dla maszyn, na których zainstalowano usługę mobilności. | [Automatyzowanie przy użyciu Menedżera agentów JetPatch](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Rozwiązywanie problemów z instalacją agenta w programie JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatyzacja za pomocą Configuration Manager

### <a name="prepare-the-installation-files"></a>Przygotowywanie plików instalacyjnych

1. Upewnij się, że zostały spełnione wymagania wstępne.
1. Utwórz bezpieczny sieciowy udział plików (udział SMB), do którego można uzyskać dostęp na komputerze z uruchomionym serwerem konfiguracji.
1. W Configuration Manager wybierz [kategoryzację serwerów](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) , na których chcesz zainstalować lub zaktualizować usługę mobilności. Jedna kolekcja powinna zawierać wszystkie serwery z systemem Windows, inne serwery z systemem Linux.
1. W udziale sieciowym utwórz folder:

   - Aby zainstalować na maszynach z systemem Windows, należy utworzyć folder o nazwie _MobSvcWindows_ .
   - W przypadku instalacji na komputerach z systemem Linux Utwórz folder o nazwie _MobSvcLinux_ .

1. Zaloguj się na komputerze serwera konfiguracji.
1. Na komputerze serwera konfiguracji Otwórz wiersz polecenia z uprawnieniami administracyjnymi.
1. Aby wygenerować plik hasła, uruchom następujące polecenie:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Skopiuj plik _MobSvc. hasło_ do folderu systemu Windows i folderu Linux.
1. Aby przejść do folderu zawierającego pliki instalacyjne, uruchom następujące polecenie:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Skopiuj te pliki instalacyjne do udziału sieciowego:

   - W przypadku systemu Windows skopiuj _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ do _MobSvcWindows_ .
   - W przypadku systemu Linux Skopiuj następujące pliki do _MobSvcLinux_ :
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Zgodnie z opisem w poniższych procedurach Skopiuj kod do folderów systemu Windows lub Linux. Przyjęto założenie, że:

   - Adres IP serwera konfiguracji to `192.168.3.121` .
   - Bezpieczny sieciowy udział plików to `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Kopiuj kod do folderu systemu Windows

Skopiuj następujący kod:

- Zapisz kod w folderze _MobSvcWindows_ jako _install.bat_ .
- Zastąp `[CSIP]` symbole zastępcze w tym skrypcie wartościami rzeczywistymi adresu IP serwera konfiguracji.
- Skrypt obsługuje nowe instalacje agenta usługi mobilności i aktualizacje agentów, którzy są już zainstalowani.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kopiuj kod do folderu systemu Linux

Skopiuj następujący kod:

- Zapisz kod w folderze _MobSvcLinux_ jako _install_linux. sh_ .
- Zastąp `[CSIP]` symbole zastępcze w tym skrypcie wartościami rzeczywistymi adresu IP serwera konfiguracji.
- Skrypt obsługuje nowe instalacje agenta usługi mobilności i aktualizacje agentów, którzy są już zainstalowani.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Tworzenie pakietu

1. Zaloguj się do konsoli Configuration Manager i przejdź do pozycji **Biblioteka oprogramowania**  >  **pakiety zarządzania aplikacjami**  >  **Packages** .
1. Kliknij prawym przyciskiem myszy pozycję **pakiety**  >  **Utwórz pakiet** .
1. Podaj szczegóły pakietu, takie jak nazwa, opis, producent, język i wersja.
1. Wybierz opcję **ten pakiet zawiera pliki źródłowe** .
1. Kliknij przycisk **Przeglądaj** , a następnie wybierz udział sieciowy i folder zawierający odpowiedni Instalator ( _MobSvcWindows_ lub _MobSvcLinux_ ). Następnie wybierz pozycję **Dalej** .

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Na stronie **Wybierz typ programu, który chcesz utworzyć** wybierz pozycję **program standardowy**  >  **dalej** .

   ![Zrzut ekranu przedstawiający Kreatora tworzenia pakietu i programu, który wyświetla opcję programu standardowego.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Na stronie **Określanie informacji o tym programie standardowym** określ następujące wartości:

    **Parametr** | **Wartość systemu Windows** | **Wartość systemu Linux**
    --- | --- | ---
    **Nazwa** | Instalowanie usługi mobilności Microsoft Azure (Windows) | Zainstaluj usługę mobilności Microsoft Azure (Linux).
    **Wiersz polecenia** | install.bat | ./install_linux. sh
    **Program może zostać uruchomiony** | Bez względu na to, czy użytkownik jest zalogowany | Bez względu na to, czy użytkownik jest zalogowany
    **Inne parametry** | Użyj ustawienia domyślnego | Użyj ustawienia domyślnego

   ![Zrzut ekranu pokazujący informacje, które można określić dla programu standardowego.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. W obszarze **Określ wymagania dla tego programu standardowego** wykonaj następujące zadania:

   - W przypadku maszyn z systemem Windows wybierz opcję **ten program można uruchomić tylko na określonych platformach** . Następnie wybierz [obsługiwane systemy operacyjne Windows](vmware-physical-azure-support-matrix.md#replicated-machines) i kliknij przycisk **dalej** .
   - W przypadku maszyn z systemem Linux wybierz opcję **ten program można uruchomić na dowolnej platformie** . Następnie wybierz przycisk **Dalej** .

1. Zakończ pracę kreatora.

### <a name="deploy-the-package"></a>Wdrażanie pakietu

1. W konsoli Configuration Manager kliknij prawym przyciskiem myszy pakiet i wybierz polecenie **Dystrybuuj zawartość** .

   ![Zrzut ekranu przedstawiający konsolę Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Wybierz punkty dystrybucji, do których mają zostać skopiowane pakiety. [Dowiedz się więcej](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Wykonaj kroki kreatora. Pakiet rozpocznie replikację do określonych punktów dystrybucji.
1. Po zakończeniu dystrybucji pakietu kliknij prawym przyciskiem myszy pakiet > **Wdróż** .

   ![Zrzut ekranu przedstawiający konsolę Configuration Manager, w której znajduje się opcja menu Wdróż.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Wybierz utworzoną wcześniej kolekcję urządzeń z systemem Windows lub Linux.
1. Na stronie **Określanie miejsca docelowego zawartości** wybierz pozycję **punkty dystrybucji** .
1. Na stronie **Określanie ustawień w celu kontrolowania sposobu wdrażania tego oprogramowania** ustaw opcję **cel** na wartość **wymagane** .

   ![Zrzut ekranu przedstawiający Kreatora wdrażania oprogramowania](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. W obszarze **Określ harmonogram tego wdrożenia** Skonfiguruj harmonogram. [Dowiedz się więcej](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Usługa mobilności jest instalowana zgodnie z określonym harmonogramem.
   - Aby uniknąć niepotrzebnych ponownych uruchomień, zaplanuj instalację pakietu w trakcie miesięcznego okna obsługi lub aktualizacji oprogramowania.

1. Na stronie **punkty dystrybucji** Skonfiguruj ustawienia i Zakończ pracę kreatora.
1. Monitoruj postęp wdrażania w konsoli Configuration Manager. Przejdź do pozycji **monitorowanie**  >  **wdrożeń**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Odinstalowywanie usługi mobilności

Aby odinstalować usługę mobilności, można utworzyć pakiety Configuration Manager. Na przykład następujący skrypt Odinstalowuje usługę mobilności:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Następne kroki

[Włącz replikację](vmware-azure-enable-replication.md) dla maszyn wirtualnych.
