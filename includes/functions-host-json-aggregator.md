---
title: dołączanie pliku
description: dołączanie pliku
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279273"
---
Określa liczbę wywołań funkcji agregowanych podczas [obliczania metryk dla Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Właściwość |Domyślny  | Opis |
|---------|---------|---------| 
|batchSize|1000|Maksymalna liczba żądań agregowania.| 
|flushTimeout|00:00:30|Maksymalny okres do agregowania.| 

Wywołania funkcji są agregowane po osiągnięciu pierwszego z dwóch limitów.
