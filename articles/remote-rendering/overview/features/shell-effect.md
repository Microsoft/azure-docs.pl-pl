---
title: Renderowanie powłoki
description: Wyjaśnia, jak używać efektu renderowania powłoki
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594167"
---
# <a name="shell-rendering"></a>Renderowanie powłoki

Stan powłoki [składnika przesłaniania hierarchicznego](../../overview/features/override-hierarchical-state.md) jest efektem przezroczystości. W przeciwieństwie [do renderowania widocznego](../../overview/features/override-hierarchical-state.md) , tylko pierwsza warstwa obiektów jest widoczna, zbliżone do nieprzezroczystego renderowania. Ponadto normalne wygląd obiektów można zmienić, gdy są renderowane jako powłoki. Ma to wpływ na przypadki użycia, w przypadku których użytkownik powinien być wizualnie odłożony od nieistotnych części przy zachowaniu świadomości przestrzennej dla całej sceny.

Można skonfigurować wygląd obiektów renderowanych powłokowo za pośrednictwem `ShellRenderingSettings` globalnego stanu. Wszystkie obiekty używające renderowania powłoki będą używać tego samego ustawienia. Brak parametrów dla obiektu.

## <a name="shellrenderingsettings-parameters"></a>Parametry ShellRenderingSettings

Klasa `ShellRenderingSettings` przechowuje ustawienia związane z właściwościami renderowania powłoki globalnej:

| Parametr      | Typ    | Opis                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | Stopień nasycenia stosowany do normalnego koloru obiektu końcowego, w zakresie 0 (bez nasycenia) do 1 (pełne nasycenie) |
| `Opacity`      | float   | Nieprzezroczystość obiektów renderowanych w ramach powłoki, w zakresie 0 (niewidoczne) do 1 (w pełni kryjące) |

Zapoznaj się również z poniższą tabelą, aby poznać Przykłady efektów parametrów w przypadku zastosowania do całej sceny:

|                | 0 | 0,25 | 0,5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Nasycenie** | ![Nasycenie — 0,0](./media/shell-desaturation-00.png) | ![Nasycenie — 0,25](./media/shell-desaturation-025.png) | ![Nasycenie — 0,5](./media/shell-desaturation-05.png) | ![Nasycenie — 0,75](./media/shell-desaturation-075.png) | ![Nasycenie — 1,0](./media/shell-desaturation-10.png) |
| **Spalin**      | ![Nieprzezroczystość — 0,0](./media/shell-opacity-00.png) | ![Nieprzezroczystość — 0,25](./media/shell-opacity-025.png) | ![Nieprzezroczystość — 0,5](./media/shell-opacity-05.png) | ![Nieprzezroczystość — 0,75](./media/shell-opacity-075.png) | ![Nieprzezroczystość — 1,0](./media/shell-opacity-10.png) |

Efekt powłoki jest stosowany w końcowym kolorze nieprzezroczystym, a scena będzie renderowana w przeciwnym razie. Obejmuje to [zastąpienie stanu hierarchicznego odcienia](../../overview/features/override-hierarchical-state.md).

## <a name="example"></a>Przykład

Poniższy kod przedstawia przykład użycia `ShellRenderingSettings` stanu za pośrednictwem interfejsu API:

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Wydajność

Funkcja renderowania powłoki realizuje niewielki stały narzut w porównaniu ze standardowym renderowaniem nieprzezroczystym. Jest to znacznie szybsze niż używanie przezroczystych materiałów na obiektach [lub](../../overview/features/override-hierarchical-state.md) renderowanie. Wydajność może być bardziej silna, jeśli tylko fragmenty sceny są przełączane do renderowania powłoki. Ta degradacja może wystąpić z powodu dodatkowo ujawnionych obiektów wymagających renderowania. W takim przypadku wydajność zachowuje się podobnie do funkcji [wycinania płaszczyzny](../../overview/features/cut-planes.md) .

## <a name="next-steps"></a>Następne kroki

* [Składnik przesłonięcia stanu hierarchicznego](../../overview/features/override-hierarchical-state.md)