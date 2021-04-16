---
title: Przywracanie usługi Azure Dyski zarządzane za pośrednictwem Azure PowerShell
description: Dowiedz się, jak przywrócić usługę Azure Dyski zarządzane przy użyciu Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520041"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Przywracanie usługi Azure Dyski zarządzane przy użyciu Azure PowerShell

W tym artykule wyjaśniono, jak przywrócić [usługę Azure Dyski zarządzane](../virtual-machines/managed-disks-overview.md) z punktu przywracania utworzonego przez Azure Backup.

Obecnie opcja Original-Location Recovery (OLR) przywracania przez zastąpienie istniejącego dysku źródłowego, z którego zostały wykonane kopie zapasowe, nie jest obsługiwana. Możesz przywrócić z punktu odzyskiwania, aby utworzyć nowy dysk w tej samej grupie zasobów co dysk źródłowy, z którego zostały wykonane kopie zapasowe, lub w dowolnej innej grupie zasobów. Jest to nazywane Alternate-Location Recovery (ALR), co pomaga zachować zarówno dysk źródłowy, jak i przywrócony (nowy).

Ten artykuł obejmuje następujące zagadnienia:

- Przywracanie w celu utworzenia nowego dysku

- Śledzenie stanu operacji przywracania

W przykładach będziemy odwoływać się do istniejącego magazynu kopii zapasowych "TestBkpVault" w grupie zasobów "testBkpVaultRG"

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Przywracanie w celu utworzenia nowego dysku

### <a name="setting-up-permissions"></a>Konfigurowanie uprawnień

Usługa Backup Vault używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby przywrócić z kopii zapasowej, tożsamość zarządzana magazynu usługi Backup wymaga zestawu uprawnień do grupy zasobów, w której ma zostać przywrócony dysk.

Magazyn kopii zapasowych używa przypisanej przez system tożsamości zarządzanej, która jest ograniczona do jednej dla każdego zasobu i jest powiązana z cyklem życia tego zasobu. Uprawnienia do tożsamości zarządzanej można przyznać przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. Tożsamość zarządzana jest jednostką usługi specjalnego typu, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych.](../active-directory/managed-identities-azure-resources/overview.md)

Przypisz odpowiednie uprawnienia dla tożsamości zarządzanej przypisanej przez system magazynu do docelowej grupy zasobów, w której dyski zostaną przywrócone/utworzone, jak [wspomniano tutaj.](restore-managed-disks.md#restore-to-create-a-new-disk)

### <a name="fetching-the-relevant-recovery-point"></a>Pobieranie odpowiedniego punktu odzyskiwania

Pobierz wszystkie wystąpienia za pomocą [polecenia Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) i zidentyfikuj odpowiednie wystąpienie.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Możesz również użyć **polecenia Az.Resourcegraph** i [Search-AzDataProtectionBackupInstanceInAzGraph,](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) aby przeszukiwać wystąpienia w wielu magazynach i subskrypcjach.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Po zidentyfikowaniu wystąpienia pobierz odpowiedni punkt odzyskiwania.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Przygotowywanie żądania przywracania

Skonstruuj identyfikator ARM nowego dysku, który ma zostać utworzony z [](#setting-up-permissions)docelową grupą zasobów, do której przypisano uprawnienia zgodnie z powyższymi informacjami, oraz wymaganą nazwę dysku. Na przykład dysk może mieć nazwę **PSTestDisk2** w ramach grupy **zasobów targetrg** z inną subskrypcją.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Użyj polecenia [Initialize-AzDataProtectionRestoreRequest,](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) aby przygotować żądanie przywrócenia ze wszystkimi odpowiednimi szczegółami.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Wyzwalanie przywracania

Użyj polecenia [Start-AzDataProtectionBackupInstanceRestore,](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) aby wyzwolić przywracanie przy użyciu przygotowanego powyżej żądania.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>Zadanie śledzenia

Śledź wszystkie zadania za pomocą [polecenia Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) Możesz wyświetlić listę wszystkich zadań i pobrać szczegóły określonego zadania.

Za pomocą funkcji **Az.ResourceGraph można również** śledzić wszystkie zadania we wszystkich magazynach kopii zapasowych. Użyj polecenia [Search-AzDataProtectionJobInAzGraph,](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) aby uzyskać odpowiednie zadanie, które może być w dowolnym magazynie kopii zapasowych.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Następne kroki

- [Azure Disk Backup — często zadawane pytania](disk-backup-faq.yml)
