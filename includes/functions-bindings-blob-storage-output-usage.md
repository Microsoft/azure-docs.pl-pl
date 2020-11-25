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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998887"
---
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

W funkcjach asynchronicznych należy użyć wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` `CloudBlockBlob` typu lub. Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) wcześniej w tym artykule.
