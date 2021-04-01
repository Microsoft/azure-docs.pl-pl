---
title: Konfigurowanie zasad zapory aplikacji sieci Web do filtrowania geograficznego dla usługi Azure front-drzwi
description: W ramach tego samouczka nauczysz się tworzyć zasady filtrowania geograficznego i kojarzyć zasady z istniejącym hostem frontonu z systemem frontonu.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 479b1d8ed1f4238486bb78e33a6139463578dbba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563311"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Skonfiguruj zasady WAFego filtrowania geograficznego dla drzwi przednich

W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Ta przykładowa zasada filtrowania geograficznego będzie blokować żądania ze wszystkich innych krajów/regionów z wyjątkiem Stany Zjednoczone.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania zasad filtrowania geograficznego Skonfiguruj środowisko programu PowerShell i Utwórz profil dla drzwi przednich.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](../../azure-resource-manager/management/overview.md). 

Możesz zainstalować program [Azure PowerShell](/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj moduł AZ PowerShell module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Nawiązywanie połączenia z platformą Azure przy użyciu interaktywnego okna dialogowego logowania

```
Install-Module -Name Az
Connect-AzAccount
```
Upewnij się, że masz zainstalowaną bieżącą wersję programu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Zainstaluj AZ. Usługa frontdoor module 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Tworzenie profilu frontu drzwi

Utwórz profil z drzwiami wstępnymi, wykonując instrukcje opisane w [przewodniku szybki start: Tworzenie profilu front-drzwi](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Zdefiniuj warunek dopasowania do filtrowania geograficznego

Utwórz przykładowy warunek dopasowania, który wybiera żądania, które nie pochodzą z elementu "US" przy użyciu polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametrach podczas tworzenia warunku dopasowywania. Dwie litery kraju/regionu dla mapowania kraju/regionu są dostępne w [co to jest filtrowanie geograficzne w domenie dla drzwi platformy Azure](waf-front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Dodawanie warunku dopasowania filtrowania geograficznego do reguły za pomocą akcji i priorytetu

Utwórz obiekt CustomRule `nonUSBlockRule` w oparciu o warunek dopasowania, akcję i priorytet przy użyciu polecenia [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Element CustomRule może mieć wiele elementów MatchCondition.  W tym przykładzie ustawiono akcję blokowania i najwyższy priorytet 1.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Dodawanie reguł do zasad

Znajdź nazwę grupy zasobów, która zawiera profil przedni drzwi przy użyciu `Get-AzResourceGroup` . Następnie Utwórz `geoPolicy` obiekt zasad zawierający `nonUSBlockRule`  przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów zawierającej profil drzwi przednich. Musisz podać unikatową nazwę zasad geograficznych. 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](../../frontdoor/quickstart-create-front-door.md) artykułu. W poniższym przykładzie Zastąp nazwę zasad *geoPolicyAllowUSOnly* unikatową nazwą zasad.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Łączenie zasad WAF z głównym hostem frontonu

Połącz obiekt zasad WAF z istniejącym hostem frontonu w przód i zaktualizuj właściwości drzwi zewnętrznych. 

Aby to zrobić, najpierw Pobierz obiekt z drzwi przedniego przy użyciu polecenia [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Następnie ustaw właściwość frontonu WebApplicationFirewallPolicyLink na identyfikator resourceId `geoPolicy` polecenia using [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Musisz ustawić tylko raz Właściwość WebApplicationFirewallPolicyLink, aby połączyć zasady WAF z hostem frontonu w przód. Kolejne aktualizacje zasad są automatycznie stosowane do hosta frontonu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](../overview.md).
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).