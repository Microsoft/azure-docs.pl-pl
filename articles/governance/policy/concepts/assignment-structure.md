---
title: Szczegóły struktury przypisania zasad
description: Zawiera opis definicji przypisania zasad używanej przez Azure Policy do powiązania definicji zasad i parametrów z zasobami do oceny.
ms.date: 03/17/2021
ms.topic: conceptual
ms.openlocfilehash: 909c1c361e092c512a73854a40e22a67efe5f2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604869"
---
# <a name="azure-policy-assignment-structure"></a>Struktura przypisań usługi Azure Policy

Przypisania zasad są używane przez Azure Policy do definiowania zasobów, które są przypisane do zasad lub inicjatyw. Przypisanie zasad pozwala określić wartości parametrów dla tej grupy zasobów w czasie przypisywania, dzięki czemu można ponownie użyć definicji zasad, które odnoszą się do tych samych właściwości zasobów z różnymi potrzebami zgodności.

Aby utworzyć przypisanie zasad, należy użyć formatu JSON. Przypisanie zasad zawiera elementy dla:

- Nazwa wyświetlana
- description (opis)
- metadane
- Tryb wymuszania
- wykluczone zakresy
- Definicja zasad
- komunikaty o niezgodności
- parameters

Na przykład poniższy kod JSON przedstawia przypisanie zasad w trybie _DoNotEnforce_ z parametrami dynamicznymi:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Wszystkie przykłady Azure Policy znajdują się na [Azure Policy próbkach](../samples/index.md).

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować przypisanie zasad i zapewnić kontekst do użycia z określonym zestawem zasobów. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

## <a name="enforcement-mode"></a>Tryb wymuszania

Właściwość **wymuszmode** zapewnia klientom możliwość testowania wyniku zasad w istniejących zasobach bez zainicjowania efektu zasad ani wyzwalania wpisów w [dzienniku aktywności platformy Azure](../../../azure-monitor/essentials/platform-logs-overview.md). Ten scenariusz jest często określany jako "What If" i wyrównany do bezpiecznych praktyk wdrażania. **wymuszanie** różni się od [wyłączonego](./effects.md#disabled) efektu, ponieważ ten efekt uniemożliwia wykonywanie oceny zasobów.

Ta właściwość ma następujące wartości:

|Tryb |Wartość JSON |Typ |Koryguj ręcznie |Wpis dziennika aktywności |Opis |
|-|-|-|-|-|-|
|Enabled (Włączony) |Domyślne |ciąg |Tak |Tak |Efekt zasad jest wymuszany podczas tworzenia lub aktualizowania zasobu. |
|Disabled |DoNotEnforce |ciąg |Tak |Nie | Efekt zasad nie jest wymuszany podczas tworzenia lub aktualizowania zasobu. |

Jeśli w definicji zasad lub inicjatywy nie określono **wymuszania** , używana jest wartość _Domyślna_ . [Zadania korygowania](../how-to/remediate-resources.md) można uruchamiać dla zasad [deployIfNotExists](./effects.md#deployifnotexists) , nawet jeśli ustawienie **wymuszania** ma wartość _DoNotEnforce_.

## <a name="excluded-scopes"></a>Wykluczone zakresy

**Zakres** przypisania obejmuje wszystkie podrzędne kontenery zasobów i zasoby podrzędne. Jeśli podrzędny kontener zasobów lub zasób podrzędny nie powinien mieć zastosowanej definicji, każdy z nich może zostać _wykluczony_ przez ustawienie **notScopes**. Ta właściwość jest tablicą, która umożliwia wykluczenie co najmniej jednego kontenera zasobów lub zasobów z oceny. **notScopes** można dodać lub zaktualizować po utworzeniu przypisania początkowego.

> [!NOTE]
> _Wykluczony_ zasób różni się od _zwolnionego_ zasobu. Aby uzyskać więcej informacji, zobacz [Opis zakresu w Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>Identyfikator definicji zasad

To pole musi zawierać pełną nazwę ścieżki definicji zasad lub definicji inicjatywy.
`policyDefinitionId` jest ciągiem, a nie tablicą. Zaleca się, aby w zamian była często przypisywanych wielu zasad w celu użycia [inicjatywy](./initiative-definition-structure.md) .

## <a name="non-compliance-messages"></a>Komunikaty o niezgodności

Aby ustawić niestandardowy komunikat opisujący, dlaczego zasób nie jest zgodny z definicją zasad lub inicjatywy, ustaw `nonComplianceMessages` w definicji przypisania. Ten węzeł jest tablicą `message` wpisów. Ten komunikat niestandardowy jest uzupełnieniem domyślnego komunikatu o błędzie dla niezgodności i jest opcjonalny.

> [!IMPORTANT]
> Komunikaty niestandardowe dla niezgodności są obsługiwane tylko w definicjach i inicjatywach, które mają definicje [trybów Menedżer zasobów](./definition-structure.md#resource-manager-modes) .

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Jeśli przypisanie dotyczy inicjatywy, można skonfigurować różne komunikaty dla każdej definicji zasad w ramach inicjatywy. Komunikaty używają `policyDefinitionReferenceId` wartości skonfigurowanej w definicji inicjatywy. Aby uzyskać szczegółowe informacje, zobacz [właściwości definicji zasad](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parametry

Ten segment przydziału zasad zawiera wartości parametrów zdefiniowanych w [definicji zasad lub definicji inicjatywy](./definition-structure.md#parameters). Dzięki temu projektowi można ponownie używać zasad lub definicji inicjatywy z różnymi zasobami, ale sprawdzać różne wartości biznesowe lub wyniki.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

W tym przykładzie parametry wcześniej zdefiniowane w definicji zasad są `prefix` i `suffix` . Ten konkretny zestaw przypisań zasad `prefix` należy do **działu** i `suffix` do **-LC**. Ta sama definicja zasad jest wielokrotnego użytku z innym zestawem parametrów dla innego działu, zmniejszając duplikowanie i złożoność definicji zasad przy jednoczesnym zapewnianiu elastyczności.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
