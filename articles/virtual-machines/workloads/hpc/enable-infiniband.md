---
title: Włączanie technologii InifinBand na maszynach wirtualnych HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak włączyć pakiet InfiniBand na urządzeniach wirtualnych HPC platformy Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599608"
---
# <a name="enable-infiniband"></a>Włączanie standardu InfiniBand

Maszyny wirtualne [serii H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z serii [RDMA](../../sizes-hpc.md#rdma-capable-instances) komunikują się za pośrednictwem sieci InfiniBand o małych opóźnieniach i dużej przepustowości. Możliwość RDMA za pośrednictwem takiego połączenia wzajemnego ma kluczowe znaczenie dla zwiększenia skalowalności i wydajności obciążeń HPC i AI w węzłach rozproszonych. Maszyny wirtualne z serii H i N z włączoną obsługą technologii InfiniBand są połączone w nieblokowym drzewie fat z niskim poziomem projektu w celu zapewnienia zoptymalizowanej i spójnej wydajności RDMA.

Istnieją różne sposoby włączania infiniBand na maszynach wirtualnych o różnych rozmiarach.

## <a name="vm-images-with-infiniband-drivers"></a>Obrazy maszyn wirtualnych ze sterownikami InfiniBand
Zobacz [](configure.md#vm-images) obrazy maszyn wirtualnych, aby uzyskać listę obsługiwanych obrazów maszyn wirtualnych w witrynie Marketplace, które są wstępnie załadowane ze sterownikami InfiniBand (dla maszyn wirtualnych SR-IOV lub innych niż SR-IOV) lub mogą być skonfigurowane przy użyciu odpowiednich sterowników dla maszyn wirtualnych z możliwością [RDMA.](../../sizes-hpc.md#rdma-capable-instances)
- Obrazy [maszyn wirtualnych CentOS-HPC](configure.md#centos-hpc-vm-images) w witrynie Marketplace są najprostszym sposobem rozpoczęcia pracy.
- Obrazy maszyn wirtualnych z systemem [Ubuntu](configure.md#ubuntu-vm-images) można skonfigurować przy użyciu odpowiednich sterowników IB.

## <a name="infiniband-driver-vm-extensions"></a>Rozszerzenia maszyny wirtualnej sterownika InfiniBand
W systemie Linux rozszerzenie [InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) maszyny wirtualnej może służyć do instalowania sterowników Mellanox OFED i włączania infiniBand na maszynach wirtualnych serii H i N z włączoną obsługą funkcji SR-IOV.

W systemie Windows rozszerzenie [InfiniBandDriverWindows vm](../../extensions/hpc-compute-infiniband-windows.md) extension instaluje sterowniki Windows Network Direct (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) dla łączności RDMA. W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy pamiętać, że rozszerzenie maszyny wirtualnej HpcVmDrivers jest przestarzałe; Nie zostanie on zaktualizowany.

Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej, możesz użyć [Azure PowerShell](/powershell/azure/) cmdlet. Aby uzyskać więcej informacji, zobacz [Virtual machine extensions and features (Rozszerzenia i funkcje maszyny wirtualnej).](../../extensions/overview.md) Możesz również pracować z rozszerzeniami dla maszyn wirtualnych wdrożonych w klasycznym [modelu wdrażania.](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)

## <a name="manual-installation"></a>Instalacja ręczna
[Sterowniki Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) można zainstalować ręcznie na maszynach wirtualnych serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z włączoną obsługą funkcji [SR-IOV.](../../sizes-hpc.md#rdma-capable-instances)

### <a name="linux"></a>Linux
Sterowniki [OFED dla systemu Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) można zainstalować z poniższym przykładem. Chociaż w tym przykładzie jest dla RHEL/CentOS, ale kroki są ogólne i mogą być używane dla dowolnego zgodnego systemu operacyjnego Linux, takiego jak Ubuntu (16.04, 18.04 19.04, 20.04) i SLES (12 SP4 i 15). Więcej przykładów dla innych dystrybucji znajduje się w [repo azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). Sterowniki skrzynki odbiorczej również działają, ale sterowniki Mellanox OFED oferują więcej funkcji.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
W przypadku systemu Windows pobierz i zainstaluj [oprogramowanie Mellanox OFED dla sterowników systemu Windows.](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)

## <a name="enable-ip-over-infiniband-ib"></a>Włączanie protokołu IP za pośrednictwem infiniBand (IB)
Jeśli planujesz uruchamianie zadań MPI, zazwyczaj nie potrzebujesz IPoIB. Biblioteka MPI będzie używać interfejsu czasowników do komunikacji IB (chyba że jawnie używasz kanału TCP/IP biblioteki MPI). Jeśli jednak masz aplikację, która używa protokołu TCP/IP do komunikacji i chcesz korzystać z interfejsu IB, możesz użyć interfejsu IPoIB za pośrednictwem interfejsu IB. Użyj następujących poleceń (dla systemu RHEL/CentOS), aby włączyć protokół IP za pośrednictwem infiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat instalowania i uruchamiania różnych obsługiwanych bibliotek [MPI](setup-mpi.md) na tych maszyn wirtualnych.
- Zapoznaj się [z omówieniem serii HBv3](hbv3-series-overview.md) i [omówieniem serii HC.](hc-series-overview.md)
- Zapoznaj się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności na Azure Compute [Tech Community Blogi](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby uzyskać wyższego poziomu widok architektury uruchamiania obciążeń HPC, zobacz Obliczenia o wysokiej wydajności [(HPC) na platformie Azure.](/azure/architecture/topics/high-performance-computing/)
