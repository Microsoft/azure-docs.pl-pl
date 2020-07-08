---
title: dołączanie pliku
description: dołączanie pliku
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
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
