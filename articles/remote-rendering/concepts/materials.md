---
title: Materiały
description: Renderowanie opisu materiału i właściwości materiału
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4406e3875bd2d56a33bcc9a94a24ad874fecf8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592181"
---
# <a name="materials"></a>Materiały

Materiały są [zasobami udostępnionymi](../concepts/lifetime.md) , które definiują sposób renderowania [siatki](meshes.md) . Materiały są używane do określania, które [tekstury](textures.md) należy zastosować, czy obiekty mają być przezroczyste i jak będą obliczane oświetlenie.

Materiały są tworzone automatycznie podczas [konwersji modelu](../how-tos/conversion/model-conversion.md) i są dostępne w czasie wykonywania. Możesz również utworzyć niestandardowe materiały z kodu i zamienić istniejące. W tym scenariuszu warto szczególnie zorientować się, czy chcesz współużytkować ten sam materiał w wielu siatkach. Ze względu na to, że modyfikacje materiału są widoczne dla każdej siatki, która odwołuje się do niej, ta metoda może być używana do łatwego stosowania zmian.

> [!NOTE]
> Niektóre przypadki użycia, takie jak wyróżnienie wybranego obiektu można wykonać, modyfikując materiały, ale są znacznie łatwiejsze w [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Typy materiału

Zdalne renderowanie na platformie Azure ma dwa różne typy materiałów:

* [Materiały PBR](../overview/features/pbr-materials.md) są używane w przypadku powierzchni, które powinny być renderowane jako fizycznie poprawne, jak to możliwe. Realistyczne oświetlenie jest obliczane dla tych materiałów przy użyciu *renderowania opartego na fizycznie* (PBR). Aby maksymalnie wykorzystać ten typ materiału, ważne jest, aby zapewnić wysokiej jakości dane wejściowe, takie jak surowce i mapy normalne.

* [Materiały koloru](../overview/features/color-materials.md) są używane w przypadkach, gdy nie jest potrzebne żadne dodatkowe oświetlenie. Te materiały są zawsze w pełni jasne i łatwiejsze do skonfigurowania. Materiały koloru są używane w przypadku danych, które nie powinny być w ogóle dostępne, czy też są już poddane statyczne oświetlenie, takie jak modele uzyskane za pomocą [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>MeshComponent — przypisanie materiału siatki

[Siatki](meshes.md) mają co najmniej jedną podsiatkę. Każda Podsiatka odwołuje się do jednego materiału. Można zmienić materiał do użycia bezpośrednio w siatce lub można przesłonić materiał, który ma być używany dla podsiatki w [MeshComponent](meshes.md#meshcomponent).

Gdy modyfikujesz materiał bezpośrednio na zasobów siatki, ta zmiana wpłynie na wszystkie wystąpienia tej siatki. Jednak zmiana tego obiektu na MeshComponent ma wpływ tylko na jedno wystąpienie siatki. Która metoda jest bardziej odpowiednia, zależy od żądanego zachowania, ale modyfikacja MeshComponent jest bardziej typowym podejściem.

## <a name="material-de-duplication"></a>Usuwanie materiału z deduplikacji

Podczas konwersji wiele materiałów z tymi samymi właściwościami i teksturami są automatycznie deduplikowane do jednego materiału. Tę funkcję można wyłączyć w [ustawieniach konwersji](../how-tos/conversion/configure-model-conversion.md), ale zalecamy jej pozostawienie w celu uzyskania najlepszej wydajności.

## <a name="material-classes"></a>Klasy materiału

Wszystkie materiały dostarczone przez interfejs API pochodzą z klasy podstawowej `Material` . Ich typ może być wysyłany przez `Material.MaterialSubType` lub przez rzutowanie ich bezpośrednio:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa materiału C#](/dotnet/api/microsoft.azure.remoterendering.material)
* [Klasa ColorMaterial języka C#](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [Klasa PbrMaterial języka C#](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. setmateriał ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [Klasa materiału C++](/cpp/api/remote-rendering/material)
* [Klasa C++ ColorMaterial](/cpp/api/remote-rendering/colormaterial)
* [Klasa C++ PbrMaterial](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Następne kroki

* [Materiały PBR](../overview/features/pbr-materials.md)
* [Materiały kolorów](../overview/features/color-materials.md)