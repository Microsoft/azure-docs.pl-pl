---
title: Dołączanie do Azure Security Center za pomocą programu PowerShell
description: Ten dokument przeprowadzi Cię przez proces włączania Azure Security Center przy użyciu poleceń cmdlet programu PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 25639509cfb689e025ea4388f302378648af8ba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439497"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatyzowanie dołączania Azure Security Center przy użyciu programu PowerShell

Obciążenia platformy Azure można zabezpieczyć programowo przy użyciu modułu Azure Security Center PowerShell.
Za pomocą programu PowerShell można zautomatyzować zadania i uniknąć błędu ludzkiego związanego z ręcznymi zadaniami. Jest to szczególnie przydatne w przypadku wdrożeń na dużą skalę, które obejmują dziesiątki subskrypcji z setkami i tysiącami zasobów — wszystkie te, które muszą być zabezpieczone od początku.

Azure Security Center dołączania przy użyciu programu PowerShell umożliwiają programowe Automatyzowanie dołączania i zarządzania zasobami platformy Azure oraz dodawanie niezbędnych kontroli zabezpieczeń.

Ten artykuł zawiera przykładowy skrypt programu PowerShell, który można zmodyfikować i użyć w środowisku w celu wdrożenia Security Center w ramach subskrypcji. 

W tym przykładzie zostanie włączona Security Center w ramach subskrypcji o IDENTYFIKATORze: d07c0080-170c-4c24-861d-9c817742786c i zastosowaniu zalecanych ustawień, które zapewniają wysoki poziom ochrony, włączając usługę Azure Defender, która zapewnia zaawansowane funkcje ochrony przed zagrożeniami i wykrywania:

1. Włącz [usługę Azure Defender](azure-defender.md). 
 
2. Ustaw obszar roboczy Log Analytics, do którego Agent Log Analytics wyśle dane zbierane na maszynach wirtualnych skojarzonych z subskrypcją — w tym przykładzie istniejący zdefiniowany przez użytkownika obszar roboczy (mój obszar roboczy).

3. Aktywuj Security Center automatyczną aprowizacji agentów, która [wdraża agenta log Analytics](security-center-enable-data-collection.md#auto-provision-mma).

5. Ustaw [CISO organizacji jako kontakt z zabezpieczeniami dla alertów Security Center i istotnych zdarzeń](security-center-provide-security-contact-details.md).

6. Przypisz [domyślne zasady zabezpieczeń](tutorial-security-policy.md)Security Center.

## <a name="prerequisites"></a>Wymagania wstępne

Te kroki należy wykonać przed uruchomieniem Security Center poleceń cmdlet:

1. Uruchom program PowerShell jako administrator.

1. Uruchom następujące polecenia w programie PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Dołączanie Security Center przy użyciu programu PowerShell

1. Zarejestruj subskrypcje dla dostawcy zasobów Security Center:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Opcjonalnie: Ustaw poziom pokrycia (usługa Azure Defender włączona/wyłączona) dla subskrypcji. Jeśli undefined, Defender jest wyłączony:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Skonfiguruj obszar roboczy Log Analytics, do którego będą zgłaszane agenci. Musisz mieć już utworzony obszar roboczy Log Analytics, do którego będą raportowane maszyny wirtualne subskrypcji. Można zdefiniować wiele subskrypcji do raportowania w tym samym obszarze roboczym. Jeśli nie zostanie zdefiniowany, zostanie użyty domyślny obszar roboczy.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Zainicjuj obsługę administracyjną agenta Log Analytics na maszynach wirtualnych platformy Azure:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Zaleca się włączenie automatycznej obsługi administracyjnej, aby upewnić się, że maszyny wirtualne platformy Azure są automatycznie chronione przez Azure Security Center.
    >

1. Opcjonalne: zdecydowanie zaleca się zdefiniowanie szczegółowych informacji o kontakcie z zabezpieczeniami dla subskrypcji, które zostaną użyte jako adresaci alertów i powiadomień generowanych przez Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Przypisz domyślną inicjatywę zasad Security Center:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Pomyślnie dołączono Azure Security Center za pomocą programu PowerShell.

Teraz można używać tych poleceń cmdlet programu PowerShell ze skryptami automatyzacji do programistycznego iteracji między subskrypcjami i zasobami. Pozwala to zaoszczędzić czas i zmniejsza prawdopodobieństwo wystąpienia błędu przez człowieka. Tego [przykładowego skryptu](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) można użyć jako odwołania.




## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat sposobu automatyzowania dołączania do Security Center za pomocą programu PowerShell, zobacz następujący artykuł:

* [AZ. Security](/powershell/module/az.security)

Aby dowiedzieć się więcej na temat Security Center, zobacz następujący artykuł:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.