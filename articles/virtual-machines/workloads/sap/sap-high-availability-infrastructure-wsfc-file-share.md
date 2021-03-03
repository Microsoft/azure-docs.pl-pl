---
title: Infrastruktura platformy Azure dla oprogramowania SAP ASCS/SCS HA z udziałem plików&usługi WSFC | Microsoft Docs
description: Przygotowanie infrastruktury platformy Azure pod kątem wysokiej dostępności SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień oprogramowania SAP ASCS/SCS
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 2ce38add-1078-4bb9-a1da-6f407a9bc910
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f539a63907bb7cbe9d04dc3249427357d79d7c87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673608"
---
# <a name="prepare-azure-infrastructure-for-sap-high-availability-by-using-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances"></a>Przygotowanie infrastruktury platformy Azure pod kątem wysokiej dostępności SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień oprogramowania SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[ms-blog-s2d-in-azure]:https://blogs.technet.microsoft.com/filecab/2016/05/05/s2dazuretp5/
[arm-sofs-s2d-managed-disks]:https://github.com/robotechredmond/301-storage-spaces-direct-md
[arm-sofs-s2d-non-managed-disks]:https://github.com/Azure/azure-quickstart-templates/tree/master/301-storage-spaces-direct
[deploy-cloud-witness]:https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]:sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f



[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności dla oprogramowania SAP)


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

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

W tym artykule opisano kroki przygotowania infrastruktury platformy Azure, które są niezbędne do zainstalowania i skonfigurowania systemów SAP o wysokiej dostępności w klastrze usługi Windows Server Failover Clustering (WSFC), korzystając z udziału plików skalowalnego w poziomie jako opcji klastrowania wystąpień SAP ASCS/SCS.

## <a name="prerequisite"></a>Wymaganie wstępne

Przed rozpoczęciem instalacji zapoznaj się z następującym artykułem:

* [Przewodnik po architekturze: klastry SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików][sap-high-availability-guide-wsfc-file-share]


## <a name="host-names-and-ip-addresses"></a>Nazwy hostów i adresy IP

| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Statyczny adres IP | Zestaw dostępności |
| --- | --- | --- | --- |
| Pierwszy węzeł klastra ASCS/SCS klaster | ASCS-1 | 10.0.6.4 | ASCS — as |
| Drugi węzeł klastra ASCS/SCS klaster | ASCS-2 | 10.0.6.5 | ASCS — as |
| Nazwa sieci klastra |ASCS — CL | 10.0.6.6 | n/d |
| Nazwa sieci klastra SAP PR1 ASCS |PR1 — ASCS | 10.0.6.7 | n/d |


**Tabela 1**: klaster ASCS/SCS

| OPROGRAMOWANIA \<SID> | Numer wystąpienia SAP ASCS/SCS |
| --- | --- |
| PR1 | 00 |

**Tabela 2**: szczegóły wystąpienia oprogramowania SAP ASCS/SCS


| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Statyczny adres IP | Zestaw dostępności |
| --- | --- | --- | --- |
| Pierwszy węzeł klastra | SOFS-1 | 10.0.6.10 | SOFS — as |
| Drugi węzeł klastra | SOFS-2 | 10.0.6.11 | SOFS — as |
| Trzeci węzeł klastra | SOFS-3 | 10.0.6.12 | SOFS — as |
| Nazwa sieci klastra | SOFS — CL | 10.0.6.13 | n/d |
| Nazwa hosta globalnego SAP | sapglobal | Używaj adresów IP wszystkich węzłów klastra | n/d |

**Tabela 3**: klaster serwera plików Scale-Out


## <a name="deploy-vms-for-an-sap-ascsscs-cluster-a-database-management-system-dbms-cluster-and-sap-application-server-instances"></a>Wdrażanie maszyn wirtualnych dla klastra SAP ASCS/SCS, klastra systemu zarządzania bazami danych (DBMS) i wystąpień serwera aplikacji SAP

Aby przygotować infrastrukturę platformy Azure, wykonaj następujące czynności:

* [Wdróż maszyny wirtualne][sap-high-availability-infrastructure-wsfc-shared-disk].

* [Utwórz i skonfiguruj moduł równoważenia obciążenia platformy Azure dla oprogramowania SAP ASCS][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules].

* W [przypadku korzystania z kolejki serwera replikacji 2 (ERS2) wykonaj Azure Load Balancer konfigurację programu ERS2 ][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]. 

* [Dodaj maszyny wirtualne z systemem Windows do domeny][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain].

* [Dodaj wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain].

* W przypadku korzystania z systemu Windows Server 2016 zalecamy skonfigurowanie [monitora chmury platformy Azure][deploy-cloud-witness].


## <a name="deploy-the-scale-out-file-server-cluster-manually"></a>Wdróż ręcznie klaster serwera plików Scale-Out 

Klaster serwera plików Scale-Out firmy Microsoft można wdrożyć ręcznie, zgodnie z opisem w blogu [bezpośrednie miejsca do magazynowania na platformie Azure][ms-blog-s2d-in-azure], wykonując następujący kod:  


```powershell
# Set an execution policy - all cluster nodes
Set-ExecutionPolicy Unrestricted

# Define Scale-Out File Server cluster nodes
$nodes = ("sofs-1", "sofs-2", "sofs-3")

# Add cluster and Scale-Out File Server features
Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools -Verbose}

# Test cluster
Test-Cluster -node $nodes -Verbose

# Install cluster
$ClusterNetworkName = "sofs-cl"
$ClusterIP = "10.0.6.13"
New-Cluster -Name $ClusterNetworkName -Node $nodes –NoStorage –StaticAddress $ClusterIP -Verbose

# Set Azure Quorum
Set-ClusterQuorum –CloudWitness –AccountName gorcloudwitness -AccessKey <YourAzureStorageAccessKey>

# Enable Storage Spaces Direct
Enable-ClusterS2D

# Create Scale-Out File Server with an SAP global host name
# SAPGlobalHostName
$SAPGlobalHostName = "sapglobal"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

## <a name="deploy-scale-out-file-server-automatically"></a>Automatyczne wdrażanie Scale-Out serwera plików

Wdrożenie Scale-Out serwera plików można również zautomatyzować, używając Azure Resource Manager szablonów w istniejącej sieci wirtualnej i środowiska Active Directory.

> [!IMPORTANT]
> Zalecamy, aby co najmniej trzy węzły klastra dla serwera plików Scale-Out z funkcją dublowania trójwymiarowego.
>
> W interfejsie użytkownika szablonu Menedżer zasobów Scale-Out serwera plików należy określić liczbę maszyn wirtualnych.
>

### <a name="use-managed-disks"></a>Korzystanie z dysków zarządzanych

Azure Resource Manager szablon służący do wdrażania Scale-Out serwera plików z usługami Bezpośrednie miejsca do magazynowania i Azure Managed Disks jest dostępny w witrynie [GitHub][arm-sofs-s2d-managed-disks].

Zalecamy używanie Managed Disks.

![Rysunek 1: ekran UI dla Scale-Out serwera plików Menedżer zasobów szablon z dyskami zarządzanymi][sap-ha-guide-figure-8010]

_**Rysunek 1**: ekran UI dla Scale-Out serwera plików Menedżer zasobów szablon z dyskami zarządzanymi_

W szablonie wykonaj następujące czynności:
1. W polu **Liczba maszyn wirtualnych** wprowadź minimalną liczbę wynoszącą **2**.
2. W polu **liczba dysków maszyny wirtualnej** wprowadź minimalną liczbę dysków wynoszącą **3** (2 dyski + 1 dysk zapasowy = 3 dyski).
3. W polu **Nazwa SOFS** wprowadź nazwę sieci globalnej hosta SAP, **sapglobalhost**.
4. W polu **Nazwa udziału** wpisz nazwę udziału plików, **sapmnt**.

### <a name="use-unmanaged-disks"></a>Korzystanie z dysków niezarządzanych

Azure Resource Manager szablon służący do wdrażania Scale-Out serwera plików Bezpośrednie miejsca do magazynowania z dyskami niezarządzanymi platformy Azure jest dostępny w witrynie [GitHub][arm-sofs-s2d-non-managed-disks].

![Rysunek 2: ekran UI dla Scale-Out serwera plików Azure Resource Manager szablon bez dysków zarządzanych][sap-ha-guide-figure-8011]

_**Rysunek 2**: ekran UI dla Scale-Out serwera plików Azure Resource Manager szablon bez dysków zarządzanych_

W polu **Typ konta magazynu** wybierz pozycję **Premium Storage**. Wszystkie inne ustawienia są takie same jak ustawienia dla dysków zarządzanych.

## <a name="adjust-cluster-timeout-settings"></a>Dostosuj ustawienia limitu czasu klastra

Po pomyślnym zainstalowaniu klastra serwera plików Scale-Out systemu Windows należy dostosować progi limitu czasu dla wykrywania trybu failover do warunków na platformie Azure. Parametry, które mają zostać zmienione, są udokumentowane w obszarze [progi sieci klastra trybu failover dostrajania][tuning-failover-cluster-network-thresholds]. Przy założeniu, że klastrowane maszyny wirtualne znajdują się w tej samej podsieci, Zmień następujące parametry na te wartości:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RouteHistoryLength = 30

Te ustawienia zostały przetestowane przez klientów i oferują dobry kompromis. Są one wystarczająco odporne, ale zapewniają również szybką pracę w trybie failover w rzeczywistych warunkach błędów lub awariach maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie rozwiązania SAP NetWeaver o wysokiej dostępności w klastrze trybu failover systemu Windows i udziału plików dla wystąpień oprogramowania SAP ASCS/SCS][sap-high-availability-installation-wsfc-file-share]
