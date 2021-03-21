---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99579887"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). W przypadku porównywania dysków mierzonych w GB (1000 ^ 3 bajtów) z dyskami mierzonymi w GiB (1024 ^ 3) Pamiętaj, że numery pojemności określone w GiB mogą być mniejsze. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Dyski danych mogą działać w trybie buforowanym lub niebuforowanym. Dla pracy dysku danych w trybie buforowanym tryb pamięci podręcznej hosta jest ustawiony na wartość **ReadOnly** lub **ReadWrite**.  Dla pracy dysku danych bez buforowania tryb pamięci podręcznej hosta jest ustawiony na wartość **None**.
- Aby dowiedzieć się, jak uzyskać najlepszą wydajność magazynu dla maszyn wirtualnych, zobacz [wydajność maszyn wirtualnych i dysków](../articles/virtual-machines/disks-performance.md).
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana przepustowość przyalokowana na typ maszyny wirtualnej dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Aby uzyskać więcej informacji, zobacz [przepustowość sieci maszyny wirtualnej](../articles/virtual-network/virtual-machine-network-throughput.md).

  Górne limity nie są gwarantowane. Ogranicza wskazówki dotyczące wybierania odpowiedniego typu maszyny wirtualnej dla zamierzonej aplikacji. Rzeczywista wydajność sieci będzie zależeć od kilku czynników, w tym przeciążenia sieci, obciążeń aplikacji i ustawień sieciowych. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Aby osiągnąć oczekiwaną wydajność sieci w systemie Linux lub Windows, może być konieczne wybranie określonej wersji lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [testowanie przepustowości/przepływności (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



