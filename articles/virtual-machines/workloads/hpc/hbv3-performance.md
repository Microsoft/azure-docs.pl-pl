---
title: Wydajność maszyny wirtualnej z serii HBv3
description: Poznaj wyniki testów wydajności dla rozmiarów maszyn wirtualnych serii HBv3 na platformie Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c92c30da2b8e78715df46341dc4bc502ed5e34ce
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472850"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Wydajność maszyny wirtualnej z serii HBv3

Użytkownicy wczesnego dostępu do maszyn wirtualnych HBv3 mogą oczekiwać następujących wartości wydajności na wspólnych mikrotestach HPC

| Obciążenie                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triad strumienia                                    | 330-350 GB/s (~ 82-86 GB/s na NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, operacji FP32) dla rozmiaru maszyny wirtualnej 120-Core               |
| Opóźnienie RDMA & przepustowość                        | 1,2 mikrosekund (1-bajtowe), 192 GB/s (jednokierunkowe)                                        |
| FIO lokalnego interfejsu NVMe dysków SSD (RAID0)                  | 7 GB/s odczyty, 3 GB/s zapisu; 186k operacje we/wy, 201k operacje we/wy na sekundę |

## <a name="process-pinning"></a>Przypinanie procesów

Przypinanie procesów działa dobrze na maszynach wirtualnych z serii HBv3, ponieważ udostępniamy podstawowy krzem jako-to-jest to maszyna wirtualna gościa. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność.

## <a name="mpi-latency"></a>Opóźnienie MPI

Test opóźnienia MPI z zestawu OSU mikrotestu można wykonać zgodnie z poniższymi tematami. Przykładowe skrypty znajdują się w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI przepustowość
Test przepustowości MPI z pakietu OSU mikrotestu można wykonać na poniższe poniżej. Przykładowe skrypty znajdują się w serwisie [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Perftest Mellanox
[Pakiet Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) ma wiele testów InfiniBand, takich jak opóźnienie (ib_send_lat) i przepustowość (ib_send_bw). Przykładowe polecenie jest poniżej. 
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [skalowania aplikacji MPI](compiling-scaling-applications.md).
- Zapoznaj się z najnowszymi powiadomieniami i niektórymi przykładami obliczeń o wysokiej wydajności (HPC) i z wynikami na [blogach społecznościowych obliczeń na platformie Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z ogólnym widokiem architektury uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).