---
title: Styleobject schemat referencyjny dla Azure Maps dynamicznej
description: Przewodnik referencyjny dotyczący schematu i składni dynamicznego Azure Maps Styleobject.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903347"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Styleobject schemat referencyjny dla map dynamicznych

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 `StylesObject`Jest `StyleObject` tablicą reprezentującą style stateset. Użyj [usługi stanu funkcji](/rest/api/maps/featurestate) Azure Maps Creator (wersja zapoznawcza), aby zastosować style stateset do funkcji danych mapy pomieszczeń. Po utworzeniu stylów stateset i skojarzeniu ich z funkcjami mapy pomieszczeń, można użyć ich do utworzenia dynamicznych map pomieszczeń. Aby uzyskać więcej informacji na temat tworzenia dynamicznych map pomieszczeń, zobacz [Implementuj style dynamiczne dla map pomieszczeń dla twórców](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>Styleobject

A `StyleObject` jest jednym z następujących reguł stylu:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

Poniższy kod JSON pokazuje przykładowe użycie każdego z trzech typów stylu.  Służy `BooleanTypeStyleRule` do określania stylu dynamicznego dla funkcji, których `occupied` Właściwość ma wartość true i false.  Służy `NumericTypeStyleRule` do określania stylu funkcji, których `temperature` Właściwość znajduje się w określonym zakresie. Na koniec `StringTypeStyleRule` jest używany do dopasowywania określonych stylów do `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 A `NumericTypeStyleRule`  [`StyleObject`](#styleobject) i składa się z następujących właściwości:

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `keyName` | ciąg | Nazwa właściwości *stan* lub dynamiczna. Element A `keyName` powinien być unikatowy wewnątrz `StyleObject` tablicy.| Tak |
| `type` | ciąg | Wartość jest wartością numeryczną. | Tak |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Tablica zakresów stylu liczbowego ze skojarzonymi kolorami. Każdy zakres definiuje kolor, który ma być używany, gdy wartość *stanu* spełnia zakres.| Tak |

### <a name="numberruleobject"></a>NumberRuleObject

A `NumberRuleObject` składa się z [`RangeObject`](#rangeobject) i `color` właściwości. Jeśli wartość *stanu* mieści się w zakresie, jego kolor wyświetlania będzie kolorem określonym we `color` właściwości.

Jeśli zdefiniujesz wiele nakładających się zakresów, wybrany kolor będzie kolorem zdefiniowanym w pierwszym zakresie, który jest spełniony.

W poniższym przykładzie JSON oba zakresy będą miały wartość true, gdy wartość *stanu* mieści się w przedziale od 50-60. Jednak kolor, który będzie używany jest, ponieważ jest to `#343deb` pierwszy zakres na liście, który został spełniony.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `range` | [Zakresobject](#rangeobject) | Element [rangeobject](#rangeobject) definiuje zestaw warunków logicznego zakresu, które w przypadku `true` zmiany koloru wyświetlania *stanu* na kolor określony we `color` właściwości. Jeśli `range` jest nieokreślony, kolor zdefiniowany we `color` właściwości będzie zawsze używany.   | Nie |
| `color` | ciąg | Kolor, który ma być używany, gdy wartość stanu mieści się w zakresie. `color`Właściwość jest CIĄGIEM JSON w jednym z następujących formatów: <ul><li> Wartości szesnastkowe w stylu HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Wstępnie zdefiniowane nazwy kolorów HTML, na przykład żółte i niebieskie.</li></ul> | Tak |

### <a name="rangeobject"></a>Zakresobject

`RangeObject`Definiuje wartość numerycznego zakresu [`NumberRuleObject`](#numberruleobject) . Aby wartość *stanu* należeć do zakresu, wszystkie zdefiniowane warunki muszą mieć wartość true.

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `minimum` | double | Cała liczba x x ≥ `minimum` .| Nie |
| `maximum` | double | Wszystkie liczby x, które x ≤ `maximum` . | Nie |
| `exclusiveMinimum` | double | Cała liczba x, która > x `exclusiveMinimum` .| Nie |
| `exclusiveMaximum` | double | Cała liczba x, która < x `exclusiveMaximum` .| Nie |

### <a name="example-of-numerictypestylerule"></a>Przykład NumericTypeStyleRule

Poniższy kod JSON ilustruje `NumericTypeStyleRule` *stan* o nazwie `temperature` . W tym przykładzie [`NumberRuleObject`](#numberruleobject) zawiera dwa zdefiniowane zakresy temperatury i skojarzone z nimi style kolorów. Jeśli zakres temperatury to 50-69, ekran powinien używać koloru `#343deb` .  Jeśli zakres temperatury to 31-70, ekran powinien używać koloru `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

A `StringTypeStyleRule` [`StyleObject`](#styleobject) i składa się z następujących właściwości:

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `keyName` | ciąg |  Nazwa właściwości *stan* lub dynamiczna.  Element A `keyName` powinien być unikatowy wewnątrz  `StyleObject` tablicy.| Tak |
| `type` | ciąg |Wartość to "String". | Tak |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Tablica N-liczbowych wartości *stanu* .| Tak |

### <a name="stringruleobject"></a>StringRuleObject

`StringRuleObject`Składa się z maksymalnie N liczb wartości stanu, które są możliwymi wartościami ciągu właściwości funkcji. Jeśli wartość właściwości funkcji nie jest zgodna z żadną z zdefiniowanych wartości stanu, ta funkcja nie będzie mieć stylu dynamicznego. Jeśli podano zduplikowane wartości stanu, pierwszeństwo ma pierwszy.

W dopasowaniu wartości ciągu rozróżniana jest wielkość liter.

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `stateValue1` | ciąg | Kolor, gdy wartość jest równa stateValue1. | Nie |
| `stateValue2` | ciąg | Kolor, gdy wartość jest równa stateValue. | Nie |
| `stateValueN` | ciąg | Kolor, gdy wartość jest równa stateValueN. | Nie |

### <a name="example-of-stringtypestylerule"></a>Przykład StringTypeStyleRule

Poniższy kod JSON ilustruje `StringTypeStyleRule` , że definiuje style skojarzone z określonymi typami spotkań.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` [`StyleObject`](#styleobject) i składa się z następujących właściwości:

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `keyName` | ciąg |  Nazwa właściwości *stan* lub dynamiczna.  Element A `keyName` powinien być unikatowy wewnątrz `StyleObject`  tablicy.| Tak |
| `type` | ciąg |Wartość to "Boolean". | Tak |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)jedno| Para logiczna z kolorami `true` i wartościami `false` *stanu* .| Tak |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`Definiuje kolory dla `true` i `false` wartości.

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `true` | ciąg | Kolor, który ma być używany, gdy wartość *stanu* to `true` . `color`Właściwość jest CIĄGIEM JSON w jednym z następujących formatów: <ul><li> Wartości szesnastkowe w stylu HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Wstępnie zdefiniowane nazwy kolorów HTML, na przykład żółte i niebieskie.</li></ul>| Tak |
| `false` | ciąg | Kolor, który ma być używany, gdy wartość *stanu* to `false` . | Tak |

### <a name="example-of-booleantypestylerule"></a>Przykład BooleanTypeStyleRule

Poniższy kod JSON ilustruje `BooleanTypeStyleRule` *stan* o nazwie `occupied` . [`BooleanRuleObject`](#booleanruleobject)Definiuje kolory `true` i `false` wartości.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```