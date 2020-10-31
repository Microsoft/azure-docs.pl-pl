---
title: Utwórz i skonfiguruj plan Azure DDoS Protection przy użyciu Azure PowerShell
description: Dowiedz się, jak utworzyć plan DDoS Protection przy użyciu Azure PowerShell
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 49fa0c849a6b2eab0647922a711c50b4fe762584
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095683"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Szybki Start: Tworzenie i Konfigurowanie standardu Azure DDoS Protection przy użyciu Azure PowerShell

Rozpocznij pracę ze standardem Azure DDoS Protection przy użyciu Azure PowerShell. 

Plan ochrony DDoS definiuje zbiór sieci wirtualnych, dla których włączono standard ochrony DDoS w ramach subskrypcji. Można skonfigurować jeden plan ochrony DDoS dla Twojej organizacji i połączyć sieci wirtualne z wielu subskrypcji w ramach tego samego planu. 

W tym przewodniku szybki start utworzysz plan ochrony DDoS i połączysz go z siecią wirtualną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Utwórz plan DDoS Protection

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć grupę zasobów, użyj polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). W tym przykładzie zmienimy nazwę _naszej grupy zasobów_ i użyjemy lokalizacji _Wschodnie stany USA_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Teraz Utwórz plan ochrony DDoS o nazwie _MyDdosProtectionPlan_ :

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Włącz DDoS dla sieci wirtualnej

### <a name="enable-ddos-for-a-new-virtual-network"></a>Włącz DDoS dla nowej sieci wirtualnej

Ochronę DDoS można włączyć podczas tworzenia sieci wirtualnej. W tym przykładzie zmienimy nazwę naszej sieci wirtualnej _MyVnet_ : 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Włącz DDoS dla istniejącej sieci wirtualnej

Istnieje możliwość skojarzenia istniejącej sieci wirtualnej podczas tworzenia planu ochrony DDoS:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Najpierw zapoznaj się ze szczegółami planu ochrony DDoS:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Sprawdź, czy polecenie zwraca poprawne szczegóły planu ochrony DDoS.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby dla następnego samouczka. Jeśli nie jest już potrzebne, Usuń grupę _zasobów zasobu._ Po usunięciu grupy zasobów należy również usunąć plan ochrony DDoS i wszystkie powiązane z nim zasoby. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Aby wyłączyć ochronę DDoS dla sieci wirtualnej: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

W celu usunięcia planu ochrony DDoS należy najpierw usunąć skojarzenie wszystkich sieci wirtualnych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wyświetlać i konfigurować dane telemetryczne dla planu ochrony DDoS, przejdź do samouczków.

> [!div class="nextstepaction"]
> [Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS](telemetry-monitoring-alerting.md)