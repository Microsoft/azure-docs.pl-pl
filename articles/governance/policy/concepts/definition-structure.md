---
title: Szczegóły struktury definicji zasad
description: Opisuje, w jaki sposób definicje zasad są używane do ustanawiania Konwencji dla zasobów platformy Azure w organizacji.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: cebba214671cfab75a3f44720578b51febacdfcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215072"
---
# <a name="azure-policy-definition-structure"></a>Struktura definicji zasad platformy Azure

Azure Policy ustanawia konwencje dla zasobów. Definicje zasad opisują [warunki](#conditions) zgodności zasobów i wpływ, jaki należy wykonać w przypadku spełnienia warunku. Warunek porównuje [pole](#fields) właściwości zasobu lub [wartość](#value) z wymaganą wartością. Pola właściwości zasobu są dostępne za pomocą [aliasów](#aliases). Gdy pole właściwości zasobu jest tablicą, można użyć specjalnego [aliasu tablicy](#understanding-the--alias) do wybrania wartości ze wszystkich elementów członkowskich tablicy i zastosować warunek do każdego z nich. Dowiedz się więcej o [warunkach](#conditions).

Dzięki zdefiniowaniu Konwencji można kontrolować koszty i łatwiej zarządzać zasobami. Można na przykład określić, że dozwolone są tylko niektóre typy maszyn wirtualnych. Lub można wymagać, aby zasoby miały określony tag. Przypisania zasad są dziedziczone przez zasoby podrzędne. Jeśli przypisanie zasad zostanie zastosowane do grupy zasobów, ma zastosowanie do wszystkich zasobów w tej grupie zasobów.

Schemat _Klasa policyrule_ definicji zasad znajduje się tutaj: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Aby utworzyć definicję zasad, należy użyć formatu JSON. Definicja zasad zawiera elementy dla:

- Nazwa wyświetlana
- description (opis)
- tryb
- metadane
- parameters
- Reguła zasad
  - Ocena logiczna
  - skuteczność

Na przykład poniższy kod JSON przedstawia zasady, które ograniczają miejsce wdrożenia zasobów:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy wbudowane i wzorce są [Azure Policy próbkami](../samples/index.md).

## <a name="display-name-and-description"></a>Nazwa wyświetlana i opis

Użyj **DisplayName** i **Description** , aby zidentyfikować definicję zasad i podać kontekst, który ma być używany. **Nazwa wyświetlana** ma maksymalną długość _128_ znaków i **Opis** ma maksymalną długość _512_ znaków.

> [!NOTE]
> Podczas tworzenia lub aktualizowania definicji zasad, **identyfikatora**, **typu** i **nazwy** są zdefiniowane przez właściwości zewnętrzne w formacie JSON i nie są wymagane w pliku JSON. Pobieranie definicji zasad za pomocą zestawu SDK zwraca **Identyfikator**, **Typ** i właściwości **nazwy** w ramach JSON, ale każda z nich jest informacjami tylko do odczytu związanymi z definicją zasad.

## <a name="type"></a>Typ

Podczas gdy nie można ustawić właściwości **Type** , istnieją trzy wartości, które są zwracane przez zestaw SDK i widoczne w portalu:

- `Builtin`: Te definicje zasad są udostępniane i obsługiwane przez firmę Microsoft.
- `Custom`: Ta wartość jest dostępna dla wszystkich definicji zasad utworzonych przez klientów.
- `Static`: Wskazuje definicję zasad [zgodności z przepisami](./regulatory-compliance.md) firmy Microsoft . Wyniki zgodności dla tych definicji zasad są wynikami audytów innych firm w ramach infrastruktury firmy Microsoft. W Azure Portal ta wartość jest czasami wyświetlana jako **zarządzana przez firmę Microsoft**. Aby uzyskać więcej informacji, zobacz [współdzielona odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Tryb

**Tryb** jest skonfigurowany w zależności od tego, czy zasady są ukierunkowane na Właściwość Azure Resource Manager lub Właściwość dostawcy zasobów.

### <a name="resource-manager-modes"></a>Tryby Menedżer zasobów

**Tryb** określa, które typy zasobów są oceniane dla definicji zasad. Obsługiwane są następujące tryby:

- `all`: Oceń grupy zasobów, subskrypcje i wszystkie typy zasobów
- `indexed`: Szacuj tylko typy zasobów obsługujące Tagi i lokalizację

Na przykład zasób `Microsoft.Network/routeTables` obsługuje znaczniki i lokalizację i jest oceniany w obu trybach. `Microsoft.Network/routeTables/routes`Nie można jednak oznaczyć zasobu i nie jest on oceniany w `Indexed` trybie.

Zaleca się, aby  `all` w większości przypadków ustawić tryb. Wszystkie definicje zasad utworzone za pomocą portalu używają `all` trybu. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz określić parametr **mode** ręcznie. Jeśli definicja zasad nie zawiera wartości **trybu** , jest ona domyślnie ustawiona `all` w Azure PowerShell i `null` w interfejsie wiersza polecenia platformy Azure. `null`Tryb jest taki sam jak w przypadku korzystania `indexed` z programu w celu zapewnienia zgodności z poprzednimi wersjami.

`indexed` należy używać podczas tworzenia zasad, które wymuszają Tagi lub lokalizacje. Chociaż nie jest to wymagane, uniemożliwiają one nie obsługujące tagów i lokalizacji, ponieważ nie są one zgodne z wynikami sprawdzania zgodności. Wyjątkiem są **grupy zasobów** i **subskrypcje**. Definicje zasad, które wymuszają lokalizację lub Tagi w grupie zasobów lub subskrypcji, powinny ustawiać **tryb** na `all` i przeznaczony dla tego `Microsoft.Resources/subscriptions/resourceGroups` `Microsoft.Resources/subscriptions` typu. Aby zapoznać się z przykładem, zobacz [wzorzec: Tags — przykład #1](../samples/pattern-tags.md). Aby uzyskać listę zasobów, które obsługują Tagi, zobacz [obsługa tagów dla zasobów platformy Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Tryby dostawcy zasobów

Następujący tryb dostawcy zasobów jest w pełni obsługiwany:

- `Microsoft.Kubernetes.Data` do zarządzania klastrami Kubernetes na platformie Azure lub w niej. Definicje używające tego trybu dostawcy zasobów służą do _inspekcji_, _odmowy_ i _wyłączania_. Użycie efektu [EnforceOPAConstraint](./effects.md#enforceopaconstraint) jest _przestarzałe_.

Następujące tryby dostawcy zasobów są obecnie obsługiwane jako **wersja zapoznawcza**:

- `Microsoft.ContainerService.Data` Aby zarządzać regułami kontrolera przyjmowania w [usłudze Azure Kubernetes](../../../aks/intro-kubernetes.md). Definicje korzystające z tego trybu dostawcy zasobów **muszą** używać efektu [EnforceRegoPolicy](./effects.md#enforceregopolicy) . Ten tryb jest _przestarzały_.
- `Microsoft.KeyVault.Data` Zarządzanie magazynami i certyfikatami w [Azure Key Vault](../../../key-vault/general/overview.md). Aby uzyskać więcej informacji na temat tych definicji zasad, zobacz [integrowanie Azure Key Vault z Azure Policy](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Tryby dostawcy zasobów obsługują tylko wbudowane definicje zasad i nie obsługują [wykluczeń](./exemption-structure.md).

## <a name="metadata"></a>Metadane

Właściwość opcjonalna `metadata` przechowuje informacje o definicji zasad. Klienci mogą definiować wszelkie właściwości i wartości przydatne w organizacji w programie `metadata` . Istnieją jednak _typowe_ właściwości, które są używane przez Azure Policy i wbudowane. Każda `metadata` Właściwość ma limit 1024 znaków.

### <a name="common-metadata-properties"></a>Wspólne właściwości metadanych

- `version` (ciąg): śledzi szczegółowe informacje o wersji zawartości definicji zasad.
- `category` (ciąg): określa, w której kategorii Azure Portal jest wyświetlana definicja zasad.
- `preview` (wartość logiczna): prawda lub FAŁSZ flagi dla Jeśli definicja zasad jest w _wersji zapoznawczej_.
- `deprecated` (wartość logiczna): flaga true lub false, jeśli definicja zasad została oznaczona jako _przestarzała_.

> [!NOTE]
> Usługa Azure Policy używa `version` , `preview` i `deprecated` właściwości do przekazywania poziomu zmiany do wbudowanej definicji zasad lub inicjatywy i stanu. Format `version` to: `{Major}.{Minor}.{Patch}` . Określone Stany, takie jak _przestarzałe_ lub _Podgląd_, są dołączane do `version` właściwości lub w innej właściwości jako **wartość logiczna**. Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Policy wersje wbudowane, zobacz [wbudowana wersja](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parametry

Parametry pomagają uprościć zarządzanie zasadami przez zmniejszenie liczby definicji zasad. Pomyśl o parametrach, takich jak pola w formularzu —, `name` `address` , `city` , `state` . Te parametry zawsze pozostają takie same, ale ich wartości zmieniają się w zależności od poszczególnych wypełniania formularza.
Parametry działają w ten sam sposób podczas kompilowania zasad. Dzięki dołączeniu parametrów w definicji zasad można ponownie użyć tych zasad dla różnych scenariuszy przy użyciu różnych wartości.

> [!NOTE]
> Parametry można dodawać do istniejącej i przypisanej definicji. Nowy parametr musi zawierać właściwość **DefaultValue** . Zapobiega to poprawnej nieprawidłowemu przypisywaniu zasad lub inicjatywy.

### <a name="parameter-properties"></a>Właściwości parametru

Parametr ma następujące właściwości, które są używane w definicji zasad:

- `name`: Nazwa parametru. Używane przez `parameters` funkcję wdrażania w ramach reguły zasad. Aby uzyskać więcej informacji, zobacz [Używanie wartości parametru](#using-a-parameter-value).
- `type`: Określa, czy parametr jest **ciągiem**, **tablicą**, **obiektem**, **wartością logiczną**, **liczbą całkowitą**, **zmiennoprzecinkową** lub **DateTime**.
- `metadata`: Definiuje podwłaściwości używane głównie przez Azure Portal do wyświetlania informacji przyjaznych dla użytkownika:
  - `description`: Wyjaśnienie, do czego służy parametr. Może służyć do podania przykładów akceptowalnych wartości.
  - `displayName`: Przyjazna nazwa wyświetlana w portalu dla parametru.
  - `strongType`: (Opcjonalnie) używany podczas przypisywania definicji zasad za pomocą portalu. Zawiera listę kontekstową. Aby uzyskać więcej informacji, zobacz [strongtype](#strongtype).
  - `assignPermissions`: (Opcjonalnie) ustawiono _wartość true_ , aby Azure Portal utworzyć przypisania roli podczas przypisywania zasad. Ta właściwość jest przydatna w przypadku, gdy chcesz przypisać uprawnienia poza zakresem przypisania. Istnieje jedno przypisanie roli w ramach zasad (lub dla każdej roli we wszystkich zasadach w ramach inicjatywy). Wartość parametru musi być prawidłowym zasobem lub zakresem.
- `defaultValue`: (Opcjonalnie) ustawia wartość parametru w przypisaniu, jeśli nie podano wartości.
  Wymagane podczas aktualizowania istniejącej definicji zasad, która jest przypisana.
- `allowedValues`: (Opcjonalnie) zawiera tablicę wartości akceptowanych przez parametr podczas przypisywania. W porównaniach dozwolonych wartości rozróżniana jest wielkość liter. 

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

W regule zasad, należy odwołać się do parametrów za pomocą następującej `parameters` składni funkcji:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ten przykład odwołuje się do parametru **allowedLocations** , który został pokazany we [właściwościach parametrów](#parameter-properties).

### <a name="strongtype"></a>strongtype

We `metadata` właściwości można użyć **silnego** typu, aby udostępnić listę opcji dostępnych w ramach Azure Portal. **silntype** może być obsługiwanym _typem zasobu_ lub dozwoloną wartością. Aby określić, czy _Typ zasobu_ jest prawidłowy dla **silnego** elementu, użyj polecenie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). Format _typu zasobu_ **strongtype** ma wartość `<Resource Provider>/<Resource Type>` . Na przykład `Microsoft.Network/virtualNetworks/subnets`.

Niektóre _typy zasobów_ , które nie są zwracane przez **Get-AzResourceProvider** , są obsługiwane. Te typy są następujące:

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

- Tylko zasoby z **subskrypcją** w ramach tej subskrypcji mogą mieć przypisaną definicję zasad.
- Do definicji zasad można przypisać tylko zasoby **grupy** zarządzania należące do podrzędnych grup zarządzania i subskrypcji podrzędnych. Jeśli planujesz zastosowanie definicji zasad do kilku subskrypcji, lokalizacja musi być grupą zarządzania, która zawiera każdą subskrypcję.

Aby uzyskać więcej informacji, zobacz [Opis zakresu w Azure Policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Reguła zasad

Reguła zasad składa się z elementów **if** i **then** . W bloku **if** definiuje się jeden lub więcej warunków, które określają, kiedy zasady są wymuszane. Operatory logiczne można stosować do tych warunków, aby precyzyjnie zdefiniować scenariusz dla zasad.

W bloku **then** definiujesz efekt, **który ma miejsce, gdy warunki są** spełnione.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
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

Warunek oblicza, czy wartość spełnia określone kryteria. Obsługiwane są następujące warunki:

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
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

W przypadku **mniej**, **lessOrEquals**, **większych** i **greaterOrEquals**, jeśli typ właściwości nie jest zgodny z typem warunku, zostanie zgłoszony błąd. Porównania ciągów są wykonywane przy użyciu `InvariantCultureIgnoreCase` .

W przypadku używania warunków **like** i **notLike** , w wartości można podać symbol wieloznaczny `*` . Wartość nie może mieć więcej niż jednego symbolu wieloznacznego `*` .

W przypadku używania warunków **Match** i **notMatch** , podaj, `#` Aby dopasować cyfrę do `?` litery, `.` Aby dopasować dowolny znak, i dowolny inny znak, aby dopasować go do rzeczywistego znaku. Chociaż **dopasowuje** i **notMatch** uwzględnia wielkość liter, wszystkie inne warunki, które szacują _stringValue_ , nie uwzględniają wielkości liter. Alternatywy bez uwzględniania wielkości liter są dostępne w **matchInsensitively** i **notMatchInsensitively**.

### <a name="fields"></a>Pola

Warunki, które sprawdzają, czy wartości właściwości w ładunku żądania zasobu spełniają określone kryteria, można utworzyć przy użyciu wyrażenia **pola** . Obsługiwane są następujące pola:

- `name`
- `fullName`
  - Zwraca pełną nazwę zasobu. Pełna nazwa zasobu to nazwa zasobu poprzedzona przez wszystkie nadrzędne nazwy zasobów (na przykład "nie dotyczy".
- `kind`
- `type`
- `location`
  - Pola lokalizacji są znormalizowane w celu obsługi różnych formatów. Na przykład `East US 2` jest traktowane jako równe `eastus2` .
  - Użyj **globalnych** dla zasobów, które są lokalizacją niezależny od.
- `id`
  - Zwraca identyfikator zasobu, który jest oceniany.
  - Przykład: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Zwraca typ [zarządzanej tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md) włączonej dla zasobu.
- `tags`
- `tags['<tagName>']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które mają znaki interpunkcyjne, takie jak łącznik, kropka lub spacja.
  - Gdzie **\<tagName\>** jest nazwą tagu do walidacji warunku.
  - Przykłady: `tags['Acct.CostCenter']` gdzie **ACCT. CostCenter** jest nazwą tagu.
- `tags['''<tagName>''']`
  - Ta składnia nawiasów umożliwia obsługę nazw tagów, które zawierają apostrofy przez ucieczki z podwójnym apostrofem.
  - Gdzie **" \<tagName\> "** jest nazwą tagu, aby zweryfikować warunek.
  - Przykład: `tags['''My.Apostrophe.Tag''']` gdzie **"My. apostrof. tag"** jest nazwą tagu.
- Aliasy właściwości — Aby uzyskać listę, zobacz [aliasy](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` i `tags[tag.with.dots]` są nadal akceptowalnymi sposobami deklarowania pola Tagi. Jednak preferowane wyrażenia są wymienione powyżej.

> [!NOTE]
> W wyrażeniach **pola** odnoszących się do **\[ \* \] aliasu** każdy element w tablicy jest obliczany pojedynczo przy użyciu koniunkcji logicznej **i** między elementami. Aby uzyskać więcej informacji, zobacz [odwoływanie się do właściwości zasobów tablicy](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="use-tags-with-parameters"></a>Używanie tagów z parametrami

Wartość parametru może być przekazanie do pola tagu. Przekazywanie parametru do pola tagu zwiększa elastyczność definicji zasad podczas przypisywania zasad.

W poniższym przykładzie `concat` jest używany do tworzenia wyszukiwania pól tagów dla tagu o nazwie **TagName** parametru. Jeśli ten tag nie istnieje, efekt **modyfikacji** jest używany do dodawania znacznika przy użyciu wartości tego samego nazwanego tagu ustawionego w nadrzędnej grupie zasobów poddane inspekcji przy użyciu `resourcegroup()` funkcji wyszukiwania.

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
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>Wartość

Warunki, które sprawdzają, czy wartość spełnia określone kryteria, można utworzyć przy użyciu wyrażenia **wartości** . Wartości mogą być literałami, wartościami [parametrów](#parameters)lub zwracanymi wartościami wszystkich [obsługiwanych funkcji szablonu](#policy-functions).

> [!WARNING]
> Jeśli wynik _funkcji szablonu_ jest błąd, Ocena zasad kończy się niepowodzeniem. Niepowodzenie oceny to niejawne **odmowa**. Aby uzyskać więcej informacji, zobacz [unikanie niepowodzeń związanych z szablonami](#avoiding-template-failures). Użyj [wymuszania](./assignment-structure.md#enforcement-mode) elementu **DoNotEnforce** , aby zapobiec wpływowi oceny zakończonej niepowodzeniem na nowe lub zaktualizowane zasoby podczas testowania i sprawdzania poprawności nowej definicji zasad.

#### <a name="value-examples"></a>Przykłady wartości

Ta reguła zasad używa tego przykładu **wartości** do porównania wyniku `resourceGroup()` funkcji i zwróconej właściwości **name** do warunku **like** `*netrg` . Reguła odmówi dowolnego zasobu, którego typ nie jest `Microsoft.Network/*` **typem** w żadnej grupie zasobów, której nazwy kończą się `*netrg` .

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

Ta reguła zasad używa tego przykładu **wartości** do sprawdzenia, czy wynik wielu zagnieżdżonych funkcji **równa** się `true` . Reguła odmówi wszelkich zasobów, które nie mają co najmniej trzech tagów.

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

Warunki, które ponoszą liczbę elementów członkowskich tablicy spełniających określone kryteria, można utworzyć przy użyciu wyrażenia **Count** . Typowe scenariusze sprawdzają, czy "co najmniej jeden z", "dokładnie jeden z", "All" lub "none" elementów członkowskich tablicy spełnia warunek. **Count** oblicza każdy element członkowski tablicy dla wyrażenia warunku i sumuje _prawdziwe_ wyniki, które są porównywane z operatorem wyrażenia.

#### <a name="field-count"></a>Liczba pól

Zlicz liczbę elementów członkowskich tablicy w ładunku żądania spełniających wyrażenie warunku. Struktura wyrażeń **zliczania pól** to:

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

Następujące właściwości są używane z **liczbą pól**:

- **Count. pole** (wymagane): zawiera ścieżkę do tablicy i musi być aliasem tablicy.
- **Count. WHERE** (opcjonalnie): wyrażenie warunku, które ma zostać obliczone indywidualnie dla każdego elementu członkowskiego tablicy [ \[ \* \] aliasów](#understanding-the--alias) `count.field` . Jeśli ta właściwość nie jest określona, wszystkie elementy członkowskie tablicy ze ścieżką "pole" są oceniane na _wartość true_. Dowolny [warunek](../concepts/definition-structure.md#conditions) może być używany wewnątrz tej właściwości.
  [Operatory logiczne](#logical-operators) mogą być używane wewnątrz tej właściwości, aby utworzyć złożone wymagania dotyczące oceny.
- **\<condition\>** (wymagane): wartość jest porównywana z liczbą elementów, które osiągnęły liczbę **.** wyrażenie warunku WHERE. Należy użyć [warunku](../concepts/definition-structure.md#conditions) liczbowego.

Wyrażenia **Count pól** mogą wyliczyć tę samą tablicę pól do trzech razy w jednej definicji **Klasa policyrule** .

Aby uzyskać szczegółowe informacje na temat sposobu pracy z właściwościami tablicy w Azure Policy, w tym szczegółowe wyjaśnienie sposobu oceniania wyrażenia **liczby pól** , zobacz [odwoływanie się do właściwości zasobów tablicy](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="value-count"></a>Liczba wartości

Zlicz liczbę elementów członkowskich tablicy spełniających warunek. Tablica może być tablicą literałową lub [odwołaniem do parametru array](#using-a-parameter-value). Struktura wyrażeń **zliczania wartości** :

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Następujące właściwości są używane z **licznikiem wartości**:

- **Count. Value** (wymagane): tablica do obliczenia.
- **Count.Name** (wymagane): Nazwa indeksu składająca się z liter i cyfr w języku angielskim. Definiuje nazwę wartości elementu członkowskiego tablicy ocenianego w bieżącej iteracji. Nazwa jest używana do odwoływania się do bieżącej wartości w `count.where` warunku. Opcjonalne, gdy wyrażenie **Count** nie znajduje się w elemencie podrzędnym innego wyrażenia **Count** . Gdy nie zostanie podany, nazwa indeksu jest niejawnie ustawiana na `"default"` .
- **Count. WHERE** (opcjonalnie): wyrażenie warunku, które ma zostać obliczone indywidualnie dla każdego elementu członkowskiego tablicy `count.value` . Jeśli ta właściwość nie jest określona, wszystkie elementy członkowskie tablicy są oceniane na _wartość true_. Dowolny [warunek](../concepts/definition-structure.md#conditions) może być używany wewnątrz tej właściwości. [Operatory logiczne](#logical-operators) mogą być używane wewnątrz tej właściwości, aby utworzyć złożone wymagania dotyczące oceny. Wartość aktualnie wyliczeniowego elementu członkowskiego tablicy można uzyskać dostęp, wywołując [bieżącą](#the-current-function) funkcję.
- **\<condition\>** (wymagane): wartość jest porównywana z liczbą elementów, które spełniają `count.where` wyrażenie warunku. Należy użyć [warunku](../concepts/definition-structure.md#conditions) liczbowego.

Są wymuszane następujące limity:
- Do 10 wyrażeń **liczenia wartości** można użyć w jednej definicji **Klasa policyrule** .
- Każde wyrażenie **Count wartości** może wykonywać do 100 iteracji. Ta liczba obejmuje liczbę iteracji wykonywanych przez dowolne wyrażenia **liczby wartości** nadrzędnych.

#### <a name="the-current-function"></a>Bieżąca funkcja

`current()`Funkcja jest dostępna tylko w ramach `count.where` warunku. Zwraca wartość elementu członkowskiego tablicy, który jest aktualnie wyliczany przez obliczenie wyrażenia **liczby** .

**Użycie liczby wartości**

- `current(<index name defined in count.name>)`. Na przykład: `current('arrayMember')`.
- `current()`. Dozwolone tylko wtedy, gdy wyrażenie **Count wartości** nie jest elementem podrzędnym innego wyrażenia **Count** . Zwraca taką samą wartość jak powyżej.

Jeśli wartość zwrócona przez wywołanie jest obiektem, obsługiwane są metody dostępu do właściwości. Na przykład: `current('objectArrayMember').property`.

**Użycie liczb pól**

- `current(<the array alias defined in count.field>)`. Na przykład `current('Microsoft.Test/resource/enumeratedArray[*]')`.
- `current()`. Dozwolone tylko wtedy, gdy wyrażenie **Count pola** nie jest elementem podrzędnym innego wyrażenia **Count** . Zwraca taką samą wartość jak powyżej.
- `current(<alias of a property of the array member>)`. Na przykład `current('Microsoft.Test/resource/enumeratedArray[*].property')`.

#### <a name="field-count-examples"></a>Przykłady liczby pól

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

Przykład 5: Sprawdź, czy co najmniej jeden element członkowski tablicy jest zgodny z wieloma właściwościami w wyrażeniu warunku

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

Przykład 6: Użyj `current()` funkcji w `where` warunkach, aby uzyskać dostęp do wartości aktualnie wyliczanej elementu członkowskiego tablicy w funkcji szablonu. Ten stan sprawdza, czy sieć wirtualna zawiera prefiks adresu, który nie znajduje się w zakresie routingu 10.0.0.0/24.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

Przykład 7: Użyj `field()` funkcji w `where` warunkach, aby uzyskać dostęp do wartości aktualnie wyliczonej składowej tablicy. Ten stan sprawdza, czy sieć wirtualna zawiera prefiks adresu, który nie znajduje się w zakresie routingu 10.0.0.0/24.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>Przykłady liczby wartości

Przykład 1: Sprawdź, czy nazwa zasobu pasuje do żadnego z podanego wzorca nazwy.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Przykład 2: Sprawdź, czy nazwa zasobu pasuje do żadnego z podanego wzorca nazwy. `current()`Funkcja nie określa nazwy indeksu. Wynik jest taki sam jak w poprzednim przykładzie.

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Przykład 3: Sprawdź, czy nazwa zasobu pasuje do któregokolwiek z określonych wzorców nazw dostarczonych przez parametr array.

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Przykład 4: Sprawdź, czy którykolwiek z prefiksów adresów sieci wirtualnej nie znajduje się na liście zatwierdzonych prefiksów.

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

Przykład 5: Sprawdź, czy wszystkie zastrzeżone reguły sieciowej grupy zabezpieczeń są zdefiniowane w sieciowej grupy zabezpieczeń. Właściwości zarezerwowanych reguł sieciowej grupy zabezpieczeń są zdefiniowane w parametrze Array zawierającym obiekty.

Wartość parametru:

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

Zasad
```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
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
- **Modyfikowanie**: dodaje, aktualizuje lub usuwa zdefiniowane znaczniki z zasobu lub subskrypcji.
- **EnforceOPAConstraint** (przestarzałe): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v3 dla samozarządzanego klastra Kubernetes na platformie Azure
- **EnforceRegoPolicy** (przestarzałe): konfiguruje kontroler "Open Policy Agent Admission Control" z strażnikiem v2 w usłudze Azure Kubernetes Service

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

Następująca funkcja jest dostępna do użycia w regule zasad, ale różni się od użycia w szablonie Azure Resource Manager (szablon ARM):

- `utcNow()` — W przeciwieństwie do szablonu ARM, ta właściwość może być używana poza _DefaultValue_.
  - Zwraca ciąg, który jest ustawiony na bieżącą datę i godzinę w formacie Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Następujące funkcje są dostępne tylko w regułach zasad:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [Required] ciąg ciągu w formacie daty/godziny uniwersalnego ISO 8601 "RRRR-MM-DDTgg: mm: SS. FFFFFFFZ'
  - **numberOfDaysToAdd**: [Required] liczba dni do dodania
- `field(fieldName)`
  - **FieldName**: [Required] — nazwa [pola](#fields) do pobrania
  - Zwraca wartość tego pola z zasobu, który jest obliczany przez warunek IF.
  - `field` jest używany głównie z **AuditIfNotExists** i **DeployIfNotExists** do odwołań do pól w analizowanym zasobie. Przykład tego zastosowania można zobaczyć w [przykładzie DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Zwraca wersję interfejsu API żądania, które spowodowało wyzwolenie oceny zasad (przykład: `2019-09-01` ).
    Ta wartość jest wersją interfejsu API, która została użyta w żądaniu PUT/PATCH do oceny przy tworzeniu/aktualizowaniu zasobów. Najnowsza wersja interfejsu API jest zawsze używana podczas oceny zgodności dla istniejących zasobów.
- `policy()`
  - Zwraca następujące informacje dotyczące zasad, które są oceniane. Do właściwości można uzyskać dostęp z zwróconego obiektu (przykład: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```

- `ipRangeContains(range, targetRange)`
  - **zakres**: [Required] ciąg-ciąg określający zakres adresów IP.
  - **targetRange**: [Required] ciąg ciągu określający zakres adresów IP.

  Zwraca czy dany zakres adresów IP zawiera docelowy zakres adresów IP. Puste zakresy lub mieszanie między rodzinami adresów IP nie są dozwolone i skutkuje niepowodzeniem oceny.

  Obsługiwane formaty:
  - Pojedynczy adres IP (przykłady: `10.0.0.0` , `2001:0DB8::3:FFFE` )
  - Zakres CIDR (przykłady: `10.0.0.0/24` , `2001:0DB8::/110` )
  - Zakres zdefiniowany przez początkowy i końcowy adres IP (przykłady: `192.168.0.1-192.168.0.9` , `2001:0DB8::-2001:0DB8::3:FFFF` )

- `current(indexName)`
  - Funkcja specjalna, która może być używana tylko w [wyrażeniach Count](#count).

#### <a name="policy-function-example"></a>Przykład funkcji zasad

Ten przykład reguły używa `resourceGroup` funkcji zasobów, aby uzyskać Właściwość **name** połączonej z `concat` funkcją Array i Object, aby skompilować `like` warunek, który wymusza nazwę zasobu, aby rozpocząć od nazwy grupy zasobów.

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

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Zrzut ekranu rozszerzenia Azure Policy dla Visual Studio Code aktywowania właściwości w celu wyświetlenia nazw aliasów." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Aby znaleźć aliasy, które mogą być używane z efektem [modyfikacji](./effects.md#modify) , użyj następującego polecenia w Azure PowerShell **4.6.0** lub wyższy:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Informacje o aliasie [*]

Kilka dostępnych aliasów ma wersję, która jest wyświetlana jako nazwa "normal" i inna, która została **\[\*\]** do niej dołączona. Na przykład:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Alias "normal" reprezentuje pole jako pojedynczą wartość. To pole jest przeznaczone do dokładnego dopasowania scenariuszy porównywania, gdy cały zestaw wartości musi być dokładnie zdefiniowany, nie więcej i nie rzadziej.

**\[\*\]** Alias reprezentuje kolekcję wartości wybranych z elementów właściwości zasobu tablicy. Na przykład:

| Alias | Wybrane wartości |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | Elementy `ipRules` tablicy. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | Wartości `action` właściwości z każdego elementu `ipRules` tablicy. |

W przypadku użycia w warunku [pola](#fields) aliasy tablic umożliwiają porównanie poszczególnych elementów tablicy z wartością docelową. Gdy jest używany z wyrażeniem [Count](#count) , możliwe jest:

- Sprawdzanie rozmiaru tablicy
- Sprawdź, czy all\any\none elementów tablicy spełnia warunki złożone
- Sprawdź, czy dokładnie ***n*** elementów tablicy spełnia warunki złożone

Aby uzyskać więcej informacji i przykładów, zobacz [odwoływanie się do właściwości zasobów tablicy](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

## <a name="next-steps"></a>Następne kroki

- Zobacz [strukturę definicji inicjatywy](./initiative-definition-structure.md)
- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](../how-to/programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](../how-to/get-compliance-data.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
