---
title: Instalowanie pakietu Office na głównym obrazie wirtualnego dysku twardego — Azure
description: Jak zainstalować i dostosować pakiet Office na głównym obrazie pulpitu wirtualnego systemu Windows na platformie Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326052"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalowanie pakietu Office w głównym obrazie wirtualnego dysku twardego

W tym artykule opisano sposób instalowania aplikacji Microsoft 365 dla przedsiębiorstw, usługi OneDrive i innych typowych aplikacji na głównym obrazie wirtualnego dysku twardego (VHD) na potrzeby przekazywania na platformę Azure. Jeśli użytkownicy muszą uzyskać dostęp do niektórych aplikacji biznesowych, zalecamy ich zainstalowanie po zakończeniu instrukcji przedstawionych w tym artykule.

W tym artykule przyjęto założenie, że utworzono już maszynę wirtualną (VM). Jeśli nie, zobacz [Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md#create-a-vm) .

W tym artykule przyjęto również, że masz podwyższony poziom dostępu do maszyny wirtualnej, niezależnie od tego, czy jest on zainicjowany na platformie Azure, czy w Menedżerze funkcji Hyper-V. Jeśli nie, zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Te instrukcje dotyczą konfiguracji specyficznej dla pulpitu wirtualnego systemu Windows, która może być używana z istniejącymi procesami w organizacji.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalowanie pakietu Office w trybie aktywacji komputera udostępnionego

Aktywacja na komputerze udostępnionym umożliwia wdrażanie aplikacji Microsoft 365 dla przedsiębiorstw na komputerze w organizacji, do którego dostęp jest uzyskiwany przez wielu użytkowników. Aby uzyskać więcej informacji na temat aktywacji komputera udostępnionego, zobacz [Omówienie aktywacji na komputerze udostępnionym dla aplikacji Microsoft 365](/deployoffice/overview-shared-computer-activation).

Zainstaluj pakiet Office za pomocą [narzędzia wdrażania pakietu Office](https://www.microsoft.com/download/details.aspx?id=49117) . Wiele sesji systemu Windows 10 Enterprise obsługuje tylko następujące wersje pakietu Office:

   - Aplikacje platformy Microsoft 365 dla przedsiębiorstw
   - Microsoft 365 aplikacje dla firm, które są dostarczane z subskrypcją usługi Microsoft 365 Business Premium

Narzędzie wdrażania pakietu Office wymaga pliku XML konfiguracji. Aby dostosować Poniższy przykład, zobacz [Opcje konfiguracji narzędzia do wdrażania pakietu Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Ten przykładowy plik XML konfiguracji wykona następujące czynności:

   - Zainstaluj pakiet Office z miesięcznego kanału przedsiębiorstwa i dostarczaj aktualizacje w miesięcznym kanale przedsiębiorstwa.
   - Użyj architektury x64.
   - Wyłącz aktualizacje automatyczne.
   - Usuń wszystkie istniejące instalacje pakietu Office i Migruj ich ustawienia.
   - Włącz aktywację komputera udostępnionego.

>[!NOTE]
>Funkcja wyszukiwania wzornika programu Visio może nie zadziałać zgodnie z oczekiwaniami w programie Virtual Desktop systemu Windows.

Oto co to jest przykładowy kod XML konfiguracji:

   - Instalowanie programu Skype dla firm
   - Zainstaluj usługę OneDrive w trybie dla użytkownika. Aby dowiedzieć się więcej, zobacz [Instalowanie usługi OneDrive w trybie dla komputera](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktywację komputera udostępnionego można skonfigurować za zasady grupy obiektów zasad grupy (GPO) lub ustawień rejestru. Obiekt zasad grupy znajduje się w obszarze **zasady konfiguracji komputera \\ \\ Szablony administracyjne \\ Microsoft Office 2016 (maszyny) \\ Ustawienia licencjonowania**

Narzędzie wdrażania pakietu Office zawiera setup.exe. Aby zainstalować pakiet Office, uruchom następujące polecenie w wierszu polecenia:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Przykład configuration.xml

Poniższy przykład kodu XML zainstaluje comiesięczną wersję Enterprise Channel.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Zespół biura zaleca używanie instalacji 64-bitowej dla parametru **OfficeClientEdition** .

Po zainstalowaniu pakietu Office można zaktualizować domyślne zachowanie biura. Uruchom następujące polecenia pojedynczo lub w pliku wsadowym, aby zaktualizować zachowanie.

```cmd
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instalowanie usługi OneDrive w trybie dla komputera

Usługa OneDrive jest zwykle instalowana dla poszczególnych użytkowników. W tym środowisku należy zainstalować na komputerze.

Poniżej przedstawiono sposób instalowania usługi OneDrive w trybie na maszynę:

1. Najpierw Utwórz lokalizację, aby przemieścić Instalatora usługi OneDrive. Lokalizacja folderu dysku lokalnego lub [ \\ \\ UNC] (File://UNC) jest odpowiednia.

2. Pobierz OneDriveSetup.exe do lokalizacji przygotowanej za pomocą tego linku: <https://aka.ms/OneDriveWVD-Installer>

3. Jeśli zainstalowano pakiet Office z usługą OneDrive **\<ExcludeApp ID="OneDrive" /\>** , należy odinstalować wszystkie istniejące instalacje dla poszczególnych użytkowników z poziomu wiersza polecenia z podwyższonym poziomem uprawnień, uruchamiając następujące polecenie:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Uruchom to polecenie w wierszu polecenia z podwyższonym poziomem uprawnień, aby ustawić wartość rejestru **AllUsersInstall** :

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Uruchom to polecenie, aby zainstalować usługę OneDrive w trybie dla komputera:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Uruchom to polecenie, aby skonfigurować usługę OneDrive do uruchamiania przy logowaniu dla wszystkich użytkowników:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Włącz **dyskretnie Skonfiguruj konto użytkownika** , uruchamiając następujące polecenie.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Przekieruj i Przenieś znane foldery systemu Windows do usługi OneDrive, uruchamiając następujące polecenie.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams i Skype dla firm

Pulpit wirtualny systemu Windows nie obsługuje programu Skype dla firm.

Aby uzyskać pomoc dotyczącą instalowania programu Microsoft Teams, zobacz Korzystanie z usługi [Microsoft Teams na pulpicie wirtualnym systemu Windows](teams-on-wvd.md). Optymalizacja multimediów dla zespołów Microsoft Teams na pulpicie wirtualnym systemu Windows jest dostępna w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

Po dodaniu pakietu Office do obrazu można kontynuować Dostosowywanie głównego obrazu wirtualnego dysku twardego. Zobacz [Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md).
