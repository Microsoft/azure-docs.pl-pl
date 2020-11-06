---
title: Tworzenie niestandardowej sondy przy użyciu programu PowerShell
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć niestandardową sondę dla Application Gateway przy użyciu programu PowerShell w programie Menedżer zasobów
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 63f4b28915a932398e5417f8ea9be3fe4d68e705
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397845"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Utwórz niestandardową sondę dla Application Gateway platformy Azure przy użyciu programu PowerShell dla Azure Resource Manager

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule opisano Dodawanie niestandardowej sondy do istniejącej bramy aplikacji przy użyciu programu PowerShell. Niestandardowe sondy są przydatne w przypadku aplikacji, które mają konkretną kontrolę kondycji lub dla aplikacji, które nie zapewniają prawidłowej odpowiedzi w domyślnej aplikacji sieci Web.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Tworzenie bramy aplikacji przy użyciu sondy niestandardowej

### <a name="sign-in-and-create-resource-group"></a>Zaloguj się i Utwórz grupę zasobów

1. Użyj `Connect-AzAccount` do uwierzytelniania.

   ```powershell
   Connect-AzAccount
   ```

1. Pobierz subskrypcje dla konta.

   ```powershell
   Get-AzSubscription
   ```

1. Wybierz subskrypcję platformy Azure do użycia.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Utwórz grupę zasobów. Możesz pominąć ten krok, jeśli masz istniejącą grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Ta lokalizacja będzie używana jako domyślna lokalizacja dla zasobów w danej grupie zasobów. Upewnij się, że wszystkie polecenia służące do tworzenia bramy aplikacji używają tej samej grupy zasobów.

W poprzednim przykładzie została utworzona grupa zasobów o nazwie **appgw-RG** w lokalizacji **zachodnie stany USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Poniższy przykład tworzy sieć wirtualną i podsieć dla bramy aplikacji. Brama aplikacji wymaga własnej podsieci do użycia. Z tego powodu podsieć utworzona dla bramy aplikacji powinna być mniejsza niż przestrzeń adresowa sieci wirtualnej, aby umożliwić tworzenie i używanie innych podsieci.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów **appgw-RG** dla regionu zachodnie stany USA. Ten przykład używa publicznego adresu IP dla adresu IP frontonu bramy aplikacji.  Usługa Application Gateway wymaga, aby publiczny adres IP miał dynamicznie utworzoną nazwę DNS, dlatego `-DomainNameLabel` nie można go określić podczas tworzenia publicznego adresu IP.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Przed utworzeniem bramy aplikacji należy skonfigurować wszystkie elementy konfiguracji. Poniższy przykład tworzy elementy konfiguracji, które są zbędne dla zasobu bramy aplikacji.

| **Składnik** | **Opis** |
|---|---|
| **Konfiguracja adresu IP bramy** | Konfiguracja adresu IP bramy aplikacji.|
| **Pula zaplecza** | Pula adresów IP, nazw FQDN lub kart sieciowych, które znajdują się na serwerach aplikacji, które obsługują aplikację sieci Web|
| **Sonda kondycji** | Niestandardowa sonda służąca do monitorowania kondycji elementów członkowskich puli zaplecza|
| **Ustawienia protokołu HTTP** | Kolekcja ustawień, w tym port, protokół, koligacja, sonda i limit czasu oparty na plikach cookie.  Te ustawienia określają, jak ruch jest kierowany do członków puli zaplecza|
| **Port frontonu** | Port, na którym Brama aplikacji nasłuchuje ruchu|
| **Odbiornika** | Kombinacja protokołu, konfiguracji adresu IP frontonu i portu frontonu. Jest to, co nasłuchuje żądań przychodzących.
|**Reguła**| Kierowanie ruchu do odpowiedniego zaplecza na podstawie ustawień protokołu HTTP.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Dodawanie sondy do istniejącej bramy aplikacji

Poniższy fragment kodu dodaje sondę do istniejącej bramy aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Usuwanie sondy z istniejącej bramy aplikacji

Poniższy fragment kodu usuwa sondę z istniejącej bramy aplikacji.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Pobieranie nazwy DNS bramy aplikacji

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. Gdy jest używany publiczny adres IP, brama aplikacji wymaga dynamicznie przypisywanej nazwy DNS, która nie jest przyjazna. Aby upewnić się, że użytkownicy końcowi mogą trafić bramę aplikacji, można użyć rekordu CNAME w celu wskazania publicznego punktu końcowego bramy aplikacji. [Konfigurowanie niestandardowej nazwy domeny dla platformy Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Aby to zrobić, pobierz szczegóły bramy aplikacji i skojarzony adres IP oraz nazwę DNS, używając elementu PublicIPAddress dołączonego do bramy aplikacji. Nazwa DNS bramy aplikacji powinna zostać użyta w celu utworzenia rekordu CNAME, który wskazuje dwóm aplikacjom internetowym tę nazwę DNS. Korzystanie z rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy aplikacji.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Następne kroki

Informacje na temat konfigurowania odciążania protokołu TLS przez odwiedzenie: [Konfigurowanie odciążania protokołu TLS](./tutorial-ssl-powershell.md)