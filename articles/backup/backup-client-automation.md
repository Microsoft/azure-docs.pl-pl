---
title: Używanie programu PowerShell do tworzenia kopii zapasowych systemu Windows Server na platformie Azure
description: W tym artykule dowiesz się, jak skonfigurować Azure Backup w systemie Windows Server lub kliencie systemu Windows przy użyciu programu PowerShell, a także zarządzać kopiami zapasowymi i odzyskiwaniem.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 582d8123f16b2d5a543d862b8eb3e45895087e4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90987098"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Wdrażanie kopii zapasowych systemu Windows Server/Windows Client na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell

W tym artykule pokazano, jak skonfigurować Azure Backup w systemie Windows Server lub kliencie systemu Windows przy użyciu programu PowerShell oraz zarządzać kopiami zapasowymi i odzyskiwaniem.

## <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć, [Zainstaluj najnowszą wersję programu PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki umożliwiają utworzenie magazynu Recovery Services. Magazyn Recovery Services jest inny niż magazyn kopii zapasowych.

1. Jeśli używasz Azure Backup po raz pierwszy, musisz użyć polecenia cmdlet **register-AzResourceProvider** , aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Magazyn Recovery Services jest zasobem Azure Resource Manager, dlatego należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową. Podczas tworzenia nowej grupy zasobów należy określić nazwę i lokalizację grupy zasobów.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Użyj polecenia cmdlet **New-AzRecoveryServicesVault** , aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla magazynu, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Określ typ nadmiarowości magazynu, który ma być używany. Można używać magazynu [lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) lub [magazynu Strefowo nadmiarowego (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). W poniższym przykładzie pokazano opcję **-BackupStorageRedundancy** dla *testVault* z ustawioną **geomiarowo**.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodnie jest przechowywać obiekt magazynu Recovery Services kopii zapasowej w zmiennej.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Użyj **Get-AzRecoveryServicesVault** , aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Możesz użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn, lub zobaczyć, które magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie **Get-AzRecoveryServicesVault** i wszystkie magazyny w subskrypcji zostaną wyświetlone na liście.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Instalowanie agenta Azure Backup

Przed zainstalowaniem agenta Azure Backup należy pobrać Instalatora i zaprezentować go w systemie Windows Server. Najnowszą wersję Instalatora można pobrać z [Centrum pobierania Microsoft](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu Recovery Services. Zapisz Instalatora w łatwo dostępnej lokalizacji, takiej jak `C:\Downloads\*` .

Można też użyć programu PowerShell do pobrania narzędzia do pobierania:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta ze wszystkimi opcjami domyślnymi. Instalacja trwa kilka minut w tle. Jeśli nie określisz opcji */Nu* , zostanie otwarte okno **Windows Update** po zakończeniu instalacji, aby sprawdzić dostępność aktualizacji. Po zainstalowaniu Agent zostanie wyświetlony na liście zainstalowanych programów.

Aby wyświetlić listę zainstalowanych programów, przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**.

![Agent zainstalowany](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji

Aby wyświetlić wszystkie opcje dostępne za pośrednictwem wiersza polecenia, użyj następującego polecenia:

```powershell
MARSAgentInstaller.exe /?
```

Dostępne opcje to:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Instalacja cicha |- |
| /p: "Location" |Ścieżka do folderu instalacji agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s: "lokalizacja" |Ścieżka do folderu pamięci podręcznej dla agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zezwól na Microsoft Update |- |
| /nu |Nie sprawdzaj, czy są aktualizacje po zakończeniu instalacji |- |
| /d |Odinstalowuje agenta Microsoft Azure Recovery Services |- |
| /pH Generuj dyrektywę |Adres hosta serwera proxy |- |
| /po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika hosta serwera proxy |- |
| /pw |Hasło serwera proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Rejestrowanie systemu Windows Server lub komputera klienckiego z systemem Windows w magazynie Recovery Services

Po utworzeniu magazynu Recovery Services Pobierz najnowszego agenta i poświadczenia magazynu i Zapisz go w wygodnej lokalizacji, takiej jak C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Rejestrowanie przy użyciu narzędzia PS AZ module

> [!NOTE]
> Usterka z generowaniem certyfikatu magazynu została rozwiązana w AZ 3.5.0 Release. Użyj AZ 3.5.0 Release w wersji lub nowszej, aby pobrać certyfikat magazynu.

W najnowszym module AZ modułu programu PowerShell ze względu na ograniczenia związane z platformą pobieranie poświadczeń magazynu wymaga certyfikatu z podpisem własnym. Poniższy przykład pokazuje, jak podać certyfikat z podpisem własnym i pobrać poświadczenia magazynu.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Na komputerze klienckim z systemem Windows Server lub Windows uruchom polecenie cmdlet [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) , aby zarejestrować maszynę w magazynie.
To i inne polecenia cmdlet używane do tworzenia kopii zapasowych pochodzą z modułu MSONLINE, który został dodany przez AgentInstaller MARS w ramach procesu instalacji.

Instalator agenta nie aktualizuje $Env:P zmiennej SModulePath. Oznacza to, że automatyczne ładowanie modułu kończy się niepowodzeniem. Aby rozwiązać ten problem, można wykonać następujące czynności:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Alternatywnie można załadować moduł ręcznie do skryptu w następujący sposób:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Po załadowaniu poleceń cmdlet kopii zapasowej online należy zarejestrować poświadczenia magazynu:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Nie używaj ścieżek względnych, aby określić plik poświadczeń magazynu. Musisz podać ścieżkę bezwzględną jako dane wejściowe do polecenia cmdlet.
>
>

## <a name="networking-settings"></a>Ustawienia sieci

Gdy łączność komputera z systemem Windows z Internetem odbywa się za pośrednictwem serwera proxy, ustawienia serwera proxy mogą być również udostępniane agentowi. W tym przykładzie nie ma serwera proxy, więc jawnie wyczyścimy wszelkie informacje związane z serwerem proxy.

Użycie przepustowości może być również kontrolowane za pomocą opcji `work hour bandwidth` i `non-work hour bandwidth` dla danego zestawu dni tygodnia.

Ustawianie informacji o serwerze proxy i przepustowości odbywa się przy użyciu polecenia cmdlet [Set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) :

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Ustawienia szyfrowania

Dane kopii zapasowej wysyłane do Azure Backup są szyfrowane w celu ochrony poufności danych. Hasło szyfrowania to "hasło" do odszyfrowania danych w momencie przywracania.

Należy wygenerować zabezpieczający numer PIN, wybierając pozycję **Generuj**, w obszarze właściwości **Ustawienia**  >    >  **zabezpieczenia numer PIN** w sekcji **Magazyn Recovery Services** w Azure Portal.

>[!NOTE]
> KOD PIN zabezpieczeń można wygenerować tylko za pośrednictwem Azure Portal.

Następnie użyj tego `generatedPIN` polecenia jako w poleceniu:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Przechowuj informacje o hasłach bezpiecznie i bezpiecznie po ich ustawieniu. Nie można przywrócić danych z platformy Azure bez tego hasła.
>
>

## <a name="back-up-files-and-folders"></a>Tworzenie kopii zapasowej plików i folderów

Wszystkie kopie zapasowe z serwerów i klientów z systemem Windows do Azure Backup podlegają zasadom. Zasady obejmują trzy części:

1. **Harmonogram tworzenia kopii zapasowych** , który określa, kiedy kopie zapasowe mają być pobierane i synchronizowane z usługą.
2. **Harmonogram przechowywania** określający, jak długo mają być przechowywane punkty odzyskiwania na platformie Azure.
3. **Specyfikacja dołączania/wykluczania plików** , która określa, co należy utworzyć kopię zapasową.

W tym dokumencie, ponieważ automatyzujemy tworzenie kopii zapasowych, założono, że nic nie zostanie skonfigurowane. Zaczynamy od utworzenia nowych zasad tworzenia kopii zapasowych za pomocą polecenia cmdlet [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) .

```powershell
$NewPolicy = New-OBPolicy
```

W tej chwili zasady są puste i potrzebne są inne polecenia cmdlet do definiowania elementów, które zostaną dołączone lub wykluczone, gdy będą wykonywane kopie zapasowe i gdzie będą przechowywane kopie zapasowe.

### <a name="configuring-the-backup-schedule"></a>Konfigurowanie harmonogramu tworzenia kopii zapasowych

Pierwszy z trzech części zasad jest harmonogramem tworzenia kopii zapasowych, który jest tworzony przy użyciu polecenia cmdlet [New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule) . Harmonogram tworzenia kopii zapasowych określa, kiedy należy wykonać kopie zapasowe. Podczas tworzenia harmonogramu należy określić dwa parametry wejściowe:

* **Dni tygodnia** , w których ma zostać uruchomiona kopia zapasowa. Zadanie tworzenia kopii zapasowej można uruchomić tylko z jednego dnia lub każdego dnia tygodnia lub dowolnej kombinacji między nimi.
* **Godzina, o** której ma zostać wykonana kopia zapasowa. Można zdefiniować maksymalnie trzy razy dziennie podczas tworzenia kopii zapasowej.

Na przykład można skonfigurować zasady tworzenia kopii zapasowych, które są uruchamiane o godzinie 16:00 w każdą sobotę i niedzielę.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Harmonogram tworzenia kopii zapasowych musi być skojarzony z zasadami. można to osiągnąć za pomocą polecenia cmdlet [Set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) .

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurowanie zasad przechowywania

Zasady przechowywania określają, jak długo punkty odzyskiwania utworzone na podstawie zadań kopii zapasowej są zachowywane. Podczas tworzenia nowych zasad przechowywania przy użyciu polecenia cmdlet [New-OBRetentionPolicy](/powershell/module/msonlinebackup/new-obretentionpolicy) możesz określić liczbę dni, przez jaką punkty odzyskiwania kopii zapasowych będą zachowywane Azure Backup. W poniższym przykładzie ustawiono zasady przechowywania wynoszące siedem dni.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Zasady przechowywania muszą być skojarzone z zasadami głównymi przy użyciu polecenia cmdlet [Set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>Uwzględnianie i wykluczanie plików, których kopia zapasowa ma zostać utworzona

`OBFileSpec`Obiekt definiuje pliki do uwzględnienia i wykluczone w kopii zapasowej. Jest to zestaw reguł określających zakres chronionych plików i folderów na komputerze. Istnieje wiele reguł dołączania lub wykluczania plików, które są wymagane i kojarzą je z zasadami. Podczas tworzenia nowego obiektu OBFileSpec można:

* Określ pliki i foldery do uwzględnienia
* Określ pliki i foldery, które mają zostać wykluczone
* Określ rekursywną kopię zapasową danych w folderze (lub), czy należy utworzyć kopię zapasową tylko plików najwyższego poziomu w określonym folderze.

Ta ostatnia zostanie osiągnięta przy użyciu flagi-niecyklicznego w New-OBFileSpec polecenie.

W poniższym przykładzie będziemy tworzyć kopie zapasowe woluminów C: i D: i wykluczać pliki binarne systemu operacyjnego w folderze systemu Windows i w folderach tymczasowych. W tym celu utworzymy dwie specyfikacje pliku przy użyciu polecenia cmdlet [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) -one do włączenia i jednego do wykluczenia. Po utworzeniu specyfikacji plików są one skojarzone z zasadami przy użyciu polecenia cmdlet [Add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) .

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Stosowanie zasad

Teraz obiekt Policy został ukończony i ma skojarzony Harmonogram kopii zapasowych, zasady przechowywania oraz listę plików dołączania/wykluczania. Te zasady można teraz przystąpić do Azure Backup. Przed zastosowaniem nowo utworzonych zasad upewnij się, że nie ma żadnych istniejących zasad kopii zapasowych skojarzonych z serwerem za pomocą polecenia cmdlet [Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) . Usunięcie zasad spowoduje wyświetlenie monitu o potwierdzenie. Aby pominąć potwierdzenie, użyj `-Confirm:$false` flagi z poleceniem cmdlet.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Zatwierdzanie obiektu zasad odbywa się przy użyciu polecenia cmdlet [Set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) . Spowoduje to również poproszenie o potwierdzenie. Aby pominąć potwierdzenie, użyj `-Confirm:$false` flagi z poleceniem cmdlet.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Szczegóły istniejących zasad tworzenia kopii zapasowych można wyświetlić za pomocą polecenia cmdlet [Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) . Możesz również przejść do szczegółów przy użyciu polecenia cmdlet [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) dla harmonogramu tworzenia kopii zapasowych i polecenia cmdlet [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) dla zasad przechowywania

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>Wykonywanie kopii zapasowej na żądanie

Po ustawieniu zasad tworzenia kopii zapasowej kopie zapasowe będą wykonywane zgodnie z harmonogramem. Wyzwalanie kopii zapasowej na żądanie jest również możliwe za pomocą polecenia cmdlet [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup) :

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>Tworzenie kopii zapasowej stanu systemu Windows Server w agencie MARS

W tej sekcji omówiono polecenie programu PowerShell służące do konfigurowania stanu systemu w agencie MARS.

### <a name="schedule"></a>Zaplanuj

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Przechowywanie

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Konfigurowanie harmonogramu i przechowywania

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Weryfikowanie zasad

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Przywracanie danych z Azure Backup

Ta sekcja przeprowadzi Cię przez procedurę automatyzacji odzyskiwania danych z Azure Backup. Wykonanie tej czynności obejmuje następujące kroki:

1. Wybierz wolumin źródłowy
2. Wybierz punkt kopii zapasowej do przywrócenia
3. Określ element do przywrócenia
4. Wyzwól proces przywracania

### <a name="picking-the-source-volume"></a>Wybieranie woluminu źródłowego

Aby przywrócić element z Azure Backup, należy najpierw zidentyfikować źródło elementu. Ponieważ wykonujemy polecenia w kontekście systemu Windows Server lub klienta systemu Windows, maszyna jest już zidentyfikowana. Następnym krokiem w identyfikacji źródła jest zidentyfikowanie woluminu zawierającego ten element. Listę woluminów lub źródeł, których kopie zapasowe są tworzone z tej maszyny, można pobrać, wykonując polecenie cmdlet [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) . To polecenie zwraca tablicę wszystkich źródeł, których kopię zapasową utworzono z tego serwera lub klienta.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Wybieranie punktu kopii zapasowej, z którego ma zostać przywrócone

Listę punktów kopii zapasowej można pobrać, wykonując polecenie cmdlet [Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) z odpowiednimi parametrami. W naszym przykładzie wybierzemy najnowszy punkt kopii zapasowej woluminu źródłowego *C:* i użyj go do odzyskania określonego pliku.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Obiekt `$Rps` jest tablicą punktów kopii zapasowych. Pierwszy element jest ostatnim punktem, a n element jest najstarszym punktem. W celu wybrania najnowszego punktu będziemy używać `$Rps[0]` .

### <a name="specifying-an-item-to-restore"></a>Określanie elementu do przywrócenia

Aby przywrócić określony plik, określ nazwę pliku względem woluminu głównego. Na przykład aby pobrać C:\Test\Cat.job, wykonaj następujące polecenie.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Wyzwalanie procesu przywracania

Aby wyzwolić proces przywracania, najpierw musimy określić opcje odzyskiwania. Można to zrobić za pomocą polecenia cmdlet [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) . Na potrzeby tego przykładu Załóżmy, że chcemy przywrócić pliki do *C:\Temp*. Załóżmy również, że chcemy pominąć pliki, które już istnieją w folderze docelowym *C:\Temp*. Aby utworzyć taką opcję odzyskiwania, użyj następującego polecenia:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Teraz Wyzwól proces przywracania za pomocą polecenia [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) wybranego `$Item` z danych wyjściowych `Get-OBRecoverableItem` polecenia cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Odinstalowywanie agenta Azure Backup

Odinstalowywanie agenta Azure Backup można wykonać przy użyciu następującego polecenia:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Odinstalowanie plików binarnych agenta z komputera ma pewne konsekwencje:

* Usuwa filtr pliku z komputera, a śledzenie zmian jest zatrzymane.
* Wszystkie informacje o zasadach są usuwane z komputera, ale informacje o zasadach nadal są przechowywane w usłudze.
* Wszystkie harmonogramy tworzenia kopii zapasowych są usuwane i nie są podejmowane żadne dalsze kopie zapasowe.

Jednak dane przechowywane na platformie Azure pozostają i są zachowywane zgodnie z konfiguracją zasad przechowywania. Starsze punkty są automatycznie przestarzałe.

## <a name="remote-management"></a>Zdalne zarządzanie

Wszystkie zarządzanie Azure Backup agentem, zasadami i źródłami danych można wykonać zdalnie za pomocą programu PowerShell. Maszyna, która będzie zarządzana zdalnie, musi zostać poprawnie przygotowana.

Domyślnie usługa WinRM jest konfigurowana do uruchamiania ręcznego. Typ uruchamiania musi być ustawiony na *automatyczny* , a usługa powinna być uruchomiona. Aby sprawdzić, czy usługa WinRM jest uruchomiona, wartość właściwości stan powinna być *uruchomiona*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

Program PowerShell należy skonfigurować pod kątem komunikacji zdalnej.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Maszynę można teraz zarządzać zdalnie — uruchamiając ją z poziomu instalacji agenta. Na przykład poniższy skrypt kopiuje agenta na maszynę zdalną i instaluje go.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup dla systemu Windows Server/klienta:

* [Wprowadzenie do usługi Azure Backup](./backup-overview.md)
* [Tworzenie kopii zapasowych serwerów z systemem Windows](backup-windows-with-mars-agent.md)
