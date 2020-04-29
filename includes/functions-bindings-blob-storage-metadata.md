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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "69642138"
---
Wyzwalacz obiektów BLOB zawiera kilka właściwości metadanych. Te właściwości mogą służyć jako część wyrażeń powiązań w innych powiązaniach lub jako parametry w kodzie. Te wartości mają tę samą semantykę co typ [polecenia cloudblob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Właściwość  |Typ  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalającego obiektu BLOB.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu BLOB dla lokalizacji podstawowej.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Właściwości systemu obiektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Metadane zdefiniowane przez użytkownika dla obiektu BLOB.|

Na przykład poniższy skrypt w języku C# i przykłady JavaScript rejestrują ścieżkę do wyzwalanego obiektu BLOB, w tym kontenera:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
