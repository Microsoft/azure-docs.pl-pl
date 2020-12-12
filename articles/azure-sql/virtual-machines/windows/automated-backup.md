---
title: Automatyczne kopie zapasowe v2 dla maszyn wirtualnych platformy Azure w SQL Server 2016/2017 | Microsoft Docs
description: W tym artykule wyjaśniono funkcję automatycznego tworzenia kopii zapasowych dla maszyn wirtualnych SQL Server 2016/2017 działających na platformie Azure. Ten artykuł dotyczy maszyn wirtualnych korzystających z Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f41614d54dc4320f683f406b2882a7b388bb4c3d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358422"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatyczne kopie zapasowe v2 dla maszyn wirtualnych platformy Azure (Menedżer zasobów)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 +](automated-backup.md)

Automatyczna kopia zapasowa v2 automatycznie konfiguruje [zarządzaną kopię zapasową do Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) dla wszystkich istniejących i nowych baz danych na maszynie wirtualnej platformy Azure z systemem SQL Server 2016 lub nowszym w wersjach Standard, Enterprise i developer. Dzięki temu można konfigurować regularne kopie zapasowe bazy danych korzystające z trwałego magazynu obiektów blob platformy Azure. Automatyczne kopie zapasowe v2 są zależne od [rozszerzenia agenta SQL Server infrastruktury jako usługi (IaaS)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć zautomatyzowanej kopii zapasowej v2, zapoznaj się z następującymi wymaganiami wstępnymi:

**System operacyjny**:

- Windows Server 2012 R2 lub nowszy

**Wersja SQL Server/Edition**:

- SQL Server 2016 lub wyższy: developer, Standard lub Enterprise

> [!NOTE]
> W przypadku SQL Server 2014 zapoznaj się z tematem [Automatyczna kopia zapasowa dla SQL Server 2014](automated-backup-sql-2014.md).

**Konfiguracja bazy danych**:

- Docelowe bazy danych _użytkowników_ muszą używać modelu odzyskiwania pełnego. Systemowe bazy danych nie muszą korzystać z modelu odzyskiwania pełnego. Jeśli jednak wymagane jest wykonanie kopii zapasowych dzienników dla modelu lub MSDB, należy użyć modelu odzyskiwania pełnego. Aby uzyskać więcej informacji o wpływie modelu odzyskiwania pełnego na kopie zapasowe, zobacz [kopia zapasowa w ramach modelu odzyskiwania pełnego](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- Maszyna wirtualna SQL Server została zarejestrowana w rozszerzeniu programu SQL IaaS Agent w [trybie zarządzania pełnego](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  Automatyczne kopie zapasowe opierają SQL Server się na [rozszerzeniu IaaS agenta](sql-server-iaas-agent-extension-automate-management.md). W związku z tym automatyczne kopie zapasowe są obsługiwane tylko w docelowych bazach danych z wystąpienia domyślnego lub z pojedynczym nazwanym wystąpieniem. Jeśli nie ma wystąpienia domyślnego i wiele wystąpień nazwanych, rozszerzenie SQL IaaS zakończy się niepowodzeniem, a automatyczne wykonywanie kopii zapasowej nie będzie działać. 

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla zautomatyzowanej kopii zapasowej v2. Rzeczywiste czynności konfiguracyjne różnią się w zależności od tego, czy używasz poleceń Azure Portal lub Azure Windows PowerShell.

### <a name="basic-settings"></a>Ustawienia podstawowe

| Ustawienie | Zakres (wartość domyślna) | Opis |
| --- | --- | --- |
| **Automatyczne usługa Backup** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza automatyczne tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z systemem SQL Server 2016/2017 Developer, Standard lub Enterprise. |
| **Okres przechowywania** | 1-30 dni (30 dni) | Liczba dni przechowywania kopii zapasowych. |
| **Konto magazynu** | Konto magazynu Azure | Konto usługi Azure Storage służące do przechowywania plików automatycznego tworzenia kopii zapasowych w usłudze BLOB Storage. W tej lokalizacji jest tworzony kontener służący do przechowywania wszystkich plików kopii zapasowej. Konwencja nazewnictwa plików kopii zapasowej obejmuje datę, godzinę i identyfikator GUID bazy danych. |
| **Szyfrowanie** |Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza szyfrowanie. Po włączeniu szyfrowania certyfikaty używane do przywracania kopii zapasowej znajdują się na określonym koncie magazynu. Używa tego samego kontenera **automatycznego tworzenia kopii zapasowych** z tą samą konwencją nazewnictwa. Jeśli hasło zostanie zmienione, zostanie wygenerowany nowy certyfikat z tym hasłem, ale stary certyfikat pozostaje przywrócony do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** |Tekst hasła | Hasło dla kluczy szyfrowania. To hasło jest wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby można było przywrócić zaszyfrowaną kopię zapasową, należy dysponować prawidłowym hasłem i powiązanym certyfikatem użytym w czasie wykonywania kopii zapasowej. |

### <a name="advanced-settings"></a>Ustawienia zaawansowane

| Ustawienie | Zakres (wartość domyślna) | Opis |
| --- | --- | --- |
| **Kopie zapasowe systemowej bazy danych** | Włącz/Wyłącz (wyłączone) | Po włączeniu tej funkcji program tworzy również kopię zapasową systemowych baz danych: Master, MSDB i model. W przypadku baz danych MSDB i modelu Sprawdź, czy są one w trybie pełnego odzyskiwania, jeśli chcesz, aby kopie zapasowe dziennika były wykonywane. Kopie zapasowe dziennika nigdy nie są pobierane dla serwera głównego. I żadne kopie zapasowe nie są pobierane dla bazy danych TempDB. |
| **Harmonogram tworzenia kopii zapasowych** | Ręczne/automatyczne (zautomatyzowane) | Domyślnie harmonogram tworzenia kopii zapasowych jest automatycznie ustalany na podstawie przyrostu dziennika. Harmonogram ręcznego tworzenia kopii zapasowych umożliwia użytkownikowi określenie przedziału czasu dla kopii zapasowych. W takim przypadku kopie zapasowe są wykonywane tylko z określoną częstotliwością i w określonym przedziale czasu danego dnia. |
| **Częstotliwość pełnej kopii zapasowej** | Codziennie/co tydzień | Częstotliwość tworzenia pełnych kopii zapasowych. W obu przypadkach pełne kopie zapasowe zaczynają się w następnym zaplanowanym przedziale czasu. W przypadku wybrania co tydzień kopie zapasowe mogą obejmować wiele dni, dopóki nie zostanie utworzona kopia zapasowa wszystkich baz danych. |
| **Godzina rozpoczęcia pełnej kopii zapasowej** | 00:00 – 23:00 (01:00) | Godzina rozpoczęcia danego dnia, w którym mogą zostać wykonane pełne kopie zapasowe. |
| **Przedział czasu pełnej kopii zapasowej** | 1 – 23 godz. (1 godzina) | Czas trwania przedziału czasu danego dnia, w którym mogą zostać wykonane pełne kopie zapasowe. |
| **Częstotliwość tworzenia kopii zapasowych dziennika** | 5 – 60 minut (60 minut) | Częstotliwość tworzenia kopii zapasowych dziennika. |

## <a name="understanding-full-backup-frequency"></a>Informacje o częstotliwości pełnej kopii zapasowej
Ważne jest, aby zrozumieć różnicę między codziennym i tygodniowym pełnymi kopiami zapasowymi. Rozważmy poniższe dwa przykładowe scenariusze.

### <a name="scenario-1-weekly-backups"></a>Scenariusz 1: cotygodniowe kopie zapasowe
Masz SQL Server maszynę wirtualną, która zawiera wiele dużych baz danych.

W poniedziałek należy włączyć automatyczne tworzenie kopii zapasowej v2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: **Ręczne**
- Częstotliwość pełnej kopii zapasowej: **co tydzień**
- Godzina rozpoczęcia pełnej kopii zapasowej: **01:00**
- Przedział czasu pełnej kopii zapasowej: **1 godzina**

Oznacza to, że następne dostępne okno tworzenia kopii zapasowej jest wtorek o 1 godzinę. W tym momencie automatyczne tworzenie kopii zapasowej rozpoczyna wykonywanie kopii zapasowych baz danych pojedynczo. W tym scenariuszu bazy danych są wystarczająco duże, aby pełne kopie zapasowe były kompletne dla pierwszych kilku baz danych. Jednak po jednej godzinie nie wszystkie bazy danych zostały wykonane.

W takim przypadku automatyczna kopia zapasowa rozpocznie tworzenie kopii zapasowych pozostałych baz danych w następnym dniu, środa o godzinie 1. Jeśli w tym czasie nie ma kopii zapasowej wszystkich baz danych, program spróbuje ponownie później, w tym samym czasie. Ten proces jest kontynuowany do momentu utworzenia kopii zapasowej wszystkich baz danych.

Po ponownym osiągnięciu wtorku, automatyczne tworzenie kopii zapasowej rozpocznie tworzenie kopii zapasowej wszystkich baz danych.

Ten scenariusz pokazuje, że automatyczna kopia zapasowa działa tylko w określonym przedziale czasu, a dla każdej bazy danych kopia zapasowa jest wykonywana raz na tydzień. Wynika to również z możliwości tworzenia kopii zapasowych w wielu dniach w przypadku, gdy nie można wykonać wszystkich kopii zapasowych w jednym dniu.

### <a name="scenario-2-daily-backups"></a>Scenariusz 2: codzienne kopie zapasowe
Masz SQL Server maszynę wirtualną, która zawiera wiele dużych baz danych.

W poniedziałek należy włączyć automatyczne tworzenie kopii zapasowej v2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: ręczne
- Częstotliwość pełnej kopii zapasowej: codziennie
- Godzina rozpoczęcia pełnej kopii zapasowej: 22:00
- Przedział czasu pełnej kopii zapasowej: 6 godzin

Oznacza to, że następne dostępne okno tworzenia kopii zapasowej jest poniedziałek o godzinie 10 godzin. W tym momencie automatyczne tworzenie kopii zapasowej rozpoczyna wykonywanie kopii zapasowych baz danych pojedynczo.

Następnie, w poniedziałek o wartości 10 przez 6 godzin, pełne kopie zapasowe wszystkich baz danych zostaną uruchomione ponownie.

> [!IMPORTANT]
> W przypadku planowania codziennej kopii zapasowej zaleca się zaplanowanie okna szerokiego czasu, aby zapewnić, że kopie zapasowe wszystkich baz danych w tym czasie mają być tworzone. Jest to szczególnie ważne w przypadku, gdy istnieje duża ilość danych do utworzenia kopii zapasowej.

## <a name="configure-new-vms"></a>Konfiguruj nowe maszyny wirtualne

Użyj Azure Portal, aby skonfigurować automatyczne tworzenie kopii zapasowych v2 podczas tworzenia nowej maszyny wirtualnej SQL Server 2016 lub 2017 w modelu wdrażania Menedżer zasobów.

Na karcie **ustawienia SQL Server** wybierz opcję **Włącz** w obszarze **zautomatyzowane kopie zapasowe**. Poniższy zrzut ekranu Azure Portal przedstawia ustawienia **automatycznego tworzenia kopii zapasowych programu SQL Server** .

![Konfiguracja zautomatyzowanej kopii zapasowej SQL w Azure Portal](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> Automatyczna kopia zapasowa v2 jest domyślnie wyłączona.

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących SQL Server maszyn wirtualnych przejdź do [zasobu maszyny wirtualne SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) , a następnie wybierz pozycję **kopie zapasowe** , aby skonfigurować automatyczne kopie zapasowe.

![Automatyczne kopie zapasowe SQL dla istniejących maszyn wirtualnych](./media/automated-backup/sql-server-configuration.png)


Po zakończeniu kliknij przycisk **Zastosuj** w dolnej części strony ustawienia **kopii zapasowych** , aby zapisać zmiany.

Jeśli automatyczne tworzenie kopii zapasowej jest włączane po raz pierwszy, platforma Azure skonfiguruje SQL Server agenta IaaS w tle. W tym czasie Azure Portal mogą nie być widoczne, że skonfigurowano automatyczną kopię zapasową. Poczekaj kilka minut, aż zostanie zainstalowany agent, który został skonfigurowany. Następnie Azure Portal odzwierciedla nowe ustawienia.

## <a name="configure-with-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Aby skonfigurować automatyczne tworzenie kopii zapasowych v2, można użyć programu PowerShell. Przed rozpoczęciem należy:

- [Pobierz i zainstaluj najnowszą Azure PowerShell](https://aka.ms/webpi-azps).
- Otwórz program Windows PowerShell i skojarz go z kontem za pomocą polecenia **Connect-AzAccount** .

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Zainstaluj rozszerzenie IaaS SQL Server
Jeśli zainicjowano SQL Server maszynę wirtualną z Azure Portal, rozszerzenie SQL Server IaaS powinno być już zainstalowane. Możesz określić, czy jest zainstalowana dla maszyny wirtualnej, wywołując polecenie **Get-AzVM** i sprawdzając Właściwość **Extensions** .

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Jeśli zainstalowano rozszerzenie agenta SQL Server IaaS, powinno ono zostać wyświetlone na liście jako "SqlIaaSAgent" lub "SQLIaaSExtension". **ProvisioningState** dla rozszerzenia powinna również zawierać "powodzenie". 

Jeśli nie jest zainstalowana lub nie można było zainicjować obsługi administracyjnej, można zainstalować ją za pomocą poniższego polecenia. Oprócz nazwy maszyny wirtualnej i grupy zasobów należy również określić region (**$region**), w którym znajduje się maszyna wirtualna.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Weryfikuj bieżące ustawienia
Jeśli podczas aprowizacji włączono funkcję automatycznego tworzenia kopii zapasowych, można sprawdzić bieżącą konfigurację przy użyciu programu PowerShell. Uruchom polecenie **Get-AzVMSqlServerExtension** i przejrzyj Właściwość **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Jeśli dane wyjściowe pokazują, że ustawienie **Włącz** ma **wartość FAŁSZ**, należy włączyć automatyczne tworzenie kopii zapasowej. Dobrym sposobem jest włączenie i skonfigurowanie zautomatyzowanej kopii zapasowej w taki sam sposób. Więcej informacji można znaleźć w następnej sekcji.

> [!NOTE] 
> Jeśli ustawienia są sprawdzane natychmiast po wprowadzeniu zmiany, istnieje możliwość przywrócenia starych wartości konfiguracji. Poczekaj kilka minut i ponownie sprawdź ustawienia, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup-v2"></a>Konfigurowanie zautomatyzowanej kopii zapasowej v2
Możesz użyć programu PowerShell, aby włączyć automatyczne tworzenie kopii zapasowych, a także zmodyfikować jego konfigurację i zachowanie w dowolnym momencie. 

Najpierw wybierz lub Utwórz konto magazynu dla plików kopii zapasowej. Poniższy skrypt wybiera konto magazynu lub tworzy je, jeśli nie istnieje.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatyczne kopie zapasowe nie obsługują przechowywania kopii zapasowych w magazynie w warstwie Premium, ale mogą tworzyć kopie zapasowe z dysków maszyn wirtualnych, które używają Premium Storage.

Następnie użyj polecenia **New-AzVMSqlServerAutoBackupConfig** , aby włączyć i skonfigurować ustawienia automatycznego tworzenia kopii zapasowych w wersji 2 w celu przechowywania kopii zapasowych na koncie usługi Azure Storage. W tym przykładzie kopie zapasowe są przechowywane przez 10 dni. Kopie zapasowe systemowej bazy danych są włączone. Pełne kopie zapasowe są zaplanowane co tydzień z przedziałem czasu, który rozpoczyna się o 20:00 przez dwie godziny. Kopie zapasowe dzienników są zaplanowane na co 30 minut. Drugie polecenie **Set-AzVMSqlServerExtension**, AKTUALIZUJE określoną maszynę wirtualną platformy Azure przy użyciu tych ustawień.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Zainstalowanie i skonfigurowanie agenta SQL Server IaaS może potrwać kilka minut. 

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr **EnableEncryption** wraz z hasłem (bezpieczny ciąg) dla parametru **CertificatePassword** . Poniższy skrypt włącza ustawienia zautomatyzowanej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Aby potwierdzić, że ustawienia są stosowane, [Sprawdź konfigurację zautomatyzowanej kopii zapasowej](#verifysettings).

### <a name="disable-automated-backup"></a>Wyłącz automatyczne tworzenie kopii zapasowej
Aby wyłączyć automatyczne tworzenie kopii zapasowych, Uruchom ten sam skrypt bez parametru **-enable** dla polecenia **New-AzVMSqlServerAutoBackupConfig** . Brak parametru **-enable** sygnalizuje polecenie, aby wyłączyć tę funkcję. Tak jak w przypadku instalacji, wyłączenie automatycznej kopii zapasowej może potrwać kilka minut.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Przykładowy skrypt
Poniższy skrypt zawiera zestaw zmiennych, które można dostosować w celu włączenia i skonfigurowania zautomatyzowanej kopii zapasowej maszyny wirtualnej. W Twoim przypadku może być konieczne dostosowanie skryptu zgodnie z wymaganiami. Na przykład trzeba wprowadzić zmiany, jeśli chcesz wyłączyć tworzenie kopii zapasowych systemowych baz danych lub włączyć szyfrowanie.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatyczne kopie zapasowe na SQL Server 2016/2017, dostępne są dwie opcje główne. Ponieważ automatyczne kopie zapasowe korzystają z funkcji zarządzanej kopii zapasowej SQL Server, te same techniki monitorowania stosują się do obu tych metod.

Najpierw można sondować stan, wywołując metodę [msdb. managed_backup. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub wykonaj zapytanie dotyczące funkcji zwracającej tabelę [msdb. managed_backup. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

Innym rozwiązaniem jest skorzystanie z wbudowanej funkcji Poczta bazy danych na potrzeby powiadomień.

1. Wywołaj procedurę składowaną [msdb. managed_backup. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) , aby przypisać do parametru **SSMBackup2WANotificationEmailIds** adres e-mail. 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny wirtualnej platformy Azure.
1. Aby skonfigurować Poczta bazy danych, Użyj serwera SMTP i nazwy użytkownika. Poczta bazy danych można skonfigurować w SQL Server Management Studio lub za pomocą poleceń języka Transact-SQL. Aby uzyskać więcej informacji, zobacz [poczta bazy danych](/sql/relational-databases/database-mail/database-mail).
1. [Skonfiguruj SQL Server agenta, aby korzystał z poczta bazy danych](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy port SMTP jest dozwolony zarówno za pośrednictwem lokalnej zapory maszyny wirtualnej, jak i sieciowej grupy zabezpieczeń dla maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
Zautomatyzowana kopia zapasowa v2 konfiguruje zarządzane kopie zapasowe na maszynach wirtualnych platformy Azure Dlatego ważne jest zapoznanie się [z dokumentacją dotyczącą zarządzanej kopii zapasowej](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) w celu zrozumienia zachowania i konsekwencji.

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania dla SQL Server na maszynach wirtualnych platformy Azure można znaleźć w następującym artykule: [Tworzenie kopii zapasowych i przywracanie SQL Server na maszynach wirtualnych platformy Azure](backup-restore.md).

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [SQL Server rozszerzenia agenta IaaS](sql-server-iaas-agent-extension-automate-management.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server Omówienie usługi Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).