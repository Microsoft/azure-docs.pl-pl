---
title: Zrozumienie kolejności kolejności wdrażania
description: Informacje o domyślnej kolejności, w której artefakty są wdrażane w trakcie przypisywania strategii i dostosowywania kolejności wdrażania.
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: 91e11f8127ba2532ad48362de1689f4be2b6f935
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864525"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Zrozumienie sekwencji wdrożenia w planach platformy Azure

Plany platformy Azure używają **kolejności sekwencjonowania** w celu określenia kolejności tworzenia zasobów podczas przetwarzania przypisania definicji planu. W tym artykule wyjaśniono następujące pojęcia:

- Domyślna kolejność sekwencjonowania, która jest używana
- Jak dostosować kolejność
- Jak jest przetwarzane zamówienie dostosowane

Przykłady JSON, które należy zastąpić własnymi wartościami, są zmienne:

- `{YourMG}` — zastąp nazwą swojej grupy zarządzania

## <a name="default-sequencing-order"></a>Domyślna kolejność sekwencjonowania

Jeśli definicja planu nie zawiera dyrektywy dotyczącej kolejności wdrażania artefaktów lub dyrektywa ma wartość null, zostanie użyta następująca kolejność:

- Artefakty **przypisania roli** poziomu subskrypcji posortowane według nazwy artefaktu
- Artefakty **przydziału zasad** na poziomie subskrypcji posortowane według nazwy artefaktu
- Artefakty **szablonu Azure Resource Manager** poziomu subskrypcji posortowane według nazwy artefaktu
- Artefakty **grupy zasobów** (w tym artefakty podrzędne) posortowane według nazwy symbolu zastępczego

W ramach każdego artefaktu **grupy zasobów** następująca kolejność sekwencji jest używana w przypadku artefaktów, które mają zostać utworzone w ramach tej grupy zasobów:

- Artefakty **przypisania roli** podrzędnej grupy zasobów posortowane według nazwy artefaktu
- Artefakty **przydziału zasad** podrzędnych grupy zasobów posortowane według nazwy artefaktu
- Artefakty **szablonu Azure Resource Manager** podrzędnego grupy zasobów posortowane według nazwy artefaktu

> [!NOTE]
> Użycie [artefaktów ()](../reference/blueprint-functions.md#artifacts) tworzy niejawną zależność od artefaktu, do którego się odwołuje.

## <a name="customizing-the-sequencing-order"></a>Dostosowywanie kolejności sekwencjonowania

Podczas redagowania dużych definicji planów może być konieczne utworzenie zasobów w określonej kolejności. Najbardziej typowym wzorcem użycia tego scenariusza jest, gdy definicja strategii zawiera kilka Azure Resource Manager szablonów. Plany platformy Azure obsługują ten wzorzec, umożliwiając zdefiniowanie kolejności sekwencjonowania.

Kolejność jest realizowana przez zdefiniowanie `dependsOn` właściwości w formacie JSON. Ta właściwość obsługuje definicje strategii, dla grup zasobów i obiektów artefaktów. `dependsOn`jest tablicą ciągów nazw artefaktów, które należy utworzyć przed utworzeniem określonego artefaktu.

> [!NOTE]
> Podczas tworzenia obiektów strategii każdy zasób artefaktów pobiera swoją nazwę z nazwy pliku, jeśli jest używany program [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact), lub punkt końcowy adresu URL, jeśli używany jest [interfejs API REST](/rest/api/blueprints/artifacts/createorupdate). odwołania do odwołań do _zasobów_ w artefaktach muszą być zgodne z tymi zdefiniowanymi w definicji strategii.

### <a name="example---ordered-resource-group"></a>Przykładowa Grupa zasobów uporządkowana

Ta przykładowa definicja planu ma grupę zasobów, która definiuje niestandardową kolejność sekwencjonowania, deklarując wartość `dependsOn`dla, wraz z grupą zasobów w warstwie Standardowa. W takim przypadku artefakt o nazwie **assignPolicyTags** zostanie przetworzony przed grupą zasobów Order **-RG** .
**Standard-RG** będzie przetwarzany według domyślnej kolejności sekwencjonowania.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Przykład — artefakt z kolejnością niestandardową

Ten przykład jest artefaktem zasad, który zależy od szablonu Azure Resource Manager. Domyślnie podczas określania kolejności zostanie utworzony artefakt zasad przed szablonem Azure Resource Manager. To porządkowanie umożliwia artefaktowi zasad oczekiwanie na utworzenie szablonu Azure Resource Manager.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Przykład — artefakt szablonu poziomu subskrypcji w zależności od grupy zasobów

Ten przykład dotyczy szablonu Menedżer zasobów wdrożonego na poziomie subskrypcji, aby zależał od grupy zasobów. W kolejności domyślnej, artefakty poziomu subskrypcji zostaną utworzone przed dowolnymi grupami zasobów i artefaktami podrzędnymi w tych grupach zasobów. Grupa zasobów jest zdefiniowana w definicji strategii podobnej do:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Artefakt szablonu poziomu subskrypcji w zależności od grupy zasobów **oczekiwanie** na wykonanie jest definiowany następująco:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Przetwarzanie dostosowanej sekwencji

Podczas procesu tworzenia, sortowanie topologiczny jest używane do tworzenia wykresu zależności artefaktów. Sprawdza, czy każdy poziom zależności między grupami zasobów i artefaktami jest obsługiwany.

Jeśli zależność artefaktu jest zadeklarowana, która nie zmienia kolejności domyślnej, nie zostanie wprowadzona żadna zmiana.
Przykładem jest Grupa zasobów, która zależy od zasad na poziomie subskrypcji. Innym przykładem jest przypisanie zasad podrzędnych grupy zasobów "Standard-RG", które zależy od przypisania roli podrzędnej grupy zasobów "Standard-RG". W obu przypadkach `dependsOn` nie mógł zmienić domyślnej kolejności sekwencjonowania i żadne zmiany nie zostaną wprowadzone.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje na temat [cyklu życia strategii](lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](parameters.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
- Rozwiązywanie problemów podczas przypisywania strategii za pomocą [ogólnych procedur rozwiązywania problemów](../troubleshoot/general.md).