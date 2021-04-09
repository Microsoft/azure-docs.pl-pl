---
title: Tworzenie pulpitu nawigacyjnego Azure Portal przy użyciu programu PowerShell
description: Dowiedz się, jak utworzyć pulpit nawigacyjny w Azure Portal przy użyciu Azure PowerShell.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 03/25/2021
ms.openlocfilehash: cd001a8259c54f1d86aab5983da1413c8163008c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557449"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Szybki Start: tworzenie pulpitu nawigacyjnego Azure Portal przy użyciu programu PowerShell

Pulpit nawigacyjny w Azure Portal to skoncentrowany i zorganizowany widok zasobów w chmurze. Ten artykuł koncentruje się na procesie korzystania z modułu programu PowerShell AZ. Portal w celu utworzenia pulpitu nawigacyjnego.
Pulpit nawigacyjny pokazuje wydajność maszyny wirtualnej, a także pewne informacje i linki statyczne.

## <a name="requirements"></a>Wymagania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Gdy moduł programu PowerShell **AZ. Portal** jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Wybierz określoną subskrypcję platformy Azure

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definiowanie zmiennych

Będziesz wielokrotnie używać kilku informacji. Utwórz zmienne do przechowywania informacji.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów na podstawie nazwy w `$resourceGroupName` zmiennej w regionie określonym w `$location` zmiennej.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Pulpit nawigacyjny tworzony w następnej części tego przewodnika Szybki Start wymaga istniejącej maszyny wirtualnej. Utwórz maszynę wirtualną, wykonując następujące kroki.

Przechowywanie poświadczeń logowania dla maszyny wirtualnej w zmiennej. Hasło musi być złożone. To jest nowa nazwa użytkownika i hasło. nie jest to na przykład konto używane do logowania się na platformie Azure. Aby uzyskać więcej informacji, zobacz Wymagania dotyczące [nazwy użytkownika](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) i [hasła](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Utwórz maszynę wirtualną.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

Wdrożenie maszyny wirtualnej rozpocznie się i zwykle trwa kilka minut. Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="download-the-dashboard-template"></a>Pobierz szablon pulpitu nawigacyjnego

Ponieważ pulpity nawigacyjne platformy Azure to zasoby, mogą być reprezentowane jako dane JSON. Poniższy kod Pobiera reprezentację w formacie JSON przykładowego pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [strukturę pulpitów nawigacyjnych platformy Azure](./azure-portal-dashboards-structure.md).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$HOME\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Dostosowywanie szablonu

Dostosuj pobrany szablon, uruchamiając następujący kod.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu pulpitów nawigacyjnych w portalu Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Wdrażanie szablonu pulpitu nawigacyjnego

Za pomocą `New-AzPortalDashboard` polecenia cmdlet, które jest częścią modułu AZ. Portal, można wdrożyć szablon bezpośrednio w programie PowerShell.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Sprawdź, czy pulpit nawigacyjny został utworzony pomyślnie.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć maszynę wirtualną i skojarzony z nią pulpit nawigacyjny, Usuń grupę zasobów, która ją zawiera.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
Remove-Item -Path "$HOME\portal-dashboard-template-testvm.json"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o poleceniach cmdlet zawartych w module AZ. Portal PowerShell, zobacz:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: polecenia cmdlet pulpitu nawigacyjnego portalu](/powershell/module/Az.Portal/)