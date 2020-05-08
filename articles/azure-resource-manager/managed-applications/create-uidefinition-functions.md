---
title: Tworzenie funkcji definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980817"
---
# <a name="createuidefinition-functions"></a>Funkcje CreateUiDefinition
Ta sekcja zawiera sygnatury dla wszystkich obsługiwanych funkcji CreateUiDefinition.

Aby użyć funkcji, należy ująć wywołanie z nawiasami kwadratowymi. Przykład:

```json
"[function()]"
```

Ciągi i inne funkcje mogą być przywoływane jako parametry dla funkcji, ale ciągi muszą być ujęte w apostrofy. Przykład:

```json
"[fn1(fn2(), 'foobar')]"
```

Jeśli ma to zastosowanie, można odwoływać się do właściwości danych wyjściowych funkcji przy użyciu operatora kropki. Przykład:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funkcje odwołujące
Te funkcje mogą być używane do odwoływania się do danych wyjściowych z właściwości lub kontekstu CreateUiDefinition.

### <a name="basics"></a>nazwie
Zwraca wartości wyjściowe elementu, który jest zdefiniowany w kroku podstawowe.

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `foo` w kroku podstawowe:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroki
Zwraca wartości wyjściowe elementu, który jest zdefiniowany w określonym kroku. Aby uzyskać wartości wyjściowe elementów w kroku podstawowe, należy zamiast tego użyć `basics()` .

Poniższy przykład zwraca dane wyjściowe elementu o nazwie `bar` w kroku o nazwie: `foo`

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Zwraca lokalizację wybraną w kroku podstawowe lub w bieżącym kontekście.

Poniższy przykład może zwrócić `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funkcje ciągów
Tych funkcji można używać tylko z ciągami JSON.

### <a name="concat"></a>concat
Łączy jeden lub więcej ciągów.

Na przykład, jeśli wartość wyjściowa elementu `element1` if `"bar"`, wówczas ten przykład zwraca ciąg `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>podciąg
Zwraca podciąg określonego ciągu. Podciąg zaczyna się od określonego indeksu i ma określoną długość.

Poniższy przykład zwraca `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Zastąp
Zwraca ciąg, w którym wszystkie wystąpienia określonego ciągu w bieżącym ciągu są zastępowane innym ciągiem.

Poniższy przykład zwraca `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Generuje unikatowy ciąg globalny (GUID).

Poniższy przykład może zwrócić `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Zwraca ciąg przekonwertowany na małe litery.

Poniższy przykład zwraca `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Zwraca ciąg przekonwertowany na wielkie litery.

Poniższy przykład zwraca `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funkcje kolekcji
Te funkcje mogą być używane z kolekcjami, takimi jak ciągi JSON, tablice i obiekty.

### <a name="contains"></a>zawiera
Zwraca `true` Jeśli ciąg zawiera określony podciąg, tablica zawiera określoną wartość lub obiekt zawiera określony klucz.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Zwraca liczbę znaków w ciągu, liczbę wartości w tablicy lub liczbę kluczy w obiekcie.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>puste
Zwraca `true` czy ciąg, tablica lub obiekt ma wartość null lub jest pusty.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Przykład 4: null i undefined
Przyjmij `element1` `null` lub niezdefiniowane. Poniższy przykład zwraca `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>pierwszego
Zwraca pierwszy znak określonego ciągu; Pierwsza wartość określonej tablicy; lub pierwszy klucz i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład zwraca `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>ostatni
Zwraca ostatni znak określonego ciągu, ostatnią wartość określonej tablicy lub ostatni klucz i wartość określonego obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>czasochłonn
Zwraca określoną liczbę znaków sąsiadujących od początku ciągu, określoną liczbę ciągłych wartości od początku tablicy lub określoną liczbę ciągłych kluczy i wartości z początku obiektu.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Poniższy przykład zwraca `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Pomiń
Pomija określoną liczbę elementów w kolekcji, a następnie zwraca pozostałe elementy.

#### <a name="example-1-string"></a>Przykład 1: ciąg
Poniższy przykład zwraca `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Przykład 2: tablica
Przyjmij `element1` wartość `[1, 2, 3]`Returns. Poniższy przykład zwraca `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Przykład 3: obiekt
Przyjmij `element1` zwrot:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Poniższy przykład zwraca `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funkcje logiczne
Te funkcje mogą być używane w warunkach. Niektóre funkcje mogą nie obsługiwać wszystkich typów danych JSON.

### <a name="equals"></a>equals
Zwraca `true` czy oba parametry mają ten sam typ i wartość. Ta funkcja obsługuje wszystkie typy danych JSON.

Poniższy przykład zwraca `true`:

```json
"[equals(0, 0)]"
```

Poniższy przykład zwraca `true`:

```json
"[equals('foo', 'foo')]"
```

Poniższy przykład zwraca `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Zwraca `true` wartość, jeśli pierwszy parametr jest ściśle mniejszy niż drugi parametr. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true`:

```json
"[less(1, 2)]"
```

Poniższy przykład zwraca `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Zwraca `true` wartość, jeśli pierwszy parametr jest mniejszy lub równy drugiemu parametrowi. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Zwraca `true` wartość, jeśli pierwszy parametr jest ściśle większy niż drugi parametr. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `false`:

```json
"[greater(1, 2)]"
```

Poniższy przykład zwraca `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Zwraca `true` wartość, jeśli pierwszy parametr jest większy lub równy drugiemu parametrowi. Ta funkcja obsługuje parametry tylko typu Number i String.

Poniższy przykład zwraca `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>and
Zwraca `true` wartość, jeśli wszystkie parametry mają `true`być szacowane do. Ta funkcja obsługuje co najmniej dwa parametry typu Boolean.

Poniższy przykład zwraca `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład zwraca `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>lub
Zwraca `true` wartość, jeśli co najmniej jeden z parametrów ma `true`wartość. Ta funkcja obsługuje co najmniej dwa parametry typu Boolean.

Poniższy przykład zwraca `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Poniższy przykład zwraca `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Zwraca `true` wartość, jeśli parametr ma `false`wartość. Ta funkcja obsługuje tylko parametry typu Boolean.

Poniższy przykład zwraca `true`:

```json
"[not(false)]"
```

Poniższy przykład zwraca `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>łączonych
Zwraca wartość pierwszego niezerowego parametru. Ta funkcja obsługuje wszystkie typy danych JSON.

Przyjmij `element1` i `element2` nie są zdefiniowane. Poniższy przykład zwraca `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Ta funkcja jest szczególnie przydatna w kontekście opcjonalnego wywołania, które następuje z powodu akcji użytkownika po załadowaniu strony. Przykładem jest to, że ograniczenia wprowadzone dla jednego pola w interfejsie użytkownika zależą od aktualnie wybranej wartości innego, **początkowo niewidocznego** pola. W takim przypadku można `coalesce()` użyć, aby zezwolić funkcji na składniowo, w czasie ładowania strony, podczas gdy użytkownik współdziała z polem.

Należy to `DropDown`rozważyć, co umożliwia użytkownikowi wybór spośród kilku różnych typów baz danych:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Aby warunkować akcję innego pola dla aktualnie wybranej wartości tego pola, użyj `coalesce()`, jak pokazano poniżej:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Jest to konieczne, ponieważ `databaseType` początkowo nie jest widoczny i w związku z tym nie ma wartości. Powoduje to, że całe wyrażenie nie zostanie prawidłowo obliczone.

## <a name="conversion-functions"></a>Funkcje konwersji
Te funkcje mogą służyć do konwertowania wartości między typami danych JSON a kodowaniem.

### <a name="int"></a>int
Konwertuje parametr na liczbę całkowitą. Ta funkcja obsługuje parametry typu Number i String.

Poniższy przykład zwraca `1`:

```json
"[int('1')]"
```

Poniższy przykład zwraca `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konwertuje parametr na zmiennoprzecinkową. Ta funkcja obsługuje parametry typu Number i String.

Poniższy przykład zwraca `1.0`:

```json
"[float('1.0')]"
```

Poniższy przykład zwraca `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Konwertuje parametr na ciąg. Ta funkcja obsługuje parametry wszystkich typów danych JSON.

Poniższy przykład zwraca `"1"`:

```json
"[string(1)]"
```

Poniższy przykład zwraca `"2.9"`:

```json
"[string(2.9)]"
```

Poniższy przykład zwraca `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

Poniższy przykład zwraca `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konwertuje parametr na wartość logiczną. Ta funkcja obsługuje parametry typu Number, String i Boolean. Podobne do wartości logicznych w języku JavaScript, dowolnej wartości `0` z `'false'` wyjątkiem `true`lub zwraca.

Poniższy przykład zwraca `true`:

```json
"[bool(1)]"
```

Poniższy przykład zwraca `false`:

```json
"[bool(0)]"
```

Poniższy przykład zwraca `true`:

```json
"[bool(true)]"
```

Poniższy przykład zwraca `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Konwertuje parametr na typ natywny. Innymi słowy, ta funkcja jest odwrotnością `string()`. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `1`:

```json
"[parse('1')]"
```

Poniższy przykład zwraca `true`:

```json
"[parse('true')]"
```

Poniższy przykład zwraca `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

Poniższy przykład zwraca `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Koduje parametr do zakodowanego ciągu Base-64. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekoduje parametr z zakodowanego ciągu Base-64. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeURIComponent —
Koduje parametr na ciąg zakodowany w adresie URL. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Dekoduje parametr z ciągu zakodowanego w adresie URL. Ta funkcja obsługuje tylko parametry typu String.

Poniższy przykład zwraca `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funkcje matematyczne
### <a name="add"></a>add
Dodaje dwie liczby i zwraca wynik.

Poniższy przykład zwraca `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Odejmuje drugą liczbę od pierwszej liczby i zwraca wynik.

Poniższy przykład zwraca `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Mnoży dwie liczby i zwraca wynik.

Poniższy przykład zwraca `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dzieli pierwszą liczbę przez drugą liczbę i zwraca wynik. Wynik jest zawsze liczbą całkowitą.

Poniższy przykład zwraca `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Funkcja
Dzieli pierwszą liczbę przez drugą liczbę i zwraca resztę.

Poniższy przykład zwraca `0`:

```json
"[mod(6, 3)]"
```

Poniższy przykład zwraca `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Zwraca małe z dwóch liczb.

Poniższy przykład zwraca `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Zwraca większą liczbę dwóch liczb.

Poniższy przykład zwraca `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Generuje sekwencję liczb całkowitych w określonym zakresie.

Poniższy przykład zwraca `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Zwraca losową liczbę całkowitą znajdującą się w określonym zakresie. Ta funkcja nie generuje kryptograficznie zabezpieczonych liczb losowych.

Poniższy przykład może zwrócić `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>wykładzin
Zwraca największą liczbę całkowitą mniejszą lub równą podanej liczbie.

Poniższy przykład zwraca `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>CEIL —
Zwraca największą liczbę całkowitą większą lub równą podanej liczbie.

Poniższy przykład zwraca `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funkcje daty
### <a name="utcnow"></a>utcNow
Zwraca ciąg w formacie ISO 8601 bieżącej daty i godziny na komputerze lokalnym.

Poniższy przykład może zwrócić `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>AddSeconds
Dodaje całkowitą liczbę sekund do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addminut
Dodaje całkowitą liczbę minut do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addgodz.
Dodaje całkowitą liczbę godzin do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).

