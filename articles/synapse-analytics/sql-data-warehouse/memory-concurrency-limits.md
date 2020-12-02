---
title: Limity pamięci i współbieżności
description: Wyświetlanie limitów pamięci i współbieżności przydzielonych do różnych poziomów wydajności i klas zasobów dla dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0ad93fb4a1269b4cca02b114c0427f0c44a31b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455350"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Limity pamięci i współbieżności dla dedykowanej puli SQL w usłudze Azure Synapse Analytics

Wyświetl limity pamięci i współbieżności przydzielono do różnych poziomów wydajności i klas zasobów w usłudze Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Ustawienia wydajności magazynu danych

W poniższych tabelach przedstawiono maksymalną pojemność hurtowni danych na różnych poziomach wydajności. Aby zmienić poziom wydajności, zobacz [skalowanie obliczeniowe — Portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Poziomy usług

Poziomy usług mieszczą się w zakresie od DW100c do DW30000c.

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje na węzeł obliczeniowy | Pamięć na magazyn danych (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c.            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Maksymalny poziom usług to DW30000c, który ma 60 węzłów obliczeniowych i jedną dystrybucję na węzeł obliczeniowy. Na przykład magazyn danych 600 TB w procesach DW30000c około 10 TB na węzeł obliczeniowy.

## <a name="concurrency-maximums-for-workload-groups"></a>Maksymalne wartości współbieżności dla grup obciążeń

Po wprowadzeniu [grup obciążeń](sql-data-warehouse-workload-isolation.md)nie ma już zastosowania koncepcji dotyczącej miejsc współbieżności.  Zasoby na żądanie są przypisywane na podstawie procentowej i określone w definicji grupy obciążeń.  Jednak nawet w przypadku usuwania miejsc współbieżności w poszczególnych zapytaniach jest wymagana minimalna ilość zasobów na podstawie poziomu usługi.  W poniższej tabeli zdefiniowano minimalną ilość zasobów wymaganych na zapytanie między poziomami usług i skojarzoną współbieżność, która może zostać osiągnięta.

|Poziom usługi|Maksymalna liczba współbieżnych zapytań|Minimalna wartość% obsługiwana dla REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8%|
|DW400c|16|6,25%|
|DW500c.|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Maksymalne wartości współbieżności dla klas zasobów

Aby upewnić się, że każda kwerenda ma wystarczającą ilość zasobów do wydajnego wykonywania, Synapse SQL śledzi wykorzystanie zasobów przez przypisanie miejsc współbieżności do poszczególnych zapytań. System umieszcza zapytania w kolejce na podstawie ważności i miejsc współbieżności. Zapytania oczekują w kolejce do momentu udostępnienia wystarczającej liczby miejsc współbieżności. [Znaczenie](sql-data-warehouse-workload-importance.md) i miejsca współbieżności określają priorytety procesora. Aby uzyskać więcej informacji, zobacz [Analizowanie obciążenia](analyze-your-workload.md)

**Statyczne klasy zasobów**

W poniższej tabeli przedstawiono maksymalną liczbę współbieżnych zapytań i miejsc współbieżności dla każdej [klasy zasobów statycznych](resource-classes-for-workload-management.md).  

| Poziom usługi | Maksymalna liczba współbieżnych zapytań | Dostępne gniazda współbieżności | Gniazda używane przez staticrc10 | Gniazda używane przez staticrc20 | Gniazda używane przez staticrc30 | Gniazda używane przez staticrc40 | Gniazda używane przez staticrc50 | Gniazda używane przez staticrc60 | Gniazda używane przez staticrc70 | Gniazda używane przez staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c.        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamiczne klasy zasobów**

W poniższej tabeli przedstawiono maksymalną liczbę współbieżnych zapytań i miejsc współbieżności dla każdej [klasy zasobów dynamicznych](resource-classes-for-workload-management.md). Dynamiczne klasy zasobów używają 3-10-22-70 alokacji procentowej pamięci dla małych i średnich klas zasobów xlarge na wszystkich poziomach usług.

| Poziom usługi | Maksymalna liczba współbieżnych zapytań | Dostępne gniazda współbieżności | Gniazda używane przez smallrc | Gniazda używane przez mediumrc | Gniazda używane przez largerc | Gniazda używane przez xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c.        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Gdy nie ma wystarczającej liczby miejsc współbieżności, aby rozpocząć wykonywanie zapytania, zapytania są umieszczane w kolejce i wykonywane na podstawie ważności.  W przypadku istnienia równoważnej ważności zapytania są wykonywane na pierwszej, pierwszej zasadzie.  Ponieważ zakończyło się zapytania, a liczba zapytań i gniazd spadnie poniżej limitów, usługa Azure Synapse Analytics zwalnia zapytania z kolejki.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sposobu korzystania z klas zasobów w celu dalszej optymalizacji obciążenia, zapoznaj się z następującymi artykułami:

* [Klasy zasobów do zarządzania obciążeniami](resource-classes-for-workload-management.md)
* [Analizowanie obciążenia](analyze-your-workload.md)
