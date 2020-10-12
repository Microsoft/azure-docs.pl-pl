---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74279212"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). W przypadku porównywania dysków mierzonych w GB (1000 ^ 3 bajtów) z dyskami mierzonymi w GiB (1024 ^ 3) Pamiętaj, że numery pojemności określone w GiB mogą być mniejsze. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Dyski danych mogą działać w trybie buforowanym lub niebuforowanym. Dla pracy dysku danych w trybie buforowanym tryb pamięci podręcznej hosta jest ustawiony na wartość **ReadOnly** lub **ReadWrite**.  Dla pracy dysku danych bez buforowania tryb pamięci podręcznej hosta jest ustawiony na wartość **None**.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków danych do dwóch dysków na vCPU.
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana przepustowość przyalokowana na typ maszyny wirtualnej dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Aby uzyskać więcej informacji, zobacz [przepustowość sieci maszyny wirtualnej](../articles/virtual-network/virtual-machine-network-throughput.md).

  Górne limity nie są gwarantowane. Ogranicza wskazówki dotyczące wybierania odpowiedniego typu maszyny wirtualnej dla zamierzonej aplikacji. Rzeczywista wydajność sieci będzie zależeć od kilku czynników, w tym przeciążenia sieci, obciążeń aplikacji i ustawień sieciowych. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optymalizowanie przepływności sieci dla maszyn wirtualnych platformy Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Aby osiągnąć oczekiwaną wydajność sieci w systemie Linux lub Windows, może być konieczne wybranie określonej wersji lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [testowanie przepustowości/przepływności (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



