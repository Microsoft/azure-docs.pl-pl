---
title: Dołączanie do usługi Azure Security Center za pomocą programu PowerShell
description: Ten dokument zawiera informacje na temat procesu włączania Azure Security Center poleceń cmdlet programu PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: f9ab258f59279112d0b90c5d460e6761ac911a2d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713358"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatyzowanie dołączania Azure Security Center przy użyciu programu PowerShell

Obciążenia platformy Azure można zabezpieczyć programowo przy użyciu modułu Azure Security Center PowerShell.
Program PowerShell umożliwia automatyzację zadań i uniknięcie błędu ludzkiego związanego z zadaniami ręcznymi. Jest to szczególnie przydatne w przypadku wdrożeń na dużą skalę, które obejmują dziesiątki subskrypcji z setkami i tysiącami zasobów — wszystkie z nich muszą być zabezpieczone od samego początku.

Dołączanie Azure Security Center przy użyciu programu PowerShell umożliwia programowe automatyzowanie dołączania i zarządzania zasobami platformy Azure oraz dodawanie niezbędnych środków kontroli zabezpieczeń.

Ten artykuł zawiera przykładowy skrypt programu PowerShell, który można modyfikować i używać w środowisku w celu Security Center w ramach subskrypcji. 

W tym przykładzie włączymy usługę Security Center dla subskrypcji o identyfikatorze: d07c0080-170c-4c24-861d-9c817742786c i zastosujemy zalecane ustawienia, które zapewniają wysoki poziom ochrony, włączając usługę Azure Defender, która zapewnia zaawansowane możliwości ochrony przed zagrożeniami i wykrywania:

1. Włącz [Azure Defender](azure-defender.md). 
 
2. Ustaw obszar roboczy usługi Log Analytics, do którego agent usługi Log Analytics wyśle zebrane dane na maszyny wirtualne skojarzone z subskrypcją — w tym przykładzie jest to istniejący obszar roboczy zdefiniowany przez użytkownika (myWorkspace).

3. Aktywuj automatyczną aprowizowanie agenta usługi Security Center, [która wdraża agenta usługi Log Analytics.](security-center-enable-data-collection.md#auto-provision-mma)

5. Ustaw organizację CISO jako kontakt zabezpieczeń dla alertów Security Center [i notowalnych zdarzeń.](security-center-provide-security-contact-details.md)

6. Przypisz Security Center [domyślnych zasad zabezpieczeń programu](tutorial-security-policy.md).

## <a name="prerequisites"></a>Wymagania wstępne

Te kroki należy wykonać przed uruchomieniem Security Center cmdlet:

1. Uruchom program PowerShell jako administrator.

1. Uruchom następujące polecenia w programie PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Dołączanie Security Center przy użyciu programu PowerShell

1. Zarejestruj swoje subskrypcje u dostawcy Security Center zasobów:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Opcjonalnie: ustaw poziom pokrycia (Azure Defender wł./wył.) subskrypcji. W przypadku niezdefiniowania defender jest wyłączony:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Skonfiguruj obszar roboczy usługi Log Analytics, do którego będą raportować agenci. Musisz mieć już utworzony obszar roboczy usługi Log Analytics, który będzie raportować maszyny wirtualne subskrypcji. Możesz zdefiniować wiele subskrypcji, które będą raportować do tego samego obszaru roboczego. Jeśli nie zostanie zdefiniowany, zostanie użyty domyślny obszar roboczy.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Automatyczna instalacja agenta usługi Log Analytics na twoich maszyn wirtualnych platformy Azure:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Zaleca się włączenie automatycznego aprowizowania, aby upewnić się, że maszyny wirtualne platformy Azure są automatycznie chronione przez Azure Security Center.
    >

1. Opcjonalnie: zdecydowanie zaleca [](security-center-provide-security-contact-details.md) się zdefiniowanie szczegółów kontaktu ds. zabezpieczeń dla dołączanych subskrypcji, które będą używane jako odbiorcy alertów i powiadomień generowanych przez Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert```

1. Przypisz domyślną Security Center zasad:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Pomyślnie doszliśmy do programu Azure Security Center PowerShell.

Teraz możesz używać tych poleceń cmdlet programu PowerShell ze skryptami automatyzacji, aby programowo iterować po subskrypcjach i zasobach. Pozwala to zaoszczędzić czas i zmniejszyć prawdopodobieństwo wystąpienia błędu ludzkiego. Jako odwołania można [użyć tego](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) przykładowego skryptu.




## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat automatyzowania dołączania do usługi Security Center przy użyciu programu PowerShell, zobacz następujący artykuł:

* [Az.Security](/powershell/module/az.security)

Aby dowiedzieć się więcej na Security Center, zobacz następujący artykuł:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.