---
title: 'Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu Azure PowerShell'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i skonfigurować serwer tras przy użyciu Azure PowerShell.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: a3ab3a801872cc20b4e41bbff02ad6474c3bab8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655210"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Szybki Start: Tworzenie i Konfigurowanie serwera tras przy użyciu Azure PowerShell

Ten artykuł ułatwia skonfigurowanie usługi Azure Route Server do komunikacji równorzędnej z sieciowym urządzeniem wirtualnym (urządzenie WUS) w sieci wirtualnej przy użyciu programu PowerShell. Usługa Azure Route Server będzie uczyć się tras z urządzenie WUS i programować je na maszynach wirtualnych w sieci wirtualnej. Serwer usługi Azure Route również anonsuje trasy sieci wirtualnej do urządzenie WUS. Aby uzyskać więcej informacji, przeczytaj temat [serwer usługi Azure Route](overview.md).

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Upewnij się, że masz najnowsze moduły programu PowerShell, lub możesz użyć Azure Cloud Shell w portalu.
* Przejrzyj [limity usługi dla serwera usługi Azure Route](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Tworzenie serwera tras

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz subskrypcję.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej

Aby można było utworzyć serwer tras platformy Azure, musisz mieć sieć wirtualną do hostowania wdrożenia. Użyj poniższego polecenia, aby utworzyć grupę zasobów i sieć wirtualną. Jeśli masz już sieć wirtualną, możesz przejść do następnej sekcji.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Dodaj podsieć o nazwie *RouteServerSubnet* , aby wdrożyć serwer tras platformy Azure w programie. Ta podsieć jest dedykowaną podsiecią tylko dla serwera tras platformy Azure. RouteServerSubnet musi mieć wartość/27 lub krótszy prefiks (na przykład/26,/25) lub podczas dodawania serwera usługi Azure Route zostanie wyświetlony komunikat o błędzie.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Uzyskaj identyfikator RouteServerSubnet. Aby wyświetlić identyfikator zasobu wszystkich podsieci w sieci wirtualnej, użyj tego polecenia:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

Identyfikator RouteServerSubnet wygląda następująco:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Tworzenie serwera tras

Utwórz serwer tras za pomocą tego polecenia:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

Lokalizacja musi być zgodna z lokalizacją sieci wirtualnej. HostedSubnet jest IDENTYFIKATORem RouteServerSubnet uzyskanym w poprzedniej sekcji.

## <a name="create-peering-with-an-nva"></a>Tworzenie komunikacji równorzędnej za pomocą urządzenie WUS

Użyj następującego polecenia, aby ustanowić komunikację równorzędną BGP z serwera tras do urządzenie WUS:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" to adres IP sieci wirtualnej przypisany do urządzenie WUS. "nva_asn" jest numerem systemu autonomicznego (ASN) skonfigurowanym w urządzenie WUS. Numer ASN może być dowolną liczbą 16-bitową inną niż te w zakresie 65515-65520. Ten zakres numerów ASN jest zarezerwowany przez firmę Microsoft.

Aby skonfigurować komunikację równorzędną z różnymi urządzenie WUS lub innym wystąpieniem tego samego urządzenie WUS w celu zapewnienia nadmiarowości, użyj tego polecenia:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Ukończ konfigurację na urządzenie WUS

Aby ukończyć konfigurację urządzenie WUS i włączyć sesje protokołu BGP, wymagany jest adres IP i ASN serwera usługi Azure Route. Te informacje można uzyskać za pomocą tego polecenia:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

Dane wyjściowe zawierają następujące informacje:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Konfigurowanie wymiany tras

Jeśli masz bramę ExpressRoute i bramę sieci VPN platformy Azure w tej samej sieci wirtualnej i chcesz, aby były one dostępne dla tras wymiany, możesz włączyć wymianę trasy na serwerze tras platformy Azure.

1. Aby włączyć wymianę tras między serwerem tras platformy Azure i bramami, użyj tego polecenia:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Aby wyłączyć wymianę tras między serwerem tras platformy Azure i bramami, użyj tego polecenia:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Za pomocą tego polecenia można wyświetlić trasy anonsowane i odebrane przez serwer usługi Azure Route:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli serwer tras platformy Azure nie jest już potrzebny, Użyj tych poleceń, aby usunąć komunikację równorzędną BGP, a następnie usunąć serwer tras. 

1. Usuń komunikację równorzędną BGP między serwerem tras platformy Azure i urządzenie WUS za pomocą tego polecenia:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Usuń serwer tras platformy Azure za pomocą tego polecenia:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu serwera usługi Azure Route należy dalej dowiedzieć się, jak usługa Azure Route Server współdziała z ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute i obsługa sieci VPN platformy Azure](expressroute-vpn-support.md)
