---
title: Szczegóły struktury przypisywania zasad
description: W tym artykule opisano definicję przypisania zasad używaną przez usługę Azure Policy do powiązania definicji zasad i parametrów z zasobami do oceny.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: cdb2fc0c6f057ece44383f68bc79fca54507db9b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683214"
---
# <a name="azure-policy-assignment-structure"></a>Struktura przypisań usługi Azure Policy

Przypisania zasad są używane przez usługę Azure Policy do definiowania, które zasoby są przypisywane, które zasady lub inicjatywy. Przypisanie zasad można określić wartości parametrów dla tej grupy zasobów w czasie przydziału, dzięki czemu można ponownie użyć definicji zasad, które dotyczą tych samych właściwości zasobów z różnych potrzeb zgodności.

JSON służy do tworzenia przypisania zasad. Przypisanie zasad zawiera elementy dla:

- nazwa wyświetlana
- description
- metadane
- tryb egzekwowania
- wykluczone zakresy
- definicja zasad
- parameters

Na przykład następujący JSON pokazuje przypisanie zasad w trybie _DoNotEnforce_ z parametrami dynamicznymi:

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

Wszystkie przykłady zasad platformy Azure znajdują się w [przykładach zasad platformy Azure.](../samples/index.md)

## <a name="display-name-and-description"></a>Wyświetlana nazwa i opis

**DisplayName** i **opis** służy do identyfikowania przypisania zasad i podaj kontekst do jego użycia z określonym zestawem zasobów. **displayName** ma maksymalną długość _128_ znaków i **opis** maksymalną długość _512_ znaków.

## <a name="enforcement-mode"></a>Tryb egzekwowania

Właściwość **enforcementMode** zapewnia klientom możliwość testowania wyników zasad dotyczących istniejących zasobów bez inicjowania efektu zasad lub wyzwalania wpisów w [dzienniku aktywności platformy Azure.](../../../azure-monitor/platform/platform-logs-overview.md) Ten scenariusz jest powszechnie określany jako "Co jeśli" i dostosowuje się do bezpiecznych praktyk wdrażania. **enforcementMode** różni się od [wyłączonego](./effects.md#disabled) efektu, ponieważ ten efekt zapobiega oceny zasobów w ogóle.

Ta właściwość ma następujące wartości:

|Tryb |Wartość JSON |Typ |Korygowanie ręcznie |Wpis dziennika aktywności |Opis |
|-|-|-|-|-|-|
|Enabled (Włączony) |Domyślne |ciąg |Tak |Tak |Efekt zasad jest wymuszany podczas tworzenia lub aktualizowania zasobów. |
|Disabled (Wyłączony) |DoNotEnforce (DoNotEnforce) |ciąg |Tak |Nie | Efekt zasad nie jest wymuszany podczas tworzenia lub aktualizowania zasobów. |

Jeśli **tryb enforcementMode** nie jest określony w definicji zasad lub inicjatywy, używana jest wartość _Domyślna._ [Zadania korygowania](../how-to/remediate-resources.md) można uruchomić dla [wdrażaniaIfNotExists](./effects.md#deployifnotexists) zasad, nawet wtedy, gdy **enforcementMode** jest ustawiona na _DoNotEnforce_.

## <a name="excluded-scopes"></a>Wykluczone zakresy

Zakres **scope** przydziału obejmuje wszystkie kontenery zasobów podrzędnych i zasoby podrzędne. Jeśli kontener zasobów podrzędnych lub zasób podrzędny nie powinien mieć zastosowanej definicji, każdy z nich może zostać wykluczony z oceny, ustawiając **notScopes**. Ta właściwość jest tablicą, aby włączyć wykluczając jeden lub więcej kontenerów zasobów lub zasobów z oceny. **notScopes** mogą być dodawane lub aktualizowane po utworzeniu początkowego przypisania.

## <a name="policy-definition-id"></a>Identyfikator definicji zasad

To pole musi być pełną nazwą ścieżki definicji zasad lub definicji inicjatywy.
`policyDefinitionId`jest ciągiem, a nie tablicą. Zaleca się, aby w przypadku często przypisywanych do siebie wielu zasad, zamiast tego użyć [inicjatywy.](./definition-structure.md#initiatives)

## <a name="parameters"></a>Parametry

Ten segment przypisania zasad zawiera wartości parametrów zdefiniowanych w [definicji zasad lub definicji inicjatywy](./definition-structure.md#parameters).
Ten projekt umożliwia ponowne użycie definicji zasad lub inicjatywy z różnymi zasobami, ale sprawdź różne wartości biznesowe lub wyniki.

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

W tym przykładzie parametry zdefiniowane wcześniej `prefix` w `suffix`definicji zasad są i . To konkretne przypisanie zasad `prefix` ustawia `suffix` **deptA** i **-LC**. Ta sama definicja zasad jest wielokrotnego użytku z innym zestawem parametrów dla innego działu, zmniejszając powielanie i złożoność definicji zasad, zapewniając jednocześnie elastyczność.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).