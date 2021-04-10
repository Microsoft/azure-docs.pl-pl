---
title: Architektura i scenariusze dla maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver | Microsoft Docs
description: Architektura i scenariusze wysokiej dostępności dla oprogramowania SAP NetWeaver na platformie Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d689db4916758e13311ad181fb3b5d7ca9a85260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675324"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektura i scenariusze wysokiej dostępności dla oprogramowania SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Definicje terminologii

**Wysoka dostępność**: odnosi się do zestawu technologii, które minimalizują zakłócenia, zapewniając ciągłość działania usług informatycznych za pomocą nadmiarowych, odpornych na uszkodzenia lub składników chronionych w trybie failover w *tym samym* centrum danych. W naszym przypadku centrum danych znajduje się w obrębie jednego regionu świadczenia usługi Azure.

**Odzyskiwanie po awarii**: dotyczy również minimalizowania przerw w działaniu usług IT i ich odzyskiwania, ale w *różnych* centrach danych, które mogą być setkami kilometrów od siebie nawzajem. W naszym przypadku centra danych mogą znajdować się w różnych regionach świadczenia usługi Azure w tym samym regionie geopolitycznym lub w lokalizacjach ustanowionych przez użytkownika jako klienta.


## <a name="overview-of-high-availability"></a>Przegląd wysokiej dostępności
Wysoką dostępność oprogramowania SAP na platformie Azure można podzielić na trzy typy:

* **Wysoka dostępność infrastruktury platformy Azure**: 

    Na przykład wysoka dostępność może obejmować obliczenia (maszyny wirtualne), sieć lub magazyn oraz jego zalety w celu zwiększenia dostępności aplikacji SAP.

* Korzystanie **z ponownego uruchamiania maszyny wirtualnej infrastruktury platformy Azure w celu uzyskania *większej dostępności* aplikacji SAP**: 

    Jeśli użytkownik zdecyduje się nie używać takich funkcji jak Windows Server Failover Clustering (WSFC) lub Pacemaker w systemie Linux, zostanie wykorzystane ponowne uruchomienie maszyny wirtualnej platformy Azure. Program chroni systemy SAP przed planowanymi i nieplanowanymi przestojami infrastruktury serwera fizycznego platformy Azure i ogólną podstawową platformą platformy Azure.

* **Wysoka dostępność aplikacji SAP**: 

    Aby zapewnić pełną dostępność systemu SAP, należy chronić wszystkie krytyczne składniki systemu SAP. Na przykład:
    * Nadmiarowe serwery aplikacji SAP.
    * Unikatowe składniki. Przykładem może być składnik single point of failure (SPOF), taki jak wystąpienie SAP ASCS/SCS lub system zarządzania bazami danych (DBMS).

Wysoka dostępność oprogramowania SAP na platformie Azure różni się od wysokiej dostępności SAP w lokalnym środowisku fizycznym lub wirtualnym. Poniższy dokument dotyczący [wysokiej dostępności i ciągłości działania firmy SAP NetWeaver w środowiskach wirtualnych z technologią VMware i funkcją Hyper-V w systemie Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] opisuje standardowe konfiguracje wysokiej dostępności SAP w środowiskach zwirtualizowanych w systemie Windows.

Dla systemu Linux nie istnieje zintegrowana z sapinst konfiguracją usługi SAP o wysokiej dostępności. Aby uzyskać informacje o wysokiej dostępności oprogramowania SAP w środowisku lokalnym dla systemu Linux, zobacz [Informacje o partnerze o wysokiej dostępności][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

### <a name="sla-for-single-instance-virtual-machines"></a>Umowa SLA dla maszyn wirtualnych z jednym wystąpieniem

Obecnie jest dostępna umowa SLA na jedną maszynę wirtualną wynoszącą 99,9% z magazynem w warstwie Premium. Aby zapoznać się z informacjami o dostępności pojedynczej maszyny wirtualnej, można utworzyć iloczyn różnych dostępnych [umów dotyczących poziomu usług platformy Azure][azure-sla].

Podstawą obliczenia jest 30 dni miesięcznie lub 43 200 minut. Na przykład czas przestoju 0,05% odpowiada 21,6 minut. Jak zwykle dostępność różnych usług jest obliczana w następujący sposób:

(Usługa dostępności #1/100) * (usługa dostępności #2/100) * (usługa dostępności #3/100) \* ...

Na przykład:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 lub Ogólna dostępność 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Wiele wystąpień maszyn wirtualnych w tym samym zestawie dostępności
W przypadku wszystkich maszyn wirtualnych, które mają co najmniej dwa wystąpienia wdrożone w tym samym *zestawie dostępności*, firma Microsoft gwarantuje, że będziesz mieć łączność z maszynami wirtualnymi z przynajmniej jednym wystąpieniem przez co najmniej 99,95% czasu.

Gdy co najmniej dwie maszyny wirtualne są częścią tego samego zestawu dostępności, każda maszyna wirtualna w zestawie dostępności ma przypisaną *domenę aktualizacji* i *domenę błędów* przez podstawową platformę Azure.

* **Domeny aktualizacji** gwarantują, że wiele maszyn wirtualnych nie jest ponownie uruchomionych w tym samym czasie podczas planowanej konserwacji infrastruktury platformy Azure. Tylko jedna maszyna wirtualna jest uruchamiana ponownie w danym momencie.

* **Domeny błędów** gwarantują, że maszyny wirtualne są wdrażane na składnikach sprzętowych, które nie współdzielą wspólnego źródła i przełącznika sieciowego. Gdy serwery, przełącznik sieciowy lub źródło prądu są poddawane nieplanowanym przestojom, to dotyczy tylko jednej maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

Zestaw dostępności jest używany do osiągnięcia wysokiej dostępności:

* Nadmiarowe serwery aplikacji SAP.  
* Klastry z co najmniej dwoma węzłami (na przykład), które chronią wpłynęły, takie jak wystąpienie SAP ASCS/SCS lub system DBMS.


### <a name="azure-availability-zones"></a>Strefy dostępności platformy Azure
System Azure jest w trakcie wdrażania koncepcji [strefy dostępności platformy Azure](../../../availability-zones/az-overview.md) w różnych [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/). W regionach platformy Azure, w których są oferowane Strefy dostępności, regiony platformy Azure mają wiele centrów danych, które są niezależne od dostarczania źródła, chłodzenia i sieci. Celem oferowania różnych stref w ramach jednego regionu świadczenia usługi Azure jest umożliwienie wdrażania aplikacji w dwóch lub trzech oferowanych Strefy dostępności. Przy założeniu, że problemy w źródłach i/lub sieci mają wpływ tylko na jedną infrastrukturę strefy dostępności, wdrożenie aplikacji w regionie świadczenia usługi Azure będzie nadal w pełni funkcjonalne. Ostatecznie dzięki mniejszej pojemności, ponieważ niektóre maszyny wirtualne w jednej strefie mogą zostać utracone. Jednak maszyny wirtualne w pozostałych dwóch strefach są nadal uruchomione. Regiony platformy Azure, które oferują strefy, są wymienione w [strefy dostępności platformy Azure](../../../availability-zones/az-overview.md).

Korzystając z Strefy dostępności, należy wziąć pod uwagę pewne zagadnienia. Lista zagadnień, takich jak:

- Zestawów dostępności platformy Azure nie można wdrażać w ramach strefy dostępności. Musisz wybrać strefę dostępności lub zestaw dostępności jako ramkę wdrożenia dla maszyny wirtualnej.
- Nie można użyć [podstawowego Load Balancer](../../../load-balancer/load-balancer-overview.md) do tworzenia rozwiązań klastra trybu failover opartych na usługach klastra trybu failover systemu Windows lub w systemie Linux Pacemaker. Zamiast tego należy użyć [jednostki SKU usługi Azure usługa Load Balancer w warstwie Standardowa](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Strefy dostępności platformy Azure nie udziela żadnych gwarancji dotyczących pewnej odległości między różnymi strefami w jednym regionie
- Opóźnienie sieci między różnymi Strefy dostępności platformy Azure w różnych regionach świadczenia usługi Azure może różnić się od regionu platformy Azure do regionu. Istnieją sytuacje, w których klient może w sposób racjonalnie uruchamiać warstwę aplikacji SAP wdrożoną w różnych strefach, ponieważ opóźnienie sieci z jednej strefy do aktywnej maszyny wirtualnej z systemem DBMS jest nadal akceptowalne na podstawie wpływu na proces biznesowy. Istnieją scenariusze klientów, w których opóźnienie między aktywną maszyną wirtualną DBMS w jednej strefie a wystąpieniem aplikacji SAP na maszynie wirtualnej w innej strefie może być zbyt inwazyjne i nieakceptowalne dla procesów firmy SAP Business. W związku z tym architektury wdrożenia muszą być różne w zależności od aktywnej/aktywnej architektury dla aplikacji lub architektury aktywny/pasywny, jeśli opóźnienie jest zbyt wysokie.
- Korzystanie z [usługi Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) jest wymagane do wdrożenia w strefy dostępności platformy Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Planowana i nieplanowana konserwacja maszyn wirtualnych

Dwa typy zdarzeń platformy Azure mogą mieć wpływ na dostępność maszyn wirtualnych:

* Zdarzenia **planowanej konserwacji** to okresowe aktualizacje wykonywane przez firmę Microsoft na podstawową platformę Azure. Aktualizacje zwiększają ogólną niezawodność, wydajność i bezpieczeństwo infrastruktury platformy, na której działają maszyny wirtualne.

* Zdarzenia **nieplanowanej konserwacji** są wykonywane, gdy sprzęt lub fizyczna infrastruktura maszyny wirtualnej nie powiodła się w jakiś sposób. Może obejmować awarie sieci lokalnej, błędy dysku lokalnego lub inne awarie poziomu stojaka. Po wykryciu tego błędu platforma Azure automatycznie migruje maszynę wirtualną z serwera fizycznego złej kondycji, który hostuje maszynę wirtualną na serwer fizyczny zdrowe. Takie zdarzenia są rzadkie, ale mogą również spowodować ponowne uruchomienie maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage
Dane na koncie magazynu są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, które spełniają warunki umowy SLA usługi Azure Storage, nawet w przypadku przejściowych awarii sprzętu.

Ponieważ usługa Azure Storage domyślnie przechowuje trzy obrazy danych, używanie macierzy RAID 5 lub RAID 1 na wielu dyskach platformy Azure jest zbędne.

Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Dyski zarządzane platformy Azure
Managed Disks jest typem zasobu w Azure Resource Manager zalecanym do użycia zamiast wirtualnych dysków twardych (VHD), które są przechowywane na kontach usługi Azure Storage. Dyski zarządzane są automatycznie wyrównane z zestawem dostępności platformy Azure, do którego są dołączone. Zwiększają one dostępność maszyny wirtualnej i usługi, które działają na niej.

Aby uzyskać więcej informacji, zobacz  [Omówienie usługi Azure Managed disks][azure-storage-managed-disks-overview].

Zalecamy używanie dysków zarządzanych, ponieważ upraszczają one wdrażanie maszyn wirtualnych i zarządzanie nimi.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Wykorzystanie infrastruktury platformy Azure o wysokiej dostępności w celu uzyskania *większej dostępności* aplikacji SAP

Jeśli zdecydujesz się nie używać takich funkcji jak WSFC lub Pacemaker w systemie Linux (obecnie obsługiwane tylko w SUSE Linux Enterprise Server [SLES] 12 i nowszych), zostanie wykorzystane ponowne uruchomienie maszyny wirtualnej platformy Azure. Program chroni systemy SAP przed planowanymi i nieplanowanymi przestojami infrastruktury serwera fizycznego platformy Azure i ogólną podstawową platformą platformy Azure.

Aby uzyskać więcej informacji na temat tego podejścia, zobacz temat [Korzystanie z ponownego uruchamiania infrastruktury platformy Azure w celu uzyskania wyższej dostępności systemu SAP][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Wysoka dostępność aplikacji SAP na platformie Azure IaaS

Aby zapewnić pełną dostępność systemu SAP, należy chronić wszystkie krytyczne składniki systemu SAP. Na przykład:
  * Nadmiarowe serwery aplikacji SAP.
  * Unikatowe składniki. Przykładem może być składnik single point of failure (SPOF), taki jak wystąpienie SAP ASCS/SCS lub system zarządzania bazami danych (DBMS).

W następnych sekcjach omówiono, jak uzyskać wysoką dostępność dla wszystkich trzech krytycznych składników systemu SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architektura wysokiej dostępności dla serwerów aplikacji SAP

> Ta sekcja ma zastosowanie do:
>
> ![Logo Windows.][Logo_Windows] System Windows i ![Logo systemu Linux.][Logo_Linux] Linux
>

Zwykle nie jest potrzebne konkretne rozwiązanie wysokiej dostępności dla serwera aplikacji SAP i wystąpień okna dialogowego. Wysoką dostępność można osiągnąć dzięki nadmiarowości i można skonfigurować wiele wystąpień okna dialogowego w różnych wystąpieniach maszyn wirtualnych platformy Azure. W dwóch wystąpieniach maszyn wirtualnych platformy Azure należy zainstalować co najmniej dwa wystąpienia aplikacji SAP.

![Rysunek 1. serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 1.** Serwer aplikacji SAP o wysokiej dostępności_

Wszystkie maszyny wirtualne obsługujące wystąpienia serwera aplikacji SAP należy umieścić w tym samym zestawie dostępności platformy Azure. Zestaw dostępności platformy Azure gwarantuje, że:

* Wszystkie maszyny wirtualne nie są częścią tej samej domeny aktualizacji.  
    Domena aktualizacji gwarantuje, że maszyny wirtualne nie są aktualizowane w tym samym czasie podczas planowanego przestoju konserwacji.

    Podstawowa funkcja, która jest oparta na różnych aktualizacjach i domenach błędów w ramach jednostki skalowania na platformie Azure, została już wprowadzona w sekcji [domeny aktualizacji][planning-guide-3.2.2] .

* Wszystkie maszyny wirtualne nie są częścią tej samej domeny błędów.  
    Domena błędów gwarantuje, że maszyny wirtualne zostaną wdrożone, tak aby żadna single point of failure nie miała wpływu na dostępność wszystkich maszyn wirtualnych.

Liczba domen aktualizacji i błędów, które mogą być używane przez zestaw dostępności platformy Azure w ramach jednostki skalowania platformy Azure, jest ograniczona. Jeśli dodasz maszyny wirtualne do jednego zestawu dostępności, co najmniej dwie maszyny wirtualne będą ostatecznie kończyć się w tej samej domenie błędów lub aktualizacji.

W przypadku wdrożenia kilku wystąpień serwera aplikacji SAP na swoich dedykowanych maszynach wirtualnych przy założeniu, że mamy pięć domen aktualizacji, pojawi się poniższy obraz. Rzeczywista Maksymalna liczba domen aktualizacji i błędów w ramach zestawu dostępności może ulec zmianie w przyszłości:

![Rysunek 2: wysoka dostępność serwerów aplikacji SAP w zestawie dostępności platformy Azure ][planning-guide-figure-3000]
 _**rysunek 2:** wysoka dostępność serwerów aplikacji SAP w zestawie dostępności platformy Azure_

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure][azure-virtual-machines-manage-availability].

Aby uzyskać więcej informacji, zobacz sekcję [zestawy dostępności platformy Azure][planning-guide-3.2.3] w dokumencie planowanie i wdrażanie w usłudze Azure Virtual Machines dla oprogramowania SAP NetWeaver.

**Tylko dyski niezarządzane:** Ponieważ konto usługi Azure Storage jest potencjalnym single point of failure, ważne jest posiadanie co najmniej dwóch kont usługi Azure Storage, w których dystrybuowane są co najmniej dwie maszyny wirtualne. W idealnym instalacji dyski każdej maszyny wirtualnej, na której działa wystąpienie okna dialogowego SAP, zostaną wdrożone na innym koncie magazynu.

> [!IMPORTANT]
> Zdecydowanie zalecamy korzystanie z usługi Azure Managed disks na potrzeby instalacji z użyciem oprogramowania SAP High-Availability. Ponieważ dyski zarządzane są automatycznie wyrównane z zestawem dostępności maszyny wirtualnej, do której są dołączone, zwiększają one dostępność maszyny wirtualnej i usługi, które są na niej uruchomione.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architektura wysokiej dostępności dla wystąpienia oprogramowania SAP ASCS/SCS w systemie Windows

> ![Logo Windows.][Logo_Windows] Windows
>

Do ochrony wystąpienia SAP ASCS/SCS można użyć rozwiązania WSFC. Rozwiązanie ma dwa warianty:

* **Klastrowanie wystąpienia SAP ASCS/SCS za pomocą klastrowanych dysków udostępnionych**: Aby uzyskać więcej informacji dotyczących tej architektury, zobacz [CLUSTERING The SAP ASCS/SCS instance in Windows failover][sap-high-availability-guide-wsfc-shared-disk]Clustering Disk.   

* **Klastrowanie wystąpienia SAP ASCS/SCS za pomocą udziału plików**: Aby uzyskać więcej informacji na temat tej architektury, zobacz [CLUSTERING an SAP ASCS/SCS instance w klastrze trybu failover systemu Windows przy użyciu udziału plików][sap-high-availability-guide-wsfc-file-share].

* **Klastrowanie wystąpienia SAP ASCS/SCS przy użyciu udziału ANF SMB**: Aby uzyskać więcej informacji na temat tej architektury, zobacz klaster klastra [wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików ANF SMB](./high-availability-guide-windows-netapp-files-smb.md).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architektura wysokiej dostępności dla wystąpienia oprogramowania SAP ASCS/SCS w systemie Linux

> ![Logo systemu Linux.][Logo_Linux] Linux
> 
> Aby uzyskać więcej informacji na temat klastrowania wystąpienia SAP ASCS/SCS przy użyciu platformy klastra SLES, zobacz [wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP][sap-suse-ascs-ha]. W przypadku alternatywnej architektury HA w systemie SLES, która nie wymaga systemu plików NFS o wysokiej dostępności, zobacz temat [High Availability w systemie SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP][sap-suse-ascs-ha-anf].

Aby uzyskać więcej informacji na temat klastrowania wystąpienia SAP ASCS/SCS przy użyciu struktury klastra Red Hat, zobacz [Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Konfiguracja protokołu SAP NetWeaver o wiele identyfikatorów SID dla klastrowanego wystąpienia SAP ASCS/SCS

> ![Logo Windows.][Logo_Windows] Windows
> 
> Wiele identyfikatorów SID jest obsługiwanych w usłudze WSFC przy użyciu udziału plików i dysku udostępnionego.
> 
> Aby uzyskać więcej informacji na temat architektury wysokiej dostępności w systemie Windows, zobacz:

* [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności dla klastra trybu failover z systemem Windows Server i udziału plików][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Rozwiązanie SAP ASCS/SCS o wysokiej dostępności dla klastra trybu failover z systemem Windows Server i dysku udostępnionego][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Logo systemu Linux.][Logo_Linux] Linux
> 
> Klastrowanie z obsługą wiele identyfikatorów SID jest obsługiwane w klastrach Pacemaker systemu Linux dla oprogramowania SAP ASCS/wykres WYWOŁUJĄCYCH, co jest ograniczone do **pięciu** identyfikatorów SID SAP w tym samym klastrze.
> Aby uzyskać więcej informacji na temat architektury wysokiej dostępności w systemie Linux, zobacz:

* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-suse-multi-sid.md)
* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie RHEL for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>Wystąpienie systemu DBMS o wysokiej dostępności

System DBMS również jest pojedynczym punktem kontaktu w systemie SAP. Należy chronić je za pomocą rozwiązania o wysokiej dostępności. Na poniższej ilustracji przedstawiono SQL Server AlwaysOn rozwiązanie wysokiej dostępności na platformie Azure z funkcją klaster trybu failover systemu Windows Server i wewnętrznym modułem równoważenia obciążenia platformy Azure. SQL Server AlwaysOn replikuje dane i pliki dziennika systemu DBMS przy użyciu własnej replikacji w systemie DBMS. W takim przypadku nie jest potrzebny udostępniony dysk klastra, co upraszcza całą konfigurację.

![Rysunek 3. przykład środowiska SAP DBMS o wysokiej dostępności z funkcją SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Rysunek 3.** Przykład funkcji SAP DBMS o wysokiej dostępności z funkcją SQL Server AlwaysOn_

Aby uzyskać więcej informacji na temat klastrowania SQL Server systemie DBMS na platformie Azure przy użyciu modelu wdrażania Azure Resource Manager, zobacz następujące artykuły:

* [Ręczne konfigurowanie zawsze włączonej grupy dostępności w usłudze Azure Virtual Machines przy użyciu Menedżer zasobów][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurowanie wewnętrznego modułu równoważenia obciążenia platformy Azure dla grupy dostępności AlwaysOn na platformie Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Aby uzyskać więcej informacji na temat klastrowania SAP HANA systemie DBMS na platformie Azure przy użyciu modelu wdrażania Azure Resource Manager, zobacz [wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure][sap-hana-ha].
