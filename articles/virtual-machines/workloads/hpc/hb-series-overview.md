---
title: Informacje o maszynie wirtualnej z serii HB — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej na temat obsługi wersji zapoznawczej rozmiaru maszyny wirtualnej z serii HB na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1e37f13bc6e819079c63c583a1eeda12af5e213c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965463"
---
# <a name="hb-series-virtual-machines-overview"></a>Informacje o maszynach wirtualnych z serii HB

Maksymalizacja wydajności aplikacji obliczeniowych o wysokiej wydajności (HPC) na AMD EPYC wymaga, aby przydatnego wpływać na pamięć i proces umieszczania. Poniżej przedstawiono konspekt architektury AMD EPYC i naszej implementacji na platformie Azure dla aplikacji HPC. Termin "pNUMA" będzie używany do odwoływania się do fizycznej domeny NUMA i "vNUMA" w celu odwoływania się do zwirtualizowanej domeny NUMA.

Fizycznie serwer [serii HB](../../hb-series.md) to 2 * 32-core EPYC 7551 procesorów CPU, co łącznie 64 rdzenie fizyczne. Te rdzenie 64 są podzielone na 16 domen pNUMA (8 na gniazdo), z których każdy jest czterema rdzeniami i nazywany "złożonym PROCESORem" (lub "CCX"). Każdy CCX ma własną pamięć podręczną L3, co oznacza, że system operacyjny zobaczy granicę pNUMA/vNUMA. Para sąsiadujących CCXs umożliwia dostęp do dwóch kanałów fizycznej pamięci DRAM (32 GB pamięci DRAM w serwerach z serii HB).

Aby zapewnić obsługę funkcji hypervisor platformy Azure bez zakłócania działania maszyny wirtualnej, rezerwujemy fizyczną pNUMA domenę 0 (pierwszy CCX). Następnie przypiszemy domeny pNUMA 1-15 (pozostałe jednostki CCX) dla maszyny wirtualnej. Na maszynie wirtualnej zobaczysz:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` rdzenie na maszynę wirtualną

Sama maszyna wirtualna nie wie, że pNUMA 0 nie została do niej udzielona. Maszyna wirtualna rozumie pNUMA 1-15 jako vNUMA 0-14 z 7 vNUMA na vSocket 0 i 8 vNUMA na vSocket 1. Chociaż jest to asymetryczne, system operacyjny powinien przeprowadzić rozruch i działać normalnie. W dalszej części tego przewodnika poinstruujemy, jak najlepiej uruchamiać aplikacje MPI w tym niesymetrycznym układzie NUMA.

Przypinanie procesów będzie współpracować z maszynami wirtualnymi z serii HB, ponieważ udostępniamy podstawowy krzem jako-to-do maszyny wirtualnej gościa. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność.

Na poniższym diagramie przedstawiono rozdzielenie rdzeni zarezerwowanych dla funkcji hypervisor platformy Azure i maszyny wirtualnej z serii HB.

![Dzielenie rdzeni zarezerwowanych na maszynę wirtualną z serii HB](./media/hb-series-overview/segregation-cores.png)

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
| Dodatkowe struktury       | Ujednolicona komunikacja X, libfabric, PGAS |
| Obsługa usługi Azure Storage       | Dyski w warstwie Standardowa i Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla sterownik RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Wsparcie dla programu Orchestrator        | CycleCloud, Partia zadań  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [architektury AMD EPYC](https://bit.ly/2Epv3kC) i [architektur wieloskładnikowych](https://bit.ly/2GpQIMb). Aby uzyskać bardziej szczegółowe informacje, zobacz [Przewodnik strojenia HPC dla procesorów AMD EPYC](https://bit.ly/2T3AWZ9).
- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC oraz wyniki na [blogach społecznościowych usługi Azure COMPUTE](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
