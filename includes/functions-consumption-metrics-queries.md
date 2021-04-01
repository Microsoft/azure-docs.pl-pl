---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168118"
---
#### <a name="determine-memory-usage"></a>Określanie użycia pamięci 

W obszarze **monitorowanie** wybierz pozycję **dzienniki (analiza)**, a następnie skopiuj poniższe zapytanie telemetryczne i wklej je do okna zapytania i wybierz polecenie **Uruchom**. To zapytanie zwraca całkowite użycie pamięci przy każdym próbkowanym czasie.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Wyniki wyglądają podobnie jak w poniższym przykładzie:

| Sygnatura czasowa \[ UTC\]          | name          | wartość       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Bajty prywatne | 209 932 288 |
| 9/12/2019, 1:06:14 \. 994 am | Bajty prywatne | 212 189 184 |
| 9/12/2019, 1:06:30 \. 010 am | Bajty prywatne | 231 714 816 |
| 9/12/2019, 1:07:15 \. 040 am | Bajty prywatne | 210 591 744 |
| 9/12/2019, 1:12:16 \. 285 am | Bajty prywatne | 216 285 184 |
| 9/12/2019, 1:12:31 \. 376 am | Bajty prywatne | 235 806 720 |

#### <a name="determine-duration"></a>Określ czas trwania 

Azure Monitor śledzi metryki na poziomie zasobu, który dla funkcji jest aplikacją funkcji. Integracja Application Insights emituje metryki dla poszczególnych funkcji. Oto przykładowe zapytanie analityczne, aby uzyskać średni czas trwania funkcji:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
