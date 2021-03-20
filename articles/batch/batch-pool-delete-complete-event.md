---
title: Zdarzenie ukończenia usuwania puli Azure Batch
description: Odwołanie do zdarzenia ukończenia usuwania puli usługi Batch. To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803735"
---
# <a name="pool-delete-complete-event"></a>Zdarzenie zakończenia usuwania puli

 To zdarzenie jest emitowane po zakończeniu operacji usuwania puli.

 Poniższy przykład pokazuje treść zdarzenia ukończenia usuwania puli.

```
{
   "id": "myPool1",
   "startTime": "2016-09-09T22:13:48.579Z",
   "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`startTime`|DateTime|Godzina, o której rozpoczęto Usuwanie puli.|
|`endTime`|DateTime|Godzina, o której ukończono Usuwanie puli.|

## <a name="remarks"></a>Uwagi

Aby uzyskać więcej informacji o stanach i kodach błędów dla operacji zmiany rozmiaru puli, zobacz [Usuwanie puli z konta](/rest/api/batchservice/delete-a-pool-from-an-account).
