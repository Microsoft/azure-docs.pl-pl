---
title: Oświetlenie sceny
description: Opis i właściwości źródła światła
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206853"
---
# <a name="scene-lighting"></a>Oświetlenie sceny

Domyślnie zdalnie renderowane obiekty są zapalane przy użyciu [światła](sky.md)w sieci. W przypadku większości aplikacji jest to już wystarczające, ale możesz dodać do sceny dalsze źródła światła.

> [!IMPORTANT]
> Źródła światła mają wpływ tylko na [materiały](pbr-materials.md) . [Materiały koloru](color-materials.md) zawsze pojawiają się w pełni jasne.

> [!NOTE]
> Cienie rzutowania nie są obecnie obsługiwane. Renderowanie zdalne na platformie Azure jest zoptymalizowane pod kątem renderowania ogromnych ilości geometrii, w razie potrzeby wykorzystujących wiele procesorów GPU. Tradycyjne podejścia do rzutowania w tle nie działają dobrze w takich scenariuszach.

## <a name="common-light-component-properties"></a>Właściwości wspólnego składnika oświetlenia

Wszystkie typy świateł pochodzą z abstrakcyjnej klasy podstawowej `LightComponent` i współdzielą te właściwości:

* **Kolor:** Kolor światła w [obszarze gamma](https://en.wikipedia.org/wiki/SRGB). Alfa jest ignorowany.

* **Intensywność:** Jasność światła. W przypadku świateł punktowych i plam intensywnie definiuje również, jak daleko jest jasne.

## <a name="point-light"></a>Światło punktowe

W przypadku renderowania zdalnego na platformie Azure `PointLightComponent` nie można wyemitować światła z pojedynczego punktu, ale również z małej sfery lub małej rury, aby symulować miękkie źródła światła.

### <a name="pointlightcomponent-properties"></a>Właściwości PointLightComponent

* **Promień:** Domyślny promień to zero, w tym przypadku światło działa jako światło punktowe. Jeśli promień jest większy niż zero, działa jako źródło światła sferycznego, które zmienia wygląd odblasków.

* **Długość:** Jeśli oba `Length` i `Radius` są inne niż zero, światło działa jako światło przewodowe. Może to służyć do symulowania rur Neon.

* **AttenuationCutoff:** W przypadku pozostałej do (0, 0) tłumienie światła zależy od jego `Intensity` . Można jednak zapewnić niestandardową, minimalną/maksymalną odległość, w której natężenie światła jest skalowane liniowo do 0. Ta funkcja może służyć do wymuszania mniejszego zakresu oddziaływania określonego światła.

* **ProjectedCubemap:** Jeśli ustawiono prawidłowy [mapy sześciennej](../../concepts/textures.md), tekstura jest rzutowana na wokół geometrii otaczającej. Kolor mapy sześciennej jest modulowany kolorem światła.

## <a name="spot-light"></a>Światło punktowe

`SpotLightComponent`Jest podobna do, `PointLightComponent` ale światło jest ograniczone do kształtu stożka. Orientacja stożkowa jest definiowana przez *ujemną oś z jednostki właściciela*.

### <a name="spotlightcomponent-properties"></a>Właściwości SpotLightComponent

* **Promień:** Analogicznie jak w przypadku `PointLightComponent` .

* **SpotAngleDeg:** Ten interwał definiuje wewnętrzny i zewnętrzny Kąt stożkowy, mierzony w stopniu. Wszystko w obrębie kąta wewnętrznego jest oświetlone z pełną jasnością. Zanik jest stosowany do kąta zewnętrznego, który generuje efekt przypominający Penumbra.

* **FalloffExponent:** Definiuje, jak ostro przechodzi przejścia między wewnętrznym a zewnętrznym kątem stożkowym. Wyższa wartość powoduje ostrzejsze przejście. Wartość domyślna 1,0 powoduje przejście liniowe.

* **AttenuationCutoff:** Analogicznie jak w przypadku `PointLightComponent` .

* **Projected2dTexture:** W przypadku wybrania prawidłowej [tekstury 2D](../../concepts/textures.md)obraz jest rzutowany na geometrię, na której świecą światła. Kolor tekstury jest modulowany kolorem światła.

## <a name="directional-light"></a>Światło kierunkowe

`DirectionalLightComponent`Symuluje Źródło światła, które jest nieograniczone od razu. Światło jest ukierunkowane na kierunek *negatywnej osi z jednostki właściciela*. Pozycja jednostki jest ignorowana.

Brak dodatkowych właściwości.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Źródła światła mają znaczny wpływ na wydajność renderowania. Używaj ich uważnie i tylko wtedy, gdy jest to wymagane przez aplikację. Dowolny statyczny, globalny warunek oświetlenia, w tym statyczny składnik, można osiągnąć przy użyciu [tekstury niestandardowej przestrzeniki](sky.md), bez dodatkowych kosztów renderowania.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa LightComponentBase języka C#](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [Klasa PointLightComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [Klasa SpotLightComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [Klasa DirectionalLightComponent języka C#](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [Klasa C++ LightComponentBase](/cpp/api/remote-rendering/lightcomponentbase)
* [Klasa C++ PointLightComponent](/cpp/api/remote-rendering/pointlightcomponent)
* [Klasa C++ SpotLightComponent](/cpp/api/remote-rendering/spotlightcomponent)
* [Klasa C++ DirectionalLightComponent](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Następne kroki

* [Materiały](../../concepts/materials.md)
* [Błękitny](sky.md)