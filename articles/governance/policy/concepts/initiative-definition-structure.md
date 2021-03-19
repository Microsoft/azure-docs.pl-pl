---
title: Szczegóły struktury definicji inicjatywy
description: Opisuje, w jaki sposób definicje inicjatyw zasad są używane do definiowania zasad grupy w celu wdrożenia do zasobów platformy Azure w organizacji.
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587223"
---
# <a name="azure-policy-initiative-definition-structure"></a>Struktura definicji Azure Policy inicjatywy

Inicjatywy umożliwiają grupowanie kilku powiązanych definicji zasad w celu uproszczenia przypisań i zarządzania, ponieważ pracujesz z grupą jako pojedynczy element. Na przykład można pogrupować powiązane definicje zasad oznakowania do jednej inicjatywy. Zamiast przypisywać poszczególne zasady indywidualnie, należy zastosować inicjatywę.

Do utworzenia definicji inicjatywy zasad używany jest kod JSON. Definicja inicjatywy Policy zawiera elementy dla:

- Nazwa wyświetlana
- description (opis)
- metadane
- parameters
- definicje zasad
- grupy zasad (Ta właściwość jest częścią [funkcji zgodności z przepisami (wersja zapoznawcza)](./regulatory-compliance.md))

Poniższy przykład ilustruje sposób tworzenia inicjatywy do obsługi dwóch tagów: `costCenter` i `productName` . Używa dwóch wbudowanych zasad, aby zastosować domyślną wartość tagu.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy wbudowane i wzorce są [Azure Policy próbkami](../samples/index.md).

## <a name="metadata"></a>Metadane

Właściwość opcjonalna `metadata` przechowuje informacje o definicji inicjatywy Policy.
Klienci mogą definiować wszelkie właściwości i wartości przydatne w organizacji w programie `metadata` . Istnieją jednak _typowe_ właściwości, które są używane przez Azure Policy i wbudowane.

### <a name="common-metadata-properties"></a>Wspólne właściwości metadanych

- `version` (ciąg): śledzi szczegółowe informacje o wersji zawartości definicji inicjatywy zasad.
- `category` (ciąg): określa, w której kategorii Azure Portal jest wyświetlana definicja zasad.

  > [!NOTE]
  > W przypadku inicjatywy [zgodności z przepisami](./regulatory-compliance.md) należy zapewnić zgodność z `category` **przepisami**.

- `preview` (wartość logiczna): prawda lub FAŁSZ flagi dla Jeśli definicja inicjatywy zasad jest w _wersji zapoznawczej_.
- `deprecated` (wartość logiczna): prawda lub FAŁSZ flagi, jeśli definicja inicjatywy zasad została oznaczona jako _przestarzała_.

> [!NOTE]
> Usługa Azure Policy używa `version` , `preview` i `deprecated` właściwości do przekazywania poziomu zmiany do wbudowanej definicji zasad lub inicjatywy i stanu. Format `version` to: `{Major}.{Minor}.{Patch}` . Określone Stany, takie jak _przestarzałe_ lub _Podgląd_, są dołączane do `version` właściwości lub w innej właściwości jako **wartość logiczna**. Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Policy wersje wbudowane, zobacz [wbudowana wersja](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametry

Parametry pomagają uprościć zarządzanie zasadami przez zmniejszenie liczby definicji zasad. Pomyśl o parametrach, takich jak pola w formularzu —, `name` `address` , `city` , `state` . Te parametry zawsze pozostają takie same, ale ich wartości zmieniają się w zależności od poszczególnych wypełniania formularza.
Parametry działają w ten sam sposób podczas tworzenia inicjatyw zasad. Dołączając parametry w definicji inicjatywy zasad, można ponownie użyć tego parametru w uwzględnionych zasadach.

> [!NOTE]
> Po przypisaniu inicjatywy nie można zmienić parametrów poziomu Initative. Ze względu na to zalecenie polega na ustawieniu elementu **DefaultValue** podczas definiowania parametru.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji inicjatywy zasad:

- `name`: Nazwa parametru. Używane przez `parameters` funkcję wdrażania w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [Używanie wartości parametru](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Określa, czy parametr jest **ciągiem**, **tablicą**, **obiektem**, **wartością logiczną**, **liczbą całkowitą**, **zmiennoprzecinkową** lub **DateTime**.
- `metadata`: Definiuje podwłaściwości używane głównie przez Azure Portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: Wyjaśnienie, do czego służy parametr. Może służyć do podania przykładów akceptowalnych wartości.
  - `displayName`: Przyjazna nazwa wyświetlana w portalu dla parametru.
  - `strongType`: (Opcjonalnie) używany podczas przypisywania definicji zasad za pomocą portalu. Zawiera listę kontekstową. Aby uzyskać więcej informacji, zobacz [strongtype](#strongtype).
- `defaultValue`: (Opcjonalnie) ustawia wartość parametru w przypisaniu, jeśli nie podano wartości.
- `allowedValues`: (Opcjonalnie) zawiera tablicę wartości akceptowanych przez parametr podczas przypisywania.

Można na przykład zdefiniować definicję inicjatywy zasad, aby ograniczyć lokalizacje zasobów w różnych uwzględnionych definicjach zasad. Parametr tej definicji inicjatywy zasad może być **allowedLocations**. Ten parametr jest następnie dostępny dla każdej uwzględnionej definicji zasad i zdefiniowany podczas przypisywania inicjatywy Policy.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Przekazywanie wartości parametru do definicji zasad

Należy zadeklarować, które parametry inicjatywy są przekazywane, do których dołączono definicje zasad w tablicy [policyDefinitions](#policy-definitions) definicji inicjatywy. Nazwa parametru może być taka sama, przy użyciu różnych nazw w inicjatywach niż w definicjach zasad upraszcza czytelność kodu.

Na przykład wcześniej zdefiniowany parametr inicjatywy **init_allowedLocations** może zostać przesłany do kilku uwzględnionych definicji zasad i ich parametrów, **sql_locations** i **vm_locations** w następujący sposób:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Ten przykład odwołuje się do parametru **init_allowedLocations** , który został pokazany we [właściwościach parametrów](#parameter-properties).

### <a name="strongtype"></a>strongtype

We `metadata` właściwości można użyć **silnego** typu, aby udostępnić listę opcji dostępnych w ramach Azure Portal. **silntype** może być obsługiwanym _typem zasobu_ lub dozwoloną wartością. Aby określić, czy _Typ zasobu_ jest prawidłowy dla **silnego** elementu, użyj polecenie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Niektóre typy zasobów, które nie są zwracane przez **Get-AzResourceProvider** , są obsługiwane. Te typy zasobów to:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Dozwolone wartości typu niezwiązanego z typem **zasobu to:**

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Definicje zasad

`policyDefinitions`Część definicji inicjatywy jest _tablicą_ zawierającą istniejące definicje zasad zawarte w ramach inicjatywy. Jak wspomniano w [przekazaniu wartości parametru do definicji zasad](#passing-a-parameter-value-to-a-policy-definition), ta właściwość jest miejscem, w którym [Parametry inicjatywy](#parameters) są przekazywane do definicji zasad.

### <a name="policy-definition-properties"></a>Właściwości definicji zasad

Każdy element _tablicy_ , który reprezentuje definicję zasad, ma następujące właściwości:

- `policyDefinitionId` (ciąg): identyfikator niestandardowej lub wbudowanej definicji zasad do uwzględnienia.
- `policyDefinitionReferenceId` (ciąg): krótka nazwa uwzględnionej definicji zasad.
- `parameters`: (Opcjonalnie) pary nazwa/wartość do przekazania parametru inicjatywy do definicji zasad uwzględnionych jako właściwość w tej definicji zasad. Aby uzyskać więcej informacji, zobacz [Parametry](#parameters).
- `groupNames` (Tablica ciągów): (opcjonalnie) Grupa, do której należy definicja zasad. Aby uzyskać więcej informacji, zobacz [grupy zasad](#policy-definition-groups).

Oto przykład `policyDefinitions` , który ma dwie dołączone definicje zasad, które zostały spełnione przez każdy z tych samych parametrów inicjatywy:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Grupy definicji zasad

Definicje zasad w definicji inicjatywy można grupować i klasyfikować. Funkcja [zgodności z przepisami](./regulatory-compliance.md) Azure Policy (wersja zapoznawcza) używa tej właściwości do grupowania definicji w **kontrolkach** i **domenach zgodności**. Te informacje są zdefiniowane we właściwości `policyDefinitionGroups` _Array_ . Dodatkowe szczegóły grupowania można znaleźć w obiekcie **policyMetadata** utworzonym przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [obiekty metadanych](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Parametry grup definicji zasad

Każdy element _tablicy_ w `policyDefinitionGroups` musi mieć obie następujące właściwości:

- `name` (ciąg) \[ wymagane \] : krótka nazwa **grupy**. W obszarze zgodność z przepisami **Kontrola**. Wartość tej właściwości jest używana przez program `groupNames` w elemencie `policyDefinitions` .
- `category` (ciąg): hierarchia, do której należy Grupa. W obszarze zgodność z przepisami, **domena zgodności** formantu.
- `displayName` (ciąg): przyjazna nazwa **grupy** lub **formantu**. Używany przez portal.
- `description` (ciąg): Opis elementów, które obejmuje **Grupa** lub **Kontrola** .
- `additionalMetadataId` (ciąg): Lokalizacja obiektu [policyMetadata](#metadata-objects) , który zawiera dodatkowe szczegóły dotyczące **kontroli** i **domeny zgodności**.

  > [!NOTE]
  > Klienci mogą wskazywać istniejący obiekt [policyMetadata](#metadata-objects) . Jednak te obiekty są _tylko do odczytu_ i są tworzone tylko przez firmę Microsoft.

Przykład `policyDefinitionGroups` właściwości z definicji wbudowanej inicjatywy NIST wygląda następująco:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Obiekty metadanych

Utworzone przez firmę Microsoft wbudowane elementy zgodności mają dodatkowe informacje na temat każdej kontrolki.
Te informacje są następujące:

- Wyświetlane w Azure Portal na temat przeglądu **kontroli** zgodności z przepisami.
- Dostępne za pośrednictwem interfejsu API REST. Zobacz `Microsoft.PolicyInsights` dostawcę zasobów i [grupę operacji policyMetadata](/rest/api/policy/policymetadata/getresource).
- Dostępne za pośrednictwem interfejsu wiersza polecenia platformy Azure. Zobacz [AZ Policy Metadata](/cli/azure/policy/metadata) Command.

> [!IMPORTANT]
> Obiekty metadanych na potrzeby zgodności z przepisami są _tylko do odczytu_ i nie mogą być tworzone przez klientów.

Metadane dla grupy zasad zawierają następujące informacje w `properties` węźle:

- `metadataId`: **Identyfikator formantu** , do którego odnosi się grupowanie.
- `category` (wymagane): **domena zgodności** , do której należy **formant** .
- `title` (wymagane): przyjazna nazwa **identyfikatora formantu**.
- `owner` (wymagane): określa, kto jest odpowiedzialny za kontrolę na platformie Azure: _Klient_, _firma Microsoft_, _współdzielona_.
- `description`: Dodatkowe informacje o formancie.
- `requirements`: Szczegółowe informacje na temat odpowiedzialności za implementację kontrolki.
- `additionalContentUrl`: Link do dodatkowych informacji o formancie. Ta właściwość jest zazwyczaj łączem do sekcji dokumentacji, która obejmuje tę kontrolkę w standardzie zgodności.

Poniżej znajduje się przykład obiektu **policyMetadata** . To przykładowe metadane należy do kontrolki _NIST SP 800-53 R4 AC-1_ .

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Następne kroki

- Zobacz [strukturę definicji](./definition-structure.md)
- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
