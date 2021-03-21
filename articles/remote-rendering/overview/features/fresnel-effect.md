---
title: Efekt Fresnela
description: Strona wyjaśnienia funkcji dla efektu materiału wygaszania Fresnela
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594116"
---
# <a name="fresnel-effect"></a>Efekt Fresnela

Funkcja materiału wygaszania Fresnela efekt jest niefizycznie poprawna. Ta funkcja jest oparta na fizycznej obserwowanie obiektów, które stają się bardziej odbijające pod tymi kątami. Odbicie wygaszania Fresnela jest już fizycznie wbudowane w [modelu materiałowym PBR](../../overview/features/pbr-materials.md) używanym podczas renderowania zdalnego platformy Azure. W przeciwieństwie do funkcji materiału wygaszania Fresnela efekt jest tylko dodatkowy efekt koloru bez zależności od [świateł](../../overview/features/lights.md) lub [środowiska](../../overview/features/sky.md).

Efekt wygaszania Fresnela daje obiektom, których to dotyczy, jest to kolor, którego krawędzie są wyświetlane. Informacje o dostosowywaniu efektów i przykładach wyników renderowania można znaleźć w poniższych sekcjach.

## <a name="enabling-the-fresnel-effect"></a>Włączanie efektu wygaszania Fresnela

Aby można było korzystać z funkcji wygaszania Fresnela Effect, należy ją włączyć w danych materiałach. Można ją włączyć, ustawiając FresnelEffect bit [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) w [materiale PBR](../../overview/features/pbr-materials.md). Ten sam wzorzec dotyczy [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) i [materiału koloru](../../overview/features/color-materials.md). Zobacz sekcję przykłady kodu, aby zapoznać się z prezentacją użycia.

Po włączeniu efekt wygaszania Fresnela będzie od razu widoczny. Domyślnie jest to kolor biały (1, 1, 1, 1) i ma wykładnik potęgi 1. Te ustawienia można dostosować za pomocą metod ustawianych poniżej.

## <a name="customizing-the-effect-appearance"></a>Dostosowywanie wyglądu efektu

Obecnie efekt wygaszania Fresnela można dostosować na materiał przy użyciu następujących właściwości:

| Właściwość materiału | Typ | Wyjaśnienie |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Kolor, który zostanie dodany najwyżej jako wygaszania Fresnela. Kanał alfa jest obecnie ignorowany. |
| FresnelEffectExponent | float | Rozproszenie wygaszania Fresnela. Zakresy od 0,01 (rozłożone na cały obiekt) do 10 (tylko najbardziej gracinge kąty). |

W przypadku różnych ustawień koloru i wykładnika będzie wyglądać następująco:

![Przykłady efektów wygaszania Fresnela](./media/fresnel-effect-examples.png)

Wartość wykładnika efektu wygaszania Fresnela jest stopniowo zwiększana z 1 do 10 dla każdego wiersza koloru. Dzięki temu progresywnie ściąga wygaszania fresnelay do krawędzi oglądanych obiektów. Przezroczystość nie ma wpływu na efekt wygaszania Fresnela, jak widać w poniższym przykładzie:

![Przykłady przezroczystości efektów wygaszania Fresnela](./media/fresnel-effect-transparent-examples.png)

Jak pokazano, obiekty na przekątnej są w pełni przezroczyste, ale wygaszania Fresnela nie pozostało. Efekt naśladuje fizycznie oparte wygaszania Fresnela w tym zakresie, który jest również obecny w tych zrzutach ekranu.

## <a name="code-samples"></a>Przykłady kodu

W poniższych przykładach kodu pokazano, jak włączyć i dostosować efekt wygaszania Fresnela dla [materiału](../../overview/features/pbr-materials.md) i [koloru](../../overview/features/color-materials.md)w języku PBR:

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [PbrMaterialFeatures C#](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [ColorMaterialFeatures C#](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Następne kroki

* [Materiały](../../concepts/materials.md)
* [Materiały PBR](../../overview/features/pbr-materials.md)
* [Materiały koloru](../../overview/features/color-materials.md)