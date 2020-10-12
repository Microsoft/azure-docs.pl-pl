---
title: Tworzenie usługi Azure Private link przy użyciu Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć usługę prywatnego połączenia platformy Azure przy użyciu Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88505636"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Tworzenie usługi linku prywatnego przy użyciu Azure PowerShell
W tym artykule opisano sposób tworzenia usługi linku prywatnego na platformie Azure przy użyciu Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał najnowszej wersji modułu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby można było utworzyć link prywatny, należy utworzyć grupę zasobów z poleceniem [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną dla prywatnego linku przy użyciu elementu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myvnet* z podsiecią dla frontonu (*frontendSubnet*), zaplecza (*backendSubnet*), link prywatny (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>Utwórz wewnętrzny Load Balancer
Utwórz usługa Load Balancer w warstwie Standardowa wewnętrzny przy użyciu właściwości [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy wewnętrzny usługa Load Balancer w warstwie Standardowa przy użyciu konfiguracji adresu IP frontonu, sondy, reguły i puli zaplecza, która została utworzona w poprzednich krokach:

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego
Utwórz usługę linku prywatnego przy użyciu elementu [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  W tym przykładzie tworzona jest usługa linku prywatnego o nazwie *myPLS* przy użyciu usługa Load Balancer w warstwie Standardowa w grupie zasobów o nazwie Moja *resourceName*.
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>Pobierz usługę linku prywatnego
Uzyskaj szczegółowe informacje o usłudze linku prywatnego za pomocą [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) w następujący sposób:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

Na tym etapie usługa linku prywatnego została pomyślnie utworzona i będzie gotowa do odbierania ruchu. Należy zauważyć, że powyższy przykład dotyczy tylko tworzenia usługi link prywatny przy użyciu programu PowerShell.  Nie skonfigurowano pul zaplecza modułu równoważenia obciążenia ani żadnej aplikacji w pulach zaplecza do nasłuchiwania ruchu. Jeśli chcesz zobaczyć kompleksowe przepływy ruchu, stanowczo zalecamy skonfigurowanie aplikacji za usługą równoważenia obciążenia w warstwie Standardowa.

Następnie pokażemy sposób mapowania tej usługi do prywatnego punktu końcowego w innej sieci wirtualnej przy użyciu programu PowerShell. Ponownie Przykładowo można utworzyć prywatny punkt końcowy i połączyć się z usługą link prywatny utworzony powyżej. Możesz utworzyć Virtual Machines w Virtual Network, aby wysłać/odebrać ruch do prywatnego punktu końcowego w celu utworzenia scenariusza.

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną dla prywatnego punktu końcowego za pomocą elementu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Ten przykład umożliwia utworzenie sieci wirtualnej o nazwie *vnetPE*   w grupie zasobów o nazwie Moja *zasobów*:

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Utwórz prywatny punkt końcowy do korzystania z usługi linku prywatnego utworzonego powyżej w sieci wirtualnej:

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>Pobierz prywatny punkt końcowy
Uzyskaj adres IP prywatnego punktu końcowego w `Get-AzPrivateEndpoint` następujący sposób:

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>Zatwierdź połączenie prywatnego punktu końcowego
Zatwierdź połączenie prywatnego punktu końcowego z usługą linku prywatnym przy użyciu "zatwierdzenie-AzPrivateEndpointConnection".

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)

