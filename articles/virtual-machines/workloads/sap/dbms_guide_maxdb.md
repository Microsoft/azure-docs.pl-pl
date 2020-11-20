---
title: Wdrażanie oprogramowania SAP MaxDB, liveCache i Content Server na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Wdrażanie oprogramowania SAP MaxDB, liveCache i Content Server na platformie Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4257fcca1172502560c182651d649745e494cc1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958969"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Wdrażanie oprogramowania SAP MaxDB, liveCache i Content Server na maszynach wirtualnych platformy Azure

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




W tym dokumencie omówiono kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania MaxDB, liveCache i serwera zawartości na platformie Azure IaaS. Jako warunek wstępny do tego dokumentu należy przeczytać [zagadnienia dotyczące dokumentu dotyczące wdrożenia systemu azure Virtual Machines DBMS dotyczące obciążeń SAP](dbms_guide_general.md) oraz innych przewodników w [obciążeniu SAP w dokumentacji platformy Azure](./get-started.md). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Specyficzne dla wdrożeń SAP MaxDB w systemie Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Obsługa wersji SAP MaxDB na platformie Azure
System SAP obecnie obsługuje oprogramowanie SAP MaxDB w wersji 7,9 lub nowszej do użycia z produktami opartymi na oprogramowaniu SAP NetWeaver na platformie Azure. Wszystkie aktualizacje sterowników oprogramowania SAP MaxDB Server lub JDBC i ODBC, które mają być używane z produktami opartymi na oprogramowaniu SAP NetWeaver, są dostarczane wyłącznie za pośrednictwem platformy SAP Service Marketplace pod adresem <https://support.sap.com/swdc> .
Ogólne informacje na temat uruchamiania oprogramowania SAP NetWeaver w systemie SAP MaxDB można znaleźć pod adresem <https://www.sap.com/community/topic/maxdb.html> .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Obsługiwane wersje systemu Microsoft Windows i typy maszyn wirtualnych platformy Azure dla systemu SAP MaxDB DBMS
Aby znaleźć obsługiwaną wersję systemu Microsoft Windows dla oprogramowania SAP MaxDB DBMS na platformie Azure, zobacz:

* [Platforma SAP — dostępność produktu (PAM)][sap-pam]
* Uwaga dotycząca oprogramowania SAP [1928533]

Zdecydowanie zaleca się użycie najnowszej wersji systemu operacyjnego Microsoft Windows, która jest w systemie Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Dostępna dokumentacja platformy SAP MaxDB dla MaxDB
Zaktualizowaną listę dokumentacji SAP MaxDB można znaleźć w następującej uwadze dotyczącej oprogramowania SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP MaxDB na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Konfiguracja usługi Storage
Najlepsze rozwiązania dotyczące usługi Azure Storage dla oprogramowania SAP MaxDB są zgodne z ogólnymi zaleceniami wymienionymi w rozdziale [Storage Structure The VM for RDBMS Deployments](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Podobnie jak w przypadku innych baz danych, rozwiązanie SAP MaxDB ma także pliki danych i dziennika. Jednakże w przypadku oprogramowania SAP MaxDB w terminologii prawidłowy termin to "Volume" (nie "File"). Na przykład istnieją woluminy danych i woluminy dzienników SAP MaxDB. Nie należy mylić ich z woluminami dysków systemu operacyjnego. 
> 
> 

Krótko mówiąc:

* Jeśli używasz kont usługi Azure Storage, Ustaw konto usługi Azure Storage, które przechowuje woluminy danych i dzienników SAP MaxDB (pliki danych i dziennika) do **magazynu lokalnego nadmiarowego (LRS)** , zgodnie z opisem w temacie [uwagi dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md).
* Oddziel ścieżkę we/wy dla woluminów danych SAP MaxDB (pliki danych) ze ścieżki we/wy dla woluminów dziennika (pliki dzienników). Oznacza to, że woluminy danych SAP MaxDB (pliki danych) muszą być zainstalowane na jednym dysku logicznym, a woluminy dzienników SAP MaxDB muszą być zainstalowane na innym dysku logicznym.
* Ustaw odpowiedni typ buforowania dla każdego dysku, w zależności od tego, czy jest on używany dla danych MaxDB oprogramowania SAP czy woluminów dziennika (plików danych i dziennika), oraz czy korzystasz z platformy Azure w warstwie Standardowa czy Azure Premium Storage, zgodnie z opisem w temacie [zagadnienia dotyczące wdrażania platformy azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md).
* Tak długo, jak bieżąca liczba operacji we/wy na dysk spełnia wymagania, można przechowywać wszystkie woluminy danych na jednym zainstalowanym dysku, a także przechowywać wszystkie woluminy dziennika bazy danych na innym dysku zainstalowanym.
* Jeśli wymagana jest większa liczba operacji we/wy i/lub spacja, zaleca się korzystanie z pul magazynów systemu Windows (dostępne tylko w systemie Microsoft Windows Server 2012 lub nowszym) w celu utworzenia jednego dużego urządzenia logicznego na wielu zainstalowanych dyskach. Aby uzyskać więcej informacji, zobacz również [zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). To podejście upraszcza administrację zarządzaniem miejscem na dysku i pozwala uniknąć wysiłku ręcznego dystrybuowania plików na wielu zainstalowanych dyskach.
* zdecydowanie zaleca się używanie Premium Storage platformy Azure na potrzeby wdrożeń MaxDB. 

![Konfiguracja referencyjna maszyny wirtualnej usługi Azure IaaS dla systemu SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Tworzenie kopii zapasowej i przywracanie
Podczas wdrażania oprogramowania SAP MaxDB na platformie Azure należy zapoznać się z metodologią tworzenia kopii zapasowych. Nawet jeśli system nie jest systemem produkcyjnym, należy wykonać kopię zapasową bazy danych SAP hostowanej przez oprogramowanie SAP MaxDB. Ponieważ usługa Azure Storage przechowuje trzy obrazy, kopia zapasowa jest teraz mniej ważna pod względem ochrony systemu przed awariami magazynu i ważniejszymi błędami operacyjnymi i administracyjnymi. Podstawową przyczyną utrzymania odpowiedniego planu tworzenia kopii zapasowych i przywracania jest to, że można skompensować błędy logiczne lub ręczne, zapewniając możliwości odzyskiwania do punktu w czasie. W związku z tym celem jest użycie kopii zapasowych do przywrócenia bazy danych do określonego punktu w czasie lub użycie kopii zapasowych na platformie Azure do wypełniania innego systemu przez skopiowanie istniejącej bazy danych. 

Tworzenie kopii zapasowej i przywracanie bazy danych na platformie Azure działa tak samo jak w przypadku systemów lokalnych, dlatego można użyć standardowych narzędzi do tworzenia kopii zapasowych SAP MaxDB i przywracania, które są opisane w jednym z dokumentów dokumentacji SAP MaxDB wymienionych w artykule SAP Uwaga [767598]. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania
Podobnie jak w przypadku wdrożeń bez systemu operacyjnego, wydajność tworzenia kopii zapasowych i przywracania zależy od liczby woluminów, które mogą być jednocześnie odczytywane i przepływności tych woluminów. W związku z tym można założyć, że:

* Im mniejsza liczba dysków używanych do przechowywania urządzeń bazy danych, tym niższa ogólna przepływność odczytu
* Mniejsza liczba elementów docelowych (katalogów rozłożonych, dysków), do których ma zostać zapisana kopia zapasowa, im niższa przepływność

Aby zwiększyć liczbę elementów docelowych do zapisu, dostępne są dwie opcje, które mogą być używane w połączeniu, w zależności od potrzeb:

* Dedykowane woluminy do tworzenia kopii zapasowych
* Rozłożenie woluminu docelowego kopii zapasowej na wiele dysków zainstalowanych w celu zwiększenia przepływności operacji we/wy na woluminie dysku rozłożonego
* Posiadanie oddzielnych dedykowanych urządzeń dysków logicznych dla:
  * Woluminy kopii zapasowej SAP MaxDB (pliki)
  * Woluminy danych SAP MaxDB (pliki)
  * Woluminy dzienników SAP MaxDB (tj. plików)

Rozłożenie woluminu na wiele zainstalowanych dysków zostało omówione wcześniej w temacie [uwagi dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Inne zagadnienia
Wszystkie inne ogólne obszary, takie jak zestawy dostępności platformy Azure lub monitorowanie SAP, również mają zastosowanie zgodnie z opisem w artykule [zagadnienia dotyczące wdrażania platformy azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md).  w przypadku wdrożeń maszyn wirtualnych przy użyciu bazy danych SAP MaxDB.
Inne ustawienia dotyczące oprogramowania SAP MaxDB są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach wymienionych w artykule SAP Uwaga [767598] i w tych uwagach SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Specyficzne dla wdrożeń SAP liveCache w systemie Windows
### <a name="sap-livecache-version-support"></a>Obsługa wersji SAP liveCache
Minimalna wersja oprogramowania SAP liveCache obsługiwana na platformie Azure Virtual Machines to **SAP LC/LCAPPS 10,0 Sp 25** , w tym **LiveCache 7.9.08.31** i **działu prawnego — kompilacja 25** wydana dla **EhP 2 dla SAP SCM 7,0** i nowszych.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Obsługiwane wersje systemu Microsoft Windows i typy maszyn wirtualnych platformy Azure dla systemu SAP liveCache DBMS
Aby znaleźć obsługiwaną wersję systemu Microsoft Windows dla oprogramowania SAP liveCache na platformie Azure, zobacz:

* [Platforma SAP — dostępność produktu (PAM)][sap-pam]
* Uwaga dotycząca oprogramowania SAP [1928533]

Zdecydowanie zaleca się użycie najnowszej wersji systemu operacyjnego Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP liveCache na maszynach wirtualnych platformy Azure
#### <a name="recommended-azure-vm-types-for-livecache"></a>Zalecane typy maszyn wirtualnych platformy Azure dla usługi liveCache
Ponieważ SAP liveCache to aplikacja, która wykonuje ogromne obliczenia, ilość i szybkość pamięci RAM i procesora ma znaczny wpływ na wydajność oprogramowania SAP liveCache. 

W przypadku typów maszyn wirtualnych platformy Azure obsługiwanych przez SAP (SAP Note [1928533]) wszystkie wirtualne zasoby procesora CPU przydzielone do maszyny wirtualnej są obsługiwane przez dedykowane fizyczne zasoby procesora CPU funkcji hypervisor. Brak nadmiernej aprowizacji (i w związku z tym nie ma żadnej konkurencji dla zasobów procesora).

Podobnie w przypadku wszystkich typów wystąpień maszyn wirtualnych platformy Azure obsługiwanych przez oprogramowanie SAP pamięć maszyny wirtualnej jest 100% zmapowana na pamięć fizyczną — nadmierne Inicjowanie obsługi administracyjnej (nadmierne zobowiązanie), na przykład nie jest używana.

W tej perspektywie zdecydowanie zaleca się korzystanie z najnowszych maszyn wirtualnych z serii Dv2, Dv3, EV3 i M. Wybór różnych typów maszyn wirtualnych zależy od pamięci potrzebnej do liveCache i potrzebnych zasobów procesora. Podobnie jak w przypadku wszystkich innych wdrożeń systemu DBMS, zaleca się korzystanie z usługi Azure Premium Storage w przypadku krytycznych woluminów wydajności.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Konfiguracja magazynu dla liveCache na platformie Azure
Ponieważ oprogramowanie SAP liveCache jest oparte na technologii SAP MaxDB, wszystkie zalecenia dotyczące najlepszych rozwiązań w zakresie usługi Azure Storage wymienione dla oprogramowania SAP MaxDB opisane w niniejszym dokumencie są również prawidłowe dla oprogramowania SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Dedykowana maszyna wirtualna platformy Azure dla scenariusza liveCache
Ponieważ rozwiązanie SAP liveCache intensywnie korzysta z mocy obliczeniowej, to zdecydowanie zalecane jest wdrożenie na dedykowanej maszynie wirtualnej platformy Azure. 

![Dedykowana maszyna wirtualna platformy Azure dla liveCache do wydajnego przypadku użycia](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Tworzenie kopii zapasowych i przywracanie dla liveCache na platformie Azure
Tworzenie kopii zapasowych i przywracanie, w tym zagadnienia dotyczące wydajności, zostały już opisane w odpowiednich rozdziałach SAP MaxDB w tym dokumencie. 

#### <a name="other-considerations"></a>Inne zagadnienia
Wszystkie pozostałe obszary ogólne są już opisane w odpowiednim rozdziale SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Specyficzne dla wdrożenia serwera zawartości SAP w systemie Windows na platformie Azure
Serwer zawartości SAP to oddzielny składnik oparty na serwerze służący do przechowywania zawartości, takiej jak dokumenty elektroniczne w różnych formatach. Serwer zawartości SAP jest udostępniany przez opracowywanie technologii i służy do korzystania z wielu aplikacji dla wszystkich aplikacji SAP. Jest on instalowany w osobnym systemie. Typowa zawartość to materiał szkoleniowy i dokumentacja z magazynu wiedzy lub rysunków technicznych pochodzących z systemu zarządzania dokumentami mySAP PLM. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Obsługa wersji serwera zawartości SAP dla maszyn wirtualnych platformy Azure
Obecnie rozwiązanie SAP obsługuje:

* **Serwer zawartości SAP** z wersją **6,50 (i nowszą)**
* **SAP MaxDB w wersji 7,9**
* **Microsoft IIS (Internet Information Server) w wersji 8,0 (lub nowszej)**

Zdecydowanie zaleca się użycie najnowszej wersji serwera zawartości SAP i najnowszej wersji programu **Microsoft IIS**. 

Zapoznaj się z najnowszymi obsługiwanymi wersjami serwera zawartości SAP i programu Microsoft IIS w [macierzy SAP Product Availability Matrix (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Obsługiwane typy Microsoft Windows i maszyny wirtualne platformy Azure dla serwera zawartości SAP
Aby uzyskać informacje na temat obsługiwanej wersji systemu Windows dla serwera zawartości SAP na platformie Azure, zobacz:

* [Platforma SAP — dostępność produktu (PAM)][sap-pam]
* Uwaga dotycząca oprogramowania SAP [1928533]

Zdecydowanie zaleca się użycie najnowszej wersji systemu Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wskazówki dotyczące konfiguracji serwera zawartości SAP dla instalacji SAP na maszynach wirtualnych platformy Azure
#### <a name="storage-configuration-for-content-server-in-azure"></a>Konfiguracja magazynu dla serwera zawartości na platformie Azure
Jeśli skonfigurujesz serwer zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, wszystkie zalecenia dotyczące najlepszych rozwiązań dotyczących usługi Azure Storage wymienione dla oprogramowania SAP MaxDB w tym dokumencie są również ważne dla scenariusza serwera zawartości SAP. 

Jeśli skonfigurujesz serwer zawartości SAP do przechowywania plików w systemie plików, zaleca się użycie dedykowanego dysku logicznego. Funkcja miejsca do magazynowania systemu Windows umożliwia również zwiększenie rozmiaru dysku logicznego i przepływności operacji we/wy, zgodnie z opisem w temacie [zagadnienia dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Lokalizacja serwera zawartości SAP
Serwer zawartości SAP należy wdrożyć w tym samym regionie świadczenia usługi Azure i w sieci wirtualnej platformy Azure, w którym wdrożono system SAP. Możesz zdecydować, czy chcesz wdrożyć składniki serwera zawartości SAP na dedykowanej maszynie wirtualnej platformy Azure, czy na tej samej maszynie wirtualnej, na której działa system SAP. 

![Dedykowana maszyna wirtualna platformy Azure dla serwera zawartości SAP](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Lokalizacja serwera pamięci podręcznej SAP
Serwer z pamięcią podręczną SAP jest dodatkowym składnikiem opartym na serwerze, aby zapewnić dostęp do (buforowanych) dokumentów lokalnie. Serwer pamięci podręcznej SAP przechowuje w pamięci podręcznej dokumenty serwera zawartości SAP. Jest to Optymalizacja ruchu sieciowego, jeśli dokumenty muszą zostać pobrane więcej niż raz z różnych lokalizacji. Ogólna zasada polega na tym, że serwer z pamięcią podręczną SAP musi być fizycznie bliski klientowi, który uzyskuje dostęp do serwera pamięci podręcznej SAP. 

Dostępne są dwie opcje:

1. **Klient jest systemem SAP zaplecza** Jeśli system SAP zaplecza jest skonfigurowany pod kątem dostępu do serwera zawartości SAP, ten system SAP jest klientem. Ponieważ system SAP i serwer zawartości SAP są wdrożone w tym samym regionie świadczenia usługi Azure, w tym samym centrum danych platformy Azure, są fizycznie blisko siebie. W związku z tym nie ma potrzeby posiadania dedykowanego serwera pamięci podręcznej SAP. Klienci interfejsu użytkownika SAP (graficzny interfejs użytkownika SAP lub przeglądarka sieci Web) uzyskują dostęp do systemu SAP bezpośrednio, a system SAP pobiera dokumenty z serwera zawartości SAP.
2. **Klient to lokalna przeglądarka sieci Web** Serwer zawartości SAP można skonfigurować do uzyskiwania dostępu bezpośrednio przez przeglądarkę sieci Web. W takim przypadku przeglądarka sieci Web działająca lokalnie jest klientem serwera zawartości SAP. Lokalne centrum danych i centrum danych platformy Azure są umieszczane w różnych lokalizacjach fizycznych (najlepiej blisko siebie). Lokalne centrum danych jest połączone z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja lub usługi ExpressRoute. Mimo że obie opcje oferują bezpieczne połączenie sieciowe sieci VPN z platformą Azure, połączenie sieciowe typu lokacja-lokacja nie oferuje umowy SLA dotyczącej przepustowości sieci i czasu oczekiwania między lokalnym centrum danych a centrum danych platformy Azure. Aby przyspieszyć dostęp do dokumentów, możesz wykonać jedną z następujących czynności:
   1. Zainstaluj serwer pamięci podręcznej SAP w środowisku lokalnym, blisko lokalnej przeglądarki sieci Web (opcja na rysunku poniżej)
   2. Skonfiguruj usługę Azure ExpressRoute, która oferuje dedykowane i niskie opóźnienia połączenie sieciowe między lokalnym centrum danych a centrum danych platformy Azure.

![Opcja instalacji lokalnego serwera pamięci podręcznej SAP](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Tworzenie kopii zapasowej/przywracanie
W przypadku skonfigurowania serwera zawartości SAP do przechowywania plików w bazie danych SAP MaxDB, procedura tworzenia kopii zapasowej i przywracania oraz zagadnienia dotyczące wydajności zostały już opisane w rozdziale SAP MaxDB w tym dokumencie. 

W przypadku skonfigurowania serwera zawartości SAP do przechowywania plików w systemie plików, jedną z opcji jest wykonanie ręcznego tworzenia kopii zapasowej/przywracania całej struktury plików, w której znajdują się dokumenty. Podobnie jak w przypadku usługi SAP MaxDB Backup/Restore, zaleca się posiadanie dedykowanego woluminu dysku na potrzeby tworzenia kopii zapasowych. 

#### <a name="other"></a>Inne
Inne ustawienia specyficzne dla serwera zawartości SAP są niewidoczne dla maszyn wirtualnych platformy Azure i są opisane w różnych dokumentach i uwagach dla oprogramowania SAP:

* <https://service.sap.com/contentserver> 
* Uwaga dotycząca oprogramowania SAP [1619726]  
