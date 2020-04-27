---
title: Informacje o maszynie wirtualnej z serii HB — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej na temat obsługi wersji zapoznawczej rozmiaru maszyny wirtualnej z serii HB na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707771"
---
# <a name="hb-series-virtual-machines-overview"></a>Informacje o maszynach wirtualnych z serii HB

Maksymalizacja wydajności aplikacji obliczeniowych o wysokiej wydajności (HPC) na AMD EPYC wymaga, aby przydatnego wpływać na pamięć i proces umieszczania. Poniżej przedstawiono konspekt architektury AMD EPYC i naszej implementacji na platformie Azure dla aplikacji HPC. Termin "pNUMA" będzie używany do odwoływania się do fizycznej domeny NUMA i "vNUMA" w celu odwoływania się do zwirtualizowanej domeny NUMA.

Fizycznie Seria HB to 2 * 32-Core EPYC 7551 procesorów CPU, co łącznie 64 rdzenie fizyczne. Te rdzenie 64 są podzielone na 16 domen pNUMA (8 na gniazdo), z których każdy jest czterema rdzeniami i nazywany "złożonym PROCESORem" (lub "CCX"). Każdy CCX ma własną pamięć podręczną L3, co oznacza, że system operacyjny zobaczy granicę pNUMA/vNUMA. Para sąsiadujących CCXs umożliwia dostęp do dwóch kanałów fizycznej pamięci DRAM (32 GB pamięci DRAM w serwerach z serii HB).

Aby zapewnić obsługę funkcji hypervisor platformy Azure bez zakłócania działania maszyny wirtualnej, rezerwujemy fizyczną pNUMA domenę 0 (pierwszy CCX). Następnie przypiszemy domeny pNUMA 1-15 (pozostałe jednostki CCX) dla maszyny wirtualnej. Na maszynie wirtualnej zobaczysz:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`rdzenie na maszynę wirtualną

Sama maszyna wirtualna nie wie, że pNUMA 0 nie została do niej udzielona. Maszyna wirtualna rozumie pNUMA 1-15 jako vNUMA 0-14 z 7 vNUMA na vSocket 0 i 8 vNUMA na vSocket 1. Chociaż jest to asymetryczne, system operacyjny powinien przeprowadzić rozruch i działać normalnie. W dalszej części tego przewodnika poinstruujemy, jak najlepiej uruchamiać aplikacje MPI w tym niesymetrycznym układzie NUMA.

Przypinanie procesów będzie współpracować z maszynami wirtualnymi z serii HB, ponieważ udostępniamy podstawowy krzem jako-to-do maszyny wirtualnej gościa. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność.

Zobacz więcej na temat [architektury AMD EPYC](https://bit.ly/2Epv3kC) i [architektury wieloskładnikowych](https://bit.ly/2GpQIMb) w serwisie LinkedIn. Aby uzyskać bardziej szczegółowe informacje, zobacz [Przewodnik strojenia HPC dla procesorów AMD EPYC](https://bit.ly/2T3AWZ9).

Na poniższym diagramie przedstawiono rozdzielenie rdzeni zarezerwowanych dla funkcji hypervisor platformy Azure i maszyny wirtualnej z serii HB.

![Dzielenie rdzeni zarezerwowanych na maszynę wirtualną z serii HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Specyfikacje sprzętu                | Maszyna wirtualna z serii HB                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 60 (wyłączone: SMT)                |
| Procesor CPU                              | AMD EPYC 7551 *                   |
| Częstotliwość procesora (AVX)          | ~ 2,55 GHz (Single + wszystkie rdzenie)   |
| Memory (Pamięć)                           | 4 GB/rdzeń (łącznie 240)            |
| Dysk lokalny                       | Interfejsu NVMe 700 GB                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Sieć                          | 50 GB sieci Ethernet (z 40 GB użytecznych) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Specyfikacje oprogramowania

| Specyfikacje oprogramowania SW           |Maszyna wirtualna z serii HB           |
|-----------------------------|-----------------------|
| Maksymalny rozmiar zadania MPI            | rdzenie 6000 (100 zestawów skalowania maszyn wirtualnych) 12000 rdzenie (200 zestawy skalowania maszyn wirtualnych)  |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, platform MPI, Intel MPI  |
| Dodatkowe struktury       | Ujednolicona komunikacja X, libfabric, PGAS |
| Obsługa usługi Azure Storage       | STD + Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla sterownik RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Pomoc techniczna platformy Azure CycleCloud    | Yes                         |
| Obsługa Azure Batch         | Yes                         |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o rozmiarach maszyn wirtualnych HPC dla systemów [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
