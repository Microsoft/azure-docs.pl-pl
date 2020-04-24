---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch
description: Odwołanie do zdarzenia uruchamiania usuwania puli usługi Batch. To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 38e419e549006d3fde2f1694e0d40e620cd438e5
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115928"
---
# <a name="pool-delete-start-event"></a>Zdarzenie rozpoczęcia usuwania puli

 To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli. Ponieważ usunięcie puli jest zdarzeniem asynchronicznym, można oczekiwać, że po zakończeniu operacji usuwania zostanie wyemitowane zdarzenie ukończenia usuwania puli.

 Poniższy przykład pokazuje treść zdarzenia uruchamiania puli Usuń.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
