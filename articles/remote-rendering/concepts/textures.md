---
title: Tekstury
description: Przepływ pracy zasobów tekstury
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594421"
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

* Teksturę można rozwiązać bezpośrednio przez parametry magazynu obiektów blob, w przypadku gdy [Magazyn obiektów BLOB jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania w tym przypadku jest `LoadTextureAsync` parametrem `LoadTextureOptions` .
* Element zawartości tekstury może być adresem URI sygnatury dostępu współdzielonego. Odpowiednia funkcja ładowania jest `LoadTextureFromSasAsync` z parametrem `LoadTextureFromSasOptions` . Użyj tego wariantu również podczas ładowania [wbudowanych tekstur](../overview/features/sky.md#built-in-environment-maps).

Następujący przykładowy kod przedstawia sposób ładowania tekstury:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Należy zauważyć, że w przypadku używania jego wariantu SAS tylko funkcja ładowania/parametr różni się.

W zależności od tego, co ma być używane dla tekstury, mogą istnieć ograniczenia dotyczące typu tekstury i zawartości. Na przykład mapa niesztywności [materiału PBR](../overview/features/pbr-materials.md) musi być w skali szarości.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Klasa tekstury C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Klasa tekstury C++](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
* [Błękitny](../overview/features/sky.md)