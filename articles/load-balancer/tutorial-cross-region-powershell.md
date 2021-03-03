---
title: 'Samouczek: Tworzenie międzyregionowego modułu równoważenia obciążenia za pomocą Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Rozpocznij pracę z tym samouczkiem, wdrażając Azure Load Balancer międzyregionowe przy użyciu Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721348"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Samouczek: Tworzenie Azure Load Balancer międzyregionowych przy użyciu Azure PowerShell

Moduł równoważenia obciążenia między regionami gwarantuje, że usługa jest dostępna globalnie w wielu regionach świadczenia usługi Azure. Jeśli jeden region ulegnie awarii, ruch jest kierowany do następnego najbliższego w dobrej kondycji regionalnego modułu równoważenia obciążenia.  

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz międzyregionowy moduł równoważenia obciążenia.
> * Utwórz regułę modułu równoważenia obciążenia.
> * Utwórz pulę zaplecza zawierającą dwa regionalne moduły równoważenia obciążenia.
> * Przetestuj moduł równoważenia obciążenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Dwie usługi równoważenia obciążenia platformy Azure w **warstwie Standardowa** z pulami zaplecza wdrożonymi w dwóch różnych regionach świadczenia usługi Azure.
    - Aby uzyskać informacje na temat tworzenia regionalnego standardowego modułu równoważenia obciążenia i maszyn wirtualnych dla pul zaplecza, zobacz [Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Dołącz nazwy modułów równoważenia obciążenia i maszyn wirtualnych w każdym regionie z **-R1** i **-R2**. 
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell.


Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-cross-region-load-balancer"></a>Tworzenie międzyregionowego modułu równoważenia obciążenia


### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia obejmującego wiele regionów

W tej sekcji opisano tworzenie zasobów wymaganych dla międzyregionowego modułu równoważenia obciążenia.

Globalny publiczny adres IP w warstwie Standardowa jest używany na potrzeby frontonu modułu równoważenia obciążenia między regionami.

* Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć publiczny adres IP.

* Utwórz konfigurację adresu IP frontonu przy użyciu elementu [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Utwórz pulę adresów zaplecza za pomocą elementu [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Utwórz międzyregionowy moduł równoważenia obciążenia za pomocą elementu [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Konfigurowanie puli zaplecza

W tej sekcji dodasz dwa regionalne standardowe usługi równoważenia obciążenia do puli zaplecza modułu równoważenia obciążenia między regionami.

> [!IMPORTANT]
> Aby wykonać te kroki, należy się upewnić, że w subskrypcji wdrożono dwa regionalne moduły równoważenia obciążenia z pulami zaplecza.  Aby uzyskać więcej informacji, zobacz **[Przewodnik Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Użyj parametrów [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) i [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) , aby przechowywać informacje o regionalnym module równoważenia obciążenia w zmiennych.

* Użyj [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) , aby utworzyć konfigurację puli adresów zaplecza dla modułu równoważenia obciążenia.

* Użyj [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) , aby dodać fronton modułu równoważenia obciążenia do wieloregionowej puli zaplecza.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji przetestujesz moduł równoważenia obciążenia między regionami. Nastąpi połączenie z publicznym adresem IP w przeglądarce internetowej.  Zatrzymasz maszyny wirtualne w jednym z pul zaplecza modułu równoważenia obciążenia i obserwuj przełączenia w tryb failover.

1. Użyj [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) , aby uzyskać publiczny adres IP modułu równoważenia obciążenia:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

3. Zatrzymaj maszyny wirtualne w puli zaplecza jednego z regionalnych modułów równoważenia obciążenia.

4. Odśwież przeglądarkę internetową i zaobserwuj tryb failover połączenia z innym regionalnym modułem równoważenia obciążenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i pozostałe zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono globalny adres IP.
* Utworzono moduł równoważenia obciążenia między regionami.
* Utworzono regułę równoważenia obciążenia.
* Dodano regionalne moduły równoważenia obciążenia do puli zaplecza międzyregionowego modułu równoważenia obciążenia.
* Przetestowano moduł równoważenia obciążenia.


Przejdź do następnego artykułu, aby dowiedzieć się, jak to zrobić...
> [!div class="nextstepaction"]
> [Maszyny wirtualne usługi równoważenia obciążenia w strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)