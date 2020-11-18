---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681041"
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

|Obszar  |Plik standardowy  |Plik Premium  |
|---------|---------|---------|
|Rozmiar     |1 TiB         |4 TiB         |
|Maksymalna liczba operacji we/wy na plik      |1000         |Do 8 000 *         |
|Równoczesne uchwyty     |2000         |2000         |
|Ruch wychodzący     |Zobacz standardowe wartości przepływności plików         |300 MiB/s (do 1 GiB/s przy użyciu obrazu SMB wielokanałowe) * *         |
|Ruch przychodzący     |Zobacz standardowe wartości przepływności plików         |200 MiB/s (do 1 GiB/s przy użyciu obrazu SMB wielokanałowe) * *        |
|Przepływność     |Do 60 MiB/s         |Zobacz wartości wejściowe/wyjściowe plików w warstwie Premium         |

\*<sup>Dotyczy systemu iOS odczytu i zapisu (zazwyczaj mniejsze rozmiary we/wy <= 64 KB). Operacje na metadanych, inne niż odczyty i zapisy, mogą być mniejsze. </sup>

\*\*<sup>Z uwzględnieniem limitów sieci maszyn, dostępnej przepustowości, rozmiarów operacji we/wy, głębokości kolejki i innych czynników. Aby uzyskać szczegółowe informacje, zobacz [wydajność protokołu SMB wielokanałowe](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
