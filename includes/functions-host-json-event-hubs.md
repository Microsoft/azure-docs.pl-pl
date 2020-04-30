---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791669"
---
### <a name="functions-2x-and-higher"></a>Funkcje 2. x i nowsze

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|maxBatchSize|10|Maksymalna liczba zdarzeń odebranych na pętlę odbierania.|
|prefetchCount|300|Domyślna liczba pobrań poprzedzających, używana przez bazowe `EventProcessorHost`.|
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.|

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w Azure Functions 2. x i więcej, zobacz [Dokumentacja pliku host. JSON dla Azure Functions](../articles/azure-functions/functions-host-json.md).

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
|prefetchCount|n/d|Domyślne pobranie wstępne, które będzie używane przez bazowe `EventProcessorHost`.| 
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.| 

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w Azure Functions 1. x, zobacz [odwołanie do pliku host. JSON dla Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

