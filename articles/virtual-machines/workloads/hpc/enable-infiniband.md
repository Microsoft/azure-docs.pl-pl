---
title: Włączanie InifinBand przy użyciu funkcji SR-IOV-Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak włączyć funkcję InfiniBand przy użyciu funkcji SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500072"
---
# <a name="enable-infiniband-with-sr-iov"></a>Włączanie funkcji InfiniBand przy użyciu funkcji SR-IOV

W przypadku maszyn wirtualnych z serii Azure NC, ND i H są one obsługiwane przez dedykowaną sieć InfiniBand. Wszystkie rozmiary z obsługą funkcji RDMA mogą korzystać z tej sieci przy użyciu technologii Intel MPI. Niektóre serie maszyn wirtualnych mają rozszerzoną obsługę wszystkich implementacji MPI i czasowników RDMA za pomocą wirtualizacji SR-IOV. Maszyny wirtualne z obsługą funkcji RDMA obejmują maszyny wirtualne [(HPC)](../../sizes-hpc.md) [zoptymalizowane pod kątem procesora GPU](../../sizes-gpu.md) .

## <a name="choose-your-installation-path"></a>Wybierz ścieżkę instalacji

Aby rozpocząć, najprostszą opcją jest użycie wstępnie skonfigurowanego obrazu platformy dla InfiniBand, gdzie to możliwe:

- **Maszyny wirtualne HPC IaaS** — aby rozpocząć pracę z maszynami wirtualnymi IaaS dla HPC, najprostszym rozwiązaniem jest użycie [obrazu systemu operacyjnego CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), który jest już skonfigurowany przy użyciu InfiniBand. Ponieważ ten obraz jest już skonfigurowany przy użyciu InfiniBand, nie trzeba go konfigurować ręcznie. Aby uzyskać zgodne wersje systemu Windows, zobacz [wystąpienia z funkcją RDMA systemu Windows](../../sizes-hpc.md#rdma-capable-instances).

- **Maszyny wirtualne IaaS GPU** — żadne obrazy platformy nie są obecnie skonfigurowane dla maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU, z wyjątkiem [obrazu systemu operacyjnego CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Aby skonfigurować obraz niestandardowy przy użyciu InfiniBand, zobacz [Ręczne instalowanie MELLANOX OFED](#manually-install-mellanox-ofed).

Jeśli używasz niestandardowego obrazu maszyny wirtualnej lub maszyny wirtualnej [zoptymalizowanej pod kątem procesora GPU](../../sizes-gpu.md) , należy ją skonfigurować przy użyciu funkcji InfiniBand, dodając rozszerzenie maszyny wirtualnej InfiniBandDriverLinux lub InfiniBandDriverWindows do wdrożenia. Dowiedz się, jak używać tych rozszerzeń maszyn wirtualnych z systemem [Linux](../../sizes-hpc.md#rdma-capable-instances) i [Windows](../../sizes-hpc.md#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Ręczna instalacja Mellanox OFED

Aby ręcznie skonfigurować InfiniBand przy użyciu funkcji SR-IOV, wykonaj następujące czynności. W przykładzie w tych krokach przedstawiono składnię dla RHEL/CentOS, ale czynności te są ogólne i mogą być używane w przypadku dowolnego zgodnego systemu operacyjnego, takiego jak Ubuntu (16,04, 18,04 19,04) i SLES (12 SP4 i 15). Sterowniki skrzynki odbiorczej działają również, ale sterowniki Mellanox OpenFabrics zapewniają więcej funkcji.

Aby uzyskać więcej informacji na temat obsługiwanych dystrybucji dla sterownika Mellanox, zobacz najnowsze [sterowniki Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Więcej informacji o sterowniku Mellanox OpenFabrics można znaleźć w [podręczniku użytkownika Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Zobacz następujący przykład konfigurowania usługi InfiniBand w systemie Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

W przypadku systemu Windows Pobierz i zainstaluj [sterowniki MELLANOX OFED for Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Włączanie protokołu IP przez InfiniBand

Użyj następujących poleceń, aby włączyć protokół IP przez InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](/azure/architecture/topics/high-performance-computing/) na platformie Azure.
