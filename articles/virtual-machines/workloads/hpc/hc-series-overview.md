---
title: Omówienie maszyn wirtualnych z serii HC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej o obsłudze wersji zapoznawczej rozmiaru maszyny wirtualnej z serii HC na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906745"
---
# <a name="hc-series-virtual-machine-overview"></a>Omówienie maszyny wirtualnej z serii HC

Maksymalizacja wydajności aplikacji HPC na skalowalnych procesorach Intel Xeon wymaga podejścia przydatnego do przetwarzania umieszczania w tej nowej architekturze. Tutaj zaplanujemy nasze wdrożenie na maszynach wirtualnych z serii HC platformy Azure dla aplikacji HPC. Termin "pNUMA" będzie używany do odwoływania się do fizycznej domeny NUMA i "vNUMA" w celu odwoływania się do zwirtualizowanej domeny NUMA. Podobnie będziemy używać terminu "pCore" do odwoływania się do fizycznych rdzeni procesora CPU i "rdzeń wirtualny" w celu odwoływania się do zwirtualizowanych rdzeni procesora.

Fizycznie serwer z technologią HC to 2 * 24 rdzenie procesorów Intel Xeon Platinum 8168 dla ogółu 48 rdzeni fizycznych. Każdy procesor CPU jest jedną domeną pNUMA i ma ujednolicony dostęp do sześciu kanałów pamięci DRAM. Procesory Intel Xeon Platinum są wyposażone w większą pamięć podręczną L2 niż w poprzednich generacjach (256 KB/rdzeni-> 1 MB/rdzeń), a jednocześnie zmniejszają pamięć podręczną L3 w porównaniu z wcześniejszymi procesorami Intel (2,5 MB/rdzeni-> 1,375 MB/rdzeni).

Powyższa topologia również przeprowadzi do konfiguracji funkcji hypervisor serii HC. Aby zapewnić możliwość działania funkcji hypervisor platformy Azure bez zakłócania pracy maszyny wirtualnej, firma Microsoft zastrzega sobie pCores 0-1 i 24-25 (czyli pierwsze 2 pCores w każdym gnieździe). Następnie przypiszemy domeny pNUMA wszystkie pozostałe rdzenie do maszyny wirtualnej. W rezultacie maszyna wirtualna zobaczy:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`rdzenie na maszynę wirtualną

Maszyna wirtualna nie ma informacji o tym, że pCores 0-1 i 24-25 nie zostały do niego przekazane. W tym celu uwidacznia każdy vNUMA tak, jakby miał natywne 22 rdzenie.

Procesory Intel Xeon Platinum, Gold i Silver również wprowadzają sieć siatkową 2D do komunikacji w ramach i na zewnątrz gniazda procesora CPU. Zdecydowanie zalecamy Przypinanie procesów, aby zapewnić optymalną wydajność i spójność. Przypinanie procesów będzie działało na maszynach wirtualnych z serii HC, ponieważ podstawowy krzem jest narażony na maszynę wirtualną gościa. Aby dowiedzieć się więcej, zobacz [Architektura technologii Intel Xeon Sp](https://bit.ly/2RCYkiE).

Na poniższym diagramie przedstawiono rozdzielenie rdzeni zarezerwowanych dla funkcji hypervisor platformy Azure i maszyny wirtualnej z serii HC.

![Podział rdzeni zarezerwowanych na maszyny wirtualne platformy Azure i maszyn wirtualnych z serii HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Specyfikacje sprzętu          | Maszyna wirtualna z serii HC                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 44 (HT wyłączone)                 |
| Procesor CPU                              | Intel Xeon Platinum 8168 *        |
| Częstotliwość procesora (AVX)          | 3,7 GHz (pojedyncze rdzeń), 2.7-3.4 GHz (wszystkie rdzenie) |
| Memory (Pamięć)                           | 8 GB/rdzeń (łącznie 352)            |
| Dysk lokalny                       | Interfejsu NVMe 700 GB                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Sieć                          | 50 GB sieci Ethernet (z 40 GB użytecznych) Azure Second gen SmartNIC * * * |

## <a name="software-specifications"></a>Specyfikacje oprogramowania

| Specyfikacje oprogramowania     | Maszyna wirtualna z serii HC          |
|-----------------------------|-----------------------|
| Maksymalny rozmiar zadania MPI            | 13200 rdzeni (300 maszyn wirtualnych w jednym VMSS z singlePlacementGroup = true) |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, platform MPI, Intel MPI  |
| Dodatkowe struktury       | Ujednolicona komunikacja X, libfabric, PGAS |
| Obsługa usługi Azure Storage       | STD + Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla sterownik RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Pomoc techniczna platformy Azure CycleCloud    | Tak                         |
| Obsługa Azure Batch         | Tak                         |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o rozmiarach maszyn wirtualnych HPC dla systemów [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
