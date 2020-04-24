---
title: Szczegóły struktury definicji zasad
description: Opisuje, w jaki sposób definicje zasad są używane do ustanawiania Konwencji dla zasobów platformy Azure w organizacji.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 329692c7220810af1d47fe7036e727bb49284810
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117288"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Azure Policy ustanawia konwencje dla zasobów. Definicje zasad opisują [warunki](#conditions) zgodności zasobów i wpływ, jaki należy wykonać w przypadku spełnienia warunku. Warunek porównuje [pole](#fields) właściwości zasobu z wymaganą wartością. Pola właściwości zasobu są dostępne za pomocą [aliasów](#aliases). Pole właściwości zasobu to pole pojedynczej wartości lub [Tablica](#understanding-the--alias) wielu wartości. Ocena warunku różni się w przypadku tablic.
Dowiedz się więcej o [warunkach](#conditions).

Dzięki zdefiniowaniu Konwencji można kontrolować koszty i łatwiej zarządzać zasobami. Można na przykład określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Lub można wymagać, aby wszystkie zasoby miały określony tag. Zasady są dziedziczone przez wszystkie zasoby podrzędne. Jeśli zasady są stosowane do grupy zasobów, mają zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat definicji zasad znajduje się tutaj:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Aby utworzyć definicję zasad, należy użyć formatu JSON. Definicja zasad zawiera elementy dla:

- tryb
- parameters
- Nazwa wyświetlana
- description
- Reguła zasad
  - Ocena logiczna
  - skuteczność

Na przykład poniższy kod JSON przedstawia zasady, które ograniczają miejsce wdrożenia zasobów:

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

Wszystkie przykłady Azure Policy znajdują się na [Azure Policy próbkach](../samples/index.md).

## <a name="mode"></a>Tryb

**Tryb** jest skonfigurowany w zależności od tego, czy zasady są ukierunkowane na Właściwość Azure Resource Manager lub Właściwość dostawcy zasobów.

### <a name="resource-manager-modes"></a>Tryby Menedżer zasobów

**Tryb** określa, które typy zasobów będą oceniane dla zasad. Obsługiwane są następujące tryby:

- `all`: Oceń grupy zasobów i wszystkie typy zasobów
- `indexed`: Szacuj tylko typy zasobów obsługujące Tagi i lokalizację

Na przykład zasób `Microsoft.Network/routeTables` obsługuje znaczniki i lokalizację i jest oceniany w obu trybach. Nie można jednak `Microsoft.Network/routeTables/routes` oznaczyć zasobu i nie jest on oceniany w `Indexed` trybie.

Zaleca się, aby `all` w większości przypadków ustawić **tryb** . Wszystkie definicje zasad utworzone za pomocą portalu używają `all` trybu. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz określić parametr **mode** ręcznie. Jeśli definicja zasad nie zawiera wartości **trybu** , jest ona domyślnie ustawiona `all` w Azure PowerShell i `null` w interfejsie wiersza polecenia platformy Azure. `null` Tryb jest taki sam jak w przypadku `indexed` korzystania z programu w celu zapewnienia zgodności z poprzednimi wersjami.

`indexed`należy używać podczas tworzenia zasad, które wymuszają Tagi lub lokalizacje. Chociaż nie jest to wymagane, uniemożliwiają one nie obsługujące tagów i lokalizacji, ponieważ nie są one zgodne z wynikami sprawdzania zgodności. Wyjątkiem są **grupy zasobów**. Zasady, które wymuszają lokalizację lub Tagi w grupie zasobów, **mode** powinny ustawiać `all` tryb na i jawnie `Microsoft.Resources/subscriptions/resourceGroups` wskazywać typ. Aby zapoznać się z przykładem, zobacz [Wymuszaj Tagi grupy zasobów](../samples/enforce-tag-rg.md). Aby uzyskać listę zasobów, które obsługują Tagi, zobacz [obsługa tagów dla zasobów platformy Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Tryby dostawcy zasobów (wersja zapoznawcza)

Następujące tryby dostawcy zasobów są obecnie obsługiwane w wersji zapoznawczej:

- `Microsoft.ContainerService.Data`Aby zarządzać regułami kontrolera przyjmowania w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md). Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceRegoPolicy](./effects.md#enforceregopolicy) .
- `Microsoft.Kubernetes.Data`do zarządzania samozarządzanymi klastrami Kubernetes Engine AKS na platformie Azure.
  Zasady korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceOPAConstraint](./effects.md#enforceopaconstraint) .
- `Microsoft.KeyVault.Data`Zarządzanie magazynami i certyfikatami w [Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Tryby dostawcy zasobów obsługują tylko wbudowane definicje zasad i nie obsługują inicjatyw w wersji zapoznawczej.

## <a name="parameters"></a>Parametry

Parametry pomagają uprościć zarządzanie zasadami przez zmniejszenie liczby definicji zasad. Pomyśl o parametrach, takich jak pola w formularzu `name`— `address`, `city`, `state`,. Te parametry zawsze pozostają takie same, ale ich wartości zmieniają się w zależności od poszczególnych wypełniania formularza.
Parametry działają w ten sam sposób podczas kompilowania zasad. Dzięki dołączeniu parametrów w definicji zasad można ponownie użyć tych zasad dla różnych scenariuszy przy użyciu różnych wartości.

> [!NOTE]
> Parametry można dodawać do istniejącej i przypisanej definicji. Nowy parametr musi zawierać właściwość **DefaultValue** . Zapobiega to poprawnej nieprawidłowemu przypisywaniu zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji zasad:

- **name**: Nazwa parametru. Używane przez funkcję `parameters` wdrażania w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [Używanie wartości parametru](#using-a-parameter-value).
- `type`: Określa, czy parametr jest **ciągiem**, **tablicą**, **obiektem**, **wartością logiczną**, **liczbą całkowitą**, **zmiennoprzecinkową**lub **DateTime**.
- `metadata`: Definiuje podwłaściwości używane głównie przez Azure Portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: Wyjaśnienie, do czego służy parametr. Może służyć do podania przykładów akceptowalnych wartości.
  - `displayName`: Przyjazna nazwa wyświetlana w portalu dla parametru.
  - `version`: (Opcjonalnie) śledzi szczegółowe informacje o wersji zawartości definicji zasad.

    > [!NOTE]
    > Usługa Azure Policy używa `version`, `preview`i `deprecated` właściwości do przekazywania poziomu zmiany do wbudowanej definicji zasad lub inicjatywy i stanu. Format `version` to: `{Major}.{Minor}.{Patch}`. Określone Stany, takie jak _przestarzałe_ lub _Podgląd_, są dołączane do `version` właściwości lub w innej właściwości jako **wartość logiczna**.

  - `category`: (Opcjonalnie) określa, w której kategorii Azure Portal zostanie wyświetlona definicja zasad.
  - `strongType`: (Opcjonalnie) używany podczas przypisywania definicji zasad za pomocą portalu. Zawiera listę kontekstową. Aby uzyskać więcej informacji, zobacz [strongtype](#strongtype).
  - `assignPermissions`: (Opcjonalnie) ustawiono _wartość true_ , aby Azure Portal utworzyć przypisania roli podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakresem przypisania. Istnieje jedno przypisanie roli w ramach zasad (lub definicji roli we wszystkich zasadach z inicjatywy). Wartość parametru musi być prawidłowym zasobem lub zakresem.
- `defaultValue`: (Opcjonalnie) ustawia wartość parametru w przypisaniu, jeśli nie podano wartości.
  Wymagane podczas aktualizowania istniejącej definicji zasad, która jest przypisana.
- `allowedValues`: (Opcjonalnie) zawiera tablicę wartości akceptowanych przez parametr podczas przypisywania.

Można na przykład zdefiniować definicję zasad, aby ograniczyć lokalizacje, w których można wdrożyć zasoby. Parametr dla tej definicji zasad może być **allowedLocations**. Ten parametr będzie używany przez każde przypisanie definicji zasad w celu ograniczenia akceptowanych wartości. Użycie **silnego** typu zapewnia ulepszone środowisko podczas kończenia przydziału w portalu:

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

### <a name="using-a-parameter-value"></a>Używanie wartości parametru

W regule zasad, należy odwołać się do parametrów `parameters` za pomocą następującej składni funkcji:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ten przykład odwołuje się do parametru **allowedLocations** , który został pokazany we [właściwościach parametrów](#parameter-properties).

### <a name="strongtype"></a>strongtype

We `metadata` właściwości można użyć **silnego** typu, aby udostępnić listę opcji dostępnych w ramach Azure Portal. **silntype** może być obsługiwanym _typem zasobu_ lub dozwoloną wartością. Aby określić, czy _Typ zasobu_ jest prawidłowy dla **silnego**elementu, użyj polecenie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Niektóre _typy zasobów_ , które nie są zwracane przez **Get-AzResourceProvider** , są obsługiwane. Są to:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Dozwolone wartości _typu_ niezwiązanego z typem " **strongtype** " to:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Lokalizacja definicji

Podczas tworzenia inicjatywy lub zasad należy określić lokalizację definicji. Lokalizacja definicji musi być grupą zarządzania lub subskrypcją. Ta lokalizacja określa zakres, do którego można przypisać inicjatywę lub zasady. Zasoby muszą być bezpośrednimi elementami członkowskimi lub elementami podrzędnymi w hierarchii lokalizacji definicji do przypisywania.

Jeśli lokalizacja definicji jest:

- Tylko zasoby z **subskrypcją** w ramach tej subskrypcji mogą być przypisane do zasad.
- Zasady mogą być przypisywane tylko zasobom należącym do **grupy** zarządzania w ramach podrzędnych grup administracyjnych i subskrypcji podrzędnych. Jeśli planujesz zastosowanie definicji zasad do kilku subskrypcji, lokalizacja musi być grupą zarządzania, która zawiera te subskrypcje.

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować definicję zasad i podać kontekst, który ma być używany. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

> [!NOTE]
> Podczas tworzenia lub aktualizowania definicji zasad, **identyfikatora**, **typu**i **nazwy** są zdefiniowane przez właściwości zewnętrzne w formacie JSON i nie są wymagane w pliku JSON. Pobieranie definicji zasad za pomocą zestawu SDK zwraca **Identyfikator**, **Typ**i właściwości **nazwy** w ramach JSON, ale każda z nich jest informacjami tylko do odczytu związanymi z definicją zasad.

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z elementów **if** i **then** . W bloku **if** definiuje się jeden lub więcej warunków, które określają, kiedy zasady są wymuszane. Operatory logiczne można stosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

W bloku **then** definiujesz efekt, **który ma miejsce, gdy warunki są** spełnione.

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

**Nie** składnia odwraca wynik warunku. Składnia **allOf** (podobna do operacji logicznej **and** ) wymaga, aby wszystkie warunki były prawdziwe. Składnia **anyOf** (podobna do operacji logicznej **or** ) wymaga co najmniej jednego warunku.

Operatory logiczne można zagnieżdżać. W poniższym przykładzie przedstawiono operację **not** , która jest zagnieżdżona w ramach operacji **allOf** .

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

Warunek oblicza, czy **pole** lub metoda dostępu do **wartości** spełniają określone kryteria. Obsługiwane są następujące warunki:

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

W przypadku **mniej**, **lessOrEquals**, **większych**i **greaterOrEquals**, jeśli typ właściwości nie jest zgodny z typem warunku, zostanie zgłoszony błąd. Porównania ciągów są wykonywane przy `InvariantCultureIgnoreCase`użyciu.

W przypadku używania warunków **like** i **notLike** , w wartości można podać `*` symbol wieloznaczny.
Wartość nie może mieć więcej niż jednego symbolu `*`wieloznacznego.

W przypadku używania warunków **Match** i **notMatch** , podaj `#` , aby dopasować cyfrę `?` do litery, `.` aby dopasować dowolny znak, i dowolny inny znak, aby dopasować go do rzeczywistego znaku. While, **Match** i **notMatch** uwzględnia wielkość liter, a wszystkie inne warunki, które szacują _stringValue_ , nie uwzględniają wielkości liter. Alternatywy bez uwzględniania wielkości liter są dostępne w **matchInsensitively** i **notMatchInsensitively**.

W wartości pola **and** tablicy ** \] aliasu każdy element w tablicy jest obliczany indywidualnie przy użyciu koniunkcji logicznej i między \[ \* ** elementami. Aby uzyskać więcej informacji, zobacz [ocenianie \[ \* \] aliasu](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Pola

Warunki są tworzone za pomocą pól. Pole jest zgodne z właściwościami w ładunku żądania zasobu i opisuje stan zasobu.

Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu to nazwa zasobu poprzedzona przez wszystkie nadrzędne nazwy zasobów (na przykład "nie dotyczy".
- `kind`
- `type`
- `location`
  - Użyj **globalnych** dla zasobów, które są lokalizacją niezależny od.
- `identity.type`
  - Zwraca typ [zarządzanej tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) włączonej dla zasobu.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które mają znaki interpunkcyjne, takie jak łącznik, kropka lub spacja.
  - Gdzie ** \<TagName\> ** jest nazwą tagu, aby zweryfikować warunek.
  - Przykłady: `tags['Acct.CostCenter']` gdzie **ACCT. CostCenter** jest nazwą tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które zawierają apostrofy przez ucieczki z podwójnym apostrofem.
  - Gdzie **"\<TagName\>"** jest nazwą tagu, aby zweryfikować warunek.
  - Przykład: `tags['''My.Apostrophe.Tag''']` gdzie **"My. apostrof. tag"** jest nazwą tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`i `tags[tag.with.dots]` są nadal akceptowalnymi sposobami deklarowania pola Tagi. Jednak preferowane wyrażenia są wymienione powyżej.

#### <a name="use-tags-with-parameters"></a>Używanie tagów z parametrami

Wartość parametru może być przekazanie do pola tagu. Przekazywanie parametru do pola tagu zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym przykładzie `concat` jest używany do tworzenia wyszukiwania pól tagów dla tagu o nazwie **TagName** parametru. Jeśli ten tag nie istnieje, efekt **modyfikacji** jest używany do dodawania znacznika przy użyciu wartości tego samego nazwanego tagu ustawionego w nadrzędnej grupie zasobów poddane inspekcji przy użyciu funkcji `resourcegroup()` wyszukiwania.

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

Warunki mogą być również tworzone przy użyciu **wartości**. **wartość** sprawdza warunki względem [parametrów](#parameters), [obsługiwanych funkcji szablonów](#policy-functions)lub literałów.
**wartość** jest sparowana z dowolnym obsługiwanym [warunkiem](#conditions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Aby uzyskać więcej informacji, zobacz [unikanie niepowodzeń związanych z szablonami](#avoiding-template-failures). Użyj [wymuszania](./assignment-structure.md#enforcement-mode) elementu **DoNotEnforce** , aby zapobiec wpływowi oceny zakończonej niepowodzeniem na nowe lub zaktualizowane zasoby podczas testowania i sprawdzania poprawności nowej definicji zasad.

#### <a name="value-examples"></a>Przykłady wartości

Ta reguła zasad używa tego przykładu **wartości** do porównania wyniku `resourceGroup()` funkcji i zwróconej właściwości **name** do warunku **like** `*netrg`. Reguła odmówi dowolnego zasobu, którego typ nie `Microsoft.Network/*` jest **typem** w żadnej grupie zasobów, której nazwy `*netrg`kończą się.

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

Ta reguła zasad używa tego przykładu **wartości** do sprawdzenia, czy wynik wielu zagnieżdżonych funkcji **równa** `true`się. Reguła odmówi wszelkich zasobów, które nie mają co najmniej trzech tagów.

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

#### <a name="avoiding-template-failures"></a>Unikanie niepowodzeń szablonów

Użycie _funkcji szablonu_ w **wartości** pozwala na wiele złożonych zagnieżdżonych funkcji. Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Przykład **wartości** , która kończy się niepowodzeniem w niektórych scenariuszach:

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

Powyższa Przykładowa reguła zasad używa [podciągu ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) , aby porównać pierwsze trzy znaki **nazwy** z **ABC**. Jeśli **Nazwa** jest krótsza niż trzy znaki, `substring()` funkcja powoduje błąd. Ten błąd powoduje, że zasady stają się efektem **odmowy** .

Zamiast tego należy użyć funkcji [if ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) , aby sprawdzić, czy pierwsze trzy znaki **nazwy** są równe **ABC** bez dopuszczania **nazwy** krótszej niż trzy znaki w celu spowodowania błędu:

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

Po zmodyfikowaniu reguły zasad `if()` Sprawdź długość **nazwy** przed próbą uzyskania `substring()` wartości przy użyciu mniej niż trzech znaków. Jeśli **Nazwa** jest za krótka, zamiast tego jest zwracana wartość "nie zaczyna się od ABC" i porównana z opcją **ABC**. Zasób z krótką nazwą, która nie zaczyna się od **ABC** , nadal kończy się niepowodzeniem reguły zasad, ale nie powoduje już błędu podczas obliczania.

### <a name="count"></a>Liczba

Warunki określające, ile elementów członkowskich tablicy w ładunku zasobów spełnia wyrażenie warunku, można utworzyć za pomocą wyrażenia **Count** . Typowe scenariusze sprawdzają, czy "co najmniej jeden z", "dokładnie jeden z", "All" lub "none" elementów członkowskich tablicy spełnia warunek. **licznik** oblicza każdy _true_ [ \[ \* \] ](#understanding-the--alias) element członkowski tablicy aliasów dla wyrażenia warunku i sumuje prawdziwe wyniki, które są następnie porównywane z operatorem wyrażenia. Wyrażenia **Count** mogą być dodawane do jednej definicji **Klasa policyrule** do 3 razy.

Struktura wyrażenia **Count** jest:

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

Następujące właściwości są używane z funkcją **Count**:

- **Count. pole** (wymagane): zawiera ścieżkę do tablicy i musi być aliasem tablicy. Jeśli brakuje tablicy, wyrażenie jest oceniane na _wartość false_ bez uwzględniania wyrażenia warunku.
- **Count. WHERE** (opcjonalnie): wyrażenie warunku do pojedynczej ocenia każdego [ \[ \* \] ](#understanding-the--alias) elementu członkowskiego tablicy aliasów w **polu Count.** Jeśli ta właściwość nie jest określona, wszystkie elementy członkowskie tablicy ze ścieżką "pole" są oceniane na _wartość true_. Dowolny [warunek](../concepts/definition-structure.md#conditions) może być używany wewnątrz tej właściwości.
  [Operatory logiczne](#logical-operators) mogą być używane wewnątrz tej właściwości, aby utworzyć złożone wymagania dotyczące oceny.
- warunek (wymagany): wartość jest porównywana z liczbą elementów, które osiągnęły liczbę **.** wyrażenie warunku WHERE. **\> \<** Należy użyć [warunku](../concepts/definition-structure.md#conditions) liczbowego.

#### <a name="count-examples"></a>Liczba przykładów

Przykład 1: sprawdzenie, czy tablica jest pusta

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Przykład 2: Sprawdź tylko jeden element członkowski tablicy, aby spełnić wyrażenie warunku

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

Przykład 3: Sprawdź co najmniej jeden element członkowski tablicy, aby spełnić wyrażenie warunku

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

Przykład 4: Sprawdź, czy wszystkie elementy członkowskie tablicy obiektów spełniają wyrażenie warunku

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

Przykład 5: Sprawdź, czy wszystkie elementy członkowskie tablicy ciągów spełniają wyrażenie warunku

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

Przykład 6: Użyj **pola** wewnątrz **wartości** , aby sprawdzić, czy wszystkie elementy członkowskie tablicy spełniają wyrażenie warunku

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

Przykład 7: Sprawdź, czy co najmniej jeden element członkowski tablicy jest zgodny z wieloma właściwościami w wyrażeniu warunku

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

Azure Policy obsługuje następujące typy efektów:

- **Append**: Dodaje zdefiniowany zestaw pól do żądania
- **Inspekcja**: generuje zdarzenie ostrzegawcze w dzienniku aktywności, ale nie kończy się niepowodzeniem żądania
- **AuditIfNotExists**: generuje zdarzenie ostrzegawcze w dzienniku aktywności, jeśli powiązany zasób nie istnieje
- **Odmów**: generuje zdarzenie w dzienniku aktywności i kończy się niepowodzeniem żądania
- **DeployIfNotExists**: wdraża powiązane zasoby, jeśli jeszcze nie istnieją
- **Wyłączone**: nie oblicza zasobów pod kątem zgodności z regułą zasad
- **EnforceOPAConstraint** (wersja zapoznawcza): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v3 dla samozarządzanej klastrów Kubernetes na platformie Azure (wersja zapoznawcza)
- **EnforceRegoPolicy** (wersja zapoznawcza): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v2 w usłudze Azure Kubernetes Service
- **Modyfikowanie**: dodaje, aktualizuje lub usuwa zdefiniowane znaczniki z zasobu

Aby uzyskać szczegółowe informacje na temat każdego efektu, kolejności oceny, właściwości i przykładów, zobacz [opis efektów Azure Policy](effects.md).

### <a name="policy-functions"></a>Funkcje zasad

Wszystkie [funkcje szablonu Menedżer zasobów](../../../azure-resource-manager/templates/template-functions.md) są dostępne do użycia w regule zasad, z wyjątkiem następujących funkcji i funkcji zdefiniowanych przez użytkownika:

- Funkcji copyindex ()
- wdrożenie ()
- staw
- newGuid()
- pickZones()
- dostawcy ()
- Reference ()
- resourceId ()
- zmienne ()

> [!NOTE]
> Te funkcje są nadal dostępne w `details.deployment.properties.template` części wdrożenia szablonu w definicji zasad **deployIfNotExists** .

Następująca funkcja jest dostępna do użycia w regule zasad, ale różni się od użycia w szablonie Azure Resource Manager:

- `utcNow()`— W przeciwieństwie do szablonu Menedżer zasobów, można go użyć poza elementem DefaultValue.
  - Zwraca ciąg, który jest ustawiony na bieżącą datę i godzinę w formacie uniwersalnego ISO 8601 DateTime-MM-DDTgg: mm: SS. fffffffZ

Następujące funkcje są dostępne tylko w regułach zasad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [Required] ciąg ciągu w formacie daty/godziny uniwersalnego ISO 8601 "RRRR-MM-DDTgg: mm: SS. fffffffZ"
  - **numberOfDaysToAdd**: [Required] liczba dni do dodania
- `field(fieldName)`
  - **FieldName**: [Required] — nazwa [pola](#fields) do pobrania
  - Zwraca wartość tego pola z zasobu, który jest obliczany przez warunek if
  - `field`jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do odwołań do pól w analizowanym zasobie. Przykład tego zastosowania można zobaczyć w [przykładzie DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Zwraca wersję interfejsu API żądania, które spowodowało wyzwolenie oceny zasad ( `2019-09-01`przykład:).
    Będzie to wersja interfejsu API, która została użyta w żądaniu PUT/PATCH do oceny przy tworzeniu/aktualizowaniu zasobów. Najnowsza wersja interfejsu API jest zawsze używana podczas oceny zgodności dla istniejących zasobów.
  
#### <a name="policy-function-example"></a>Przykład funkcji zasad

Ten przykład `resourceGroup` reguły używa funkcji zasobów, aby uzyskać Właściwość **name** połączonej z funkcją `concat` Array i Object, aby skompilować `like` warunek, który wymusza nazwę zasobu, aby rozpocząć od nazwy grupy zasobów.

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

Aliasy właściwości są używane do uzyskiwania dostępu do określonych właściwości dla typu zasobu. Aliasy umożliwiają ograniczenie wartości lub warunków dozwolonych dla właściwości zasobu. Każdy alias mapuje do ścieżek w różnych wersjach interfejsu API dla danego typu zasobu. Podczas obliczania zasad aparat zasad Pobiera ścieżkę właściwości dla tej wersji interfejsu API.

Lista aliasów zawsze rośnie. Aby dowiedzieć się, jakie aliasy są obecnie obsługiwane przez Azure Policy, należy użyć jednej z następujących metod:

- Rozszerzenie Azure Policy dla Visual Studio Code (zalecane)

  Użyj [rozszerzenia Azure Policy](../how-to/extension-for-vscode.md) , aby Visual Studio Code do wyświetlania i odnajdywania aliasów dla właściwości zasobów.

  ![Azure Policy rozszerzenie dla Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Użyj operatora `project` , aby wyświetlić **alias** zasobu.

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

- Interfejs API REST/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informacje o aliasie [*]

Kilka dostępnych aliasów ma wersję, która jest wyświetlana jako nazwa "normal" i inna, która została ** \[ \* ** do niej dołączona. Przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" reprezentuje pole jako pojedynczą wartość. To pole jest przeznaczone do dokładnego dopasowania scenariuszy porównywania, gdy cały zestaw wartości musi być dokładnie zdefiniowany, nie więcej i nie rzadziej.

** \[ Alias umożliwia porównanie wartości poszczególnych elementów w tablicy i określonych właściwości \* ** każdego elementu. Takie podejście umożliwia porównanie właściwości elementów dla elementu "If None of", "if any" lub "If all of". W przypadku bardziej złożonych scenariuszy Użyj wyrażenia warunku [Count](#count) . Za **pomocą\[\*ipRules**, przykładem będzie sprawdzanie, czy każda _Akcja_ jest _odrzucana_, ale nie martw się o liczbę istniejących reguł lub _wartość_ IP.
Ta przykładowa reguła sprawdza, czy dla dowolnych dopasowań **\[\*\]ipRules. Value** do **10.0.4.1** i stosuje wartość **effecttype** tylko wtedy, gdy nie znajdzie co najmniej jednego dopasowania:

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

Aby uzyskać więcej informacji, zobacz [ocenianie aliasu\*[]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Inicjatyw

Inicjatywy umożliwiają grupowanie kilku powiązanych definicji zasad w celu uproszczenia przypisań i zarządzania, ponieważ pracujesz z grupą jako pojedynczy element. Na przykład można pogrupować powiązane definicje zasad oznakowania do jednej inicjatywy. Zamiast przypisywać poszczególne zasady indywidualnie, należy zastosować inicjatywę.

> [!NOTE]
> Po przypisaniu inicjatywy nie można zmienić parametrów poziomu inicjatywy. Ze względu na to zalecenie polega na ustawieniu elementu **DefaultValue** podczas definiowania parametru.

Poniższy przykład ilustruje sposób tworzenia inicjatywy do obsługi dwóch tagów: `costCenter` i. `productName` Używa dwóch wbudowanych zasad, aby zastosować domyślną wartość tagu.

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

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
