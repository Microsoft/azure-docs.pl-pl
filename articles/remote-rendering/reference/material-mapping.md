---
title: Mapowanie materiałów dla formatów modelu
description: Opisuje konwersję domyślną z formatów źródłowych modelu do materiału PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024048"
---
# <a name="material-mapping-for-model-formats"></a>Mapowanie materiałów dla formatów modelu

Gdy zasób źródłowy jest [konwertowany jako model](../how-tos/conversion/model-conversion.md), konwerter tworzy [materiały](../concepts/materials.md) dla każdej [siatki](../concepts/meshes.md). Sposób tworzenia materiałów można [przesłonić](../how-tos/conversion/override-materials.md). Jednak domyślnie konwersja spowoduje utworzenie [materiałów PBR](../overview/features/pbr-materials.md). Ponieważ każdy format pliku źródłowego, na przykład FBX, używa własnych Konwencji do definiowania materiałów, te konwencje muszą być mapowane do parametrów materiałowych PBR w przypadku renderowania zdalnego na platformie Azure. 

W tym artykule wymieniono dokładne mapowania używane do konwertowania materiałów z zasobów źródłowych do materiałów środowiska uruchomieniowego.

## <a name="gltf"></a>glTF

Niemal wszystko z specyfikacji glTF 2,0 jest obsługiwane w przypadku renderowania zdalnego na platformie Azure, z wyjątkiem *EmissiveFactor* i *EmissiveTexture*.

W poniższej tabeli przedstawiono mapowanie:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalowy                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   niesztywność                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   zamknięcia                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphamode. unkryjący  |   alphaClipEnabled = false, istransparentd = false |
|   alphamode. MASK    |   alphaClipEnabled = true, istransparentd = false  |
|   alphamode. BLEND   |   istransparentd = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Każda tekstura w glTF może mieć `texCoord` wartość, która jest również obsługiwana w materiałach zdalnego renderowania na platformie Azure.

### <a name="embedded-textures"></a>Tekstury osadzone

Tekstury osadzone w plikach *\* . bin* lub *\* . GLB* są obsługiwane.

### <a name="supported-gltf-extension"></a>Obsługiwane rozszerzenie glTF

Oprócz zestawu funkcji podstawowych funkcja renderowania zdalnego platformy Azure obsługuje następujące rozszerzenia glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): odpowiada [materiałom koloru](../overview/features/color-materials.md). W przypadku materiałów *emisyjny* zaleca się użycie tego rozszerzenia.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): zamiast tekstur metalicznych o niesztywności można udostępniać tekstury rozproszone-odblasków-glossiness. Implementacja renderowania zdalnego platformy Azure jest bezpośrednio zgodna z formułami konwersji z rozszerzenia.

## <a name="fbx"></a>FBX

Format FBX jest zamknięty, a materiały FBX nie są zgodne z materiałami PBR. FBX używa złożonego opisu powierzchni z wieloma unikatowymi parametrami i właściwościami, a **nie wszystkie z nich są używane przez potok renderowania zdalnego na platformie Azure**.

> [!IMPORTANT]
> Potok konwersji modelu renderowania zdalnego platformy Azure obsługuje tylko **FBX 2011 i nowsze**.

Format FBX definiuje podejście, które jest bardziej ostrożne dla materiałów, istnieją tylko dwa typy w oficjalnych specyfikacjach FBX:

* *Lamberta* — nie jest to często używane przez dość jakiś czas, ale jest nadal obsługiwane przez konwersję do podstawowego Phong w czasie konwersji.
* *Podstawowego Phong* — prawie wszystkie materiały i większość narzędzi zawartości używają tego typu.

Model podstawowego Phong jest bardziej dokładny i służy jako *jedyny* model dla materiałów FBX. Poniżej będzie ona określana jako *materiał FBX*.

> Maya używa dwóch rozszerzeń niestandardowych dla FBX przez zdefiniowanie właściwości niestandardowych dla typów materiału PBR i Stingray. Te szczegóły nie są uwzględnione w specyfikacji FBX, więc nie są obsługiwane przez zdalne renderowanie na platformie Azure.

Materiały FBX używają koncepcji rozpraszania-odblasków-SpecularLevel, dlatego konwersja z tekstury rozproszonej na mapę albedo musi obliczyć inne parametry, aby odjąć je od rozpraszania.

> Wszystkie kolory i tekstury w FBX znajdują się w przestrzeni sRGB (zwanej również spacją gamma), ale zdalne renderowanie na platformie Azure działa z odstępem liniowym podczas wizualizacji, a na końcu ramki konwertuje wszystko z powrotem do obszaru sRGB. Potok zasobów renderowania zdalnego na platformie Azure konwertuje wszystko do miejsca liniowego, aby wysłać je jako przygotowane dane do modułu renderowania.

W tej tabeli przedstawiono, w jaki sposób tekstury są mapowane z materiałów FBX do materiałów renderowania zdalnego platformy Azure. Niektóre z nich nie są bezpośrednio używane, ale w połączeniu z innymi teksturami, które uczestniczą w formułach (na przykład tekstury rozpraszania):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Mapa zamknięcia   |
| DiffuseColor | *używane na potrzeby Albedoości, metalu* |
| TransparentColor | *używane dla kanału alfa albedo* |
| TransparencyFactor | *używane dla kanału alfa albedo* |
| Spalin | *używane dla kanału alfa albedo* |
| SpecularColor | *używane na potrzeby Albedoości, metalu, niesztywności* |
| SpecularFactor| *używane na potrzeby Albedoości, metalu, niesztywności* |
| ShininessExponent | *używane na potrzeby Albedoości, metalu, niesztywności* |
| NormalMap | NormalMap |
| Wstrząs | *przekonwertowano na NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Powyższe mapowanie to najbardziej złożona część konwersji materiału ze względu na wiele założeń, które należy wykonać. Omawiamy poniższe założenia.

Niektóre definicje użyte poniżej:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Red ∗ 0,2125 +  `Specular` . Zielony ∗ 0,7154 + `Specular` . Niebieska ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Czerwony<sup>2</sup> + 0,587 * `Diffuse` . Zielony<sup>2</sup> + 0,114 * `Diffuse` . Niebieski<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Czerwony<sup>2</sup> + 0,587 * `Specular` . Zielony<sup>2</sup> + 0,114 * `Specular` . Niebieski<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Czerwony, `Specular` . Zielony, `Specular` . Świetlon

W [tym miejscu](https://en.wikipedia.org/wiki/Luma_(video))zostanie uzyskana formuła SpecularIntensity.
Formuła jasności jest opisana w tej [specyfikacji](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Niesztywność

`Roughness` jest obliczany z `Specular` i `ShininessExponent` przy użyciu [tej formuły](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). Formuła to przybliżenie przybliżonej wartości z wykładnika podstawowego Phong odblasków:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalowy

`Metalness` jest obliczany z `Diffuse` i `Specular` przy użyciu tej [formuły ze specyfikacji glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Pomysłem jest to, że rozwiązujemy równanie: AX<sup>2</sup> + BX + C = 0.
Zasadniczo, oddzielne powierzchnie odzwierciedlają około 4% światła w odblasków sposób, a reszta jest rozpraszana. Powierzchnie metalowe nie odzwierciedlają światła w sposób rozpraszania, ale wszystko to w odblasków sposób.
Ta formuła zawiera kilka wad, ponieważ nie ma możliwości rozróżniania między błyszczącymi plastikowymi i błyszczącymi powierzchniami metalicznymi. Przyjmujemy większość czasu, gdy powierzchnia ma metaliczne właściwości, a w efekcie błyszczące plastyczne i gumowe powierzchnie mogą nie wyglądać zgodnie z oczekiwaniami.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` jest obliczany z `Diffuse` , `Specular` , i `Metalness` .

Zgodnie z opisem w sekcji metalu, powierzchnie odelektryczne odzwierciedlają około 4% światła.  
Pomysłem jest liniowe Interpolacja między `Dielectric` i `Metal` kolorami przy użyciu `Metalness` wartości jako czynnika. Jeśli metalu jest `0.0` , w zależności od odblasków będzie to kolor ciemny (jeśli odblasków jest wysoki) lub Dyfuzja nie ulegnie zmianie (jeśli nie odblasków jest obecny). Jeśli metal jest dużą wartością, kolor rozpraszania będzie znikał na korzyść koloru odblasków.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` został obliczony przez powyższą formułę, ale kanał alfa wymaga dodatkowych obliczeń. Format FBX jest niezrozumiały dla przejrzystości i ma wiele sposobów na jego Definiowanie. Różne narzędzia zawartości używają różnych metod. Dobrym pomysłem jest ujednolicenie ich do jednej formuły. Niektóre elementy zawartości nieprawidłowo pokazywane jako przezroczyste, ale jeśli nie są tworzone w typowy sposób.

Jest on obliczany z `TransparentColor` , `TransparencyFactor` , `Opacity` :

Jeśli `Opacity` jest zdefiniowany, użyj go bezpośrednio: `AlbedoAlpha`  =  `Opacity` else  
Jeśli `TransparencyColor` jest zdefiniowany, then `AlbedoAlpha` = 1,0-(( `TransparentColor` . Czerwony + `TransparentColor` . Zielony + `TransparentColor` . Niebieski)/3,0) inny  
Jeśli `TransparencyFactor` , then `AlbedoAlpha` = 1,0- `TransparencyFactor`

Końcowy `Albedo` kolor ma cztery kanały, łącząc przy `AlbedoRGB` użyciu `AlbedoAlpha` .

### <a name="summary"></a>Podsumowanie

Aby podsumować w tym miejscu, `Albedo` będzie blisko oryginalnego `Diffuse` , jeśli `Specular` jest bliski zeru. W przeciwnym razie powierzchnia będzie wyglądać jak metalowa powierzchnia i utraci kolor rozpraszania. Powierzchnia będzie wyglądać bardziej dopracowane i odzwierciedlać, jeśli `ShininessExponent` jest wystarczająco duża i `Specular` jest jasna. W przeciwnym razie powierzchnia zostanie wyświetlona i stanowią jedynie ułamek odzwierciedla środowisko.

### <a name="known-issues"></a>Znane problemy

* Bieżąca formuła nie działa prawidłowo w przypadku prostej geometrycznej geometrii. Jeśli `Specular` jest wystarczająco krótki, wszystkie geometrie stają się odbijającymi powierzchniami metalicznymi bez żadnego koloru. Obejście tego problemu jest mniejsze `Specular` do 30% od oryginału lub do użycia ustawienia konwersji [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* Zostały ostatnio dodane do `Maya` `3DS Max` narzędzi i tworzenia zawartości. Wykorzystują one niestandardowe właściwości czarnego pola zdefiniowane przez użytkownika, aby przekazać je do FBX. Zdalne renderowanie na platformie Azure nie odczytuje tych dodatkowych właściwości, ponieważ nie są one udokumentowane i format jest zamknięty.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](../how-tos/conversion/model-conversion.md)
* [Zastępowanie materiałów podczas konwersji modelu](../how-tos/conversion/override-materials.md)
