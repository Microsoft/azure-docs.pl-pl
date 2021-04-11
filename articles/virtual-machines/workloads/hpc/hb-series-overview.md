---
title: Informacje o maszynie wirtualnej z serii HB — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej na temat obsługi wersji zapoznawczej rozmiaru maszyny wirtualnej z serii HB na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2f5dddd3d59ebe778d577176e439528a86bb42a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802600"
---
# <a name="hb-series-virtual-machines-overview"></a>Informacje o maszynach wirtualnych z serii HB

Maksymalizacja wydajności aplikacji obliczeniowych o wysokiej wydajności (HPC) na AMD EPYC wymaga, aby przydatnego wpływać na pamięć i proces umieszczania. Poniżej przedstawiono konspekt architektury AMD EPYC i naszej implementacji na platformie Azure dla aplikacji HPC. Termin "pNUMA" będzie używany do odwoływania się do fizycznej domeny NUMA i "vNUMA" w celu odwoływania się do zwirtualizowanej domeny NUMA.

Fizycznie serwer [serii HB](../../hb-series.md) to 2 * 32-core EPYC 7551 procesorów CPU, co łącznie 64 rdzenie fizyczne. Te rdzenie 64 są podzielone na 16 domen pNUMA (8 na gniazdo), z których każdy jest czterema rdzeniami i nazywany "złożonym PROCESORem" (lub "CCX"). Każdy CCX ma własną pamięć podręczną L3, co oznacza, że system operacyjny zobaczy granicę pNUMA/vNUMA. Para sąsiadujących CCXs umożliwia dostęp do dwóch kanałów fizycznej pamięci DRAM (32 GB pamięci DRAM w serwerach z serii HB).

Aby zapewnić obsługę funkcji hypervisor platformy Azure bez zakłócania działania maszyny wirtualnej, rezerwujemy fizyczną pNUMA domenę 0 (pierwszy CCX). Następnie przypiszemy domeny pNUMA 1-15 (pozostałe jednostki CCX) dla maszyny wirtualnej. Na maszynie wirtualnej zobaczysz:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` rdzenie na maszynę wirtualną

Sama maszyna wirtualna nie wie, że pNUMA 0 nie została do niej udzielona. Maszyna wirtualna rozumie pNUMA 1-15 jako vNUMA 0-14 z 7 vNUMA na vSocket 0 i 8 vNUMA na vSocket 1. Chociaż jest to asymetryczne, system operacyjny powinien przeprowadzić rozruch i działać normalnie. W dalszej części tego przewodnika poinstruujemy, jak najlepiej uruchamiać aplikacje MPI w tym niesymetrycznym układzie NUMA.

Przypinanie procesów będzie współpracować z maszynami wirtualnymi z serii HB, ponieważ udostępniamy podstawowy krzem jako-to-do maszyny wirtualnej gościa. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność.

Na poniższym diagramie przedstawiono rozdzielenie rdzeni zarezerwowanych dla funkcji hypervisor platformy Azure i maszyny wirtualnej z serii HB.

![Dzielenie rdzeni zarezerwowanych na maszynę wirtualną z serii HB](./media/architecture/hb-segregation-cores.png)

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Specyfikacje sprzętu                | Maszyna wirtualna z serii HB                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 60 (wyłączone: SMT)                |
| Procesor CPU                              | PROCESOR AMD EPYC 7551                    |
| Częstotliwość procesora (AVX)          | ~ 2,55 GHz (Single + wszystkie rdzenie)   |
| Pamięć                           | 4 GB/rdzeń (łącznie 240 GB)         |
| Dysk lokalny                       | DYSK SSD 700 GB                       |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 |
| Sieć                          | 50 GB sieci Ethernet (z 40 GB użytecznych) Azure Second gen SmartNIC |

## <a name="software-specifications"></a>Specyfikacje oprogramowania

| Specyfikacje oprogramowania           |Maszyna wirtualna z serii HB           |
|-----------------------------|-----------------------|
| Maksymalny rozmiar zadania MPI            | 18000 rdzeni (300 maszyny wirtualne w jednym zestawie skalowania maszyn wirtualnych z singlePlacementGroup = true)  |
| Obsługa MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| Dodatkowe struktury       | UCX, libfabric, PGAS |
| Obsługa usługi Azure Storage       | Dyski w warstwie Standardowa i Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla sterownik RDMA   | CentOS/RHEL 7.6 +, Ubuntu 16.04 +, SLES 12 SP4 +, WinServer 2016 +  |
| Wsparcie dla programu Orchestrator        | CycleCloud, Batch, AKS; [Opcje konfiguracji klastra](../../sizes-hpc.md#cluster-configuration-options) |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [architektury AMD EPYC](https://bit.ly/2Epv3kC) i [architektur wieloskładnikowych](https://bit.ly/2GpQIMb). Aby uzyskać bardziej szczegółowe informacje, zobacz [Przewodnik strojenia HPC dla procesorów AMD EPYC](https://bit.ly/2T3AWZ9).
- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
