---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2a8f27c0df2224aed5c69c8c38f463a97e3cf294
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711397"
---
W tej tabeli przedstawiono [metryki na poziomie konta](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts).

| Metryka | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |