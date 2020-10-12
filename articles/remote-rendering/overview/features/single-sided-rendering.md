---
title: Renderowanie jednostronne
description: Opisuje ustawienia renderowania jednostronnego i przypadki użycia
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f9feb75cf46eee4329c9392771fe2e329a1d6d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613618"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: dawania

Większość renderowania korzysta z [odtwarzania z tyłu](https://en.wikipedia.org/wiki/Back-face_culling) , aby zwiększyć wydajność. Jeśli jednak siatki są obcinane jako otwarte z [płaszczyznami wycinania](cut-planes.md), użytkownicy często będą oglądać z tyłu trójkątów. Jeśli te trójkąty są odrzucane, wynik nie wygląda na przekonujący.

Sposobem nieniezawodnego zapobiegania temu problemowi jest renderowanie trójkątów *dwukrotnie*. Jako że nie korzystasz z funkcji usuwania z tyłu, ma to wpływ na wydajność, ponieważ domyślne renderowanie zdalne platformy Azure przełącza się tylko na dwustronne Renderowanie siatek, które są przecinane z płaszczyzną wycinania.

Ustawienie * :::no-loc text="single-sided"::: renderowania* pozwala dostosować to zachowanie.

> [!CAUTION]
> :::no-loc text="single-sided":::Ustawienie renderowania jest funkcją eksperymentalną. Może zostać ponownie usunięta w przyszłości. Nie zmieniaj ustawienia domyślnego, chyba że naprawdę rozwiąże problem krytyczny w aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

:::no-loc text="single-sided":::Ustawienie renderowania ma wpływ tylko na siatki, które zostały [przekonwertowane](../../how-tos/conversion/configure-model-conversion.md) przy użyciu `opaqueMaterialDefaultSidedness` opcji ustawionej na `SingleSided` . Domyślnie ta opcja jest ustawiona na `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: ustawienie renderowania

Istnieją trzy różne tryby:

**Normalne:** W tym trybie siatki są zawsze renderowane podczas konwersji. Oznacza to, że siatki konwertowane z `opaqueMaterialDefaultSidedness` zestawem na `SingleSided` zawsze będą renderowane z włączonym usuwaniem z tyłu, nawet gdy przecinają płaszczyznę wycinania.

**DynamicDoubleSiding:** W tym trybie, gdy płaszczyzna wycinania przecina siatkę, zostanie ona automatycznie przełączona na Render dwustronny. Ten tryb jest trybem domyślnym.

**AlwaysDoubleSided:** Wymusza, aby cała geometria jednostronna była renderowana dwustronnie przez cały czas. Ten tryb jest głównie narażony, dzięki czemu można łatwo porównać wpływ na wydajność między :::no-loc text="single-sided"::: i :::no-loc text="double-sided"::: renderingu.

Zmiany :::no-loc text="single-sided"::: ustawień renderowania można wykonać w następujący sposób:

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
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Właściwość RemoteManager. SingleSidedSettings języka C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.singlesidedsettings)
* [Zdalnymanager:: SingleSidedSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#singlesidedsettings)

## <a name="next-steps"></a>Następne kroki

* [Wycięte płaszczyzny](cut-planes.md)
* [Konfigurowanie konwersji modelu](../../how-tos/conversion/configure-model-conversion.md)
