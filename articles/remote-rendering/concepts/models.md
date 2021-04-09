---
title: Modele
description: Opisuje, co model znajduje się w usłudze Azure Remote rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593878"
---
# <a name="models"></a>Modele

*Model* w ramach renderowania zdalnego na platformie Azure odnosi się do pełnej reprezentacji obiektu, składającej się z [jednostek](entities.md) i [składników](components.md). Modele są głównym sposobem uzyskiwania niestandardowych danych do zdalnej usługi renderowania.

## <a name="model-structure"></a>Struktura modelu

Model ma dokładnie jedną [jednostkę](entities.md) jako węzeł główny. Poniżej może istnieć arbitralna hierarchia jednostek podrzędnych. Podczas ładowania modelu zwracane jest odwołanie do tej jednostki głównej.

Każda jednostka może mieć dołączone [składniki](components.md) . W najbardziej typowym przypadku jednostki mają *MeshComponents*, które odwołują się do [zasobów siatki](meshes.md).

## <a name="creating-models"></a>Tworzenie modeli

Tworzenie modeli dla środowiska uruchomieniowego jest realizowane przez [konwertowanie modeli wejściowych](../how-tos/conversion/model-conversion.md) z formatów plików, takich jak FBX i GLTF. Proces konwersji wyodrębnia wszystkie zasoby, takie jak tekstury, materiały i siatki, i konwertuje je na zoptymalizowane formaty środowiska uruchomieniowego. Wyodrębni również informacje strukturalne i przekonwertuje je na strukturę grafu jednostki/składnika modelu ARR.

> [!IMPORTANT]
> [Konwersja modelu](../how-tos/conversion/model-conversion.md) jest jedynym sposobem tworzenia [siatek](meshes.md). Chociaż siatki mogą być współdzielone między jednostkami w czasie wykonywania, nie istnieje żaden inny sposób na uzyskanie siatki w środowisku uruchomieniowym, innym niż ładowanie modelu.

## <a name="loading-models"></a>Ładowanie modeli

Po przekonwertowaniu modelu można go załadować z usługi Azure Blob Storage do środowiska uruchomieniowego.

Istnieją dwie odrębne funkcje ładowania, które różnią się w zależności od sposobu, w jaki zasób jest rozliczane w usłudze BLOB Storage:

* Model może być rozwiązany bezpośrednio przez parametry magazynu obiektów blob, w przypadku gdy [Magazyn obiektów BLOB jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania w tym przypadku jest `LoadModelAsync` parametrem `LoadModelOptions` .
* Model może być adresem URI sygnatury dostępu współdzielonego. Odpowiednia funkcja ładowania jest `LoadModelFromSasAsync` z parametrem `LoadModelFromSasOptions` . Użyj tego wariantu również podczas ładowania [wbudowanych modeli](../samples/sample-model.md).

Poniższe fragmenty kodu przedstawiają sposób ładowania modeli przy użyciu dowolnej funkcji. Aby załadować model przy użyciu jego parametrów magazynu obiektów blob, należy użyć kodu takiego jak poniższy:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Jeśli chcesz załadować model przy użyciu tokenu sygnatury dostępu współdzielonego, użyj kodu podobnego do następującego fragmentu:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Następnie można przejść do hierarchii jednostek i zmodyfikować jednostki i składniki. Załadowanie tego samego modelu wielokrotne powoduje utworzenie wielu wystąpień, z których każda ma własną kopię struktury jednostki/składnika. Ponieważ siatki, materiały i tekstury są [zasobami udostępnionymi](../concepts/lifetime.md), ich dane nie zostaną ponownie załadowane. W związku z tym utworzenie wystąpienia modelu więcej niż raz powoduje stosunkowo małe obciążenie pamięci.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Siatki](meshes.md)