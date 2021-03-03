---
title: Wdrożenie Oracle Azure Virtual Machines DBMS dla obciążenia SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, Oracle, ochrona danych
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/18/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29b1bcec58d6350d0f63c3fe0ce11ef99a648019
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668982"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines wdrożenie Oracle DBMS dla obciążeń SAP

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
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

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


W tym dokumencie omówiono kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania Oracle Database na potrzeby obciążeń SAP na platformie Azure IaaS. Przed przeczytaniem tego dokumentu zalecamy zapoznanie się z [zagadnieniami dotyczącymi wdrażania platformy Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). Zalecamy także zapoznanie się z innymi przewodnikami w ramach [obciążeń SAP w dokumentacji platformy Azure](./get-started.md). 

Informacje o wersjach programu Oracle i odpowiednich wersjach systemu operacyjnego, które są obsługiwane do uruchamiania oprogramowania SAP w programie Oracle na platformie Azure, można znaleźć w temacie SAP Note [2039619].

Ogólne informacje o uruchamianiu programu SAP Business Suite na serwerze Oracle można znaleźć pod adresem [SAP w witrynie Oracle](https://www.sap.com/community/topic/oracle.html).
Oprogramowanie Oracle jest obsługiwane przez program Oracle do uruchamiania na Microsoft Azure. Aby uzyskać więcej informacji na temat ogólnej obsługi funkcji Windows Hyper-V i platformy Azure, zapoznaj się z tematem [często zadawane pytania dotyczące oprogramowania Oracle i Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Uwagi dotyczące oprogramowania SAP dla systemów Oracle, SAP i Azure 

Poniższe uwagi dotyczące oprogramowania SAP są powiązane z oprogramowaniem SAP na platformie Azure.

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP on Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z ulepszonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2178632] |Metryki monitorowania kluczy dla SAP na Microsoft Azure |
| [2191498] |SAP w systemie Linux z platformą Azure: ulepszone monitorowanie |
| [2039619] |Aplikacje SAP na Microsoft Azure przy użyciu bazy danych Oracle: obsługiwane produkty i wersje |
| [2243692] |Maszyna wirtualna z systemem Linux na Microsoft Azure (IaaS): problemy z licencją SAP |
| [2069760] |Oracle Linux 7. x instalacja i uaktualnienie SAP |
| [1597355] |Zalecenie wymiany miejsca dla systemu Linux |
| [2171857] |Oracle Database 12c — obsługa systemu plików w systemie Linux |
| [1114181] |Oracle Database 11g — obsługa systemu plików w systemie Linux |

Dokładne konfiguracje i funkcje, które są obsługiwane przez firmę Oracle i oprogramowanie SAP na platformie Azure, są udokumentowane w temacie SAP uwagi [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

System Windows i Oracle Linux są jedynymi systemami operacyjnymi obsługiwanymi przez firmę Oracle i oprogramowanie SAP na platformie Azure. Powszechnie używane dystrybucje SLES i RHEL systemu Linux nie są obsługiwane w przypadku wdrażania składników Oracle na platformie Azure. Składniki Oracle obejmują klienta Oracle Database, który jest używany przez aplikacje SAP do łączenia się z systemem Oracle DBMS. 

Wyjątki, zgodnie z uwagami SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619), to składniki SAP, które nie korzystają z Oracle Database Client. Takimi składnikami SAP są autonomiczne kolejki SAP, serwer komunikatów, usługi replikacji w kolejce, webwysyłkę i Brama SAP Gateway.  

Nawet w przypadku uruchamiania wystąpień aplikacji Oracle DBMS i SAP na Oracle Linux można uruchomić usługi SAP Central w systemie SLES lub RHEL i chronić je za pomocą klastra z systemem Pacemaker. Pacemaker jako platforma wysokiej dostępności nie jest obsługiwana w Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Specyficzne dla Oracle Database w systemie Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP na maszynach wirtualnych platformy Azure w systemie Windows

Zgodnie z instrukcją instalacji SAP nie należy instalować plików związanych z programem Oracle ani ich zlokalizować na dysku systemu operacyjnego maszyny wirtualnej (dysk c:). Maszyny wirtualne o różnych rozmiarach mogą obsługiwać różną liczbę podłączonych dysków. Mniejsze typy maszyn wirtualnych mogą obsługiwać mniejszą liczbę podłączonych dysków. 

Jeśli masz mniejsze maszyny wirtualne i zostanie osiągnięty limit liczby dysków, które można dołączyć do maszyny wirtualnej, możesz zainstalować/zlokalizować oprogramowanie Oracle Home, Stage,,, `saptrace` , `saparch` `sapbackup` `sapcheck` lub `sapreorg` do dysku systemu operacyjnego. Te części składników systemu Oracle DBMS nie są zbyt intensywne we/wy i przepływności we/wy. Oznacza to, że dysk systemu operacyjnego może obsłużyć wymagania we/wy. Domyślny rozmiar dysku systemu operacyjnego wynosi 127 GB. 

Pliki dziennika Oracle Database i wykonaj ponownie muszą być przechowywane na oddzielnych dyskach danych. Istnieje wyjątek dla tymczasowego obszaru tabel firmy Oracle. `Tempfiles` można utworzyć na D:/ (dysk nietrwały). Nietrwały D:\ dysk oferuje również lepsze opóźnienia we/wy i przepływność (z wyjątkiem maszyn wirtualnych serii A). 

Aby określić odpowiednią ilość miejsca dla programu `tempfiles` , można sprawdzić rozmiary `tempfiles` w istniejących systemach.

### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Obsługiwane jest tylko jedno wystąpienie Oracle używające dysków sformatowanych w systemie plików NTFS. Wszystkie pliki bazy danych muszą być przechowywane w systemie plików NTFS na Managed Disks (zalecane) lub na dyskach VHD. Te dyski są instalowane na maszynę wirtualną platformy Azure i są oparte na [usłudze Azure Page BLOB Storage](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) lub [Azure Managed disks](../../managed-disks-overview.md). 

Zapoznaj się z artykułem [typy magazynów Azure dla obciążeń SAP](./planning-guide-storage.md) , aby uzyskać więcej szczegółów na temat określonych typów magazynów blokowych platformy Azure, które są odpowiednie dla obciążenia systemu DBMS.

Zdecydowanie zalecamy korzystanie z [usługi Azure Managed disks](../../managed-disks-overview.md). Zdecydowanie zalecamy również korzystanie z [usługi Azure Premium Storage lub Azure Ultra Disk](../../disks-types.md) na potrzeby wdrożeń Oracle Database.

Dyski sieciowe lub udziały zdalne, takie jak usługi plików platformy Azure, nie są obsługiwane w przypadku plików Oracle Database. Aby uzyskać więcej informacji, zobacz:

- [Introducing Microsoft Azure File Service](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service) (Wprowadzenie do usługi plików platformy Microsoft Azure)

- [Persisting connections to Microsoft Azure Files (Utrwalanie połączeń z plikami na platformie Microsoft Azure)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Jeśli używasz dysków opartych na usłudze Azure Page BLOB Storage lub Managed Disks, instrukcje w temacie [zagadnienia dotyczące wdrażania platformy azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń z Oracle Database.

Istnieją limity przepływności operacji we/wy na sekundę dla dysków platformy Azure. Omawiane koncepcje zostały omówione w temacie [zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). Dokładne przydziały zależą od używanego typu maszyny wirtualnej. Listę typów maszyn wirtualnych z ich przydziałami można znaleźć w [obszarze rozmiary dla maszyn wirtualnych z systemem Windows na platformie Azure][virtual-machines-sizes-windows].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zobacz uwagi dotyczące oprogramowania SAP [1928533].

Minimalna konfiguracja jest następująca: 

| Składnik | Dysk | Buforowanie | Pula magazynu |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA & mirrlogB | Premium lub Ultra Disk | Brak | Nie jest wymagany |
| \oracle \<SID> \origlogaB & mirrlogA | Premium lub Ultra Disk | Brak | Nie jest wymagany |
| \oracle \<SID> \sapdata1... Azotan | Premium lub Ultra Disk | Tylko odczyt | Może być używany w przypadku wersji Premium |
| \oracle \<SID> \oraarch | Standardowa | Brak | Nie jest wymagany |
| Strona główna firmy Oracle, `saptrace` ,... | Dysk systemu operacyjnego (Premium) | | Nie jest wymagany |


Dyski wybierające do hostowania dzienników wykonywania w trybie online powinny być zależne od wymagań IOPS. Możliwe jest przechowywanie wszystkich sapdata1... n (obszary tabel) na jednym zainstalowanym dysku, o ile rozmiar, liczba operacji we/wy i przepływność spełniają wymagania. 

Konfiguracja wydajności jest następująca:

| Składnik | Dysk | Buforowanie | Pula magazynu |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA | Premium lub Ultra Disk | Brak | Może być używany w przypadku wersji Premium  |
| \oracle \<SID> \origlogaB | Premium lub Ultra Disk | Brak | Może być używany w przypadku wersji Premium |
| \oracle \<SID> \mirrlogAB | Premium lub Ultra Disk | Brak | Może być używany w przypadku wersji Premium |
| \oracle \<SID> \mirrlogBA | Premium lub Ultra Disk | Brak | Może być używany w przypadku wersji Premium |
| \oracle \<SID> \sapdata1... Azotan | Premium lub Ultra Disk | Tylko odczyt | Zalecane w przypadku wersji Premium  |
| \oracle\SID\sapdata (n + 1) * | Premium lub Ultra Disk | Brak | Może być używany w przypadku wersji Premium |
| \oracle \<SID> \oraarch * | Premium lub Ultra Disk | Brak | Nie jest wymagany |
| Strona główna firmy Oracle, `saptrace` ,... | Dysk systemu operacyjnego (Premium) | Nie jest wymagany |

* (n + 1): hostowanie systemu, TEMP i COFAnie tabel. Wzorzec we/wy systemowych i cofania tabel przeszukanych różni się od innych tabel przechowujących dane aplikacji. Żadne buforowanie nie jest najlepszą opcją dla wydajności systemu i cofania tabel.

* oraarch: Pula magazynów nie jest konieczna z punktu widzenia wydajności. Może służyć do uzyskania większej ilości miejsca.

Jeśli w przypadku usługi Azure Premium Storage wymagane są więcej IOPS, zalecamy używanie pul magazynu systemu Windows (dostępne tylko w systemie Windows Server 2012 i nowszych) do tworzenia jednego dużego urządzenia logicznego na wielu zainstalowanych dyskach. Takie podejście upraszcza zarządzanie miejscem na dysku i pozwala uniknąć wysiłku ręcznego rozpowszechniania plików na wielu zainstalowanych dyskach.


#### <a name="write-accelerator"></a>Akcelerator zapisu
W przypadku maszyn wirtualnych serii M opóźnienie zapisywane w dziennikach ponownego wykonywania w trybie online można zmniejszyć o czynniki w porównaniu do usługi Azure Premium Storage. Włącz akcelerator zapisu platformy Azure dla dysków (VHD) opartych na usłudze Azure Premium Storage, które są używane dla plików dziennika ponownego wykonywania w trybie online. Aby uzyskać więcej informacji, zobacz [Akcelerator zapisu](../../how-to-enable-write-accelerator.md). Lub Użyj usługi Azure Ultra Disk w przypadku woluminu dziennika ponownego wykonywania w trybie online.


### <a name="backuprestore"></a>Tworzenie/przywracanie kopii zapasowych
W przypadku funkcji tworzenia kopii zapasowej/przywracania narzędzia SAP BR * Tools for Oracle są obsługiwane w taki sam sposób, jak w standardowych systemach operacyjnych Windows Server. Oracle Recovery Manager (RMAN) jest również obsługiwany w przypadku tworzenia kopii zapasowych na dysku i przywracania z dysku.

Za pomocą Azure Backup można również uruchamiać kopie zapasowe maszyny wirtualnej spójnej na poziomie aplikacji. Artykuł [Planowanie infrastruktury kopii zapasowych maszyny wirtualnej na platformie Azure](../../../backup/backup-azure-vms-introduction.md) wyjaśnia, jak Azure Backup używa funkcji VSS systemu Windows do wykonywania kopii zapasowych spójnych na poziomie aplikacji. Wersje systemu Oracle DBMS obsługiwane przez system Azure przez SAP mogą korzystać z funkcji usługi VSS dla kopii zapasowych. Aby uzyskać więcej informacji, zobacz dokumentację programu Oracle — [podstawowe pojęcia związane z tworzeniem kopii zapasowych i odzyskiwaniem bazy danych za pomocą usługi VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Wysoka dostępność
Funkcja Oracle Data Guard jest obsługiwana w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Aby zapewnić automatyczną pracę w trybie failover w usłudze Data Guard, musisz używać Fast-Start trybu failover (FSFA). Obserwator (FSFA) wyzwala tryb failover. Jeśli nie korzystasz z FSFA, możesz użyć tylko ręcznej konfiguracji trybu failover.

Aby uzyskać więcej informacji na temat odzyskiwania po awarii dla baz danych Oracle na platformie Azure, zobacz [odzyskiwanie awaryjne dla Oracle Database bazy danych 12c w środowisku platformy Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Wydajniejsze sieci
W przypadku wdrożeń Oracle w systemie Windows zdecydowanie zalecamy szybsze korzystanie z sieci zgodnie z opisem w oknie [przyspieszonej sieci platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Należy również wziąć pod uwagę zalecenia dotyczące [wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). 
### <a name="other"></a>Inne
[Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md) zawiera inne ważne pojęcia związane z wdrożeniami maszyn wirtualnych z Oracle Database, w tym zestawy dostępności platformy Azure i monitorowanie SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Specyficzne dla Oracle Database na Oracle Linux
Oprogramowanie Oracle jest obsługiwane przez firmę Oracle do uruchamiania na Microsoft Azure z Oracle Linux jako system operacyjny gościa. Aby uzyskać więcej informacji na temat ogólnej obsługi funkcji Windows Hyper-V i platformy Azure, zobacz [często zadawane pytania dotyczące platformy Azure i programu Oracle](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Obsługiwany jest również konkretny scenariusz aplikacji SAP wykorzystujących bazy danych Oracle. Szczegóły zostały omówione w następnej części dokumentu.

### <a name="oracle-version-support"></a>Obsługa wersji Oracle
Aby uzyskać informacje o wersjach firmy Oracle i odpowiednich wersjach systemu operacyjnego na potrzeby uruchamiania oprogramowania SAP w programie Oracle na platformie Azure Virtual Machines, zobacz temat SAP Uwaga [2039619].

Ogólne informacje o uruchamianiu programu SAP Business Suite na platformie Oracle można znaleźć na [stronie społeczności programu SAP na platformie Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Wskazówki dotyczące konfiguracji oprogramowania SAP na maszynach wirtualnych platformy Azure w systemie Linux

Zgodnie z instrukcjami dotyczącymi instalacji SAP pliki związane z programem Oracle nie należy instalować ani znajdować w sterownikach systemowych dla dysku rozruchowego maszyny wirtualnej. Różne rozmiary maszyn wirtualnych obsługują różną liczbę podłączonych dysków. Mniejsze typy maszyn wirtualnych mogą obsługiwać mniejszą liczbę podłączonych dysków. 

W tym przypadku zalecamy zainstalowanie/znalezienie dysku macierzystego firmy Oracle, etapu,,,,, `saptrace` `saparch` `sapbackup` `sapcheck` lub `sapreorg` na dysk rozruchowy. Te części składników systemu Oracle DBMS nie są intensywne w przypadku przepływności we/wy i operacji we/wy. Oznacza to, że dysk systemu operacyjnego może obsłużyć wymagania we/wy. Domyślny rozmiar dysku systemu operacyjnego to 30 GB. Dysk rozruchowy można rozszerzyć przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Po rozwinięciu dysku rozruchowego można dodać dodatkową partycję dla plików binarnych firmy Oracle.


### <a name="storage-configuration"></a>Konfiguracja usługi Storage

Systemy plików ext4, XFS, NFSv 4.1 (tylko w Azure NetApp Files (ANF)) lub Oracle ASM (patrz uwagi SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619) do wymagań dotyczących wersji/wydania) są obsługiwane w przypadku plików Oracle Database na platformie Azure. Wszystkie pliki bazy danych muszą być przechowywane w tych systemach plików na podstawie dysków VHD, Managed Disks lub ANF. Te dyski są instalowane na maszynę wirtualną platformy Azure i są oparte na [usłudze Azure Page BLOB Storage](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs), [azure Managed disks](../../managed-disks-overview.md)lub [Azure NetApp Files](https://azure.microsoft.com/services/netapp/).

Lista wymagań minimalnych, takich jak: 

- W przypadku jądra Oracle Linux UEK wymagane jest co najmniej UEK w wersji 4 do obsługi [platformy Azure Premium dysków SSD](../../premium-storage-performance.md#disk-caching).
- W przypadku oprogramowania Oracle z ANF minimalny obsługiwany Oracle Linux to 8,2.
- W przypadku platformy Oracle z ANF Minimalna obsługiwana wersja programu Oracle to 19c (19.8.0.0)

Zapoznaj się z artykułem [typy magazynów platformy Azure dla obciążeń SAP](./planning-guide-storage.md) , aby uzyskać więcej szczegółów dotyczących konkretnych typów magazynów blokowych platformy Azure, które są odpowiednie dla obciążenia systemu DBMS

W przypadku korzystania z usługi Azure Block Storage zdecydowanie zaleca się używanie usługi Azure [Managed disks](../../managed-disks-overview.md) i dysków SSD w wersji [Premium](../../disks-types.md) na potrzeby wdrożeń Oracle Database.

Z wyjątkiem Azure NetApp Files, inne dyski udostępnione, dyski sieciowe lub udziały zdalne, takie jak usługi plików platformy Azure (AFS), nie są obsługiwane w przypadku plików Oracle Database. Aby uzyskać więcej informacji, zobacz następujące tematy: 

- [Introducing Microsoft Azure File Service](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service) (Wprowadzenie do usługi plików platformy Microsoft Azure)

- [Persisting connections to Microsoft Azure Files (Utrwalanie połączeń z plikami na platformie Microsoft Azure)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Jeśli używasz dysków opartych na usłudze Azure Page BLOB Storage lub Managed Disks, instrukcje dotyczące [wdrażania platformy azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń z Oracle Database.

Istnieją limity przepływności operacji we/wy na sekundę dla dysków platformy Azure. Omawiane koncepcje zostały omówione w temacie [zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). Dokładne przydziały zależą od używanego typu maszyny wirtualnej. Aby uzyskać listę typów maszyn wirtualnych z ich przydziałami, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][virtual-machines-sizes-linux].

Aby zidentyfikować obsługiwane typy maszyn wirtualnych platformy Azure, zobacz uwagi dotyczące oprogramowania SAP [1928533].

Minimalna konfiguracja:

| Składnik | Dysk | Buforowanie | Obcięcie |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA & mirrlogB | Premium, Ultra Disk lub ANF | Brak | Nie jest wymagany |
| /Oracle/ \<SID> /origlogaB & mirrlogA | Premium, Ultra Disk lub ANF | Brak | Nie jest wymagany |
| /Oracle/ \<SID> /sapdata1... Azotan | Premium, Ultra Disk lub ANF | Tylko odczyt | Może być używany w przypadku wersji Premium |
| /Oracle/ \<SID> /oraarch | Standardowa lub ANF | Brak | Nie jest wymagany |
| Strona główna firmy Oracle, `saptrace` ,... | Dysk systemu operacyjnego (Premium) | | Nie jest wymagany |

* Odcięcie: LVM lub MDADM przy użyciu RAID0

Wybór dysku do hostowania dzienników wykonywania w trybie online firmy Oracle powinien być oparty na wymaganiach IOPS. Możliwe jest przechowywanie wszystkich sapdata1... n (obszary tabel) na jednym zainstalowanym dysku tak długo, jak wolumin, operacje we/wy i przepływność spełniają wymagania. 

Konfiguracja wydajności:

| Składnik | Dysk | Buforowanie | Obcięcie |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA | Premium, Ultra Disk lub ANF | Brak | Może być używany w przypadku wersji Premium  |
| /Oracle/ \<SID> /origlogaB | Premium, Ultra Disk lub ANF | Brak | Może być używany w przypadku wersji Premium |
| /Oracle/ \<SID> /mirrlogAB | Premium, Ultra Disk lub ANF | Brak | Może być używany w przypadku wersji Premium |
| /Oracle/ \<SID> /mirrlogBA | Premium, Ultra Disk lub ANF | Brak | Może być używany w przypadku wersji Premium |
| /Oracle/ \<SID> /sapdata1... Azotan | Premium, Ultra Disk lub ANF | Tylko odczyt | Zalecane w przypadku wersji Premium  |
| /Oracle/ \<SID> /sapdata (n + 1) * | Premium, Ultra Disk lub ANF | Brak | Może być używany w przypadku wersji Premium |
| /Oracle/ \<SID> /oraarch * | Premium, Ultra Disk lub ANF | Brak | Nie jest wymagany |
| Strona główna firmy Oracle, `saptrace` ,... | Dysk systemu operacyjnego (Premium) | Nie jest wymagany |

* Odcięcie: LVM lub MDADM przy użyciu RAID0

* (n + 1): hostowanie systemowych, tymczasowych i COFAjących się tabel: wzorzec we/wy systemu i cofanie tabel przeszukanych różni się od innych tabel, które obsługują dane aplikacji. Żadne buforowanie nie jest najlepszą opcją dla wydajności systemu i cofania tabel.

* oraarch: Pula magazynów nie jest konieczna z punktu widzenia wydajności.


Jeśli podczas korzystania z usługi Azure Premium Storage wymagane są więcej IOPS, zalecamy użycie LVM (Menedżer woluminów logicznych) lub MDADM w celu utworzenia jednego dużego woluminu logicznego na wielu zainstalowanych dyskach. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md) dotyczące wskazówek i wskaźników dotyczących korzystania z LVM lub MDADM. Takie podejście upraszcza administrację zarządzaniem miejscem na dysku i pozwala uniknąć wysiłku ręcznego rozpowszechniania plików na wielu zainstalowanych dyskach.

Jeśli planujesz używać Azure NetApp Files upewnij się, że klient dNFS jest prawidłowo skonfigurowany. Korzystanie z programu dNFS jest obowiązkowe jako obsługiwane środowisko. Konfiguracja dNFS jest udokumentowana w artykule [tworzenie Oracle Database w bezpośrednim systemie plików NFS](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA).

Przykład przedstawiający użycie Azure NetApp Files opartych na systemie plików NFS dla baz danych Oracle znajduje się w blogu [wdrażanie oprogramowania SAP AnyDB (Oracle 19c) przy użyciu Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043).


#### <a name="write-accelerator"></a>Akcelerator zapisu
W przypadku maszyn wirtualnych z serii M platformy Azure w przypadku korzystania z usługi Azure akcelerator zapisu czas oczekiwania na zapis w dziennikach wykonywania w trybie online można zmniejszyć o czynniki używane w przypadku korzystania z usługi Azure Premium Storage. Włącz akcelerator zapisu platformy Azure dla dysków (VHD) opartych na usłudze Azure Premium Storage, które są używane dla plików dziennika ponownego wykonywania w trybie online. Aby uzyskać więcej informacji, zobacz [Akcelerator zapisu](../../how-to-enable-write-accelerator.md). Lub Użyj usługi Azure Ultra Disk w przypadku woluminu dziennika ponownego wykonywania w trybie online.


### <a name="backuprestore"></a>Tworzenie/przywracanie kopii zapasowych
W przypadku funkcji tworzenia kopii zapasowej/przywracania narzędzia SAP BR * Tools for Oracle są obsługiwane w taki sam sposób, jak na komputerach bez systemu operacyjnego i funkcji Hyper-V. Oracle Recovery Manager (RMAN) jest również obsługiwany w przypadku tworzenia kopii zapasowych na dysku i przywracania z dysku.

Aby uzyskać więcej informacji na temat korzystania z usług Azure Backup i Recovery Services na potrzeby tworzenia kopii zapasowych i odzyskiwania baz danych Oracle, zobacz [Tworzenie kopii zapasowej i odzyskiwanie bazy danych Oracle Database 12c na maszynie wirtualnej platformy Azure z systemem Linux](../oracle/oracle-overview.md).

### <a name="high-availability"></a>Wysoka dostępność
Funkcja Oracle Data Guard jest obsługiwana w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii. Aby uzyskać automatyczną pracę awaryjną w usłudze Data Guard, należy użyć Fast-Start trybu failover (FSFA). Funkcja obserwatora (FSFA) wyzwala tryb failover. Jeśli nie korzystasz z FSFA, możesz użyć tylko ręcznej konfiguracji trybu failover. Aby uzyskać więcej informacji, zobacz [Implementowanie funkcji Oracle Data Guard na maszynie wirtualnej platformy Azure z systemem Linux](../oracle/configure-oracle-dataguard.md).


Zagadnienia dotyczące odzyskiwania po awarii dla baz danych Oracle na platformie Azure zostały przedstawione w artykule [odzyskiwanie po awarii dla Oracle Database bazy danych 12c w środowisku platformy Azure](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Wydajniejsze sieci
Obsługa przyspieszonej sieci platformy Azure w Oracle Linux jest dostępna z Oracle Linux 7 Update 5 (Oracle Linux 7,5). Jeśli nie można uaktualnić do najnowszej wersji Oracle Linux 7,5, może wystąpić obejście przy użyciu jądra zgodnego z RedHat (RHCK) zamiast jądra UEK firmy Oracle. 

Używanie jądra RHEL w Oracle Linux jest obsługiwane zgodnie z [#1565179](https://launchpad.support.sap.com/#/notes/1565179)uwagi SAP. W przypadku usługi Azure przyspieszonej, minimalna wersja jądra RHCKL musi być 3.10.0-862.13.1. el7. Jeśli używasz jądra UEK w Oracle Linux w połączeniu z [usługą Azure przyspieszone sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), musisz użyć jądra Oracle UEK w wersji 5.

Jeśli maszyny wirtualne są wdrażane z obrazu, który nie jest oparty na witrynie Azure Marketplace, należy skopiować dodatkowe pliki konfiguracji do maszyny wirtualnej, uruchamiając następujący kod: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem 

- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md)
