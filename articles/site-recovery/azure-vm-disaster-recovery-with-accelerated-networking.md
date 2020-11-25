---
title: Włącz przyspieszone sieci na potrzeby odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Opisuje sposób włączania przyspieszonej sieci przy użyciu Azure Site Recovery na potrzeby odzyskiwania po awarii maszyny wirtualnej platformy Azure
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000253"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Przyspieszona sieć z odzyskiwaniem po awarii maszyny wirtualnej platformy Azure

Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki datapath, skracając czas opóźnienia, wahania i użycie procesora CPU w celu użycia z najbardziej wymagającymi obciążeniami sieci na obsługiwanych typach maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi za pomocą i bez przyspieszonej sieci:

![Porównanie](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery pozwala korzystać z zalet przyspieszonej sieci dla maszyn wirtualnych platformy Azure, które zostały przełączone w tryb failover w innym regionie świadczenia usługi Azure. W tym artykule opisano sposób włączania przyspieszonej sieci dla maszyn wirtualnych platformy Azure replikowanych za pomocą Azure Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz:
-   [Architektura replikacji](azure-to-azure-architecture.md) maszyny wirtualnej platformy Azure
-   [Konfigurowanie replikacji](azure-to-azure-tutorial-enable-replication.md) dla usługi Azure Virtual Machines
-   Przechodzenie [w tryb](azure-to-azure-tutorial-failover-failback.md) failover Azure Virtual Machines

## <a name="accelerated-networking-with-windows-vms"></a>Przyspieszona sieć z maszynami wirtualnymi z systemem Windows

Azure Site Recovery obsługuje funkcję włączania przyspieszonej sieci dla replikowanych maszyn wirtualnych tylko wtedy, gdy źródłowa maszyna wirtualna ma włączoną funkcję przyspieszonej sieci. Jeśli źródłowa maszyna wirtualna nie ma włączonej przyspieszonej sieci, możesz dowiedzieć się, jak włączyć przyspieszone sieci [dla maszyn wirtualnych](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)z systemem Windows.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane z poziomu galerii platformy Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU.  Obsługiwane są następujące serie: D/DSv2 i F/FS

W wystąpieniach, które obsługują wielowątkowość, przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych vCPU. Obsługiwane są następujące serie: D/DSv3, E/ESv3, Fsv2 i MS/MMS

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Przyspieszona sieć z maszynami wirtualnymi z systemem Linux

Azure Site Recovery obsługuje funkcję włączania przyspieszonej sieci dla replikowanych maszyn wirtualnych tylko wtedy, gdy źródłowa maszyna wirtualna ma włączoną funkcję przyspieszonej sieci. Jeśli źródłowa maszyna wirtualna nie ma włączonej przyspieszonej sieci, możesz dowiedzieć się, jak włączyć przyspieszone sieci dla maszyn wirtualnych z systemem Linux w [tym miejscu](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane z poziomu galerii platformy Azure:
* **Ubuntu 16.04**
* **SLES 12 Z DODATKIEM SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Rozciągnij" przy użyciu jądra dla portów**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU.  Obsługiwane są następujące serie: D/DSv2 i F/FS

W wystąpieniach, które obsługują wielowątkowość, przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych vCPU. Obsługiwane są następujące serie: D/DSv3, E/ESv3, Fsv2 i MS/MMS.

Aby uzyskać więcej informacji o wystąpieniach maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Włączanie przyspieszonej sieci dla replikowanych maszyn wirtualnych

Po [włączeniu replikacji](azure-to-azure-tutorial-enable-replication.md) dla usługi Azure Virtual machines program Site Recovery automatycznie wykryje, czy interfejsy sieciowe maszyny wirtualnej mają włączoną funkcję przyspieszonej sieci. Jeśli jest już włączona funkcja przyspieszonej sieci, Site Recovery automatycznie skonfiguruje przyspieszone sieci w interfejsach sieciowych zreplikowanej maszyny wirtualnej.

Stan przyspieszonej sieci można zweryfikować w sekcji **interfejsy sieciowe** w obszarze Ustawienia **obliczeń i sieci** dla zreplikowanej maszyny wirtualnej.

![Przyspieszone ustawienie sieci](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Jeśli po włączeniu replikacji włączono funkcję przyspieszonej sieci na źródłowej maszynie wirtualnej, można włączyć przyspieszone sieci dla interfejsów sieciowych zreplikowanej maszyny wirtualnej przez następujący proces:
1. Otwórz ustawienia **obliczeń i sieci** dla zreplikowanej maszyny wirtualnej
2. Kliknij nazwę interfejsu sieciowego w sekcji **interfejsy sieciowe** .
3. Wybierz pozycję **włączone** z listy rozwijanej dla przyspieszonej sieci w kolumnie **Target**

![Włącz przyspieszone sieci](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Należy również zastosować powyższy proces dla istniejących zreplikowanych maszyn wirtualnych, które nie były wcześniej automatycznie obsługiwane przez Site Recovery.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [zaletach przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Dowiedz się więcej o ograniczeniach i ograniczeniach dotyczących przyspieszonej sieci dla maszyn [wirtualnych z systemem Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) i [maszyn wirtualnych systemu Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.