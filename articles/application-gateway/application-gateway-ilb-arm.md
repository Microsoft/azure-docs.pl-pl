---
title: Korzystanie z Load Balancer wewnętrznego — Azure Application Gateway
description: Ta strona zawiera instrukcje dotyczące tworzenia, konfigurowania, uruchamiania i usuwania bramy aplikacji platformy Azure za pomocą wewnętrznego modułu równoważenia obciążenia na potrzeby usługi Azure Resource Manager
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 439523fe55f231548ebc80ebc5d3b53c2f0d6e2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808143"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB)

Usługę Azure Application Gateway można skonfigurować z internetowym wirtualnym adresem IP lub wewnętrznym punktem końcowym niepołączonym z Internetem, znanym także jako punkt końcowy wewnętrznego modułu równoważenia obciążenia. Konfigurowanie bramy przy użyciu wewnętrznego modułu równoważenia obciążenia jest pomocne w przypadku wewnętrznych aplikacji LOB niepołączonych z Internetem. Jest on również przydatny w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń, które nie są dostępne w Internecie, ale nadal wymagają dystrybucji obciążenia z działaniem okrężnym, lepkość sesji lub Transport Layer Security (TLS), wcześniej znanej jako SSL (SSL), zakończenie.

W tym artykule przeprowadzimy Cię przez proces konfigurowania bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Zainstaluj najnowszą wersję modułu Azure PowerShell, postępując zgodnie z [instrukcjami dotyczącymi instalacji](/powershell/azure/install-az-ps).
2. Utworzysz sieć wirtualną i podsieć dla usługi Application Gateway. Upewnij się, że z podsieci nie korzystają żadne maszyny wirtualne ani wdrożenia w chmurze. Usługa Application Gateway musi sama znajdować się w podsieci sieci wirtualnej.
3. Serwery konfigurowane do używania bramy aplikacji muszą być umieszczone w sieci wirtualnej lub z przypisanym adresem IP/VIP lub mieć w niej utworzone punkty końcowe.

## <a name="what-is-required-to-create-an-application-gateway"></a>Co jest wymagane do utworzenia bramy aplikacji?

* **Pula serwerów zaplecza:** lista adresów IP serwerów zaplecza. Adresy IP na liście powinny należeć do sieci wirtualnej, ale w innej podsieci bramy aplikacji, lub być publicznymi bądź wirtualnymi adresami IP.
* **Ustawienia puli serwerów zaplecza:** Każda pula ma ustawienia, takie jak port, protokół i koligacja oparta na plikach cookie. Te ustawienia są powiązane z pulą i są stosowane do wszystkich serwerów w tej puli.
* **Port frontonu:** Ten port jest portem publicznym otwartym na bramie aplikacji. Ruch trafia do tego portu, a następnie jest przekierowywany do jednego z serwerów zaplecza.
* **Odbiornik:** odbiornik ma port frontonu, protokół (Http lub Https, z uwzględnieniem wielkości liter) oraz nazwę certyfikatu SSL (w przypadku konfigurowania odciążania protokołu SSL).
* **Reguła:** reguła wiąże odbiornik z pulą serwerów zaplecza i umożliwia zdefiniowanie, do której puli serwerów zaplecza ma być przekierowywany ruch w przypadku trafienia do określonego odbiornika. Obecnie jest obsługiwana tylko reguła *podstawowa*. Reguła *podstawowa* to dystrybucja obciążenia z działaniem okrężnym.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Różnica między klasycznym modelem wdrożenia Azure i usługą Azure Resource Manager polega na kolejności tworzenia bramy aplikacji i elementów, które należy skonfigurować.
W usłudze Resource Manager wszystkie elementy składające się na bramę aplikacji są konfigurowane osobno, a następnie składane w celu utworzenia zasobu bramy aplikacji.

Oto kroki wymagane do utworzenia bramy aplikacji:

1. Tworzenie grupy zasobów dla usługi Resource Manager
2. Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji
3. Tworzenie obiektu konfiguracji bramy aplikacji
4. Tworzenie zasobu bramy aplikacji

## <a name="create-a-resource-group-for-resource-manager"></a>Tworzenie grupy zasobów dla usługi Resource Manager

Upewnij się, że program PowerShell został przełączony do trybu umożliwiającego korzystanie z poleceń cmdlet usługi Azure Resource Manager. Więcej informacji znajduje się w artykule [Using Windows PowerShell with Resource Manager](../powershell-azure-resource-manager.md) (Używanie programu Windows PowerShell z usługą Resource Manager).

### <a name="step-1"></a>Krok 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Krok 2

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzSubscription
```

Zostanie wyświetlony monit o uwierzytelnienie przy użyciu własnych poświadczeń.

### <a name="step-3"></a>Krok 3

Wybierz subskrypcję platformy Azure do użycia.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Krok 4

Utwórz nową grupę zasobów (ten krok można pominąć, jeśli używasz istniejącej grupy zasobów).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Usługa Azure Resource Manager wymaga, żeby wszystkie grupy zasobów miały lokalizację. Będzie ona używana jako domyślna lokalizacja zasobów w danej grupie. Upewnij się, że we wszystkich poleceniach służących do tworzenia bramy aplikacji jest używana ta sama grupa zasobów.

W poprzednim przykładzie została utworzona grupa zasobów o nazwie "appgw-RG" i lokalizacji "zachodnie stany USA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Tworzenie sieci wirtualnej i podsieci dla bramy aplikacji

W poniższym przykładzie pokazano, jak utworzyć sieć wirtualną przy użyciu usługi Resource Manager:

### <a name="step-1"></a>Krok 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Ten krok przypisuje zakres adresów 10.0.0.0/24 do zmiennej podsieci, która ma zostać użyta do utworzenia sieci wirtualnej.

### <a name="step-2"></a>Krok 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ten krok powoduje utworzenie sieci wirtualnej o nazwie "appgwvnet" w grupie zasobów "appgw-RG" dla regionu zachodnie stany USA przy użyciu prefiksu 10.0.0.0/16 z podsiecią 10.0.0.0/24.

### <a name="step-3"></a>Krok 3

```powershell
$subnet = $vnet.subnets[0]
```

Ten krok powoduje przypisanie obiektu podsieci do zmiennej $subnet w celu wykonania następnych kroków.

## <a name="create-an-application-gateway-configuration-object"></a>Tworzenie obiektu konfiguracji bramy aplikacji

### <a name="step-1"></a>Krok 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Ten krok powoduje utworzenie konfiguracji adresu IP bramy aplikacji o nazwie "gatewayIP01". Uruchomiona usługa Application Gateway wybierze adres IP ze skonfigurowanej podsieci i skieruje ruch sieciowy do adresów IP w puli adresów IP zaplecza. Pamiętaj, że każde wystąpienie będzie mieć jeden adres IP.

### <a name="step-2"></a>Krok 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ten krok umożliwia skonfigurowanie puli adresów IP zaplecza o nazwie "pool01" z adresami IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Są to adresy IP odbierające ruch sieciowy pochodzący z punktu końcowego adresu IP frontonu. Powyższe adresy IP można zastąpić własnymi dodawanymi punktami końcowymi adresów IP aplikacji.

### <a name="step-3"></a>Krok 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ten krok umożliwia skonfigurowanie ustawienia bramy aplikacji "poolsetting01" dla ruchu sieciowego ze zrównoważonym obciążeniem w puli zaplecza.

### <a name="step-4"></a>Krok 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ten krok umożliwia skonfigurowanie portu adresu IP frontonu o nazwie "frontendport01" dla ILB.

### <a name="step-5"></a>Krok 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ten krok powoduje utworzenie konfiguracji adresu IP frontonu o nazwie "fipconfig01" i skojarzenie jej z prywatnym adresem IP z bieżącej podsieci sieci wirtualnej.

### <a name="step-6"></a>Krok 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ten krok powoduje utworzenie odbiornika o nazwie "listener01" i skojarzenie portu frontonu z konfiguracją adresów IP frontonu.

### <a name="step-7"></a>Krok 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Ten krok powoduje utworzenie reguły routingu modułu równoważenia obciążenia o nazwie "rule01", która służy do konfigurowania zachowania modułu równoważenia obciążenia.

### <a name="step-8"></a>Krok 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ten krok umożliwia skonfigurowanie rozmiaru wystąpienia bramy aplikacji.

> [!NOTE]
> Wartość domyślna dla pojemności wynosi 2. W obszarze Nazwa jednostki SKU można wybrać między Standard_Small, Standard_Medium i Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Tworzenie bramy aplikacji przy użyciu polecenia New-AzureApplicationGateway

Tworzy bramę aplikacji ze wszystkimi elementami konfiguracji z poprzednich kroków. W tym przykładzie brama aplikacji ma nazwę „appgwtest”.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

W tym kroku zostanie utworzona Brama aplikacji ze wszystkimi elementami konfiguracji z poprzednich kroków. W przykładzie brama aplikacji ma nazwę „appgwtest”.

## <a name="delete-an-application-gateway"></a>Usuwanie bramy aplikacji

Aby usunąć bramę aplikacji, należy wykonać następujące czynności w kolejności:

1. Użyj polecenia cmdlet `Stop-AzApplicationGateway`, aby zatrzymać bramę.
2. Użyj polecenia cmdlet `Remove-AzApplicationGateway`, aby usunąć bramę.
3. Aby sprawdzić, czy brama została usunięta, użyj polecenia cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Krok 1

Pobierz obiekt bramy aplikacji i skojarz go ze zmienną „$getgw”.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Krok 2

Użyj polecenia `Stop-AzApplicationGateway`, aby zatrzymać bramę aplikacji. Ten przykład pokazuje `Stop-AzApplicationGateway` polecenie cmdlet w pierwszym wierszu, a następnie dane wyjściowe.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Po zatrzymaniu bramy aplikacji użyj polecenia cmdlet `Remove-AzApplicationGateway`, aby usunąć usługę.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Przełącznik **-force** umożliwia pomijanie komunikatu potwierdzającego usunięcie.

Aby sprawdzić, czy usługa została usunięta, możesz użyć polecenia cmdlet `Get-AzApplicationGateway`. Ten krok nie jest wymagany.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz skonfigurować odciążanie protokołu SSL, zobacz artykuł [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Konfigurowanie bramy aplikacji na potrzeby odciążania protokołu SSL).

Więcej ogólnych informacji na temat opcji równoważenia obciążenia możesz znaleźć w następujących artykułach:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

