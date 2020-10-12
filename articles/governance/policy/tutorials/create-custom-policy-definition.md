---
title: 'Samouczek: Tworzenie definicji zasad niestandardowych'
description: W tym samouczku utworzysz niestandardową definicję zasad Azure Policy, aby wymusić niestandardowe reguły biznesowe dla zasobów platformy Azure.
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 72282cbc5ed1877cf3f61b792235e8dc2f72fffe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649823"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Samouczek: Tworzenie definicji zasad niestandardowych

Niestandardowa definicja zasad umożliwia klientom definiowanie własnych reguł dotyczących korzystania z platformy Azure. Te reguły często wymuszają:

- Rozwiązania z zakresu bezpieczeństwa
- Zarządzanie kosztami
- Reguły specyficzne dla organizacji (np. dotyczące nazewnictwa lub lokalizacji)

Niezależnie od potrzeb biznesowych wymagających utworzenia zasad niestandardowych, kroki definiowania nowych zasad niestandardowych są takie same.

Przed utworzeniem zasad niestandardowych sprawdź [przykłady zasad](../samples/index.md), aby określić, czy zasady pasujące do potrzeb już istnieją.

Proces tworzenia zasad niestandardowych obejmuje następujące kroki:

> [!div class="checklist"]
> - Określanie wymagań biznesowych
> - Mapowanie każdego wymagania na właściwość zasobu platformy Azure
> - Mapowanie właściwości na alias
> - Określanie efektu do użycia
> - Tworzenie definicji zasad

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="identify-requirements"></a>Określanie wymagań

Ważne jest, aby przed utworzeniem definicji zasad poznać przeznaczenie zasad. W tym samouczku jako celu użyjemy typowego wymagania dotyczącego zabezpieczeń w przedsiębiorstwie, aby przedstawić wykonywane kroki:

- Każde konto magazynu musi mieć włączoną obsługę protokołu HTTPS
- Każde konto magazynu musi mieć wyłączoną obsługę protokołu HTTP

Wymagania powinny wyraźnie definiować zarówno „poprawne”, jak i „zakazane” stany zasobów.

Chociaż zdefiniowaliśmy oczekiwany stan zasobu, nie zdefiniowaliśmy jeszcze, co chcemy zrobić z niezgodnymi zasobami. Azure Policy obsługuje wiele [efektów](../concepts/effects.md). W tym samouczku zdefiniujemy wymaganie biznesowe, które zakazuje tworzenia zasobów, jeśli nie są one zgodne z regułami firmy. Aby osiągnąć ten cel, użyjemy efektu [Deny](../concepts/effects.md#deny) (Odmów). Chcemy również mieć możliwość wstrzymania zasad dla określonych przypisań. Dlatego użyjemy efektu [Disabled](../concepts/effects.md#disabled) (Wyłączone) i określimy go jako [parametr](../concepts/definition-structure.md#parameters) w definicji zasad.

## <a name="determine-resource-properties"></a>Określanie właściwości zasobów

Zgodnie z wymaganiami biznesowymi zasób platformy Azure do inspekcji przy użyciu Azure Policy jest kontem magazynu. Jednak nie znamy właściwości do użycia w definicji zasad. Azure Policy oblicza względem reprezentacji JSON zasobu, dlatego musimy zrozumieć właściwości dostępne dla tego zasobu.

Istnieje wiele sposobów określania właściwości zasobu platformy Azure. Omówimy każdy z nich na potrzeby tego samouczka:

- Rozszerzenie usługi Azure Policy dla programu VS Code
- Szablony Azure Resource Manager (szablony ARM)
  - Eksportowanie istniejącego zasobu
  - Środowisko tworzenia
  - Szablony Szybki start (GitHub)
  - Dokumentacja szablonu
- Eksplorator zasobów Azure

### <a name="view-resources-in-vs-code-extension"></a>Wyświetl zasoby w VS Code rozszerzeniu

[Rozszerzenia vs Code](../how-to/extension-for-vscode.md#search-for-and-view-resources) można użyć do przeglądania zasobów w środowisku i wyświetlania właściwości Menedżer zasobów poszczególnych zasobów.

### <a name="arm-templates"></a>Szablony usługi ARM

[Szablon usługi Resource Manager](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) zawierający szukaną właściwość w celu zarządzania nią można sprawdzić na kilka sposobów.

#### <a name="existing-resource-in-the-portal"></a>Istniejący zasób w portalu

Najprostszym sposobem na znalezienie właściwości jest przyjrzenie się istniejącemu zasobowi tego samego typu. Zasoby już skonfigurowane za pomocą ustawienia, które ma być wymuszane, służą także do porównywania wartości.
Przyjrzyj się stronie **Eksportuj szablon** (w obszarze **ustawienia**) w Azure Portal dla tego konkretnego zasobu.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Zrzut ekranu przedstawiający stronę Eksportuj szablon w istniejącym zasobie w Azure Portal." border="false":::

Wykonanie tego działania dla konta magazynu spowoduje wyświetlenie szablonu podobnego do następującego przykładu:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

W obszarze **properties** znajduje się właściwość o nazwie **supportsHttpsTrafficOnly** ustawiona na wartość **false**. Ta właściwość prawdopodobnie jest właściwością, której szukamy. Ponadto właściwość **type** zasobu ma wartość **Microsoft.Storage/storageAccounts**. Ten typ umożliwia ograniczenie zasad do zasobów tylko tego typu.

#### <a name="create-a-resource-in-the-portal"></a>Tworzenie zasobu w portalu

Innym sposobem użycia portalu jest środowisko tworzenia zasobu. Podczas tworzenia konta magazynu za pośrednictwem portalu na karcie **Zaawansowane** znajduje się pozycja **Wymagany transfer zabezpieczeń**. Ta właściwość ma opcje _Wyłączone_ i _Włączone_. Ikona informacji zawiera dodatkowy tekst, który potwierdza, że ta opcja jest prawdopodobnie odpowiednia. Jednak portal nie podaje nazwy właściwości na tym ekranie.

W dolnej części karty **Przeglądanie + tworzenie** znajduje się link **Pobierz szablon automatyzacji**. Wybranie linku otwiera szablon, który tworzy skonfigurowany zasób. W tym przypadku są widoczne dwie kluczowe informacje:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Te informacje określają typ właściwości i potwierdzają, że właściwość **supportsHttpsTrafficOnly** jest tą, której szukamy.

#### <a name="quickstart-templates-on-github"></a>Szablony Szybki start w usłudze GitHub

[Szablony szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates) w usłudze GitHub mają setki szablonów ARM utworzonych dla różnych zasobów. Szablony te mogą być doskonałym sposobem na znalezienie szukanej właściwości zasobu. Niektóre właściwości mogą wydawać się odpowiednie, lecz kontrolują coś innego.

#### <a name="resource-reference-docs"></a>Dokumentacja zasobu

Aby sprawdzić poprawność właściwości **supportsHttpsTrafficOnly** , sprawdź informacje dotyczące szablonu ARM dla [zasobu konta magazynu](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) w dostawcy magazynu. Obiekt właściwości zawiera listę prawidłowych parametrów. Wybranie linku [Obiekt StorageAccountPropertiesCreateParameters](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) umożliwia wyświetlenie tabeli dopuszczalnych właściwości. Właściwość **supportsHttpsTrafficOnly** jest obecna i jej opis odpowiada naszym wymaganiom biznesowym.

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure

Innym sposobem eksplorowania zasobów platformy Azure jest użycie usługi [Azure Resource Explorer](https://resources.azure.com) (wersja zapoznawcza). To narzędzie używa kontekstu subskrypcji, więc musisz uwierzytelnić się w witrynie internetowej przy użyciu poświadczeń platformy Azure. Po uwierzytelnieniu możesz przeglądać pozycje według dostawców, subskrypcji, grup zasobów i zasobów.

Znajdź zasób konta magazynu i poszukaj właściwości. Tutaj także znajduje się właściwość **supportsHttpsTrafficOnly**. Po wybraniu karty **Dokumentacja** widać, że opis właściwości pasuje do tego, co znaleźliśmy wcześniej w dokumentacji.

## <a name="find-the-property-alias"></a>Znajdowanie aliasu właściwości

Zidentyfikowaliśmy właściwość zasobu, ale musimy zamapować tę właściwość na [alias](../concepts/definition-structure.md#aliases).

Istnieje kilka sposobów określenia aliasów dla zasobu platformy Azure. Omówimy każdy z nich na potrzeby tego samouczka:

- Rozszerzenie usługi Azure Policy dla programu VS Code
- Interfejs wiersza polecenia platformy Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="get-aliases-in-vs-code-extension"></a>Pobierz aliasy w VS Code rozszerzeniu

Rozszerzenie Azure Policy dla rozszerzenia VS Code ułatwia przeglądanie zasobów i [odnajdywanie aliasów](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> Rozszerzenie VS Code uwidacznia tylko właściwości trybu Menedżer zasobów i nie wyświetla żadnych właściwości [trybu dostawcy zasobów](../concepts/definition-structure.md#mode) .

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure polecenie `az provider` służy do wyszukiwania aliasów zasobu. Przefiltrujemy przestrzeń nazw **Microsoft.Storage** za pomocą uzyskanych wcześniej szczegółów dotyczących zasobu platformy Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

W wynikach jest widoczny alias o nazwie **supportsHttpsTrafficOnly** obsługiwany przez konta magazynu. Istnienie tego aliasu oznacza, że możemy zapisać zasady, aby wymuszać nasze wymagania biznesowe!

### <a name="azure-powershell"></a>Azure PowerShell

W programie Azure PowerShell polecenie cmdlet `Get-AzPolicyAlias` służy do wyszukiwania aliasów zasobu. Przefiltrujemy przestrzeń nazw **Microsoft.Storage** za pomocą uzyskanych wcześniej szczegółów dotyczących zasobu platformy Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Podobnie jak w przypadku interfejsu wiersza polecenia platformy Azure, w wynikach jest widoczny alias o nazwie **supportsHttpsTrafficOnly** obsługiwany przez konta magazynu.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) to usługa, która zapewnia inną metodę znajdowania właściwości zasobów platformy Azure. Tutaj przedstawiono przykładowe zapytanie umożliwiające przejrzenie pojedynczego konta magazynu przy użyciu usługi Resource Graph:

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Wyniki wyglądają podobnie jak te widoczne w szablonach ARM i za pomocą Azure Resource Explorer. Jednak wyniki wykresu zasobów platformy Azure mogą również zawierać szczegóły [aliasu](../concepts/definition-structure.md#aliases) przez _projekcję_ tablicy _aliasów_ :

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Tutaj przedstawiono przykładowe dane wyjściowe dla konta magazynu dotyczące aliasów:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Wykres zasobów platformy Azure może być używany w [Cloud Shell](https://shell.azure.com), co umożliwia szybkie i łatwe Eksplorowanie właściwości zasobów.

## <a name="determine-the-effect-to-use"></a>Określanie efektu do użycia

Określenie, co należy zrobić z niezgodnymi zasobami, jest niemal tak ważne, jak zdecydowanie, co należy ocenić w pierwszej kolejności. Każda możliwa odpowiedź na niezgodny zasób jest nazywana [efektem](../concepts/effects.md). Efekt kontroluje, czy niezgodny zasób jest rejestrowany, blokowany, czy są dołączane do niego dane lub czy jest z nim kojarzone wdrożenie przywracające zasób do stanu zgodności.

W naszym przykładzie odmowa jest efektem, ponieważ nie chcemy, aby niezgodne zasoby zostały utworzone w środowisku platformy Azure. Audit (Inspekcja) jest dobrym pierwszym wyborem dla efektu zasad, umożliwiając określenie wpływu zasad przed ustawieniem dla nich efektu Deny (Odmów). Jednym ze sposobów na ułatwienie modyfikowania efektu dla przypisania jest sparametryzowanie efektu. Zobacz [Parametry](#parameters) poniżej, aby uzyskać szczegółowe informacje na ten temat.

## <a name="compose-the-definition"></a>Tworzenie definicji

Teraz mamy szczegóły i alias właściwości, którą chcemy zarządzać. Następnie utworzymy samą regułę zasad. Jeśli jeszcze nie znasz języka zasad, zapoznaj się z artykułem dotyczącym [struktury definicji zasad](../concepts/definition-structure.md), aby dowiedzieć się, jak określić strukturę definicji zasad. Pusty szablon z definicją zasad wygląda następująco:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadane

Pierwsze trzy składniki to metadane zasad. Łatwo jest podać wartości dla tych składników, ponieważ wiemy, jaki jest cel tworzenia reguły. [Tryb](../concepts/definition-structure.md#mode) dotyczy przede wszystkim tagów i lokalizacji zasobu. Ponieważ nie potrzebujemy ograniczać oceny do zasobów obsługujących tagi, użyjemy wartości _all_ (wszystko) dla parametru **mode** (tryb).

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

Mimo że nie użyliśmy parametru do zmiany oceny, chcemy użyć parametru, aby zezwolić na zmienianie **efektu** na potrzeby rozwiązywania problemów. Zdefiniujemy parametr **effectType** (typEfektu) i ograniczymy go tylko do opcji **Deny** (Odmów) i **Disabled** (Wyłączone). Te dwie opcje pasują do naszych wymagań biznesowych. Ukończony blok parametrów wygląda jak w następującym przykładzie:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Reguła zasad

Utworzenie [reguły zasad](../concepts/definition-structure.md#policy-rule) to ostatni krok tworzenia niestandardowej definicji zasad. Określiliśmy dwa warunki do testowania:

- Właściwość **type** konta magazynu to **Microsoft.Storage/storageAccounts**
- Właściwość **supportsHttpsTrafficOnly** konta magazynu nie ma wartości **true**

Ponieważ oba te warunki muszą być spełnione, użyjemy opcji **allOf (wszystkie) jako ** [operatora logicznego](../concepts/definition-structure.md#logical-operators). Przekażemy parametr **effectType** (typEfektu) do efektu zamiast określania deklaracji statycznej. Ukończona reguła wygląda podobnie do następującego przykładu:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Ukończona definicja

Oto kompletna definicja zawierająca wszystkie trzy części zasad:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Ukończonej definicji można użyć do utworzenia nowych zasad. Portal i każdy z zestawów SDK (interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell i interfejsu API REST) przyjmuje definicję w inny sposób, więc należy przejrzeć polecenia dla każdego z nich, aby zwalidować poprawność składni. Następnie przypisz ją za pomocą sparametryzowanego efektu do odpowiednich zasobów w celu zarządzania zabezpieczeniami kont magazynu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz dalszej pracy z zasobami utworzonymi w tym samouczku, wykonaj poniższe kroki, aby usunąć wszystkie utworzone powyżej przypisania lub definicje:

1. Wybierz pozycję **Definicje** (lub **Przypisania**, jeśli próbujesz usunąć przypisanie) w obszarze **Tworzenie** w lewej części strony usługi Azure Policy.

1. Wyszukaj nowo utworzoną definicję inicjatywy lub zasad (albo przypisanie), którą chcesz usunąć.

1. Kliknij prawym przyciskiem myszy wiersz albo wybierz wielokropek na końcu definicji lub przypisania, a następnie wybierz pozycję **Usuń definicję** (lub **Usuń przypisanie**).

## <a name="review"></a>Przegląd

W tym samouczku pomyślnie wykonano następujące czynności:

> [!div class="checklist"]
> - Określono wymagania biznesowe
> - Zamapowano każde wymaganie na właściwość zasobu platformy Azure
> - Zamapowano właściwości na alias
> - Określono efekt do użycia
> - Utworzono definicję zasad

## <a name="next-steps"></a>Następne kroki

Następnie za pomocą niestandardowej definicji zasad utwórz i przypisz zasady:

> [!div class="nextstepaction"]
> [Tworzenie i przypisywanie definicji zasad](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
