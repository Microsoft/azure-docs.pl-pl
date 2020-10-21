---
title: Omówienie maszyn wirtualnych z serii HBv2 — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej o rozmiarze maszyny wirtualnej z serii HBv2 na platformie Azure.
services: virtual-machines
author: vermagit
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 48366f205ed8eb2d179bdc39c8da3d673f066a69
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332640"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Omówienie maszyny wirtualnej z serii HBv2 

 
Maksymalizacja wydajności aplikacji obliczeniowych o wysokiej wydajności (HPC) na AMD EPYC wymaga, aby przydatnego wpływać na pamięć i proces umieszczania. Poniżej przedstawiono konspekt architektury AMD EPYC i naszej implementacji na platformie Azure dla aplikacji HPC. Termin **pNUMA** będzie używany do odwoływania się do fizycznej domeny NUMA i **vNUMA** do odwoływania się do zwirtualizowanej domeny NUMA. 

Fizycznie serwer [serii HBv2](../../hbv2-series.md) to 2 * 64-core EPYC 7742 procesorów CPU, co łącznie 128 rdzenie fizyczne. Te rdzenie 128 są podzielone na domeny pNUMA 32 (16 na gniazdo), z których każdy ma 4 rdzenie i jest oznaczany przez AMD jako **rdzeń podstawowy** (lub **CCX**). Każdy CCX ma własną pamięć podręczną L3, co oznacza, że system operacyjny zobaczy granicę pNUMA/vNUMA. Cztery sąsiednie CCXs współdzielą dostęp do dwóch kanałów fizycznej pamięci DRAM. 

Aby zapewnić obsługę funkcji hypervisor platformy Azure bez zakłócania działania maszyny wirtualnej, rezerwujemy fizyczne domeny pNUMA 0 i 16 (tj. pierwszy CCX każdego gniazda CPU). Wszystkie pozostałe 30 domen pNUMA są przypisywane do maszyny wirtualnej, w której stanie się vNUMA. W rezultacie maszyna wirtualna zobaczy:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` rdzenie na maszynę wirtualną 

Sama maszyna wirtualna nie ma świadomości, że pNUMA 0 i 16 są zarezerwowane. Wylicza vNUMA uważany za 0-29 z 15 vNUMA na gniazdo symetryczne, vNUMA 0-14 w vSocket 0 i vNUMA 15-29 on vSocket 1. W następnej sekcji znajdują się instrukcje, jak najlepiej uruchamiać aplikacje MPI w tym niesymetrycznym układzie NUMA. 

Przypinanie procesów będzie współpracować z maszynami wirtualnymi z serii HBv2, ponieważ udostępnimy podstawowy krzem jako-to-do maszyny wirtualnej gościa. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność. 


## <a name="hardware-specifications"></a>Specyfikacje sprzętowe 

| Specyfikacje sprzętu          | Maszyna wirtualna z serii HBv2                   | 
|----------------------------------|----------------------------------|
| Rdzenie                            | 120 (wyłączone: SMT)               | 
| Procesor CPU                              | PROCESOR AMD EPYC 7742                    | 
| Częstotliwość procesora (AVX)          | ~ 3,1 GHz (Single + wszystkie rdzenie)    | 
| Pamięć                           | 4 GB/rdzeń (łącznie 480 GB)         | 
| Dysk lokalny                       | 960 GB interfejsu NVMe (blok), dysk SSD 480 GB (plik stronicowania) | 
| InfiniBand                       | 200 GB/s EDR Mellanox ConnectX-6 | 
| Sieć                          | 50 GB/s Ethernet (możliwe do użycia 40 GB/s) Azure Second gen SmartNIC | 


## <a name="software-specifications"></a>Specyfikacje oprogramowania 

| Specyfikacje oprogramowania     | Maszyna wirtualna z serii HBv2                                            | 
|-----------------------------|-----------------------------------------------------------|
| Maksymalny rozmiar zadania MPI            | 36000 rdzeni (300 maszyny wirtualne w jednym zestawie skalowania maszyn wirtualnych z singlePlacementGroup = true) |
| Obsługa MPI                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| Dodatkowe struktury       | Ujednolicona komunikacja X, libfabric, PGAS                  |
| Obsługa usługi Azure Storage       | Dyski w warstwie Standardowa i Premium (maksymalnie 8 dysków)              |
| Obsługa systemu operacyjnego dla sterownik RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Wsparcie dla programu Orchestrator        | CycleCloud, Partia zadań                                         | 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [architektury AMD EPYC](https://bit.ly/2Epv3kC) i [architektur wieloskładnikowych](https://bit.ly/2GpQIMb). Aby uzyskać bardziej szczegółowe informacje, zobacz [Przewodnik strojenia HPC dla procesorów AMD EPYC](https://bit.ly/2T3AWZ9).
- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC na [blogach społecznościowych obliczeń na platformie Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).