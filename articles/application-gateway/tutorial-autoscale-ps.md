---
title: 'Samouczek: ulepszanie dostępu do aplikacji sieci Web — Application Gateway platformy Azure'
description: W tym samouczku dowiesz się, jak utworzyć automatycznie skalowaną, strefowo nadmiarową bramę aplikacji z zastrzeżonym adresem IP przy użyciu programu Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e07fc34c7177e3a1dace34ab298b64dc3aa6a06a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74011361"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Samouczek: Tworzenie bramy aplikacji, która usprawnia dostęp do aplikacji sieci Web

Jeśli jesteś administratorem IT i chcesz poprawić dostęp do aplikacji internetowych, możesz zoptymalizować bramę aplikacji, aby była skalowana na podstawie potrzeb klientów i obejmowała wiele stref dostępności. Ten samouczek pomoże Ci skonfigurować funkcje usługi Azure App Gateway pod kątem skalowania automatycznego, nadmiarowości stref i zastrzeżonych wirtualnych adresów IP (statycznych adresów IP). Aby rozwiązać problem, użyjesz poleceń cmdlet programu Azure PowerShell i modelu wdrażania usługi Azure Resource Manager.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie sieci wirtualnej skalowania automatycznego
> * Tworzenie zastrzeżonego publicznego adresu IP
> * Konfigurowanie infrastruktury bramy aplikacji
> * Określanie skalowania automatycznego
> * Tworzenie bramy aplikacji
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga, aby program Azure PowerShell działał lokalnie. Musisz mieć zainstalowany moduł Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów w jednej z dostępnych lokalizacji.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

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

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną z jedną dedykowaną podsiecią dla bramy aplikacji ze skalowaniem automatycznym. Aktualnie w każdej dedykowanej podsieci można wdrożyć tylko jedną bramę aplikacji ze skalowaniem automatycznym.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Tworzenie zastrzeżonego publicznego adresu IP

Określ metodę alokacji publicznego adresu IP (PublicIPAddress) jako **statyczną**. Adres VIP bramy aplikacji ze skalowaniem automatycznym można być tylko statyczny. Dynamiczne adresy IP nie są obsługiwane. Obsługiwana jest tylko standardowa jednostka SKU PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Pobieranie szczegółów

Pobierz szczegóły grupy zasobów, podsieci i adresu IP lokalnego obiektu w celu utworzenia szczegółów konfiguracji adresu IP dla bramy aplikacji.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurowanie infrastruktury

Ustaw konfigurację adresu IP, konfigurację adresu IP frontonu, pulę zaplecza, ustawienia protokołu HTTP, certyfikat, port, odbiornik i regułę w formacie identycznym, jaki obowiązuje w istniejącej usłudze Application Gateway w warstwie Standardowa. W nowej jednostce SKU obowiązuje taki sam model obiektów jak w jednostce SKU w warstwie Standardowa.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Określanie skalowania automatycznego

Teraz możesz określić konfigurację skalowania automatycznego dla bramy aplikacji. Obsługiwane są dwa typy konfiguracji skalowania automatycznego:

* **Tryb stałej pojemności**. W tym trybie brama aplikacji nie jest skalowana automatycznie i działa ze stałą pojemnością jednostki skalowania.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Tryb skalowania automatycznego**. W tym trybie brama aplikacji jest skalowana automatycznie w oparciu o wzorzec ruchu aplikacji.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji i dodaj strefy nadmiarowości oraz konfigurację skalowania automatycznego.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Użyj Get-AzPublicIPAddress, aby uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP (lub nazwę DNS), a następnie wklej go na pasku adresu przeglądarki.

`Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Najpierw zapoznaj się z zasobami utworzonymi przy użyciu bramy aplikacji. Następnie, gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, możesz je usunąć za pomocą polecenia `Remove-AzResourceGroup`.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-powershell.md)
