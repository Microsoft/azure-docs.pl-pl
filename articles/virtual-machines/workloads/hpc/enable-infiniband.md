---
title: Włącz InifinBand na maszynach wirtualnych HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak włączyć funkcję InfiniBand na maszynach wirtualnych platformy Azure HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538901"
---
# <a name="enable-infiniband"></a>Włączanie standardu InfiniBand

Maszyny wirtualne z serii [H](../../sizes-hpc.md) [obsługujące funkcję RDMA](../../sizes-hpc.md#rdma-capable-instances) i [N-Series](../../sizes-gpu.md) komunikują się za pośrednictwem sieci InfiniBand o małym opóźnieniu i dużej przepustowości. Funkcja RDMA przez takie połączenie ma kluczowe znaczenie dla zwiększenia skalowalności i wydajności obciążeń w węzłach rozproszonych i systemu AI. Maszyny wirtualne z serii H z obsługą funkcji InfiniBand i serii N są połączone w nieblokującym drzewie FAT o niskiej średnicy dla zoptymalizowanej i spójnej wydajności RDMA.

Istnieją różne sposoby włączania InfiniBand w przypadku rozmiarów maszyn wirtualnych.

## <a name="vm-images-with-infiniband-drivers"></a>Obrazy maszyn wirtualnych ze sterownikami InfiniBand
Zobacz [obrazy maszyn wirtualnych](configure.md#vm-images) , aby zapoznać się z listą obsługiwanych obrazów maszyn wirtualnych w portalu Marketplace, które są wstępnie załadowane za pomocą sterowników InfiniBand (dla maszyn wirtualnych SR-IOV lub innych niż SR-IOV) lub można je skonfigurować przy użyciu odpowiednich sterowników.
W przypadku [maszyn wirtualnych](../../sizes-hpc.md#rdma-capable-instances)obsługujących funkcję SR-IOV z obsługą funkcji RDMA, [CentOS-HPC wersja 7,6 lub nowsza](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) wersja obrazów maszyn wirtualnych w portalu Marketplace to najprostszy sposób na rozpoczęcie pracy.
Obrazy maszyn wirtualnych Ubuntu można skonfigurować za pomocą odpowiednich sterowników dla maszyn wirtualnych z obsługą wirtualizacji SR-IOV i innych niż SR-IOV przy użyciu instrukcji znajdujących się [tutaj](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).

## <a name="infiniband-driver-vm-extensions"></a>Rozszerzenia maszyny wirtualnej sterownika InfiniBand
W systemie Linux [rozszerzenie maszyny wirtualnej InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) może służyć do instalowania sterowników Mellanox OFED i włączania InfiniBand na maszynach wirtualnych z serii H i l z obsługą wirtualizacji SR-IOV.

W systemie Windows [rozszerzenie maszyny wirtualnej InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) instaluje sterowniki bezpośrednie sieci systemu Windows (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) na potrzeby łączności RDMA. W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy pamiętać, że rozszerzenie maszyny wirtualnej HpcVmDrivers jest przestarzałe; nie zostanie ona zaktualizowana.

Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej, można użyć poleceń cmdlet [Azure PowerShell](/powershell/azure/) . Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej](../../extensions/overview.md). Możesz również korzystać z rozszerzeń dla maszyn wirtualnych wdrożonych w [klasycznym modelu wdrażania](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Instalacja ręczna
[Sterowniki Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) można instalować ręcznie na maszynach wirtualnych z [obsługą wirtualizacji SR-IOV](../../sizes-hpc.md#rdma-capable-instances) [i](../../sizes-hpc.md) [serii N](../../sizes-gpu.md) .

### <a name="linux"></a>Linux
[Sterowniki OFED dla systemu Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) można zainstalować przy użyciu poniższego przykładu. Ten przykład jest przeznaczony dla RHEL/CentOS, ale czynności są ogólne i mogą być używane dla dowolnego zgodnego systemu operacyjnego Linux, takiego jak Ubuntu (16,04, 18,04 19,04, 20,04) i SLES (12 SP4 i 15). Sterowniki skrzynki odbiorczej działają również, ale sterowniki Mellanox OFED zapewniają więcej funkcji.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
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
W przypadku systemu Windows Pobierz i zainstaluj [sterowniki MELLANOX OFED for Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Włącz protokół IP przez InfiniBand (IB)
Jeśli planujesz Uruchamianie zadań MPI, zazwyczaj nie potrzebujesz programu IPoIB. Biblioteka MPI będzie używać interfejsu czasowników do komunikacji IB (chyba że jawnie używasz kanału TCP/IP biblioteki MPI). Ale jeśli masz aplikację, która korzysta z protokołu TCP/IP do komunikacji i chcesz uruchomić ponad IB, możesz użyć usługi IPoIB przez interfejs IB. Aby włączyć protokół IP przez InfiniBand, użyj następujących poleceń (dla RHEL/CentOS).

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o instalowaniu różnych [obsługiwanych BIBLIOTEK MPI](setup-mpi.md) i ich optymalną konfigurację na maszynach wirtualnych.
- Zapoznaj się z [omówieniem HB-Series](hb-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md) , aby dowiedzieć się więcej o optymalnym konfigurowaniu obciążeń dotyczących wydajności i skalowalności.
- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC oraz wyniki na [blogach społecznościowych usługi Azure COMPUTE](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
