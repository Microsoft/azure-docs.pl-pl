---
title: Zdarzenie rozpoczęcia usuwania puli Azure Batch
description: Odwołanie do zdarzenia uruchamiania usuwania puli usługi Batch. To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83723939"
---
# <a name="pool-delete-start-event"></a>Zdarzenie rozpoczęcia usuwania puli

 To zdarzenie jest emitowane po rozpoczęciu operacji usuwania puli. Ponieważ usunięcie puli jest zdarzeniem asynchronicznym, można oczekiwać, że po zakończeniu operacji usuwania zostanie wyemitowane zdarzenie ukończenia usuwania puli.

 Poniższy przykład pokazuje treść zdarzenia uruchamiania puli Usuń.

```
{
    "id": "myPool1"
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
