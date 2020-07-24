---
title: Obliczenia o wysokiej wydajności na maszynach wirtualnych serii H — Virtual Machines platformy Azure
description: Poznaj funkcje i możliwości maszyn wirtualnych z serii H zoptymalizowane pod kątem HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: 1f88496a6c474194089f20e128b2b92ec61e8559
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083433"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Obliczenia o wysokiej wydajności na maszynach wirtualnych z serii H

Obliczenia o wysokiej wydajności (HPC) na maszynach wirtualnych z serii HB i HC zapewniają optymalną wydajność HPC dla wszystkich maszyn wirtualnych na platformie Azure. Maszyny wirtualne zoptymalizowane pod kątem HPC są używane do rozwiązywania niektórych trudnych problemów matematycznych, takich jak: płynność Biodynamiki, symulacje gazu oraz modelowanie pogody.

W tym artykule omówiono niektóre kluczowe funkcje maszyn wirtualnych z serii HB i HC, dlaczego te maszyny wirtualne działają dobrze w scenariuszach HPC i jak zacząć pracę.

## <a name="features-and-capabilities"></a>Funkcje i możliwości

Maszyny wirtualne z serii HB i HC zostały zaprojektowane w celu zapewnienia najlepszej wydajności HPC, skalowalności interfejsu przekazywania komunikatów (MPI) i efektywności kosztów obciążeń HPC.

### <a name="message-passing-interface"></a>Interfejs przekazywania komunikatów

Seria HB i seria HC obsługują niemal wszystkie typy i wersje MPI. Oto niektóre z najpopularniejszych, obsługiwanych typów MPI: OpenMPI, MVAPICH2, platform MPI, Intel MPI i wszystkie czasowniki zdalnego bezpośredniego dostępu do pamięci (RDMA). Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu przekazywania komunikatów dla HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA i InfiniBand

Interfejs RDMA jest standardem dla maszyn wirtualnych z serii HB i HC. Wystąpienia z obsługą funkcji RDMA komunikują się za pośrednictwem sieci InfiniBand, działając przy użyciu ulepszonych stawek danych (EDR) dla maszyn wirtualnych serii HB i HC. Wystąpienia z obsługą funkcji RDMA mogą zwiększyć skalowalność i wydajność niektórych aplikacji MPI.

Konfiguracja InfiniBand obsługująca maszyny wirtualne serii HB i HC nie blokuje drzew tłuszczowych o niskiej średnicy dla spójnej wydajności RDMA.

Zobacz [Włączanie InfiniBand](enable-infiniband.md) , aby dowiedzieć się więcej o konfigurowaniu InfiniBand na maszynach wirtualnych z serii HB lub HC.

## <a name="get-started"></a>Rozpoczęcie pracy

Najpierw zdecyduj, która maszyna wirtualna serii H ma być używana. Aby uzyskać szczegółowe informacje na temat maszyn wirtualnych zoptymalizowanych pod kątem HPC, zobacz [Omówienie z serii HB](hb-series-overview.md) i [Omówienie serii HC](hc-series-overview.md). Aby uzyskać specyfikacje, zobacz [duże rozmiary maszyn wirtualnych obliczeniowych o wysokiej wydajności](../../sizes-hpc.md).

Po wybraniu i utworzeniu maszyny wirtualnej dla aplikacji musisz ją skonfigurować, włączając opcję InfiniBand. Aby dowiedzieć się, jak włączyć funkcję InfiniBand na maszynach wirtualnych z systemami Windows i Linux, zobacz [Włączanie usługi InfiniBand](enable-infiniband.md).

Krytyczny składnik obciążeń HPC to MPI. Seria HB i seria HC obsługują niemal wszystkie typy i wersje MPI. Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu przekazywania komunikatów dla HPC](setup-mpi.md).

Po wybraniu serii maszyn wirtualnych Skonfiguruj InfiniBand i MPI, aby rozpocząć tworzenie obciążeń HPC.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem HB-Series](hb-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md) , aby dowiedzieć się więcej na temat kluczowych różnic i specyfikacji.

- W przypadku wyższego poziomu, widoku architektury działającego obciążeń HPC, zobacz [wydajność obliczeń o wysokiej wydajności (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
