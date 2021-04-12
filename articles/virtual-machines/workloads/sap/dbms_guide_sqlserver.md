---
title: SQL Server wdrożenie systemu Azure Virtual Machines DBMS dla obciążenia SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure, SQL Server, SAP, AlwaysOn
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05a0aeb43b13dc4db28ca8c56fc668756a2a4510
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258728"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver

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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




W tym dokumencie omówiono kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania SQL Server na potrzeby obciążeń SAP na platformie Azure IaaS. Jako warunek wstępny do tego dokumentu należy przeczytać [zagadnienia dotyczące dokumentu dotyczące wdrożenia systemu azure Virtual Machines DBMS dotyczące obciążeń SAP](./dbms_guide_general.md) i innych przewodników w [obciążeniu SAP w dokumentacji platformy Azure](./get-started.md). 



> [!IMPORTANT]
> Zakres tego dokumentu to wersja systemu Windows w SQL Server. System SAP nie obsługuje wersji systemu Linux SQL Server z żadnym oprogramowaniem SAP. Dokument nie zawiera omówienia Microsoft Azure SQL Database, czyli platformy jako oferty usługi platformy Microsoft Azure. W omówieniu tego dokumentu zawarto informacje na temat uruchamiania SQL Server produktu, który jest znany dla wdrożeń lokalnych w usłudze Azure Virtual Machines, wykorzystując infrastrukturę jako możliwości usługi platformy Azure. Możliwości i funkcje bazy danych między tymi dwoma ofertami są różne i nie należy ich mieszać ze sobą. Zobacz również: <https://azure.microsoft.com/services/sql-database/>
> 
>

Ogólnie rzecz biorąc, należy rozważyć użycie najnowszych wersji SQL Server do uruchamiania obciążeń SAP na platformie Azure IaaS. Najnowsze wersje SQL Server oferują lepszą integrację z niektórymi usługami i funkcjami platformy Azure. Lub mają zmiany, które optymalizują operacje w infrastrukturze usługi Azure IaaS.

Przed kontynuowaniem zalecamy zapoznanie się z artykułem [co to jest SQL Server w usłudze Azure Virtual Machines (Windows)] [ https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ].

W poniższych sekcjach są agregowane i wymieniane elementy części dokumentacji w powyższym łączu. Szczegóły dotyczące oprogramowania SAP są również wymienione, a niektóre koncepcje są opisane bardziej szczegółowo. Jednak zdecydowanie zaleca się przeprowadzenie powyższej dokumentacji powyżej przed przeczytaniem dokumentacji dotyczącej SQL Server.

Poniżej znajdują się pewne SQL Server informacje, które należy znać przed kontynuowaniem:

* **Obsługa wersji SQL**: w przypadku klientów SAP, SQL Server 2008 R2 i nowsze są obsługiwane na Microsoft Azure maszynie wirtualnej. Wcześniejsze wersje nie są obsługiwane. Zapoznaj się z ogólną [instrukcją obsługi](https://support.microsoft.com/kb/956893) , aby uzyskać więcej szczegółów. Ogólnie rzecz biorąc, SQL Server 2008 jest również obsługiwane przez firmę Microsoft. Jednak ze względu na znaczną funkcjonalność SAP, która została wprowadzona w SQL Server 2008 R2, SQL Server 2008 R2 jest minimalnym wydaniem dla oprogramowania SAP. Ogólnie rzecz biorąc, należy rozważyć użycie najnowszych wersji SQL Server do uruchamiania obciążeń SAP na platformie Azure IaaS. Najnowsze wersje SQL Server oferują lepszą integrację z niektórymi usługami i funkcjami platformy Azure. Lub mają zmiany, które optymalizują operacje w infrastrukturze usługi Azure IaaS. W związku z tym papier jest ograniczony do SQL Server 2016 i SQL Server 2017.
* **Wydajność SQL**: Microsoft Azure hostowane Virtual Machines działają dobrze w porównaniu z innymi ofertami wirtualizacji chmury publicznej, ale poszczególne wyniki mogą się różnić. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wydajności SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
* **Korzystanie z obrazów z portalu Azure Marketplace**: najszybszym sposobem wdrożenia nowej maszyny wirtualnej Microsoft Azure jest użycie obrazu z witryny Azure Marketplace. W portalu Azure Marketplace znajdują się obrazy zawierające najnowsze wersje SQL Server. Obrazy, w których zainstalowano SQL Server już nie mogą być używane dla aplikacji SAP NetWeaver. Przyczyną jest domyślne sortowanie SQL Server w tych obrazach, a nie sortowanie wymagane przez systemy SAP NetWeaver. Aby można było korzystać z takich obrazów, należy zapoznać się z krokami opisanymi w rozdziale [przy użyciu SQL Server obrazu z Microsoft Azure Marketplace][dbms-guide-5.6]. 
*  **SQL Server obsługę wielowystąpień w ramach jednej maszyny wirtualnej platformy Azure**: Ta metoda wdrażania jest obsługiwana. Należy jednak pamiętać o ograniczeniach zasobów, szczególnie w odróżnieniu od przepustowości sieci i magazynu używanego typu maszyny wirtualnej. Szczegółowe informacje są dostępne w artykule [rozmiary maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). Te ograniczenia przydziałów mogą uniemożliwiać zaimplementowanie tej samej architektury o wiele wystąpień, co można zaimplementować lokalnie. Zgodnie z konfiguracją i wpływem udostępniania zasobów dostępnych w ramach jednej maszyny wirtualnej należy wziąć pod uwagę takie same kwestie jak lokalne potrzeby.
*  **Wiele baz danych SAP w jednym wystąpieniu SQL Server na jednej maszynie wirtualnej**: jak powyżej, obsługiwane są konfiguracje, takie jak te. Zagadnienia dotyczące wielu baz danych SAP współużytkujących udostępnione zasoby jednego wystąpienia SQL Server są takie same jak w przypadku wdrożeń lokalnych. Dodatkowe utrzymywanie innych ograniczeń, takich jak liczba dysków, które mogą być połączone z konkretnym typem maszyny wirtualnej. Lub limity przydziału sieci i magazynu dla określonych typów maszyn wirtualnych jako szczegółowe [rozmiary maszyn wirtualnych na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Zalecenia dotyczące struktury maszyn wirtualnych/VHD dla wdrożeń SQL Server związanych z systemem SAP
Zgodnie z ogólnym opisem, system operacyjny, SQL Server pliki wykonywalne i w przypadku systemów SAP 2-warstwowych, pliki wykonywalne SAP powinny być zlokalizowane lub zainstalowane oddzielne dyski platformy Azure. Zazwyczaj większość baz danych systemu SQL Server nie jest używana na wysokim poziomie przez obciążenie SAP NetWeaver. Niemniej jednak systemowe bazy danych SQL Server (Master, msdb i model) powinny być razem z innymi katalogami SQL Server na osobnym dysku platformy Azure. SQL Server tempdb powinna znajdować się na nonperisisted D:\ dysk lub na osobnym dysku.


* Wszystkie typy maszyn wirtualnych z certyfikatem SAP (patrz Uwaga: [1928533]), z wyjątkiem maszyn wirtualnych serii a, danych tempdb i plików dziennika, można umieścić na nieutrwalonym D:\ litera. 
* W przypadku starszych wersji SQL Server, w których SQL Server domyślnie instaluje bazę danych tempdb z jednym plikiem z danymi, zaleca się używanie wielu plików danych tempdb. D:\ woluminy dysków różnią się w zależności od typu maszyny wirtualnej. Dokładne rozmiary D:\ na dysku różnych maszyn wirtualnych Sprawdź [rozmiary artykułów dla maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md).

Te konfiguracje umożliwiają użycie w bazie danych tempdb większej ilości miejsca i większej liczby operacji we/wy na sekundę, niż na dysku systemowym. Nietrwały D:\ dysk oferuje również lepsze opóźnienia we/wy i przepływność (z wyjątkiem maszyn wirtualnych serii A). Aby określić odpowiedni rozmiar bazy danych tempdb, można sprawdzić rozmiary tempdb w istniejących systemach. 

>[!NOTE]
> w przypadku umieszczania plików danych tempdb i pliku dziennika w folderze D:\ dysk, który został utworzony, należy upewnić się, że folder istnieje po ponownym uruchomieniu maszyny wirtualnej. Ponieważ D:\ dysk jest świeżo zainicjowany po ponownym uruchomieniu maszyny wirtualnej wszystkie pliki i struktury katalogów zostały wyczyszczone. Możliwość ponownego tworzenia struktur katalogów ostatecznych na D:\ dysk przed uruchomieniem usługi SQL Server jest udokumentowany w [tym artykule](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Konfiguracja maszyny wirtualnej, która działa SQL Server z bazą danych SAP oraz miejsce, w której dane bazy danych tempdb i plik dziennika tempdb są umieszczane na D:\ dysk będzie wyglądać następująco:

![Diagram konfiguracji prostego dysku maszyny wirtualnej dla SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Na powyższym diagramie wyświetlana jest prosta wielkość liter. Jako eluded w artykule [zagadnienia dotyczące wdrażania systemu azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md), typ usługi Azure Storage, liczba i rozmiar dysków są zależne od różnych czynników. Ale ogólnie zalecamy:

- Użycie jednego dużego woluminu, który zawiera pliki danych SQL Server. Przyczyną tej konfiguracji jest fakt, że w rzeczywistości istnieje wiele baz danych SAP z różnymi plikami baz danych z różnymi obciążeniami we/wy.
- Użyj D:\drive dla bazy danych tempdb, o ile wydajność jest wystarczająca. Jeśli całkowite obciążenie jest ograniczone w wydajności przez bazę danych tempdb znajdujące się w D:\ Stacja dysków może być konieczna do przeniesienia bazy danych tempdb do oddzielenia usługi Azure Premium Storage lub dysków Ultra Disk zgodnie z zaleceniami w [tym artykule](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).


### <a name="special-for-m-series-vms"></a>Specjalne dla maszyn wirtualnych z serii M
W przypadku maszyny wirtualnej z serii M na platformie Azure opóźnienie zapisywania w dzienniku transakcji można zmniejszyć o czynniki w porównaniu z wydajnością Premium Storage platformy Azure, korzystając z usługi Azure akcelerator zapisu. W związku z tym należy wdrożyć akcelerator zapisu platformy Azure dla dysków VHD, które tworzą wolumin dla SQL Server dziennika transakcji. Szczegóły można odczytać w dokumencie [Akcelerator zapisu](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Formatowanie dysków
W przypadku SQL Server rozmiar bloku systemu plików NTFS dla dysków zawierających SQL Server danych i plików dziennika powinien wynosić 64 KB. Nie ma potrzeby formatowania D:\ litera. Ten dysk jest wstępnie sformatowany.

Aby upewnić się, że przywracanie lub tworzenie baz danych nie inicjuje plików danych przez zero zawartości plików, należy upewnić się, że kontekst użytkownika, w którym działa usługa SQL Server, ma określone uprawnienie. Te uprawnienia są zwykle użytkownicy w grupie administratorów systemu Windows. Jeśli usługa SQL Server jest uruchomiona w kontekście użytkownika niebędącego administratorem systemu Windows, należy przypisać temu użytkownikowi prawo **wykonywanie zadań konserwacji woluminu**.  Zobacz szczegóły w tym artykule z bazy wiedzy Microsoft Knowledge Base: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość operacji we/wy może stać się czynnikem ograniczającym, każda miara, która zmniejsza liczbę operacji wejścia/wyjścia, może pomóc w rozciągnięciu obciążenia. W związku z tym, jeśli jeszcze tego nie zrobiono, zastosowanie kompresji SQL Server strony jest zalecane przez oprogramowanie SAP i Microsoft przed przekazaniem istniejącej bazy danych SAP do platformy Azure.

Zaleca się wykonanie kompresji bazy danych przed przekazaniem do platformy Azure z dwóch powodów:

* Ilość przekazanych danych jest niższa.
* Czas trwania wykonywania kompresji jest krótszy, przy założeniu, że jedna z nich może korzystać z silniejszego sprzętu z większą liczbą procesorów CPU lub wyższą przepustowością operacji we/wy lub mniejszą zwłoką we/wy w środowisku lokalnym.
* Mniejsze rozmiary baz danych mogą prowadzić do mniejszej ilości kosztów alokacji dysku

Kompresja bazy danych działa również w Virtual Machines platformy Azure, ponieważ jest ona lokalna. Aby uzyskać więcej informacji na temat kompresowania istniejących baz danych programu SAP NetWeaver SQL Server, zapoznaj się z artykułem [udoskonalone narzędzie do kompresji SAP MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 i nowsze — przechowywanie plików bazy danych bezpośrednio na platformie Azure Blob Storage
SQL Server 2014 i nowsze wersje otwierają możliwość przechowywania plików bazy danych bezpośrednio w magazynie obiektów blob platformy Azure bez "otoki" otaczającego dysku VHD. Szczególnie w przypadku korzystania z usługi Azure Storage w warstwie Standardowa lub mniejszych typów maszyn wirtualnych ten typ wdrożenia umożliwia scenariusze, w których można przezwyciężyć limity liczby operacji we/wy, które będą wymuszane przez ograniczoną liczbę dysków, które można zainstalować na mniejszych typach maszyn wirtualnych. Ten sposób wdrożenia działa w przypadku baz danych użytkowników nie dotyczy jednak systemowych baz danych programu SQL Server. Działa również w przypadku plików danych i dzienników SQL Server. Jeśli chcesz wdrożyć bazę danych SAP SQL Server w ten sposób zamiast "Zawijaj" do dysków VHD, pamiętaj o następujących kwestiach:

* Używane konto magazynu musi znajdować się w tym samym regionie świadczenia usługi Azure, który jest używany do wdrożenia maszyny wirtualnej SQL Server jest uruchomiony w systemie.
* Wymienione wcześniej zagadnienia dotyczące dystrybucji dysków VHD za pośrednictwem różnych kont usługi Azure Storage dotyczą również tej metody wdrożeń. Oznacza liczbę operacji we/wy względem limitów konta usługi Azure Storage.
* Zamiast obsłużyć limit przydziału operacji we/wy magazynu maszyny wirtualnej, ruch związany z obiektami blob magazynu reprezentującymi SQL Server plików danych i dziennika będzie uwzględniać przepustowość sieci maszyny wirtualnej określonego typu maszyny wirtualnej. Aby uzyskać przepustowość sieci i magazynu dla określonego typu maszyny wirtualnej, zapoznaj się z artykułem [rozmiary dotyczące maszyn wirtualnych z systemem Windows na platformie Azure](../../sizes.md).
* W wyniku wypchnięcia operacji we/wy na plikach przez przydziały sieci nastąpi przekroczenie limitu przydziału pamięci masowej i przy użyciu ogólnej przepustowości maszyny wirtualnej tylko częściowo.
* Cele operacji we/wy i przepływności wydajności operacji wejścia/wyjścia, które nie są już stosowane dla systemu Azure Premium Storage dla różnych rozmiarów dysków. Nawet jeśli utworzone obiekty blob znajdują się na platformie Azure Premium Storage. Cele są udokumentowane w artykule [o wysokiej wydajności Premium Storage i dyskach zarządzanych dla maszyn wirtualnych](../../disks-types.md#premium-ssd). W wyniku umieszczania plików danych SQL Server i plików dziennika bezpośrednio w obiektach Blob przechowywanych w usłudze Azure Premium Storage charakterystyki wydajności mogą być różne w porównaniu z dyskami VHD w Premium Storage platformy Azure.
* Buforowanie oparte na hoście jako dostępne dla dysków usługi Azure Premium Storage nie jest dostępne podczas umieszczania SQL Server plików danych bezpośrednio w obiektach Blob platformy Azure.
* W przypadku maszyn wirtualnych z serii M usługa Azure akcelerator zapisu nie może być używana do obsługi zapisów podmilisekundów względem SQL Server pliku dziennika transakcji. 

Szczegółowe informacje o tych funkcjach można znaleźć w artykule [SQL Server pliki danych w Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)

Zalecenie dla systemów produkcyjnych polega na uniknięciu tej konfiguracji i raczej wyborze miejsc SQL Server danych i plików dziennika na platformie Azure Premium Storage VHD zamiast bezpośrednio z obiektów blob platformy Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Rozszerzenie puli buforów SQL Server 2014
W SQL Server 2014 wprowadzono nową funkcję o nazwie [rozszerzenie puli buforów](/sql/database-engine/configure-windows/buffer-pool-extension). Ta funkcja rozszerza pulę buforów SQL Server, która jest przechowywana w pamięci z pamięcią podręczną drugiego poziomu, która jest obsługiwana przez lokalną dysków SSD serwera lub maszyny wirtualnej. Rozszerzenie puli buforów umożliwia utrzymywanie większego roboczego zestawu danych w pamięci. W porównaniu z dostępem do usługi Azure Standard Storage dostęp do rozszerzenia puli buforów, który jest przechowywany w lokalnej dysków SSD maszyny wirtualnej platformy Azure, jest szybszy. Porównanie rozszerzenia puli buforów z usługą Azure Premium Storage odczyt pamięci podręcznej, zgodnie z zaleceniami SQL Server plików danych, nie są oczekiwane żadne znaczące korzyści dla rozszerzeń puli buforów. Przyczyną jest to, że obie pamięci podręczne (SQL Server rozszerzenia puli buforów i Premium Storage pamięci podręcznej odczytu) używają dysków lokalnych w węźle obliczeniowym platformy Azure.

Środowiska zdobyte w międzyczasie z rozszerzeniem puli buforów SQL Server z obciążeniem SAP są mieszane i nadal nie zezwalają na jednoznaczne zalecenia dotyczące tego, czy mają być używane we wszystkich przypadkach. Idealnym przypadkiem jest to, że zestaw roboczy, który wymaga aplikacji SAP, mieści się w pamięci głównej. Dzięki platformie Azure w przypadku maszyn wirtualnych, które mają do 4 TB pamięci, powinno być osiągalne, aby zachować zestaw roboczy w pamięci. W związku z tym użycie rozszerzenia puli buforów jest ograniczone do niektórych rzadkich przypadków i nie powinno być większością małych liter.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Uwagi dotyczące tworzenia kopii zapasowej/odzyskiwania SQL Server
Podczas wdrażania SQL Server na platformie Azure należy przejrzeć swoją metodologię tworzenia kopii zapasowych. Nawet jeśli system nie jest systemem produkcyjnym, należy regularnie wykonywać kopie zapasowe bazy danych SAP hostowanej przez SQL Server. Ponieważ usługa Azure Storage przechowuje trzy obrazy, kopia zapasowa jest teraz mniej ważna w przypadku kompensacji awarii magazynu. Powód, dla którego należy zachować właściwy plan tworzenia kopii zapasowych i odzyskiwania, to więcej informacji o błędach logicznych/ręcznych przez zapewnienie możliwości odzyskiwania do punktu w czasie. W związku z tym celem jest użycie kopii zapasowych do przywrócenia bazy danych z powrotem do określonego punktu w czasie lub do użycia kopii zapasowych na platformie Azure do wypełniania innego systemu przez skopiowanie istniejącej bazy danych. 

Aby zapoznać się z różnymi SQL Server możliwościami tworzenia kopii zapasowych na platformie Azure, przeczytaj artykuł [Tworzenie kopii zapasowych i przywracanie w ramach SQL Server na platformie azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). Artykuł obejmuje kilka różnych możliwości.

### <a name="manual-backups"></a>Ręczne kopie zapasowe
Istnieje kilka możliwości wykonywania kopii zapasowych "ręcznych" według:

1. Wykonywanie konwencjonalnych SQL Server kopii zapasowych na podłączonych bezpośrednio dyskach platformy Azure. Ta metoda ma zaletę, że kopie zapasowe są dostępne szybko do odświeżenia systemu i tworzą nowe systemy jako kopie istniejących systemów SAP
2.  SQL Server 2012 CU4 i nowsze mogą tworzyć kopie zapasowe baz danych w adresie URL usługi Azure Storage.
3.  File-Snapshot kopie zapasowe plików bazy danych w usłudze Azure Blob Storage. Ta metoda działa tylko wtedy, gdy SQL Server danych i plików dziennika znajdują się w usłudze Azure Blob Storage

Pierwsza metoda jest dobrze znana i stosowana w wielu przypadkach w lokalnym świecie. Niemniej jednak opuszcza zadanie, aby rozwiązać dłuższy termin lokalizacji kopii zapasowej. Ponieważ nie chcesz przechowywać kopii zapasowych przez 30 lub więcej dni w lokalnie dołączonym magazynie Azure, musisz użyć usług Azure Backup Services lub innego narzędzia do tworzenia kopii zapasowych/odzyskiwania innych firm, które obejmuje zarządzanie dostępem i przechowywaniem kopii zapasowych. Możesz też utworzyć duży serwer plików na platformie Azure przy użyciu funkcji miejsca do magazynowania systemu Windows.

Druga metoda została opisana bliżej w artykule [SQL Server kopia zapasowa na adres URL](../../../azure-sql/virtual-machines/windows/backup-restore.md). Różne wersje SQL Server mają kilka odmian w tej funkcji. W związku z tym należy zapoznać się z dokumentacją dotyczącą konkretnego sprawdzenia wersji SQL Server. Należy pamiętać, że w tym artykule wymieniono liczne ograniczenia. Można wykonać kopię zapasową z:

- Jeden obiekt BLOB na stronie platformy Azure, który następnie ogranicza rozmiar kopii zapasowej do 1000 GB. To ograniczenie ogranicza przepływność, którą można osiągnąć.
- Wiele (do 64) blokowych obiektów blob platformy Azure, które umożliwiają teoretyczny rozmiar kopii zapasowej wynoszący 12 TB. Jednak testy z bazami danych klienta wykazały, że maksymalny rozmiar kopii zapasowej może być mniejszy niż jego teoretyczny limit. W takim przypadku użytkownik jest odpowiedzialny za zarządzanie przechowywaniem kopii zapasowych i dostępem do kopii zapasowych.


### <a name="automated-backup-for-sql-server"></a>Zautomatyzowane tworzenie kopii zapasowej programu SQL Server
Automatyczne kopie zapasowe zapewniają usługę automatycznego tworzenia kopii zapasowych dla SQL Server Standard i wersji Enterprise działających na maszynie wirtualnej z systemem Windows na platformie Azure. Ta usługa jest świadczona przez [SQL Server rozszerzenie agenta IaaS](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md), które jest instalowane automatycznie na SQL Server obrazów maszyn wirtualnych z systemem Windows w Azure Portal. W przypadku wdrażania własnych obrazów systemu operacyjnego z zainstalowanym SQL Server należy oddzielnie zainstalować rozszerzenia maszyn wirtualnych. W tym [artykule](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)opisano kroki, które należy wykonać.

Więcej informacji na temat możliwości tej metody można znaleźć w następujących artykułach:

- SQL Server 2014: [Automatyczna kopia zapasowa dla SQL Server 2014 Virtual Machines (Menedżer zasobów)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017: [Automatyczna kopia zapasowa v2 dla Virtual Machines platformy Azure (Menedżer zasobów)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

Zapoznaj się z dokumentacją, aby dowiedzieć się, że funkcje z nowszymi wersjami SQL Server udoskonalone. Więcej informacji o SQL Server zautomatyzowanych kopii zapasowych znajduje się w artykule [SQL Server zarządzanej kopii zapasowej do Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure). Teoretyczny limit rozmiaru kopii zapasowej wynosi 12 TB.  Automatyczne kopie zapasowe mogą być dobrą metodą tworzenia kopii zapasowych o rozmiarze do 12 TB. Ponieważ wiele obiektów BLOB jest jednocześnie zapisywana, można oczekiwać, że przepływność przekracza 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup SQL Server maszyn wirtualnych
Ta nowa metoda SQL Server tworzenia kopii zapasowych jest oferowana z czerwca 2018 jako publiczna wersja zapoznawcza usługi Azure Backup Services. Metoda tworzenia kopii zapasowych SQL Server jest taka sama jak w przypadku innych narzędzi innych firm, a mianowicie SQL Server interfejsu VSS/VDI do przesyłania strumieniowego kopii zapasowych do lokalizacji docelowej. W takim przypadku lokalizacja docelowa to magazyn usługi Azure Recovery.

Bardziej szczegółowy opis tej metody tworzenia kopii zapasowej, który dodaje wiele zalet scentralizowanych konfiguracji, monitorowania i administracji kopii zapasowej, jest dostępny [tutaj](../../../backup/backup-azure-sql-database.md). 


### <a name="third-party-backup-solutions"></a>Rozwiązania do tworzenia kopii zapasowych innych firm
W przypadku korzystania z wielu klientów SAP nie było możliwości rozpoczęcia od początku i wprowadzenia nowych rozwiązań do tworzenia kopii zapasowych, które zostały wykonane na platformie Azure. W związku z tym istniejące rozwiązania do tworzenia kopii zapasowych, które są konieczne do użycia i rozszerzone na platformę Azure. Rozszerzanie istniejących rozwiązań do tworzenia kopii zapasowych na platformę Azure zwykle działało najlepiej z większością głównych dostawców w tym miejscu. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Używanie SQL Server obrazu z Microsoft Azure Marketplace
Firma Microsoft oferuje maszyny wirtualne w portalu Azure Marketplace, które zawierają już wersje SQL Server. W przypadku klientów SAP, którzy wymagają licencji dla SQL Server i systemu Windows, korzystanie z tych obrazów może stanowić okazję do pokrycia potrzebnych licencji przez nawirowanie maszyn wirtualnych z SQL Server już zainstalowanymi. Aby można było używać takich obrazów dla oprogramowania SAP, należy wykonać następujące zagadnienia:

* SQL Server wersje nieewaluacyjne uzyskują wyższe koszty niż maszyna wirtualna "tylko Windows" wdrożona w witrynie Azure Marketplace. Zobacz następujące artykuły, aby porównać ceny: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> i <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> . 
* Można używać tylko wydań SQL Server, które są obsługiwane przez system SAP.
* Sortowanie wystąpienia SQL Server, które jest instalowane na maszynach wirtualnych oferowanych w portalu Azure Marketplace, nie jest sortowaniem SAP NetWeaver wymaga uruchomienia wystąpienia SQL Server. Sortowanie można zmienić, korzystając z instrukcji w poniższej sekcji.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Zmienianie SQL Server sortowania maszyny wirtualnej Microsoft Windows/SQL Server
Ponieważ obrazy SQL Server w portalu Azure Marketplace nie są skonfigurowane do używania sortowania, które jest wymagane przez aplikacje SAP NetWeaver, należy je zmienić bezpośrednio po wdrożeniu. W przypadku SQL Server ta zmiana sortowania może wykonać następujące kroki, gdy tylko maszyna wirtualna została wdrożona, a administrator będzie mógł zalogować się do wdrożonej maszyny wirtualnej:

* Otwórz okno poleceń systemu Windows, jako administrator.
* Zmień katalog na C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Wykonaj polecenie: Setup.exe/QUIET/ACTION = REBUILDDATABASE/INSTANCENAME = MSSQLSERVER/SQLSYSADMINACCOUNTS = `<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> to konto, które zostało zdefiniowane jako konto administratora podczas pierwszego wdrażania maszyny wirtualnej za pomocą galerii.

Proces powinien trwać tylko kilka minut. Aby upewnić się, że krok zakończył się z prawidłowym wynikiem, wykonaj następujące czynności:

* Otwórz program SQL Server Management Studio.
* Otwórz okno zapytania.
* Wykonaj polecenie sp_helpsort w bazie danych Master SQL Server.

Żądany wynik powinien wyglądać następująco:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Jeśli wynik jest inny, Zatrzymaj wdrażanie SAP i sprawdź, dlaczego polecenie instalacji nie działa zgodnie z oczekiwaniami. Wdrożenie aplikacji SAP NetWeaver na wystąpienie SQL Server z różnymi SQL Server CodePage **nie** jest obsługiwane.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server High-Availability dla oprogramowania SAP na platformie Azure
Za pomocą SQL Server w wdrożeniach IaaS platformy Azure dla oprogramowania SAP masz kilka różnych możliwości do dodania w celu wdrożenia warstwy DBMS o wysokiej dostępności. Zgodnie z opisem w temacie [zagadnienia dotyczące wdrażania systemu azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md) już istnieje, platforma Azure udostępnia różne umowy SLA w czasie dla jednej maszyny wirtualnej i parę maszyn wirtualnych wdrożonych w zestawie dostępności platformy Azure. Przyjęto założenie, że na bieżąco podano umowę SLA dla wdrożeń produkcyjnych, które wymagają wdrożenia w zestawach dostępności platformy Azure. W takim przypadku należy wdrożyć co najmniej dwie maszyny wirtualne w tym zestawie dostępności. Dla jednej maszyny wirtualnej zostanie uruchomione wystąpienie usługi Active SQL Server. Na drugiej maszynie wirtualnej zostanie uruchomione wystąpienie pasywne

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>SQL Server klastrowanie przy użyciu serwera plików skalowalnego w poziomie systemu Windows lub dysku udostępnionego platformy Azure
W systemie Windows Server 2016 firma Microsoft wprowadziła [bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/storage-spaces-direct-overview). W oparciu o wdrożenie Bezpośrednie miejsca do magazynowania usługa SQL Server FCI jest obsługiwana ogólnie. Platforma Azure oferuje również [dyski udostępnione platformy Azure](../../disks-shared-enable.md?tabs=azure-cli) , które mogą być używane na potrzeby klastrowania systemu Windows. W przypadku obciążeń SAP nie obsługujemy tych opcji HA. 

### <a name="sql-server-log-shipping"></a>Wysyłanie dziennika SQL Server
Jedną z metod wysokiej dostępności jest SQL Server wysyłania dziennika. Jeśli maszyny wirtualne uczestniczące w konfiguracji HA mają działające rozpoznawanie nazw, nie ma problemu. Instalacja na platformie Azure nie różni się od żadnej instalacji wykonywanej lokalnie w celu skonfigurowania dostarczania dzienników oraz zasad dotyczących wysyłania dzienników. Szczegóły dotyczące wysyłania dzienników SQL Server można znaleźć w artykule [dotyczącym wysyłania dziennika (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server).

Funkcja dostarczania dzienników SQL Server była twardo używana na platformie Azure w celu zapewnienia wysokiej dostępności w ramach jednego regionu świadczenia usługi Azure. Jednak w następujących scenariuszach klienci SAP korzystają z funkcji wysyłania dzienników w połączeniu z platformą Azure:

- Scenariusze odzyskiwania po awarii z jednego regionu świadczenia usługi Azure w innym regionie świadczenia usługi Azure
- Konfiguracja odzyskiwania po awarii z lokalizacji lokalnej do regionu platformy Azure
- Przecinanie scenariuszy z lokalnego do platformy Azure. W takich przypadkach wysyłanie dziennika służy do synchronizowania nowego wdrożenia systemu DBMS na platformie Azure z trwającym systemem produkcyjnym lokalnym. W momencie rozmieszczenia w systemie produkcja jest zamykana i należy upewnić się, że ostatnie i najnowsze kopie zapasowe dziennika transakcji zostały przeniesione do wdrożenia systemu Azure DBMS. Następnie wdrożenie systemu Azure DBMS jest otwierane w środowisku produkcyjnym.  



### <a name="database-mirroring"></a>Dublowanie baz danych
Funkcja dublowania baz danych obsługiwana przez oprogramowanie SAP (patrz Uwaga dla oprogramowania SAP [965908]) opiera się na definiowaniu partnera trybu failover w parametrach połączenia SAP. W przypadku przypadków obejmujących wiele lokalizacji należy założyć, że dwie maszyny wirtualne znajdują się w tej samej domenie i że kontekst użytkownika ma dwa wystąpienia SQL Server są uruchamiane w ramach użytkownika domeny i ma odpowiednie uprawnienia w dwóch wystąpieniach SQL Server. W związku z tym Konfiguracja funkcji dublowania baz danych na platformie Azure nie różni się od standardowej instalacji lokalnej/konfiguracji.

W przypadku wdrożeń Cloud-Only najłatwiej jest skonfigurować inną konfigurację domeny na platformie Azure w celu posiadania tych maszyn wirtualnych systemu DBMS (i najlepiej dedykowanych maszyn wirtualnych SAP) w jednej domenie.

Jeśli domena nie jest możliwa, można także użyć certyfikatów dla punktów końcowych dublowania bazy danych, jak opisano tutaj: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Samouczek służący do konfigurowania funkcji dublowania baz danych na platformie Azure można znaleźć tutaj: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Opcja Zawsze włączone programu SQL Server
Usługa as Always On jest obsługiwana w przypadku lokalnego rozwiązania SAP (patrz Uwaga dla oprogramowania SAP [1772688]), ale jest obsługiwana w połączeniu z oprogramowaniem SAP na platformie Azure. Istnieją pewne specjalne zagadnienia dotyczące wdrażania odbiornika grupy dostępności SQL Server (nie należy mylić z zestawem dostępności platformy Azure), ponieważ platforma Azure w tym momencie nie zezwala na tworzenie obiektu usługi AD/DNS, ponieważ jest to możliwe lokalnie. W związku z tym niektóre różne kroki instalacji są niezbędne do pokonania konkretnego zachowania platformy Azure.

Niektóre zagadnienia dotyczące korzystania z odbiornika grupy dostępności są następujące:

* Używanie odbiornika grupy dostępności jest możliwe tylko w systemie Windows Server 2012 lub nowszym jako system operacyjny gościa maszyny wirtualnej. W przypadku systemu Windows Server 2012 należy upewnić się, że ta poprawka jest stosowana: <https://support.microsoft.com/kb/2854082> 
* W przypadku systemu Windows Server 2008 R2 ta poprawka nie istnieje i zawsze musi być używana w taki sam sposób, jak dublowanie bazy danych przez określenie partnera trybu failover w ciągu połączeń (za pośrednictwem ustawienia domyślnego SAP. pfl parametru baz danych//////////Server-zobacz uwagi SAP [965908]).
* W przypadku korzystania z odbiornika grupy dostępności, maszyny wirtualne bazy danych muszą być połączone z dedykowaną Load Balancer. Aby uniknąć, że platforma Azure przypisze nowe adresy IP w przypadkach, gdy obie maszyny wirtualne są wyłączone, należy przypisać statyczne adresy IP do interfejsów sieciowych tych maszyn wirtualnych w konfiguracji zawsze włączone (definiowanie statycznego adresu IP jest opisany w [tym][virtual-networks-reserved-private-ip] artykule)
* Podczas kompilowania konfiguracji klastra usługi WSFC należy wykonać specjalne czynności, w przypadku których klaster wymaga przypisanych specjalnych adresów IP, ponieważ platforma Azure z jego bieżącą funkcją przypisze nazwę klastra tego samego adresu IP co węzeł, w którym jest tworzony klaster. To zachowanie oznacza, że należy wykonać ręczny krok w celu przypisania innego adresu IP do klastra.
* Odbiornik grupy dostępności zostanie utworzony na platformie Azure z punktami końcowymi protokołu TCP/IP przypisanymi do maszyn wirtualnych, na których są uruchomione repliki podstawowej i pomocniczej grupy dostępności.
* Być może istnieje potrzeba zabezpieczenia tych punktów końcowych przy użyciu list ACL.

Szczegółowa dokumentacja dotycząca wdrażania zawsze włączona z SQL Server na maszynach wirtualnych platformy Azure, takich jak:

- [Wprowadzenie SQL Server zawsze dostępnych grup dostępności na maszynach wirtualnych platformy Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
- [Skonfiguruj zawsze włączona Grupa dostępności na maszynach wirtualnych platformy Azure w różnych regionach](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
- [Skonfiguruj moduł równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

>[!NOTE]
> W przypadku konfigurowania modułu równoważenia obciążenia platformy Azure dla wirtualnego adresu IP odbiornika grupy dostępności upewnij się, że DirectServerReturn jest skonfigurowany. Skonfigurowanie tej opcji spowoduje zmniejszenie opóźnienia podróży sieci między warstwą aplikacji SAP a warstwą DBMS. 

SQL Server zawsze włączona to najpopularniejsze funkcje wysokiej dostępności i odzyskiwania po awarii używane na platformie Azure na potrzeby wdrożeń obciążeń SAP. Większość klientów zawsze korzysta z wysokiej dostępności w ramach jednego regionu świadczenia usługi Azure. Jeśli wdrożenie jest ograniczone tylko do dwóch węzłów, dostępne są dwie opcje łączności:

- Korzystanie z odbiornika grupy dostępności. W przypadku odbiornika grupy dostępności wymagane jest wdrożenie modułu równoważenia obciążenia platformy Azure. W ten sposób jest to domyślna metoda wdrażania. Aplikacje SAP można skonfigurować do nawiązywania połączeń z odbiornikiem grupy dostępności, a nie z pojedynczym węzłem
- Używanie parametrów łączności SQL Server dublowania baz danych. W takim przypadku należy skonfigurować łączność aplikacji SAP w sposób, w którym nazwy obu węzłów mają nazwę. Dokładne szczegóły dotyczące takiej konfiguracji po stronie SAP zostały udokumentowane w temacie SAP uwagi [#965908](https://launchpad.support.sap.com/#/notes/965908). Korzystając z tej opcji, nie trzeba konfigurować odbiornika grupy dostępności. I bez modułu równoważenia obciążenia platformy Azure dla SQL Server wysokiej dostępności. W związku z tym opóźnienie sieci między warstwą aplikacji SAP a warstwą DBMS jest niższe, ponieważ ruch przychodzący do wystąpienia SQL Server nie jest kierowany przez moduł równoważenia obciążenia platformy Azure. Jednak ta opcja działa tylko wtedy, gdy ograniczasz grupę dostępności, aby obejmowała dwa wystąpienia. 

Całkiem kilku klientów korzystających SQL Server zawsze z funkcjonalnością funkcji odzyskiwania po awarii między regionami platformy Azure. Kilku klientów korzysta również z możliwości wykonywania kopii zapasowych z repliki pomocniczej. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Transparent Data Encryption
Istnieje wielu klientów, którzy korzystają z programu SQL Server [transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) podczas wdrażania ich baz danych SQL Server SAP na platformie Azure. SQL Server funkcje TDE są w pełni obsługiwane przez SAP (Zobacz uwagi dotyczące oprogramowania SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Zastosowanie SQL Server TDE
W przypadku przeprowadzania niejednorodnej migracji z innego systemu DBMS działającego lokalnie w systemie Windows/SQL Server działającym na platformie Azure należy utworzyć pustą docelową bazę danych w SQL Server przed czasem. W następnym kroku należy zastosować SQL Server funkcje TDE. Mimo że system produkcyjny nadal działa lokalnie. Przyczyna, którą chcesz wykonać w tej sekwencji, polega na tym, że proces szyfrowania pustej bazy danych może zająć trochę czasu. Procesy importowania SAP zaimportują dane do zaszyfrowanej bazy danych w fazie przestoju. Obciążenie związane z importowaniem do zaszyfrowanej bazy danych ma w sposób niższy wpływ niż szyfrowanie bazy danych po fazie eksportu w fazie wyłączania. Podczas próby zastosowania TDE z obciążeniem SAP uruchomionym w bazie danych nie wprowadzono żadnych negatywnych środowisk. Dlatego zaleca się traktowanie wdrożenia TDE jako działania, które należy wykonać bez obciążenia SAP w danej bazie danych.

W przypadku przenoszenia baz danych SAP SQL Server ze środowiska lokalnego na platformę Azure zalecamy Testowanie infrastruktury, w której można zastosować szyfrowanie. W tym celu należy pamiętać o następujących faktach:

- Nie można zdefiniować liczby wątków używanych do zastosowania szyfrowania danych w bazie danych. Liczba wątków jest zależna od liczby woluminów dysków, do których są dystrybuowane SQL Server danych i plików dziennika. Oznacza bardziej odrębne woluminy (litery dysku), tym większa liczba wątków zostanie przełączona równolegle w celu przeprowadzenia szyfrowania. Taka konfiguracja jest niezależna od bitu z wcześniejszą sugestią konfiguracji dysku podczas tworzenia jednej lub mniejszej liczby miejsc do magazynowania dla plików bazy danych SQL Server na maszynach wirtualnych platformy Azure. Konfiguracja z niewielką liczbą woluminów może prowadzić do niewielkiej liczby wątków wykonujących szyfrowanie. Szyfrowanie pojedynczego wątku odczytuje rozmiar 64 KB, szyfruje go, a następnie zapisuje rekord w pliku dziennika transakcji, co oznacza, że zakres został zaszyfrowany. W efekcie obciążenie dziennika transakcji jest umiarkowane.
- W starszych wersjach SQL Server kompresja kopii zapasowej nie była już wydajna podczas szyfrowania SQL Server bazy danych. Takie zachowanie może wystąpić w przypadku, gdy planujesz Zaszyfrowanie bazy danych SQL Server lokalnie, a następnie skopiowanie kopii zapasowej na platformę Azure w celu przywrócenia bazy danych na platformie Azure. SQL Server kompresji kopii zapasowej zwykle osiąga współczynnik kompresji współczynnik 4.
- W SQL Server 2016 SQL Server wprowadzono nowe funkcje, które umożliwiają kompresowanie zaszyfrowanych baz danych, a także wydajny sposób. Aby uzyskać szczegółowe informacje, zobacz [ten blog](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) .
 
Traktowanie aplikacji TDE Encryption bez ani niewiele obciążeń SAP, należy przetestować określoną konfigurację, aby lepiej zastosować TDE do bazy danych SAP w środowisku lokalnym lub aby to zrobić na platformie Azure. Na platformie Azure użytkownik ma większą elastyczność pod względem infrastruktury nadmiernej aprowizacji i zmniejsza infrastrukturę po zastosowaniu TDE.

### <a name="using-azure-key-vault"></a>Używanie Azure Key Vault
Platforma Azure oferuje usługę [Key Vault](https://azure.microsoft.com/services/key-vault/) do przechowywania kluczy szyfrowania. SQL Server po drugiej stronie oferuje łącznik, aby wykorzystać Azure Key Vault jako magazyn dla certyfikatów TDE.

Więcej szczegółów na temat używania Azure Key Vault dla list SQL Server TDE, takich jak:

- [Rozszerzalne zarządzanie kluczami za pomocą Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- [SQL Server rozszerzalne zarządzanie kluczami TDE za pomocą Azure Key Vault — kroki instalacji](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault).
- [Rozwiązywanie problemów z & konserwacją SQL Server Connector](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting).
- [Więcej pytań od klientów dotyczących SQL Server transparent Data Encryption – TDE i Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault).


>[!IMPORTANT]
>Przy użyciu SQL Server TDE, szczególnie w przypadku usługi Azure Key, zaleca się korzystanie z najnowszych poprawek SQL Server 2014, SQL Server 2016 i SQL Server 2017. Przyczyną jest to, że w oparciu o opinie klientów, optymalizacje i poprawki zostały zastosowane do kodu. Na przykład sprawdź [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Ogólne SQL Server dla oprogramowania SAP na platformie Azure — podsumowanie
Ten przewodnik zawiera wiele zaleceń i zalecamy przeczytanie go więcej niż raz przed zaplanowaniem wdrożenia platformy Azure. Ogólnie rzecz biorąc, należy przestrzegać najważniejszych ogólnych systemów DBMS dotyczących zaleceń dotyczących platformy Azure:

1. Użyj najnowszej wersji systemu DBMS, takiej jak SQL Server 2017, która ma największe zalety na platformie Azure. 
2. Starannie Zaplanuj poziom systemu SAP na platformie Azure, aby zrównoważyć układ plików danych i ograniczenia platformy Azure:
   * Nie masz zbyt dużej liczby dysków, ale wystarczająco że masz pewność, że możesz uzyskać dostęp do wymaganych IOPS.
   * Jeśli nie używasz Managed Disks, pamiętaj, że liczby IOPS są również ograniczone dla konta usługi Azure Storage, a konta magazynu są ograniczone w ramach każdej subskrypcji platformy Azure ([więcej informacji][azure-resource-manager/management/azure-subscription-service-limits]). 
   * W przypadku konieczności osiągnięcia wyższej przepływności w różnych dyskach.
3. Nigdy nie instaluj oprogramowania ani nie umieszczaj plików, które wymagają trwałości w D:\ dysk jest nietrwały i wszystkie elementy na tym dysku zostaną utracone przy ponownym uruchomieniu systemu Windows.
4. Nie używaj buforowania dysków dla usługi Azure Standard Storage.
5. Nie używaj kont Azure Standard Storage z replikacją geograficzną platformy Azure.  Użyj lokalnie nadmiarowego dla obciążeń systemu DBMS.
6. Użyj rozwiązania HA/DR dostawcy systemu DBMS do replikowania danych bazy danych.
7. Zawsze używaj rozpoznawania nazw, nie używaj adresów IP.
8. Przy użyciu SQL Server TDE Zastosuj najnowsze SQL Server poprawki.
9. Użyj najwyższej możliwej kompresji bazy danych. Która jest kompresją strony dla SQL Server.
10. Należy zachować ostrożność przy użyciu SQL Server obrazów z portalu Azure Marketplace. Jeśli używasz SQL Server jeden, należy zmienić sortowanie wystąpienia przed zainstalowaniem na nim dowolnego systemu SAP NetWeaver.
11. Zainstaluj i skonfiguruj monitorowanie hosta SAP dla platformy Azure zgodnie z opisem w [Podręczniku wdrażania][deployment-guide].


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem 

- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md)