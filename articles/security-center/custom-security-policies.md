---
title: Tworzenie niestandardowych zasad zabezpieczeń w Azure Security Center | Microsoft Docs
description: Definicje zasad niestandardowych platformy Azure monitorowane przez Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: db042a6112944e1c825177506782758d0a96b390
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448398"
---
# <a name="using-custom-security-policies"></a>Korzystanie z niestandardowych zasad zabezpieczeń

Aby pomóc w zabezpieczeniu systemów i środowiska, Azure Security Center generuje zalecenia dotyczące zabezpieczeń. Zalecenia te są oparte na najlepszych rozwiązaniach branżowych, które są włączone do ogólnych, domyślnych zasad zabezpieczeń dostarczonych wszystkim klientom. Mogą również pochodzić z Security Center znajomości standardów branżowych i prawnych.

Korzystając z tej funkcji, możesz dodać własne inicjatywy *niestandardowe* . Następnie otrzymasz zalecenia, jeśli środowisko nie będzie zgodne z tworzonymi zasadami. Wszelkie utworzone inicjatywy niestandardowe będą wyświetlane wraz z wbudowanymi inicjatywami na pulpicie nawigacyjnym zgodności z przepisami, zgodnie z opisem w samouczku [poprawa zgodności z przepisami](security-center-compliance-dashboard.md).

Zgodnie z opisem w [dokumentacji Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location), gdy określisz lokalizację dla inicjatywy niestandardowej, musi to być grupa zarządzania lub subskrypcja. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aby dodać inicjatywę niestandardową do subskrypcji 

1. Na pasku bocznym Security Center Otwórz stronę **zasady zabezpieczeń** .

1. Wybierz subskrypcję lub grupę zarządzania, do której chcesz dodać inicjatywę niestandardową.

    [![Wybieranie subskrypcji, dla której zostaną utworzone zasady niestandardowe](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Należy dodać niestandardowe standardy na poziomie subskrypcji (lub nowszym), aby były oceniane i wyświetlane w Security Center. 
    >
    > Dodanie niestandardowego standardu powoduje przypisanie *inicjatywy* do tego zakresu. Dlatego zalecamy wybranie najszerszego zakresu wymaganego dla tego przydziału.

1. Na stronie zasady zabezpieczeń w obszarze inicjatywy niestandardowe kliknij pozycję **Dodaj inicjatywę niestandardową**.

    [![Kliknij pozycję Dodaj inicjatywę niestandardową](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zostanie wyświetlona następująca strona:

    ![Tworzenie lub Dodawanie zasad](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stronie Dodawanie niestandardowych inicjatyw Przejrzyj listę zasad niestandardowych już utworzonych w organizacji. Jeśli zobaczysz, że chcesz ją przypisać do swojej subskrypcji, kliknij przycisk **Dodaj**. Jeśli na liście nie ma inicjatywy, która spełnia Twoje wymagania, Pomiń ten krok.

1. Aby utworzyć nową inicjatywę niestandardową:

    1. Kliknij przycisk **Utwórz nowy**.
    1. Wprowadź lokalizację i nazwę definicji.
    1. Wybierz zasady do uwzględnienia i kliknij przycisk **Dodaj**.
    1. Wprowadź wszelkie wymagane parametry.
    1. Kliknij pozycję **Zapisz**.
    1. Na stronie Dodaj niestandardowe inicjatywy kliknij przycisk Odśwież. Twoja nowa inicjatywa będzie wyświetlana jako dostępna.
    1. Kliknij pozycję **Dodaj** i przypisz ją do subskrypcji.

    > [!NOTE]
    > Tworzenie nowych inicjatyw wymaga poświadczeń właściciela subskrypcji. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [uprawnienia w Azure Security Center](security-center-permissions.md).

    Twoja nowa inicjatywa zacznie obowiązywać i zobaczysz wpływ na dwa sposoby:

    * Na pasku bocznym Security Center w obszarze zasady & zgodność wybierz pozycję **zgodność z przepisami**. Zostanie otwarty pulpit nawigacyjny zgodności pokazujący nową inicjatywę niestandardową wraz z wbudowaną inicjatywą.
    
    * Jeśli środowisko nie będzie zgodne ze zdefiniowanymi zasadami, zaczniesz otrzymywać zalecenia.

1. Aby zobaczyć, jakie są zalecenia dotyczące zasad, kliknij przycisk **zalecenia** na pasku bocznym, aby otworzyć stronę zalecenia. Zalecenia będą wyświetlane z etykietą "niestandardowy" i będą dostępne w ciągu około godziny.

    [![Zalecenia niestandardowe](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Ulepszanie niestandardowych zaleceń ze szczegółowymi informacjami

Wbudowane zalecenia dotyczące Azure Security Center zawierają szczegóły, takie jak poziomy ważności i instrukcje korygowania. Jeśli chcesz dodać ten typ informacji do niestandardowych zaleceń, tak aby pojawił się w Azure Portal lub wszędzie tam, gdzie będziesz uzyskiwać dostęp do zaleceń, musisz użyć interfejsu API REST. 

Dwa typy informacji, które można dodać, to:

- **RemediationDescription** — ciąg
- **Ważność** — Wyliczenie [niski, średni, wysoki]

Metadane należy dodać do definicji zasad dla zasad będących częścią inicjatywy niestandardowej. Powinien być we właściwości "securityCenter", jak pokazano poniżej:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

Poniżej znajduje się przykład zasad niestandardowych obejmujących Właściwość Metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High"
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

Inny przykład użycia właściwości securityCenter można znaleźć w [tej sekcji dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia niestandardowych zasad zabezpieczeń. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Omówienie zasad zabezpieczeń](tutorial-security-policy.md)
- [Lista wbudowanych zasad zabezpieczeń](security-center-policy-definitions.md)