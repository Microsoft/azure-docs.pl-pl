---
title: Skonfiguruj reguły niestandardowe WAF & domyślnego zestawu reguł dla drzwi frontonu platformy Azure
description: Dowiedz się, jak skonfigurować zasady WAF składają się z reguł niestandardowych i zarządzanych dla istniejącego punktu końcowego z przodu.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 40f0195ced375da09891eb577bd81f36b2f37cba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005552"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu Azure PowerShell

Zasady zapory aplikacji sieci Web platformy Azure (WAF) definiują inspekcje wymagane, gdy żądanie dociera do przodu.
W tym artykule opisano sposób konfigurowania zasad WAF, które składają się z niektórych reguł niestandardowych i z włączonym domyślnym zestawem reguł dla platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania zasad limitu szybkości Skonfiguruj środowisko programu PowerShell i Utwórz profil dla drzwi.

### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj polecenie AZ PowerShell module.

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```
Connect-AzAccount

```
Przed zainstalowaniem modułu usługi Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Zainstaluj AZ. Usługa frontdoor module 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Tworzenie profilu frontu drzwi

Utwórz profil z drzwiami wstępnymi, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie profilu front-drzwi](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Reguła niestandardowa oparta na parametrach http

Poniższy przykład pokazuje, jak skonfigurować regułę niestandardową z dwoma warunkami dopasowania przy użyciu polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Żądania pochodzą z określonej lokacji zdefiniowanej przez obiekt odwołujący, a ciąg zapytania nie zawiera "hasła". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Reguła niestandardowa oparta na metodzie żądania http

Utwórz regułę blokującą metodę "PUT" przy użyciu polecenia [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) w następujący sposób:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Utwórz regułę niestandardową opartą na ograniczeniu rozmiaru

Poniższy przykład tworzy regułę blokującą żądania o adresie URL dłuższym niż 100 znaków przy użyciu Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Dodaj zarządzany zestaw reguł domyślnych

Poniższy przykład tworzy zarządzany domyślny zestaw reguł przy użyciu Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdź nazwę grupy zasobów, która zawiera profil przedni drzwi przy użyciu `Get-AzResourceGroup` . Następnie skonfiguruj zasady zabezpieczeń z utworzonymi regułami w poprzednich krokach przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów zawierającej profil drzwi przednich.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Łączenie zasad z hostem frontonu przedniego

Połącz obiekt zasad zabezpieczeń z istniejącym hostem frontonu z przednim Drzwiem i zaktualizuj właściwości drzwi zewnętrznych. Najpierw Pobierz obiekt front-drzwi przy użyciu polecenia [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Następnie ustaw właściwość *WebApplicationFirewallPolicyLink* frontonu na identyfikator *ResourceID* "$myWAFPolicy $" utworzony w poprzednim kroku przy użyciu polecenia [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](../../frontdoor/quickstart-create-front-door.md) artykułu. Ponadto w poniższym przykładzie Zastąp $frontDoorName nazwą profilu od drzwi do przodu. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Musisz tylko ustawić właściwość *WebApplicationFirewallPolicyLink* , aby połączyć zasady zabezpieczeń z frontonem. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach zewnętrznych](../../frontdoor/front-door-overview.md) 
- Dowiedz się więcej o [WAF z przednim drzwiem](afds-overview.md)
