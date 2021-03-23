---
title: Rozmiary maszyn wirtualnych platformy Azure — HPC | Microsoft Docs
description: Wyświetla listę różnych rozmiarów dostępnych w przypadku maszyn wirtualnych o wysokiej wydajności obliczeniowych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772437"
---
# <a name="high-performance-computing-vm-sizes"></a>Rozmiary maszyn wirtualnych o wysokiej wydajności obliczeniowej

Maszyny wirtualne z serii H platformy Azure zaprojektowano w celu zapewnienia wydajności, skalowalności i efektywności klasy lidera dla różnych rzeczywistych obciążeń HPC.

[Seria HBv3](hbv3-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji HPC, takich jak dynamika płynów, jawne i niejawne ograniczone analizy elementów, modelowanie pogody, przetwarzanie sejsmiczne, Symulacja zbiornika i symulacja RTL. HBv3 maszyny wirtualne mają do 120 rdzenie procesora AMD EPYC™ 7003 serii (Mediolan), 448 GB pamięci RAM i bez wielowątkowości. Maszyny wirtualne z serii HBv3 zapewniają również 350 GB/s przepustowości pamięci, do 32 MB pamięci podręcznej L3 na rdzeń, do 7 GB/s z urządzenia blokowego wydajności dysków SSD i częstotliwości zegara do 3,675 GHz. 

Wszystkie maszyny wirtualne z serii HBv3 są wyposażone w funkcję 200 GB/s w formacie HDR InfiniBand z sieci NVIDIA w celu włączenia obciążeń MPI w skali komputera. Te maszyny wirtualne są połączone w nieblokującym drzewie FAT na potrzeby zoptymalizowanej i spójnej wydajności RDMA. Sieć szkieletowa HDR InfiniBand obsługuje również adaptacyjne Routing i transport podłączony dynamicznie (DCT), a także transporty standardowe RC i UD. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

[Seria HBv2](hbv2-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, skończona analiza elementów i symulacja zbiornika. HBv2 maszyny wirtualne z funkcją 120 AMD EPYC 7742 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Każda maszyna wirtualna w HBv2 zapewnia do 340 GB/s przepustowości pamięci oraz do 4 teraflopa obliczeń FP64.

Funkcja maszyn wirtualnych HBv2 200 GB/s — karta Mellanox HDR InfiniBand, podczas gdy funkcja maszyn wirtualnych serii HB i HC 100 GB/s Mellanox EDR InfiniBand. Każdy z tych typów maszyn wirtualnych jest połączony w nieblokującym drzewie FAT na potrzeby zoptymalizowanej i spójnej wydajności RDMA. Maszyny wirtualne HBv2 obsługują Routing adaptacyjny i transport podłączony dynamicznie (DCT), a także standardowe transporty w wersji RC i UD. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

[HB — seria](hb-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, jawna, skończona analiza elementów i modelowanie pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma AMD EPYC zapewnia ponad 260 GB/s przepustowości pamięci.

[Seria HC](hc-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na gęstym obliczaniu, takich jak niejawne ograniczone analizy elementów, biocząsteczkowa i obliczeniowa. Funkcja maszyn wirtualnych HC 44 rdzeni procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma Intel Xeon Platinum obsługuje bogate ekosystemy narzędzi programistycznych firmy Intel, takich jak biblioteka jądra matematycznych firmy Intel.

[Seria H](h-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji z wysoką częstotliwością procesora CPU lub dużą ilością pamięci na podstawowe wymagania. Maszyny wirtualne serii H funkcja 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, 7 lub 14 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Funkcja serii H 56 GB/s karta Mellanox FDR InfiniBand w nieblokującej konfiguracji drzewa FAT dla spójnej wydajności RDMA. Maszyny wirtualne z serii H obsługują technologię Intel MPI 5. x i MS-MPI.

> [!NOTE]
> Wszystkie maszyny wirtualne z serii HBv3, HBv2, HB i HC mają wyłączny dostęp do serwerów fizycznych. Na jednym serwerze fizycznym jest tylko 1 maszyna wirtualna i nie ma żadnej udostępnionej wielu dzierżawców z innymi maszynami wirtualnymi dla tych rozmiarów maszyn wirtualnych.

> [!NOTE]
> [Maszyny wirtualne A8 – A11](./sizes-previous-gen.md#a-series---compute-intensive-instances) są wycofywane z 3/2021. Obecnie nie są dostępne żadne nowe wdrożenia maszyn wirtualnych o tych rozmiarach. Jeśli masz istniejące maszyny wirtualne, zapoznaj się z wiadomościami e-mail dotyczącymi następnych kroków, takich jak migracja do innych rozmiarów maszyn wirtualnych w [przewodniku migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA

Większość rozmiarów maszyn wirtualnych HPC korzysta z interfejsu sieciowego do łączności z funkcją zdalnego bezpośredniego dostępu do pamięci (RDMA). Wybrane rozmiary [serii N](./nc-series.md) oznaczone przy użyciu "r" obsługują również funkcję RDMA. Ten interfejs jest poza standardowym interfejsem sieciowym sieci Ethernet platformy Azure dostępnym w innych rozmiarach maszyn wirtualnych.

Ten interfejs pomocniczy umożliwia wystąpieniem funkcji RDMA komunikowanie się za pośrednictwem sieci InfiniBand (IB), który działa według stawek za HBv3, HBv2, EDR dla liczby HB, HC, NDv2 i FDR dla H16r, H16mr i innych maszyn wirtualnych z serii N z obsługą funkcji RDMA. Te możliwości RDMA umożliwiają zwiększenie skalowalności i wydajności aplikacji opartych na interfejsie MPI (Message Passing Interface).

> [!NOTE]
> **Obsługa wirtualizacji SR-IOV**: w systemie Azure HPC obecnie istnieją dwie klasy maszyn wirtualnych w zależności od tego, czy są włączone Wirtualizacja SR-IOV dla InfiniBand. Obecnie niemal wszystkie nowsze maszyny wirtualne z obsługą funkcji RDMA lub InfiniBand na platformie Azure obsługują funkcję SR-IOV z wyjątkiem H16r, H16mr i NC24r.
> Funkcja RDMA jest włączona tylko przez sieć InfiniBand (IB) i jest obsługiwana dla wszystkich maszyn wirtualnych z obsługą funkcji RDMA.
> Protokół IP over IB jest obsługiwany tylko na maszynach wirtualnych z włączoną funkcją SR-IOV.
> Funkcja RDMA nie jest włączona w sieci Ethernet.

- **System operacyjny** — dystrybucje systemu Linux, takie jak CentOS, RHEL, Ubuntu, SUSE są często używane. System Windows Server 2016 i nowsze wersje są obsługiwane we wszystkich maszynach wirtualnych z serii HPC. Systemy Windows Server 2012 R2 i Windows Server 2012 są również obsługiwane na maszynach wirtualnych z obsługą wirtualizacji SR-IOV. Należy pamiętać, że [system Windows Server 2012 R2 nie jest obsługiwany w przypadku maszyn wirtualnych o rozmiarze przekraczającym 64 (wirtualnym lub fizycznym)](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Zobacz [obrazy maszyn wirtualnych](./workloads/hpc/configure.md) , aby zapoznać się z listą obsługiwanych obrazów maszyn wirtualnych w witrynie Marketplace oraz jak można odpowiednio je skonfigurować. Na stronach o odpowiednim rozmiarze maszyny wirtualnej znajduje się również lista obsługi stosu oprogramowania.

- **InfiniBand i sterowniki** — na maszynach wirtualnych z włączoną funkcją InfiniBand wymagane są odpowiednie sterowniki, aby włączyć funkcję RDMA. Zobacz [obrazy maszyn wirtualnych](./workloads/hpc/configure.md) , aby zapoznać się z listą obsługiwanych obrazów maszyn wirtualnych w witrynie Marketplace oraz jak można odpowiednio je skonfigurować. Zobacz też temat [Włączanie sterownika InfiniBand](./workloads/hpc/enable-infiniband.md) , aby dowiedzieć się więcej o ROZSZERZENIACH maszyn wirtualnych lub ręcznej instalacji sterowników InfiniBand.

- **MPI** — rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure zezwalają niemal dowolnym wersja MPI do użycia z Mellanox OFED. W przypadku maszyn wirtualnych z obsługą funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikacji między maszynami wirtualnymi. W związku z tym obsługiwane są tylko procesory Intel MPI 5. x i Microsoft MPI (MS-MPI) 2012 R2 lub nowsze. Nowsze wersje biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami usługi Azure RDMA. Zobacz [Setup MPI for HPC](./workloads/hpc/setup-mpi.md) , aby uzyskać więcej informacji na temat konfigurowania MPI na maszynach wirtualnych HPC na platformie Azure.

  > [!NOTE]
  > **Przestrzeń adresów sieciowych RDMA**: sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchamiać aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.

## <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Platforma Azure oferuje kilka opcji tworzenia klastrów maszyn wirtualnych HPC, które mogą komunikować się za pomocą sieci RDMA, w tym: 

- **Maszyny wirtualne**  — Wdróż maszyny wirtualne z obsługą funkcji RDMA w tym samym zestawie skalowania lub zestawie dostępności (w przypadku korzystania z Azure Resource Manager modelu wdrażania). W przypadku korzystania z klasycznego modelu wdrażania należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze.

- **Zestawy skalowania maszyn wirtualnych** — w zestawie skalowania maszyn wirtualnych upewnij się, że wdrożenie jest ograniczone do pojedynczej grupy umieszczania na potrzeby komunikacji InfiniBand w ramach zestawu skalowania. Na przykład w szablonie Menedżer zasobów Ustaw `singlePlacementGroup` Właściwość na `true` . Należy zauważyć, że maksymalny rozmiar zestawu skalowania, który może być przystosowany do programu, `singlePlacementGroup=true` jest domyślnie ustawiony na maszyny wirtualne 100. Jeśli Twoje wymagania dotyczące skalowania zadań HPC są większe niż 100 maszyn wirtualnych w ramach jednej dzierżawy, możesz poprosić o zwiększenie, a bezpłatnie [otworzyć żądanie pomocy technicznej online](../azure-portal/supportability/how-to-create-azure-support-request.md) . Limit liczby maszyn wirtualnych w pojedynczym zestawie skalowania można zwiększyć do 300. Należy pamiętać, że podczas wdrażania maszyn wirtualnych przy użyciu zestawów dostępności maksymalny limit wynosi 200 maszyn wirtualnych na zestaw dostępności.

  > [!NOTE]
  > **MPI między maszynami wirtualnymi**: Jeśli wymagana jest funkcja RDMA (np. Używanie komunikacji MPI) między maszynami wirtualnymi, upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie skalowania maszyn wirtualnych lub zestawie dostępności.

- **Azure CycleCloud** — Tworzenie klastra HPC przy użyciu [usługi Azure CycleCloud](/azure/cyclecloud/) do uruchamiania zadań MPI.

- **Azure Batch** — utwórz pulę [Azure Batch](../batch/index.yml) do uruchamiania obciążeń MPI. Aby korzystać z wystąpień intensywnie korzystających z obliczeń podczas uruchamiania aplikacji MPI przy użyciu Azure Batch, zobacz temat [Używanie zadań z wielu wystąpień w celu uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w programie Azure Batch](../batch/batch-mpi.md).

- **Pakiet Microsoft HPC Pack**  -  [Pakiet HPC Pack](/powershell/high-performance-computing/overview) zawiera środowisko uruchomieniowe dla MS-MPI, które używa sieci usługi Azure RDMA w przypadku wdrożenia na maszynach wirtualnych z systemem Linux obsługujących funkcję RDMA. Na przykład wdrożenia, zobacz [Konfigurowanie klastra RDMA systemu Linux przy użyciu pakietu HPC Pack w celu uruchamiania aplikacji MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- **Subskrypcja platformy Azure** — aby wdrożyć więcej niż kilka wystąpień intensywnie korzystających z obliczeń, weź pod uwagę subskrypcję z płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- **Cennik i dostępność** — sprawdzanie cen i [dostępności](https://azure.microsoft.com/global-infrastructure/services/) [maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) według regionów świadczenia usługi Azure.

- **Przydział rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni w ramach subskrypcji platformy Azure z wartości domyślnej. Twoja subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym dla serii H. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-portal/supportability/how-to-create-azure-support-request.md) . (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).

  > [!NOTE]
  > Skontaktuj się z pomocą techniczną platformy Azure, jeśli potrzebujesz dużej pojemności. Przydziały platformy Azure to limity kredytowe, a nie gwarancje wydajności. Niezależnie od limitu przydziału opłata jest naliczana tylko za używane rdzenie.
  
- **Sieć wirtualna** — [Sieć wirtualna](https://azure.microsoft.com/documentation/services/virtual-network/) platformy Azure nie jest wymagana do korzystania z wystąpień intensywnie korzystających z obliczeń. Jednak w przypadku wielu wdrożeń potrzebna jest co najmniej oparta na chmurze usługa Azure Virtual Network lub połączenie lokacja-lokacja, jeśli trzeba uzyskać dostęp do zasobów lokalnych. W razie potrzeby utwórz nową sieć wirtualną, aby wdrożyć wystąpienia. Dodawanie maszyn wirtualnych intensywnie korzystających z obliczeń do sieci wirtualnej w grupie koligacji nie jest obsługiwane.

- Zmiana **rozmiaru** — ze względu na wyspecjalizowany sprzęt można zmienić tylko wystąpienia intensywnie korzystające z mocy obliczeniowej w tej samej rodzinie wielkości (seria H lub seria N). Na przykład można zmienić rozmiar maszyny wirtualnej serii H tylko z jednego rozmiaru serii H na inny. Dodatkowe zagadnienia dotyczące obsługi sterowników InfiniBand i dysków interfejsu NVMe mogą być brane pod uwagę w przypadku niektórych maszyn wirtualnych.


## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [konfigurowaniu maszyn wirtualnych](./workloads/hpc/configure.md), [włączeniu funkcji InfiniBand](./workloads/hpc/enable-infiniband.md), [konfigurowaniu MPI](./workloads/hpc/setup-mpi.md) i optymalizowaniu aplikacji HPC dla platformy Azure w ramach [obciążeń HPC](./workloads/hpc/overview.md).
- Zapoznaj się z [omówieniem HBv3](./workloads/hpc/hbv3-series-overview.md) i [omówieniem z serii HC](./workloads/hpc/hc-series-overview.md).
- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
