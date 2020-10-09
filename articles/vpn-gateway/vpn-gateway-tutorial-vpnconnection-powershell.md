---
title: 'Azure VPN Gateway: tworzenie połączeń sieci VPN S2S i zarządzanie nimi: samouczek'
description: Samouczek — tworzenie połączeń S2S sieci VPN i zarządzanie nimi za pomocą modułu Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 9b712c1b2e8ac33ec189542bf6f60929ac59858d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420561"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Samouczek: tworzenie połączeń sieci VPN S2S i zarządzanie nimi przy użyciu programu PowerShell

Połączenia S2S sieci VPN na platformie Azure zapewniają bezpieczną łączność między lokalizacjami klienta a platformą Azure. Ten samouczek przedstawia cykle życia połączeń IPsec S2S sieci VPN, takie jak tworzenie połączenia S2S sieci VPN i zarządzanie nim. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie połączenia S2S sieci VPN
> * Aktualizowanie właściwości połączenia: klucza wstępnego, protokołu BGP, zasad IPsec/IKE
> * Dodawanie większej liczby połączeń sieci VPN
> * Usuwanie połączenia sieci VPN

Na poniższym diagramie przedstawiono topologię wykorzystywaną w tym samouczku:

![Diagram połączenia sieci VPN typu lokacja-lokacja](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Praca z Azure Cloud Shell i Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>Wymagania

Wykonaj pierwszy samouczek: [Utwórz bramę sieci VPN z Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md) , aby utworzyć następujące zasoby:

1. Grupa zasobów (TestRG1), sieć wirtualna (VNet1) i podsieć GatewaySubnet
2. Brama sieci VPN (VNet1GW)

Poniżej przedstawiono wartości parametrów sieci wirtualnej. Zwróć uwagę na dodatkowe wartości bramy sieci lokalnej, które reprezentują Twoją sieć lokalną. Zmień poniższe wartości na podstawie konfiguracji środowiska i sieci, a następnie skopiuj je i wklej, aby ustawić zmienne na potrzeby tego samouczka. Jeśli upłynie limit czasu sesji usługi Cloud Shell lub konieczne jest użycie innego okna programu PowerShell, skopiuj i wklej zmienne do nowej sesji i kontynuuj pracę z tym samouczkiem.

>[!NOTE]
> Jeśli odbywa się to w celu nawiązania połączenia, pamiętaj, aby zmienić wartości na zgodne z Twoją siecią lokalną. Jeśli wykonujesz te kroki tylko w ramach samouczka, nie jest konieczne wprowadzanie zmian, ale połączenie nie będzie działać.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Przepływ pracy prowadzący do utworzenia połączenia S2S sieci VPN jest prosty:

1. Utwórz bramę sieci lokalnej do reprezentowania sieci lokalnej.
2. Utwórz połączenie między bramą sieci VPN platformy Azure i bramą sieci lokalnej.

## <a name="create-a-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Brama sieci lokalnej reprezentuje Twoją sieć lokalną. W bramie sieci lokalnej możesz określić właściwości Twojej sieci lokalnej, w tym:

* Publiczny adres IP urządzenia sieci VPN
* Przestrzeń adresów lokalnych
* (Opcjonalnie) Atrybuty protokołu BGP (adres IP elementu równorzędnego protokołu BGP oraz numer AS)

Utwórz bramę sieci lokalnej za pomocą polecenia [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway).

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location $Location1 -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Tworzenie połączenia S2S sieci VPN

Następnie utwórz połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej i urządzeniem sieci VPN za pomocą polecenia [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Należy pamiętać, że dla połączenia sieci VPN typu lokacja-lokacja wartość parametru „-ConnectionType” to *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

Dodaj opcjonalną właściwość „**-EnableBGP $True**”, aby włączyć protokół BGP dla połączenia, jeśli używasz protokołu BGP. To ustawienie jest domyślnie wyłączone. Parametr "-ConnectionProtocol" jest opcjonalny przy użyciu protokołu IKEv2 jako domyślnego. Połączenie z protokołami IKEv1 można utworzyć, określając **ConnectionProtocol IKEv1**.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aktualizowanie klucza wstępnego, protokołu BGP i zasad IPsec/IKE połączenia VPN

### <a name="view-and-update-your-pre-shared-key"></a>Wyświetlanie i aktualizowanie klucza wstępnego

Połączenie S2S sieci VPN na platformie Azure używa klucza wstępnego (klucza tajnego) do uwierzytelniania między lokalnym urządzeniem sieci VPN i bramą sieci VPN platformy Azure. Klucz wstępny połączenia można wyświetlić i zaktualizować za pomocą poleceń [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) i [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Klucz wstępny jest ciągiem **drukowalnych znaków ASCII**. Jego długość nie może przekraczać 128 znaków.

To polecenie wyświetla klucz wstępny połączenia:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Wynikiem tego przykładu będzie "**Azure \@ ! b2C3**". Użyj poniższego polecenia, aby zmienić wartość klucza wstępnego na "**Azure \@ ! _B2 = C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Włączanie protokołu BGP dla połączenia sieci VPN

Brama sieci VPN platformy Azure obsługuje protokół dynamicznego routingu BGP. Protokół BGP można włączyć dla poszczególnych połączeń w zależności od tego, czy używasz protokołu BGP w lokalnych sieciach i urządzeniach. Przed włączeniem protokołu BGP dla połączenia należy określić następujące właściwości protokołu BGP:

* Numer ASN (Autonomous System Number) sieci VPN platformy Azure
* Lokalny numer ASN bramy sieci lokalnej
* Lokalny adres IP elementu równorzędnego protokołu BGP bramy sieci lokalnej

Jeśli nie skonfigurowano właściwości protokołu BGP, następujące polecenia dodają te właściwości do bramy sieci VPN i bramy sieci lokalnej: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) i [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

Aby skonfigurować właściwości protokołu BGP, skorzystaj z następującego przykładu:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Włącz protokół BGP za pomocą polecenia [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Protokół BGP można wyłączyć, zmieniając wartość właściwości „-EnableBGP” na **$False**. Zapoznaj się z artykułem [BGP on Azure VPN gateways](vpn-gateway-bgp-resource-manager-ps.md) (Protokół BGP w bramach sieci VPN platformy Azure), aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące protokołu BGP w bramach sieci VPN platformy Azure.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Stosowanie niestandardowych zasad IPsec/IKE do połączenia

Aby określić dokładną kombinację algorytmów kryptograficznych i siłę kluczy IPsec/IKE połączenia, zamiast [domyślnych propozycji](vpn-gateway-about-vpn-devices.md#ipsec) można zastosować opcjonalne zasady IPsec/IKE. Poniższy przykładowy skrypt tworzy inne zasady IPsec/IKE z następującymi algorytmami i parametrami:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, okres istnienia skojarzeń zabezpieczeń 14 400 sekund, pojemność 102 400 000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Aby zapoznać się z pełną listą algorytmów i instrukcji, zobacz [IPsec/IKE policy for S2S or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Zasady IPsec/IKE dla połączeń S2S lub połączeń między sieciami wirtualnymi).

## <a name="add-another-s2s-vpn-connection"></a>Dodawanie następnego połączenia S2S sieci VPN

Dodaj dodatkowe połączenie S2S sieci VPN do tej samej bramy sieci VPN, utwórz inną bramę sieci lokalnej, a następnie utwórz nowe połączenie między nową bramą sieci lokalnej i bramą sieci VPN. Skorzystaj z następujących przykładów, pamiętając o konieczności zmodyfikowania zmiennych pod kątem własnej konfiguracji sieci.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Teraz istnieją dwa połączenia S2S sieci VPN do bramy sieci VPN platformy Azure.

![Połączenia sieci VPN z obsługą wielu lokacji](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Usuwanie połączenia S2S sieci VPN

Użyj polecenia [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection), aby usunąć połączenie S2S sieci VPN.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Usuń bramę sieci lokalnej, jeśli nie jest już potrzebna. Nie można usunąć bramy sieci lokalnej, jeśli skojarzono z nią inne połączenia.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli ta konfiguracja jest częścią wdrożenia prototypowego lub służącego do weryfikacji koncepcji, możesz usunąć grupę zasobów, bramę sieci VPN i wszystkie powiązane zasoby za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia połączeń S2S sieci VPN i zarządzania nimi. Opisano m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie połączenia S2S sieci VPN
> * Aktualizowanie właściwości połączenia: klucza wstępnego, protokołu BGP, zasad IPsec/IKE
> * Dodawanie większej liczby połączeń sieci VPN
> * Usuwanie połączenia sieci VPN

Przejdź do następujących samouczków, aby dowiedzieć się więcej na temat połączeń S2S, między sieciami wirtualnymi i P2S.

> [!div class="nextstepaction"]
> * [Tworzenie połączeń między sieciami wirtualnymi](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Tworzenie połączeń P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
