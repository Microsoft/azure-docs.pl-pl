---
title: Konfiguracja i optymalizacja urządzeń z serii H i N z włączoną obsługą technologii InfiniBand na platformie Azure Virtual Machines
description: Dowiedz się więcej na temat konfigurowania i optymalizowania maszyn wirtualnych serii H i N z włączoną obsługą technologii InfiniBand dla hpc.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600883"
---
# <a name="configure-and-optimize-vms"></a>Konfigurowanie i optymalizowanie maszyn wirtualnych

Ten artykuł zawiera wskazówki dotyczące konfigurowania i optymalizowania maszyn wirtualnych serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z włączoną obsługą technologii InfiniBand do pracy z rozwiązaniami HPC.

## <a name="vm-images"></a>Obrazy maszyn wirtualnych
W przypadku maszyn wirtualnych z włączoną obsługą technologii InfiniBand do włączenia funkcji RDMA są wymagane odpowiednie sterowniki.
- Obrazy [maszyn wirtualnych CentOS-HPC](#centos-hpc-vm-images) w witrynie Marketplace są wstępnie skonfigurowane przy użyciu odpowiednich sterowników IB i są najprostszym sposobem rozpoczęcia pracy.
- Obrazy [maszyn wirtualnych z systemem Ubuntu](#ubuntu-vm-images) można skonfigurować przy użyciu odpowiednich sterowników IB. Zaleca się tworzenie niestandardowych [obrazów maszyn wirtualnych z](../../linux/tutorial-custom-images.md) odpowiednimi sterownikami i konfiguracją oraz ponowne używanie tych obrazów cyklicznie.

Na maszynach [wirtualnych serii N](../../sizes-gpu.md) z włączoną obsługą procesora GPU [](../../extensions/hpccompute-gpu-linux.md) wymagane są dodatkowo odpowiednie sterowniki procesora GPU, które można dodać za pośrednictwem rozszerzeń maszyny wirtualnej lub [ręcznie.](../../linux/n-series-driver-setup.md) Niektóre obrazy maszyn wirtualnych w witrynie Marketplace są również wstępnie zainstalowane ze sterownikami procesorów GPU firmy Nvidia, w tym niektóre obrazy maszyn wirtualnych firmy Nvidia.

### <a name="centos-hpc-vm-images"></a>Obrazy maszyn wirtualnych CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>Maszyny wirtualne z obsługą funkcji SR-IOV
W przypadku maszyn wirtualnych z obsługą funkcji [RDMA](../../sizes-hpc.md#rdma-capable-instances)z obsługą funkcji SR-IOV odpowiednie są obrazy maszyn wirtualnych [CentOS-HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) w witrynie Marketplace w wersji 7.6 lub nowszej. Te obrazy maszyn wirtualnych są zoptymalizowane i wstępnie załadowane ze sterownikami OFED dla RDMA i różnymi powszechnie używanymi bibliotekami MPI oraz pakietami obliczeniowymi naukowymi i są najprostszym sposobem rozpoczęcia pracy.
- Szczegółowe informacje na temat obrazów maszyn wirtualnych centOS-HPC w wersji 7.6 lub nowszej znajdują się w artykule [TechCo w witrynie TechCo w witrynie](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Skrypty używane podczas tworzenia obrazów maszyn wirtualnych CentOS-HPC w wersji 7.6 lub nowszej z podstawowego obrazu z witryny CentOS Marketplace znajdują się w [repocie apc-images.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE] 
> Najnowsze obrazy z witryny Azure HPC Marketplace mają wersję Mellanox OFED 5.1 i więcej, która nie obsługuje ConnectX3-Pro InfiniBand. Rozmiary maszyn wirtualnych serii N z włączoną obsługą funkcji SR-IOV z siecią FDR InfiniBand (np. NCv3 i starsze) będą mogły korzystać z następującego obrazu maszyny wirtualnej CentOS-HPC lub starszych wersji z witryny Marketplace:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Maszyny wirtualne bez obsługi funkcji SR-IOV
W przypadku maszyn wirtualnych bez obsługi funkcji [RDMA](../../sizes-hpc.md#rdma-capable-instances)z obsługą funkcji SR-IOV odpowiedni jest system CentOS-HPC w wersji 6.5 lub nowszej, a w witrynie Marketplace jest dostępna wersja do 7.4. Na przykład w przypadku maszyn wirtualnych z serii [H16](../../h-series.md)zalecane są wersje od 7.1 do 7.4. Te obrazy maszyn wirtualnych są wstępnie załadowane ze sterownikami sieci direct dla RDMA i Intel MPI w wersji 5.1.

> [!NOTE]
> Na tych obrazach HPC opartych na systemie CentOS dla maszyn wirtualnych bez obsługi funkcji SR-IOV aktualizacje jądra są wyłączone w pliku **konfiguracji yum.** Wynika to z tego, że sterowniki RDMA systemu Linux NetworkDirect są dystrybuowane jako pakiet RPM, a aktualizacje sterowników mogą nie działać, jeśli jądro zostanie zaktualizowane.

### <a name="rhelcentos-vm-images"></a>Obrazy maszyn wirtualnych z systemem RHEL/CentOS
Obrazy maszyn wirtualnych innych niż HPC oparte na systemie RHEL lub CentOS w witrynie Marketplace można skonfigurować do użycia na maszynach wirtualnych z obsługą funkcji [RDMA](../../sizes-hpc.md#rdma-capable-instances)z obsługą funkcji SR-IOV. Dowiedz się więcej [na temat włączania infiniBand](enable-infiniband.md) [i konfigurowania mpi](setup-mpi.md) na maszyn wirtualnych.
- Można również użyć skryptów używanych podczas tworzenia obrazów maszyn wirtualnych CentOS-HPC w wersji 7.6 lub nowszej z podstawowego obrazu z witryny CentOS Marketplace z [repo azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE]
> Mellanox OFED 5.1 i więcej nie obsługują kart InfiniBand ConnectX3-Pro na maszynach wirtualnych serii N z włączoną obsługą funkcji SR-IOV z FDR InfiniBand (np. NCv3). Użyj wersji LTS Mellanox OFED w wersji 4.9-0.1.7.0 lub starszej na maszynach wirtualnych serii N z ConnectX3-Pro wirtualną. Więcej szczegółów można znaleźć [tutaj.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)

### <a name="ubuntu-vm-images"></a>Obrazy maszyn wirtualnych z systemem Ubuntu
Obrazy maszyn wirtualnych z systemami Ubuntu Server 16.04 LTS, 18.04 LTS i 20.04 LTS w witrynie Marketplace są obsługiwane zarówno dla maszyn wirtualnych z możliwością sr-IOV, jak i innych niż SR-IOV [RDMA.](../../sizes-hpc.md#rdma-capable-instances) Dowiedz się więcej [o włączaniu infiniBand](enable-infiniband.md) [i konfigurowaniu mpi](setup-mpi.md) na maszyn wirtualnych.
- Instrukcje dotyczące włączania technologii InfiniBand na obrazach maszyn wirtualnych z systemem Ubuntu znajdują się w artykule [TechCo w artykule](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Skrypty używane podczas tworzenia obrazów maszyn wirtualnych HPC opartych na systemie Ubuntu 18.04 i 20.04 LTS z podstawowego obrazu z witryny Ubuntu Marketplace znajdują się w [repozytorium apcpc-images.](https://github.com/Azure/azhpc-images/tree/master/ubuntu)

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server obrazów maszyn wirtualnych
Obsługiwane są systemy SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15 VM w witrynie Marketplace. Te obrazy maszyn wirtualnych są wstępnie załadowane ze sterownikami sieci direct dla RDMA i Intel MPI w wersji 5.1. Dowiedz się więcej [o konfigurowaniu mpi](setup-mpi.md) na tych maszyn wirtualnych.

## <a name="optimize-vms"></a>Optymalizowanie maszyn wirtualnych

Poniżej przedstawiono niektóre opcjonalne ustawienia optymalizacji w celu poprawy wydajności maszyny wirtualnej.

### <a name="update-lis"></a>Aktualizowanie lis

Jeśli jest to konieczne ze względu na funkcjonalność lub wydajność, sterowniki usług [Linux Integration Services (LIS)](../../linux/endorsed-distros.md) można instalować lub aktualizować w obsługiwanych dystrybucjach systemu operacyjnego, zwłaszcza w przypadku wdrażania przy użyciu obrazu niestandardowego lub starszej wersji systemu operacyjnego, takiej jak CentOS/RHEL 6.x lub starszej wersji 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Odzyskiwanie pamięci

Zwiększ wydajność, automatycznie odzyskując pamięć, aby uniknąć zdalnego dostępu do pamięci.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Aby ten stan utrwalić po ponownym uruchomieniu maszyny wirtualnej:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Wyłączanie zapory i środowiska SELinux

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

### <a name="configure-walinuxagent"></a>Konfigurowanie usługi WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Opcjonalnie można wyłączyć usługę WALinuxAgent w kroku przed zadaniem i włączyć obsługę po zadaniu w celu maksymalnej dostępności zasobów maszyny wirtualnej dla obciążenia HPC.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat włączania infiniBand](enable-infiniband.md) na maszyny wirtualne serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z włączoną obsługą infiniBand.
- Dowiedz się więcej na temat instalowania i uruchamiania różnych obsługiwanych bibliotek [MPI](setup-mpi.md) na tych maszyn wirtualnych.
- Zapoznaj się [z omówieniem serii HBv3](hbv3-series-overview.md) i [omówieniem serii HC.](hc-series-overview.md)
- Zapoznaj się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności na Azure Compute [Tech Community Blogi](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby uzyskać wyższego poziomu widok architektury uruchamiania obciążeń HPC, zobacz Obliczenia o wysokiej wydajności [(HPC) na platformie Azure.](/azure/architecture/topics/high-performance-computing/)
