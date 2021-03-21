---
title: Zapisz ponownie nagłówki HTTP na platformie Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące ponownego zapisywania nagłówków HTTP w usłudze Azure Application Gateway przy użyciu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711692"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Ponowne zapisywanie nagłówków żądań i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — Azure PowerShell

W tym artykule opisano, jak za pomocą Azure PowerShell skonfigurować wystąpienie [jednostki SKU Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) w celu ponownego zapisania nagłówków HTTP w żądaniach i odpowiedziach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Aby wykonać kroki opisane w tym artykule, musisz uruchomić Azure PowerShell lokalnie. Musisz również mieć zainstalowany AZ module w wersji 1.0.0 lub nowszej. Uruchom `Import-Module Az` polecenie, a następnie `Get-Module Az` Określ zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Musisz mieć wystąpienie jednostki SKU Application Gateway v2. Ponowne zapisywanie nagłówków nie jest obsługiwane w jednostce SKU v1. Jeśli nie masz jednostki SKU w wersji 2, przed rozpoczęciem Utwórz wystąpienie [jednostki sku Application Gateway v2](./tutorial-autoscale-ps.md) .

## <a name="create-required-objects"></a>Tworzenie wymaganych obiektów

Aby skonfigurować ponowne zapisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty wymagane do ponownego zapisania nagłówka HTTP:

   - **RequestHeaderConfiguration**: służy do określania pól nagłówka żądania, które mają być ponownie zapisane, oraz do nowej wartości dla nagłówków.

   - **ResponseHeaderConfiguration**: służy do określania pól nagłówka odpowiedzi, które mają być ponownie zapisane, i nową wartością dla nagłówków.

   - **ActionSet**: zawiera konfiguracje żądań i nagłówków odpowiedzi określonych wcześniej.

   - **Warunek**: opcjonalna konfiguracja. Warunki ponownego zapisu sprawdzają zawartość żądań i odpowiedzi HTTP (S). Akcja ponownego zapisu zostanie wykonana, jeśli żądanie HTTP (S) lub odpowiedź pasuje do warunku ponownego zapisu.

     Jeśli powiążesz więcej niż jeden warunek z akcją, Akcja występuje tylko wtedy, gdy wszystkie warunki są spełnione. Innymi słowy, operacja jest operacją logiczną i.

   - **RewriteRule**: zawiera wielokrotne ponowne zapisywanie kombinacji warunku/ponownego zapisywania.

   - **RuleSequence**: opcjonalna konfiguracja, która pomaga określić kolejność wykonywania reguł ponownego zapisywania. Ta konfiguracja jest przydatna, jeśli masz wiele reguł ponownego zapisywania w zestawie do wielokrotnego zapisu. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli ta sama wartość sekwencji reguł zostanie przypisana do dwóch reguł ponownego zapisywania, kolejność wykonywania nie jest deterministyczna.

     Jeśli nie określisz jawnie RuleSequence, zostanie ustawiona wartość domyślna 100.

   - **RewriteRuleSet**: zawiera wiele reguł ponownego zapisywania, które zostaną skojarzone z regułą routingu żądania.

2. Dołącz RewriteRuleSet do reguły routingu. Konfiguracja ponownego zapisywania jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu, konfiguracja ponownego zapisywania nagłówka jest skojarzona z odbiornikiem źródłowym i jest ponownym zapisem nagłówka globalnego. W przypadku korzystania z reguły routingu opartej na ścieżce, konfiguracja ponownego zapisywania nagłówka jest definiowana na mapie ścieżki URL. W takim przypadku ma zastosowanie tylko do obszaru określonej ścieżki w lokacji.

Można utworzyć wiele zestawów wielokrotnego zapisu nagłówka HTTP i zastosować każdy ponowny zapis ustawiony dla wielu odbiorników. Można jednak zastosować tylko jeden wielokrotny zapis do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Określ konfigurację reguły ponownego zapisywania nagłówka HTTP

W tym przykładzie zmodyfikujemy adres URL przekierowania przez ponowne zapisanie nagłówka lokalizacji w odpowiedzi HTTP, gdy nagłówek lokalizacji zawiera odwołanie do azurewebsites.net. W tym celu dodamy warunek, aby sprawdzić, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Użyjemy wzorca `(https?)://.*azurewebsites.net(.*)$` . I będziemy używać `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartości nagłówka. Ta wartość spowoduje zamienienie *azurewebsites.NET* z *contoso.com* w nagłówku lokalizacji.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Pobierz konfigurację bramy aplikacji

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Pobierz konfigurację reguły routingu żądania

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizowanie bramy aplikacji przy użyciu konfiguracji zapisywania nagłówków HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Usuń regułę ponownego zapisu

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu niektórych typowych przypadków użycia, zobacz [typowe scenariusze ponownego zapisywania nagłówka](./rewrite-http-headers.md).