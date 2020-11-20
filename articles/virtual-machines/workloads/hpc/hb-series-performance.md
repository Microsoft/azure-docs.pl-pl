---
title: Niestandardowa wydajność maszyn wirtualnych z serii HB
description: Poznaj wyniki testów wydajności dla rozmiarów maszyn wirtualnych z serii HB na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 10d1bfea4527eb7ab9a341de8f12a32cb59597eb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966976"
---
# <a name="hb-series-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych z serii HB

Kilka testów wydajności zostało uruchomionych w rozmiarach z serii HB. Poniżej przedstawiono niektóre wyniki tego testu wydajnościowego.

| Obciążenie                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Triad strumienia                                    | 260 GB/s (32-33 GB/s na CCX)  |
| High-Performance Linpack (HPL)                  | 1 000 GigaFLOPS (Rpeak), 860 GigaFLOPS (RMAX) |
| Opóźnienie RDMA & przepustowość                        | 1,27 mikrosekund, 99,1 GB/s   |
| FIO na lokalnym dysku SSD interfejsu NVMe                           | 1,7 GB/s odczyty, 1,0 GB/s zapisów      |  
| IOR na 4 * SSD w warstwie Premium platformy Azure (P30 Managed Disks, RAID0) * *  | 725 MB/s odczyty, 780 MB/zapisy   |


## <a name="mpi-latency"></a>Opóźnienie MPI

Test opóźnienia MPI z zestawu OSU mikrotestu jest uruchamiany. Przykładowe skrypty są w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="MPI opóźnienie na platformie Azure.":::

## <a name="mpi-bandwidth"></a>MPI przepustowość

Test przepustowości MPI z zestawu OSU mikrotestu jest uruchamiany. Przykładowe skrypty są w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="MPI przepustowość na platformie Azure (HB).":::


## <a name="mellanox-perftest"></a>Perftest Mellanox

[Pakiet Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) ma wiele testów InfiniBand, takich jak opóźnienie (ib_send_lat) i przepustowość (ib_send_bw). Przykładowe polecenie jest poniżej.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z najnowszymi powiadomieniami i niektórymi przykładami obliczeń o wysokiej wydajności (HPC) i z wynikami na [blogach społecznościowych obliczeń na platformie Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z ogólnym widokiem architektury uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
