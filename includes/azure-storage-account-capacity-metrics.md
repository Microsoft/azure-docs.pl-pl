---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 1aeaa8607618a6f0a2dbf756e6ddae420627bce6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750050"
---
W tej tabeli przedstawiono [metryki na poziomie konta](../articles/azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccounts).

| Metric | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |