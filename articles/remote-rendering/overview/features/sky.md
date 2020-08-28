---
title: Odbicia nieba
description: Opisuje sposób konfigurowania map środowiska na potrzeby odbicia w przestrzeni powietrznej
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f2a871e409761116182f67eb877f3727038fe0dc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013643"
---
# <a name="sky-reflections"></a>Odbicia nieba

W przypadku renderowania zdalnego na platformie Azure tekstura w przestrzenice do sieci jest używana do realistycznych obiektów. W przypadku aplikacji w rzeczywistości rozszerzonej ta tekstura powinna przypominać otoczenie w świecie rzeczywistym, aby obiekty były wyświetlane jako przekonujące. W tym artykule opisano, jak zmienić teksturę z przestrzenią.

> [!NOTE]
> Tekstura dla przestrzeni powietrznej jest również określana jako *Mapa środowiska*. Te warunki są używane zamiennie.

## <a name="object-lighting"></a>Oświetlenie obiektów

Zdalne renderowanie na platformie Azure wykorzystuje *renderowanie oparte na fizycznie* (PBR) dla realistycznych obliczeń oświetlenia. Mimo że możesz dodać [źródła światła](lights.md) do swojej sceny, przy użyciu odpowiedniej tekstury w dobrej.

Poniższe obrazy pokazują wyniki oświetlenia różnych powierzchni tylko przy użyciu tekstury w postaci wartościowej:

| Niesztywność  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Bez metalu  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Aby uzyskać więcej informacji na temat modelu oświetlenia, zapoznaj się z rozdziałem [materiały](../../concepts/materials.md) .

> [!IMPORTANT]
> Funkcja renderowania zdalnego platformy Azure używa tekstury z przestrzenią kolorów tylko dla modeli oświetlenia. Nie renderuje ona przestrzeni powietrznej jako tła, ponieważ aplikacje w rzeczywistości rozszerzonej mają już prawidłowe tło — świecie rzeczywistym.

## <a name="changing-the-sky-texture"></a>Zmiana tekstury z przestrzenią

Aby zmienić mapę środowiska, wystarczy [załadować teksturę](../../concepts/textures.md) i zmienić jej `SkyReflectionSettings` :

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!");
            }
        });
}

```

Należy zauważyć, że `LoadTextureFromSASAsync` wariant jest używany powyżej, ponieważ załadowana została wbudowana tekstura. W przypadku ładowania z [połączonych magazynów obiektów BLOB](../../how-tos/create-an-account.md#link-storage-accounts)Użyj `LoadTextureAsync` wariantu.

## <a name="sky-texture-types"></a>Typy tekstury przestrzeni powietrznej

Można użyć tekstury *[CubeMaps](https://en.wikipedia.org/wiki/Cube_mapping)* i *2D* jako mapy środowiska.

Wszystkie tekstury muszą być w [obsługiwanym formacie tekstury](../../concepts/textures.md#supported-texture-formats). Nie musisz podawać tekstury mipmapy for.

### <a name="cube-environment-maps"></a>Mapy środowiska modułu

W odniesieniu do tego jest nieopakowany mapy sześciennej:

![Nieopakowany mapy sześciennej](media/Cubemap-example.png)

Użyj `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` with `TextureType.CubeMap` , aby ładować tekstury mapy sześciennej.

### <a name="sphere-environment-maps"></a>Mapy środowiska sfery

W przypadku używania tekstury 2D jako mapy środowiska obraz musi znajdować się w [sferycznej przestrzeni współrzędnych](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Obraz w przestrzeni powietrznej we współrzędnych sferycznych](media/spheremap-example.png)

Za pomocą programu `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` można ładować mapy środowiska sferycznego.

## <a name="built-in-environment-maps"></a>Wbudowane mapy środowiska

Zdalne renderowanie na platformie Azure udostępnia kilka wbudowanych map środowiska, które są zawsze dostępne. Wszystkie wbudowane mapy środowiska są cubemapse.

|Identyfikator                         | Opis                                              | Ilustracji                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Różne światełka, jasne oświetlenie podstawowe    | ![Autozakupy](media/autoshop.png)
|builtin://BoilerRoom               | Jasne ustawienie oświetlenia, wiele świateł okna      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Zróżnicuj kolorowe sygnalizatory w ustawieniu średniego oświetlenia lekkiego  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Średnio jasne światełko korytarza                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Przyciemnij ustawienia wewnętrzne z kontrastem jasnym i ciemnym              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Światło w otoczeniu Daytime, jasne oświetlenie obszaru okna     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Ciemna noc i Ziemia z wieloma otaczającymi światłami   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Jasne światło słoneczne i kontrast cień                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasne oświetlenie o umiarkowanej powierzchni            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Średnio różne słońce i cieniowanie                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Jasne podejście Dusk                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Jasne, Lush-zielone i białe światła, wyszarzone | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dzienne oświetlenie otoczenia                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Analogicznie jak TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Kolejne kroki

* [Światła](../../overview/features/lights.md)
* [Materiały](../../concepts/materials.md)
* [Tekstury](../../concepts/textures.md)
* [Narzędzie wiersza polecenia TexConv](../../resources/tools/tex-conv.md)
