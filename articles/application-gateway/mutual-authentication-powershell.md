---
title: Konfigurowanie uwierzytelniania wzajemnego na platformie Azure Application Gateway za pomocą programu PowerShell
description: Informacje dotyczące konfigurowania Application Gateway w celu uwierzytelniania wzajemnego za poorednictwem programu PowerShell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231512"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Konfigurowanie uwierzytelniania wzajemnego za pomocą Application Gateway za pomocą programu PowerShell (wersja zapoznawcza)
W tym artykule opisano sposób korzystania z programu PowerShell w celu skonfigurowania uwierzytelniania wzajemnego na Application Gateway. Uwierzytelnianie wzajemne polega na tym, Application Gateway uwierzytelnia klienta wysyłającego żądanie przy użyciu przekazanego certyfikatu klienta do Application Gateway. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten artykuł wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby można było skonfigurować uwierzytelnianie wzajemne przy użyciu Application Gateway, do bramy należy przekazać certyfikat klienta. Certyfikat klienta będzie używany do weryfikacji certyfikatu, który zostanie Application Gateway przez klienta. Do celów testowych można użyć certyfikatu z podpisem własnym. Nie jest to jednak zalecane w przypadku obciążeń produkcyjnych, ponieważ są one trudniejsze do zarządzania i nie są całkowicie bezpieczne.

Aby dowiedzieć się więcej, szczególnie o rodzaju certyfikatów klienta, które można przekazać, zobacz [Omówienie uwierzytelniania wzajemnego za pomocą Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Najpierw utwórz nową grupę zasobów w ramach subskrypcji. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Wdróż sieć wirtualną do wdrożenia w ramach Application Gateway.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Tworzenie publicznego adresu IP

Utwórz publiczny adres IP do użycia z Twoim Application Gateway. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Tworzenie konfiguracji Application Gateway IP

Utwórz konfiguracje IP i port frontonu. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Konfigurowanie protokołu SSL frontonu 

Skonfiguruj certyfikaty SSL dla Application Gateway.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Konfigurowanie uwierzytelniania klienta 

Skonfiguruj uwierzytelnianie klienta na Application Gateway. Aby uzyskać więcej informacji na temat wyodrębniania zaufanych łańcuchów certyfikatów klienta urzędu certyfikacji do użycia w tym miejscu, zobacz [Jak wyodrębnić łańcuch certyfikatów zaufanego klienta urzędu certyfikacji](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Upewnij się, że cały łańcuch certyfikatów klienta urzędu certyfikacji został przekazany w jednym pliku, i tylko jeden łańcuch na plik.  

> [!NOTE]
> Zalecamy korzystanie z protokołu TLS 1,2 z uwierzytelnianiem obustronnym, ponieważ protokół TLS 1,2 będzie w przyszłości uznawany za. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Konfigurowanie puli i ustawień zaplecza

Skonfiguruj pulę zaplecza i ustawienia dla Application Gateway. Opcjonalnie Skonfiguruj zaufany certyfikat główny zaplecza na potrzeby kompleksowego szyfrowania SSL.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Konfigurowanie reguły

Skonfiguruj regułę na Application Gateway.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Skonfiguruj domyślne zasady protokołu SSL dla przyszłych odbiorników

Podczas konfigurowania wzajemnego uwierzytelniania skonfigurowano zasady protokołu SSL dotyczące odbiornika. W tym kroku można opcjonalnie ustawić domyślne zasady protokołu SSL dla tworzonych przez Ciebie przyszłych odbiorników. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Tworzenie Application Gateway

Korzystając z wszystkiego utworzonego powyżej, wdróż Application Gateway.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, Brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Odnów wygasłe certyfikaty urzędu certyfikacji klienta

W przypadku wygaśnięcia certyfikatu urzędu certyfikacji klienta możesz zaktualizować certyfikat na bramie, wykonując następujące czynności: 

1. Logowanie do platformy Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Pobierz konfigurację Application Gateway
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Usuń zaufany certyfikat klienta z bramy 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Dodaj nowy certyfikat do bramy 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Aktualizowanie bramy przy użyciu nowego certyfikatu 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Następne kroki

- [zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą interfejsu wiersza polecenia platformy Azure](./tutorial-manage-web-traffic-cli.md)