---
title: Zastępowanie materiałów podczas konwersji modelu
description: Wyjaśnia przepływ pracy zastępowania materiału w czasie konwersji
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576646"
---
# <a name="override-materials-during-model-conversion"></a>Zastępowanie materiałów podczas konwersji modelu

Ustawienia materiału w modelu źródłowym są używane do definiowania [materiałów PBR](../../overview/features/pbr-materials.md) używanych w module renderowania.
Czasami [Konwersja domyślna](../../reference/material-mapping.md) nie daje żądanych wyników i należy wprowadzić zmiany.
Gdy model jest konwertowany do użycia w przypadku zdalnego renderowania na platformie Azure, można dostarczyć plik zastąpień materiału, aby dostosować sposób konwersji materiału na podstawie materiału.
Jeśli plik o nazwie `<modelName>.MaterialOverrides.json` znajduje się w kontenerze danych wejściowych obok modelu wejściowego `<modelName>.<ext>` , będzie używany jako plik zastąpienia materiału.

## <a name="the-override-file-used-during-conversion"></a>Plik przesłonięcia używany podczas konwersji

W prostym przykładzie Załóżmy, że model Box ma pojedynczy materiał o nazwie "default".
Ponadto Załóżmy, że kolor albedo musi zostać dostosowany do użycia w ARR.
W takim przypadku `box.MaterialOverrides.json` plik można utworzyć w następujący sposób:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json`Plik zostanie umieszczony w kontenerze danych wejściowych obok `box.fbx` , co oznacza, że usługa konwersji będzie stosowała nowe ustawienia.

### <a name="color-materials"></a>Materiały kolorów

Model [materiału kolorów](../../overview/features/color-materials.md) opisuje ciągle zacieniowaną powierzchnię, która jest niezależna od oświetlenia.
Materiały koloru są przydatne w przypadku zasobów wykonanych przez algorytmy photogrammetry, na przykład.
W plikach zastąpienia plików materiał może być zadeklarowany jako materiał koloru przez ustawienie `unlit` do `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignoruj określone mapy tekstury

Czasami może zaistnieć potrzeba ignorowania określonych map tekstury przez proces konwersji. Może się tak zdarzyć w przypadku, gdy model został wygenerowany przez narzędzie generujące specjalne mapy, które nie są prawidłowo zrozumiałe dla modułu renderowania. Na przykład "OpacityMap", który jest używany do definiowania czegoś innego niż krycie lub model, w którym "NormalMap" jest przechowywany jako "BumpMap". (W tym ostatnim przypadku chcesz zignorować "NormalMap", co spowoduje, że konwerter użyje "BumpMap" jako "NormalMap").

Zasada jest prosta. Wystarczy dodać właściwość o nazwie `ignoreTextureMaps` i dodać dowolną mapę tekstury, która ma być ignorowana:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Aby zapoznać się z pełną listą map tekstury, które można zignorować, zobacz poniższy schemat JSON.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Stosowanie tych samych zastąpień w wielu materiałach

Domyślnie wpis w pliku zastąpień materiału ma zastosowanie, gdy jego nazwa pasuje dokładnie do nazwy materiału.
Ponieważ to samo przesłonięcie ma zastosowanie do wielu materiałów, opcjonalnie można podać wyrażenie regularne jako nazwę wpisu.
Pole `nameMatching` ma wartość domyślną `exact` , ale można ustawić do stanu, w `regex` którym wpis ma być stosowany do każdego pasującego materiału.
Używana składnia jest taka sama jak w przypadku języka JavaScript. W poniższym przykładzie przedstawiono przesłonięcie, które ma zastosowanie do materiałów z nazwami takimi jak "Material2", "Material01" i "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Co najwyżej jeden wpis w pliku zastąpienia materiału ma zastosowanie do pojedynczego materiału.
Jeśli istnieje dokładne dopasowanie (tj. `nameMatching` jest nieobecne lub równe `exact` ) dla nazwy materiału, ten wpis jest wybierany.
W przeciwnym razie jest wybierany pierwszy wpis wyrażenia regularnego w pliku, który pasuje do nazwy materiału.

### <a name="getting-information-about-which-entries-applied"></a>Pobieranie informacji o zastosowanych wpisach

[Plik informacji](get-information.md#information-about-a-converted-model-the-info-file) zapisany w kontenerze danych wyjściowych zawiera informacje o liczbie podanych zastąpień oraz o liczbie przesłoniętych materiałów.

## <a name="json-schema"></a>Schemat JSON

Pełny schemat JSON dla plików materiałów jest podawany w tym miejscu. Z wyjątkiem `unlit` i `ignoreTextureMaps` , dostępne właściwości są podzbiorem właściwości opisanych w sekcjach na [materiałach kolorów](../../overview/features/color-materials.md) i w modelu. [](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Materiały kolorów](../../overview/features/color-materials.md)
* [Materiały PBR](../../overview/features/pbr-materials.md)
