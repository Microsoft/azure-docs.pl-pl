---
title: 'Szybki start: tworzenie i konfigurowanie serwera tras przy użyciu Azure PowerShell'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć i skonfigurować serwer tras przy użyciu Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538712"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Szybki start: tworzenie i konfigurowanie serwera tras przy użyciu Azure PowerShell

Ten artykuł ułatwia skonfigurowanie serwera usługi Azure Route Server do komunikacji równorzędnej z wirtualnym urządzeniem sieciowym (WUS) w sieci wirtualnej przy użyciu programu PowerShell. Serwer usługi Azure Route Server będzie uczyć trasy z urządzenia WUS i programować je na maszynach wirtualnych w sieci wirtualnej. Serwer usługi Azure Route Server będzie również anonsować trasy sieci wirtualnej do urządzenia WUS. Aby uzyskać więcej informacji, zapoznaj się [z tematem Azure Route Server](overview.md).

> [!IMPORTANT]
> Serwer usługi Azure Route Server (wersja zapoznawcza) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Upewnij się, że masz najnowsze moduły programu PowerShell lub możesz użyć Azure Cloud Shell w portalu.
* Zapoznaj się z [limitami usługi dla serwera usługi Azure Route Server.](route-server-faq.md#limitations)

## <a name="create-a-route-server"></a>Tworzenie serwera tras

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz subskrypcję.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej

Aby można było utworzyć serwer usługi Azure Route Server, musisz mieć sieć wirtualną, która będzie hostować wdrożenie. Użyj następującego polecenia, aby utworzyć grupę zasobów i sieć wirtualną. Jeśli masz już sieć wirtualną, możesz przejść do następnej sekcji.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Dodaj podsieć o *nazwie RouteServerSubnet,* aby wdrożyć serwer usługi Azure Route Server. Ta podsieć jest dedykowaną podsiecią tylko dla serwera usługi Azure Route Server. Podsieć RouteServerSubnet musi mieć wartość /27 lub krótszy prefiks (np. /26, /25). W przypadku dodania serwera usługi Azure Route Server zostanie wyświetlony komunikat o błędzie.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Uzyskaj identyfikator routeServerSubnet. Aby wyświetlić identyfikator zasobu wszystkich podsieci w sieci wirtualnej, użyj tego polecenia:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

Identyfikator podsieci RouteServerSubnet wygląda następująco:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Tworzenie serwera tras

Utwórz serwer tras za pomocą tego polecenia:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

Lokalizacja musi być dopasowana do lokalizacji sieci wirtualnej. Podsieć HostedSubnet to identyfikator RouteServerSubnet uzyskany w poprzedniej sekcji.

## <a name="create-peering-with-an-nva"></a>Tworzenie komunikacji równorzędnej z urządzeniem WUS

Użyj następującego polecenia, aby nawiązać połączenie równorzędne BGP z serwera tras do urządzenia WUS:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" to adres IP sieci wirtualnej przypisany do urządzenia WUS. "nva_asn" to numer systemu autonomicznego (ASN) skonfigurowany w węzłem WUS. Numer ASN może być dowolną liczbą 16-bitową inną niż ta z zakresu 65515–65520. Ten zakres usług ASN jest zarezerwowany przez firmę Microsoft.

Aby skonfigurować w celu zapewnienia nadmiarowości komunikacji równorzędnej z innym urządzeniem WUS lub innym wystąpieniem tego samego urządzenia WUS, użyj tego polecenia:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Dokończ konfigurację urządzenia WUS

Aby ukończyć konfigurację urządzenia WUS i włączyć sesje protokołu BGP, potrzebny jest adres IP i nazwa ASN serwera usługi Azure Route Server. Te informacje można uzyskać za pomocą tego polecenia:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Dane wyjściowe zawierają następujące informacje:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurowanie wymiany tras

Jeśli masz bramę usługi ExpressRoute i bramę sieci VPN platformy Azure w tej samej sieci wirtualnej i chcesz, aby wymieniały się trasami, możesz włączyć wymianę tras na serwerze usługi Azure Route Server.

1. Aby włączyć wymianę tras między serwerem usługi Azure Route Server i bramami, użyj tego polecenia:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Aby wyłączyć wymianę tras między serwerem usługi Azure Route Server i bramami, użyj tego polecenia:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Trasy anonsowane i odbierane przez serwer usługi Azure Route Server można wyświetlić za pomocą tego polecenia:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli serwer usługi Azure Route Server nie jest już potrzebny, użyj tych poleceń, aby usunąć komunikacji równorzędnej BGP, a następnie usunąć serwer tras. 

1. Usuń komunikacja równorzędna BGP między serwerem usługi Azure Route i urządzeniem WUS za pomocą tego polecenia:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Usuń serwer usługi Azure Route Server za pomocą tego polecenia:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu serwera usługi Azure Route Server dowiedz się, jak serwer usługi Azure Route Server współdziała z usługą ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute i sieci VPN platformy Azure](expressroute-vpn-support.md)
