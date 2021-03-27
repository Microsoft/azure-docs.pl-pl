---
title: Przywracanie Managed Disks platformy Azure za pośrednictwem Azure PowerShell
description: Dowiedz się, jak przywrócić Managed Disks platformy Azure przy użyciu Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0ddf552947c39692ea01d0dea7e67f147d754fcc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630445"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Przywracanie Managed Disks platformy Azure przy użyciu Azure PowerShell

W tym artykule wyjaśniono, jak przywrócić [Managed disks platformy Azure](../virtual-machines/managed-disks-overview.md) z punktu przywracania utworzonego przez Azure Backup.

Obecnie opcja odzyskiwania Original-Location (OLR) przywracania przez zastąpienie istniejącego dysku źródłowego, z którego wykonano kopie zapasowe, nie jest obsługiwana. Można przywrócić z punktu odzyskiwania, aby utworzyć nowy dysk w tej samej grupie zasobów co dysk źródłowy, z którego wykonano kopie zapasowe lub w innej grupie zasobów. Jest to nazywane Alternate-Location odzyskiwaniem (ALR) i pomaga zachować zarówno dysk źródłowy, jak i przywrócony (nowy) dysk.

Ten artykuł obejmuje następujące zagadnienia:

- Przywróć, aby utworzyć nowy dysk

- Śledzenie stanu operacji przywracania

Odwołujemy się do istniejącego magazynu kopii zapasowych "TestBkpVault" w grupie zasobów "testBkpVaultRG" w przykładach

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Przywróć, aby utworzyć nowy dysk

### <a name="setting-up-permissions"></a>Konfigurowanie uprawnień

Magazyn kopii zapasowych używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby przywrócić kopię zapasową, zarządzana tożsamość magazynu kopii zapasowych wymaga zestawu uprawnień do grupy zasobów, w której dysk ma zostać przywrócony.

Magazyn kopii zapasowych używa tożsamości zarządzanej przypisanej do systemu, która jest ograniczona do jednego na zasób i jest związana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można udzielić przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Tożsamość zarządzana to nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md).

Przypisz odpowiednie uprawnienia do zarządzanej tożsamości przypisanej do systemu magazynu w docelowej grupie zasobów, w której dyski zostaną przywrócone/utworzone zgodnie z opisem w [tym miejscu](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetching-the-relevant-recovery-point"></a>Pobieranie odpowiedniego punktu odzyskiwania

Pobierz wszystkie wystąpienia za pomocą polecenia [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) i zidentyfikuj odpowiednie wystąpienie.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Możesz również użyć polecenia **AZ. Resourcegraph** i [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) , aby wyszukiwać między wystąpieniami w wielu magazynach i subskrypcjach.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Po zidentyfikowaniu wystąpienia Pobierz odpowiedni punkt odzyskiwania.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Przygotowywanie żądania przywracania

Utwórz identyfikator ARM nowego dysku, który ma zostać utworzony z docelową grupą zasobów, do której przypisano uprawnienia zgodnie z [powyższymi](#setting-up-permissions)informacjami, oraz wymaganą nazwę dysku. Na przykład dysk może mieć nazwę **PSTestDisk2** w grupie zasobów **targetrg** z inną subskrypcją.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Użyj polecenia [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) , aby przygotować żądanie przywracania ze wszystkimi odpowiednimi szczegółami.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Wyzwalanie przywracania

Użyj polecenia [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) , aby wyzwolić przywracanie przy użyciu żądania przygotowanego powyżej.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Zadanie śledzenia

Śledź wszystkie zadania za pomocą polecenia [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Można wyświetlić listę wszystkich zadań i pobrać szczegóły konkretnego zadania.

Można również użyć **AZ. ResourceGraph** , aby śledzić wszystkie zadania w ramach wszystkich magazynów kopii zapasowych. Użyj polecenia [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) , aby uzyskać odpowiednie zadania, które mogą znajdować się w magazynie kopii zapasowych.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Następne kroki

- [Kopia zapasowa Azure Disk — często zadawane pytania](disk-backup-faq.md)
