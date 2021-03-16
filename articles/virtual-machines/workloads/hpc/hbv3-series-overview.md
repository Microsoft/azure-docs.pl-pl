---
title: Omówienie maszyn wirtualnych z serii HBv3 — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej o rozmiarze maszyny wirtualnej z serii HBv3 na platformie Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 94e4590c66cdee74555611302f4f9228a755c76a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472899"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Omówienie maszyny wirtualnej z serii HBv3 

[HBv3 z seriami](../../hbv3-series.md) serwerów — funkcje 2 * 64-Core EPYC 7V13, aby uzyskać łącznie 128 fizycznych rdzeni "Zen3". Jednoczesne wielowątkowość (SMT) jest wyłączona w HBv3. Te rdzenie 128 są podzielone na 16 sekcji (8 na gniazdo), Każda sekcja zawierająca 8 rdzeni procesora z jednolitym dostępem do pamięci podręcznej o rozmiarze 32 MB. Na serwerach usługi Azure HBv3 są również uruchamiane następujące ustawienia systemu BIOS firmy AMD:

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

W związku z tym serwer jest uruchamiany z 4 domenami NUMA (2 na gniazdo) każdego 32-rdzeni w rozmiarze. Każda architektura NUMA ma bezpośredni dostęp do 4 kanałów fizycznej pamięci DRAM z systemem 3200 MT/s.

Aby zapewnić możliwość działania funkcji hypervisor platformy Azure bez zakłócania pracy z maszyną wirtualną, firma Microsoft rezerwuje 8 rdzeni fizycznych na serwer. 

Należy pamiętać, że rozmiary maszyn wirtualnych z ograniczoną ilością zmniejszają tylko liczbę rdzeni fizycznych narażonych na maszynę wirtualną. Wszystkie globalne zasoby udostępnione (pamięć RAM, przepustowość pamięci, pamięć podręczna L3, GMI i xGMI łączność, InfiniBand, Sieć Ethernet platformy Azure, lokalny dysk SSD) pozostają stałe. Pozwala to klientowi na wybranie rozmiaru maszyny wirtualnej najlepiej dopasowanej do danego zestawu obciążeń lub wymagań dotyczących licencjonowania oprogramowania.

## <a name="infiniband-networking"></a>Sieci InfiniBand
Maszyny wirtualne HBv3 są również wyposażone w karty sieciowe NVIDIA Mellanox HDR InfiniBand (ConnectX-6) działające do 200 GB/s. Karta sieciowa jest przenoszona do maszyny wirtualnej za pośrednictwem sterownik, umożliwiając ruch sieciowy w celu obejścia funkcji hypervisor. W związku z tym klienci ładują standardowe sterowniki Mellanox OFED na maszynach wirtualnych HBv3 w miarę jak środowisko systemu operacyjnego.

Maszyny wirtualne HBv3 obsługują Routing adaptacyjny, transport połączony dynamicznie (DCT, w przypadku dodatkowych do standardowych wersji RC i UD Transports) oraz sprzętowy odciążanie MPI zbiory do procesora Onboard karty ConnectX-6. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

## <a name="temporary-storage"></a>Magazyn tymczasowy
Maszyny wirtualne HBv3 z funkcją 3 fizycznie lokalnych urządzeń SSD. Jedno urządzenie jest sformatowane jako plik stronicowania i będzie wyświetlane w ramach maszyny wirtualnej jako ogólne urządzenie "SSD".

Dwie inne, większe dysków SSD są udostępniane jako niesformatowane, blokowe urządzenia interfejsu NVMe za pośrednictwem NVMeDirect. Ponieważ blokowe urządzenie interfejsu NVMe pomija funkcję hypervisor, będzie miała wyższą przepustowość, wyższą liczbę IOPS i małe opóźnienia na redukcja.

W przypadku sparowania w macierzy warstwowej dysk SSD interfejsu NVMe zapewnia do 7 GB/s odczyty i 3 GB/s, a nawet 186 000 operacji we/wy (odczyty) oraz 201 000 IOPS (zapisy) dla głębokości kolejki głębokiej.

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe 

| Specyfikacje sprzętowe          | Maszyny wirtualne z serii HBv3              |
|----------------------------------|----------------------------------|
| Rdzenie                            | 120, 96, 64, 32 lub 16 (wyłączone: SMT)               | 
| Procesor CPU                              | 7V13 AMD EPYC                   | 
| Częstotliwość procesora (AVX)          | 3,1 GHz (wszystkie rdzenie), 3,675 GHz (do 10 rdzeni)    | 
| Pamięć                           | 448 GB (pamięć RAM na rdzeń zależy od rozmiaru maszyny wirtualnej)         | 
| Dysk lokalny                       | 2 * 960 GB interfejsu NVMe (blok), dysk SSD 480 GB (plik stronicowania) | 
| InfiniBand                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Sieć                          | 50 GB/s Ethernet (możliwe do użycia 40 GB/s) Azure Second gen SmartNIC | 

## <a name="software-specifications"></a>Specyfikacje oprogramowania 

| Specyfikacje oprogramowania        | Maszyny wirtualne z serii HBv3                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maksymalny rozmiar zadania MPI               | 36 000 rdzeni (300 maszyny wirtualne w jednym zestawie skalowania maszyn wirtualnych z singlePlacementGroup = true) |
| Obsługa MPI                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Dodatkowe struktury          | Ujednolicona komunikacja X, libfabric, PGAS                  |
| Obsługa usługi Azure Storage          | Dyski w warstwie Standardowa i Premium (maksymalnie 32 dysków)              |
| Obsługa systemu operacyjnego dla sterownik RDMA      | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Zalecany system operacyjny dla wydajności | CentOS 8,1, Windows Server 2019 +
| Wsparcie dla programu Orchestrator           | Azure CycleCloud, Azure Batch, Azure Kubernetes Service                      | 

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC na [blogach społecznościowych obliczeń na platformie Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).