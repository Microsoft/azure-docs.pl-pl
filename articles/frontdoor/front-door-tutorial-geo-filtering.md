---
title: Samouczek — Konfigurowanie zasad WAFymi filtrowania geograficznego — drzwi platformy Azure
description: W ramach tego samouczka nauczysz się, jak utworzyć zasady WAFego filtrowania geograficznego i skojarzyć je z istniejącym hostem frontonu z przodu.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324030"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Samouczek: jak skonfigurować zasady WAFego filtrowania geograficznego dla drzwi czołowych
W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Ta przykładowa zasada filtrowania geograficznego będzie blokować żądania ze wszystkich innych krajów/regionów z wyjątkiem Stany Zjednoczone.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Zdefiniuj warunek dopasowania filtrowania geograficznego.
> - Dodaj warunek dopasowania do filtrowania geograficznego do reguły.
> - Dodawanie reguł do zasad.
> - Połącz zasady WAF z hostem frontonu usługa frontdoor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* Przed rozpoczęciem konfigurowania zasad filtrowania geograficznego Skonfiguruj środowisko programu PowerShell i Utwórz profil dla drzwi przednich.
* Utwórz drzwi tylne, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie profilu front-drzwi](quickstart-create-front-door.md).

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

W poniższym przykładzie użyto nazwy grupy zasobów *FrontDoorQS_rg0* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](quickstart-create-front-door.md) artykułu. W poniższym przykładzie Zastąp nazwę zasad *geoPolicyAllowUSOnly* unikatową nazwą zasad.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Łączenie zasad WAF z głównym hostem frontonu
Połącz obiekt zasad WAF z istniejącym hostem frontonu w przód i zaktualizuj właściwości drzwi zewnętrznych. 

Aby to zrobić, najpierw Pobierz obiekt z drzwi przedniego przy użyciu polecenia [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Następnie ustaw właściwość frontonu WebApplicationFirewallPolicyLink na identyfikator resourceId `geoPolicy` polecenia using [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Musisz ustawić tylko raz Właściwość WebApplicationFirewallPolicyLink, aby połączyć zasady WAF z hostem frontonu w przód. Kolejne aktualizacje zasad są automatycznie stosowane do hosta frontonu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach skonfigurowano regułę filtrowania geograficznego skojarzoną z zasadami WAFymi. Następnie połączysz zasady z hostem frontonu z przodu. Jeśli reguły filtrowania geograficznego lub zasady WAF nie są już potrzebne, należy najpierw usunąć skojarzenie zasad z hosta frontonu, aby można było usunąć zasady WAF.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="Usuń skojarzenie zasad WAF":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować zaporę aplikacji sieci Web dla drzwi z przodu, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Zapora aplikacji internetowej i usługa Front Door](front-door-waf.md)
