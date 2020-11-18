---
title: Tekstury
description: Przepływ pracy zasobów tekstury
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: b951dab1ad01187c7612fad047bc52eb6aa9700e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701878"
---
# <a name="textures"></a>Tekstury

Tekstury są niezmiennym [zasobem udostępnionym](../concepts/lifetime.md). Tekstury można ładować z [magazynu obiektów BLOB](../how-tos/conversion/blob-storage.md) i bezpośrednio stosować do modeli, jak pokazano w [samouczku: zmiana środowiska i materiałów](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). Najczęściej, chociaż tekstury będą częścią [konwertowanego modelu](../how-tos/conversion/model-conversion.md), w którym odwołują się do niego [materiały](materials.md).

## <a name="texture-types"></a>Typy tekstury

Różne typy tekstury mają różne przypadki użycia:

* **tekstury 2D** są głównie używane w [materiałach](materials.md).
* **CubeMaps** można użyć dla [przestrzeni powietrznej](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Obsługiwane formaty tekstury

Wszystkie tekstury przekazane do ARR muszą mieć [Format DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Najlepiej z kompresją mipmapy i teksturą.

## <a name="loading-textures"></a>Ładowanie tekstur

Podczas ładowania tekstury należy określić jej oczekiwany typ. Jeśli typ jest niezgodny, ładowanie tekstury kończy się niepowodzeniem.
Załadowanie tekstury z tym samym identyfikatorem URI dwa razy spowoduje zwrócenie tego samego obiektu tekstury, co jest [zasobem udostępnionym](../concepts/lifetime.md).

Podobnie jak w przypadku ładowania modeli, istnieją dwa warianty adresowania zasobów tekstury w źródłowym magazynie obiektów blob:

* Element zawartości tekstury może być adresem URI sygnatury dostępu współdzielonego. Odpowiednia funkcja ładowania jest `LoadTextureFromSASAsync` z parametrem `LoadTextureFromSASParams` . Użyj tego wariantu również podczas ładowania [wbudowanych tekstur](../overview/features/sky.md#built-in-environment-maps).
* Teksturę można rozwiązać bezpośrednio przez parametry magazynu obiektów blob, w przypadku gdy [Magazyn obiektów BLOB jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania w tym przypadku jest `LoadTextureAsync` parametrem `LoadTextureParams` .

Poniższy przykładowy kod przedstawia sposób ładowania tekstury za pomocą identyfikatora URI sygnatury dostępu współdzielonego (lub tekstury wbudowanej) — należy zauważyć, że tylko funkcja ładowania/parametr różni się w przypadku innych przypadków:

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

W zależności od tego, co ma być używane dla tekstury, mogą istnieć ograniczenia dotyczące typu tekstury i zawartości. Na przykład mapa niesztywności [materiału PBR](../overview/features/pbr-materials.md) musi być w skali szarości.

> [!CAUTION]
> Wszystkie funkcje *asynchroniczne* w asynchronicznych obiektach operacji Return. Odwołanie do tych obiektów należy przechowywać do momentu ukończenia operacji. W przeciwnym razie Moduł wyrzucania elementów bezużytecznych języka C# może usunąć operację wczesną i nigdy nie zakończy się. W przykładowym kodzie powyżej zmiennej składowej "_textureLoad" jest używany do przechowywania odwołania do momentu *zakończenia ukończenia* zdarzenia.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa tekstury C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [Zdalnymanager. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [Zdalnymanager. LoadTextureFromSASAsync ()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [Klasa tekstury C++](/cpp/api/remote-rendering/texture)
* [Zdalnymanager:: LoadTextureAsync ()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [Zdalnymanager:: LoadTextureFromSASAsync ()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
* [Błękitny](../overview/features/sky.md)