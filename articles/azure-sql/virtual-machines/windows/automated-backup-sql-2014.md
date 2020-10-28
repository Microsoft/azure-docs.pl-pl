---
title: Automatyczne kopie zapasowe dla maszyn wirtualnych platformy Azure SQL Server 2014
description: Wyjaśnia funkcję automatycznej kopii zapasowej dla maszyn wirtualnych SQL Server 2014 działających na platformie Azure. Ten artykuł dotyczy maszyn wirtualnych korzystających z Menedżer zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8119d01ae8e8ed1e809753e433b063a844a2c5c3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790682"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Zautomatyzowana kopia zapasowa maszyn wirtualnych SQL Server 2014 (Menedżer zasobów)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

Automatyczna kopia zapasowa automatycznie konfiguruje [zarządzaną kopię zapasową do Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) dla wszystkich istniejących i nowych baz danych na maszynie wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. Dzięki temu można konfigurować regularne kopie zapasowe bazy danych korzystające z trwałego magazynu obiektów blob platformy Azure. Automatyczne kopie zapasowe są zależne od [rozszerzenia agenta SQL Server infrastruktury jako usługi (IaaS)](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z zautomatyzowanej kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:


**System operacyjny** :

- System Windows Server 2012 i nowsze 

**Wersja SQL Server/Edition** :

- Standard SQL Server 2014
- SQL Server 2014 Enterprise

> [!NOTE]
> Aby uzyskać więcej niż SQL 2016 i więcej, zobacz [Automatyczne tworzenie kopii zapasowych dla SQL Server 2016](automated-backup.md).

**Konfiguracja bazy danych** :

- Docelowe bazy danych _użytkowników_ muszą używać modelu odzyskiwania pełnego. Systemowe bazy danych nie muszą korzystać z modelu odzyskiwania pełnego. Jeśli jednak wymagane jest wykonanie kopii zapasowych dzienników dla modelu lub MSDB, należy użyć modelu odzyskiwania pełnego. Aby uzyskać więcej informacji o wpływie modelu odzyskiwania pełnego na kopie zapasowe, zobacz [kopia zapasowa w ramach modelu odzyskiwania pełnego](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- Maszyna wirtualna SQL Server została zarejestrowana w ramach dostawcy zasobów maszyny wirtualnej SQL w [trybie zarządzania pełnego](sql-vm-resource-provider-register.md#upgrade-to-full). 
-  Automatyczne kopie zapasowe opierają SQL Server się na [rozszerzeniu IaaS agenta](sql-server-iaas-agent-extension-automate-management.md). W związku z tym automatyczne kopie zapasowe są obsługiwane tylko w docelowych bazach danych z wystąpienia domyślnego lub z pojedynczym nazwanym wystąpieniem. Jeśli nie ma wystąpienia domyślnego i wiele wystąpień nazwanych, rozszerzenie SQL IaaS zakończy się niepowodzeniem, a automatyczne wykonywanie kopii zapasowej nie będzie działać. 

## <a name="settings"></a>Ustawienia

W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowych. Rzeczywiste czynności konfiguracyjne różnią się w zależności od tego, czy używasz poleceń Azure Portal lub Azure Windows PowerShell.

| Ustawienie | Zakres (wartość domyślna) | Opis |
| --- | --- | --- |
| **Automatyczne usługa Backup** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza automatyczne tworzenie kopii zapasowej maszyny wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** | 1-30 dni (30 dni) | Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** | Konto magazynu Azure | Konto usługi Azure Storage służące do przechowywania plików automatycznego tworzenia kopii zapasowych w usłudze BLOB Storage. W tej lokalizacji jest tworzony kontener służący do przechowywania wszystkich plików kopii zapasowej. Konwencja nazewnictwa plików kopii zapasowej obejmuje datę, godzinę i nazwę maszyny. |
| **Szyfrowanie** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza szyfrowanie. Po włączeniu szyfrowania certyfikaty używane do przywracania kopii zapasowej znajdują się na określonym koncie magazynu w tym samym `automaticbackup` kontenerze przy użyciu tej samej konwencji nazewnictwa. Jeśli hasło zostanie zmienione, zostanie wygenerowany nowy certyfikat z tym hasłem, ale stary certyfikat pozostaje przywrócony do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** | Tekst hasła | Hasło dla kluczy szyfrowania. Jest to wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby można było przywrócić zaszyfrowaną kopię zapasową, należy dysponować prawidłowym hasłem i powiązanym certyfikatem użytym w czasie wykonywania kopii zapasowej. |


## <a name="configure-new-vms"></a>Konfiguruj nowe maszyny wirtualne

Użyj Azure Portal, aby skonfigurować automatyczne tworzenie kopii zapasowej podczas tworzenia nowej maszyny wirtualnej SQL Server 2014 w modelu wdrażania Menedżer zasobów.

Na karcie **ustawienia SQL Server** przewiń w dół do opcji **zautomatyzowane tworzenie kopii zapasowej** i wybierz pozycję **Włącz** . Poniższy zrzut ekranu Azure Portal przedstawia ustawienia **automatycznego tworzenia kopii zapasowych programu SQL Server** .

![Konfiguracja zautomatyzowanej kopii zapasowej SQL w Azure Portal](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych SQL Server można włączyć i wyłączyć automatyczne kopie zapasowe, zmienić okres przechowywania, określić konto magazynu i włączyć szyfrowanie z Azure Portal. 

Przejdź do [zasobu maszyny wirtualne SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) dla maszyny wirtualnej SQL Server 2014, a następnie wybierz pozycję **kopie zapasowe** . 

![Automatyczne kopie zapasowe SQL dla istniejących maszyn wirtualnych](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Po zakończeniu wybierz przycisk **Zastosuj** w dolnej części strony **kopie zapasowe** , aby zapisać zmiany.

Jeśli automatyczne tworzenie kopii zapasowej jest włączane po raz pierwszy, platforma Azure skonfiguruje SQL Server agenta IaaS w tle. W tym czasie Azure Portal mogą nie być widoczne, że skonfigurowano automatyczną kopię zapasową. Poczekaj kilka minut, aż Agent zostanie zainstalowany i skonfigurowany. Następnie Azure Portal odzwierciedla nowe ustawienia.

> [!NOTE]
> Możesz również skonfigurować automatyczne tworzenie kopii zapasowej przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablon szybkiego startu platformy Azure dla zautomatyzowanej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Za pomocą programu PowerShell można skonfigurować automatyczne tworzenie kopii zapasowych. Przed rozpoczęciem należy:

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

Jeśli nie jest zainstalowana lub nie można było zainicjować obsługi administracyjnej, można zainstalować ją za pomocą poniższego polecenia. Oprócz nazwy maszyny wirtualnej i grupy zasobów należy również określić region ( **$region** ), w którym znajduje się maszyna wirtualna. Określ typ licencji dla maszyny wirtualnej SQL Server, wybierając między opcją płatność zgodnie z rzeczywistym użyciem lub przeniesieniem własnych licencji za pośrednictwem [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Aby uzyskać więcej informacji o licencjonowaniu, zobacz [model licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest jeszcze zainstalowane, instalacja rozszerzenia zostanie ponownie uruchomiona SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Weryfikuj bieżące ustawienia

Jeśli podczas aprowizacji włączono funkcję automatycznego tworzenia kopii zapasowych, można sprawdzić bieżącą konfigurację przy użyciu programu PowerShell. Uruchom polecenie **Get-AzVMSqlServerExtension** i przejrzyj Właściwość **AutoBackupSettings** :

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Powinny zostać wyświetlone dane wyjściowe podobne do następujących:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Jeśli dane wyjściowe pokazują, że ustawienie **Włącz** ma **wartość FAŁSZ** , należy włączyć automatyczne tworzenie kopii zapasowej. Dobrym sposobem jest włączenie i skonfigurowanie zautomatyzowanej kopii zapasowej w taki sam sposób. Więcej informacji można znaleźć w następnej sekcji.

> [!NOTE] 
> Jeśli ustawienia są sprawdzane natychmiast po wprowadzeniu zmiany, istnieje możliwość przywrócenia starych wartości konfiguracji. Poczekaj kilka minut i ponownie sprawdź ustawienia, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup"></a>Konfigurowanie zautomatyzowanej kopii zapasowej
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

Następnie użyj polecenia **New-AzVMSqlServerAutoBackupConfig** , aby włączyć i skonfigurować ustawienia automatycznego tworzenia kopii zapasowej w celu przechowywania kopii zapasowych na koncie usługi Azure Storage. W tym przykładzie kopie zapasowe są przechowywane przez 10 dni. Drugie polecenie **Set-AzVMSqlServerExtension** , AKTUALIZUJE określoną maszynę wirtualną platformy Azure przy użyciu tych ustawień.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Zainstalowanie i skonfigurowanie agenta SQL Server IaaS może potrwać kilka minut.

> [!NOTE]
> Istnieją inne ustawienia dla elementu **New-AzVMSqlServerAutoBackupConfig** , które mają zastosowanie tylko do SQL Server 2016 i zautomatyzowanej kopii zapasowej v2. SQL Server 2014 nie obsługuje następujących ustawień: **BackupSystemDbs** , **BackupScheduleType** , **FullBackupFrequency** , **FullBackupStartHour** , **FullBackupWindowInHours** i **LogBackupFrequencyInMinutes** . Jeśli podjęto próbę skonfigurowania tych ustawień na maszynie wirtualnej SQL Server 2014, nie ma błędów, ale ustawienia nie zostaną zastosowane. Jeśli chcesz użyć tych ustawień na maszynie wirtualnej SQL Server 2016, zobacz [Automatyczne tworzenie kopii zapasowej v2 dla SQL Server 2016 Azure Virtual](automated-backup.md)Machines.

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr **EnableEncryption** wraz z hasłem (bezpieczny ciąg) dla parametru **CertificatePassword** . Poniższy skrypt włącza ustawienia zautomatyzowanej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatyczne kopie zapasowe na SQL Server 2014, dostępne są dwie opcje główne. Ponieważ automatyczne kopie zapasowe korzystają z funkcji zarządzanej kopii zapasowej SQL Server, te same techniki monitorowania stosują się do obu tych metod.

Najpierw można sondować stan, wywołując metodę [msdb. smart_admin. sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub wykonaj zapytanie dotyczące funkcji zwracającej tabelę [msdb. smart_admin. fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) .

> [!NOTE]
> Schemat dla zarządzanej kopii zapasowej w SQL Server 2014 jest **msdb.smart_admin** . W SQL Server 2016 ta zmiana na **msdb.managed_backup** , a tematy referencyjne używają tego nowszego schematu. Ale dla SQL Server 2014 należy nadal używać schematu **smart_admin** dla wszystkich obiektów zarządzanych kopii zapasowych.

Innym rozwiązaniem jest skorzystanie z wbudowanej funkcji Poczta bazy danych na potrzeby powiadomień.

1. Wywołaj procedurę składowaną [msdb. smart_admin. sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) , aby przypisać do parametru **SSMBackup2WANotificationEmailIds** adres e-mail. 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny wirtualnej platformy Azure.
1. Aby skonfigurować Poczta bazy danych, Użyj serwera SMTP i nazwy użytkownika. Poczta bazy danych można skonfigurować w SQL Server Management Studio lub za pomocą poleceń języka Transact-SQL. Aby uzyskać więcej informacji, zobacz [poczta bazy danych](/sql/relational-databases/database-mail/database-mail).
1. [Skonfiguruj SQL Server agenta, aby korzystał z poczta bazy danych](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy port SMTP jest dozwolony zarówno za pośrednictwem lokalnej zapory maszyny wirtualnej, jak i sieciowej grupy zabezpieczeń dla maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Automatyczna kopia zapasowa konfiguruje zarządzaną kopię zapasową na maszynach wirtualnych W związku z tym ważne jest [zapoznanie się z dokumentacją zarządzanej kopii zapasowej na SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania dla SQL Server na maszynach wirtualnych platformy Azure można znaleźć w następującym artykule: [Tworzenie kopii zapasowych i przywracanie SQL Server na maszynach wirtualnych platformy Azure](backup-restore.md).

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [SQL Server rozszerzenia agenta IaaS](sql-server-iaas-agent-extension-automate-management.md).

Aby uzyskać więcej informacji na temat uruchamiania SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server Omówienie usługi Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).