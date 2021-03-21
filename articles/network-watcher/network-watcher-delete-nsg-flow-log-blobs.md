---
title: Usuwanie obiektów blob magazynu dla dzienników przepływu sieciowych grup zabezpieczeń na platformie Azure Network Watcher | Microsoft Docs
description: W tym artykule wyjaśniono, jak usunąć obiekty blob magazynu dzienników usługi Network Security Group, które są poza okresem zasad przechowywania w usłudze Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 1b73b8fa14271619d07fca63b01e4197f4f7c8cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017854"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Usuwanie obiektów blob magazynu dzienników sieciowej grupy zabezpieczeń w Network Watcher

W przypadku konieczności ręcznego usuwania dzienników przepływów z konta magazynu możesz użyć poniższego skryptu programu PowerShell.
Ten skrypt usuwa tylko obiekty blob magazynu starsze niż istniejące zasady przechowywania określone przez użytkownika.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>Uruchom skrypt programu PowerShell, aby usunąć dzienniki przepływu sieciowej grupy zabezpieczeń
 
Skopiuj i Zapisz następujący skrypt w lokalizacji, na przykład w bieżącym katalogu roboczym. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. W razie konieczności wprowadź następujące parametry skryptu:
   - **Subskrypcji** [obowiązkowe]: Identyfikator subskrypcji, z której chcesz usunąć obiekty blob dziennika przepływu sieciowej grupy zabezpieczeń.
   - **Lokalizacja** [obowiązkowe]: _ciąg lokalizacji_ regionu sieciowych grup zabezpieczeń, dla którego chcesz usunąć obiekty blob dziennika przepływu sieciowej grupy zabezpieczeń. Te informacje można wyświetlić na Azure Portal lub w witrynie [GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23).
   - **Potwierdź** , że [opcjonalne]: Przekaż flagę Confirm, jeśli chcesz ręcznie potwierdzić usunięcie każdego magazynu obiektów BLOB.

1. Uruchom zapisany skrypt, jak pokazano w poniższym przykładzie, gdzie plik skryptu został zapisany jako **Delete-NsgFlowLogsBlobs.ps1**:
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>Następne kroki
- Klienci mogą zautomatyzować uruchamianie skryptu przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lub [Azure Automation](https://azure.microsoft.com/services/automation/)
- Aby dowiedzieć się więcej o rejestrowaniu sieciowej grupy zabezpieczeń, zobacz [dzienniki Azure monitor dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

