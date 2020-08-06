---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841938"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe limity poziomów udziałów plików w warstwie Premium

|Obszar  |Cel  |
|---------|---------|
|Minimalny wzrost/zmniejszenie rozmiaru    |1 GiB      |
|Liczba IOPS linii bazowej    |1 Liczba operacji we/wy na sekundę, do 100 000|
|Rozerwanie operacji we/wy    |3 razy dziennie operacji we/wy na GiB, do 100 000|
|Szybkość ruchu wychodzącego         |60 MiB/s + 0,06 * GiB aprowizacji        |
|Szybkość transferu danych przychodzących| 40 MiB/s + 0,04 * GiB aprowizacji |

#### <a name="file-level-limits"></a>Limity poziomów plików

|Obszar  |Plik Premium  |Plik standardowy |
|---------|---------|---------|
|Rozmiar                  |4 TiB         |1 TiB|
|Maksymalna liczba operacji we/wy na plik     |5000         |1000|
|Równoczesne uchwyty    |2000         |2000|
|Ruch wychodzący  |300 MiB/s|      Zobacz standardowe wartości przepływności plików|
|Ruch przychodzący  |200 MiB/s| Zobacz standardowe wartości przepływności plików|
|Przepływność| Zobacz wartości wejściowe/wyjściowe plików w warstwie Premium| Do 60 MiB/s|
