---
title: Przekierowywanie z protokołu HTTP do HTTPS przy użyciu programu PowerShell — Azure Application Gateway
description: Dowiedz się, jak utworzyć bramę aplikacji z przekierowywanym ruchem z protokołu HTTP do https przy użyciu Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: c9118d0e5b314f05e89334991c68ec1b3b5751e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866309"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Tworzenie bramy aplikacji z przekierowywaniem protokołu HTTP do HTTPS przy użyciu Azure PowerShell

Możesz użyć tej Azure PowerShell, aby utworzyć bramę aplikacji [z](overview.md) certyfikatem zakończenia protokołu TLS/SSL. Reguła routingu służy do przekierowywania ruchu HTTP do portu HTTPS w bramie aplikacji. W tym przykładzie utworzysz również zestaw [skalowania](../virtual-machine-scale-sets/overview.md) maszyn wirtualnych dla puli zaplecza bramy aplikacji, która zawiera dwa wystąpienia maszyn wirtualnych. 

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie certyfikatu z podpisem własnym
* Konfigurowanie sieci
* Tworzenie bramy aplikacji z certyfikatem
* Dodawanie odbiornika i reguły przekierowania
* Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchomić polecenia w tym samouczku, należy również uruchomić polecenie , aby `Login-AzAccount` utworzyć połączenie z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użytku produkcyjnego należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Wynik powinien wyglądać podobnie do następującego:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów platformy Azure *o nazwie myResourceGroupAG* przy [użyciu narzędzia New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz konfiguracje podsieci *myBackendSubnet* i *myAGSubnet* przy użyciu [narzędzia New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Utwórz sieć wirtualną o nazwie *myVNet* przy [użyciu narzędzia New-AzVirtualNetwork z](/powershell/module/az.network/new-azvirtualnetwork) konfiguracjami podsieci. Na koniec utwórz publiczny adres IP o nazwie *myAGPublicIPAddress przy* użyciu [new-AzPublicIpAddress.](/powershell/module/az.network/new-azpublicipaddress) Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```powershell
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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

*Skojarz wcześniej utworzoną podsieć myAGSubnet* z bramą aplikacji przy użyciu [narzędzia New-AzApplicationGatewayIPConfiguration.](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Przypisz *adres myAGPublicIPAddress* do bramy aplikacji przy użyciu [polecenie New-AzApplicationGatewayFrontendIPConfig.](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) Następnie możesz utworzyć port HTTPS przy użyciu [narzędzia New-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/new-azapplicationgatewayfrontendport)

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Tworzenie puli zaplecza i ustawień

Utwórz pulę zaplecza o *nazwie appGatewayBackendPool* dla bramy aplikacji przy użyciu [new-AzApplicationGatewayBackendAddressPool.](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) Skonfiguruj ustawienia puli zaplecza przy użyciu [pozycji New-AzApplicationGatewayBackendHttpSettings.](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślnego i reguły domyślnej

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym przykładzie utworzysz podstawowy odbiornik, który nasłuchuje ruchu HTTPS pod głównym adresem URL. 

Utwórz obiekt certyfikatu przy użyciu narzędzia [New-AzApplicationGatewaySslCertificate,](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) a następnie utwórz odbiornik o nazwie *appGatewayHttpListener* przy użyciu [pozycji New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) z wcześniej utworzonymi konfiguracjami frontona, portem frontonia i certyfikatem. Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Utwórz podstawową regułę o nazwie *rule1* przy [użyciu narzędzia New-AzApplicationGatewayRequestRoutingRule.](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Po utworzeniu niezbędnych zasobów obsługi określ parametry bramy aplikacji o nazwie *myAppGateway* przy użyciu [polecenia New-AzApplicationGatewaySku,](/powershell/module/az.network/new-azapplicationgatewaysku)a następnie utwórz ją przy użyciu [polecenia New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) z certyfikatem.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Dodawanie odbiornika i reguły przekierowania

### <a name="add-the-http-port"></a>Dodawanie portu HTTP

Dodaj port HTTP do bramy aplikacji przy użyciu [narzędzia Add-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/add-azapplicationgatewayfrontendport)

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>Dodawanie odbiornika HTTP

Dodaj odbiornik HTTP o nazwie *myListener* do bramy aplikacji przy użyciu [narzędzia Add-AzApplicationGatewayHttpListener.](/powershell/module/az.network/add-azapplicationgatewayhttplistener)

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Dodaj konfigurację przekierowania protokołu HTTP do https do bramy aplikacji przy użyciu narzędzia [Add-AzApplicationGatewayRedirectConfiguration.](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration)

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Dodawanie reguły rozsyłania

Dodaj regułę routingu z konfiguracją przekierowania do bramy aplikacji przy użyciu narzędzia [Add-AzApplicationGatewayRequestRoutingRule.](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule)

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji. Zestaw skalowania przypisuje się do puli zaplecza podczas konfigurowania ustawień adresu IP.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
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
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Instalowanie usług IIS

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, możesz użyć [get-AzPublicIPAddress.](/powershell/module/az.network/get-azpublicipaddress) Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Następne kroki

- [Ponowne przepisanie nagłówków HTTP i adresów URL przy użyciu Application Gateway](rewrite-http-headers-url.md)
