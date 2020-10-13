---
title: Modele
description: Opisuje, co model znajduje się w usłudze Azure Remote rendering
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e9c8c4a1209b8bb5be7af87ef22aeab0ffd90b79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90023775"
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

* Model może być adresem URI sygnatury dostępu współdzielonego. Odpowiednia funkcja ładowania jest `LoadModelFromSASAsync` z parametrem `LoadModelFromSASParams` . Użyj tego wariantu również podczas ładowania [wbudowanych modeli](../samples/sample-model.md).
* Model może być rozwiązany bezpośrednio przez parametry magazynu obiektów blob, w przypadku gdy [Magazyn obiektów BLOB jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania w tym przypadku jest `LoadModelAsync` parametrem `LoadModelParams` .

Poniższe fragmenty kodu przedstawiają sposób ładowania modeli przy użyciu dowolnej funkcji. Aby załadować model przy użyciu identyfikatora URI sygnatury dostępu współdzielonego, należy użyć kodu takiego jak poniższy:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

Jeśli chcesz załadować model bezpośrednio przy użyciu jego parametrów magazynu obiektów blob, użyj kodu podobnego do poniższego kodu:

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

Następnie można przejść do hierarchii jednostek i zmodyfikować jednostki i składniki. Załadowanie tego samego modelu wielokrotne powoduje utworzenie wielu wystąpień, z których każda ma własną kopię struktury jednostki/składnika. Ponieważ siatki, materiały i tekstury są [zasobami udostępnionymi](../concepts/lifetime.md), ich dane nie zostaną ponownie załadowane. W związku z tym utworzenie wystąpienia modelu więcej niż raz powoduje stosunkowo małe obciążenie pamięci.

> [!CAUTION]
> Wszystkie funkcje *asynchroniczne* w asynchronicznych obiektach operacji Return. Odwołanie do tych obiektów należy przechowywać do momentu ukończenia operacji. W przeciwnym razie Moduł wyrzucania elementów bezużytecznych języka C# może usunąć operację wczesną i nigdy nie zakończy się. W przykładowym kodzie powyżej użycia programu *await* gwarantuje, że zmienna lokalna "loadOp" przechowuje odwołanie do momentu zakończenia ładowania modelu. Jeśli jednak zamiast tego użyć zdarzenia *ukończone* , należy zapisać operację asynchroniczną w zmiennej składowej.

## <a name="api-documentation"></a>Dokumentacja interfejsu API

* [Zdalnymanager. LoadModelAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelasync)
* [Zdalnymanager. LoadModelFromSASAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadmodelfromsasasync)
* [Zdalnymanager:: LoadModelAsync ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadmodelasync)
* [Zdalnymanager:: LoadModelFromSASAsync ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#loadmodelfromsasasync)

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Siatki](meshes.md)
