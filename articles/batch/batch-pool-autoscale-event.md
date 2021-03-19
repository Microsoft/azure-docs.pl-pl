---
title: Zdarzenie automatycznego skalowania puli Azure Batch
description: Odwołanie do zdarzenia automatycznego skalowania puli usługi Batch, które jest emitowane po przeprowadzeniu automatycznej skalowania puli. Zawartość dziennika uwidocznia wyniki formuł i oceny automatycznego skalowania dla puli.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91852180"
---
# <a name="pool-autoscale-event"></a>Zdarzenie automatycznego skalowania puli

 To zdarzenie jest emitowane po wykonaniu automatycznego skalowania puli. Zawartość dziennika uwidocznia wyniki formuł i oceny automatycznego skalowania dla puli.

 Poniższy przykład przedstawia treść zdarzenia skalowania automatycznego puli dla puli automatyczne skalowanie, które nie powiodło się z powodu niewystarczających przykładowych danych.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`timestamp`|DateTime|Sygnatura czasowa, gdy jest wykonywane automatyczne skalowanie.|
|`formula`|Ciąg|Formuła zdefiniowana do automatycznego skalowania.|
|`results`|Ciąg|Wyniki oceny dla wszystkich zmiennych użytych w formule.|
|[`error`](#error)|Typ złożony|Szczegółowy błąd skalowania automatycznego.|

###  <a name="error"></a><a name="error"></a> Porn

|Nazwa elementu|Typ|Uwagi|
|------------------|----------|-----------|
|`code`|Ciąg|Identyfikator błędu skalowania automatycznego. Kody są niezmienne i mają być używane programowo.|
|`message`|Ciąg|Komunikat opisujący automatyczny błąd skalowania, przeznaczony do wyświetlania w interfejsie użytkownika.|
|`values`|Tablica|Lista par nazwa-wartość opisująca więcej szczegółów dotyczących błędu skalowania automatycznego.|
