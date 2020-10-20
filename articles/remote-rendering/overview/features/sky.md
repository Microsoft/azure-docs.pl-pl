---
title: Odbicia nieba
description: Opisuje sposób konfigurowania map środowiska na potrzeby odbicia w przestrzeni powietrznej
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a193fdf82ed5e9a785c6c01db4931fb05e9aaac7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205748"
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
| Bez metalu  | ![Nieelektryczny, niesztywność = 0](media/dielectric-0.png)   | ![Nieelektryczny, niesztywność = 0,25](media/dielectric-0.25.png)  | ![Nieelektryczny, niesztywność = 0,5](media/dielectric-0.5.png)  | ![Nieelektryczny, niesztywność = 0,75](media/dielectric-0.75.png)  | ![Nieelektryczny, niesztywność = 1](media/dielectric-1.png)  |
| Metal      | ![Metal, niesztywność = 0](media/metallic-0.png)  | ![Metal, niesztywność = 0,25](media/metallic-0.25.png)    | ![Metal, niesztywność = 0,5](media/metallic-0.5.png)    | ![Metal, niesztywność = 0,75](media/metallic-0.75.png)    | ![Metal, niesztywność = 1](media/metallic-1.png)    |

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
                printf("Texture loading failed!\n");
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
|builtin://Autoshop                 | Różne światełka, jasne oświetlenie podstawowe    | ![Autoshop skybox używany do lekkiego obiektu](media/autoshop.png)
|builtin://BoilerRoom               | Jasne ustawienie oświetlenia, wiele świateł okna      | ![BoilerRoom skybox używany do lekkiego obiektu](media/boiler-room.png)
|builtin://ColorfulStudio           | Zróżnicuj kolorowe sygnalizatory w ustawieniu średniego oświetlenia lekkiego  | ![ColorfulStudio skybox używany do lekkiego obiektu](media/colorful-studio.png)
|builtin://Hangar                   | Średnio jasne światełko korytarza                     | ![SmallHangar skybox używany do lekkiego obiektu](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Przyciemnij ustawienia wewnętrzne z kontrastem jasnym i ciemnym              | ![IndustrialPipeAndValve skybox używany do lekkiego obiektu](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Światło w otoczeniu Daytime, jasne oświetlenie obszaru okna     | ![Lebombo skybox używany do lekkiego obiektu](media/lebombo.png)
|builtin://SataraNight              | Ciemna noc i Ziemia z wieloma otaczającymi światłami   | ![SataraNight skybox używany do lekkiego obiektu](media/satara-night.png)
|builtin://SunnyVondelpark          | Jasne światło słoneczne i kontrast cień                      | ![SunnyVondelpark skybox używany do lekkiego obiektu](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Jasne oświetlenie o umiarkowanej powierzchni            | ![Syferfontein skybox używany do lekkiego obiektu](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Średnio różne słońce i cieniowanie                         | ![TearsOfSteelBridge skybox używany do lekkiego obiektu](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Jasne podejście Dusk                    | ![VeniceSunset skybox używany do lekkiego obiektu](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Jasne, Lush-zielone i białe światła, wyszarzone | ![WhippleCreekRegionalPark skybox używany do lekkiego obiektu](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dzienne oświetlenie otoczenia                 | ![WinterRiver skybox używany do lekkiego obiektu](media/winter-river.png)
|builtin://DefaultSky               | Analogicznie jak TearsOfSteelBridge                               | ![DefaultSky skybox używany do lekkiego obiektu](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Właściwość RemoteManager. SkyReflectionSettings języka C#](/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [Zdalnymanager:: SkyReflectionSettings ()](/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Następne kroki

* [Lampki](../../overview/features/lights.md)
* [Materiały](../../concepts/materials.md)
* [Tekstury](../../concepts/textures.md)
* [Narzędzie wiersza polecenia TexConv](../../resources/tools/tex-conv.md)