---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057738"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe limity poziomów udziałów plików w warstwie Premium

|Warstwowy  |Cel  |
|---------|---------|
|Minimalny wzrost/zmniejszenie rozmiaru    |1 GiB      |
|Liczba IOPS linii bazowej    |1 Liczba operacji we/wy na sekundę, do 100 000|
|Rozerwanie operacji we/wy    |3 razy dziennie operacji we/wy na GiB, do 100 000|
|Szybkość ruchu wychodzącego         |60 MiB/s + 0,06 * GiB aprowizacji        |
|Szybkość transferu danych przychodzących| 40 MiB/s + 0,04 * GiB aprowizacji |

#### <a name="file-level-limits"></a>Limity poziomów plików

|Warstwowy  |Plik standardowy  |Plik Premium  |
|---------|---------|---------|
|Rozmiar     |1 TiB         |4 TiB         |
|Maksymalna liczba operacji we/wy na plik      |1000         |5000         |
|Równoczesne uchwyty     |2000         |2000         |
|Ruch wychodzący     |Zobacz standardowe wartości przepływności plików         |300 MiB/s         |
|Ruch przychodzący     |Zobacz standardowe wartości przepływności plików         |200 MiB/s         |
|Przepływność     |Do 60 MiB/s         |Zobacz wartości wejściowe/wyjściowe plików w warstwie Premium         |