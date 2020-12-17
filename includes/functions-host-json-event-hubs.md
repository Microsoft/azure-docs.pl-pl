---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f5101233f7995fb58fc530e613ba3235a55c783c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628732"
---
### <a name="functions-2x-and-higher"></a>Funkcje w wersji 2.x i nowszych

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTime": ""
            }
        }
    }
}  
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.|
|eventProcessorOptions/maxBatchSize|10|Maksymalna liczba zdarzeń odebranych na pętlę odbierania.|
|eventProcessorOptions/prefetchCount|300|Domyślna liczba pobrań poprzedzających, używana przez bazowe `EventProcessorHost` . Minimalna dozwolona wartość to 10.|
|initialOffsetOptions/typ|fromStart|Lokalizacja w strumieniu zdarzeń, z której ma zostać rozpoczęte przetwarzanie, gdy punkt kontrolny nie istnieje w magazynie. Dostępne opcje `fromStart` to `fromEnd` lub `fromEnqueuedTime` . `fromEnd` przetwarza nowe zdarzenia, które zostały dodane do kolejki po uruchomieniu aplikacji funkcji. Dotyczy wszystkich partycji.  Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
|initialOffsetOptions/enqueuedTime|Nie dotyczy| Określa czas (w kolejce) zdarzenia w strumieniu, z którego ma zostać rozpoczęte przetwarzanie. Gdy `initialOffsetOptions/type` jest skonfigurowany jako `fromEnqueuedTime` , to ustawienie jest obowiązkowe. Obsługuje czas w dowolnym formacie obsługiwanym przez [element DateTime. Parse ()](/dotnet/standard/base-types/parsing-datetime), np  `2020-10-26T20:31Z` .. Dla jasności należy również określić strefę czasową. Gdy strefa czasowa nie jest określona, funkcja przyjmuje lokalną strefę czasową maszyny z uruchomioną aplikacją funkcji, która jest UTC w przypadku uruchamiania na platformie Azure. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
> [!NOTE]
> Aby uzyskać informacje na temat host.jsw Azure Functions 2. x i więcej, zobacz [host.json Reference for Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------| 
|maxBatchSize|64|Maksymalna liczba zdarzeń odebranych na pętlę odbierania.|
|prefetchCount|n/d|Domyślne pobranie wstępne, które będzie używane przez bazowe `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.| 

> [!NOTE]
> Aby uzyskać informacje na temat host.jsna Azure Functions 1. x, zobacz [host.json Reference for Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).
