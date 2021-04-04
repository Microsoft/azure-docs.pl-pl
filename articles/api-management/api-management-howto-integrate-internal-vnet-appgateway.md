---
title: Jak używać API Management w Virtual Network z Application Gateway
titleSuffix: Azure API Management
description: Dowiedz się, jak zainstalować i skonfigurować usługę Azure API Management w ramach wewnętrznego Virtual Network za pomocą Application Gateway (WAF) jako frontonu
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
ms.openlocfilehash: 3db1c8bfc3a11151342589af0873d88e3d90c6a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91825624"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrowanie usługi API Management w wewnętrznej sieci wirtualnej z usługą Application Gateway

## <a name="overview"></a><a name="overview"></a> Przegląd

Usługę API Management można skonfigurować w Virtual Network w trybie wewnętrznym, co umożliwia dostęp do niej tylko z poziomu Virtual Network. Azure Application Gateway to usługa PAAS, która zapewnia moduł równoważenia obciążenia warstwy 7. Działa jako usługa zwrotnego serwera proxy i udostępnia między swoimi ofertami zaporę aplikacji sieci Web (WAF).

Łączenie API Management obsługiwane w wewnętrznej sieci wirtualnej przy użyciu Application Gateway frontonu umożliwia realizację następujących scenariuszy:

* Użyj tego samego zasobu API Management do wykorzystania przez użytkowników wewnętrznych i użytkowników zewnętrznych.
* Użyj pojedynczego zasobu API Management i określ podzestaw interfejsów API, które są zdefiniowane w API Management dostępne dla użytkowników zewnętrznych.
* Zapewnianie klawisza granicznego w celu przełączenia dostępu do API Management z publicznej sieci Internet.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certyfikaty — PFX i CER dla nazwy hosta interfejsu API i PFX dla nazwy hosta portalu dla deweloperów.

## <a name="scenario"></a><a name="scenario"></a> Scenariusz

W tym artykule opisano sposób korzystania z jednej usługi API Management zarówno dla użytkowników wewnętrznych, jak i zewnętrznych oraz że pełnią rolę jednego frontonu dla interfejsów API zarówno lokalnych, jak i w chmurze. Zobaczysz również, jak uwidocznić tylko podzestaw interfejsów API (w przykładzie są one wyróżnione kolorem zielonym) na użytek zewnętrzny przy użyciu funkcji routingu dostępnych w Application Gateway.

W pierwszym przykładzie konfiguracji wszystkie interfejsy API są zarządzane tylko z poziomu Virtual Network. Użytkownicy wewnętrzni (wyróżniony w kolorze pomarańczowym) mogą uzyskiwać dostęp do wszystkich wewnętrznych i zewnętrznych interfejsów API. Ruch nigdy nie przechodzi do Internetu. Łączność o wysokiej wydajności jest dostarczana za pośrednictwem obwodów usługi Express Route.

![Trasa URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"></a> Przed rozpoczęciem

* Upewnij się, że używasz najnowszej wersji programu Azure PowerShell. Zapoznaj się z instrukcjami dotyczącymi instalacji w artykule [instalowanie Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Co jest wymagane do utworzenia integracji między API Management i Application Gateway?

* **Pula serwerów zaplecza:** Jest to wewnętrzny wirtualny adres IP usługi API Management.
* **Ustawienia puli serwerów zaplecza:** Każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są stosowane do wszystkich serwerów w puli.
* **Port frontonu:** Jest to port publiczny, który jest otwierany w bramie aplikacji. Ruch przychodzący do jednego z serwerów zaplecza.
* **Odbiornik:** Odbiornik ma port frontonu, protokół (http lub https, te wartości są zależne od wielkości liter) oraz nazwę certyfikatu TLS/SSL (w przypadku konfigurowania odciążania protokołu TLS).
* **Reguła:** Reguła wiąże odbiornik z pulą serwerów zaplecza.
* **Niestandardowa sonda kondycji:** Application Gateway domyślnie używa sond opartych na adresie IP, aby ustalić, które serwery w BackendAddressPool są aktywne. Usługa API Management odpowiada tylko na żądania z prawidłowym nagłówkiem hosta, dlatego domyślne sondy kończą się niepowodzeniem. Należy zdefiniować niestandardową sondę kondycji, aby pomóc bramie aplikacji w ustaleniu, że usługa jest aktywna i powinna przesyłać żądania dalej.
* **Niestandardowe certyfikaty domeny:** Aby uzyskać dostęp do API Management z Internetu, należy utworzyć mapowanie CNAME nazwy hosta na nazwę DNS frontonu Application Gateway. Dzięki temu nagłówek i certyfikat nazwy hosta wysyłane do Application Gateway, które są przekazywane do API Management, to jeden APIM może być rozpoznawany jako prawidłowy. W tym przykładzie będziemy używać dwóch certyfikatów — dla zaplecza i portalu dla deweloperów.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"></a> Kroki wymagane do integracji API Management i Application Gateway

1. Utworzenie grupy zasobów dla usługi Resource Manager.
2. Utwórz Virtual Network, podsieć i publiczny adres IP dla Application Gateway. Utwórz inną podsieć dla API Management.
3. Utwórz usługę API Management wewnątrz podsieci sieci wirtualnej utworzonej powyżej i upewnij się, że używasz trybu wewnętrznego.
4. Skonfiguruj niestandardową nazwę domeny w usłudze API Management.
5. Utwórz obiekt konfiguracji Application Gateway.
6. Utwórz zasób Application Gateway.
7. Utwórz rekord CNAME z publicznej nazwy DNS Application Gateway do nazwy hosta API Management proxy.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Udostępnianie portalu dla deweloperów zewnętrznie za pomocą Application Gateway

W tym przewodniku udostępnimy również **Portal deweloperów** dla zewnętrznych odbiorców za pomocą Application Gateway. Wymaga wykonania dodatkowych czynności w celu utworzenia odbiornika, sondy, ustawień i reguł portalu deweloperów. Wszystkie szczegóły znajdują się w odpowiednich krokach.

> [!WARNING]
> Jeśli używasz usługi Azure AD lub uwierzytelniania innej firmy, Włącz funkcję [koligacji sesji na podstawie plików cookie](../application-gateway/features.md#session-affinity) w Application Gateway.

> [!WARNING]
> Aby uniemożliwić Application Gateway WAF pobieranie specyfikacji OpenAPI w portalu dla deweloperów, należy wyłączyć regułę zapory `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"` .
> 
> Application Gateway reguły WAF, które mogą spowodować przerwanie funkcjonalności portalu:
> 
> - `920300`,,,,,,,, `920330` `931130` `942100` `942110` `942180` `942200` `942260` `942340` , `942370` dla trybu administracyjnego
> - `942200`, `942260` ,,, `942370` `942430` `942440` dla opublikowanego portalu

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

### <a name="step-1"></a>Krok 1

Zaloguj się do platformy Azure.

```powershell
Connect-AzAccount
```

Uwierzytelnij się przy użyciu swoich poświadczeń.

### <a name="step-2"></a>Krok 2

Wybierz żądaną subskrypcję.

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

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie Virtual Network i podsieci dla bramy aplikacji

Poniższy przykład pokazuje, jak utworzyć Virtual Network przy użyciu Menedżer zasobów.

### <a name="step-1"></a>Krok 1

Przypisz zakres adresów 10.0.0.0/24 do zmiennej podsieci, która ma być używana dla Application Gateway podczas tworzenia Virtual Network.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Krok 2

Przypisz zakres adresów 10.0.1.0/24 do zmiennej podsieci, która ma być używana dla API Management podczas tworzenia Virtual Network.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Krok 3

Utwórz Virtual Network o nazwie **appgwvnet** w grupie zasobów **APIM-appGw-RG** dla regionu zachodnie stany USA. Użyj prefiksu 10.0.0.0/16 z podsieciami 10.0.0.0/24 i 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Krok 4

Przypisz zmienną podsieci do następnych kroków

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Tworzenie usługi API Management wewnątrz sieci wirtualnej skonfigurowanej w trybie wewnętrznym

Poniższy przykład pokazuje, jak utworzyć usługę API Management w sieci wirtualnej skonfigurowanej tylko do dostępu wewnętrznego.

### <a name="step-1"></a>Krok 1

Utwórz obiekt Virtual Network API Management przy użyciu $apimsubnetdata podsieci utworzonego powyżej.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Krok 2

Utwórz usługę API Management w Virtual Network.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Gdy powyższe polecenie zakończy się pomyślnie, odwołuje się do [konfiguracji DNS wymaganej do](api-management-using-with-internal-vnet.md#apim-dns-configuration) uzyskania dostępu do tej usługi API Management wewnętrznej sieci wirtualnej. Ten krok może potrwać ponad pół godziny.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Skonfiguruj niestandardową nazwę domeny w API Management

> [!IMPORTANT]
> [Nowy portal dla deweloperów](api-management-howto-developer-portal.md) wymaga również włączenia połączenia z punktem końcowym zarządzania API Management, a także z poniższymi krokami.

### <a name="step-1"></a>Krok 1

Zainicjuj następujące zmienne, podając szczegóły certyfikatów z kluczami prywatnymi domen. W tym przykładzie będziemy używać `api.contoso.net` i `portal.contoso.net` .  

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

Utwórz i ustaw obiekty konfiguracji nazwy hosta dla serwera proxy oraz dla portalu.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Aby skonfigurować starszą łączność z portalem deweloperów, należy `-HostnameType DeveloperPortal` zastąpić `-HostnameType Portal` .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Tworzenie publicznego adresu IP dla konfiguracji frontonu

Utwórz zasób publicznego adresu IP **publicIP01** w grupie zasobów.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Adres IP jest przypisywany do bramy aplikacji w chwili uruchamiania usługi.

## <a name="create-application-gateway-configuration"></a>Utwórz konfigurację bramy aplikacji

Wszystkie elementy konfiguracji muszą zostać skonfigurowane przed utworzeniem bramy aplikacji. Poniższe kroki umożliwiają utworzenie elementów konfiguracji wymaganych w przypadku zasobu bramy aplikacji.

### <a name="step-1"></a>Krok 1

Utwórz konfigurację adresu IP bramy aplikacji o nazwie **gatewayIP01**. Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Krok 2

Skonfiguruj port frontonu IP dla punktu końcowego publicznego adresu IP. Port ten jest portem, z którym łączą się użytkownicy końcowi.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Krok 3

Skonfiguruj adres IP frontonu z punktem końcowym publicznego adresu IP.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Krok 4

Skonfiguruj certyfikaty dla Application Gateway, które będą używane do odszyfrowywania i ponownego szyfrowania ruchu przechodzącego przez.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Krok 5

Utwórz odbiorniki HTTP dla Application Gateway. Przypisz do nich konfigurację IP frontonu, port oraz certyfikaty TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Krok 6

Utwórz niestandardowe sondy do `ContosoApi` punktu końcowego domeny serwera proxy usługi API Management. Ścieżka `/status-0123456789abcdef` jest domyślnym punktem końcowym kondycji hostowanym na wszystkich API Management usługach. Ustaw `api.contoso.net` jako niestandardową nazwę hosta sondy do zabezpieczenia za pomocą certyfikatu TLS/SSL.

> [!NOTE]
> Nazwa hosta `contosoapi.azure-api.net` jest domyślną nazwą hosta serwera proxy skonfigurowaną, gdy usługa nazwana `contosoapi` jest tworzona na publicznej platformie Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/internal-status-0123456789abcdef" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Krok 7

Przekaż certyfikat, który będzie używany w zasobach puli zaplecza z włączoną obsługą protokołu TLS. Jest to ten sam certyfikat, który został dostarczony w kroku 4 powyżej.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Krok 8

Skonfiguruj ustawienia wewnętrznej bazy danych protokołu HTTP dla Application Gateway. Obejmuje to ustawienie limitu czasu dla żądania wewnętrznej bazy danych, po upływie którego są anulowane. Ta wartość różni się od limitu czasu sondy.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Krok 9

Skonfiguruj pulę adresów IP zaplecza o nazwie **apimbackend**  z wewnętrznym wirtualnym adresem ip usługi API Management utworzonej powyżej.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Krok 10

Utwórz reguły dla Application Gateway, aby użyć routingu podstawowego.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Zmień wartość-Ruletype i routing, aby ograniczyć dostęp do niektórych stron portalu dla deweloperów.

### <a name="step-11"></a>Krok 11

Skonfiguruj liczbę wystąpień i rozmiar Application Gateway. W tym przykładzie korzystamy z [jednostki SKU WAF](../web-application-firewall/ag/ag-overview.md) , aby zwiększyć bezpieczeństwo zasobów API Management.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Krok 12

Skonfiguruj WAF jako "Zapobieganie".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Utwórz Application Gateway

Utwórz Application Gateway ze wszystkimi obiektami konfiguracji z poprzednich kroków.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Odnotuj nazwę hosta API Management proxy do publicznej nazwy DNS zasobu Application Gateway

Po utworzeniu bramy następnym krokiem jest skonfigurowanie frontonu na potrzeby komunikacji. W przypadku korzystania z publicznego adresu IP Application Gateway wymaga dynamicznie przypisanej nazwy DNS, co może nie być łatwe w użyciu.

Nazwa DNS Application Gateway powinna zostać użyta do utworzenia rekordu CNAME, który wskazuje nazwę hosta serwera proxy APIM (np. `api.contoso.net` w powyższych przykładach) do tej nazwy DNS. Aby skonfigurować rekord CNAME IP frontonu, Pobierz szczegóły Application Gateway i skojarzonego z nim nazwy IP/DNS przy użyciu elementu PublicIPAddress. Użycie rekordów A nie jest zalecane, ponieważ adres VIP może ulec zmianie po ponownym uruchomieniu bramy.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"></a> Podsumowanie
Usługa Azure API Management skonfigurowana w sieci wirtualnej zapewnia pojedynczy interfejs bramy dla wszystkich skonfigurowanych interfejsów API, niezależnie od tego, czy są one hostowane lokalnie, czy w chmurze. Integracja Application Gateway z API Management zapewnia elastyczność selektywnego włączania konkretnych interfejsów API w Internecie, a także udostępnia zaporę aplikacji sieci Web jako fronton do wystąpienia API Management.

## <a name="next-steps"></a><a name="next-steps"></a> Następne kroki
* Dowiedz się więcej o usłudze Azure Application Gateway
  * [Przegląd Application Gateway](../application-gateway/overview.md)
  * [Application Gateway zapory aplikacji sieci Web](../web-application-firewall/ag/ag-overview.md)
  * [Application Gateway przy użyciu routingu opartego na ścieżkach](../application-gateway/tutorial-url-route-powershell.md)
* Dowiedz się więcej o API Management i sieci wirtualnych
  * [Używanie API Management dostępne tylko w sieci wirtualnej](api-management-using-with-internal-vnet.md)
  * [Używanie API Management w sieci wirtualnej](api-management-using-with-vnet.md)
