---
title: Tworzenie kopii zapasowej Managed Disks platformy Azure przy użyciu Azure PowerShell
description: Dowiedz się, jak utworzyć kopię zapasową Managed Disks platformy Azure przy użyciu Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630495"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Tworzenie kopii zapasowej Managed Disks platformy Azure przy użyciu Azure PowerShell

W tym artykule wyjaśniono, jak utworzyć kopię zapasową [dysku zarządzanego na platformie Azure](../virtual-machines/managed-disks-overview.md) przy użyciu Azure PowerShell.

Ten artykuł obejmuje następujące zagadnienia:

- Tworzenie magazynu kopii zapasowych

- Tworzenie zasad kopii zapasowych

- Konfigurowanie kopii zapasowej dysku platformy Azure

- Uruchamianie zadania tworzenia kopii zapasowej na żądanie

Aby uzyskać informacje na temat dostępności obszaru kopii zapasowej na dysku platformy Azure, obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz obsługi](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Tworzenie magazynu kopii zapasowych

Magazyn kopii zapasowych to jednostka magazynowa na platformie Azure, która przechowuje dane kopii zapasowej dla różnych nowszych obciążeń obsługiwanych przez Azure Backup, takich jak serwery Azure Database for PostgreSQL i dyski platformy Azure. Magazyny kopii zapasowych ułatwiają organizowanie danych kopii zapasowej, jednocześnie minimalizując koszty zarządzania. Magazyny kopii zapasowych są oparte na modelu Azure Resource Manager platformy Azure, który oferuje ulepszone funkcje ułatwiające zabezpieczanie danych kopii zapasowych.

Przed utworzeniem magazynu kopii zapasowych wybierz nadmiarowość magazynu danych w ramach magazynu. Następnie Kontynuuj, aby utworzyć magazyn kopii zapasowych z tą nadmiarowość magazynu i lokalizacją. W tym artykule utworzymy magazyn kopii zapasowych "TestBkpVault" w regionie "zachodnie" w grupie zasobów "testBkpVaultRG". Użyj polecenia [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) , aby utworzyć magazyn kopii zapasowych. Dowiedz się więcej o [tworzeniu magazynu kopii zapasowych](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Po utworzeniu magazynu utwórz zasady tworzenia kopii zapasowej, aby chronić dyski platformy Azure.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Aby zrozumieć wewnętrzne składniki zasad tworzenia kopii zapasowych na dysku Azure, Pobierz szablon zasad przy użyciu polecenia [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). To polecenie zwraca domyślny szablon zasad dla danego typu źródła danych. Użyj tego szablonu zasad, aby utworzyć nowe zasady.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Szablon zasad składa się z wyzwalacza (który decyduje o tym, co wyzwala kopia zapasowa), a także cyklu życia (który decyduje o tym, kiedy usunąć/skopiować/przenieść kopię zapasową). W kopii zapasowej na dysku platformy Azure wartość domyślna dla wyzwalacza to zaplanowany, co 4 godziny (PT4H), aby zachować każdą kopię zapasową przez 7 dni.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Kopia zapasowa Azure Disk oferuje wiele kopii zapasowych dziennie. Jeśli potrzebujesz kilku częstych kopii zapasowych, wybierz częstotliwość wykonywania kopii zapasowych co **godzinę** , aby tworzyć kopie zapasowe z interwałami co 4, 6, 8 lub 12 godzin. Kopie zapasowe są planowane na podstawie wybranego przedziału **czasu** . Jeśli na przykład wybierzesz **co 4 godziny**, kopie zapasowe zostaną wykonane o około 4 godzinach, a kopie zapasowe są równomiernie rozłożone na cały dzień. Jeśli wystarcza kopia zapasowa raz dziennie, wybierz częstotliwość **codziennej** kopii zapasowej. W przypadku częstotliwości codziennej kopii zapasowej można określić godzinę wykonywania kopii zapasowych. Należy pamiętać, że czas dnia wskazuje godzinę rozpoczęcia tworzenia kopii zapasowej, a nie godzinę ukończenia tworzenia kopii zapasowej. Czas wymagany do ukończenia operacji tworzenia kopii zapasowej zależy od różnych czynników, w tym rozmiaru dysku, i współczynnika zmian między kolejnymi kopiami zapasowymi. Kopia zapasowa dysku platformy Azure to jednak kopia zapasowa bez agenta, która korzysta z [migawek przyrostowych](../virtual-machines/disks-incremental-snapshots.md), co nie ma wpływu na wydajność aplikacji produkcyjnej.

   >[!NOTE]
   > Mimo że wybrany magazyn może mieć ustawienie Global-nadmiarowości, obecnie kopia zapasowa dysku platformy Azure obsługuje tylko funkcję Snapshot Store. Wszystkie kopie zapasowe są przechowywane w grupie zasobów w subskrypcji i nie są kopiowane do magazynu magazynów kopii zapasowych.

Aby dowiedzieć się więcej o tworzeniu zasad, zapoznaj się z dokumentem [zasad tworzenia kopii zapasowej na dysku Azure](backup-managed-disks.md#create-backup-policy) .

Jeśli chcesz edytować częstotliwość godzinową lub okres przechowywania, Użyj poleceń [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) i/lub [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . Gdy obiekt zasad ma wszystkie wymagane wartości, należy utworzyć nowe zasady z obiektu zasad przy użyciu polecenia [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Po utworzeniu magazynu i zasad istnieją 3 krytyczne punkty, które użytkownik musi wziąć pod uwagę w celu ochrony dysku platformy Azure.

### <a name="key-entities-involved"></a>Jednostki kluczy, których dotyczy

#### <a name="disk-to-be-protected"></a>Dysk, który ma być chroniony

Pobierz identyfikator ARM dysku, który ma być chroniony. Ta wartość będzie stanowić identyfikator dysku. Będziemy używać przykładowego dysku o nazwie "PSTestDisk" w grupie zasobów "diskrg" w ramach innej subskrypcji.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Grupa zasobów migawek

Migawki dysku są przechowywane w grupie zasobów w ramach subskrypcji. Jako wskazówkę zaleca się utworzenie dedykowanej grupy zasobów jako magazynu danych migawek, która ma być używana przez usługę Azure Backup. Posiadanie dedykowanej grupy zasobów pozwala ograniczyć uprawnienia dostępu do grupy zasobów, zapewniając bezpieczeństwo i łatwość zarządzania danymi kopii zapasowych. Zanotuj identyfikator ARM dla grupy zasobów, w której chcesz umieścić migawki dysku

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Magazyn kopii zapasowych

Magazyny kopii zapasowych wymagają uprawnień na dysku i grupie zasobów migawek, aby móc wyzwolić migawki i zarządzać ich cyklem życia. Zarządzana przez system tożsamość magazynu jest używana do przypisywania takich uprawnień. Użyj polecenia [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) , aby włączyć tożsamość zarządzaną przypisaną przez system dla magazynu usługi Recovery Services.

### <a name="assign-permissions"></a>Przypisywanie uprawnień

Użytkownik musi przypisać kilka uprawnień za pośrednictwem RBAC do magazynu (reprezentowane przez plik MSI magazynu) oraz odpowiedni dysk i/lub dysk RG. Można je wykonać za pośrednictwem portalu lub programu PowerShell. Wszystkie powiązane uprawnienia są szczegółowo opisane w punkcie 1, 2, 3 w [tej sekcji](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Przygotuj żądanie

Po ustawieniu wszystkich odpowiednich uprawnień Konfiguracja kopii zapasowej jest wykonywana w dwóch krokach. Najpierw przygotujemy odpowiednie żądanie przy użyciu odpowiedniego magazynu, zasad, dysku i grupy zasobów migawek przy użyciu polecenia [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Następnie wyślemy żądanie ochrony dysku przy użyciu polecenia [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

Pobierz odpowiednie wystąpienie kopii zapasowej, na którym użytkownik chce wyzwolić kopię zapasową za pomocą polecenia [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Można określić regułę przechowywania podczas wyzwalania kopii zapasowej. Aby wyświetlić reguły przechowywania w zasadach, przejdź przez obiekt zasad dla reguł przechowywania. W poniższym przykładzie zostanie wyświetlona reguła o nazwie "default". Ta reguła zostanie użyta do utworzenia kopii zapasowej na żądanie

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Wyzwól tworzenie kopii zapasowej na żądanie za pomocą polecenia [Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Zadania śledzenia

Śledź wszystkie zadania za pomocą polecenia [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Można wyświetlić listę wszystkich zadań i pobrać szczegóły konkretnego zadania.

Można również użyć AZ. ResourceGraph, aby śledzić wszystkie zadania w ramach wszystkich magazynów kopii zapasowych. Użyj polecenia [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) , aby uzyskać odpowiednie zadania, które mogą znajdować się w magazynie kopii zapasowych.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Następne kroki

- [Przywracanie Managed Disks platformy Azure przy użyciu Azure PowerShell](restore-managed-disks-ps.md)
