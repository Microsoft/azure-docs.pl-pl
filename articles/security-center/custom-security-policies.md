---
title: Tworzenie niestandardowych zasad zabezpieczeń w Azure Security Center | Microsoft Docs
description: Niestandardowe definicje zasad platformy Azure monitorowane przez Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a41696ba92757550f9cbaa08ccf78d9a5da528d2
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718903"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Tworzenie niestandardowych inicjatyw i zasad zabezpieczeń

Aby ułatwić zabezpieczanie systemów i środowiska, Azure Security Center generuje zalecenia dotyczące zabezpieczeń. Te zalecenia są oparte na najlepszych rozwiązaniach branżowych, które są włączone do ogólnych, domyślnych zasad zabezpieczeń dostarczanych wszystkim klientom. Mogą również pochodzić z Security Center na temat standardów branżowych i prawnych.

Ta funkcja umożliwia dodawanie własnych inicjatyw *niestandardowych.* Następnie otrzymasz rekomendacje, jeśli środowisko nie jest zgodne z zasadami, które utworzysz. Wszystkie niestandardowe inicjatywy, które utworzysz, będą wyświetlane obok wbudowanych inicjatyw na pulpicie nawigacyjnym zgodności z przepisami, zgodnie z opisem w samouczku Improve your regulatory compliance (Ulepszanie [zgodności z przepisami).](security-center-compliance-dashboard.md)

Jak omówiono w [Azure Policy dokumentacji](../governance/policy/concepts/definition-structure.md#definition-location)programu , określenie lokalizacji dla inicjatywy niestandardowej musi być grupą zarządzania lub subskrypcją. 

> [!TIP]
> Aby uzyskać omówienie kluczowych pojęć na tej stronie, zobacz [What are security policies, initiatives, and recommendations?](security-policy-concept.md)(Co to są zasady zabezpieczeń, inicjatywy i zalecenia?).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aby dodać niestandardową inicjatywę do subskrypcji 

1. Na Security Center bocznym aplikacji otwórz stronę **Zasady** zabezpieczeń.

1. Wybierz subskrypcję lub grupę zarządzania, do której chcesz dodać inicjatywę niestandardową.

    [![Wybieranie subskrypcji, dla której utworzysz zasady niestandardowe](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Musisz dodać niestandardowe standardy na poziomie subskrypcji (lub wyższym), aby były one oceniane i wyświetlane w Security Center. 
    >
    > Dodanie niestandardowego standardu przypisuje *inicjatywę do* tego zakresu. Dlatego zalecamy wybranie najszerszego zakresu wymaganego dla tego przypisania.

1. Na stronie Zasady zabezpieczeń w obszarze Twoje niestandardowe inicjatywy kliknij pozycję **Dodaj inicjatywę niestandardową.**

    [![Kliknij pozycję Dodaj inicjatywę niestandardową](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zostanie wyświetlona następująca strona:

    ![Tworzenie lub dodawanie zasad](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stronie Dodawanie inicjatyw niestandardowych przejrzyj listę zasad niestandardowych utworzonych już w organizacji. Jeśli zostanie wyświetlony jeden z nich, który chcesz przypisać do subskrypcji, kliknij pozycję **Dodaj.** Jeśli na liście nie ma inicjatywy, która spełnia Twoje potrzeby, pomiń ten krok.

1. Aby utworzyć nową inicjatywę niestandardową:

    1. Kliknij **pozycję Utwórz nowy.**
    1. Wprowadź lokalizację i nazwę definicji.
    1. Wybierz zasady, które mają być dołączane, a następnie kliknij **pozycję Dodaj.**
    1. Wprowadź wszelkie żądane parametry.
    1. Kliknij pozycję **Zapisz**.
    1. Na stronie Dodawanie inicjatyw niestandardowych kliknij przycisk Odśwież. Nowa inicjatywa będzie wyświetlana jako dostępna.
    1. Kliknij **pozycję Dodaj** i przypisz ją do subskrypcji.

    > [!NOTE]
    > Tworzenie nowych inicjatyw wymaga poświadczeń właściciela subskrypcji. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [Uprawnienia w Azure Security Center](security-center-permissions.md).

    Twoja nowa inicjatywa zostanie w efekcie wywłaszczy i zobaczysz jej wpływ na następujące dwa sposoby:

    * Na pasku bocznym Security Center w obszarze Zgodność & zasadami wybierz pozycję **Zgodność z przepisami.** Zostanie otwarty pulpit nawigacyjny zgodności z nową inicjatywą niestandardową obok wbudowanych inicjatyw.
    
    * Zaczniesz otrzymywać rekomendacje, jeśli twoje środowisko nie jest zgodne ze zdefiniowanymi zasadami.

1. Aby wyświetlić wynikowe zalecenia dotyczące zasad, kliknij pozycję **Zalecenia** na pasku bocznym, aby otworzyć stronę zaleceń. Zalecenia zostaną wyświetlone z etykietą "Niestandardowa" i będą dostępne w ciągu około godziny.

    [![Zalecenia niestandardowe](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń w programie Azure Policy użyciu interfejsu API REST

W ramach natywnej integracji z usługą Azure Policy usługa Azure Security Center umożliwia korzystanie z interfejsu API REST usługi Azure Policy do tworzenia przypisań zasad. Poniższe instrukcje zawiera instrukcje tworzenia przypisań zasad, a także dostosowywania istniejących przypisań. 

Ważne pojęcia w Azure Policy: 

- Definicja **zasad jest** regułą 

- Inicjatywa **jest** kolekcją definicji zasad (reguł) 

- **Przypisanie** to zastosowanie inicjatywy lub zasad do określonego zakresu (grupy zarządzania, subskrypcji itp.) 

Security Center ma wbudowaną inicjatywę, [Test porównawczy zabezpieczeń](https://docs.microsoft.com/security/benchmark/azure/introduction)platformy Azure, która obejmuje wszystkie jego zasady zabezpieczeń. Aby ocenić Security Center zasobów platformy Azure, należy utworzyć przypisanie dla grupy zarządzania lub subskrypcji, którą chcesz ocenić.

Wbudowana inicjatywa ma domyślnie Security Center wszystkich zasad usługi. Niektóre zasady można wyłączyć z wbudowanej inicjatywy. Aby na przykład zastosować wszystkie zasady Security Center z wyjątkiem zapory aplikacji **internetowej,** zmień wartość parametru efektu zasad na **Wyłączone.**

## <a name="api-examples"></a>Przykłady dotyczące interfejsu API

W poniższych przykładach zastąp te zmienne:

- **{scope}** wprowadź nazwę grupy zarządzania lub subskrypcji, do której są stosowanie zasad
- **{policyAssignmentName}** wprowadź nazwę odpowiedniego przypisania zasad
- **{name}** wprowadź swoje imię i nazwisko lub nazwę administratora, który zatwierdził zmianę zasad

W tym przykładzie pokazano, jak przypisać wbudowaną inicjatywę Security Center subskrypcji lub grupy zarządzania
 
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

W tym przykładzie pokazano, jak przypisać wbudowaną inicjatywę Security Center subskrypcji z wyłączonymi następującymi zasadami: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Ochrona punktu końcowego ("endpointProtectionMonitoringEffect") 

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
W tym przykładzie pokazano, jak usunąć przypisanie:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Ulepszanie rekomendacji niestandardowych dzięki szczegółowym informacjom

Wbudowane zalecenia dostarczane z usługą Azure Security Center zawierają szczegółowe informacje, takie jak poziomy ważności i instrukcje korygowania. Jeśli chcesz dodać ten typ informacji do rekomendacji niestandardowych, aby pojawiały się w obszarze Azure Portal lub wszędzie tam, gdzie uzyskujesz dostęp do zaleceń, musisz użyć interfejsu API REST. 

Istnieją dwa typy informacji, które można dodać:

- **RemediationDescription** — ciąg
- **Ważność** — wyli wcześniejsza [niska, średnia, wysoka]

Metadane należy dodać do definicji zasad dla zasad, które są częścią niestandardowej inicjatywy. Powinien on być we właściwości "securityCenter", jak pokazano poniżej:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Poniżej przedstawiono przykład zasad niestandardowych, w tym właściwość metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Inny przykład użycia właściwości securityCenter można znaleźć w [tej sekcji dokumentacji interfejsu API REST](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób tworzenia niestandardowych zasad zabezpieczeń. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Omówienie zasad zabezpieczeń](tutorial-security-policy.md)
- [Lista wbudowanych zasad zabezpieczeń](./policy-reference.md)