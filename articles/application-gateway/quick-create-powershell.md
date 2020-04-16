---
title: 'Szybki start: bezpośredni ruch internetowy za pomocą programu PowerShell'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak użyć programu Azure PowerShell do utworzenia bramy aplikacji platformy Azure, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406210"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Szybki start: bezpośredni ruch internetowy z bramą aplikacji platformy Azure przy użyciu programu Azure PowerShell

W tym przewodniku Szybki start można użyć programu Azure PowerShell do utworzenia bramy aplikacji. Następnie przetestować go, aby upewnić się, że działa poprawnie. 

Brama aplikacji kieruje ruch sieci web aplikacji do określonych zasobów w puli wewnętrznej bazy danych. Przypisywanie odbiorników do portów, tworzenie reguł i dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule używa prostej konfiguracji z publicznym adresem IP front-end, odbiornika podstawowego do hostowania pojedynczej lokacji w bramie aplikacji, podstawowej reguły routingu żądań i dwóch maszyn wirtualnych w puli wewnętrznej bazy danych.

Ten przewodnik Szybki start można również ukończyć przy użyciu [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) lub portalu [Azure.](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Program Azure PowerShell w wersji 1.0.0 lub nowszej](/powershell/azure/install-az-ps) (jeśli uruchomisz program Azure PowerShell lokalnie).

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Aby połączyć się `Connect-AzAccount`z platformą Azure, uruchom program .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Można użyć istniejącej grupy zasobów lub utworzyć nową.

Aby utworzyć nową grupę `New-AzResourceGroup` zasobów, użyj polecenia cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Można utworzyć nową podsieć bramy aplikacji lub użyć istniejącej. W tym przykładzie utworzysz dwie podsieci w tym przykładzie: jedną dla bramy aplikacji, a drugą dla serwerów wewnętrznej bazy danych. Można skonfigurować adres IP frontu bramy aplikacji jako publiczny lub prywatny, zgodnie z przypadkiem użycia. W tym przykładzie wybierzesz adres IP frontu publicznego.

1. Tworzenie konfiguracji podsieci za pomocą programu `New-AzVirtualNetworkSubnetConfig`.
2. Utwórz sieć wirtualną z konfiguracjami podsieci za pomocą programu `New-AzVirtualNetwork`. 
3. Utwórz publiczny adres `New-AzPublicIpAddress`IP przy użyciu programu . 

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

1. Służy `New-AzApplicationGatewayIPConfiguration` do tworzenia konfiguracji, która kojarzy podsieć utworzoną z bramą aplikacji. 
2. Służy `New-AzApplicationGatewayFrontendIPConfig` do tworzenia konfiguracji, która przypisuje publiczny adres IP, który został wcześniej utworzony do bramy aplikacji. 
3. Służy `New-AzApplicationGatewayFrontendPort` do przypisywania portu 80 w celu uzyskania dostępu do bramy aplikacji.

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

1. Służy `New-AzApplicationGatewayBackendAddressPool` do tworzenia puli wewnętrznej bazy danych dla bramy aplikacji. Pula zaplecza będzie na razie pusta. Podczas tworzenia kart sieciowych serwera wewnętrznej bazy danych w następnej sekcji, należy dodać je do puli wewnętrznej bazy danych.
2. Skonfiguruj ustawienia puli `New-AzApplicationGatewayBackendHttpSetting`wewnętrznej bazy danych za pomocą programu .

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

1. Utwórz odbiornik `New-AzApplicationGatewayHttpListener` przy użyciu konfiguracji frontendu i portu frontendu, który został wcześniej utworzony. 
2. Służy `New-AzApplicationGatewayRequestRoutingRule` do tworzenia reguły o nazwie *rule1*. 

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

1. Służy `New-AzApplicationGatewaySku` do określania parametrów bramy aplikacji.
2. Służy `New-AzApplicationGateway` do tworzenia bramy aplikacji.

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

Teraz, gdy utworzono bramę aplikacji, należy utworzyć maszyny wirtualne wewnętrznej bazy danych, które będą hostować witryny sieci Web. Zaplecze może składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service. W tym przykładzie utworzysz dwie maszyny wirtualne platformy Azure, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstalujesz również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

1. Pobierz ostatnio utworzoną konfigurację `Get-AzApplicationGatewayBackendAddressPool`puli wewnętrznej bazy danych bramy aplikacji za pomocą programu .
2. Utwórz interfejs `New-AzNetworkInterface`sieciowy za pomocą programu .
3. Utwórz konfigurację `New-AzVMConfig`maszyny wirtualnej za pomocą programu .
4. Utwórz maszynę `New-AzVM`wirtualną za pomocą programu .

Po uruchomieniu następującego przykładowego kodu w celu utworzenia maszyn wirtualnych na platformie Azure zostanie wyświetlony monit o podanie poświadczeń. Wprowadź *użytkownika azureuser* dla nazwy użytkownika i hasła:
    
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

Mimo że zainstalowanie usług IIS nie jest wymagane do utworzenia bramy aplikacji, zainstalowano je w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Uruchom, `Get-AzPublicIPAddress` aby uzyskać publiczny adres IP bramy aplikacji. 
2. Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki. Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa maszyny wirtualnej. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie połączyć się z zapleczem.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie bramy aplikacji](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne zasoby utworzone za pomocą bramy aplikacji, usuń grupę zasobów. Po usunięciu grupy zasobów można również usunąć bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów, wywołanie polecenia `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą programu Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

