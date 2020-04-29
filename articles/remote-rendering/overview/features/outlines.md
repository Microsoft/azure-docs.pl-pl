---
title: Renderowanie konturów
description: Wyjaśniono, jak przeprowadzić renderowanie w konspekcie zaznaczenia
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680832"
---
# <a name="outline-rendering"></a>Renderowanie konturów

Wybrane obiekty można wizualnie podświetlone, dodając renderowanie konspektu za pośrednictwem [składnika zastąpienia stanu hierarchicznego](../../overview/features/override-hierarchical-state.md). W tym rozdziale wyjaśniono, jak globalne parametry renderowania w konspekcie są zmieniane za pomocą interfejsu API klienta.

Właściwości konspektu to ustawienie globalne. Wszystkie obiekty, które używają renderowania konturu, będą używać tego samego ustawienia — nie można użyć koloru konturu dla obiektu.

## <a name="parameters-for-outlinesettings"></a>Parametry dla`OutlineSettings`

Klasa `OutlineSettings` przechowuje ustawienia związane z właściwościami konspektu globalnego. Uwidacznia następujące elementy członkowskie:

| Parametr      | Typ    | Opis                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Kolor używany do rysowania konspektu. Część alfa jest ignorowana.         |
| `PulseRateHz`    | float   | Szybkość, z jaką krzywe konspektu na sekundę|
| `PulseIntensity` | float   | Intensywność efektu impulsu konturu. Musi mieć długość od 0,0 do bez napędu i 1,0 do pełnego napędu. Intensywność ustawia minimalną nieprzezroczystość konspektu `MinOpacity = 1.0 - PulseIntensity`jako. |

![Pokreśl](./media/outlines.png) skutki zmiany `color` parametru z żółtego (z lewej) na amarantowy (wyśrodkuj) i `pulseIntensity` od 0 do 0,8 (prawo).

## <a name="example"></a>Przykład

Poniższy kod przedstawia przykład ustawiania parametrów konspektu za pośrednictwem interfejsu API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Wydajność

Renderowanie konspektu może mieć znaczny wpływ na wydajność renderowania. Ten wpływ zależy od relacji przestrzennej między zaznaczonymi i niewybranymi obiektami dla danej ramki.

## <a name="next-steps"></a>Następne kroki

* [Składnik przesłonięcia stanu hierarchicznego](../../overview/features/override-hierarchical-state.md)
