---
title: Wdrażanie Azure Monitor dla rozwiązań SAP z Azure PowerShell
description: Wdrażanie Azure Monitor dla rozwiązań SAP z Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0a81d6c1849de52bd3103005afaa9ccd01a517a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961157"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Szybki Start: wdrażanie Azure Monitor dla rozwiązań SAP z Azure PowerShell

W tym artykule opisano sposób tworzenia Azure Monitor dla zasobów rozwiązań SAP za pomocą polecenia [AZ. HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell.

> [!CAUTION]
> Azure Monitor dla rozwiązań SAP jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [omówienie Azure Cloud Shell](../../../cloud-shell/overview.md) , aby uzyskać więcej informacji.

> [!IMPORTANT]
> Mimo że moduł **AZ. HanaOnAzure** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Monitor SAP

Aby utworzyć monitor SAP, należy użyć polecenia cmdlet [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) . Poniższy przykład tworzy monitor SAP dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

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

Aby pobrać właściwości monitora SAP, należy użyć polecenia cmdlet [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) . Poniższy przykład pobiera właściwości monitora SAP dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Wystąpienie dostawcy

Aby utworzyć wystąpienie dostawcy, należy użyć polecenia cmdlet [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) . Poniższy przykład tworzy wystąpienie dostawcy dla określonej subskrypcji, grupy zasobów i nazwy zasobu.

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

Aby pobrać właściwości wystąpienia dostawcy, należy użyć polecenia cmdlet [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) . Poniższy przykład pobiera właściwości wystąpienia dostawcy dla określonej subskrypcji, grupy zasobów, nazwy SapMonitor i nazwy zasobu.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, można je usunąć, uruchamiając następujące przykłady.

### <a name="delete-the-provider-instance"></a>Usuwanie wystąpienia dostawcy

Aby usunąć wystąpienie dostawcy, należy użyć polecenia cmdlet [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) . Poniższy przykład usuwa wystąpienie dostawcy dla określonej subskrypcji, grupy zasobów, nazwy SapMonitor i nazwy zasobu.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Usuń monitor SAP

Aby usunąć monitor SAP, należy użyć polecenia cmdlet [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) . Poniższy przykład usuwa monitor SAP dla określonej subskrypcji, grupy zasobów i nazwy monitora.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat Azure monitor dla rozwiązań SAP](azure-monitor-overview.md).