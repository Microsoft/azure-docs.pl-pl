---
title: Konfiguracja i optymalizacja dla serii H z obsługą sekwencji i serii N Virtual Machines platformy Azure
description: Dowiedz się więcej o konfigurowaniu i optymalizowaniu maszyn wirtualnych z obsługą serii H i serii N dla HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0c6f5dc55f7406aba7d6e3dc1a278b57fe4ec9ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721276"
---
# <a name="configure-and-optimize-vms"></a>Konfigurowanie i optymalizowanie maszyn wirtualnych

W tym artykule udostępniono pewne wskazówki dotyczące konfigurowania i optymalizowania [maszyn wirtualnych z serii](../../sizes-gpu.md) [H](../../sizes-hpc.md) z obsługą InfiniBand dla HPC.

## <a name="vm-images"></a>Obrazy maszyn wirtualnych
Na maszynach wirtualnych z włączoną funkcją InfiniBand odpowiednie sterowniki są wymagane do włączenia funkcji RDMA.
- [Obrazy maszyn wirtualnych CentOS-HPC](#centos-hpc-vm-images) w portalu Marketplace są wstępnie skonfigurowane przy użyciu odpowiednich sterowników IB i są najprostszym sposobem na rozpoczęcie pracy.
- [Obrazy maszyn wirtualnych Ubuntu](#ubuntu-vm-images) można skonfigurować przy użyciu odpowiednich sterowników IB. Zaleca się utworzenie [niestandardowych obrazów maszyn wirtualnych](../../linux/tutorial-custom-images.md) z odpowiednimi sterownikami i konfiguracją oraz wielokrotne użycie.

Na maszynach wirtualnych z [serii N](../../sizes-gpu.md) z obsługą procesora GPU odpowiednie sterowniki procesora GPU są dodatkowo wymagane, które mogą być dodawane za pośrednictwem [rozszerzeń maszyn wirtualnych](../../extensions/hpccompute-gpu-linux.md) lub [ręcznie](../../linux/n-series-driver-setup.md). Niektóre obrazy maszyn wirtualnych w portalu Marketplace są również wstępnie zainstalowane przy użyciu sterowników NVIDIA GPU, w tym niektórych obrazów maszyn wirtualnych z firmy NVIDIA.

### <a name="centos-hpc-vm-images"></a>Obrazy maszyn wirtualnych CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>Maszyny wirtualne z obsługą funkcji SR-IOV
W przypadku funkcji SR-IOV z obsługą funkcji [RDMA](../../sizes-hpc.md#rdma-capable-instances), [obrazy maszyn wirtualnych CentOS-HPC w witrynie Marketplace w](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) wersji 7,6 i nowszych są odpowiednie. Te obrazy maszyn wirtualnych są zoptymalizowane i wstępnie załadowane za pomocą sterowników OFED dla funkcji RDMA oraz różnych powszechnie używanych bibliotek MPI i naukowych pakietów obliczeniowych. najłatwiej rozpocząć pracę.
- Szczegółowe informacje o tym, co zawiera CentOS-HPC w wersji 7,6 i nowszych, znajdują się w [artykule TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Skrypty używane podczas tworzenia obrazów maszyn wirtualnych CentOS-HPC w wersji 7,6 i nowszych z podstawowego obrazu portalu CentOS Marketplace znajdują się w [repozytorium azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
> [!NOTE] 
> Najnowsze obrazy z witryny Azure HPC Marketplace mają Mellanox OFED 5,1 i nowsze, które nie obsługują ConnectX3-Pro kart InfiniBand. Maszyny wirtualne z serii N obsługujące wirtualizację SR-IOV z systemem FDR InfiniBand (np. seria NCV3 i starsze) będą mogły korzystać z następujących obrazów maszyn wirtualnych CentOS-HPC lub starszych wersji z portalu Marketplace:
>- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
>- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
>- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
>- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
>- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
>- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Maszyny wirtualne z obsługą funkcji SR-IOV
W przypadku [maszyn wirtualnych obsługujących funkcję RDMA](../../sizes-hpc.md#rdma-capable-instances), które nie obsługują funkcji SR-IOV, CentOS-HPC w wersji 6,5 lub nowszej, do 7,4 w portalu Marketplace są odpowiednie. Na przykład w przypadku [maszyn wirtualnych z serii H16](../../h-series.md)zaleca się używanie wersji 7,1 do 7,4. Te obrazy maszyn wirtualnych są wstępnie załadowane za pomocą sterowników sieci bezpośrednio dla funkcji RDMA i Intel MPI w wersji 5,1.

> [!NOTE]
> W tych obrazach HPC opartych na CentOS dla maszyn wirtualnych z obsługą wirtualizacji SR-IOV aktualizacje jądra są wyłączone w pliku konfiguracyjnym **yum** . Wynika to z faktu, że sterowniki RDMA NetworkDirect systemu Linux są dystrybuowane jako pakiet KCO, a aktualizacje sterowników mogą nie zadziałać, jeśli jądro zostało zaktualizowane.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS obrazy maszyn wirtualnych
Maszyny wirtualne z obsługą funkcji SR-IOV obsługujące funkcję [RDMA](../../sizes-hpc.md#rdma-capable-instances)można skonfigurować pod kątem RHEL lub opartych na CentOS obrazów maszyn wirtualnych innych niż HPC w portalu Marketplace. Dowiedz się więcej na temat [włączania funkcji InfiniBand](enable-infiniband.md) i [konfigurowania MPI](setup-mpi.md) na maszynach wirtualnych.
- Można również użyć skryptów używanych podczas tworzenia CentOS-HPC w wersji 7,6 i nowszych obrazów maszyn wirtualnych z podstawowego obrazu portalu CentOS Marketplace z poziomu [repozytorium azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos) .
  
> [!NOTE]
> Karta Mellanox OFED 5,1 i nowsze nie obsługują ConnectX3-Pro kart InfiniBand na maszynach wirtualnych z serii N z obsługą wirtualizacji przy użyciu programu FDR InfiniBand (np. seria NCV3). Użyj LTS Mellanox OFED w wersji 4.9-0.1.7.0 lub starszej na maszynach wirtualnych serii N z kartami ConnectX3-Pro. Więcej szczegółów można znaleźć [tutaj](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Ubuntu obrazy maszyn wirtualnych
Ubuntu Server 16,04 LTS, 18,04 LTS i 20,04 LTS maszyn wirtualnych w portalu Marketplace są obsługiwane dla [maszyn wirtualnych z obsługą](../../sizes-hpc.md#rdma-capable-instances)wirtualizacji SR-IOV i innych niż SR-IOV RDMA. Dowiedz się więcej na temat [włączania funkcji InfiniBand](enable-infiniband.md) i [konfigurowania MPI](setup-mpi.md) na maszynach wirtualnych.
- Instrukcje dotyczące włączania usługi InfiniBand w obrazach maszyn wirtualnych Ubuntu znajdują się w [artykule TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Skrypty używane podczas tworzenia obrazów maszyn wirtualnych z systemem Ubuntu 18,04 i 20,04 LTS oparte na podstawowym Ubuntu Marketplace znajdują się w [repozytorium azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server obrazów maszyn wirtualnych
SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15 maszyn wirtualnych w portalu Marketplace są obsługiwane. Te obrazy maszyn wirtualnych są wstępnie załadowane za pomocą sterowników sieci bezpośrednio dla funkcji RDMA i Intel MPI w wersji 5,1. Dowiedz się więcej o [konfigurowaniu MPI](setup-mpi.md) na maszynach wirtualnych.

## <a name="optimize-vms"></a>Optymalizowanie maszyn wirtualnych

Poniżej przedstawiono niektóre opcjonalne ustawienia optymalizacji zapewniające lepszą wydajność na maszynie wirtualnej.

### <a name="update-lis"></a>Aktualizacja LIS

W razie potrzeby związanych z funkcjonalnością lub wydajnością [sterowniki systemu Linux Integration Services (LIS)](../../linux/endorsed-distros.md) można zainstalować lub zaktualizować w obsługiwanych dystrybucje OS, szczególnie w przypadku wdrażania przy użyciu niestandardowego obrazu lub starszej wersji systemu operacyjnego, takiej jak CentOS/RHEL 6. x lub wcześniejsza wersja 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Odzyskiwanie pamięci

Zwiększenie wydajności przez automatyczne ododzyskiwanie pamięci w celu uniknięcia dostępu do pamięci zdalnej.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Aby to zrobić, po ponownym uruchomieniu maszyny wirtualnej:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Wyłącz zaporę i SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Wyłącz cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Konfigurowanie WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Opcjonalnie WALinuxAgent można wyłączyć jako krok przedzadania i włączyć wykonywanie zadania końcowego dla maksymalnej dostępności zasobów maszyny wirtualnej dla obciążenia HPC.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [włączania funkcji InfiniBand](enable-infiniband.md) na maszynach wirtualnych z obsługą urządzeń z serii [H](../../sizes-hpc.md) i [serii N](../../sizes-gpu.md) .
- Dowiedz się więcej o instalowaniu różnych [obsługiwanych BIBLIOTEK MPI](setup-mpi.md) i ich optymalną konfigurację na maszynach wirtualnych.
- Zapoznaj się z [omówieniem HBv3](hbv3-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md).
- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
