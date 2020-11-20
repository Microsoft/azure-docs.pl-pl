---
title: Styleobject dla Azure Maps dynamicznej
description: Przewodnik referencyjny dotyczący schematu i składni JSON dla obiektu Styleobject używanego podczas tworzenia w Azure Maps dynamicznym.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4284956138002d209ab0934cdd052748ef8aab78
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966279"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Styleobject schemat referencyjny dla map dynamicznych

 `StylesObject`Jest `StyleObject` tablicą reprezentującą style stateset. Użyj [usługi stanu funkcji](/rest/api/maps/featurestate) twórcy Azure Maps, aby zastosować style stateset do funkcji danych mapy pomieszczeń. Po utworzeniu stylów stateset i skojarzeniu ich z funkcjami mapy pomieszczeń, można użyć ich do utworzenia dynamicznych map pomieszczeń. Aby uzyskać więcej informacji na temat tworzenia dynamicznych map pomieszczeń, zobacz [Implementuj style dynamiczne dla map pomieszczeń dla twórców](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>Styleobject

A `StyleObject` jest albo [`BooleanTypeStyleRule`](#booleantypestylerule) [`NumericTypeStyleRule`](#numerictypestylerule) .

Poniższy kod JSON zawiera `BooleanTypeStyleRule` nazwę `occupied` i `NumericTypeStyleRule` nazwę `temperature` .

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

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A `BooleanTypeStyleRule` [`StyleObject`](#styleobject) i składa się z następujących właściwości:

| Właściwość | Typ | Opis | Wymagane |
|-----------|----------|-------------|-------------|
| `keyName` | ciąg |  Nazwa właściwości *stan* lub dynamiczna.  `keyName`Powinna być unikatowa wewnątrz tablicy stylów.| Tak |
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