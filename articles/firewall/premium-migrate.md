---
title: Migrowanie do usługi Azure firewall Premium Preview
description: Dowiedz się, jak przeprowadzić migrację z usługi Azure firewall Standard do usługi Azure firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549857"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrowanie do usługi Azure firewall Premium Preview

Usługę Azure firewall standard można migrować do usługi Azure firewall Premium w wersji zapoznawczej, aby korzystać z nowych funkcji Premium. Aby uzyskać więcej informacji na temat funkcji usługi Azure firewall Premium w wersji zapoznawczej, zobacz [funkcje usługi Azure firewall Premium Preview](premium-features.md).

Poniższe dwa przykłady pokazują, jak:
- Migrowanie istniejących zasad standardowych przy użyciu Azure PowerShell
- Migrowanie istniejącej standardowej zapory (z regułami klasycznymi) do usługi Azure firewall Premium przy użyciu zasad Premium.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrowanie istniejących zasad przy użyciu Azure PowerShell

`Transform-Policy.ps1` jest skryptem Azure PowerShell, który tworzy nowe zasady Premium na podstawie istniejących zasad standardowych.

Przy użyciu standardowego identyfikatora zasad zapory skrypt przekształca go w zasady zapory platformy Azure w warstwie Premium. Skrypt najpierw nawiązuje połączenie z kontem platformy Azure, ściąga zasady, przekształca/dodaje różne parametry, a następnie przekazuje nowe zasady w warstwie Premium. Nowe zasady Premium mają nazwę `<previous_policy_name>_premium` .

Przykład użycia:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Skrypt nie migruje ustawień analizy zagrożeń. Przed kontynuowaniem i przeprowadzeniem migracji należy zwrócić uwagę na te ustawienia.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrowanie istniejącej zapory standardowej przy użyciu Azure Portal

Ten przykład pokazuje, jak używać Azure Portal do migrowania standardowej zapory (reguły klasyczne) do usługi Azure firewall Premium przy użyciu zasad Premium.

1. Na Azure Portal wybierz warstwę Standardowa. Na stronie **Przegląd** wybierz pozycję **Migruj do zasad zapory**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrowanie do zasad zapory":::

1. Na stronie **Migrowanie do zasad zapory** wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz przycisk **Utwórz**.

   Wdrożenie może potrwać kilka minut.
1. Użyj `Transform-Policy.ps1` [skryptu Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) , aby przekształcić te nowe zasady standardowe do zasad w warstwie Premium.
1. W portalu Wybierz standardowy zasób zapory. 
1. W obszarze **Automatyzacja** wybierz pozycję **Eksportuj szablon**. Pozostaw tę kartę przeglądarki otwartą. Powrócisz do niego później.
   > [!TIP]
   > Aby upewnić się, że nie utracisz szablonu, Pobierz i Zapisz go na wypadek, gdy karta przeglądarki zostanie zamknięta lub odświeżona.
1. Otwórz nową kartę przeglądarki, przejdź do Azure Portal i otwórz grupę zasobów zawierającą zaporę.
1. Usuń istniejące wystąpienie zapory standardowej.

   Wykonanie tej operacji może zająć kilka minut.

1. Wróć do karty przeglądarki z wyeksportowanym szablonem.
1. Wybierz pozycję **Wdróż**, a następnie na stronie **wdrożenie niestandardowe** wybierz pozycję **Edytuj szablon**.
1. Edytuj tekst szablonu:
   
   1. W obszarze `Microsoft.Network/azureFirewalls` zasób w obszarze `Properties` , `sku` Zmień wartość `tier` z "Standardowa" na "Premium".
   1. W obszarze szablonu `Parameters` Zmień `defaultValue` wartość na `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` :
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      na:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz przycisk **Utwórz**.

Po zakończeniu wdrażania można skonfigurować wszystkie nowe funkcje usługi Azure firewall Premium w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o funkcjach usługi Azure firewall Premium](premium-features.md)