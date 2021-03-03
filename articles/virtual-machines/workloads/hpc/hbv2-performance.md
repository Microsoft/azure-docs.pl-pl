---
title: Wydajność maszyny wirtualnej z serii HBv2
description: Poznaj wyniki testów wydajności dla rozmiarów maszyn wirtualnych serii HBv2 na platformie Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670066"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych z serii HBv2

Na maszynach wirtualnych o rozmiarze [serii HBv2](../../hbv2-series.md) zostały uruchomione kilka testów wydajności. Poniżej przedstawiono niektóre wyniki tego testu wydajnościowego.


| Obciążenie                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad strumienia                                    | 350 GB/s (21-23 GB/s na CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 teraflopa (Rpeak, FP64), 8 teraflopa (RMAX, operacji FP32)               |
| Opóźnienie RDMA & przepustowość                        | 1,2 mikrosekund, 190 GB/s                                        |
| FIO na lokalnym dysku SSD interfejsu NVMe                           | 2,7 GB/s odczyty, 1,1 GB/s zapisów; 102k operacje we/wy, Liczba zapisów IOPS 115 |
| IOR 8 * Azure SSD w warstwie Premium (P40 Managed Disks, RAID0) * *  | 1,3 GB/s odczyty, 2,5 GB/zapisy; 101k operacje we/wy, 105k operacje we/wy na sekundę |


## <a name="mpi-latency"></a>Opóźnienie MPI

Test opóźnienia MPI z zestawu OSU mikrotestu jest uruchamiany. Przykładowe skrypty znajdują się w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI opóźnienie na platformie Azure.":::


## <a name="mpi-bandwidth"></a>MPI przepustowość

Test przepustowości MPI z zestawu OSU mikrotestu jest uruchamiany. Przykładowe skrypty znajdują się w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI przepustowość na platformie Azure (HB).":::


## <a name="mellanox-perftest"></a>Perftest Mellanox

[Pakiet Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) ma wiele testów InfiniBand, takich jak opóźnienie (ib_send_lat) i przepustowość (ib_send_bw). Przykładowe polecenie jest poniżej. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z najnowszymi powiadomieniami i niektórymi przykładami obliczeń o wysokiej wydajności (HPC) i z wynikami na [blogach społecznościowych obliczeń na platformie Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z ogólnym widokiem architektury uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).