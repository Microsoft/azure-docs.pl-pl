---
title: Zastępowanie materiałów podczas konwersji modelu
description: Wyjaśnia przepływ pracy zastępowania materiału w czasie konwersji
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 2e9cb216c100f1732230a90572284bd3f8462584
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433134"
---
# <a name="override-materials-during-model-conversion"></a>Zastępowanie materiałów podczas konwersji modelu

Ustawienia materiału w modelu źródłowym są używane do definiowania [materiałów PBR](../../overview/features/pbr-materials.md) używanych w module renderowania.
Czasami [Konwersja domyślna](../../reference/material-mapping.md) nie daje żądanych wyników i należy wprowadzić zmiany.
Gdy model jest konwertowany do użycia w przypadku renderowania zdalnego na platformie Azure, można dostarczyć plik przesłaniający materiał, aby dostosować sposób konwersji materiału dla poszczególnych materiałów.
Sekcja dotycząca [konfigurowania konwersji modelu](configure-model-conversion.md) zawiera instrukcje dotyczące deklarowania nazwy pliku zastępującego materiał.

## <a name="the-override-file-used-during-conversion"></a>Plik przesłonięcia używany podczas konwersji

W prostym przykładzie Załóżmy, że model Box ma pojedynczy materiał o nazwie "default".
Ponadto Załóżmy, że kolor albedo musi zostać dostosowany do użycia w ARR.
W takim przypadku `box_materials_override.json` plik można utworzyć w następujący sposób:

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

`box_materials_override.json`Plik znajduje się w kontenerze wejściowym i `box.ConversionSettings.json` jest dodawany obok `box.fbx` , który informuje o konwersji, gdzie znaleźć plik przesłonięcia (zobacz [Konfigurowanie konwersji modelu](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Po przekonwertowaniu modelu zostaną zastosowane nowe ustawienia.

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

## <a name="json-schema"></a>Schemat JSON

Pełny schemat JSON dla plików materiałów jest podawany w tym miejscu. Z wyjątkiem `unlit` i `ignoreTextureMaps` , dostępne właściwości są podzbiorem właściwości opisanych w sekcjach na [materiałach kolorów](../../overview/features/color-materials.md) i w modelu. [PBR material](../../overview/features/pbr-materials.md)

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