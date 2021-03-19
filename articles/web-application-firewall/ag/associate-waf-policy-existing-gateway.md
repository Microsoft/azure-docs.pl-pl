---
title: Kojarzenie zasad zapory aplikacji sieci Web z istniejącym Application Gateway platformy Azure
description: Dowiedz się, jak skojarzyć zasady zapory aplikacji sieci Web z istniejącym Application Gateway platformy Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74083903"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Skojarz zasady WAF z istniejącym Application Gateway

Azure PowerShell można użyć do [utworzenia zasad WAFymi](create-waf-policy-ag.md), ale być może masz już Application Gateway i chcesz skojarzyć z nim zasady WAF. W tym artykule opisano, jak to zrobić. Utwórz zasady WAF i skojarz je z już istniejącym Application Gateway. 

1. Pobierz zasady Application Gateway i zapory. Jeśli nie masz istniejących zasad zapory, zobacz krok 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Obowiązkowe Utwórz zasady zapory.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Jeśli tworzysz zasady WAF w celu przejścia z konfiguracji WAF do zasad WAF, zasady muszą być dokładną kopią starej konfiguracji. Oznacza to, że każda wykluczenie, reguła niestandardowa, wyłączona Grupa reguł itd., musi być dokładnie taka sama jak w konfiguracji WAF.
3. Obowiązkowe Zasady WAF można skonfigurować zgodnie z potrzebami. Obejmuje to reguły niestandardowe, wyłączanie reguł/grup reguł, wykluczeń, Ustawianie limitów przekazywania plików itp. W przypadku pominięcia tego kroku zostaną zaznaczone wszystkie wartości domyślne. 
   
4. Zapisz zasady i Dołącz je do Application Gateway. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat reguł niestandardowych.](configure-waf-custom-rules.md)
