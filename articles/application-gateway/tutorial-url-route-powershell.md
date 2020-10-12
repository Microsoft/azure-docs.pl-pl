---
title: Kierowanie ruchu internetowego na podstawie adresu URL — Azure PowerShell
description: Dowiedz się, jak kierować ruch internetowy do określonych skalowalnych pul serwerów na podstawie adresu URL, korzystając z programu Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 37e76f54b9c4fe38c891f7cee7bc443d1b0b20f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596077"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Kierowanie ruchu internetowego na podstawie adresu URL za pomocą programu Azure PowerShell

Korzystając z programu Azure PowerShell, możesz skonfigurować kierowanie ruchu internetowego do określonych skalowalnych pul serwerów na podstawie adresu URL użytego do uzyskania dostępu do aplikacji. W tym artykule opisano tworzenie [Application Gateway platformy Azure](application-gateway-introduction.md) z trzema pulami zaplecza przy użyciu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Każda z pul zaplecza ma określony cel, na przykład obsługę typowych danych, obrazów i wideo.  Kierowanie ruchu do oddzielnych pul gwarantuje, że klienci otrzymają potrzebne informacje we właściwym czasie.

Aby włączyć kierowanie ruchu, należy utworzyć [reguły routingu](application-gateway-url-route-overview.md) przypisane do odbiorników nasłuchujących na określonych portach w celu zapewnienia, że ruch internetowy będzie kierowany do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Konfigurowanie sieci
* Tworzenie odbiorników, mapy ścieżek URL i reguł
* Tworzenie skalowalnych pul zaplecza

![Przykład routingu adresów URL](./media/tutorial-url-route-powershell/scenario.png)

Jeśli wolisz, możesz wykonać tę procedurę przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-url-route-cli.md) lub [Azure Portal](create-url-route-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

W związku z upływem czasu niezbędnego do utworzenia zasobów można wykonać tę procedurę, poświęć do 90 minut.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów zawierającą wszystkie zasoby aplikacji. 

Utwórz grupę zasobów platformy Azure przy użyciu polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Niezależnie od tego, czy masz istniejącą sieć wirtualną, czy tworzysz nową, musisz upewnić się, że zawiera ona podsieć używaną wyłącznie na potrzeby bram aplikacji. W tym artykule utworzysz podsieć dla bramy aplikacji i podsieć dla zestawów skalowania. Utworzysz publiczny adres IP, aby umożliwić dostęp do zasobów przy użyciu bramy aplikacji.

Utwórz konfiguracje podsieci *myAGSubnet* i *myBackendSubnet* przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) z konfiguracjami podsieci. Na koniec Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

W tej części utworzysz zasoby, które obsługują bramę aplikacji, a na koniec utworzysz bramę aplikacji. Tworzone zasoby obejmują:

- *Konfiguracje IP i port frontonu* — kojarzy utworzoną wcześniej sieć z bramą aplikacji i przypisuje port do użycia w celu uzyskania do niego dostępu.
- *Domyślna pula* — wszystkie bramy aplikacji muszą mieć co najmniej jedną pulę serwerów zaplecza.
- *Odbiornik domyślny i reguła domyślna* — odbiornik domyślny nasłuchuje ruchu na porcie, który został przypisany, a reguła domyślna wysyła ruch do puli domyślnej.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Skojarz *myAGSubnet* utworzone wcześniej z bramą aplikacji przy użyciu polecenia [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Przypisz *myAGPublicIPAddress* do bramy aplikacji przy użyciu polecenia [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Tworzenie puli domyślnej i ustawień

Utwórz domyślną pulę zaplecza o nazwie *appGatewayBackendPool* dla bramy aplikacji za pomocą polecenia [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia puli zaplecza przy użyciu polecenia [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślnego i reguły domyślnej

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym artykule opisano tworzenie dwóch detektorów. Pierwszy, podstawowy odbiornik, który utworzysz, nasłuchuje ruchu pod głównym adresem URL. Drugi odbiornik, który utworzysz, nasłuchuje ruchu pod innymi określonymi adresami URL.

Utwórz domyślny odbiornik o nazwie *myDefaultListener* przy użyciu polecenia [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) z konfiguracją frontonu i portem frontonu, który został wcześniej utworzony. 

Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Utwórz podstawową regułę o nazwie *RULE1* przy użyciu polecenia [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, gdy zostały utworzone niezbędne zasoby pomocnicze, określ parametry bramy aplikacji o nazwie *myAppGateway* przy użyciu polecenia [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), a następnie utwórz je za pomocą polecenia [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

Utworzenie bramy aplikacji może potrwać do 30 minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji. 

W tym momencie masz bramę aplikacji, która nasłuchuje ruchu na porcie 80 i wysyła ten ruch do domyślnej puli serwerów.

### <a name="add-image-and-video-backend-pools-and-port"></a>Dodawanie pul zaplecza i portu na potrzeby obsługi obrazów i wideo

Dodaj pule zaplecza o nazwie *imagesBackendPool* i *videoBackendPool* do bramy Application Gateway[Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Dodaj port frontonu dla pul przy użyciu polecenia [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Prześlij zmiany do bramy aplikacji przy użyciu polecenia [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Aktualizowanie bramy aplikacji również może potrwać do 20 minut.

### <a name="add-backend-listener"></a>Dodawanie odbiornika zaplecza

Dodaj odbiornik zaplecza o nazwie *backendListener* , który jest wymagany do kierowania ruchem za pomocą polecenia [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Dodawanie mapy ścieżek URL

Mapy ścieżek URL zapewniają kierowanie adresów URL wysyłanych do aplikacji do określonych pul zaplecza. Utwórz mapy ścieżek adresów URL o nazwach *imagePathRule* i *videoPathRule* przy użyciu polecenia [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) i [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Dodawanie reguły routingu

Reguła routingu kojarzy mapę adresów URL z utworzonym odbiornikiem. Dodaj regułę o nazwie *ograniczeniem zakresu wystąpień* przy użyciu polecenia [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech utworzonych pul zaplecza. Utworzone zestawy skalowania będą miały nazwy *myvmss1*, *myvmss2* i *myvmss3*. Zestaw skalowania przypisuje się do puli zaplecza podczas konfigurowania ustawień adresu IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalowanie usług IIS

Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się usługi IIS.  Zostanie utworzona Przykładowa strona do testowania, jeśli Brama aplikacji działa.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Użyj [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) , aby uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak, `http://52.168.55.24` , `http://52.168.55.24:8080/images/test.htm` lub `http://52.168.55.24:8080/video/test.htm` .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Zmień adres URL na http:// &lt; IP-Address &gt; : 8080/images/test.htm, zastępując adres IP adresem &lt; IP &gt; i powinien wyglądać podobnie do poniższego przykładu:

![Testowanie adresu URL obrazów w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Zmień adres URL na http:// &lt; IP-Address &gt; : 8080/video/test.htm, zastępując adres IP adresem &lt; IP &gt; i powinien wyglądać podobnie do poniższego przykładu:

![Testowanie adresu URL wideo w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, Brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

[Przekierowywanie ruchu internetowego na podstawie adresu URL](./tutorial-url-redirect-powershell.md)
