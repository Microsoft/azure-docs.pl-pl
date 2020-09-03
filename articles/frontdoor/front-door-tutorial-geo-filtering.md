---
title: Samouczek — Konfigurowanie zasad WAFymi filtrowania geograficznego — drzwi platformy Azure
description: W ramach tego samouczka nauczysz się tworzyć zasady filtrowania geograficznego i kojarzyć zasady z istniejącym hostem frontonu z przodu.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: duau
ms.openlocfilehash: 31892232d5483bd2cb99d27c4672dbf347b904ef
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399025"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Jak skonfigurować zasady filtrowania geograficznego zapory aplikacji internetowej dla własnej usługi Front Door
W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Ta przykładowa zasada filtrowania geograficznego będzie blokować żądania ze wszystkich innych krajów/regionów z wyjątkiem Stany Zjednoczone.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad filtrowania geograficznego Skonfiguruj środowisko programu PowerShell i Utwórz profil dla drzwi przednich.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj moduł AZ PowerShell module.

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
Utwórz profil z drzwiami wstępnymi, wykonując instrukcje opisane w [przewodniku szybki start: Tworzenie profilu front-drzwi](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Zdefiniuj warunek dopasowania do filtrowania geograficznego

Utwórz przykładowy warunek dopasowania, który wybiera żądania, które nie pochodzą z elementu "US" przy użyciu polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametrach podczas tworzenia warunku dopasowywania. W [tym miejscu](front-door-geo-filtering.md)są udostępniane dwa kody kraju/regionu na potrzeby mapowania kraju/regionu.

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
Znajdź nazwę grupy zasobów, która zawiera profil przedni drzwi przy użyciu `Get-AzResourceGroup` . Następnie Utwórz `geoPolicy` obiekt zasad zawierający `nonUSBlockRule`  przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów zawierającej profil drzwi przednich. Należy podać unikatową nazwę zasad filtrowania geograficznego. 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](quickstart-create-front-door.md) artykułu. W poniższym przykładzie Zastąp nazwę zasad *geoPolicyAllowUSOnly* unikatową nazwą zasad.

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
- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](waf-overview.md).
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
