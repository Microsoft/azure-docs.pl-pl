---
title: Skonfiguruj regułę limitu szybkości WAF dla czołowych drzwi Azure PowerShell
description: Dowiedz się, jak skonfigurować regułę limitu szybkości dla istniejącego punktu końcowego z przodu.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: 4b8aa72c7b77da8fdde9925325587b67411de8d8
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506417"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Skonfiguruj regułę limitu szybkości zapory aplikacji sieci Web przy użyciu Azure PowerShell
Reguła limitu szybkości usługi Azure Web Application Firewall (WAF) dla drzwi frontonu platformy Azure określa liczbę żądań dozwolonych od klientów w czasie trwania jednej minuty.
W tym artykule opisano sposób konfigurowania reguły limitu szybkości WAF, która kontroluje liczbę żądań dozwolonych od klientów do aplikacji sieci Web, która zawiera */promo* w adresie URL przy użyciu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Limity szybkości są stosowane dla każdego adresu IP klienta. Jeśli masz wielu klientów uzyskujących dostęp do czołowych drzwi z różnych adresów IP, zostaną zastosowane własne limity szybkości.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad limitu szybkości Skonfiguruj środowisko programu PowerShell i Utwórz profil dla drzwi.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure, i zainstaluj polecenie AZ PowerShell module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Nawiązywanie połączenia z platformą Azure przy użyciu interaktywnego okna dialogowego logowania
```
Connect-AzAccount

```
Przed zainstalowaniem modułu front-drzwi upewnij się, że jest zainstalowana bieżąca wersja programu PowerShellGet. Uruchom następujące polecenie i Otwórz ponownie program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Zainstaluj AZ. Usługa frontdoor module 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Tworzenie profilu frontu drzwi
Utwórz profil z drzwiami wstępnymi, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Tworzenie profilu front-drzwi](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definiuj warunki dopasowania adresów URL
Zdefiniuj warunek dopasowania adresu URL (adres URL zawiera/promo) przy użyciu polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Poniższy przykład dopasowuje */promo* jako wartość zmiennej *RequestUri* :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Utwórz niestandardową regułę limitu szybkości
Ustaw limit szybkości przy użyciu polecenia [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). W poniższym przykładzie limit jest ustawiony na 1000. Żądania od dowolnego klienta do strony promocyjnej przekraczające 1000 w ciągu jednej minuty są blokowane do momentu rozpoczęcia następnej minuty.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdź nazwę grupy zasobów, która zawiera profil przedni drzwi przy użyciu `Get-AzureRmResourceGroup` . Następnie skonfiguruj zasady zabezpieczeń z niestandardową regułą limitu szybkości przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów, która zawiera profil drzwi przednich.

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](../../frontdoor/quickstart-create-front-door.md) artykułu przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Łączenie zasad z hostem frontonu przedniego
Połącz obiekt zasad zabezpieczeń z istniejącym hostem frontonu z przednim Drzwiem i zaktualizuj właściwości drzwi zewnętrznych. Najpierw Pobierz obiekt Front-drzwiczk przy użyciu polecenia [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Następnie ustaw właściwość *WebApplicationFirewallPolicyLink* frontonu na identyfikator *zasobu* "$ratePolicy" utworzony w poprzednim kroku przy użyciu polecenia [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* z założeniem, że profil przeddrzwi został utworzony przy użyciu instrukcji przedstawionych w [przewodniku szybki start: Tworzenie przedniego](../../frontdoor/quickstart-create-front-door.md) artykułu. Ponadto w poniższym przykładzie Zastąp $frontDoorName nazwą profilu od drzwi do przodu. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Musisz tylko ustawić właściwość *WebApplicationFirewallPolicyLink* , aby połączyć zasady zabezpieczeń z frontonem. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach zewnętrznych](../../frontdoor/front-door-overview.md). 


