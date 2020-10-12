---
title: Renderowanie konturów
description: Wyjaśniono, jak przeprowadzić renderowanie w konspekcie zaznaczenia
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5dd6d682c9db044763cad64eec420c1974d4ac03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613710"
---
# <a name="outline-rendering"></a>Renderowanie konturów

Wybrane obiekty można wizualnie podświetlone, dodając renderowanie konspektu za pośrednictwem [składnika zastąpienia stanu hierarchicznego](../../overview/features/override-hierarchical-state.md). W tym rozdziale wyjaśniono, jak globalne parametry renderowania w konspekcie są zmieniane za pomocą interfejsu API klienta.

Właściwości konspektu to ustawienie globalne. Wszystkie obiekty, które używają renderowania konturu, będą używać tego samego ustawienia — nie można użyć koloru konturu dla obiektu.

## <a name="parameters-for-outlinesettings"></a>Parametry dla `OutlineSettings`

Klasa `OutlineSettings` przechowuje ustawienia związane z właściwościami konspektu globalnego. Uwidacznia następujące elementy członkowskie:

| Parametr      | Typ    | Opis                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Kolor używany do rysowania konspektu. Część alfa jest ignorowana.         |
| `PulseRateHz`    | float   | Szybkość, z jaką krzywe konspektu na sekundę|
| `PulseIntensity` | float   | Intensywność efektu impulsu konturu. Musi mieć długość od 0,0 do bez napędu i 1,0 do pełnego napędu. Intensywność ustawia minimalną nieprzezroczystość konspektu jako `MinOpacity = 1.0 - PulseIntensity` . |

![Obiekt renderowany trzykrotnie przy użyciu różnych parametrów konspektu ](./media/outlines.png) , wpływ zmiany `color` parametru z żółtego (z lewej) na amarantowy (środek) i `pulseIntensity` od 0 do 0,8 (prawo).

## <a name="example"></a>Przykład

Poniższy kod przedstawia przykład ustawiania parametrów konspektu za pośrednictwem interfejsu API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Wydajność

Renderowanie konspektu może mieć znaczny wpływ na wydajność renderowania. Ten wpływ zależy od relacji przestrzennej między zaznaczonymi i niewybranymi obiektami dla danej ramki.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Właściwość RemoteManager. OutlineSettings języka C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [Zdalnymanager:: OutlineSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Następne kroki

* [Składnik przesłonięcia stanu hierarchicznego](../../overview/features/override-hierarchical-state.md)
