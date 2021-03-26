---
title: Omówienie HBv3 maszyn wirtualnych z serii, architektura, topologia — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej o rozmiarze maszyny wirtualnej z serii HBv3 na platformie Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608288"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Omówienie maszyny wirtualnej z serii HBv3 

[HBv3 z seriami](../../hbv3-series.md) serwerów — funkcje 2 * 64-Core EPYC 7V13, aby uzyskać łącznie 128 fizycznych rdzeni "Zen3". Jednoczesne wielowątkowość (SMT) jest wyłączona w HBv3. Te rdzenie 128 są podzielone na 16 sekcji (8 na gniazdo), Każda sekcja zawierająca 8 rdzeni procesora z jednolitym dostępem do pamięci podręcznej o rozmiarze 32 MB. Na serwerach usługi Azure HBv3 są również uruchamiane następujące ustawienia systemu BIOS firmy AMD:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

W związku z tym serwer jest uruchamiany z 4 domenami NUMA (2 na gniazdo) każdego 32-rdzeni w rozmiarze. Każda architektura NUMA ma bezpośredni dostęp do 4 kanałów fizycznej pamięci DRAM z systemem 3200 MT/s.

Aby zapewnić możliwość działania funkcji hypervisor platformy Azure bez zakłócania pracy z maszyną wirtualną, firma Microsoft rezerwuje 8 rdzeni fizycznych na serwer.

## <a name="vm-topology"></a>Topologia maszyny wirtualnej

Na poniższym diagramie przedstawiono topologię serwera. Firma Microsoft rezerwuje te 8 rdzeni hosta funkcji hypervisor (żółtych) w obu gniazdach procesora CPU, pobierając pierwsze 2 rdzenie z określonych podstawowych złożonych CCDs w każdej domenie NUMA z pozostałymi rdzeniami dla maszyny wirtualnej serii HBv3 (zielony).

![Topologia serwera serii HBv3](./media/architecture/hbv3/hbv3-topology-server.png)

Należy zauważyć, że granica CCD nie jest równoważna z granicą NUMA. W systemie HBv3 grupa czterech kolejnych (4) CCDs jest konfigurowana jako domena NUMA, zarówno na poziomie serwera hosta, jak i w ramach maszyny wirtualnej gościa. W ten sposób wszystkie rozmiary maszyn wirtualnych HBv3 uwidaczniają 4 domeny NUMA, które będą widoczne dla systemu operacyjnego i aplikacji, jak pokazano poniżej, 4 jednolite domeny NUMA, z których każda ma różną liczbę rdzeni w zależności od określonego [rozmiaru maszyny wirtualnej HBv3](../../hbv3-series.md).

![Topologia maszyny wirtualnej z serii HBv3](./media/architecture/hbv3/hbv3-topology-vm.png)

Każdy rozmiar maszyny wirtualnej HBv3 jest podobny do układu fizycznego, funkcji i wydajności innego procesora niż seria AMD EPYC 7003, w następujący sposób:

| Rozmiar maszyny wirtualnej serii HBv3             | Domeny NUMA | Rdzenie na domenę NUMA  | Podobieństwo z procesorem AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Dual-Socket EPYC 7713            |
Standard_HB120r — 96s_v3            | 4            | 24                     | Dual-Socket EPYC 7643            |
Standard_HB120r — 64s_v3            | 4            | 16                     | Dual-Socket EPYC 7543            |
Standard_HB120r — 32s_v3            | 4            | 8                      | Dual-Socket EPYC 7313            |
Standard_HB120r — 16s_v3            | 4            | 4                      | Dual-Socket EPYC 72F3            |

> [!NOTE]
> Rozmiary maszyn wirtualnych z ograniczonymi rdzeniami zmniejszają tylko liczbę rdzeni fizycznych narażonych na maszynę wirtualną. Wszystkie globalne zasoby udostępnione (pamięć RAM, przepustowość pamięci, pamięć podręczna L3, GMI i xGMI łączność, InfiniBand, Sieć Ethernet platformy Azure, lokalny dysk SSD) pozostają stałe. Pozwala to klientowi na wybranie rozmiaru maszyny wirtualnej najlepiej dopasowanej do danego zestawu obciążeń lub wymagań dotyczących licencjonowania oprogramowania.

Wirtualne mapowanie NUMA każdego rozmiaru maszyny wirtualnej HBv3 jest zamapowane na podstawową topologię fizycznej architektury NUMA. Nie istnieje potencjalnie myląca Abstrakcja topologii sprzętowej. 

Dokładna topologia dla różnych [rozmiarów maszyn wirtualnych HBv3](../../hbv3-series.md) jest wyświetlana w następujący sposób przy użyciu danych wyjściowych [lstopo](https://linux.die.net/man/1/lstopo):
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Kliknij, aby wyświetlić dane wyjściowe lstopo dla Standard_HB120rs_v3</summary>

![lstopo dane wyjściowe dla maszyny wirtualnej HBv3-120](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Kliknij, aby wyświetlić dane wyjściowe lstopo dla Standard_HB120rs-96_v3</summary>

![lstopo dane wyjściowe dla maszyny wirtualnej HBv3-96](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Kliknij, aby wyświetlić dane wyjściowe lstopo dla Standard_HB120rs-64_v3</summary>

![lstopo dane wyjściowe dla maszyny wirtualnej HBv3-64](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Kliknij, aby wyświetlić dane wyjściowe lstopo dla Standard_HB120rs-32_v3</summary>

![lstopo dane wyjściowe dla maszyny wirtualnej HBv3-32](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Kliknij, aby wyświetlić dane wyjściowe lstopo dla Standard_HB120rs-16_v3</summary>

![lstopo dane wyjściowe dla maszyny wirtualnej HBv3-16](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

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
| Dodatkowe struktury          | UCX, libfabric, PGAS                  |
| Obsługa usługi Azure Storage          | Dyski w warstwie Standardowa i Premium (maksymalnie 32 dysków)              |
| Obsługa systemu operacyjnego dla sterownik RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Zalecany system operacyjny dla wydajności | CentOS 8,1, Windows Server 2019 +
| Wsparcie dla programu Orchestrator           | Azure CycleCloud, Azure Batch, AKS; [Opcje konfiguracji klastra](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> System Windows Server 2012 R2 nie jest obsługiwany na HBv3 i innych maszynach wirtualnych z ponad 64 (wirtualnym lub fizycznym) rdzeniami. Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
