---
title: Modele
description: W tym artykule opisano, jaki jest model w renderowaniu zdalnym platformy Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 5d737b1e85a28661a7491b8d2822e6472538c7a1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617957"
---
# <a name="models"></a>Modele

*Model* w renderowaniu zdalnym platformy Azure odnosi się do pełnej reprezentacji obiektów, składającej się z [jednostek](entities.md) i [składników.](components.md) Modele są głównym sposobem, aby uzyskać niestandardowe dane do usługi zdalnego renderowania.

## <a name="model-structure"></a>Struktura modelu

Model ma dokładnie jedną [jednostkę](entities.md) jako węzeł główny. Poniżej, że może mieć dowolną hierarchię jednostek podrzędnych. Podczas ładowania modelu zwracane jest odwołanie do tej jednostki głównej.

Każda jednostka może mieć [dołączone składniki.](components.md) W najczęstszym przypadku jednostki mają *MeshComponents*, które odwołują się do [zasobów siatki](meshes.md).

## <a name="creating-models"></a>Tworzenie modeli

Tworzenie modeli dla środowiska wykonawczego odbywa się poprzez [konwersję modeli wejściowych](../how-tos/conversion/model-conversion.md) z formatów plików, takich jak FBX i GLTF. Proces konwersji wyodrębnia wszystkie zasoby, takie jak tekstury, materiały i siatki, i konwertuje je na zoptymalizowane formaty środowiska uruchomieniowego. Wyodrębni również informacje strukturalne i przekształci je w strukturę wykresu jednostki/komponentu ARR.

> [!IMPORTANT]
>
> [Konwersja modelu](../how-tos/conversion/model-conversion.md) jest jedynym sposobem tworzenia [siatek](meshes.md). Mimo że siatki mogą być współużytkowane między jednostkami w czasie wykonywania, nie ma innego sposobu, aby uzyskać siatki w czasie wykonywania, inne niż ładowanie modelu.

## <a name="loading-models"></a>Modele ładowania

Po przekonwertowaniu modelu można go załadować z magazynu obiektów blob platformy Azure do środowiska wykonawczego.

Istnieją dwie różne funkcje ładowania, które różnią się w zależności od sposobu, w jaki zasób jest adresowany w magazynie obiektów blob:

* Model może być rozwiązany przez jego identyfikator URI SAS. Odpowiednia funkcja `LoadModelFromSASAsync` ładowania `LoadModelFromSASParams`jest z parametrem . Użyj tego wariantu również podczas ładowania [wbudowanych modeli](../samples/sample-model.md).
* Model można rozwiązać za pomocą parametrów magazynu obiektów blob bezpośrednio, w przypadku, gdy [magazyn obiektów blob jest połączony z kontem](../how-tos/create-an-account.md#link-storage-accounts). Odpowiednia funkcja ładowania `LoadModelAsync` w `LoadModelParams`tym przypadku jest z parametrem .

Poniższe fragmenty kodu pokazują, jak załadować modele za pomocą jednej z funkcji. Aby załadować model przy użyciu identyfikatora URI sygnatury dostępu Współdzielonego, należy użyć kodu podobnego do poniższego:

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

Jeśli chcesz załadować model bezpośrednio przy użyciu jego parametrów magazynu obiektów blob, należy użyć kodu podobnego do następującego fragmentu kodu:

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

Następnie można przechodzić przez hierarchię jednostek i modyfikować elementy i komponenty. Ładowanie tego samego modelu wiele razy tworzy wiele wystąpień, z których każdy z własną kopię struktury jednostki/składnika. Ponieważ siatki, materiały i tekstury są [zasobami udostępnionymi,](../concepts/lifetime.md)ich dane nie zostaną ponownie załadowane. W związku z tym tworzenie wystąpienia modelu więcej niż jeden raz wiąże się stosunkowo mało narzutów pamięci.

> [!CAUTION]
> Wszystkie *funkcje asynchroniczne* w ARR zwracają obiekty operacji asynchronialnych. Należy przechowywać odwołanie do tych obiektów, dopóki operacja nie zostanie zakończona. W przeciwnym razie moduł odśmiecania elementów bezużytecznych języka C# może usunąć operację wcześniej i nigdy nie może zakończyć. W powyższym kodzie przykładowym użycie *await* gwarantuje, że zmienna lokalna "loadOp" zawiera odwołanie do momentu zakończenia ładowania modelu. Jednak jeśli zamiast tego można użyć *completed* zdarzenia, należy przechowywać operację asynchroniza w zmiennej członkowskiej.

## <a name="next-steps"></a>Następne kroki

* [Jednostki](entities.md)
* [Siatki](meshes.md)
