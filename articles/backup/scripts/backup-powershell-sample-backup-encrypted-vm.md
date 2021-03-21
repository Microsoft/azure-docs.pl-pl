---
title: Przykładowy skrypt programu PowerShell — tworzenie kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak za pomocą przykładowego skryptu Azure PowerShell utworzyć kopię zapasową maszyny wirtualnej platformy Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97967331"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Tworzenie zaszyfrowanej kopii zapasowej maszyny wirtualnej platformy Azure za pomocą programu PowerShell

Ten skrypt tworzy magazyn Recovery Services z magazynem geograficznie nadmiarowym (GRS) dla zaszyfrowanej maszyny wirtualnej platformy Azure. Względem magazynu są stosowane domyślne zasady ochrony. Zasady generują codzienne kopie zapasowe maszyny wirtualnej, a każda kopia zapasowa jest przechowywana przez 365 dni. Skrypt wyzwala również początkowy punkt odzyskiwania dla maszyny wirtualnej i zachowuje ten punkt odzyskiwania przez 30 dni.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Tworzy magazyn Recovery Services do przechowywania kopii zapasowych. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Określa właściwości magazynu kopii zapasowych w magazynie usług Recovery Services. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Tworzy zasady ochrony, używając zasad planowania i zasad przechowywania, w magazynie usług Recovery Services. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Umożliwia utworzenie kopii zapasowej elementu za pomocą określonych zasad ochrony kopii zapasowych. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modyfikuje istniejące zasady ochrony kopii zapasowych. |
| [Kopia zapasowa — AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Uruchamia kopię zapasową chronionego Azure Backup elementu, który nie jest powiązany z harmonogramem tworzenia kopii zapasowych. |
| [Oczekiwanie — AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Czeka na zakończenie zadania usługi Azure Backup. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/new-azureps-module-az).
