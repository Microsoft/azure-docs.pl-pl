---
title: Konfigurowanie zakończenia protokołu TLS za pomocą certyfikatów magazynu kluczy — Program PowerShell
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak zintegrować usługę Azure Application Gateway z usługą Key Vault dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/27/2020
ms.author: victorh
ms.openlocfilehash: ffda4b41497a9fd84db5fcee36202eb1c1dca2c0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457845"
---
# <a name="configure-tls-termination-with-key-vault-certificates-by-using-azure-powershell"></a>Konfigurowanie zakończenia protokołu TLS przy użyciu certyfikatów usługi Key Vault przy użyciu programu Azure PowerShell

[Usługa Azure Key Vault](../key-vault/general/overview.md) to tajny magazyn zarządzany przez platformę, którego można używać do ochrony wpisów tajnych, kluczy i certyfikatów TLS/SSL. Usługa Azure Application Gateway obsługuje integrację z usługą Key Vault dla certyfikatów serwera dołączonych do odbiorników obsługujących protokół HTTPS. Ta obsługa jest ograniczona do jednostki SKU bramy aplikacji w wersji 2.

Aby uzyskać więcej informacji, zobacz [Zakończenie protokołu TLS z certyfikatami Usługi Key Vault](key-vault-certs.md).

W tym artykule pokazano, jak używać skryptu programu Azure PowerShell do integracji magazynu kluczy z bramą aplikacji dla certyfikatów zakończenia protokołu TLS/SSL.

Ten artykuł wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchomić polecenia w tym artykule, należy również utworzyć `Connect-AzAccount`połączenie z platformą Azure, uruchamiając program .

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zainstalować moduł ManagedServiceIdentity:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Przykładowy skrypt

### <a name="set-up-variables"></a>Konfigurowanie zmiennych

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"
```

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Tworzenie grupy zasobów i tożsamości zarządzanej przez użytkownika

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Tworzenie magazynu kluczy, zasad i certyfikatów, które mają być używane przez bramę aplikacji

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> Flaga -EnableSoftDelete musi być używana do poprawnego działania zakończenia protokołu TLS. Jeśli konfigurujesz [usuwanie programowe usługi Key Vault za pośrednictwem portalu,](../key-vault/general/overview-soft-delete.md#soft-delete-behavior)okres przechowywania musi być utrzymywany na poziomie 90 dni, czyli wartością domyślną. Brama aplikacji nie obsługuje jeszcze innego okresu przechowywania. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Tworzenie statycznego publicznego wirtualnego adresu IP (VIP)

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Tworzenie portów puli i front-end

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-tlsssl-certificate-to-your-key-vault"></a>Wskaż certyfikat TLS/SSL do magazynu kluczy

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Tworzenie odbiorników, reguł i skalowania automatycznego

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Przypisywanie tożsamości zarządzanej przez użytkownika do bramy aplikacji

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o zakończeniu protokołu TLS](ssl-overview.md)
