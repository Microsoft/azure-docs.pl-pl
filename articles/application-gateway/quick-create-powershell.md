---
title: 'Szybki start: bezpośredni ruch internetowy przy użyciu programu PowerShell'
titleSuffix: Azure Application Gateway
description: W tym przewodniku Szybki start dowiesz się, jak za pomocą usługi Azure PowerShell utworzyć sieć Azure Application Gateway, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: a4aa16ba4334e5f1e035face9549f4c03ad3a14f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538551"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Szybki start: bezpośredni ruch internetowy w Azure Application Gateway użyciu Azure PowerShell

W tym przewodniku Szybki start użyjemy Azure PowerShell do utworzenia bramy aplikacji. Następnie przetestuj go, aby upewnić się, że działa prawidłowo. 

Brama aplikacji kieruje ruch internetowy aplikacji do określonych zasobów w puli zaplecza. Odbiorniki przypisuje się do portów, tworzy się reguły i dodaje zasoby do puli zaplecza. Dla uproszczenia w tym artykule użyto prostej konfiguracji z publicznym adresem IP frontonia, podstawowym odbiornikiem do hostowania pojedynczej witryny w bramie aplikacji, podstawową regułą routingu żądań i dwiema maszynami wirtualnymi w puli zaplecza.

Możesz również ukończyć ten przewodnik Szybki start przy użyciu interfejsu wiersza polecenia platformy [Azure](quick-create-cli.md) [lub Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell wersję 1.0.0 lub nowszą](/powershell/azure/install-az-ps) (jeśli uruchamiasz Azure PowerShell lokalnie).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby nawiązać połączenie z platformą Azure, `Connect-AzAccount` uruchom .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć nową grupę zasobów, użyj `New-AzResourceGroup` polecenia cmdlet : 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Możesz utworzyć nową podsieć dla Application Gateway lub użyć istniejącej. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza. Możesz skonfigurować adres IP frontendu usługi Application Gateway jako publiczny lub prywatny zgodnie z twoim przypadekem użycia. W tym przykładzie wybierzesz publiczny adres IP frontonia.

1. Utwórz konfiguracje podsieci przy `New-AzVirtualNetworkSubnetConfig` użyciu .
2. Utwórz sieć wirtualną z konfiguracjami podsieci przy użyciu programu `New-AzVirtualNetwork` . 
3. Utwórz publiczny adres IP przy `New-AzPublicIpAddress` użyciu . 
> [!NOTE]
> [Zasady punktu końcowego usługi dla sieci wirtualnej](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nie są obecnie obsługiwane Application Gateway podsieci.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

1. Użyj `New-AzApplicationGatewayIPConfiguration` programu , aby utworzyć konfigurację, która kojarzy utworzoną podsieć z bramą aplikacji. 
2. Użyj `New-AzApplicationGatewayFrontendIPConfig` programu , aby utworzyć konfigurację, która przypisuje publiczny adres IP utworzony wcześniej dla bramy aplikacji. 
3. Użyj `New-AzApplicationGatewayFrontendPort` , aby przypisać port 80 w celu uzyskania dostępu do bramy aplikacji.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Tworzenie puli zaplecza

1. Użyj `New-AzApplicationGatewayBackendAddressPool` , aby utworzyć pulę zaplecza dla bramy aplikacji. Pula zaplecza jest na razie pusta. Podczas tworzenia karty sieciowej serwera zaplecza w następnej sekcji dodasz je do puli zaplecza.
2. Skonfiguruj ustawienia dla puli zaplecza za pomocą `New-AzApplicationGatewayBackendHttpSetting` .

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Tworzenie odbiornika i dodawanie reguły

Platforma Azure wymaga odbiornika, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. Platforma Azure wymaga również reguły, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. 

1. Utwórz odbiornik przy użyciu wcześniej utworzonej konfiguracji i `New-AzApplicationGatewayHttpListener` portu frontendu. 
2. Użyj `New-AzApplicationGatewayRequestRoutingRule` , aby utworzyć regułę o nazwie *rule1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz po utworzeniu niezbędnych zasobów pomocniczych utwórz bramę aplikacji:

1. Użyj `New-AzApplicationGatewaySku` polecenia , aby określić parametry bramy aplikacji.
2. Użyj `New-AzApplicationGateway` , aby utworzyć bramę aplikacji.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Serwery zaplecza

Teraz, po utworzeniu Application Gateway, utwórz maszyny wirtualne zaplecza, które będą hostować witryny sieci Web. Zaplecza mogą składać się z karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczny adres IP, wewnętrzny adres IP, w pełni kwalifikowane nazwy domen (FQDN) i wielodostępne zaplecza, takie jak Azure App Service. 

W tym przykładzie utworzysz dwie maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstalujesz również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

1. Pobierz ostatnio utworzoną konfigurację Application Gateway zaplecza za `Get-AzApplicationGatewayBackendAddressPool` pomocą .
2. Utwórz interfejs sieciowy za `New-AzNetworkInterface` pomocą .
3. Utwórz konfigurację maszyny wirtualnej za pomocą programu `New-AzVMConfig` .
4. Utwórz maszynę wirtualną za `New-AzVM` pomocą .

Po uruchomieniu następującego przykładowego kodu w celu utworzenia maszyn wirtualnych na platformie Azure zostanie wyświetlony monit o podanie poświadczeń. Wprowadź nazwę użytkownika i hasło:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Chociaż usługi IIS nie są wymagane do utworzenia bramy aplikacji, zainstalowano je w tym przewodniku Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

Użyj usług do przetestowania bramy aplikacji:

1. Uruchom `Get-AzPublicIPAddress` , aby uzyskać publiczny adres IP bramy aplikacji. 
2. Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki. Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa maszyny wirtualnej. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zaplecza.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie bramy aplikacji](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie bramy aplikacji i wszystkich powiązanych z nią zasobów. 

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą programu Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)
