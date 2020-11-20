---
title: Infrastruktura platformy Azure dla oprogramowania SAP ASCS/SCS z usługą WSFC&udostępniony dysk | Microsoft Docs
description: Dowiedz się, jak przygotować infrastrukturę platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 205a290d2483a032ed6b5579dbedd555f9f6d02f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958646"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Przygotowanie infrastruktury platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla oprogramowania SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności dla oprogramowania SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![System operacyjny Windows][Logo_Windows] Windows


W tym artykule opisano kroki, które należy wykonać, aby przygotować infrastrukturę platformy Azure do instalowania i konfigurowania wystąpienia usługi SAP ASCS/SCS o wysokiej dostępności w klastrze trybu failover systemu Windows przy użyciu *udostępnionego dysku klastra* jako opcji klastrowania wystąpienia SAP ASCS.
W dokumentacji przedstawiono dwa alternatywy dla *udostępnionego dysku klastra* :

- [Dyski udostępnione platformy Azure](../../disks-shared.md)
- Tworzenie dublowanego magazynu przy użyciu [oprogramowanie SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) 

Poprzednia konfiguracja jest zależna od [grup rozmieszczenia usługi Azure zbliżeniowe (PPG)](./sap-proximity-placement-scenarios.md) w celu uzyskania optymalnego opóźnienia sieci dla obciążeń SAP. Dokumentacja nie obejmuje warstwy bazy danych.  

> [!NOTE]
> Grupy umieszczania w usłudze Azure — wymagania wstępne są wymagane do korzystania z dysku udostępnionego platformy Azure.
 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji zapoznaj się z tym artykułem:

* [Przewodnik po architekturze: klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Tworzenie maszyn wirtualnych ASCS

W przypadku klastra SAP ASCS/SCS Wdróż dwie maszyny wirtualne w zestawie dostępności platformy Azure. Wdróż maszyny wirtualne w tej samej grupie umieszczania sąsiedztwa. Po wdrożeniu maszyn wirtualnych:  
- Tworzenie wewnętrznego Load Balancer platformy Azure dla wystąpienia oprogramowania SAP ASCS/SCS 
- Dodawanie maszyn wirtualnych z systemem Windows do domeny usługi AD

Nazwy hostów i adresy IP dla prezentowanego scenariusza są następujące:

| Rola nazwy hosta | Nazwa hosta | Statyczny adres IP | Zestaw dostępności | Grupa położenia zbliżeniowe |
| --- | --- | --- |---| ---|
| pierwszy węzeł klastra ASCS/SCS klaster |PR1-ASCS-10 |10.0.0.4 |PR1-ASCS-avset |PR1PPG |
| klaster ASCS/SCS drugiego węzła klastra |PR1-ASCS-11 |10.0.0.5 |PR1-ASCS-avset |PR1PPG |
| Nazwa sieci klastra | pr1clust |10.0.0.42 (**tylko** w przypadku klastra win 2016) | n/d | n/d |
| Nazwa sieci klastra ASCS | pr1-ascscl |10.0.0.43 | n/d | n/d |
| Nazwa sieci klastra wykres WYWOŁUJĄCYCH (**tylko** dla ERS2) | pr1-erscl |10.0.0.44 | n/d | n/d |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Tworzenie wewnętrznego modułu równoważenia obciążenia platformy Azure

SAP ASCS, SAP SCS i New SAP ERS2, użyj wirtualnej nazwy hosta i wirtualnych adresów IP. W przypadku korzystania z wirtualnego adresu IP na platformie Azure wymagany jest [moduł równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md) . Zdecydowanie zalecamy użycie usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.    


Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS/wykres WYWOŁUJĄCYCH. Konfiguracja programów SAP ASCS i ERS2 w ramach tego samego modułu równoważenia obciążenia platformy Azure.  

**Z SCS**
- Konfiguracja frontonu
    - Statyczny adres IP ASCS/SCS **10.0.0.43**
- Konfiguracja zaplecza  
    Dodaj wszystkie maszyny wirtualne, które powinny być częścią klastra programu (A) SCS/wykres WYWOŁUJĄCYCH. W tym przykładzie maszyny wirtualne **PR1-ASCS-10** i **PR1-ASCS-11**.
- Port sondy
    - Port 620 **Nr** pozostaw opcję domyślną dla protokołu (TCP), interwał (5), próg złej kondycji (2)
- Reguły równoważenia obciążenia
    - W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję Porty HA
    - W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
        - 32 **Nr** TCP
        - 36 **Nr** TCP
        - 39 **Nr** TCP
        - 81 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **Nr** 16 TCP

    - Upewnij się, że limit czasu bezczynności (w minutach) jest ustawiony na wartość maksymalna 30 i że jest włączony swobodny adres IP (bezpośredni zwrot serwera).

**ERS2**

W przypadku, gdy w kolejce jest również klastrowane serwer replikacji 2 (ERS2), ERS2 wirtualny adres IP musi być również skonfigurowany na platformie Azure ILB oprócz powyżej protokołu SAP ASCS/SCS IP. Ta sekcja ma zastosowanie tylko w przypadku korzystania ze architektury serwera replikacji w kolejce 2.  
- Druga konfiguracja frontonu
    - Statyczny adres IP ERS2 SAP **10.0.0.44**

- Konfiguracja zaplecza  
  Maszyny wirtualne zostały już dodane do puli zaplecza ILB.  

- drugi port sondy
    - Port 621 **Nr**  
    Pozostaw opcję domyślną dla protokołu (TCP), interwał (5), próg złej kondycji (2)

- drugie reguły równoważenia obciążenia
    - W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję Porty HA
    - W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
        - 32 **Nr** TCP
        - 33 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **Nr** 16 TCP

    - Upewnij się, że limit czasu bezczynności (w minutach) jest ustawiony na wartość maksymalna 30 i że jest włączony swobodny adres IP (bezpośredni zwrot serwera).


> [!TIP]
> [Szablon Azure Resource Manager dla wystąpienia usługi WSFC dla oprogramowania SAP ASCS/SCS z dyskiem udostępnionym platformy Azure](https://github.com/robotechredmond/301-shared-disk-sap)pozwala zautomatyzować Przygotowanie infrastruktury przy użyciu dysku udostępnionego platformy Azure dla jednego identyfikatora SID SAP z ERS1.  
> Szablon Azure ARM spowoduje utworzenie dwóch maszyn wirtualnych z systemem Windows 2019 lub 2016, utworzenie udostępnionego dysku platformy Azure i dołączenie do maszyn wirtualnych. Zostanie również utworzona i skonfigurowana wewnętrzna Load Balancer platformy Azure. Aby uzyskać szczegółowe informacje, zobacz szablon ARM. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Dodaj wpisy rejestru na obu węzłach klastra wystąpienia ASCS/SCS

Azure Load Balancer mogą zamykać połączenia, jeśli połączenia są bezczynne przez okres i przekraczają limit czasu bezczynności. Proces roboczy SAP umożliwia otwieranie połączeń z procesem dodawania do kolejki SAP zaraz po pierwszym wysłaniu żądania w kolejce/wyłączaniu do kolejki. Aby uniknąć przerwania tych połączeń, należy zmienić wartości protokołu TCP/IP KeepAliveTime i KeepAliveInterval w obu węzłach klastra. Jeśli jest używany ERS1, konieczne jest również dodanie parametrów profilu SAP, zgodnie z opisem w dalszej części tego artykułu.
Następujące wpisy rejestru należy zmienić w obu węzłach klastra:

- KeepAliveTime
- KeepAliveInterval

| Ścieżka| Nazwa zmiennej | Typ zmiennej  | Wartość | Dokumentacja |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (liczba dziesiętna) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (liczba dziesiętna) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Aby zastosować zmiany, uruchom ponownie oba węzły klastra.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Dodaj maszyny wirtualne z systemem Windows do domeny
Po przypisaniu statycznych adresów IP do maszyn wirtualnych należy dodać maszyny wirtualne do domeny. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Instalowanie i Konfigurowanie klastra trybu failover systemu Windows 

### <a name="install-the-windows-failover-cluster-feature"></a>Instalowanie funkcji klastra trybu failover systemu Windows

Uruchom to polecenie na jednym z węzłów klastra:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Po zakończeniu instalacji funkcji należy ponownie uruchomić oba węzły klastra.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testowanie i Konfigurowanie klastra trybu failover systemu Windows 

W systemie Windows 2019 klaster automatycznie rozpozna, że jest uruchomiony na platformie Azure, i jako domyślną opcję dla adresu IP zarządzania klastrami użyje nazwy sieci rozproszonej. W związku z tym będzie używać dowolnych lokalnych adresów IP węzłów klastra. W związku z tym nie ma potrzeby dla klastra dedykowanej nazwy sieciowej (wirtualnej) i nie ma potrzeby konfigurowania tego adresu IP w wewnętrznej Load Balancer platformy Azure.

Aby uzyskać więcej informacji, zobacz [Klastry trybu failover w systemie Windows Server 2019 nowe funkcje](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) uruchamiają to polecenie na jednym z węzłów klastra:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Konfigurowanie kworum w chmurze klastra
W przypadku korzystania z systemu Windows Server 2016 lub 2019 zalecamy skonfigurowanie [monitora chmury platformy Azure](/windows-server/failover-clustering/deploy-cloud-witness)jako kworum klastra.

Uruchom to polecenie na jednym z węzłów klastra:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Dostrajanie progów klastra trybu failover systemu Windows
 
Po pomyślnym zainstalowaniu klastra trybu failover systemu Windows należy dostosować niektóre progi, aby były odpowiednie dla klastrów wdrożonych na platformie Azure. Parametry, które mają zostać zmienione, są udokumentowane w obszarze [progi sieci klastra trybu failover dostrajania](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Przy założeniu, że dwie maszyny wirtualne tworzące konfigurację klastra systemu Windows dla ASCS/SCS znajdują się w tej samej podsieci, Zmień następujące parametry na te wartości:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Te ustawienia zostały przetestowane przez klientów i oferują dobry kompromis. Są one wystarczająco odporne, ale zapewniają również tryb failover, który jest wystarczająco szybki dla rzeczywistych warunków błędów w obciążeniu SAP lub awariach maszyny wirtualnej.  

## <a name="configure-azure-shared-disk"></a>Konfigurowanie dysku udostępnionego platformy Azure
Ta sekcja ma zastosowanie tylko w przypadku korzystania z dysku udostępnionego platformy Azure. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Tworzenie i dołączanie dysku udostępnionego platformy Azure przy użyciu programu PowerShell
Uruchom to polecenie na jednym z węzłów klastra. Konieczne będzie dostosowanie wartości dla grupy zasobów, regionu platformy Azure, SAPSID i tak dalej.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formatowanie udostępnionego dysku przy użyciu programu PowerShell
1. Pobierz numer dysku. Uruchom te polecenia programu PowerShell na jednym z węzłów klastra:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Sformatuj dysk. W tym przykładzie jest to dysk numer 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Sprawdź, czy dysk jest teraz widoczny jako dysk klastrowy.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Zarejestruj dysk w klastrze.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> OPROGRAMOWANIE SIOS DataKeeper Cluster Edition dla dysku udziału klastra SAP ASCS/SCS
Ta sekcja ma zastosowanie tylko w przypadku używania oprogramowania oprogramowanie SIOS DataKeeper klastra, aby utworzyć dublowany magazyn, który symuluje udostępniony dysk klastra.  

Teraz masz działającą konfigurację klastra trybu failover systemu Windows Server na platformie Azure. Do zainstalowania wystąpienia oprogramowania SAP ASCS/SCS potrzebny jest zasób dysku udostępnionego. Jedną z opcji jest użycie oprogramowanie SIOS DataKeeper Cluster Edition to rozwiązanie innej firmy, za pomocą którego można utworzyć udostępnione zasoby dyskowe.  

Instalowanie oprogramowanie SIOS DataKeeper Cluster w przypadku dysku udziału klastra SAP ASCS/SCS obejmuje następujące zadania:
- W razie konieczności Dodaj platformę Microsoft .NET. Zobacz [dokumentacja oprogramowanie SIOS] ( https://us.sios.com/products/datakeeper-cluster/) Aby uzyskać najbardziej aktualne wymagania dotyczące programu .NET Framework 
- Zainstaluj program oprogramowanie SIOS DataKeeper
- Konfiguruj oprogramowanie SIOScy

### <a name="install-sios-datakeeper"></a>Zainstaluj program oprogramowanie SIOS DataKeeper
Zainstaluj program oprogramowanie SIOS DataKeeper Cluster Edition na każdym węźle w klastrze. Aby utworzyć wirtualny magazyn udostępniony z oprogramowanie SIOS DataKeeper, Utwórz zsynchronizowane dublowanie i Symuluj magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania oprogramowanie SIOS należy utworzyć użytkownika domeny DataKeeperSvc.

> [!NOTE]
> Dodaj użytkownika domeny DataKeeperSvc do lokalnej grupy administratorów na obu węzłach klastra.
>

1. Zainstaluj oprogramowanie oprogramowanie SIOS na obu węzłach klastra.

   ![Instalator oprogramowanie SIOS][sap-ha-guide-figure-3030]

   ![Rysunek 31. Pierwsza strona instalacji elementu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3031]

   _Pierwsza strona instalacji elementu oprogramowanie SIOS DataKeeper_

2. W oknie dialogowym wybierz pozycję **tak**.

   ![Ilustracja 32: usługa DataKeeper informuje o tym, że usługi zostaną wyłączone][sap-ha-guide-figure-3032]

   _Element informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym zalecamy wybranie **konta domeny lub serwera**.

   ![Rysunek 33: wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper_

4. Wprowadź nazwę użytkownika i hasło konta domeny, które zostały utworzone dla oprogramowanie SIOS DataKeeper.

   ![Rysunek 34: Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3034]

   _Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper_

5. Zainstaluj klucz licencji dla wystąpienia oprogramowanie SIOS DataKeeper, jak pokazano na rysunku 35.

   ![Rysunek 35: Wprowadź klucz licencji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3035]

   _Wprowadź klucz licencji oprogramowanie SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

### <a name="configure-sios-datakeeper"></a>Konfiguruj oprogramowanie SIOScy
Po zainstalowaniu usługi oprogramowanie SIOS DataKeeper na obu węzłach Uruchom konfigurację. Celem konfiguracji jest posiadanie synchronicznej replikacji danych między dodatkowymi dyskami dołączonymi do każdej maszyny wirtualnej.

1. Uruchom narzędzie do zarządzania i konfiguracji programu DataKeeper, a następnie wybierz pozycję **Połącz serwer**.

   ![Ilustracja 36: oprogramowanie SIOS narzędzia do zarządzania i konfiguracji][sap-ha-guide-figure-3036]

   _Narzędzie do zarządzania i konfiguracji oprogramowanie SIOSer_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł.

   ![Rysunek 37: Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł][sap-ha-guide-figure-3037]

   _Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Rysunek 38: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _Tworzenie zadania replikacji_

   Kreator przeprowadzi Cię przez proces tworzenia zadania replikacji.

4. Zdefiniuj nazwę zadania replikacji.

   ![Rysunek 39: Definiowanie nazwy zadania replikacji][sap-ha-guide-figure-3039]

   _Zdefiniuj nazwę zadania replikacji_

   ![Rysunek 40: Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym][sap-ha-guide-figure-3040]

   _Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Rysunek 41: Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego][sap-ha-guide-figure-3041]

   _Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego_

6. Zdefiniuj algorytmy kompresji. W naszym przykładzie zalecamy kompresję strumienia replikacji. W szczególności w sytuacjach ponownych synchronizacji kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Kompresja wykorzystuje zasoby procesora CPU i pamięci RAM maszyny wirtualnej. W miarę wzrostu szybkości kompresji jest to ilość używanych zasobów procesora CPU. Można dostosować to ustawienie później.

7. Innym ustawieniem należy sprawdzić, czy replikacja odbywa się asynchronicznie czy synchronicznie. W przypadku ochrony konfiguracji oprogramowania SAP ASCS/SCS należy użyć replikacji synchronicznej.  

   ![Rysunek 42: Definiowanie szczegółów replikacji][sap-ha-guide-figure-3042]

   _Zdefiniuj Szczegóły replikacji_

8. Zdefiniuj, czy wolumin replikowany przez zadanie replikacji ma być reprezentowany przez konfigurację klastra trybu failover systemu Windows Server jako dysk udostępniony. W przypadku konfiguracji SAP ASCS/SCS wybierz opcję **tak** , aby klaster systemu Windows widział wolumin replikowany jako dysk udostępniony, który może być używany jako wolumin klastra.

   ![Rysunek 43: wybierz pozycję tak, aby ustawić wolumin replikowany jako wolumin klastra][sap-ha-guide-figure-3043]

   _Wybierz pozycję **tak** , aby ustawić wolumin replikowany jako wolumin klastra_

   Po utworzeniu woluminu narzędzie zarządzania i konfiguracji programu DataManagement wskazuje, że zadanie replikacji jest aktywne.

   ![Rysunek 44: funkcja dublowania synchronicznego dla dysku udziału ASCS/SCS jest aktywna][sap-ha-guide-figure-3044]

   _Funkcja dublowania synchronicznego dla dysku udziału SAP ASCS/SCS jest aktywna_

   Menedżer klastra trybu failover teraz przedstawia dysk jako dysk programu do przechowywania danych, jak pokazano na rysunku 45:

   ![Rysunek 45: Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik][sap-ha-guide-figure-3045]

   _Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik_


## <a name="next-steps"></a>Następne kroki

* [Instalowanie oprogramowania SAP NetWeaver HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]