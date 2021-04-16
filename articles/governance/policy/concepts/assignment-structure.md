---
title: Szczegóły struktury przypisania zasad
description: Opisuje definicję przypisania zasad używaną przez Azure Policy do powiązać definicje zasad i parametry z zasobami do oceny.
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 9de210b17264330e79ab5978a449e7a494054be2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535879"
---
# <a name="azure-policy-assignment-structure"></a>Struktura przypisań usługi Azure Policy

Przypisania zasad są używane przez Azure Policy do definiowania, do których zasobów są przypisywane zasady lub inicjatywy. Przypisanie zasad może określać wartości parametrów dla tej grupy zasobów w czasie przypisywania, co umożliwia ponowne użycie definicji zasad, które są zgodne z tym samymi właściwościami zasobów o różnych potrzebach zgodności.

Aby utworzyć przypisanie zasad, należy użyć danych JSON. Przypisanie zasad zawiera elementy dla:

- nazwa wyświetlana
- description (opis)
- metadane
- tryb wymuszania
- wykluczone zakresy
- definicja zasad
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

Wszystkie Azure Policy przykłady znajdują się w [Azure Policy przykładów](../samples/index.md).

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Za pomocą **wartości displayName** i **description** można zidentyfikować przypisanie zasad i podać kontekst do jego użycia z określonym zestawem zasobów. **Nazwa wyświetlana** ma _maksymalną długość 128_ znaków i **opis** maksymalna długość _512_ znaków.

## <a name="metadata"></a>Metadane

Opcjonalna `metadata` właściwość przechowuje informacje o przypisaniu zasad. Klienci mogą definiować dowolne właściwości i wartości przydatne w organizacji w programie `metadata` . Istnieją jednak pewne typowe _właściwości_ używane przez Azure Policy. Każda `metadata` właściwość ma limit 1024 znaków.

### <a name="common-metadata-properties"></a>Typowe właściwości metadanych

- `assignedBy` (ciąg): przyjazna nazwa podmiotu zabezpieczeń, który utworzył przypisanie.
- `createdBy` (ciąg): identyfikator GUID podmiotu zabezpieczeń, który utworzył przypisanie.
- `createdOn` (ciąg): uniwersalny format Daty/godziny ISO 8601 czasu utworzenia przypisania.
- `parameterScopes` (obiekt): kolekcja par klucz-wartość, w której klucz jest zgodny z nazwą parametru skonfigurowanego przez typ [strongType,](./definition-structure.md#strongtype) a wartość definiuje zakres zasobów używany w portalu w celu zapewnienia listy dostępnych zasobów przez dopasowanie właściwości _strongType_. Portal ustawia tę wartość, jeśli zakres jest inny niż zakres przypisania. Jeśli to ustawienie jest ustawione, edycja przypisania zasad w portalu automatycznie ustawia zakres parametru na tę wartość. Jednak zakres nie jest zablokowany dla wartości i można go zmienić na inny zakres.

  Poniższy przykład to parametr strongType o nazwie `parameterScopes` **backupPolicyId,** który ustawia zakres wyboru zasobów podczas edytowania przypisania w portalu. 

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (ciąg): przyjazna nazwa podmiotu zabezpieczeń, który zaktualizował przypisanie, jeśli takie było.
- `updatedOn` (ciąg): uniwersalny format DateTime iso 8601 czasu aktualizacji przypisania, jeśli jest używany.

## <a name="enforcement-mode"></a>Tryb wymuszania

Właściwość **enforcementMode** zapewnia klientom możliwość testowania wyniku zasad w istniejących zasobach bez inicjowania efektu zasad ani wyzwalania wpisów w dzienniku aktywności [platformy Azure.](../../../azure-monitor/essentials/platform-logs-overview.md) Ten scenariusz jest często określany jako "What If" i jest dopasowany do praktyk bezpiecznego wdrażania. **Tryb enforcementMode** różni się od [efektu Disabled,](./effects.md#disabled) ponieważ ten efekt w ogóle uniemożliwia ocenę zasobów.

Ta właściwość ma następujące wartości:

|Tryb |Wartość JSON |Typ |Korygowanie ręcznie |Wpis dziennika aktywności |Opis |
|-|-|-|-|-|-|
|Enabled (Włączony) |Domyślne |ciąg |Yes |Yes |Efekt zasad jest wymuszany podczas tworzenia lub aktualizowania zasobów. |
|Disabled |DoNotEnforce |ciąg |Yes |Nie | Efekt zasad nie jest wymuszany podczas tworzenia lub aktualizowania zasobów. |

Jeśli **tryb enforcementMode** nie jest określony w definicji zasad lub inicjatywy, używana jest wartość _Domyślna._ [Zadania korygowania](../how-to/remediate-resources.md) można rozpocząć dla zasad [deployIfNotExists](./effects.md#deployifnotexists) nawet wtedy, gdy **tryb enforcementMode** jest ustawiony na _wartość DoNotEnforce._

## <a name="excluded-scopes"></a>Wykluczone zakresy

Zakres **przypisania** obejmuje wszystkie kontenery zasobów podrzędne i zasoby podrzędne. Jeśli zasób podrzędny lub zasób podrzędny nie powinien  mieć zastosowanej definicji, każdy z nich może zostać wykluczony z oceny przez ustawienie **notScopes**. Ta właściwość jest tablicą, która umożliwia wykluczenie z oceny co najmniej jednego kontenera zasobów lub zasobów. **NotScopes** można dodać lub zaktualizować po utworzeniu początkowego przypisania.

> [!NOTE]
> _Wykluczony_ zasób różni się od _wykluczonego_ zasobu. Aby uzyskać więcej informacji, zobacz [Opis zakresu w Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>Identyfikator definicji zasad

To pole musi być pełną nazwą ścieżki definicji zasad lub definicji inicjatywy.
`policyDefinitionId` jest ciągiem, a nie tablicą. Zaleca się, aby jeśli wiele zasad jest często przypisanych razem, zamiast tego użyć [inicjatywy.](./initiative-definition-structure.md)

## <a name="non-compliance-messages"></a>Komunikaty o niezgodności

Aby ustawić niestandardowy komunikat opisujący, dlaczego zasób jest niezgodny z definicją zasad lub inicjatywy, ustaw w `nonComplianceMessages` definicji przypisania. Ten węzeł jest tablicą `message` wpisów. Ten niestandardowy komunikat jest dodatkiem do domyślnego komunikatu o błędzie w przypadku niezgodności i jest opcjonalny.

> [!IMPORTANT]
> Komunikaty niestandardowe w przypadku niezgodności są obsługiwane tylko w definicjach lub inicjatywach z Resource Manager [definicji trybów.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Jeśli przypisanie jest dla inicjatywy, dla każdej definicji zasad w inicjatywie można skonfigurować różne komunikaty. Komunikaty używają wartości `policyDefinitionReferenceId` skonfigurowanej w definicji inicjatywy. Aby uzyskać szczegółowe informacje, zobacz [właściwości definicji zasad](./initiative-definition-structure.md#policy-definition-properties).

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

Ten segment przypisania zasad zawiera wartości parametrów zdefiniowanych w definicji [zasad lub definicji inicjatywy](./definition-structure.md#parameters). Ten projekt umożliwia ponowne użycie zasad lub definicji inicjatywy z różnymi zasobami, ale umożliwia sprawdzenie różnych wartości biznesowych lub wyników.

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

W tym przykładzie parametry zdefiniowane wcześniej w definicji zasad to `prefix` i `suffix` . To konkretne przypisanie zasad `prefix` ma wartości **DeptA** i `suffix` **-LC.** Ta sama definicja zasad jest wielokrotnego użytku z innym zestawem parametrów dla innego działu, co zmniejsza duplikowanie i złożoność definicji zasad przy jednoczesnym zapewnieniu elastyczności.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [o strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, [jak programowo tworzyć zasady.](../how-to/programmatically-create.md)
- Dowiedz się, jak [uzyskać dane zgodności.](../how-to/get-compliance-data.md)
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
- Aby sprawdzić, czym jest grupa zarządzania, zobacz [Organize your resources with Azure management groups (Organizowanie zasobów przy użyciu grup zarządzania platformy Azure).](../../management-groups/overview.md)
