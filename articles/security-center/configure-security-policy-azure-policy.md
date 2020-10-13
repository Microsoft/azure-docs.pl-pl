---
title: Tworzenie i edytowanie zasad zabezpieczeń Azure Policy przy użyciu interfejsu API REST
description: Więcej informacji na temat Azure Policy zarządzania zasadami za pośrednictwem interfejsu API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fcfed585aece4fd57c085e0c9f6fdcd6cbeae74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448354"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń w Azure Policy przy użyciu interfejsu API REST

W ramach integracji natywnej z Azure Policy Azure Security Center umożliwia korzystanie z interfejsu API REST Azure Policy do tworzenia przypisań zasad. Poniższe instrukcje przeprowadzą Cię przez proces tworzenia przypisań zasad, a także dostosowywania istniejących przypisań. 

Ważne pojęcia dotyczące Azure Policy: 

- **Definicja zasad** jest regułą 

- **Inicjatywa** jest kolekcją definicji zasad (reguł) 

- **Przypisanie** jest aplikacją inicjatywy lub zasadami do określonego zakresu (grupy zarządzania, subskrypcji itp.) 

Security Center ma wbudowaną inicjatywę obejmującą wszystkie jej zasady zabezpieczeń. Aby ocenić zasady Security Center dla zasobów platformy Azure, należy utworzyć przypisanie do grupy zarządzania lub subskrypcję, którą chcesz ocenić.

Z wbudowaną inicjatywą są domyślnie włączone wszystkie zasady Security Center. Niektóre zasady można wyłączyć z poziomu wbudowanej inicjatywy. Na przykład, aby zastosować wszystkie zasady Security Center z wyjątkiem **zapory aplikacji sieci Web**, należy zmienić wartość parametru efektu zasad na **wyłączone**. 

## <a name="api-examples"></a>Przykłady dotyczące interfejsu API

W poniższych przykładach Zamień te zmienne:

- **{SCOPE}** wprowadź nazwę grupy zarządzania lub subskrypcji, do której są stosowane zasady.
- **{policyAssignmentName}** wprowadź [nazwę odpowiedniego przypisania zasad](#policy-names).
- **{name}** wprowadź swoją nazwę lub nazwę administratora, który zatwierdził zmianę zasad.

W tym przykładzie przedstawiono sposób przypisywania wbudowanej inicjatywy Security Center w ramach subskrypcji lub grupy zarządzania
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

W tym przykładzie przedstawiono sposób przypisywania wbudowanej inicjatywy Security Center w ramach subskrypcji, z wyłączeniem następujących zasad: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Ten przykład pokazuje, jak usunąć przypisanie:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Odwołania do nazw zasad <a name="policy-names"></a>

|Nazwa zasad w Security Center|Nazwa zasad wyświetlana w Azure Policy |Nazwa parametru efektu zasad|
|----|----|----|
|Szyfrowanie SQL |Monitorowanie nieszyfrowanej bazy danych SQL w Azure Security Center |sqlEncryptionMonitoringEffect| 
|Inspekcja SQL |Monitoruj niepoddaną inspekcję bazy danych SQL w Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizacje systemu |Monitoruj brakujące aktualizacje systemu w Azure Security Center |systemUpdatesMonitoringEffect|
|Szyfrowanie w usłudze Storage |Należy wykonać inspekcję pod kątem braku szyfrowania obiektów blob dla kont magazynu |storageEncryptionMonitoringEffect|
|Dostęp do sieci JIT |Monitoruj możliwy dostęp do sieci just-in-Time (JIT) w Azure Security Center |jitNetworkAccessMonitoringEffect |
|Funkcje adaptacyjnego sterowania aplikacjami |Monitorowanie dostępnych list dozwolonych aplikacji w Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupy zabezpieczeń sieci |Monitoruj dostęp do sieci w Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfiguracje zabezpieczeń |Monitoruj luki w zabezpieczeniach systemu operacyjnego w Azure Security Center |systemConfigurationsMonitoringEffect| 
|Program Endpoint Protection |Monitoruj brakujące Endpoint Protection w Azure Security Center |endpointProtectionMonitoringEffect |
|Szyfrowanie dysków |Monitoruj nieszyfrowane dyski maszyn wirtualnych w Azure Security Center |diskEncryptionMonitoringEffect|
|Ocena luk w zabezpieczeniach |Monitoruj luki w zabezpieczeniach maszyn wirtualnych w Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Zapora aplikacji internetowej |Monitoruj niechronione aplikacje sieci Web w Azure Security Center |webApplicationFirewallMonitoringEffect |
|Zapora nowej generacji |Monitoruj niechronione punkty końcowe sieci w Azure Security Center| |


## <a name="next-steps"></a>Następne kroki

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Niestandardowe zasady zabezpieczeń](custom-security-policies.md)
- [Przegląd zasad zabezpieczeń](tutorial-security-policy.md)