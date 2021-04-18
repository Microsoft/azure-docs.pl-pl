---
title: Obliczenia o wysokiej wydajności na maszynach wirtualnych serii H i N z włączoną obsługą standardu InfiniBand — Azure Virtual Machines
description: Dowiedz się więcej o funkcjach i możliwościach maszyn wirtualnych serii H i N z włączoną obsługą technologii InfiniBand zoptymalizowanych pod kątem hpc.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 554764b89e5da4cd6777ec89fcb2f2d5ad104ebf
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600271"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Obliczenia o wysokiej wydajności na maszynach wirtualnych serii H i N z włączoną obsługą standardu InfiniBand

Maszyny wirtualne serii H i N z włączoną obsługą technologii InfiniBand platformy Azure zostały zaprojektowane w celu zapewnienia wydajności klasy lidera, skalowalności Message Passing Interface (MPI) i efektywności kosztowej dla różnych rzeczywistych obciążeń HPC i AI. Te maszyny wirtualne zoptymalizowane pod kątem obliczeń o wysokiej wydajności (HPC) są używane do rozwiązywania niektórych z najbardziej wymagających obliczeń problemów w nauce i inżynierii, takich jak dynamika płynów, modelowanie ziemi, symulacje pogodowe itp.

W tych artykułach opisano, jak rozpocząć pracę na maszynach wirtualnych z serii H i N z włączoną obsługą infiniBand na platformie Azure, a także jak uzyskać optymalną konfigurację obciążeń HPC i AI na tych maszynach wirtualnych w celu zapewnienia skalowalności.

## <a name="features-and-capabilities"></a>Funkcje i możliwości

Maszyny wirtualne serii H i N z włączoną opcją InfiniBand zostały zaprojektowane w celu zapewnienia najlepszej wydajności HPC, skalowalności MPI i efektywności kosztowej dla obciążeń HPC. Zobacz [Maszyny wirtualne serii H](../../sizes-hpc.md) i [N,](../../sizes-gpu.md) aby dowiedzieć się więcej o funkcjach i możliwościach maszyn wirtualnych.

### <a name="rdma-and-infiniband"></a>RDMA i InfiniBand

Maszyny wirtualne [serii H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z serii [RDMA](../../sizes-hpc.md#rdma-capable-instances) komunikują się za pośrednictwem sieci InfiniBand o małych opóźnieniach i dużej przepustowości. Możliwość RDMA za pośrednictwem takiego połączenia wzajemnego ma kluczowe znaczenie dla zwiększenia skalowalności i wydajności obciążeń HPC i AI z węzłami rozproszonymi. Maszyny wirtualne serii H i N z włączoną obsługą infiniBand są połączone w nieblokowym drzewie grubym o niskiej zawartości, co zapewnia zoptymalizowaną i spójną wydajność RDMA.
Zobacz [Enable InfiniBand (Włączanie infiniBand),](enable-infiniband.md) aby dowiedzieć się więcej na temat konfigurowania sieci InfiniBand na maszyn wirtualnych z włączoną obsługą infiniBand.

### <a name="message-passing-interface"></a>Interfejs przekazywania komunikatów

Seria H i seria N z włączoną obsługą funkcji SR-IOV obsługują prawie wszystkie biblioteki i wersje MPI. Niektóre z najczęściej używanych bibliotek MPI to: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI. Obsługiwane są wszystkie czasowniki zdalnego bezpośredniego dostępu do pamięci (RDMA).
Zobacz [Konfigurowanie mpi,](setup-mpi.md) aby dowiedzieć się więcej o instalowaniu różnych obsługiwanych bibliotek MPI i ich optymalnej konfiguracji.

## <a name="get-started"></a>Rozpoczęcie pracy

Pierwszym krokiem jest wybranie typu maszyny wirtualnej serii [H](../../sizes-hpc.md) i [serii N](../../sizes-gpu.md) optymalnego dla obciążenia na podstawie specyfikacji maszyny wirtualnej i możliwości [RDMA.](../../sizes-hpc.md#rdma-capable-instances)
Po drugie skonfiguruj maszynę wirtualną, włączając sieć InfiniBand. Istnieją różne metody, w tym użycie zoptymalizowanych obrazów maszyn wirtualnych ze sterownikami. Aby uzyskać [szczegółowe informacje,](configure.md) zobacz Optymalizacja dla systemu Linux i [Włączanie infiniBand.](enable-infiniband.md)
Po trzecie, w przypadku obciążeń węzłów rozproszonych wybór i odpowiednie skonfigurowanie infrastruktury MPI ma kluczowe znaczenie. Aby [uzyskać szczegółowe informacje, zobacz Konfigurowanie mpi.](setup-mpi.md)
Po czwarte, aby uzyskać wydajność i skalowalność, optymalnie skonfiguruj obciążenia, korzystając ze wskazówek specyficznych dla rodziny maszyn wirtualnych, takich jak omówienie serii [HBv3](hbv3-series-overview.md) i omówienie serii [HC.](hc-series-overview.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [więcej o konfigurowaniu i optymalizowaniu](configure.md) maszyn wirtualnych serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z włączoną obsługą technologii InfiniBand.
- Zapoznaj się [z omówieniem serii HBv3](hb-series-overview.md) i omówieniem serii [HC,](hc-series-overview.md) aby dowiedzieć się więcej na temat optymalnego konfigurowania obciążeń pod potrzeby wydajności i skalowalności.
- Aby zapoznać się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności, zobacz blogi społeczności technicznej Azure Compute [Tech Community.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Przetestuj swoją [wiedzę za pomocą modułu szkoleniowego na temat optymalizacji aplikacji HPC na platformie Azure.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Aby uzyskać wyższy poziom widoku architektury uruchamiania obciążeń HPC, zobacz Obliczenia o wysokiej wydajności [(HPC) na platformie Azure.](/azure/architecture/topics/high-performance-computing/)
