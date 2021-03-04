---
title: Dołączanie grupy zarządzania do Azure Security Center
description: Dowiedz się, jak za pomocą podanej definicji Azure Policy włączyć Azure Security Center dla wszystkich subskrypcji w grupie zarządzania.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 9db276425d510316c41126f8e942a1e2a362ea35
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101294"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Włącz Security Center we wszystkich subskrypcjach w grupie zarządzania

Za pomocą Azure Policy można włączyć Azure Security Center wszystkich subskrypcji platformy Azure w ramach tej samej grupy zarządzania (MG). Jest to wygodniejsze niż uzyskiwanie dostępu do nich indywidualnie z portalu i działa nawet wtedy, gdy subskrypcje należą do różnych właścicieli. 

Aby dołączyć grupę zarządzania i wszystkie jej subskrypcje:

1. Jako użytkownik z uprawnieniami **administratora zabezpieczeń** Otwórz Azure Policy i Wyszukaj definicję **Włącz Azure Security Center w subskrypcji**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Definicja Azure Policy włączania Azure Security Center w subskrypcji":::

1. Wybierz pozycję **Przypisz** i upewnij się, że ustawisz zakres na poziomie mg.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Przypisywanie definicji Włącz Azure Security Center w ramach subskrypcji":::

    > [!TIP]
    > Poza zakresem nie ma wymaganych parametrów.

1. Wybierz pozycję **Utwórz zadanie korygowania** , aby upewnić się, że wszystkie istniejące subskrypcje, dla których nie włączono Security Center, zostaną dołączone.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Tworzenie zadania korygowania definicji Azure Policy Włącz Azure Security Center w subskrypcji":::

1. Po przypisaniu definicji:

    1. Wykryj wszystkie subskrypcje w MILIGRAMach, które nie zostały jeszcze zarejestrowane w Security Center.
    1. Oznacz te subskrypcje jako "niezgodne".
    1. Oznacz jako "zgodne" wszystkie zarejestrowane subskrypcje (niezależnie od tego, czy są one dostępne w usłudze Azure Defender, czy wyłączone).

    Następnie zadanie korygujące umożliwi Security Center bezpłatnie na niezgodnych subskrypcjach.

> [!IMPORTANT]
> Definicja zasad spowoduje włączenie tylko Security Center **istniejących** subskrypcji. Aby zarejestrować nowo utworzone subskrypcje, Otwórz kartę zgodność, wybierz odpowiednie niezgodne subskrypcje i Utwórz zadanie korygowania. Powtórz ten krok, jeśli masz co najmniej jedną nową subskrypcję, którą chcesz monitorować za pomocą Security Center.

## <a name="optional-modifications"></a>Opcjonalne modyfikacje

Istnieje wiele różnych sposobów modyfikowania definicji Azure Policy: 

- **Definiowanie zgodności w różny sposób** — podane zasady klasyfikują wszystkie subskrypcje w mg, które nie zostały jeszcze zarejestrowane w Security Center jako "niezgodne". Możesz zdecydować się na ustawienie wszystkich subskrypcji bez usługi Azure Defender.

    Podana Definicja definiuje *jedno* z poniższych ustawień "cennika" jako zgodne. Oznacza to, że subskrypcja jest zgodna z ustawieniem "Standardowa" lub "Free".

    > [!TIP]
    > Po włączeniu planu usługi Azure Defender jest on opisany w definicji zasad w ramach ustawienia "standardowe". Gdy jest wyłączona, jest "bezpłatna". Aby dowiedzieć się więcej o różnicach między tymi planami, zobacz [Security Center bezpłatnie a usługa Azure Defender włączona](security-center-pricing.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Jeśli zmienisz ją na następujące, tylko subskrypcje ustawione na wartość "Standardowa" byłyby klasyfikowane jako zgodne:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Zdefiniuj niektóre plany usługi Azure Defender do zastosowania podczas włączania Security Center** -podane zasady umożliwiają Security Center bez żadnych opcjonalnych planów usługi Azure Defender. Możesz włączyć co najmniej jedną z nich.

    Sekcja dostarczonej definicji `deployment` zawiera parametr `pricingTier` . Domyślnie jest to ustawienie `free` , ale można je zmodyfikować. 


## <a name="next-steps"></a>Następne kroki:

Teraz, po dodaniu całej grupy zarządzania, Włącz zaawansowaną ochronę usługi Azure Defender. 

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](enable-azure-defender.md)
