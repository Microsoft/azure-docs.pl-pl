---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "75392411"
---
| Zasób | Środowisko docelowe        |
|----------|---------------|
| Maksymalny rozmiar pojedynczego kontenera obiektów BLOB | Taka sama jak Maksymalna pojemność konta magazynu |
| Maksymalna liczba bloków w blokowym obiekcie blob lub dołączeniu obiektu BLOB | bloki 50 000 |
| Maksymalny rozmiar bloku w blokowym obiekcie blob | 100 MiB |
| Maksymalny rozmiar bloku obiektu BLOB | 50 000 X 100 MiB (około 4,75 TiB) |
| Maksymalny rozmiar bloku w obiekcie blob dołączania | 4 MiB |
| Maksymalny rozmiar obiektu BLOB dołączania | 50 000 x 4 MiB (około 195 GiB) |
| Maksymalny rozmiar obiektu BLOB stronicowania | 8 TiB |
| Maksymalna liczba przechowywanych zasad dostępu dla kontenera obiektów BLOB | 5 |
|Docelowa stawka żądania dla pojedynczego obiektu BLOB | Do 500 żądań na sekundę |
|Docelowa przepływność dla jednostronicowego obiektu BLOB | Do 60 MiB na sekundę |
|Docelowa przepływność dla pojedynczego bloku obiektu BLOB |Limity ruchu przychodzącego/wychodzącego do konta magazynu<sup>1</sup> |

<sup>1</sup> przepływność dla pojedynczego obiektu BLOB zależy od kilku czynników, w tym między innymi: współbieżność, rozmiar żądania, warstwa wydajności, szybkość źródła dla przekazywania i miejsce docelowe do pobrania. Aby wykorzystać ulepszenia wydajności [blokowych obiektów BLOB o wysokiej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), Przekaż większe obiekty blob lub bloki. W celu wywołaj operację [Put obiektu BLOB](/rest/api/storageservices/put-blob) lub [Put blok](/rest/api/storageservices/put-block) z rozmiarem obiektu BLOB lub bloku, który jest większy niż 4 MIB dla kont magazynu w warstwie Standardowa. W przypadku blokowych obiektów BLOB w warstwie Premium lub dla kont magazynu Data Lake Storage Gen2 Użyj rozmiaru bloku lub obiektu BLOB, który jest większy niż 256 KiB.
