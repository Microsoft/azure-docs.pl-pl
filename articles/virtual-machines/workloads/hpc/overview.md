---
title: Obliczenia o wysokiej wydajności na maszynach wirtualnych z serii H i serii N — platforma Azure Virtual Machines
description: Dowiedz się więcej o funkcjach i możliwościach z obsługą maszyn wirtualnych z serii H i serii N zoptymalizowanych pod kątem HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720597"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Obliczenia o wysokiej wydajności na maszynach wirtualnych z serii H i serii N

Maszyny wirtualne z serii H obsługujące funkcję InfiniBand na platformie Azure zostały zaprojektowane w celu zapewnienia wydajności klasy lidera, skalowalności interfejsu MPI (Message Passing Interface) i efektywności kosztów dla różnych rzeczywistych obciążeń platformy HPC i AI. Te zoptymalizowane na potrzeby przetwarzania maszyn wirtualnych o wysokiej wydajności (HPC) są używane do rozwiązywania niektórych problemów z dużą ilością operacji w nauce i inżynieryjnych, takich jak: płynność dynamiki, modelowanie ziemi, symulacje pogody itd.

W tych artykułach opisano, jak rozpocząć pracę z maszynami wirtualnymi z serii H z obsługą urządzeń InfiniBand oraz na platformie Azure, a także optymalną konfigurację obciążeń HPC i AI na maszynach wirtualnych w celu zapewnienia skalowalności.

## <a name="features-and-capabilities"></a>Funkcje i możliwości

Maszyny wirtualne z serii H z obsługą InfiniBand i serii N zostały zaprojektowane w celu zapewnienia najlepszej wydajności HPC, skalowalności MPI i efektywności kosztów obciążeń HPC. Zapoznaj się z maszynami wirtualnymi [serii H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) , aby dowiedzieć się więcej o funkcjach i możliwościach maszyn wirtualnych.

### <a name="rdma-and-infiniband"></a>RDMA i InfiniBand

Maszyny wirtualne z serii [H](../../sizes-hpc.md) [obsługujące funkcję RDMA](../../sizes-hpc.md#rdma-capable-instances) i [N-Series](../../sizes-gpu.md) komunikują się za pośrednictwem sieci InfiniBand o małym opóźnieniu i dużej przepustowości. Funkcja RDMA przez takie połączenie ma kluczowe znaczenie dla zwiększenia skalowalności i wydajności obciążeń w węzłach rozproszonych i systemu AI. Maszyny wirtualne z serii H z obsługą funkcji InfiniBand i serii N są połączone w nieblokującym drzewie FAT o niskiej średnicy dla zoptymalizowanej i spójnej wydajności RDMA.
Aby dowiedzieć się więcej o konfigurowaniu InfiniBand na maszynach wirtualnych z włączoną funkcją InfiniBand, zobacz [Włączanie InfiniBand](enable-infiniband.md) .

### <a name="message-passing-interface"></a>Interfejs przekazywania komunikatów

Seria H z obsługą wirtualizacji SR-IOV i serii N obsługują niemal wszystkie biblioteki i wersje MPI. Najczęściej używanymi bibliotekami MPI są: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, platform MPI. Obsługiwane są wszystkie zlecenia zdalnego bezpośredniego dostępu do pamięci (RDMA).
Zobacz [Konfigurowanie MPI](setup-mpi.md) , aby dowiedzieć się więcej o instalowaniu różnych obsługiwanych bibliotek MPI i ich optymalną konfigurację.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest wybranie typu maszyn wirtualnych serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) dla obciążenia w oparciu o specyfikacje maszyn wirtualnych i [funkcję RDMA](../../sizes-hpc.md#rdma-capable-instances).
Następnie skonfiguruj maszynę wirtualną przez włączenie funkcji InfiniBand. Istnieją różne metody do wykonania tej czynności, w tym używanie zoptymalizowanych obrazów maszyn wirtualnych ze sterownikami rozszerzania. Aby uzyskać szczegółowe informacje, zobacz [Optymalizacja dla systemu Linux](configure.md) i [Włączanie usługi InfiniBand](enable-infiniband.md) .
Trzeci, w przypadku obciążeń węzła rozproszonego Wybieranie i Konfigurowanie MPI jest odpowiednio krytyczne. Aby uzyskać szczegółowe informacje, zobacz [set up MPI](setup-mpi.md) .
Czwarty, w celu zapewnienia wydajności i skalowalności, optymalnie konfiguruje obciążenia, postępując zgodnie ze wskazówkami dotyczącymi rodziny maszyn wirtualnych, takimi jak [Przegląd serii HBv3](hbv3-series-overview.md) oraz [Przegląd z serii HC](hc-series-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [konfigurowaniu i optymalizowaniu](configure.md) maszyn wirtualnych z serii [H](../../sizes-hpc.md) i serii [N](../../sizes-gpu.md) .
- Zapoznaj się z omówieniem [HBv3](hb-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md) , aby dowiedzieć się więcej o optymalnym konfigurowaniu obciążeń dotyczących wydajności i skalowalności.
- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
