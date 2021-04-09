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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455922"
---
Wyzwalacz obiektów BLOB zawiera kilka właściwości metadanych. Te właściwości mogą służyć jako część wyrażeń powiązań w innych powiązaniach lub jako parametry w kodzie. Te wartości mają tę samą semantykę co typ [polecenia cloudblob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) .

|Właściwość  |Typ  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalającego obiektu BLOB.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu BLOB dla lokalizacji podstawowej.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Właściwości systemu obiektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Metadane zdefiniowane przez użytkownika dla obiektu BLOB.|

Na przykład poniższy skrypt w języku C# i przykłady JavaScript rejestrują ścieżkę do wyzwalanego obiektu BLOB, w tym kontenera:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```