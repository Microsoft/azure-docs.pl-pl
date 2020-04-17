---
title: Migrowanie zasad WAF dla bramy aplikacji platformy Azure
description: Dowiedz się, jak przeprowadzić migrację zasad Zapory aplikacji sieci Web platformy Azure przy użyciu programu Azure PowerShell.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 04/16/2020
ms.author: ant
ms.openlocfilehash: fb3b922b753b9696aa26ea189597589ecc5772db
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536628"
---
# <a name="migrate-web-application-firewall-policies-using-azure-powershell"></a>Migrowanie zasad zapory aplikacji sieci Web przy użyciu programu Azure PowerShell

Ten skrypt ułatwia przejście z konfiguracji WAF lub niestandardowych zasad WAF tylko do pełnej zasady WAF. W portalu może pojawić się ostrzeżenie, które mówi, że *migracja do zasad WAF*lub możesz chcieć nowych publicznych funkcji WAF w wersji zapoznawczej, takich jak reguły niestandardowe Geomatch, zasady WAF dla witryny i identyfikatorów URI lub znaczniki ryzyka bota. Aby korzystać z dowolnej z tych funkcji, należy pełne zasady WAF skojarzone z bramą aplikacji. 

Aby uzyskać więcej informacji na temat tworzenia nowych zasad WAF, zobacz [Tworzenie zasad zapory aplikacji sieci Web dla bramy aplikacji](create-waf-policy-ag.md). Aby uzyskać informacje dotyczące migracji, zobacz [Migracja do zasad WAF](create-waf-policy-ag.md#migrate-to-waf-policy).

## <a name="to-migrate-to-waf-policy-using-the-migration-script"></a>Aby przeprowadzić migrację do zasad WAF przy użyciu skryptu migracji

Aby uruchomić skrypt migracji, użyj następujących kroków: 

1. Otwórz następujące okno powłoki chmury lub otwórz je z poziomu portalu.
2. Skopiuj skrypt do okna powłoki chmury i uruchom go.
3. Skrypt prosi o identyfikator subskrypcji, nazwę grupy zasobów, nazwę bramy aplikacji, z którą jest skojarzony config WAF, oraz nazwę nowej zasady WAF, która ma być tworzona. Po wprowadzeniu tych danych wejściowych skrypt uruchamia się i tworzy nowe zasady WAF
4. Skojarz nowe zasady WAF z bramą aplikacji. Przejdź do zasad WAF w portalu i wybierz kartę **Skojarzone bramy aplikacji.** Wybierz pozycję **Skojarz bramę aplikacji,** a następnie wybierz bramę aplikacji, z nią chcesz skojarzyć zasady WAF.

> [!NOTE]
> Skrypt nie kończy migracji, jeśli istnieją następujące warunki:
> - Cała reguła jest wyłączona. Aby ukończyć migrację, upewnij się, że cała grupa reguł nie jest wyłączona.
> - Wpis(-y) wykluczenia z *operatorem Equals.* Aby zakończyć migrację, upewnij się, że wpisy wykluczeń z *operatorem Equals Any* nie są obecne.
>
> Aby uzyskać więcej informacji, zobacz *ValidateInput* funkcji w skrypcie.

```azurepowershell-interactive
<#PSScriptInfo
.DESCRIPTION
Will be used to migrate to the application-gateway to a top level waf policy experience.

.VERSION 1.0

.GUID b6fedd43-ebd0-41ed-9847-4f1c1c43be22

.AUTHOR Venkat.Krishnan

.PARAMETER subscriptionId 
Subscription Id of where the resources are present.
.PARAMETER resourceGroupName
Resource-group where the resources are present.
.PARAMETER applicationGatewayName
Application-Gateway name
.PARAMETER wafPolicyName
Name of the web application firewall policy

.EXAMPLE
./migrateToWafPolicy.ps1 -subscriptionId  <your-subscription-id> -applicationGatewayName <your-appgw-name> -resourceGroupName <your-resource-group-name> -wafPolicyName <new-waf-policy-name>
#>

param(
    [Parameter(Mandatory=$true)]
    [string] $subscriptionId,
    [Parameter(Mandatory=$true)]
    [string] $resourceGroupName,
    [Parameter(Mandatory=$true)]
    [string] $applicationGatewayName,
    [Parameter(Mandatory=$true)]
    [string] $wafPolicyName
)

function ValidateInput ($appgwName, $resourceGroupName) {
    # Obtain the application-gateway
    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName
    if (-not $appgw) {
        Write-Error "ApplicationGateway: $applicationGatewayName is not present in ResourceGroup: $resourceGroupName"
        return $false
    }

    # Check if already have a global firewall policy
    if ($appgw.FirewallPolicy) {
        $fp = Get-AzResource -ResourceId $appgw.FirewallPolicy.Id
        if ($fp.PolicySettings) {
            Write-Error "ApplicationGateway: $applicationGatewayName already has a global firewall policy: $fp.Name. Please use portal for changing the policy."
            return $false
        }
    }

    if ($appgw.WebApplicationFirewallConfiguration) {
        # Throw an error, since ruleGroup disabled case can't be migrated now.
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                if ($disabled.Rules.Count -eq 0) {
                    $ruleGroupName = $disabled.RuleGroupName
                    Write-Error "The ruleGroup '$ruleGroupName' is disabled. Currently we can't migrate to a firewall policy when an entire ruleGroup is disabled. This feature will be delivered shortly. To continue, kindly ensure the entire rulegroups are not disabled. "
                    return $false
                }
            }
        }

        # Throw an error when exclusion entry with 'EqualsAny' operator is present
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($null -ne $excl.MatchVariable -and $null -eq $excl.SelectorMatchOperator -and $null -eq $excl.Selector) {
                    Write-Error " You have an exclusion entry(s) with the 'Equals any' operator. Currently we can't migrate to a firewall policy with 'Equals Any' operator. This feature will be delivered shortly. To continue, kindly ensure exclusion entries with 'Equals Any' operator is not present. "
                    return $false
                }
            }
        }
    }

    if ($appgw.Sku.Name -ne "WAF_v2" -or $appgw.Sku.Tier -ne "WAF_v2") {
        Write-Error " Cannot associate a firewall policy to application gateway :$applicationGatewayName since the Sku is not on WAF_v2"
        return $false
    }

    return $true
}

function Login() {
    $context = Get-AzContext
    if ($null -eq $context -or $null -eq $context.Account) {
        Login-AzAccount
    }
}

function createNewTopLevelWafPolicy ($subscriptionId, $resourceGroupName, $applicationGatewayName, $wafPolicyName) {
    Select-AzSubscription -Subscription $subscriptionId
    $retVal = ValidateInput -appgwName $applicationGatewayName -resourceGroupName $resourceGroupName
    if (!$retVal) {
        return
    }

    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName

    # Get the managedRule and PolicySettings
    $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule
    $policySetting = New-AzApplicationGatewayFirewallPolicySetting
    if ($appgw.WebApplicationFirewallConfiguration) {
        $ruleGroupOverrides = [System.Collections.ArrayList]@()
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                $rules = [System.Collections.ArrayList]@()
                if ($disabled.Rules.Count -gt 0) {
                    foreach ($rule in $disabled.Rules) {
                        $ruleOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleOverride -RuleId $rule
                        $_ = $rules.Add($ruleOverride)              
                    }
                }
                
                $ruleGroupOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleGroupOverride -RuleGroupName $disabled.RuleGroupName -Rule $rules
                $_ = $ruleGroupOverrides.Add($ruleGroupOverride)
            }
        }

        $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion 
        if ($ruleGroupOverrides.Count -ne 0) {
            $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion -RuleGroupOverride $ruleGroupOverrides 
        }
    
        $exclusions = [System.Collections.ArrayList]@()  
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($excl.MatchVariable -and $excl.SelectorMatchOperator -and $excl.Selector) {
                    $exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion -MatchVariable  $excl.MatchVariable -SelectorMatchOperator $excl.SelectorMatchOperator -Selector $excl.Selector
                    $_ = $exclusions.Add($exclusionEntry)               
                }
            }
        }
    
        $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet
        $exclCount = $exclusions.Count
        if ($exclCount -ne 0) {
            $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet -Exclusion $exclusions
        }

        
        $policySetting = New-AzApplicationGatewayFirewallPolicySetting -MaxFileUploadInMb $appgw.WebApplicationFirewallConfiguration.FileUploadLimitInMb -MaxRequestBodySizeInKb $appgw.WebApplicationFirewallConfiguration.MaxRequestBodySizeInKb -Mode Detection -State Disabled
        if ($appgw.WebApplicationFirewallConfiguration.FirewallMode -eq "Prevention") {
            $policySetting.Mode = "Prevention"
        }

        if ($appgw.WebApplicationFirewallConfiguration.Enabled) {
            $policySetting.State = "Enabled"
        }

        $policySetting.RequestBodyCheck = $appgw.WebApplicationFirewallConfiguration.RequestBodyCheck;
    }

    if ($appgw.FirewallPolicy) {
        $customRulePolicyId = $appgw.FirewallPolicy.Id
        $rg = Get-AzResourceGroup -Id $customRulePolicyId
        $crPolicyName = $customRulePolicyId.Substring($customRulePolicyId.LastIndexOf("/") + 1)
        $customRulePolicy = Get-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $crPolicyName
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $wafPolicyName -CustomRule $customRulePolicy.CustomRules -ManagedRule $managedRule -PolicySetting $policySetting -Location $appgw.Location
    } else { 
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name $wafPolicyName -ResourceGroupName $resourceGroupName -PolicySetting $policySetting -ManagedRule $managedRule -Location $appgw.Location
    }

    if (!$wafPolicy) {
        return
    }

    $appgw.FirewallPolicy = $wafPolicy
    $appgw = Set-AzApplicationGateway -ApplicationGateway $appgw
    Write-Host " firewallPolicy: $wafPolicyName has been created/updated successfully and applied to applicationGateway: $applicationGatewayName!"
    return $wafPolicy
}

function Main() {
    Login
    $policy = createNewTopLevelWafPolicy -subscriptionId $subscriptionId -resourceGroupName $resourceGroupName -applicationGatewayName $applicationGatewayName -wafPolicyName $wafPolicyName
    return $policy
}

Main
```
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [grupach i regułach crs zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).
