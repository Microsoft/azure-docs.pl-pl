---
title: Tworzenie zasad dla właściwości tablicy zasobów
description: Dowiedz się, jak korzystać z parametrów tablicy i wyrażeń języka tablicowego, oszacować alias [*] i dołączać elementy z regułami definicji Azure Policy.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 650b2ec6bc1bbd12cd10abb1917ef5ea2d6029e9
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220749"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Tworzenie zasad dla właściwości tablicy zasobów platformy Azure

Właściwości Azure Resource Manager są zwykle określane jako ciągi i wartości logiczne. Gdy istnieje relacja jeden do wielu, właściwości złożone są raczej zdefiniowane jako tablice. W Azure Policy tablice są używane na kilka różnych sposobów:

- Typ [parametru definicji](../concepts/definition-structure.md#parameters)w celu zapewnienia wielu opcji
- Część [reguły zasad](../concepts/definition-structure.md#policy-rule) z zastosowaniem warunków **w** lub **notIn**
- Część reguły zasad, która zlicza, ile elementów członkowskich tablicy spełnia warunek
- W efektach [dołączania](../concepts/effects.md#append) i [modyfikowania](../concepts/effects.md#modify) do aktualizowania istniejącej tablicy

W tym artykule opisano każde użycie programu według Azure Policy i przedstawiono kilka przykładów definicji.

## <a name="parameter-arrays"></a>Tablice parametrów

### <a name="define-a-parameter-array"></a>Zdefiniuj tablicę parametrów

Zdefiniowanie parametru jako tablicy umożliwia elastyczność zasad, gdy wymagana jest więcej niż jedna wartość.
Ta definicja zasad umożliwia określenie dowolnej pojedynczej lokalizacji parametru **allowedLocations** i wartości domyślnych _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Jako **Typ** to _ciąg_, podczas przypisywania zasad można ustawić tylko jedną wartość. Jeśli te zasady są przypisane, zasoby w zakresie są dozwolone tylko w ramach jednego regionu świadczenia usługi Azure. Większość definicji zasad musi zezwalać na listę zatwierdzonych opcji, takich jak Zezwalanie na _eastus2_, _Wschodnie_ i _westus2_.

Aby utworzyć definicję zasad w celu zezwolenia na wiele opcji, należy użyć **typu** _tablicy_ . Te same zasady można napisać ponownie w następujący sposób:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Po zapisaniu definicji zasad nie można zmienić właściwości **typu** dla parametru.

Ta nowa definicja parametru przyjmuje więcej niż jedną wartość podczas przypisywania zasad. Po zdefiniowaniu właściwości array **allowedValues** wartości dostępne podczas przypisywania są dodatkowo ograniczone do wstępnie zdefiniowanej listy opcji. Użycie **allowedValues** jest opcjonalne.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Przekazywanie wartości do tablicy parametrów podczas przypisywania

Podczas przypisywania zasad przy użyciu Azure Portal parametr **typu** _Array_ jest wyświetlany jako pojedyncze pole tekstowe. Wskazówka mówi "Use; do oddzielania wartości. (np. Londyn; Nowy Jork) ". Aby przekazać dozwolone wartości lokalizacji _eastus2_, _Wschodnie_ i _westus2_ do parametru, użyj następującego ciągu:

`eastus2;eastus;westus2`

Format wartości parametru jest różny w przypadku korzystania z interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub interfejsu API REST. Wartości są przesyłane przez ciąg JSON, który zawiera również nazwę parametru.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Aby użyć tego ciągu z każdym zestawem SDK, użyj następujących poleceń:

- Interfejs wiersza polecenia platformy Azure: polecenie [AZ Policy przypisanie Create](/cli/azure/policy/assignment#az_policy_assignment_create) **z parametrem** Parameter
- Azure PowerShell: polecenie cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) z parametrem **PolicyParameter**
- Interfejs API REST: w operacji _Put_ [Create](/rest/api/resources/policyassignments/create) jako część treści żądania jako wartość właściwości **Properties. Parameters**

## <a name="using-arrays-in-conditions"></a>Używanie tablic w warunkach

### <a name="in-and-notin"></a>`In` i `notIn`

`in`Warunki i `notIn` działają tylko z wartościami tablicy. Sprawdzają istnienie wartości w tablicy. Tablica może być literalną tablicą JSON lub odwołaniem do parametru tablicy. Na przykład:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Liczba wartości

Wyrażenie [liczby wartości](../concepts/definition-structure.md#value-count) oblicza liczbę elementów członkowskich tablicy spełniających warunek. Zapewnia sposób wielokrotnego oszacowania tego samego warunku przy użyciu różnych wartości dla każdej iteracji. Na przykład, poniższy warunek sprawdza, czy nazwa zasobu pasuje do żadnego wzorca z tablicy wzorców:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Aby oszacować wyrażenie, Azure Policy ocenia `where` warunek 3 razy dla każdego elementu członkowskiego `[ "test*", "dev*", "prod*" ]` , licząc, ile razy była Szacowana `true` . Dla każdej iteracji wartość bieżącego elementu członkowskiego tablicy jest sparowana z `pattern` nazwą indeksu zdefiniowaną przez `count.name` . Ta wartość może być następnie przywoływana wewnątrz `where` warunku przez wywołanie specjalnej funkcji szablonu: `current('pattern')` .

| Iteracja | `current('pattern')` wartość zwrócona |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

Warunek ma wartość true tylko wtedy, gdy wynikowa liczba jest większa niż 0.

Aby warunek był bardziej ogólny, użyj odwołania do parametru zamiast tablicy literałowej:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Gdy wyrażenie **Count wartości** nie znajduje się w żadnym innym wyrażeniu **Count** , `count.name` jest opcjonalne, a `current()` Funkcja może być używana bez żadnych argumentów:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**Licznik wartości** obsługuje również tablice obiektów złożonych, co pozwala na bardziej złożone warunki. Na przykład poniższy warunek definiuje żądaną wartość tagu dla każdego wzorca nazwy i sprawdza, czy nazwa zasobu pasuje do wzorca, ale nie ma wymaganej wartości tagu:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Aby poznać przydatne przykłady, zobacz [przykłady liczby wartości](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Odwoływanie się do właściwości zasobów tablicy

Wiele przypadków użycia wymaga pracy z właściwościami tablicy w oszacowanym zasobie. Niektóre scenariusze wymagają odwołującego się do całej tablicy (na przykład sprawdzając jej długość). Inne wymagają zastosowania warunku do każdego indywidualnego elementu członkowskiego tablicy (na przykład upewnij się, że cała reguła zapory blokuje dostęp z Internetu). Zrozumienie różnych sposobów, Azure Policy mogą odwoływać się do właściwości zasobów oraz jak te odwołania zadziałały, gdy odwołują się do właściwości tablicy, jest kluczem do pisania warunków, które obejmują te scenariusze.

### <a name="referencing-resource-properties"></a>Odwoływanie się do właściwości zasobów

Do właściwości zasobów można odwoływać się Azure Policy przy użyciu [aliasów](../concepts/definition-structure.md#aliases) istnieją dwa sposoby odwoływania się do wartości właściwości zasobu w ramach Azure Policy:

- Użyj warunku [pola](../concepts/definition-structure.md#fields) , aby sprawdzić, czy **wszystkie** wybrane właściwości zasobów spełniają warunek. Przykład:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Użyj `field()` funkcji, aby uzyskać dostęp do wartości właściwości. Przykład:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

Warunek pola ma niejawne zachowanie "All of". Jeśli alias reprezentuje kolekcję wartości, sprawdza, czy wszystkie poszczególne wartości spełniają warunek. `field()`Funkcja zwraca wartości reprezentowane przez alias jako-is, które można następnie manipulować przez inne funkcje szablonu.

### <a name="referencing-array-fields"></a>Odwołujące się do pól tablicy

Właściwości zasobów tablicy są zwykle reprezentowane przez dwa różne typy aliasów. Jeden "normalny" alias i aliasy [tablic](../concepts/definition-structure.md#understanding-the--alias) , które zostały `[*]` dołączone do niego:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Odwoływanie się do tablicy

Pierwszy alias reprezentuje pojedynczą wartość, wartość `stringArray` właściwości z zawartości żądania. Ponieważ wartość tej właściwości jest tablicą, nie jest ona bardzo przydatna w warunkach zasad. Na przykład:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Ten warunek porównuje całą `stringArray` tablicę z jedną wartością ciągu. Większość warunków, w tym `equals` , tylko akceptowanie wartości ciągu, dlatego nie jest dużo użycia podczas porównywania tablicy z ciągiem. Głównym scenariuszem, do którego odwołuje się Właściwość Array, jest przydatny podczas sprawdzania, czy istnieje:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

`field()`Funkcja zwraca wartość, która jest tablicą z zawartości żądania, która może być następnie używana z dowolnymi [obsługiwanymi funkcjami szablonu](../concepts/definition-structure.md#policy-functions) , które akceptują argumenty tablicy. Na przykład, poniższy warunek sprawdza, czy długość `stringArray` jest większa od 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Odwoływanie się do kolekcji elementów członkowskich tablicy

Aliasy używające `[*]` składni reprezentują **kolekcję wartości właściwości wybranych ze właściwości array**, która różni się od wybrania samej właściwości Array. W przypadku `Microsoft.Test/resourceType/stringArray[*]` zwraca kolekcję, która ma wszystkie elementy członkowskie `stringArray` . Jak wspomniano wcześniej, `field` warunek sprawdza, czy wszystkie wybrane właściwości zasobów spełniają warunek, w związku z czym następujący warunek jest prawdziwy tylko wtedy, gdy **wszystkie** elementy członkowskie `stringArray` są równe "" wartości "".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Jeśli tablica zawiera obiekty, `[*]` alias może służyć do wybrania wartości konkretnej właściwości z każdego elementu członkowskiego tablicy. Przykład:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Ten warunek ma wartość true, jeśli wartości wszystkich `property` właściwości w `objectArray` są równe `"value"` . Aby uzyskać więcej przykładów, zobacz [dodatkowe \[ \* \] przykłady aliasów](#appendix--additional--alias-examples).

Gdy funkcja jest używana `field()` do odwoływania aliasu tablicy, zwrócona wartość jest tablicą wszystkich wybranych wartości. Takie zachowanie oznacza, że typowy przypadek użycia `field()` funkcji, możliwość stosowania funkcji szablonu do wartości właściwości zasobów, jest bardzo ograniczony. Jedynymi funkcjami szablonu, które mogą być używane w tym przypadku, są te, które akceptują argumenty tablicy. Na przykład można uzyskać długość tablicy z `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Jednak bardziej złożone scenariusze, takie jak stosowanie funkcji szablonu do każdego elementu członkowskiego tablicy i porównywanie ich z pożądaną wartością, są możliwe tylko przy użyciu `count` wyrażenia. Aby uzyskać więcej informacji, zobacz [wyrażenie liczby pól](#field-count-expressions).

Aby podsumować dane, zobacz następującą przykładową zawartość zasobów i wybrane wartości zwracane przez różne aliasy:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

W przypadku użycia warunku pola dla przykładowej zawartości zasobów wyniki są następujące:

| Alias | Wybrane wartości |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Pusta Kolekcja wartości. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Pusta Kolekcja wartości. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

W przypadku korzystania z `field()` funkcji na przykładowej zawartości zasobów wyniki są następujące:

| Wyrażenie | Wartość zwrócona |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Wyrażenia liczb pól

Wyrażenia [liczb pól](../concepts/definition-structure.md#field-count) obliczają liczbę elementów członkowskich tablicy spełniających warunek i porównując liczbę z wartością docelową. `Count` jest bardziej intuicyjny i wszechstronny do oceny tablic w porównaniu z `field` warunkami. Składnia jest następująca:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Gdy jest używany bez `where` warunku, `count` po prostu zwraca długość tablicy. W przypadku przykładowej zawartości zasobów z poprzedniej sekcji następujące `count` wyrażenie jest oceniane, `true` ponieważ `stringArray` ma trzy elementy członkowskie:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

To zachowanie działa również w przypadku tablic zagnieżdżonych. Na przykład następujące `count` wyrażenie jest oceniane, `true` ponieważ w tablicach znajdują się cztery elementy członkowskie tablicy `nestedArray` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Moc `count` jest w `where` stanie. Gdy jest określony, Azure Policy wylicza elementy członkowskie tablicy i ocenia każde względem warunku, licząc liczbę ocenionych elementów członkowskich tablicy `true` . W odniesieniu do każdej iteracji `where` oceny warunku Azure Policy wybiera jeden element członkowski tablicy ***i** _ i ocenia zawartość zasobu `where` pod warunkiem _*, jeśli **_i_*_ jest jedynym członkiem array_ *. Posiadanie tylko jednego elementu członkowskiego tablicy dostępnej w każdej iteracji umożliwia stosowanie złożonych warunków na poszczególnych elementach członkowskich tablicy.

Przykład:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Aby oszacować `count` wyrażenie, Azure Policy ocenia `where` warunek 3 razy dla każdego elementu członkowskiego `stringArray` , licząc, ile razy była Szacowana `true` . Gdy `where` warunek odwołuje się do `Microsoft.Test/resourceType/stringArray[*]` elementów członkowskich tablicy, a nie do zaznaczania wszystkich elementów członkowskich `stringArray` , w każdym momencie wybierany jest tylko pojedynczy element członkowski tablicy:

| Iteracja | Wybrane `Microsoft.Test/resourceType/stringArray[*]` wartości | `where` Wynik oceny |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Z tego względu zwrócimy `count` `1` .

Oto bardziej złożone wyrażenie:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteracja | Wybrane wartości | `where` Wynik oceny |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

I w ten sposób `count` zwraca wartość `1` .

Fakt, że `where` wyrażenie jest oceniane względem **całej** zawartości żądania (ze zmianami tylko dla elementu członkowskiego tablicy, który jest aktualnie wyliczany) oznacza, że `where` warunek może odwoływać się również do pól poza tablicą:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteracja | Wybrane wartości | `where` Wynik oceny |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Zagnieżdżone wyrażenia Count są również dozwolone:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Iteracja pętli zewnętrznej | Wybrane wartości | Iteracja pętli wewnętrznej | Wybrane wartości |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

#### <a name="accessing-current-array-member-with-template-functions"></a>Uzyskiwanie dostępu do bieżącego elementu członkowskiego tablicy przy użyciu funkcji szablonu

Korzystając z funkcji szablonu, należy użyć `current()` funkcji, aby uzyskać dostęp do wartości bieżącego elementu członkowskiego tablicy lub wartości dowolnej z jej właściwości. Aby uzyskać dostęp do wartości bieżącego elementu członkowskiego tablicy, należy przekazać alias zdefiniowany w `count.field` lub dowolny alias podrzędny jako argument do `current()` funkcji. Na przykład:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteracja | `current()` wartość zwrócona | `where` Wynik oceny |
|:---|:---|:---|
| 1 | Wartość `property` pierwszego elementu członkowskiego `objectArray[*]` : `value1` | `true` |
| 2 | Wartość `property` pierwszego elementu członkowskiego `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>Funkcja pola wewnątrz warunków WHERE

`field()`Funkcja może być również używana do uzyskiwania dostępu do wartości bieżącego elementu członkowskiego tablicy, tak długo, jak wyrażenie **Count** nie znajduje się w **warunku istnienia** ( `field()` Funkcja zawsze odwołuje się do zasobu ocenianego w warunku **if** ).
Zachowanie `field()` podczas odwoływania się do obliczanej tablicy jest oparte na następujących pojęciach:
1. Aliasy tablic są rozwiązywane do kolekcji wartości wybranych ze wszystkich elementów członkowskich tablicy.
1. `field()` funkcje odwołujące się do aliasów tablic zwracają tablicę z wybranymi wartościami.
1. Odwołanie do liczonego aliasu tablicy wewnątrz `where` warunku zwraca kolekcję z pojedynczą wartością wybraną z elementu członkowskiego tablicy, który jest obliczany w bieżącej iteracji.

To zachowanie oznacza, że w przypadku odwoływania się do liczonej składowej tablicy z `field()` funkcją w ramach `where` warunku **zwraca tablicę z pojedynczym elementem członkowskim**. Chociaż ta wartość może nie być intuicyjna, jest spójna z pomysłem, że aliasy tablic zawsze zwracają kolekcję wybranych właściwości. Oto przykład:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteracja | Wartości wyrażeń | `where` Wynik oceny |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

W związku z tym, gdy istnieje potrzeba uzyskania dostępu do wartości aliasu tablicy liczonej przy użyciu `field()` funkcji, sposób, aby to zrobić, jest zawijany przy użyciu `first()` funkcji szablonu:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteracja | Wartości wyrażeń | `where` Wynik oceny |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Aby zapoznać się z przydatnymi przykładami, zobacz [przykłady liczby pól](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Modyfikowanie tablic

[Dołączanie](../concepts/effects.md#append) i [Modyfikowanie](../concepts/effects.md#modify) zmian właściwości zasobu podczas tworzenia lub aktualizowania. Podczas pracy z właściwościami tablicy zachowanie tych efektów zależy od tego, czy operacja próbuje zmodyfikować alias, czy  **\[\*\]** nie:

> [!NOTE]
> Używanie `modify` efektu z aliasami jest obecnie w **wersji zapoznawczej**.

|Alias |Efekt | Wynik |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | W razie braku Azure Policy dołącza całą tablicę określoną w szczegółach efektu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` z `add` operacją | W razie braku Azure Policy dołącza całą tablicę określoną w szczegółach efektu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` z `addOrReplace` operacją | Azure Policy dołącza całą tablicę określoną w szczegółach efektu, jeśli brakuje lub zastąpi istniejącą tablicę. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy dołącza element członkowski tablicy określony w szczegółach efektu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` z `add` operacją | Azure Policy dołącza element członkowski tablicy określony w szczegółach efektu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` z `addOrReplace` operacją | Azure Policy usuwa wszystkie istniejące elementy członkowskie tablicy i dołącza element członkowski tablicy określony w szczegółach efektu. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy dołącza wartość do `action` właściwości każdego elementu członkowskiego tablicy. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` z `add` operacją | Azure Policy dołącza wartość do `action` właściwości każdego elementu członkowskiego tablicy. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` z `addOrReplace` operacją | Azure Policy dołącza lub zastępuje istniejącą `action` Właściwość każdego elementu członkowskiego tablicy. |

Aby uzyskać więcej informacji, zobacz [przykłady dołączania](../concepts/effects.md#append-examples).

## <a name="appendix--additional--alias-examples"></a>Dodatek — dodatkowe przykłady dotyczące aliasu [*]

Zaleca się użycie [wyrażeń zliczania pól](#field-count-expressions) , aby sprawdzić, czy element "All of" lub "any" elementów członkowskich tablicy w treści żądania spełnia warunek. Jednak w przypadku niektórych prostych warunków można osiągnąć ten sam wynik przy użyciu metody dostępu do pola z aliasem tablicy (zgodnie z opisem w odniesieniu do [kolekcji elementów członkowskich tablicy](#referencing-the-array-members-collection)). Może to być przydatne w regułach zasad, które przekraczają limit dozwolonych wyrażeń **Count** . Oto przykłady typowych przypadków użycia:

Przykładowa reguła zasad dla poniższej tabeli scenariusza:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Tablica **ipRules** jest następująca dla poniższej tabeli scenariusza:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Dla każdego poniższego przykładu warunku Zastąp ciąg wyrażeniem `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

Poniższe wyniki są wynikiem kombinacji warunku i przykładową regułę zasad i tablicę istniejących wartości powyżej:

|Warunek |Wynik | Scenariusz |Objaśnienie |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |Brak dopasowania |Jeden element tablicy ma wartość false (127.0.0.1! = 127.0.0.1) i jeden jako true (127.0.0.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość false_ , a efekt nie jest wyzwalany. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efekt zasad |Brak dopasowania |Oba elementy tablicy są oceniane jako prawdziwe (10.0.4.1! = 127.0.0.1 i 10.0.4.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość true_ i zostanie wyzwolony efekt. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efekt zasad |Co najmniej jedno dopasowanie |Jeden element tablicy ma wartość false (127.0.0.1! = 127.0.0.1) i jeden jako true (127.0.0.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |Co najmniej jedno dopasowanie |Oba elementy tablicy są oceniane jako prawdziwe (10.0.4.1! = 127.0.0.1 i 10.0.4.1! = 192.168.1.1), więc warunek **notEquals** ma _wartość true_. Operator logiczny ma wartość false (**nie** _true_), więc efekt nie zostanie wyzwolony. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efekt zasad |Nie wszystkie dopasowania |Jeden element tablicy ma wartość true (127.0.0.1 = = 127.0.0.1) i jeden jako wartość false (127.0.0.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efekt zasad |Nie wszystkie dopasowania |Oba elementy tablicy są oceniane jako false (10.0.4.1 = = 127.0.0.1 i 10.0.4.1 = = 192.168.1.1), więc warunek **Equals** ma _wartość false_. Operator logiczny ma wartość true (**nie** _false_), więc efekt zostanie wyzwolony. |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |Wszystkie dopasowania |Jeden element tablicy ma wartość true (127.0.0.1 = = 127.0.0.1) i jeden jako wartość false (127.0.0.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_ , a efekt nie jest wyzwalany. |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |Wszystkie dopasowania |Oba elementy tablicy są oceniane jako false (10.0.4.1 = = 127.0.0.1 i 10.0.4.1 = = 192.168.1.1), więc warunek **równości** ma _wartość false_ , a efekt nie jest wyzwalany. |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
