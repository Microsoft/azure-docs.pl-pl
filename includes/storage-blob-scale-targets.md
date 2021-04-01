---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665620"
---
| Zasób | Cel | Target (wersja zapoznawcza) |
|-|-|-|
| Maksymalny rozmiar pojedynczego kontenera obiektów BLOB | Taka sama jak Maksymalna pojemność konta magazynu |  |
| Maksymalna liczba bloków w blokowym obiekcie blob lub dołączeniu obiektu BLOB | bloki 50 000 |  |
| Maksymalny rozmiar bloku w blokowym obiekcie blob | 100 MiB | 4000 MiB (wersja zapoznawcza) |
| Maksymalny rozmiar bloku obiektu BLOB | 50 000 X 100 MiB (około 4,75 TiB) | 50 000 X 4000 MiB (około 190,7 TiB) (wersja zapoznawcza) |
| Maksymalny rozmiar bloku w obiekcie blob dołączania | 4 MiB |  |
| Maksymalny rozmiar obiektu BLOB dołączania | 50 000 x 4 MiB (około 195 GiB) |  |
| Maksymalny rozmiar obiektu BLOB stronicowania | 8 TiB<sup>2</sup> |  |
| Maksymalna liczba przechowywanych zasad dostępu dla kontenera obiektów BLOB | 5 |  |
| Docelowa stawka żądania dla pojedynczego obiektu BLOB | Do 500 żądań na sekundę |  |
| Docelowa przepływność dla jednostronicowego obiektu BLOB | Do 60 MiB na sekundę<sup>2</sup> |  |
| Docelowa przepływność dla pojedynczego bloku obiektu BLOB | Limity ruchu przychodzącego/wychodzącego do konta magazynu<sup>1</sup> |  |

<sup>1</sup> przepływność dla pojedynczego obiektu BLOB zależy od kilku czynników, w tym między innymi: współbieżność, rozmiar żądania, warstwa wydajności, szybkość źródła dla przekazywania i miejsce docelowe do pobrania. Aby wykorzystać ulepszenia wydajności [blokowych obiektów BLOB o wysokiej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), Przekaż większe obiekty blob lub bloki. W celu wywołaj operację [Put obiektu BLOB](/rest/api/storageservices/put-blob) lub [Put blok](/rest/api/storageservices/put-block) z rozmiarem obiektu BLOB lub bloku, który jest większy niż 4 MIB dla kont magazynu w warstwie Standardowa. W przypadku blokowych obiektów BLOB w warstwie Premium lub dla kont magazynu Data Lake Storage Gen2 Użyj rozmiaru bloku lub obiektu BLOB, który jest większy niż 256 KiB.

<sup>2</sup> stronicowe obiekty blob nie są jeszcze obsługiwane w przypadku kont, które mają dla nich ustawienie **hierarchicznej przestrzeni nazw** .

W poniższej tabeli opisano maksymalny rozmiar bloków i obiektów BLOB dozwolonych przez wersję usługi.

| Wersja usługi | Maksymalny rozmiar bloku (za pośrednictwem bloku Put) | Maksymalny rozmiar obiektu BLOB (za pośrednictwem listy bloków Put) | Maksymalny rozmiar obiektu BLOB za pośrednictwem jednokrotnej operacji zapisu (za pośrednictwem obiektu BLOB Put) |
|-|-|-|-|
| Wersja 2019-12-12 i nowsze | 4000 MiB (wersja zapoznawcza) | Około 190,7 TiB (4000 MiB X 50 000 bloków) (wersja zapoznawcza) | 5000 MiB (wersja zapoznawcza) |
| Wersja 2016-05-31 do 2019-07-07 | 100 MiB | Około 4,75 TiB (100 MiB X 50 000 bloków) | 256 MiB |
| Wersje wcześniejsze niż 2016-05-31 | 4 MiB | Około 195 GiB (4 bloki MiB X 50 000) | 64 MiB |
