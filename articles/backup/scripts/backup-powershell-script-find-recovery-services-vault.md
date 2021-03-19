---
title: Skrypt programu PowerShell — Znajdowanie magazynu dla konta magazynu
description: Dowiedz się, jak używać skryptu Azure PowerShell, aby znaleźć magazyn Recovery Services, w którym zarejestrowano konto magazynu.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075702"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Skrypt programu PowerShell umożliwiający znalezienie magazynu Recovery Services, w którym zarejestrowano konto magazynu

Ten skrypt ułatwia znalezienie magazynu Recovery Services, w którym zarejestrowano konto magazynu.

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Jak wykonać skrypt

1. Zapisz skrypt powyżej na swojej maszynie, podając wybraną nazwę. W tym przykładzie Zapisano go jako *FindRegisteredStorageAccount.ps1*.
2. Wykonaj skrypt, podając następujące parametry:

    * **-ResourceGroupName** — Grupa zasobów konta magazynu
    * **-StorageAccountName** — nazwa konta magazynu
    * **-** Subscription — Identyfikator subskrypcji, w której znajduje się konto magazynu.

Poniższy przykład próbuje znaleźć magazyn Recovery Services, w którym zarejestrowano konto magazynu *afsaccount* :

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Dane wyjściowe

W danych wyjściowych zostanie wyświetlona pełna ścieżka do magazynu Recovery Services, w którym zarejestrowano konto magazynu. Oto przykładowe dane wyjściowe:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak tworzyć kopie zapasowe udziałów plików platformy Azure z Azure Portal](../backup-afs.md)
