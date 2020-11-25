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
ms.openlocfilehash: 4a879c4041fe317955a07eda9dd8a3ef9f542275
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998848"
---
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