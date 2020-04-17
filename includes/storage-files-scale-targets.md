---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536447"
---
| Zasób | Standardowe udziały plików | Udziały plików Premium |
|----------|---------------|------------------------------------------|
| Minimalny rozmiar udziału plików | Nie minimum; płacić zgodnie z tobą | 100 GiB; Przygotowana |
| Maksymalny rozmiar udziału plików | 100 TiB*, 5 TiB | 100 TiB |
| Maksymalny rozmiar pliku w udziale plików | 1 TiB | 1 TiB |
| Maksymalna liczba plików w udziale plików | Bez ograniczeń | Bez ograniczeń |
| Maksymalna liczba IOPS na jedną akcję | 10 000 IOP*, 1000 IOPS | 100 000 IOPS |
| Maksymalna liczba zasad dostępu przechowywanego na udział plików | 5 | 5 |
| Docelowa przepływność dla pojedynczego udziału plików | do 300 MiB/s*, do 60 MiB/s,  | Wyświetlanie wartości przychodzących i wychodzących udziału plików w usłudze Premium|
| Maksymalny ruch wychodzący dla pojedynczego udziału plików | Zobacz standardową przepływność docelową udziału plików | Do 6204 MiB/s |
| Maksymalny ruch przychodzący dla pojedynczego udziału plików | Zobacz standardową przepływność docelową udziału plików | Do 4136 MiB/s |
| Maksymalna liczba otwartych uchwytów na plik | 2000 otwartych uchwytów | 2000 otwartych uchwytów |
| Maksymalna liczba migawek udziału | 200 migawek akcji | 200 migawek akcji |
| Maksymalna długość nazwy obiektu (katalogów i plików) | 2 048 znaków | 2 048 znaków |
| Maksymalny składnik nazwy ścieżki (w ścieżce \A\B\C\D każda litera jest komponentem) | 255 znaków | 255 znaków |

\*Domyślnie w standardowych udziałach plików jest 5 TiB, zobacz [Włączanie i tworzenie dużych udziałów plików,](../articles/storage/files/storage-files-how-to-create-large-file-share.md) aby uzyskać szczegółowe informacje na temat zwiększania standardowego udziału plików w skali do 100 TiB.
