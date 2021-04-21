---
title: Jak używać API Management w Virtual Network z Application Gateway
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować usługę Azure API Management w usłudze Internal Virtual Network z Application Gateway (WAF) jako frontendem
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6500ecdb811306239951cb339abe2043d77b8cf2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813075"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrowanie usługi API Management w wewnętrznej sieci wirtualnej z usługą Application Gateway

## <a name="overview"></a><a name="overview"></a> Omówienie

Usługę API Management można skonfigurować w trybie wewnętrznym Virtual Network, co sprawia, że jest ona dostępna tylko z poziomu Virtual Network. Azure Application Gateway to usługa PAAS, która zapewnia równoważenie obciążenia warstwy 7. Działa ona jako usługa zwrotnego serwera proxy i udostępnia w swojej ofercie usługę Web Application Firewall (WAF).

Połączenie API Management aprowizowanych w wewnętrznej sieci wirtualnej z Application Gateway frontonie umożliwia korzystanie z następujących scenariuszy:

* Użyj tego samego API Management do użytku zarówno przez użytkowników wewnętrznych, jak i użytkowników zewnętrznych.
* Użyj pojedynczego zasobu API Management i masz podzestaw interfejsów API zdefiniowanych w API Management dostępny dla użytkowników zewnętrznych.
* Zapewnienie kluczowych sposobów przełączania dostępu do API Management z publicznego Internetu wł. i wył.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certyfikaty — pfx i cer dla nazwy hosta interfejsu API i pfx dla nazwy hosta portalu deweloperów.

## <a name="scenario"></a><a name="scenario"></a> Scenariusz

W tym artykule opisano sposób używania pojedynczej usługi API Management dla użytkowników wewnętrznych i zewnętrznych oraz sprawić, że będzie ona działać jako pojedynczy frontend zarówno dla interfejsów API lokalnych, jak i w chmurze. Zobaczysz również, jak uwidocznić tylko podzbiór interfejsów API (w przykładzie są one wyróżnione na zielono) dla użycia zewnętrznego przy użyciu funkcji routingu dostępnych w Application Gateway.

W pierwszym przykładzie konfiguracji wszystkie interfejsy API są zarządzane tylko z poziomu Virtual Network. Użytkownicy wewnętrzni (wyróżnione kolorem pomarańczowym) mogą uzyskać dostęp do wszystkich wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie przechodzi do Internetu. Łączność o wysokiej wydajności jest dostarczana za pośrednictwem obwodów usługi Express Route.

![trasa adresu URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Przed rozpoczęciem

* Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Zapoznaj się z instrukcjami instalacji w [te Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co jest wymagane do utworzenia integracji między API Management i Application Gateway?

* **Pula serwerów back-end:** Jest to wewnętrzny wirtualny adres IP usługi API Management wirtualnej.
* **Ustawienia puli serwerów back-end:** Każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port frontony:** Jest to port publiczny otwarty w bramie aplikacji. Ruch do niego trafiony jest przekierowywany do jednego z serwerów końcowych.
* **Odbiornik:** Odbiornik ma port frontona, protokół (Http lub Https, z rozróżnianą wielkością liter) oraz nazwę certyfikatu TLS/SSL (w przypadku konfigurowania odciążania protokołu TLS).
* **Reguła:** Reguła wiąże odbiornik z pulą serwerów back-end.
* **Niestandardowa sonda kondycji:** Application Gateway domyślnie używa sond opartych na adresach IP, aby ustalić, które serwery w puli BackendAddressPool są aktywne. Usługa API Management odpowiada tylko na żądania z prawidłowym nagłówkiem hosta, dlatego domyślne sondy nie powiodą się. Należy zdefiniować niestandardową sondę kondycji, aby ułatwić bramie aplikacji określenie, czy usługa jest żywa i powinna przesyłać żądania.
* **Niestandardowe certyfikaty domeny:** Aby uzyskać API Management z Internetu, musisz utworzyć mapowanie nazwy hosta CNAME na nazwę DNS Application Gateway frontonie. Gwarantuje to, że nagłówek nazwy hosta i certyfikat wysyłane do usługi Application Gateway do usługi API Management, który jest jednym z rozpoznanych przez usługę APIM jako prawidłowy. W tym przykładzie użyjemy dwóch certyfikatów — dla zaplecza i portalu dla deweloperów.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Kroki wymagane do integracji API Management i Application Gateway

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Utwórz adres Virtual Network, podsieć i publiczny adres IP dla Application Gateway. Utwórz kolejną podsieć dla API Management.
3. Utwórz nową API Management w podsieci sieci wirtualnej utworzonej powyżej i upewnij się, że używasz trybu wewnętrznego.
4. Skonfiguruj niestandardową nazwę domeny w usłudze API Management service.
5. Utwórz obiekt Application Gateway konfiguracji.
6. Utwórz zasób Application Gateway zasobów.
7. Utwórz rekord CNAME z publicznej nazwy DNS serwera Application Gateway do API Management serwera proxy.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Zewnętrzne udostępnianie portalu dla deweloperów za pośrednictwem Application Gateway

W tym przewodniku udostępnimy również portal dla **deweloperów** odbiorcom zewnętrznym za pośrednictwem Application Gateway. Wymaga to dodatkowych kroków w celu utworzenia odbiornika, sondy, ustawień i reguł portalu dla deweloperów. Wszystkie szczegóły są podane w odpowiednich krokach.

> [!WARNING]
> Jeśli używasz usługi Azure AD lub uwierzytelniania innej firmy, włącz funkcję [koligacji](../application-gateway/features.md#session-affinity) sesji na podstawie plików cookie w Application Gateway.

> [!WARNING]
> Aby zapobiec Application Gateway zapory aplikacji internetowej przed przerwaniem pobierania specyfikacji OpenAPI w portalu dla deweloperów, należy wyłączyć regułę zapory `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway WAF, które mogą łamać funkcje portalu, obejmują:
> 
> - `920300`, `920330` , , , , , , , dla trybu `931130` `942100` `942110` `942180` `942200` `942260` `942340` `942370` administracyjnego
> - `942200`, `942260` , , dla `942370` `942430` `942440` opublikowanego portalu

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
```

Uwierzytelnij się przy użyciu poświadczeń.

### <a name="step-2"></a>Krok 2

Wybierz odpowiednią subskrypcję.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Krok 3

Utwórz grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Upewnij się, że wszystkie polecenia służące do tworzenia bramy aplikacji używają tej samej grupy zasobów.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie Virtual Network podsieci dla bramy aplikacji

W poniższym przykładzie pokazano, jak utworzyć Virtual Network przy użyciu Resource Manager.

### <a name="step-1"></a>Krok 1

Przypisz zakres adresów 10.0.0.0/24 do zmiennej podsieci, która ma być używana na Application Gateway podczas tworzenia Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz zakres adresów 10.0.1.0/24 do zmiennej podsieci, która ma być używana na API Management podczas tworzenia Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Utwórz grupę Virtual Network o nazwie **appgwvnet** w grupie zasobów **apim-appGw-RG** dla regionu Zachodnie stany USA. Użyj prefiksu 10.0.0.0/16 z podsieciami 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisywanie zmiennej podsieci do następnych kroków

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi API Management w sieci wirtualnej skonfigurowanej w trybie wewnętrznym

W poniższym przykładzie pokazano, jak utworzyć usługę API Management w sieci wirtualnej skonfigurowanej tylko do dostępu wewnętrznego.

### <a name="step-1"></a>Krok 1

Utwórz obiekt API Management Virtual Network przy użyciu podsieci utworzonej $apimsubnetdata powyżej.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Utwórz usługę API Management wewnątrz Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Po zakończeniu działania powyższego polecenia zapoznaj się z tematem Konfiguracja dns wymagana do uzyskania dostępu do wewnętrznej sieci [API Management wirtualnej w](api-management-using-with-internal-vnet.md#apim-dns-configuration) celu uzyskania do niego dostępu. Ten krok może potrwać ponad pół godziny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurowanie niestandardowej nazwy domeny w u API Management

> [!IMPORTANT]
> Nowy [portal dla deweloperów](api-management-howto-developer-portal.md) wymaga również włączenia łączności API Management punktu końcowego zarządzania usługą API Management oprócz poniższych kroków.

### <a name="step-1"></a>Krok 1

Zaimicj następujące zmienne ze szczegółami certyfikatów z kluczami prywatnymi dla domen. W tym przykładzie użyjemy i `api.contoso.net` `portal.contoso.net` .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Krok 2

Utwórz i ustaw obiekty konfiguracji nazwy hosta dla serwera proxy i portalu.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Aby skonfigurować łączność ze starszym portalem dla deweloperów, należy zastąpić ciągu `-HostnameType DeveloperPortal` . `-HostnameType Portal`

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Tworzenie konfiguracji bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Utwórz konfigurację adresu IP bramy aplikacji o nazwie **gatewayIP01.** Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port adresu IP frontonia dla punktu końcowego publicznego adresu IP. Ten port jest portem, z który łączą się użytkownicy końcowi.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Skonfiguruj certyfikaty dla serwera Application Gateway, które będą używane do odszyfrowywania i ponownego szyfrowania ruchu przechodzącego przez program.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Utwórz odbiorniki HTTP dla Application Gateway. Przypisz do nich konfigurację adresu IP frontonu, port i certyfikaty TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Utwórz niestandardowe sondy do punktu końcowego API Management `ContosoApi` proxy usługi. Ścieżka jest `/status-0123456789abcdef` domyślnym punktem końcowym kondycji hostowany we wszystkich API Management usługi. Ustaw `api.contoso.net` jako niestandardową nazwę hosta sondy, aby zabezpieczyć ją przy użyciu certyfikatu TLS/SSL.

> [!NOTE]
> Nazwa hosta jest `contosoapi.azure-api.net` domyślną nazwą hosta serwera proxy skonfigurowaną podczas tworzenia usługi o `contosoapi` nazwie na publicznej platformie Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat, który ma być używany w zasobach puli zaplecza z obsługą usługi TLS. Jest to ten sam certyfikat, który został podany w kroku 4 powyżej.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Skonfiguruj ustawienia zaplecza HTTP dla Application Gateway. Obejmuje to ustawienie limitu czasu dla żądania zaplecza, po którym zostanie anulowane. Ta wartość różni się od prze wychodzącego czasu sondy.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj pulę adresów IP zaplecza o nazwie **apimbackend**  przy użyciu wewnętrznego wirtualnego adresu IP API Management utworzonej powyżej.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Utwórz reguły dla Application Gateway, aby używać routingu podstawowego.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Zmień typ -RuleType i routing, aby ograniczyć dostęp do niektórych stron portalu dla deweloperów.

### <a name="step-11"></a>Krok 11

Skonfiguruj liczbę wystąpień i rozmiar Application Gateway. W tym przykładzie używamy [sku WAF SKU](../web-application-firewall/ag/ag-overview.md) w celu zwiększenia bezpieczeństwa API Management zasobów.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Skonfiguruj aplikację WAF tak, aby był w trybie "Zapobieganie".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Tworzenie Application Gateway

Utwórz Application Gateway ze wszystkimi obiektami konfiguracji z poprzednich kroków.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Rekord CNAME API Management nazwę hosta serwera proxy na publiczną nazwę DNS Application Gateway zasobów

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. W przypadku korzystania z publicznego adresu IP Application Gateway dynamicznie przypisywana nazwa DNS, co może nie być łatwe w użyciu.

Nazwa APPLICATION GATEWAY DNS powinna być używana do utworzenia rekordu CNAME, który wskazuje nazwę hosta serwera proxy usługi APIM (np. w powyższych przykładach) na tę `api.contoso.net` nazwę DNS. Aby skonfigurować rekord CNAME adresu IP frontonu, pobierz szczegóły rekordu Application Gateway i jego skojarzoną nazwę IP/DNS przy użyciu elementu PublicIPAddress. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Podsumowanie
Usługa Azure API Management skonfigurowana w sieci wirtualnej zapewnia jeden interfejs bramy dla wszystkich skonfigurowanych interfejsów API, niezależnie od tego, czy są one hostowane lokalnie, czy w chmurze. Integracja usługi Application Gateway z usługą API Management zapewnia elastyczność selektywnego zapewniania dostępu do określonych interfejsów API w Internecie, a także zapewnia Web Application Firewall jako fronton dla wystąpienia API Management.

## <a name="next-steps"></a><a name="next-steps"></a> Następne kroki
* Dowiedz się więcej o Azure Application Gateway
  * [Application Gateway omówienie](../application-gateway/overview.md)
  * [Application Gateway Web Application Firewall](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway przy użyciu routingu opartego na ścieżkach](../application-gateway/tutorial-url-route-powershell.md)
* Dowiedz się więcej API Management sieci wirtualnych
  * [Korzystanie API Management dostępne tylko w sieci wirtualnej](api-management-using-with-internal-vnet.md)
  * [Korzystanie API Management sieci wirtualnej](api-management-using-with-vnet.md)
