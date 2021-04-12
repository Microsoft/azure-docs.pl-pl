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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100381662"
---
### <a name="default"></a>Domyślne

Aby napisać obiekty blob, można powiązać następujące typy:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>jedno</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>dwóch</sup>
* `CloudBlockBlob`<sup>dwóch</sup>
* `CloudPageBlob`<sup>dwóch</sup>
* `CloudAppendBlob`<sup>dwóch</sup>

<sup>1</sup> wymaga powiązania "in" `direction` w *function.jsna* lub `FileAccess.Read` w bibliotece klas języka C#. Można jednak użyć obiektu kontenera, który środowisko uruchomieniowe zapewnia do wykonywania operacji zapisu, takich jak przekazywanie obiektów BLOB do kontenera.

<sup>2</sup> wymaga powiązania "Inout" `direction` w *function.jsna* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` `CloudBlockBlob` typu lub. Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) wcześniej w tym artykule.

### <a name="additional-types"></a>Dodatkowe typy

Aplikacje korzystające z [5.0.0 lub nowszej wersji rozszerzenia magazynu](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) mogą również używać typów z [zestawu Azure SDK dla platformy .NET](/dotnet/api/overview/azure/storage.blobs-readme). Ta wersja porzuca obsługę starszych `CloudBlobContainer` typów, `CloudBlobDirectory` ,,, `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` i `CloudAppendBlob` na korzyść następujących typów:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> wymaga powiązania "in" `direction` w *function.jsna* lub `FileAccess.Read` w bibliotece klas języka C#. Można jednak użyć obiektu kontenera, który środowisko uruchomieniowe zapewnia do wykonywania operacji zapisu, takich jak przekazywanie obiektów BLOB do kontenera.

<sup>2</sup> wymaga powiązania "Inout" `direction` w *function.jsna* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Przykłady korzystania z tych typów znajdują się w [repozytorium GitHub dla rozszerzenia](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
