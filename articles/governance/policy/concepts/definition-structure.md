---
title: Szczegóły struktury definicji zasad
description: W tym artykule opisano, jak definicje zasad są używane do ustanawiania konwencji dla zasobów platformy Azure w organizacji.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 0a7c4e05270ff242fa97b253b27a5de92895368a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461008"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Usługa Azure Policy ustanawia konwencje dotyczące zasobów. Definicje zasad opisują [warunki](#conditions) zgodności zasobów i efekt, który należy podjąć, jeśli warunek jest spełniony. Warunek porównuje [pole](#fields) właściwości zasobu z wymaganą wartością. Dostęp do pól właściwości zasobu są dostępne przy użyciu [aliasów](#aliases). Pole właściwości zasobu jest polem o pojedynczej wartości lub [tablicą](#understanding-the--alias) wielu wartości. Ocena warunku jest inna w tablicach.
Dowiedz się więcej o [warunkach](#conditions).

Definiując konwencje, można kontrolować koszty i łatwiej zarządzać zasobami. Na przykład można określić, że tylko niektóre typy maszyn wirtualnych są dozwolone. Lub można wymagać, aby wszystkie zasoby miały określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Jeśli zasada jest stosowana do grupy zasobów, ma zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat definicji zasad znajduje się tutaj:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Za pomocą JSON można utworzyć definicję zasad. Definicja zasad zawiera elementy dla:

- tryb
- parameters
- nazwa wyświetlana
- description
- reguła polityki
  - ocena logiczna
  - Efekt

Na przykład następujący JSON pokazuje zasady, które ogranicza, gdzie zasoby są wdrażane:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Wszystkie przykłady zasad platformy Azure znajdują się w [przykładach zasad platformy Azure.](../samples/index.md)

## <a name="mode"></a>Tryb

**Tryb** jest skonfigurowany w zależności od tego, czy zasada jest kierowana na właściwość usługi Azure Resource Manager lub właściwość dostawcy zasobów.

### <a name="resource-manager-modes"></a>Tryby Menedżera zasobów

**Tryb** określa, które typy zasobów będą oceniane dla zasad. Obsługiwane tryby to:

- `all`: ocena grup zasobów i wszystkich typów zasobów
- `indexed`: oceniaj tylko typy zasobów obsługujące znaczniki i lokalizację

Na przykład `Microsoft.Network/routeTables` zasób obsługuje tagi i lokalizację i jest oceniany w obu trybach. Jednak zasobu `Microsoft.Network/routeTables/routes` nie można oznaczyć i `Indexed` nie jest oceniany w trybie.

Zaleca się ustawienie **mode** trybu `all` na w większości przypadków. Wszystkie definicje zasad utworzone za `all` pośrednictwem portalu używają tego trybu. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, można ręcznie określić parametr **trybu.** Jeśli definicja zasad nie zawiera wartości **trybu,** `all` domyślnie w usłudze `null` Azure PowerShell i w usłudze Azure CLI. Tryb `null` jest taki sam, jak przy użyciu `indexed` do obsługi zgodności z powrotem.

`indexed`powinny być używane podczas tworzenia zasad, które wymuszają tagi lub lokalizacje. Chociaż nie jest to wymagane, zapobiega zasobów, które nie obsługują tagów i lokalizacji, wyświetlane jako niezgodne w wynikach zgodności. Wyjątkiem są **grupy zasobów**. Zasady, które wymuszają lokalizację lub `all` tagi w `Microsoft.Resources/subscriptions/resourceGroups` grupie zasobów, powinny ustawić **tryb** i w szczególności kierować go na ten typ. Na przykład zobacz [Wymuszanie znaczników grup zasobów](../samples/enforce-tag-rg.md). Aby uzyskać listę zasobów obsługujących tagi, zobacz [Obsługa tagów dla zasobów platformy Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Tryby dostawcy zasobów (wersja zapoznawcza)

Następujące tryby dostawcy zasobów są obecnie obsługiwane podczas wersji zapoznawczej:

- `Microsoft.ContainerService.Data`do zarządzania regułami kontrolera przyjęć w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md). Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceRegoPolicy.](./effects.md#enforceregopolicy)
- `Microsoft.Kubernetes.Data`do zarządzania samodzielnie zarządzanymi klastrami Kubernetes aparatu AKS na platformie Azure.
  Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`do zarządzania magazynami i certyfikatami w [usłudze Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Tryby dostawcy zasobów obsługują tylko wbudowane definicje zasad i nie obsługują inicjatyw w wersji zapoznawczej.

## <a name="parameters"></a>Parametry

Parametry pomagają uprościć zarządzanie zasadami, zmniejszając liczbę definicji zasad. Pomyśl o parametrach, takich `name`jak `address` `city`pola `state`w formularzu – , , , . Parametry te zawsze pozostają takie same, jednak ich wartości zmieniają się na podstawie indywidualnego wypełniania formularza.
Parametry działają w ten sam sposób podczas tworzenia zasad. Dołączając parametry do definicji zasad, można ponownie użyć tej zasady dla różnych scenariuszy przy użyciu różnych wartości.

> [!NOTE]
> Parametry mogą być dodawane do istniejącej i przypisanej definicji. Nowy parametr musi zawierać **właściwość defaultValue.** Zapobiega to pośrednio nieprawidłowe istniejące przypisania zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametrów

Parametr ma następujące właściwości, które są używane w definicji zasad:

- **nazwa**: Nazwa parametru. Używane przez `parameters` funkcję wdrażania w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [użycie wartości parametru](#using-a-parameter-value).
- `type`: Określa, czy parametr jest **ciągiem,** **tablicą,** **obiektem,** **wartością logiczną, całkowitej,** **floatem**lub **datetime**. **boolean**
- `metadata`: Definiuje właściwości podrzędne używane głównie przez witrynę Azure portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: Wyjaśnienie, do czego służy ten parametr. Może służyć do podaju przykładów dopuszczalnych wartości.
  - `displayName`: Przyjazna nazwa wyświetlana w portalu dla parametru.
  - `version`: (Opcjonalnie) Śledzi szczegóły dotyczące wersji definicji zasad.

    > [!NOTE]
    > Usługa Azure Policy `version`używa `preview`, `deprecated` i właściwości, aby przekazać poziom zmian do wbudowanej definicji zasad lub inicjatywy i stanu. Format `version` jest: `{Major}.{Minor}.{Patch}`. Określone stany, takie jak _przestarzałe_ lub _podgląd,_ są dołączane do `version` właściwości lub innej właściwości jako wartość **logiczna.**

  - `category`: (Opcjonalnie) Określa, w której kategorii w witrynie Azure portal jest wyświetlana definicja zasad.
  - `strongType`: (Opcjonalnie) Używany podczas przypisywania definicji zasad za pośrednictwem portalu. Zawiera listę kontekstu. Aby uzyskać więcej informacji, zobacz [strongType](#strongtype).
  - `assignPermissions`: (Opcjonalnie) Ustaw jako _true,_ aby witryna Azure portal tworzyła przypisania ról podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakresem przypisania. Istnieje jedno przypisanie roli na definicję roli w zasadach (lub definicji roli we wszystkich zasadach w inicjatywie). Wartość parametru musi być prawidłowym zasobem lub zakresem.
- `defaultValue`: (Opcjonalnie) Ustawia wartość parametru w przypisaniu, jeśli nie podano żadnej wartości.
  Wymagane podczas aktualizowania istniejącej definicji zasad, która jest przypisana.
- `allowedValues`: (Opcjonalnie) Zawiera tablicę wartości akceptowanych przez parametr podczas przypisywania.

Na przykład można zdefiniować definicję zasad, aby ograniczyć lokalizacje, w których można wdrożyć zasoby. Parametrem dla tej definicji zasad mogą być **dozwoloneLocations**. Ten parametr będzie używany przez każde przypisanie definicji zasad w celu ograniczenia przyjętych wartości. Korzystanie z **strongType** zapewnia ulepszone środowisko podczas wykonywania przypisania za pośrednictwem portalu:

```json
"parameters": {
    "allowedLocations": {
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

### <a name="using-a-parameter-value"></a>Korzystanie z wartości parametru

W regule zasad odwołujesz `parameters` się do parametrów z następującą składnią funkcji:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ten przykład odwołuje się do parametru **allowedLocations,** który został zademonstrowany we [właściwościach parametru](#parameter-properties).

### <a name="strongtype"></a>strongType

W `metadata` ramach właściwości można użyć **strongType,** aby zapewnić listę wielu wybierz opcji w witrynie Azure portal. **strongType** może być obsługiwanym _typem zasobu_ lub dozwoloną wartością. Aby ustalić, czy _typ zasobu_ jest prawidłowy dla **strongType,** należy użyć [programu Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Niektóre _typy zasobów_ nie zwracane przez **Get-AzResourceProvider** są obsługiwane. Są to:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Niedozwolone wartości _typu zasobu_ dla **strongType** to:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia inicjatywy lub zasad konieczne jest określenie lokalizacji definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcją. Ta lokalizacja określa zakres, do którego można przypisać inicjatywę lub zasady. Zasoby muszą być bezpośrednimi członkami lub elementy podrzędne w hierarchii lokalizacji definicji do celu dla przypisania.

Jeśli lokalizacja definicji jest:

- **Subskrypcja** — zasady można przypisać tylko zasoby w ramach tej subskrypcji.
- **Grupa zarządzania** — zasady można przypisać tylko zasoby w podrzędnych grupach zarządzania i subskrypcjach podrzędnych. Jeśli zamierzasz zastosować definicję zasad do kilku subskrypcji, lokalizacja musi być grupą zarządzania zawierającą te subskrypcje.

## <a name="display-name-and-description"></a>Wyświetlana nazwa i opis

**DisplayName** i **opis** służy do identyfikowania definicji zasad i podać kontekst, kiedy jest używany. **displayName** ma maksymalną długość _128_ znaków i **opis** maksymalną długość _512_ znaków.

> [!NOTE]
> Podczas tworzenia lub aktualizowania definicji zasad **identyfikator**, **typ**i **nazwa** są definiowane przez właściwości zewnętrzne do JSON i nie są konieczne w pliku JSON. Pobieranie definicji zasad za pomocą sdk zwraca **identyfikator**, **typ**i **właściwości nazwy** jako część JSON, ale każdy z nich są tylko do odczytu informacje związane z definicją zasad.

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z **bloku If** i **Then.** W **Bloku If** można zdefiniować jeden lub więcej warunków, które określają, kiedy zasady są wymuszane. Operatory logiczne można zastosować do tych warunków, aby dokładnie zdefiniować scenariusz dla zasad.

W **Bloku Następnie** można zdefiniować efekt, który dzieje się, gdy **if** warunki są spełnione.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operatory logiczne

Obsługiwane operatory logiczne to:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Nie **not** składnia odwraca wynik warunku. Składnia **allOf** (podobna do logicznej **i** operacji) wymaga spełnienia wszystkich warunków. Składnia **anyOf** (podobna do logicznej **lub** operacji) wymaga co najmniej jednego warunku, aby były spełnione.

Operatory logiczne można zagnieżdżać. W poniższym przykładzie pokazano **operację nie,** która jest zagnieżdżona w **operacji allOf.**

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Warunki

Warunek ocenia, czy **pole** lub akcesor **wartości** spełnia określone kryteria. Obsługiwane warunki to:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

Dla **mniej**, **lessOrEquals**, **greater**, i **greaterOrEquals**, jeśli typ właściwości nie pasuje do typu warunku, zgłaszany jest błąd. Porównania ciągów są `InvariantCultureIgnoreCase`dokonywane przy użyciu .

Korzystając z warunków **podobnych** i **nieJak,** `*` należy podać symbol wieloznaczny w wartości.
Wartość nie powinna mieć więcej niż `*`jednego symbolu wieloznacznego.

Korzystając z warunków **dopasowania** i **notMatch,** podaj, `#` aby dopasować cyfrę, `?` dla litery, `.` aby dopasować dowolny znak i dowolny inny znak, aby dopasować ten rzeczywisty znak. Podczas, **dopasowania** i **notMatch** są uwzględniane wielkość liter, wszystkie inne warunki, które oceniają _stringValue_ są bez uwzględniania wielkości liter. Alternatywy bez uwzględniania wielkości liter są dostępne w **dopasowaniuInsensitively** i **notMatchInsensitively**.

W wartości pola **tablicy \] aliasu każdy element w tablicy jest oceniany indywidualnie za pomocą logicznego i między elementami. \[ \* ** **and** Aby uzyskać więcej informacji, zobacz [Ocena \[ \* \] aliasu](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Pola

Warunki są tworzone przy użyciu pól. Pole dopasowuje właściwości ładunku żądania zasobu i opisuje stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu to nazwa zasobu poprzedzana przez nazwy zasobów nadrzędnych (na przykład "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Użyj **globalnego** dla zasobów, które są niezależną od lokalizacji.
- `identity.type`
  - Zwraca typ [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md) włączonej w zasobie.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasu obsługuje nazwy znaczników, które mają znaki interpunkcyjne, takie jak myślnik, kropka lub spacja.
  - Gdzie ** \<tagName\> ** jest nazwą tagu, aby sprawdzić poprawność warunku.
  - Przykłady: `tags['Acct.CostCenter']` gdzie **Acct.CostCenter** jest nazwą tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasu obsługuje nazwy znaczników, które mają apostrofy w nim przez ucieczkę z podwójnymi apostrofami.
  - Gdzie **\<' tagName\>'** jest nazwą tagu, aby sprawdzić poprawność warunku.
  - Przykład: `tags['''My.Apostrophe.Tag''']` gdzie **"My.Apostrophe.Tag"** jest nazwą tagu.
- aliasy właściwości - aby uzyskać listę, zobacz [Aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`i `tags[tag.with.dots]` nadal są akceptowalnymi sposobami deklarowania pola znaczników. Jednak preferowane wyrażenia są wymienione powyżej.

#### <a name="use-tags-with-parameters"></a>Używanie znaczników z parametrami

Wartość parametru można przekazać do pola znacznika. Przekazywanie parametru do pola znacznika zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym `concat` przykładzie jest używany do tworzenia wyszukiwania pola znaczników dla tagu o nazwie wartość parametru **tagName.** Jeśli ten tag nie istnieje, efekt **modyfikacji** jest używany do dodawania znacznika przy użyciu wartości tego samego `resourcegroup()` nazwanego zestawu znaczników w grupie zasobów nadrzędnych zasobów objętych inspekcją przy użyciu funkcji odnośnika.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Wartość

Warunki mogą być również tworzone przy użyciu **wartości**. **wartość** sprawdza warunki względem [parametrów,](#parameters) [obsługiwanych funkcji szablonu](#policy-functions)lub literałów.
**wartość** jest sparowana z dowolnym obsługiwanym [warunkiem](#conditions).

> [!WARNING]
> Jeśli wynikiem _funkcji szablonu_ jest błąd, ocena zasad kończy się niepowodzeniem. Nieudana ocena jest niejawną **odmową.** Aby uzyskać więcej informacji, zobacz [unikanie błędów szablonu](#avoiding-template-failures). Użyj [enforcementMode](./assignment-structure.md#enforcement-mode) **DoNotEnforce,** aby zapobiec wpływowi nieudanej oceny na nowe lub zaktualizowane zasoby podczas testowania i sprawdzania poprawności nowej definicji zasad.

#### <a name="value-examples"></a>Przykłady wartości

W tym przykładzie reguły zasad użyto **wartości** do porównania wyniku `resourceGroup()` funkcji `*netrg`i właściwości zwracanej **nazwy** z **podobnym** warunkiem . Reguła odrzuca wszelkie zasoby `Microsoft.Network/*` nie **typu** w żadnej `*netrg`grupie zasobów, której nazwa kończy się na .

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

W tym przykładzie reguły zasad użyto **wartości** do sprawdzenia, czy wynik wielu zagnieżdżonych funkcji **jest równy** `true`. Reguła odrzuca wszelkie zasoby, które nie mają co najmniej trzech tagów.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Unikanie błędów szablonu

Użycie _funkcji szablonu_ w **wartości** pozwala na wiele złożonych funkcji zagnieżdżonych. Jeśli wynikiem _funkcji szablonu_ jest błąd, ocena zasad kończy się niepowodzeniem. Nieudana ocena jest niejawną **odmową.** Przykład **wartości,** która nie powiedzie się w niektórych scenariuszach:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Powyższa reguła zasad używa [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) do porównania pierwszych trzech znaków **nazwy** z **abc**. Jeśli **nazwa** jest krótsza `substring()` niż trzy znaki, funkcja powoduje błąd. Ten błąd powoduje, że zasady stają się **efektem odmowy.**

Zamiast tego użyj funkcji [if(),](../../../azure-resource-manager/templates/template-functions-logical.md#if) aby sprawdzić, czy pierwsze trzy znaki **nazwy** są równe **abc,** nie zezwalając **na nazwę** krótszą niż trzy znaki, aby spowodować błąd:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

W polu Zmieniona `if()` reguła zasad sprawdza długość `substring()` **nazwy** przed próbą uzyskania wartości z mniej niż trzema znakami. Jeśli **nazwa** jest zbyt krótka, zwracana jest wartość "nie zaczyna się od abc" i porównywana z **abc**. Zasób o krótkiej nazwie, który nie zaczyna się od **abc** nadal nie reguły zasad, ale nie powoduje już błąd podczas oceny.

### <a name="count"></a>Liczba

Warunki, które liczą, ile członków tablicy w ładunku zasobu spełniają wyrażenie warunek można utworzyć za pomocą wyrażenia **count.** Typowe scenariusze sprawdzają, czy "co najmniej jeden z", "dokładnie jeden z", "wszystkie" lub "żaden z" członków tablicy spełnia warunek. **count** ocenia każdy [ \[ \* \] element członkowski tablicy aliasu](#understanding-the--alias) dla wyrażenia warunku i sumuje _wyniki prawdziwe,_ które są następnie porównywane z operatorem wyrażenia.

Struktura wyrażenia **count** jest:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Następujące właściwości są używane z **liczbą:**

- **count.field** (wymagane): Zawiera ścieżkę do tablicy i musi być aliasem tablicy. Jeśli brakuje tablicy, wyrażenie jest oceniane na _false_ bez uwzględnienia wyrażenia warunku.
- **count.where** (opcjonalnie): Wyrażenie warunku do indywidualnej oceny każdego [ \[ \* \] członka tablicy aliasu](#understanding-the--alias) **count.field**. Jeśli ta właściwość nie jest podana, wszystkie elementy członkowskie tablicy ze ścieżką "pole" są oceniane na _wartość true_. Wewnątrz tej właściwości można użyć dowolnego [warunku.](../concepts/definition-structure.md#conditions)
  [Operatory logiczne](#logical-operators) mogą być używane wewnątrz tej właściwości do tworzenia złożonych wymagań oceny.
- warunek (wymagane): Wartość jest porównywana z liczbą elementów, które spełniają wyrażenie warunku **count.where.** ** \<\> ** Należy użyć [warunku](../concepts/definition-structure.md#conditions) liczbowego.

#### <a name="count-examples"></a>Przykłady zliczania

Przykład 1: Sprawdź, czy tablica jest pusta

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Przykład 2: Sprawdź, czy tylko jeden element członkowski tablicy spełnia wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Przykład 3: Sprawdź, czy co najmniej jeden element członkowski tablicy spełnia wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Przykład 4: Sprawdź, czy wszystkie elementy członkowskie tablicy obiektów spełniają wyrażenie warunek

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Przykład 5: Sprawdź, czy wszystkie elementy członkowskie tablicy ciągów spełniają wyrażenie warunek

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Przykład 6: Użyj **pola** wewnątrz **wartości,** aby sprawdzić, czy wszystkie elementy członkowskie tablicy spełniają wyrażenie warunku

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Przykład 7: Sprawdź, czy co najmniej jeden element członkowski tablicy pasuje do wielu właściwości w wyrażeniu warunek

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Efekt

Usługa Azure Policy obsługuje następujące typy efektów:

- **Dołącz**: dodaje zdefiniowany zestaw pól do żądania
- **Inspekcja:** generuje zdarzenie ostrzegawcze w dzienniku aktywności, ale nie rozwiązuje żądania
- **AuditIfNotExists**: generuje zdarzenie ostrzegawcze w dzienniku działań, jeśli powiązany zasób nie istnieje
- **Odmów**: generuje zdarzenie w dzienniku aktywności i kończy się niepowodzeniem żądanie
- **DeployIfIfNotExists**: wdraża powiązany zasób, jeśli jeszcze nie istnieje
- **Wyłączone**: nie ocenia zasobów pod kątem zgodności z regułą zasad
- **EnforceOPAConstraint** (wersja zapoznawcza): konfiguruje kontroler przyjęć open policy agent z Gatekeeper w wersji 3 dla samodzielnie zarządzanych klastrów Kubernetes na platformie Azure (wersja zapoznawcza)
- **EnforceRegoPolicy** (wersja zapoznawcza): konfiguruje kontroler przyjęć open policy agent z gatekeeper v2 w usłudze Azure Kubernetes
- **Modyfikowanie**: dodaje, aktualizuje lub usuwa zdefiniowane znaczniki z zasobu

Aby uzyskać szczegółowe informacje na temat każdego efektu, kolejności oceny, właściwości i przykładów, zobacz [Opis efektów zasad platformy Azure.](effects.md)

### <a name="policy-functions"></a>Funkcje zasad

Wszystkie [funkcje szablonu Menedżera zasobów](../../../azure-resource-manager/templates/template-functions.md) są dostępne do użycia w ramach reguły zasad, z wyjątkiem następujących funkcji i funkcji zdefiniowanych przez użytkownika:

- copyIndex()
- deployment()
- lista*
- newGuid()
- pickZones()
- dostawców()
- reference()
- identyfikator zasobu()
- zmiennych()

> [!NOTE]
> Te funkcje są `details.deployment.properties.template` nadal dostępne w ramach części wdrożenia szablonu w **deployIfNotExists** definicji zasad.

Następująca funkcja jest dostępna do użycia w regule zasad, ale różni się od użycia w szablonie usługi Azure Resource Manager:

- `utcNow()`- W przeciwieństwie do szablonu Menedżera zasobów, może to być używane poza defaultValue.
  - Zwraca ciąg ustawiony na bieżącą datę i godzinę w formacie DataTime uniwersalnej ISO 8601 'yyyy-MM-ddTHH:mm:ss.fffffZ'

Następujące funkcje są dostępne tylko w regułach zasad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [Wymagane] ciąg - Ciąg w uniwersalnym formacie DataTime ISO 8601 'yyyy-MM-ddTHH:mm:ss.fffffZ'
  - **numberOfDaysToAdd**: [Wymagane] liczba całkowita — liczba dni do dodania
- `field(fieldName)`
  - **nazwa pola:** ciąg [Wymagane] — nazwa [pola](#fields) do pobrania
  - Zwraca wartość tego pola z zasobu, który jest oceniany przez warunek If
  - `field`jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do odwoływania się do pól w zasobie, które są oceniane. Przykład tego zastosowania można zobaczyć w [deployIfNotExists przykład](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Zwraca wersję interfejsu API żądania, która wyzwoliła `2019-09-01`ocenę zasad (przykład: ).
    Będzie to wersja interfejsu API, która została użyta w żądaniu PUT/PATCH do oceny tworzenia/aktualizacji zasobów. Najnowsza wersja interfejsu API jest zawsze używana podczas oceny zgodności istniejących zasobów.
  
#### <a name="policy-function-example"></a>Przykład funkcji zasad

W tym przykładzie `resourceGroup` reguły zasad użyto funkcji zasobu, aby uzyskać `like` właściwość **name** w połączeniu z funkcją `concat` tablicy i obiektu w celu utworzenia warunku, który wymusza nazwę zasobu, aby rozpocząć od nazwy grupy zasobów.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliasy

Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości typu zasobu. Aliasy umożliwiają ograniczenie, jakie wartości lub warunki są dozwolone dla właściwości w zasobie. Każdy alias jest mapowyny do ścieżek w różnych wersjach interfejsu API dla danego typu zasobu. Podczas oceny zasad aparat zasad pobiera ścieżkę właściwości dla tej wersji interfejsu API.

Lista aliasów stale rośnie. Aby dowiedzieć się, jakie aliasy są obecnie obsługiwane przez usługę Azure Policy, użyj jednej z następujących metod:

- Rozszerzenie zasad platformy Azure dla programu Visual Studio Code (zalecane)

  Użyj [rozszerzenia zasad platformy Azure dla programu Visual Studio Code,](../how-to/extension-for-vscode.md) aby wyświetlać i odnajdywać aliasy właściwości zasobów.

  ![Rozszerzenie zasad platformy Azure dla kodu programu Visual Studio](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Użyj `project` operatora, aby wyświetlić **alias** zasobu.

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

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- INTERFEJS SPOCZYNEK / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Opis aliasu [*]

Kilka dostępnych aliasów ma wersję, która jest wyświetlana jako "normalna" nazwa, a drugą, która została ** \[ \* ** do niej dołączona. Przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normalny" reprezentuje pole jako pojedynczą wartość. To pole jest przeznaczone dla scenariuszy porównania dopasowania ścisłego, gdy cały zestaw wartości musi być dokładnie tak, jak zdefiniowano, nie więcej i nie mniej.

Alias ** \[ \* ** umożliwia porównanie z wartością każdego elementu w tablicy i określonych właściwości każdego elementu. Takie podejście umożliwia porównanie właściwości elementu dla scenariuszy "jeśli żaden z", "jeśli którykolwiek z" lub "jeśli wszystkie" scenariuszy. W przypadku bardziej złożonych scenariuszy należy użyć wyrażenia warunku [liczby.](#count) Za pomocą **ipRules\[\***, przykładem będzie sprawdzanie, że każda _akcja_ jest _Deny_, ale nie martwiąc się o ile reguł istnieje lub co to jest _wartość_ IP.
Ta przykładowa reguła sprawdza, czy wszystkie dopasowania **ipRules\[\*\].value** do **10.0.4.1** i stosuje **effectType** tylko wtedy, gdy nie znajdzie co najmniej jednego dopasowania:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Aby uzyskać więcej informacji, zobacz [ocenę aliasu\*[ ].](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

## <a name="initiatives"></a>Inicjatyw

Inicjatywy umożliwiają grupowanie kilku powiązanych definicji zasad w celu uproszczenia przydziałów i zarządzania, ponieważ pracujesz z grupą jako pojedynczym elementem. Na przykład definicje zasad tagowania pokrewnych można pogrupować w jedną inicjatywę. Zamiast przypisywać poszczególne zasady indywidualnie, należy zastosować inicjatywę.

> [!NOTE]
> Po przypisaniu inicjatywy nie można zmienić parametrów poziomu inicjatywy. W związku z tym zalecenie jest ustawienie **defaultValue** podczas definiowania parametru.

Poniższy przykład ilustruje sposób tworzenia inicjatywy obsługi `costCenter` `productName`dwóch tagów: i . Używa dwóch wbudowanych zasad, aby zastosować domyślną wartość znacznika.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
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

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](../how-to/remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
