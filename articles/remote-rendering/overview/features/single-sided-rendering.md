---
title: Renderowanie jednostronne
description: Opisuje ustawienia renderowania jednostronnego i przypadki użycia
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759102"
---
# <a name="single-sided-rendering"></a>Renderowanie jednostronne

Większość renderowania korzysta z [odtwarzania z tyłu](https://en.wikipedia.org/wiki/Back-face_culling) , aby zwiększyć wydajność. Jeśli jednak siatki są obcinane jako otwarte z [płaszczyznami wycinania](cut-planes.md), użytkownicy często będą oglądać z tyłu trójkątów. Jeśli te trójkąty są odrzucane, wynik nie wygląda na przekonujący.

Sposobem nieniezawodnego zapobiegania temu problemowi jest renderowanie trójkątów *dwukrotnie*. Jako że nie korzystasz z funkcji usuwania z tyłu, ma to wpływ na wydajność, ponieważ domyślne renderowanie zdalne platformy Azure przełącza się tylko na dwustronne Renderowanie siatek, które są przecinane z płaszczyzną wycinania.

Ustawienie *renderowania jednostronnego* pozwala dostosować to zachowanie.

> [!CAUTION]
> Ustawienie renderowania jednostronnego jest funkcją eksperymentalną. Może zostać ponownie usunięta w przyszłości. Nie zmieniaj ustawienia domyślnego, chyba że naprawdę rozwiąże problem krytyczny w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ustawienie renderowania jednostronnego ma wpływ tylko na siatki, które zostały [przekonwertowane](../../how-tos/conversion/configure-model-conversion.md) przy użyciu `opaqueMaterialDefaultSidedness` opcji ustawionej na `SingleSided` . Domyślnie ta opcja jest ustawiona na `DoubleSided` .

## <a name="single-sided-rendering-setting"></a>Ustawienie renderowania jednostronnego

Istnieją trzy różne tryby:

**Normalne:** W tym trybie siatki są zawsze renderowane podczas konwersji. Oznacza to, że siatki konwertowane z `opaqueMaterialDefaultSidedness` zestawem na `SingleSided` zawsze będą renderowane z włączonym usuwaniem z tyłu, nawet gdy przecinają płaszczyznę wycinania.

**DynamicDoubleSiding:** W tym trybie, gdy płaszczyzna wycinania przecina siatkę, zostanie ona automatycznie przełączona na Render dwustronny. Ten tryb jest trybem domyślnym.

**AlwaysDoubleSided:** Wymusza, aby cała geometria jednostronna była renderowana dwustronnie przez cały czas. Ten tryb jest głównie narażony, dzięki czemu można łatwo porównać wpływ na wydajność między renderowaniem jednostronnym i dwustronnym.

Zmiana ustawień renderowania jednostronnego można wykonać w następujący sposób:

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Następne kroki

* [Wycięte płaszczyzny](cut-planes.md)
* [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md)
