---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698099"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe limity poziomów udziałów plików w warstwie Premium

|Warstwowy  |Cel  |
|---------|---------|
|Minimalny wzrost/zmniejszenie rozmiaru    |1 GiB      |
|Liczba IOPS linii bazowej    |400 + 1 Liczba operacji we/wy na sekundę na GiB, do 100 000|
|Rozerwanie operacji we/wy    |Maks. (4000, 1 razy dziennie operacji we/wy na GiB), do 100 000|
|Szybkość ruchu wychodzącego         |60 MiB/s + 0,06 * GiB aprowizacji        |
|Szybkość transferu danych przychodzących| 40 MiB/s + 0,04 * GiB aprowizacji |

#### <a name="file-level-limits"></a>Limity poziomów plików

|Warstwowy  |Plik standardowy  |Plik Premium  |
|---------|---------|---------|
|Rozmiar     |1 TiB         |4 TiB         |
|Maksymalna liczba operacji we/wy na plik      |1000         |Do 8 000 *         |
|Równoczesne uchwyty     |2000         |2000         |
|Ruch wychodzący     |Zobacz standardowe wartości przepływności plików         |300 MiB/s (do 1 GiB/s przy użyciu obrazu SMB wielokanałowe) * *         |
|Ruch przychodzący     |Zobacz standardowe wartości przepływności plików         |200 MiB/s (do 1 GiB/s przy użyciu obrazu SMB wielokanałowe) * *        |
|Przepływność     |Do 60 MiB/s         |Zobacz wartości wejściowe/wyjściowe plików w warstwie Premium         |

\*<sup>Dotyczy systemu iOS odczytu i zapisu (zazwyczaj mniejsze rozmiary we/wy <= 64 KB). Operacje na metadanych, inne niż odczyty i zapisy, mogą być mniejsze. </sup>

\*\*<sup>Z uwzględnieniem limitów sieci maszyn, dostępnej przepustowości, rozmiarów operacji we/wy, głębokości kolejki i innych czynników. Aby uzyskać szczegółowe informacje, zobacz [wydajność protokołu SMB wielokanałowe](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
