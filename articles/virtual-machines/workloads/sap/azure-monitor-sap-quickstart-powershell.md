---
title: Wdrażanie Azure Monitor rozwiązań SAP za pomocą Azure PowerShell
description: Wdrażanie Azure Monitor rozwiązań SAP za pomocą Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538694"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Szybki start: Wdrażanie usługi Azure Monitor dla rozwiązań SAP za pomocą programu Azure PowerShell

W tym artykule opisano sposób tworzenia zasobów Azure Monitor dla rozwiązań SAP przy użyciu [modułu Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell.

> [!CAUTION]
> Azure Monitor dla rozwiązań SAP jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [Omówienie Azure Cloud Shell,](../../../cloud-shell/overview.md) aby uzyskać więcej informacji.

> [!IMPORTANT]
> Gdy moduł **Az.HanaOnAzure** PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, stanie się częścią przyszłych wersji modułu Az programu PowerShell i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../../azure-resource-manager/management/overview.md) przy użyciu polecenia cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Monitor SAP

Aby utworzyć monitor SAP, użyj polecenia cmdlet [New-AzSapMonitor.](/powershell/module/az.hanaonazure/new-azsapmonitor) Poniższy przykład tworzy monitor SAP dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Aby pobrać właściwości monitora SAP, użyj polecenia cmdlet [Get-AzSapMonitor.](/powershell/module/az.hanaonazure/get-azsapmonitor) Poniższy przykład pobiera właściwości monitora SAP dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Wystąpienie dostawcy

Aby utworzyć wystąpienie dostawcy, użyj polecenia cmdlet [New-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) Poniższy przykład tworzy wystąpienie dostawcy dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Aby pobrać właściwości wystąpienia dostawcy, użyj polecenia cmdlet [Get-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) Poniższy przykład pobiera właściwości wystąpienia dostawcy dla określonej subskrypcji, grupy zasobów, nazwy systemu SapMonitor i nazwy zasobu.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, możesz je usunąć, uruchamiając poniższe przykłady.

### <a name="delete-the-provider-instance"></a>Usuwanie wystąpienia dostawcy

Aby usunąć wystąpienie dostawcy, użyj polecenia cmdlet [Remove-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) Poniższy przykład usuwa wystąpienie dostawcy dla określonej subskrypcji, grupy zasobów, nazwy systemu SapMonitor i nazwy zasobu.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Usuwanie monitora SAP

Aby usunąć monitor SAP, użyj polecenia cmdlet [Remove-AzSapMonitor.](/powershell/module/az.hanaonazure/remove-azsapmonitor) Poniższy przykład usuwa monitor SAP dla określonej subskrypcji, grupy zasobów i nazwy monitora.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby poza zakresem tego artykułu istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Monitor dla rozwiązań SAP](azure-monitor-overview.md).
