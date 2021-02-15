---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381612"
---
### <a name="default"></a>Domyślne

Dla wyzwalanego obiektu BLOB można użyć następujących typów parametrów:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>jedno</sup>
* `CloudBlockBlob`<sup>jedno</sup>
* `CloudPageBlob`<sup>jedno</sup>
* `CloudAppendBlob`<sup>jedno</sup>

<sup>1</sup> wymaga powiązania "Inout" `direction` w *function.jsna* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` `CloudBlockBlob` typu lub. Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) w dalszej części tego artykułu.

### <a name="additional-types"></a>Dodatkowe typy

Aplikacje korzystające z [5.0.0 lub nowszej wersji rozszerzenia magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) mogą również używać typów z [zestawu Azure SDK dla platformy .NET](/dotnet/api/overview/azure/storage.blobs-readme). Ta wersja porzuca obsługę starszych `ICloudBlob` typów, `CloudBlockBlob` , `CloudPageBlob` i, `CloudAppendBlob` na korzyść następujących typów:

- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> wymaga powiązania "Inout" `direction` w *function.jsna* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Przykłady korzystania z tych typów znajdują się w [repozytorium GitHub dla rozszerzenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
