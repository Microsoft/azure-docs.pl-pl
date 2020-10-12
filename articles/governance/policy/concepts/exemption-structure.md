---
title: Szczegóły struktury wykluczenia zasad
description: Opisuje definicję wykluczenia zasad używane przez Azure Policy do wykluczenia zasobów z oceny inicjatyw lub definicji.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1fd14d31824dc86dcd3788607030f28f978f5801
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968051"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy struktura wykluczenia

Funkcja wykluczania Azure Policy (wersja zapoznawcza) służy do _wykluczania_ hierarchii zasobów lub pojedynczego zasobu z oceny inicjatyw lub definicji. Zasoby, które są _wykluczone_ względem ogólnej zgodności, ale nie mogą być oceniane ani mieć tymczasowego zwolnienia. Aby uzyskać więcej informacji, zobacz [Opis zakresu w Azure Policy](./scope.md). Azure Policy wykluczenia działają tylko z [trybami Menedżer zasobów](./definition-structure.md#resource-manager-modes) i nie działają z **trybami dostawcy zasobów**.

> [!IMPORTANT]
> Ta funkcja jest bezpłatna w **wersji zapoznawczej**. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy/). Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Aby utworzyć wykluczenie zasad, należy użyć formatu JSON. Wykluczanie zasad zawiera elementy dla:

- Nazwa wyświetlana
- description
- metadane
- przypisanie zasad
- definicje zasad w ramach inicjatywy
- Kategoria wykluczenia
- datę

> [!NOTE]
> Wykluczanie zasad jest tworzone jako obiekt podrzędny w hierarchii zasobów lub do indywidualnego zasobu, który udzielił wykluczenia, dlatego element docelowy nie jest uwzględniony w definicji wykluczenia.

Na przykład poniższy kod JSON pokazuje wykluczenia zasad w kategorii **odstąpienia** zasobu do przypisania inicjatywy o nazwie `resourceShouldBeCompliantInit` . Zasób jest _wykluczony_ tylko z dwóch definicji zasad z inicjatywy, `customOrgPolicy` definicji zasad niestandardowych (odwołanie `requiredTags` ) i wbudowanej definicji zasad "dozwolone lokalizacje" (Identyfikator: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , odwołanie `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Fragment dotyczący pokrewnej inicjatywy z dopasowaniem `policyDefinitionReferenceIds` używanym przez wykluczenie zasad:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować wykluczenia zasad i podać kontekst do użycia z określonym zasobem. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

## <a name="metadata"></a>Metadane

Właściwość **Metadata** umożliwia tworzenie wszelkich właściwości podrzędnych potrzebnych do przechowywania istotnych informacji. W powyższym przykładzie właściwości **RequestedBy**, **approvedBy**, **approvedOn**i **ticketRef** zawierają wartości klienta, aby podać informacje o osobach, które zażądały wykluczenia, kto je zatwierdził i kiedy oraz wewnętrzny bilet śledzenia żądania. Te właściwości **metadanych** są przykładami, ale nie są wymagane, a **metadane** nie są ograniczone do tych właściwości podrzędnych.

## <a name="policy-assignment-id"></a>Identyfikator przypisania zasad

To pole musi zawierać pełną nazwę ścieżki przypisania zasad lub przypisanie inicjatywy.
`policyAssignmentId` jest ciągiem, a nie tablicą. Ta właściwość definiuje przypisanie, z którego ma zostać _wykluczona_ hierarchia zasobów nadrzędnych lub poszczególnych zasobów.

## <a name="policy-definition-ids"></a>Identyfikatory definicji zasad

Jeśli `policyAssignmentId` dotyczy to przypisania inicjatywy, `policyDefinitionReferenceIds` Właściwość może służyć do określenia definicji zasad w ramach inicjatywy, do których zasób podmiotu ma wykluczenie. Ponieważ zasób może być zwolniony z co najmniej jednej z uwzględnionych definicji zasad, ta właściwość jest _tablicą_. Wartości muszą być zgodne z wartościami w definicji inicjatywy w `policyDefinitions.policyDefinitionReferenceId` polach.

## <a name="exemption-category"></a>Kategoria wykluczenia

Istnieją dwie kategorie wykluczenia i są używane do grupowania wykluczeń:

- **Skorygowane**: wykluczenie jest udzielane, ponieważ przeznaczenie zasad zostało spełnione przez inną metodę.
- **Odstąpienie**: zwolnienie jest przyznawane, ponieważ stan braku zgodności zasobu jest tymczasowo zaakceptowany. Kolejną przyczyną użycia tej kategorii jest zasób lub hierarchia zasobów, która powinna zostać wykluczona z co najmniej jednej definicji z inicjatywy, ale nie powinna być wykluczona z całej inicjatywy.

## <a name="expiration"></a>Wygaśnięcie

Aby określić, kiedy hierarchia zasobów lub pojedynczy zasób nie jest już _wykluczony_ do przypisania, ustaw `expiresOn` Właściwość. Ta opcjonalna właściwość musi mieć format typu DateTime uniwersalne ISO 8601 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> Wykluczenia zasad nie są usuwane, gdy `expiresOn` Data zostanie osiągnięta. Obiekt jest zachowywany do zachowywania rekordów, ale wyłączenie nie jest już honorowane.

## <a name="required-permissions"></a>Wymagane uprawnienia

Uprawnienia usługi Azure RBAC wymagające zarządzania obiektami wykluczenia zasad znajdują się w `Microsoft.Authorization/policyExemptions` grupie operacji. [Współautorzy zasad zasobów](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) i [administrator zabezpieczeń](../../../role-based-access-control/built-in-roles.md#security-admin) wbudowane role mają uprawnienia `read` i `write` uprawnienia oraz [Moduł zapisywania danych usługi Insights (wersja zapoznawcza)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) `read` .

Zwolnienia mają dodatkowe środki bezpieczeństwa, ponieważ mają wpływ na wykluczenie. Poza wymaganiem `Microsoft.Authorization/policyExemptions/write` operacji w hierarchii zasobów lub poszczególnych zasobów, twórca wykluczenia musi mieć `exempt/Action` zlecenie dotyczące przypisania docelowej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strukturze definicji zasad](./definition-structure.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).