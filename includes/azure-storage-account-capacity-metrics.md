---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 48e2d798a3dcf7354e68d07785c2762ba03f39f8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564023"
---
W tej tabeli przedstawiono [metryki na poziomie konta](../articles/azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts).

| Metric | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |