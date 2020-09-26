---
title: 'SAP na platformie Azure: Przewodnik planowania i implementacji'
description: Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8884711bbb32054ca1d8e4d9f9e7dee753f0c629
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361929"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Microsoft Azure umożliwia firmom pozyskiwanie zasobów obliczeniowych i magazynu w minimalnym czasie bez długotrwałych cykli zakupów. Usługa Azure Virtual Machines umożliwia firmom wdrażanie klasycznych aplikacji, takich jak aplikacje oparte na oprogramowaniu SAP NetWeaver na platformie Azure, oraz rozszerzanie ich niezawodności i dostępności bez konieczności używania dalszych zasobów w środowisku lokalnym. Usługi Azure Virtual Machines obsługują również łączność między środowiskami lokalnymi, dzięki czemu firmy mogą aktywnie zintegrować Virtual Machines platformy Azure z domenami lokalnymi, chmurami prywatnymi i ich system SAP.
W tym dokumencie opisano podstawowe informacje dotyczące Microsoft Azure maszyny wirtualnej i omówiono zagadnienia dotyczące planowania i implementacji instalacji oprogramowania SAP NetWeaver na platformie Azure. w związku z tym należy zapoznać się z dokumentem przed rozpoczęciem rzeczywistej wdrożenia oprogramowania SAP NetWeaver na platformie Azure.
Ten dokument uzupełnia dokumentację instalacji SAP i uwagi SAP, które reprezentują podstawowe zasoby dotyczące instalacji i wdrożeń oprogramowania SAP na określonych platformach.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Podsumowanie
Chmura obliczeniowa to szeroko używany termin, który zwiększa i zwiększa znaczenie w branży IT, od małych firm do dużych i wielonarodowych firm.

Microsoft Azure jest platformą Cloud Services firmy Microsoft, która oferuje szeroką gamę nowych możliwości. Teraz klienci mogą szybko inicjować obsługę administracyjną aplikacji jako usługę w chmurze i ją wycofać, więc nie są one ograniczone do ograniczeń technicznych lub budżetowych. Zamiast czasu inwestycji i budżetu do infrastruktury sprzętu firmy mogą skupić się na aplikacjach, procesach biznesowych i korzyściach dla klientów i użytkowników.

Wraz z usługami Microsoft Azure Virtual Machines firma Microsoft oferuje kompleksową platformę typu infrastruktura jako usługa (IaaS). Aplikacje oparte na oprogramowaniu SAP NetWeaver są obsługiwane w usłudze Azure Virtual Machines (IaaS). W tym dokumencie opisano, jak planować i wdrażać aplikacje oparte na oprogramowaniu SAP NetWeaver w Microsoft Azure jako platformę wyboru.

Sam papier koncentruje się na dwóch głównych aspektach:

* W pierwszej części opisano dwa obsługiwane wzorce wdrażania dla aplikacji opartych na systemie SAP NetWeaver na platformie Azure. Opisano w nim również ogólną obsługę platformy Azure z wdrożeniami oprogramowania SAP.
* Druga część zawiera szczegóły implementujące różne scenariusze opisane w pierwszej części.

Aby uzyskać dodatkowe zasoby, zobacz rozdział [zasoby][planning-guide-1.2] w tym dokumencie.

### <a name="definitions-upfront"></a>Definicje z góry
W całym dokumencie używane są następujące warunki:

* IaaS: infrastruktura jako usługa
* PaaS: platforma jako usługa
* SaaS: oprogramowanie jako usługa
* Składnik SAP: indywidualna aplikacja SAP, taka jak ECC, BW, Menedżer rozwiązań lub S/4HANA.  Składniki SAP mogą opierać się na tradycyjnych technologiach ABAP lub Java lub aplikacjach nieopartych na NetWeaver, takich jak obiekty biznesowe.
* Środowisko SAP: co najmniej jeden składnik SAP logicznie zgrupowany do wykonywania funkcji biznesowej, takiej jak programowanie, QAS, szkolenia, DR lub produkcja.
* SAP krajobraz: ten termin odnosi się do całego zasobu SAP w poziomie klienta IT. System SAP zawiera wszystkie środowiska produkcyjne i nieprodukcyjne.
* System SAP: kombinacja warstwy systemu DBMS i warstwy aplikacji, na przykład system do programowania SAP ERP, SAP BW system testowy, System produkcyjny SAP CRM itd. W przypadku wdrożeń platformy Azure nie jest obsługiwane dzielenie tych dwóch warstw między środowiskiem lokalnym i platformą Azure. Oznacza, że system SAP został wdrożony lokalnie lub został wdrożony na platformie Azure. Można jednak wdrożyć różne systemy oprogramowania SAP poziomo na platformie Azure lub lokalnie. Można na przykład wdrożyć systemy deweloperskie i testowe oprogramowania SAP CRM na platformie Azure, ale system produkcyjny SAP CRM w środowisku lokalnym.
* Wiele lokalizacji lub hybrydowe: w tym artykule opisano scenariusz, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność między lokacjami, wiele witryn lub ExpressRoute między lokalnymi centrami danych i platformą Azure. W typowej dokumentacji platformy Azure te rodzaje wdrożeń są również opisane jako scenariusze obejmujące wiele lokalizacji lub hybrydowe. Przyczyną połączenia jest rozszerzonie domen lokalnych, lokalnych Active Directory/OpenLDAP i lokalnych nazw DNS na platformę Azure. Lokalne poziomy są rozszerzane do zasobów platformy Azure w ramach subskrypcji. Posiadanie tego rozszerzenia maszyn wirtualnych może być częścią domeny lokalnej. Użytkownicy domeny lokalnej mogą uzyskać dostęp do serwerów i mogą uruchamiać usługi na tych maszynach wirtualnych (takich jak usługi DBMS). Możliwa jest komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i maszynami wirtualnymi wdrożonymi na platformie Azure. Jest to najbardziej typowy i niemal wyłączny przypadek do wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zapoznaj się z [tym][vpn-gateway-cross-premises-options] artykułem [.][vpn-gateway-site-to-site-create]
* Rozszerzenie monitorowania platformy Azure, ulepszone monitorowanie i rozszerzenie platformy Azure dla oprogramowania SAP: opisz jeden i ten sam element. Opisuje rozszerzenie maszyny wirtualnej, które należy wdrożyć, aby udostępnić podstawowe dane dotyczące infrastruktury platformy Azure dla agenta hosta SAP. Oprogramowanie SAP w programie SAP uwagi może odwoływać się do niego jako rozszerzenie monitorowania lub ulepszone monitorowanie. Na platformie Azure odwołujemy się do niego jako **rozszerzenie platformy Azure dla oprogramowania SAP**.

> [!NOTE]
> Zbiorowe lub hybrydowe wdrożenia systemów SAP, w których platformy Azure Virtual Machines z systemami SAP są członkami domeny lokalnej, są obsługiwane w przypadku produkcyjnych systemów SAP. Konfiguracje obejmujące wiele lokalizacji lub hybrydowe są obsługiwane w przypadku wdrażania części lub kompletnego rozwiązania SAP Landscapes na platformie Azure. Nawet w przypadku korzystania z kompletnej krajobrazu SAP na platformie Azure wymagane jest, aby maszyny wirtualne były częścią domeny lokalnej i usług AD/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Zasoby
Punkt wejścia dla obciążeń SAP w dokumentacji platformy Azure znajduje się [tutaj](./get-started.md). Począwszy od tego punktu wejścia znajdziesz wiele artykułów, które obejmują tematy dotyczące:

- SAP NetWeaver i Business One na platformie Azure
- Przewodniki SAP DBMS dla różnych systemów DBMS na platformie Azure
- Wysoka dostępność i odzyskiwanie po awarii dla obciążeń SAP na platformie Azure
- Szczegółowe wskazówki dotyczące uruchamiania SAP HANA na platformie Azure
- Wskazówki dotyczące dużych wystąpień platformy Azure HANA dla systemu SAP HANA DBMS


> [!IMPORTANT]
> Gdy jest to możliwe, należy użyć linku do przewodników instalacji oprogramowania SAP lub innej dokumentacji SAP (odwołanie InstGuide-01, zobacz <http://service.sap.com/instguides> ). Gdy przejdziesz do wymagań wstępnych, procesu instalacji lub szczegółowych informacji o określonej funkcji SAP, dokumentacja i przewodniki SAP powinny zawsze być odczytywane, ponieważ dokumenty firmy Microsoft dotyczą tylko określonych zadań oprogramowania SAP zainstalowanych i obsługiwanych w Microsoft Azure maszynie wirtualnej.
>
>

Poniższe uwagi dotyczące oprogramowania SAP są powiązane z tematem SAP na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i rozmiary |
| [2015553] |SAP on Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z ulepszonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2178632] |Metryki monitorowania kluczy dla SAP na Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2191498] |SAP w systemie Linux z platformą Azure: ulepszone monitorowanie |
| [2243692] |Maszyna wirtualna z systemem Linux na Microsoft Azure (IaaS): problemy z licencją SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7. x: Instalowanie i uaktualnianie |
| [2069760] |Oracle Linux 7. x instalacja i uaktualnienie SAP |
| [1597355] |Zalecenie wymiany miejsca dla systemu Linux |

Zapoznaj się również z witryną [typu wiki usługi SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) , która zawiera wszystkie uwagi SAP dla systemu Linux.

Ogólne ograniczenia domyślne i maksymalne ograniczenia dotyczące subskrypcji platformy Azure można znaleźć w [tym artykule][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Możliwe scenariusze
System SAP jest często traktowany jako jedna z najbardziej krytycznych aplikacji w przedsiębiorstwach. Architektura i operacje tych aplikacji są w większości skomplikowane i zapewniają, że spełniasz wymagania dotyczące dostępności i wydajności.

W związku z tym przedsiębiorstwa muszą dokładnie rozważyć, który dostawca usług w chmurze ma wybrać do uruchamiania takich krytycznych procesów firmowych. System Azure to idealna platforma usług w chmurze publicznej dla krytycznych aplikacji SAP i procesów firmy. Mając na względzie szeroką gamę infrastruktury platformy Azure, prawie wszystkie istniejące systemy SAP NetWeaver i S/4HANA mogą być hostowane na platformie Azure już dziś. Platforma Azure udostępnia maszyny wirtualne z wieloma terabajtami pamięci i więcej niż 200 procesorów CPU. Poza tym, że platforma Azure oferuje [duże wystąpienia platformy Hana](./hana-overview-architecture.md), które umożliwiają skalowanie w poziomie do 24 TB, a SAP HANA skalowanie w poziomie do 120 TB. Jednym z nich może być dziś, że niemal wszystkie lokalne scenariusze SAP można również uruchomić na platformie Azure.

Aby zapoznać się z nieprzewidzianymi scenariuszami i nieobsługiwanymi scenariuszami, zapoznaj się z artykułem [Obsługa obciążeń SAP na maszynach wirtualnych platformy Azure](./sap-planning-supported-configurations.md).

Sprawdź te scenariusze i niektóre warunki, które zostały nazwane jako nieobsługiwane w dokumentacji dotyczącej projektowania i opracowywania architektury, która ma zostać wdrożona na platformie Azure.

Typowym wzorcem wdrożenia jest scenariusz między lokalizacjami, taki jak Wyświetlono

![Sieć VPN z połączeniem lokacja-lokacja (wiele lokalizacji)][planning-guide-figure-300]

Przyczyną zastosowania wzorca wdrożenia obejmującego wiele lokalizacji jest fakt, że jest to najbardziej przejrzyste dla wszystkich aplikacji, które można wdrożyć lokalnie na platformie Azure przy użyciu usługi Azure ExpressRoute i traktować platformę Azure jako wirtualne centrum danych. Gdy coraz więcej zasobów jest przenoszonych na platformę Azure, zostanie powiększona infrastruktura i infrastruktura sieciowa wdrożona na platformie Azure, a zasoby lokalne zostaną odpowiednio zredukowane. Wszystkie elementy widoczne dla użytkowników i aplikacji.

Aby pomyślnie wdrożyć systemy SAP na platformie Azure IaaS lub IaaS ogólnie rzecz biorąc, ważne jest zrozumienie znaczących różnic między ofertami tradycyjnych lub usług hostingowych oraz ofert IaaS. Natomiast tradycyjny dostawca usług hostingowych (sieci, magazynu i serwera) dostosowuje się do obciążenia, które klient chce hostować, zamiast tego klient lub partner decyduje się na określenie obciążenia i wybranie odpowiednich składników platformy Azure maszyn wirtualnych, magazynu i sieci na potrzeby wdrożeń IaaS.

W celu zebrania danych dotyczących planowania wdrożenia na platformie Azure ważne jest:

- Oceń, które produkty SAP są obsługiwane na maszynach wirtualnych platformy Azure
- Oceń, jakie wersje systemu operacyjnego są obsługiwane dla określonych maszyn wirtualnych platformy Azure dla tych produktów SAP
- Oceń, jakie wersje systemu DBMS są obsługiwane przez produkty SAP z określonymi maszynami wirtualnymi platformy Azure
- Oceń, czy niektóre z wymaganych wydań systemu operacyjnego/DBMS wymagają wykonania wydania SAP, uaktualnienia pakietu pomocy technicznej i uaktualnień jądra, aby uzyskać dostęp do obsługiwanej konfiguracji
- Oceń, czy konieczne jest przejście do różnych systemów operacyjnych w celu wdrożenia na platformie Azure.

Szczegółowe informacje na temat obsługiwanych składników SAP na platformie Azure, obsługiwanych jednostek infrastruktury platformy Azure oraz związanych z nimi wersji systemu operacyjnego i wersji DBMS zostały omówione w artykule [co to jest oprogramowanie SAP obsługiwane przez wdrożenia platformy Azure](./sap-supported-product-on-azure.md). Wyniki uzyskane z oceny prawidłowych wydań SAP, systemu operacyjnego i systemów DBMS mają duży wpływ na wysiłki przenoszące systemy SAP na platformę Azure. Wynikiem tej oceny jest określenie, czy może wystąpić znaczący nakład pracy w przypadku, gdy są konieczne uaktualnienia wersji SAP lub zmiany systemów operacyjnych.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiony platformy Azure
Usługi platformy Azure firmy Microsoft są zbierane w regionach platformy Azure. Region świadczenia usługi Azure to jedna lub kolekcja z centrów danych, które zawierają sprzęt i infrastrukturę, które działają i obsługują różne usługi platformy Azure. Ta infrastruktura obejmuje dużą liczbę węzłów, które działają jako węzły obliczeniowe lub węzły magazynu, lub uruchamiają funkcje sieciowe.

Aby zapoznać się z listą różnych regionów świadczenia usługi Azure, zapoznaj się z artykułem [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/). Nie wszystkie regiony platformy Azure oferują te same usługi. Zależnie od produktu SAP, który chcesz uruchomić, a także systemu operacyjnego i w systemie DBMS, możesz się dokończyć w sytuacji, w której określony region nie oferuje wymaganych typów maszyn wirtualnych. Jest to szczególnie ważne w przypadku uruchamiania SAP HANA, w którym zwykle są potrzebne maszyny wirtualne z serii maszyn wirtualnych M/Mv2. Te rodziny maszyn wirtualnych są wdrażane tylko w podzbiorze regionów. Możesz sprawdzić dokładną maszynę wirtualną, typy, typy magazynów platformy Azure lub inne usługi platformy Azure, które są dostępne w regionach z ułatwieniami dostępu do [produktów lokacji dostępnych w danym regionie](https://azure.microsoft.com/global-infrastructure/services/). Po rozpoczęciu planowania i przydzielenia określonych regionów jako regionu podstawowego i ostatecznie pomocniczego regionu należy najpierw zbadać, czy wymagane usługi są dostępne w tych regionach.

### <a name="availability-zones"></a>Strefy dostępności
Kilka regionów świadczenia usługi Azure zaimplementował koncepcję o nazwie Strefy dostępności. Strefy dostępności są fizycznie oddzielone lokalizacjami w regionie świadczenia usługi Azure. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Na przykład wdrożenie dwóch maszyn wirtualnych w ramach dwóch Strefy dostępności platformy Azure oraz wdrożenie struktury wysokiej dostępności dla systemu SAP DBMS lub usług SAP Central zapewnia najlepszą umowę SLA na platformie Azure. W przypadku tej konkretnej umowy SLA maszyny wirtualnej na platformie Azure Zapoznaj się z najnowszą wersją [umowy SLA maszyny wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Ponieważ regiony platformy Azure są opracowywane i rozszerzane szybko w ciągu ostatnich lat, topologia regionów świadczenia usługi Azure, liczba fizycznych centrów danych, odległość między centrami danych i odległość między Strefy dostępności platformy Azure mogą być różne. I z tym opóźnieniem sieci.

Zasada Strefy dostępności nie ma zastosowania do usługi HANA specyficznej dla [dużych wystąpień platformy Hana](./hana-overview-architecture.md). Umowy dotyczące poziomu usług dla dużych wystąpień HANA można znaleźć w artykule [Umowa SLA dla oprogramowanie SAP HANA na platformie Azure — duże wystąpienia](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domeny błędów
Domeny błędów reprezentują fizyczną jednostkę awarii, ściśle powiązane z infrastrukturą fizyczną zawartą w centrach danych, a podczas gdy fizyczny blok lub Stojak może być traktowany jako domena błędów, nie istnieje bezpośrednie mapowanie jeden do jednego między nimi.

W przypadku wdrażania wielu Virtual Machines w ramach jednego systemu SAP w Microsoft Azure usługach maszyn wirtualnych można mieć wpływ na kontroler sieci szkieletowej platformy Azure w celu wdrożenia aplikacji w różnych domenach błędów, co spełnia wyższe wymagania dotyczące dostępności umowy SLA. Jednak dystrybucja domen błędów za pośrednictwem jednostki skalowania platformy Azure (kolekcja setek węzłów obliczeniowych lub węzłów magazynu i sieci) lub przypisanie maszyn wirtualnych do konkretnej domeny błędów jest czymś, w którym nie masz bezpośredniego sterowania. Aby skierować Kontroler sieci szkieletowej platformy Azure do wdrożenia zestawu maszyn wirtualnych w różnych domenach błędów, należy przypisać zestaw dostępności platformy Azure do maszyn wirtualnych w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [dostępności platformy Azure][planning-guide-3.2.3] w tym dokumencie.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Uaktualnij domeny
Domeny uaktualnienia reprezentują jednostkę logiczną, która pomaga określić, jak działa maszyna wirtualna w systemie SAP, która składa się z wystąpień SAP działających na wielu maszynach wirtualnych. Gdy następuje uaktualnienie, Microsoft Azure przechodzi przez proces aktualizacji tych domen uaktualnienia po jednej przez jeden. Dzięki rozproszeniu maszyn wirtualnych w czasie wdrażania w różnych domenach uaktualnienia można chronić system SAP częściowo od potencjalnych przestojów. Aby wymusić wdrożenie przez platformę Azure maszyn wirtualnych systemu SAP w różnych domenach uaktualnienia, należy ustawić określony atrybut w czasie wdrażania każdej maszyny wirtualnej. Podobnie jak w przypadku domen błędów, jednostka skalowania platformy Azure jest dzielona na wiele domen uaktualnienia. Aby skierować Kontroler sieci szkieletowej platformy Azure do wdrożenia zestawu maszyn wirtualnych w różnych domenach uaktualnienia, należy przypisać zestaw dostępności platformy Azure do maszyn wirtualnych w czasie wdrażania. Aby uzyskać więcej informacji na temat zestawów dostępności platformy Azure, zobacz rozdział [Azure Availability Sets][planning-guide-3.2.3] poniżej.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Zestawy dostępności platformy Azure
Virtual Machines platformy Azure w ramach jednego zestawu dostępności platformy Azure są dystrybuowane przez kontroler sieci szkieletowej platformy Azure w różnych domenach błędów i uaktualnień. Celem dystrybucji wielu domen błędów i uaktualniania jest uniemożliwienie zamknięcia wszystkich maszyn wirtualnych systemu SAP w przypadku konserwacji infrastruktury lub awarii w ramach jednej domeny błędów. Domyślnie maszyny wirtualne nie są częścią zestawu dostępności. Uczestnictwo maszyny wirtualnej w zestawie dostępności jest zdefiniowane w czasie wdrażania lub później w ramach ponownej konfiguracji i ponownego wdrożenia maszyny wirtualnej.

Aby zrozumieć koncepcję zestawów dostępności platformy Azure i sposób, w jaki zestawy dostępności odnoszą się do domen błędów i uaktualnień, Przeczytaj [ten artykuł][virtual-machines-manage-availability].

Podczas definiowania zestawów dostępności i próby zmieszania różnych maszyn wirtualnych różnych rodzin maszyn wirtualnych w ramach jednego zestawu dostępności mogą wystąpić problemy uniemożliwiające dołączenie określonego typu maszyny wirtualnej do takiego zestawu dostępności. Przyczyną jest to, że zestaw dostępności jest powiązany z jednostką skalowania, która zawiera określony typ hostów obliczeniowych. A pewien typ hosta obliczeniowego może uruchamiać tylko niektóre typy rodzin maszyn wirtualnych. Jeśli na przykład utworzysz zestaw dostępności i wdrożono pierwszą maszynę wirtualną w zestawie dostępności i wybierzesz typ maszyny wirtualnej rodziny Esv3, a następnie spróbujesz wdrożyć jako drugą maszynę wirtualną jako MASZYNę wirtualną z rodziny M, zostanie ona odrzucona w drugim przydziale. Przyczyną jest to, że maszyny wirtualne z rodziną Esv3 nie są uruchomione na tym samym sprzęcie hosta, w którym znajdują się maszyny wirtualne z rodziny M. Ten sam problem może wystąpić, gdy użytkownik próbuje zmienić rozmiar maszyn wirtualnych i próbować przenieść maszynę wirtualną z rodziny Esv3 do typu maszyny wirtualnej rodziny M. W przypadku zmiany rozmiaru rodziny maszyn wirtualnych, która nie może być hostowana na tym samym sprzęcie hosta, należy zamknąć wszystkie maszyny wirtualne w zestawie dostępności i zmienić ich rozmiar, aby można było uruchamiać je na innym typie komputera hosta. W przypadku umowy SLA maszyn wirtualnych, które są wdrożone w ramach zestawu dostępności, zapoznaj się z artykułem [umowy SLA maszyny wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Zasada zestawu dostępności i powiązanej aktualizacji i domeny błędów nie ma zastosowania do usługi Hana specyficznej dla [dużych wystąpień](./hana-overview-architecture.md)platformy Hana. Umowy dotyczące poziomu usług dla dużych wystąpień HANA można znaleźć w artykule [Umowa SLA dla oprogramowanie SAP HANA na platformie Azure — duże wystąpienia](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> Pojęcia dotyczące Strefy dostępności platformy Azure i zestawów dostępności platformy Azure wykluczają się wzajemnie. Oznacza to, że można wdrożyć parę lub wiele maszyn wirtualnych w określonej strefie dostępności lub w zestawie dostępności platformy Azure. Ale nie obu.

### <a name="azure-paired-regions"></a>Sparowane regiony platformy Azure
Platforma Azure oferuje pary regionów platformy Azure, w których replikacja określonych danych jest włączana między tymi stałymi parami regionów. Parowanie regionów jest udokumentowane w artykule [ciągłość biznesowa i odzyskiwanie po awarii (BCDR): Wielosparowane regiony platformy Azure](../../../best-practices-availability-paired-regions.md). Zgodnie z opisem w artykule replikacja danych jest związana z typami magazynów platformy Azure, które mogą być konfigurowane przez użytkownika w celu replikowania w sparowany region. Zobacz również [nadmiarowość magazynu artykułów w regionie pomocniczym](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region). Typy magazynów zezwalające na taką replikację są typami magazynów, które nie są odpowiednie dla obciążenia systemu DBMS. W związku z tym użyteczność replikacji usługi Azure Storage będzie ograniczona do magazynu obiektów blob platformy Azure (takiego jak na potrzeby tworzenia kopii zapasowych) lub innych scenariuszy magazynu o dużej opóźnieniu. Podczas sprawdzania par regionów i usług, które mają być używane jako region podstawowy lub pomocniczy, mogą wystąpić sytuacje, w których usługi platformy Azure i/lub typy maszyn wirtualnych, których zamierzasz używać w regionie podstawowym, nie są dostępne w sparowanym regionie. Lub może wystąpić sytuacja, w której sparowany region platformy Azure nie jest akceptowalny ze względu na zgodność danych. W takich przypadkach należy użyć niesparowanego regionu jako regionu odzyskiwania dodatkowego/awaryjnego. W takim przypadku należy zadbać o replikację niektórych części danych, które zostały zreplikowane przez platformę Azure. Przykład sposobu replikowania Active Directory i usługi DNS do regionu odzyskiwania po awarii został opisany w artykule [Konfigurowanie odzyskiwania po awarii dla Active Directory i systemu DNS](../../../site-recovery/site-recovery-active-directory.md)


## <a name="azure-virtual-machine-services"></a>Usługi Azure Virtual Machines
Platforma Azure oferuje wiele maszyn wirtualnych, które można wybrać do wdrożenia. Nie ma potrzeby dokonywania zakupów z góry i technologii. Oferta usługi maszyny wirtualnej platformy Azure upraszcza konserwację i obsługę aplikacji przez zapewnienie obliczeniowych i magazynów na żądanie na potrzeby hostowania, skalowania i zarządzania aplikacją sieci Web i połączonymi aplikacjami. Zarządzanie infrastrukturą jest zautomatyzowane dzięki platformie, która została zaprojektowana w celu zapewnienia wysokiej dostępności i dynamicznego skalowania w celu dopasowania do potrzeb użycia z opcją kilku różnych modeli cenowych.

![Pozycjonowanie Microsoft Azure usług Virtual Machine Services][planning-guide-figure-400]

Dzięki usłudze Azure Virtual Machines firma Microsoft umożliwia wdrażanie niestandardowych obrazów serwera na platformie Azure jako wystąpienia IaaS. Możesz też wybrać spośród bogatych opcji wzbogacania obrazów systemu operacyjnego z galerii obrazów platformy Azure.

Z punktu widzenia działania usługa Azure Virtual Machines oferuje podobne środowisko w przypadku maszyn wirtualnych wdrożonych lokalnie. Użytkownik jest odpowiedzialny za administrację, operacje, a także poprawkę danego systemu operacyjnego, uruchomionego na maszynie wirtualnej platformy Azure i jej aplikacjach na tej maszynie wirtualnej. Firma Microsoft nie zapewnia więcej usług wykraczających poza Hosting maszyn wirtualnych w infrastrukturze platformy Azure (infrastruktura jako Usługa — IaaS). W przypadku obciążeń SAP wdrażanych przez klienta firma Microsoft nie ma żadnych ofert poza ofertą IaaS.

Platforma Microsoft Azure jest platformą z wieloma dzierżawcami. W związku z tym zasoby magazynu, sieci i zasobów obliczeniowych, które obsługują maszyny wirtualne platformy Azure, są z kilkoma wyjątkami współdzielonymi między dzierżawcami. Inteligentne ograniczanie przepływności i logikę przydziałów są używane, aby uniemożliwić jednemu dzierżawie wpływ na wydajność innej dzierżawy (sąsiada) w drastyczny sposób. Szczególnie w przypadku certyfikowania platformy Azure dla SAP HANA, firma Microsoft musi udowodnić izolację zasobów w przypadku, gdy wiele maszyn wirtualnych może działać na tym samym hoście w regularnych odstępach czasu SAP. Chociaż logika na platformie Azure próbuje zachować różnice w przepustowości, środowisko o wysokiej udostępnionej platformie zapewnia większe Wariancje w dostępności zasobów/przepustowości niż klienci mogą korzystać z wdrożeń lokalnych. Prawdopodobieństwo, że system SAP na platformie Azure może mieć większe wariancje niż w systemie lokalnym, należy wziąć pod uwagę.

### <a name="azure-virtual-machines-for-sap-workload"></a>Azure Virtual Machines for SAP — obciążenie

W przypadku obciążeń SAP zawężamy wybór do różnych rodzin maszyn wirtualnych, które są odpowiednie dla obciążenia SAP i bardziej szczegółowego obciążenia SAP HANA. Sposób znalezienia poprawnego typu maszyny wirtualnej i jego możliwości pracy w ramach obciążenia SAP opisano w dokumencie, [jakie oprogramowanie SAP jest obsługiwane przez wdrożenia platformy Azure](./sap-supported-product-on-azure.md).

> [!NOTE]
> Typy maszyn wirtualnych certyfikowane dla obciążeń SAP nie zapewnia nadmiernej aprowizacji zasobów procesora i pamięci.

Poza wyborem czystych typów maszyn wirtualnych należy również sprawdzić, czy te typy maszyn wirtualnych są dostępne w określonym regionie w oparciu o dostępne w danym regionie [produkty](https://azure.microsoft.com/global-infrastructure/services/)lokacji. Jednak ważne jest, aby sprawdzić, czy:

- Zasoby procesora i pamięci różnych typów maszyn wirtualnych
- Przepustowość IOPS różnych typów maszyn wirtualnych
- Możliwości sieciowe różnych typów maszyn wirtualnych
- Liczba dysków, które można dołączyć
- Możliwość korzystania z określonych typów magazynu platformy Azure

Dopasuj do potrzeb. Większość tych danych można znaleźć [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows] dla określonego typu maszyny wirtualnej.

Model cen zawiera kilka różnych opcji cenowych, takich jak:

- Płatność zgodnie z rzeczywistym użyciem
- Jeden rok zarezerwowany
- Zarezerwowane trzy lata
- Cennik punktowy

Cennik każdej z różnych ofert z różnymi ofertami usług dotyczących systemów operacyjnych i różnych regionów jest dostępny w witrynie [Linux Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows Virtual Machines cenniku](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Aby uzyskać szczegółowe informacje i elastyczność jednego roku i 3-roczne wystąpienia zarezerwowane, zapoznaj się z następującymi artykułami:

- [Co to są rezerwacje platformy Azure?](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Elastyczność rozmiaru maszyny wirtualnej w usłudze Reserved VM Instances](../../windows/reserved-vm-instance-size-flexibility.md)
- [Jak rabat na rezerwację platformy Azure jest stosowany do maszyn wirtualnych](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Aby uzyskać więcej informacji na temat cennika, zapoznaj się z artykułem [Virtual Machines platformy Azure](https://azure.microsoft.com/pricing/spot/). Cennik tego samego typu maszyn wirtualnych może być również różny dla różnych regionów świadczenia usługi Azure. W przypadku niektórych klientów warto wdrożyć ją w tańszym regionie świadczenia usługi Azure.

Ponadto platforma Azure oferuje koncepcje dedykowanego hosta. Dedykowane koncepcje hosta dają większą kontrolę nad cyklami poprawek wykonywanymi przez platformę Azure. Można czasowo zastosować poprawki zgodnie z własnymi harmonogramami. Oferta jest przeznaczona dla klientów korzystających z obciążeń, które mogą nie być zgodne z normalnym cyklem obciążeń. Aby zapoznać się z pojęciami dedykowanych ofert hosta platformy Azure, zapoznaj się z artykułem [dedykowanym hosta platformy Azure](../../windows/dedicated-hosts.md). Ta oferta jest obsługiwana w przypadku obciążeń SAP i jest używana przez kilku klientów SAP, którzy chcą mieć większą kontrolę nad stosowaniem poprawek infrastruktury i planów samoobsługowych firmy Microsoft. Aby uzyskać więcej informacji na temat sposobu, w jaki firma Microsoft utrzymuje i aktualizuje infrastrukturę platformy Azure, która obsługuje maszyny wirtualne, zapoznaj się z artykułem [konserwacja maszyn wirtualnych na platformie Azure](../../maintenance-and-updates.md).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Maszyny wirtualne generacji 1 i 2
Funkcja hypervisor firmy Microsoft może obsługiwać dwa różne generacji maszyn wirtualnych. Te formaty nazywają **generację 1** i **2. generacji**. **Generacja 2** została wprowadzona w roku 2012 przy użyciu funkcji hypervisor systemu Windows Server 2012. Platforma Azure rozpoczęła korzystanie z maszyn wirtualnych generacji 1. Podczas wdrażania usługi Azure Virtual Machines domyślnie nadal jest używany format generacji 1. W tym czasie można również wdrożyć formaty maszyn wirtualnych generacji 2. W artykule [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../../windows/generation-2.md) zawiera listę rodzin maszyn wirtualnych platformy Azure, które można WDROŻYĆ jako maszynę wirtualną generacji 2. W tym artykule wymieniono również ważne różnice funkcjonalne maszyn wirtualnych generacji 2, które mogą być uruchamiane w chmurze prywatnej funkcji Hyper-V i na platformie Azure. W tym artykule opisano również różnice funkcjonalne między maszynami wirtualnymi generacji 1 i maszyną wirtualną 2. generacji, jak te działające na platformie Azure.

> [!NOTE]
> Istnieją funkcjonalne różnice między maszynami wirtualnymi generacji 1 i 2. generacji działającymi na platformie Azure. Zapoznaj się z artykułem  [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../../windows/generation-2.md) , aby wyświetlić listę tych różnic.

Przeniesienie istniejącej maszyny wirtualnej z jednej generacji do drugiej generacji nie jest możliwe. Aby zmienić generację maszyny wirtualnej, należy wdrożyć nową MASZYNę wirtualną generacji i ponownie zainstalować oprogramowanie, które jest uruchomione w maszynie wirtualnej generacji. Ta zmiana ma wpływ tylko na podstawowy obraz wirtualnego dysku twardego maszyny wirtualnej i nie ma wpływu na dyski danych ani dołączone udziały NFS lub SMB. Dyski danych, system plików NFS lub udziały SMB, które pierwotnie zostały przypisane do programu, na przykład na maszynie wirtualnej generacji 1.

> [!NOTE]
> Wdrożenie Mv1 maszyn wirtualnych rodziny maszyn wirtualnych jako maszyn wirtualnych 2. generacji jest możliwe od początku maja 2020. Dzięki temu jest możliwe pozornie mniej i downsizing z maszyn wirtualnych z rodziną Mv1 i Mv2.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Magazyn: Microsoft Azure Storage i dyski z danymi
Microsoft Azure Virtual Machines używać różnych typów magazynów. Podczas implementowania oprogramowania SAP w usługach Azure Virtual Machines ważne jest zrozumienie różnic między tymi dwoma głównymi typami magazynu:

* Nietrwały magazyn trwały.
* Magazyn trwały.

Maszyny wirtualne platformy Azure oferują dyski nietrwałe po wdrożeniu maszyny wirtualnej. W przypadku ponownego uruchomienia maszyny wirtualnej cała zawartość na tych dyskach zostanie wyczyszczona. W związku z tym, ma to miejsce, że pliki danych i pliki dziennika/ponawiania baz danych nie powinny znajdować się na tych dyskach, które nie są utrwalone. Mogą istnieć wyjątki dla niektórych baz danych, w których te dyski nieutrwalone mogą być odpowiednie dla bazy tempdb i tymczasowych tabel przestawnych. Należy jednak unikać używania tych dysków dla maszyn wirtualnych z serii A, ponieważ dyski nieutrwalone są ograniczone w przepływności z tą rodziną maszyn wirtualnych. Aby uzyskać więcej informacji, przeczytaj artykuł [dotyczący dysku tymczasowego na maszynach wirtualnych z systemem Windows na platformie Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

---
> ![Logo Windows.][Logo_Windows] Windows
>
> D:\ dysku na maszynie wirtualnej platformy Azure jest dysk nieutrwalony, który jest obsługiwany przez niektóre dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalony, oznacza to, że wszelkie zmiany wprowadzone do zawartości w D:\ dysk zostanie utracony po ponownym uruchomieniu maszyny wirtualnej. Według "dowolnych zmian", takich jak pliki przechowywane, katalogi utworzone, aplikacje zainstalowane itp.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Maszyny wirtualne platformy Azure z systemem Linux automatycznie instalują dysk w/mnt/Resource, który jest dyskiem nieutrwalonym z kopii zapasowej dysków lokalnych w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalony, oznacza to, że wszelkie zmiany wprowadzone do zawartości w/mnt/Resource są tracone po ponownym uruchomieniu maszyny wirtualnej. Według wszelkich zmian, takich jak pliki przechowywane, katalogi utworzone, aplikacje zainstalowane itp.
>
>

#### <a name="azure-storage-accounts"></a>Konta usługi Azure Storage

Podczas wdrażania usług lub maszyn wirtualnych na platformie Azure wdrażanie dysków VHD i obrazów maszyn wirtualnych jest zorganizowane w jednostkach o nazwie konta usługi Azure Storage. [Konta usługi Azure Storage](../../../storage/common/storage-account-overview.md) mają ograniczenia dotyczące liczby operacji we/wy na sekundę, przepływności lub rozmiarów. W przeszłości te ograniczenia, które opisano w następujących tematach:

- [Elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../../../storage/common/scalability-targets-standard-account.md)
- [Elementy docelowe skalowalności dla kont usługi BLOB Storage na stronie Premium](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

odgrywasz ważną rolę w planowaniu wdrożenia SAP na platformie Azure. Był on w trakcie zarządzania liczbą utrwalonych dysków w ramach konta magazynu. Należy zarządzać kontami magazynu i ostatecznie utworzyć nowe konta magazynu w celu utworzenia bardziej utrwalonych dysków.

W ostatnich latach wprowadzenie [dysków zarządzanych przez platformę Azure](../../managed-disks-overview.md) zostało zwolnione z tych zadań. Zalecenie dotyczące wdrożeń SAP polega na użyciu usługi Azure Managed disks, a nie do samodzielnego zarządzania kontami usługi Azure Storage. Usługa Azure Managed disks będzie rozpowszechniać dyski na różnych kontach magazynu, dzięki czemu nie zostaną przekroczone limity poszczególnych kont magazynu.

W ramach konta magazynu istnieje typ koncepcji folderu o nazwie "Containers", który może służyć do grupowania określonych dysków w określonych kontenerach.

Na platformie Azure nazwa dysku/dysku VHD jest zgodna z następującym połączeniem nazewnictwa, które musi podać unikatową nazwę wirtualnego dysku twardego na platformie Azure:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Ciąg powyżej musi jednoznacznie identyfikować dysk/dysk VHD, który jest przechowywany w usłudze Azure Storage.


#### <a name="azure-persisted-storage-types"></a>Typy utrwalanych magazynów platformy Azure
System Azure oferuje wiele opcji trwałego magazynu, które mogą być używane dla obciążeń SAP i określonych składników stosu SAP. Aby uzyskać więcej informacji, zapoznaj się z dokumentem  [Azure Storage for SAP obciążeń](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure sieci

Microsoft Azure udostępnia infrastrukturę sieciową, która umożliwia mapowanie wszystkich scenariuszy, które chcemy zrealizować przy użyciu oprogramowania SAP. Dostępne są następujące możliwości:

* Dostęp z zewnątrz, bezpośrednio do maszyn wirtualnych za pośrednictwem usług terminalowych systemu Windows lub protokołu SSH/VNC
* Dostęp do usług i określonych portów używanych przez aplikacje na maszynach wirtualnych
* Komunikacja wewnętrzna i rozpoznawanie nazw między grupą maszyn wirtualnych wdrożonymi jako maszyny wirtualne platformy Azure
* Łączność między różnymi lokalizacjami między siecią lokalną klienta a siecią platformy Azure
* Między lokacjami platformy Azure

Więcej informacji można znaleźć tutaj: <https://azure.microsoft.com/documentation/services/virtual-network/>

Istnieje wiele różnych możliwości konfigurowania nazw i rozpoznawania adresów IP na platformie Azure. Istnieje również usługa Azure DNS, która może być używana zamiast konfigurować własny serwer DNS. Więcej informacji można znaleźć w [tym artykule][virtual-networks-manage-dns-in-vnet] i na [tej stronie](https://azure.microsoft.com/services/dns/).

W przypadku scenariuszy obejmujących wiele lokalizacji lub hybrydowych, firma Microsoft polega na tym, że lokalne usługi AD/OpenLDAP/DNS zostały rozszerzone za pośrednictwem sieci VPN lub połączenia prywatnego z platformą Azure. W przypadku niektórych scenariuszy, które opisano w tym miejscu, może być konieczne posiadanie na platformie Azure repliki usługi AD/OpenLDAP.

Ponieważ obsługa sieci i nazw jest istotną częścią wdrożenia bazy danych dla systemu SAP, koncepcja ta jest omówiona bardziej szczegółowo w [Podręczniku wdrażania w systemie DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Tworząc Virtual Network platformy Azure, możesz zdefiniować zakres adresów prywatnych adresów IP przydzielone przez funkcje usługi DHCP platformy Azure. W scenariuszach obejmujących wiele lokalizacji, zdefiniowany zakres adresów IP jest nadal alokowany przy użyciu protokołu DHCP na platformie Azure. Jednak rozpoznawanie nazw domen odbywa się lokalnie (przy założeniu, że maszyny wirtualne są częścią domeny lokalnej) i w związku z tym mogą rozpoznawać adresy poza różnymi Cloud Services platformy Azure.

Każda maszyna wirtualna na platformie Azure musi być podłączona do Virtual Network.

Więcej szczegółów można znaleźć w [tym artykule][resource-groups-networking] i na [tej stronie](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Domyślnie po wdrożeniu maszyny wirtualnej nie można zmienić konfiguracji Virtual Network. Ustawienia protokołu TCP/IP muszą pozostać na serwerze DHCP platformy Azure. Zachowanie domyślne to dynamiczne przypisywanie adresów IP.
>
>

Adres MAC karty sieci wirtualnej może ulec zmianie, na przykład po zmianie rozmiaru, a system operacyjny gościa systemu Windows lub Linux wybiera nową kartę sieciową i automatycznie używa protokołu DHCP do przypisywania adresów IP i DNS w tym przypadku.

##### <a name="static-ip-assignment"></a>Przypisanie statycznego adresu IP
Można przypisywać stałe lub zastrzeżone adresy IP do maszyn wirtualnych w ramach Virtual Network platformy Azure. Uruchomienie maszyn wirtualnych w usłudze Azure Virtual Network otwiera doskonałą możliwość korzystania z tej funkcji, jeśli jest to konieczne lub wymagane dla niektórych scenariuszy. Przypisanie adresu IP pozostaje ważne przez cały czas istnienia maszyny wirtualnej, niezależnie od tego, czy maszyna wirtualna jest uruchomiona, czy wyłączona. W związku z tym podczas definiowania zakresu adresów IP dla Virtual Network należy uwzględnić ogólną liczbę maszyn wirtualnych (uruchomionych i zatrzymanych maszyn wirtualnych). Adres IP pozostaje przypisany do momentu usunięcia maszyny wirtualnej i jej interfejsu sieciowego lub do momentu ponownego przydzielenia adresu IP. Aby uzyskać więcej informacji, Przeczytaj [ten artykuł][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Statyczne adresy IP należy przypisać do poszczególnych vNICs na platformie Azure. Nie należy przypisywać statycznych adresów IP w systemie operacyjnym gościa do wirtualnej karty sieciowej. Niektóre usługi platformy Azure, takie jak Azure Backup, korzystają z faktu, że co najmniej podstawowy wirtualnej karty sieciowej jest ustawiony na wartość DHCP, a nie na statyczne adresy IP. Zobacz również dokument [Rozwiązywanie problemów z kopiami zapasowymi maszyny wirtualnej platformy Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Wiele kart sieciowych na maszynę wirtualną

Można zdefiniować wiele kart interfejsu sieci wirtualnej (wirtualnej karty sieciowej) dla maszyny wirtualnej platformy Azure. Z możliwością korzystania z wielu vNICs można rozpocząć konfigurowanie separacji ruchu sieciowego, gdzie na przykład ruch klienta jest kierowany przez jeden wirtualnej karty sieciowej, a ruch zaplecza jest kierowany przez drugi wirtualnej karty sieciowej. Zależnie od typu maszyny wirtualnej istnieją różne ograniczenia dotyczące liczby vNICs przypisanych do maszyny wirtualnej. Dokładne informacje, funkcje i ograniczenia można znaleźć w następujących artykułach:

* [Tworzenie maszyny wirtualnej z systemem Windows z wieloma kartami sieciowymi][virtual-networks-multiple-nics-windows]
* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu wielu kart sieciowych][virtual-networks-multiple-nics-linux]
* [Wdrażanie maszyn wirtualnych z wielojęzyczną kartą sieciową przy użyciu szablonu][virtual-network-deploy-multinic-arm-template]
* [Wdrażanie maszyn wirtualnych z użyciem kilku kart sieciowych przy użyciu programu PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Wdrażanie maszyn wirtualnych z użyciem kilku kart sieciowych przy użyciu interfejsu wiersza polecenia platformy Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Łączność między lokacjami

Wiele lokalizacji jest maszynami wirtualnymi platformy Azure i lokalnymi połączonymi z niewidocznym i trwałym połączeniem sieci VPN. Oczekuje się, że jest to najbardziej typowy wzorzec wdrożenia SAP na platformie Azure. Przyjęto założenie, że procedury operacyjne i procesy z wystąpieniami SAP na platformie Azure powinny działać w sposób przezroczysty. Oznacza to, że powinno być możliwe wydrukowanie z tych systemów oraz użycie systemu zarządzania transportem SAP (TMS) do transportowania zmian z systemu na platformie Azure do systemu testowego, który jest wdrażany lokalnie. Więcej dokumentacji dotyczącej lokacja-lokacja można znaleźć w [tym artykule][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Urządzenie tunelu sieci VPN

Aby można było utworzyć połączenie lokacja-lokacja (lokalne centrum danych z centrum danych platformy Azure), należy uzyskać i skonfigurować urządzenie sieci VPN lub użyć usługi Routing i dostęp zdalny (RRAS), która została wprowadzona jako składnik oprogramowania z systemem Windows Server 2012.

* [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informacje o urządzeniach sieci VPN używanych na potrzeby połączeń bramy VPN typu lokacja-lokacja][vpn-gateway-about-vpn-devices]
* [Brama sieci VPN — często zadawane pytania][vpn-gateway-vpn-faq]

![Połączenie lokacja-lokacja między środowiskiem lokalnym i platformą Azure][planning-guide-figure-600]

Powyższy rysunek przedstawia dwie subskrypcje platformy Azure, które mają podzakresy adresów IP zarezerwowane do użycia w sieciach wirtualnych na platformie Azure. Połączenie z sieci lokalnej do platformy Azure jest nawiązywane za pośrednictwem sieci VPN.

#### <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Sieć VPN typu punkt-lokacja wymaga, aby każdy komputer kliencki łączył się ze swoją własną siecią VPN na platformę Azure. W przypadku scenariuszy SAP jest to niepraktyczne w przypadku połączeń punkt-lokacja. W związku z tym nie są przekazywane żadne dalsze odwołania do połączenia sieci VPN typu punkt-lokacja.

Więcej informacji można znaleźć tutaj
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>Sieć VPN z obsługą wiele lokacji

Platforma Azure obecnie oferuje również możliwość tworzenia połączenia sieci VPN z obsługą wielu lokacji dla jednej subskrypcji platformy Azure. Wcześniej jedna subskrypcja została ograniczona do jednego połączenia sieci VPN typu lokacja-lokacja. To ograniczenie odbiega od połączeń sieci VPN z obsługą jednej lokacji w ramach pojedynczej subskrypcji. Dzięki temu można korzystać z więcej niż jednego regionu świadczenia usługi Azure w ramach określonej subskrypcji za pomocą konfiguracji wielu lokalizacji.

Aby uzyskać więcej dokumentacji, zobacz [ten artykuł][vpn-gateway-create-site-to-site-rm-powershell] .

#### <a name="vnet-to-vnet-connection"></a>Połączenie między sieciami wirtualnymi

W przypadku korzystania z sieci VPN w różnych lokacjach należy skonfigurować oddzielne Virtual Network platformy Azure w każdym regionie. Jednak często istnieje wymóg komunikacji między składnikami oprogramowania w różnych regionach. W idealnym przypadku ta komunikacja nie powinna być kierowana z jednego regionu świadczenia usługi Azure do lokalnego i z innych regionów świadczenia usługi Azure. Na stronie skrót platforma Azure oferuje możliwość skonfigurowania połączenia między usługą Azure Virtual Network w jednym regionie i innym Virtual Network platformy Azure hostowanym w innym regionie. Ta funkcja jest nazywana połączeniem między sieciami wirtualnymi. Więcej informacji na temat tej funkcji można znaleźć tutaj: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/> .

#### <a name="private-connection-to-azure-expressroute"></a>Połączenie prywatne z usługą Azure ExpressRoute

Microsoft Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną klienta lub w środowisku wspólnej lokalizacji. Usługa ExpressRoute jest oferowana przez różnych dostawców sieci VPN lub innych dostawców usług sieciowych. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Połączenia ExpressRoute oferują wyższy poziom zabezpieczeń, większą niezawodność dzięki wielu obwodów równoległych, szybszym szybkościom i krótszym opóźnieniu niż typowe połączenia przez Internet.

Więcej informacji o usłudze Azure ExpressRoute i ofertach znajdziesz tutaj:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Usługa Express Route umożliwia używanie wielu subskrypcji platformy Azure za pomocą jednego obwodu usługi ExpressRoute zgodnie z opisem w tym miejscu

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Wymuszone tunelowanie w przypadku wielu lokalizacji
W przypadku maszyn wirtualnych łączących domeny lokalne za pośrednictwem lokacja-lokacja, punkt-lokacja lub ExpressRoute należy upewnić się, że ustawienia internetowego serwera proxy zostały wdrożone dla wszystkich użytkowników w tych maszynach wirtualnych. Domyślnie oprogramowanie uruchomione na tych maszynach wirtualnych lub użytkowników korzystających z przeglądarki w celu uzyskania dostępu do Internetu nie przejdzie przez firmowy serwer proxy, ale nawiąże połączenie proste z Internetem za pośrednictwem platformy Azure. Mimo że ustawienie serwera proxy nie jest rozwiązaniem 100%, aby skierować ruch za pośrednictwem serwera proxy firmy, ponieważ jest on odpowiedzialny za oprogramowanie i usługi do sprawdzania serwera proxy. Jeśli program działający na maszynie wirtualnej nie wykonuje tego działania lub administrator manipuluje ustawieniami, ruch do Internetu można przeprowadzić ponownie, bezpośrednio przez platformę Azure do Internetu.

Aby uniknąć takiej bezpośredniej łączności z Internetem, można skonfigurować Wymuszone tunelowanie przy użyciu połączenia typu lokacja-lokacja między środowiskiem lokalnym i platformą Azure. Szczegółowy opis funkcji wymuszonego tunelowania jest publikowany w tym miejscu <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Wymuszone tunelowanie za pomocą ExpressRoute jest włączane przez klientów anonsowanie trasy domyślnej za pośrednictwem sesji komunikacji równorzędnej BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Podsumowanie dotyczące sieci platformy Azure

Ten rozdział zawiera wiele ważnych punktów dotyczących sieci platformy Azure. Poniżej znajduje się podsumowanie głównych punktów:

* Usługa Azure Virtual Networks umożliwia umieszczenie struktury sieci w ramach wdrożenia platformy Azure. Sieci wirtualnych może być odizolowany od siebie lub za pomocą ruchu sieciowego grup zabezpieczeń między sieci wirtualnych można kontrolować.
* Za pomocą usługi Azure Virtual Networks można przypisywać zakresy adresów IP do maszyn wirtualnych lub przypisywać stałe adresy IP do maszyn wirtualnych
* Aby skonfigurować połączenie typu lokacja-lokacja lub punkt-lokacja, musisz najpierw utworzyć Virtual Network platformy Azure
* Po wdrożeniu maszyny wirtualnej nie można już zmieniać Virtual Network przypisanej do maszyny wirtualnej

### <a name="quotas-in-azure-virtual-machine-services"></a>Przydziały w usługach Azure Virtual Machines
Musimy jasno wiedzieć, że infrastruktura magazynu i sieci jest udostępniana między maszynami wirtualnymi, na których działają różne usługi w infrastrukturze platformy Azure. Podobnie jak w przypadku własnych centrów danych klienta, nadmierne Inicjowanie obsługi niektórych zasobów infrastruktury odbywa się w pewnym stopniu. Platforma Microsoft Azurea używa dysku, procesora, sieci i innych przydziałów w celu ograniczenia zużycia zasobów i zachowania spójnej i deterministycznej wydajności.  Różne typy maszyn wirtualnych (A5, A6 itp.) mają różne przydziały dla liczby dysków, procesora CPU, pamięci RAM i sieci.

> [!NOTE]
> Zasoby procesora i pamięci dla typów maszyn wirtualnych obsługiwane przez oprogramowanie SAP są wstępnie przydzielone na węzłach hosta. Oznacza to, że po wdrożeniu maszyny wirtualnej zasoby na hoście są dostępne zgodnie z definicją typu maszyny wirtualnej.
>
>

W przypadku planowania i ustalania rozmiaru SAP w rozwiązaniach platformy Azure należy wziąć pod uwagę limity przydziału dla każdego rozmiaru maszyny wirtualnej. Przydziały maszyn wirtualnych są opisane [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Opisane limity przydziałów reprezentują teoretyczne wartości maksymalne.  Limit liczby operacji we/wy na dysku można osiągnąć przy użyciu małych operacji we/wy (8 KB), ale prawdopodobnie nie można go osiągnąć przy użyciu dużych operacji we/wy (1 MB).  Limit liczby operacji we/wy jest wymuszany na poziomie szczegółowości pojedynczego dysku.

Jako bezwzględne drzewo decyzyjne decyduje o tym, czy system SAP mieści się w usługach Azure Virtual Machines i jego możliwościach, czy istniejący system musi być skonfigurowany inaczej w celu wdrożenia systemu na platformie Azure, można użyć poniższego drzewa decyzyjnego:

![Drzewo decyzyjne do podejmowania decyzji o możliwości wdrożenia oprogramowania SAP na platformie Azure][planning-guide-figure-700]

1. Najważniejszymi informacjami, które należy zacząć od, jest wymagania dotyczące punktów SAP dla danego systemu SAP. Wymagania dotyczące punktów SAP należy oddzielić do części systemu DBMS i części aplikacji SAP, nawet jeśli system SAP został już wdrożony lokalnie w konfiguracji 2-warstwowej. W przypadku istniejących systemów protokoły SAP związane z używanym sprzętem często mogą być określane lub szacowane na podstawie istniejących testów porównawczych SAP. Wyniki można znaleźć [tutaj](https://sap.com/about/benchmark.html). W przypadku nowo wdrożonych systemów SAP należy przeszedł postęp, który powinien określić wymagania systemu SAP.
1. W przypadku istniejących systemów należy mierzyć wolumin we/wy oraz operacje we/wy na sekundę na serwerze DBMS. W przypadku nowo planowanych systemów ćwiczenie zmiany wielkości dla nowego systemu powinny również dawać przybliżone pomysły dotyczące wymagań we/wy po stronie systemu DBMS. W przypadku braku pewności należy przeprowadzić weryfikację koncepcji.
1. Porównanie wymagania dotyczącego punktów SAP dla serwera DBMS z wydaniami, które mogą zapewnić różne typy maszyn wirtualnych platformy Azure. Informacje na temat punktów SAP różnych typów maszyn wirtualnych platformy Azure opisano w temacie SAP Note [1928533]. Fokus powinien znajdować się na maszynie wirtualnej z systemem DBMS najpierw, ponieważ warstwa bazy danych jest warstwą w systemie SAP NetWeaver, która nie jest skalowana w większości wdrożeń. W przeciwieństwie do warstwy aplikacji SAP można skalować w poziomie. Jeśli żaden z typów maszyn wirtualnych platformy Azure obsługiwanych przez SAP nie może dostarczyć wymaganych punktów SAP, nie można uruchomić obciążenia planowanego systemu SAP na platformie Azure. Należy wdrożyć system lokalnie lub trzeba zmienić wolumin obciążeń dla systemu programu.
1. Zgodnie z opisem w [tym miejscu (Linux)][virtual-machines-sizes-linux] i [tym miejscu (Windows)][virtual-machines-sizes-windows]platforma Azure wymusza przydział operacji we/wy na dysku niezależnie od tego, czy używany jest magazyn w warstwie Standardowa czy Premium Storage. Zależnie od typu maszyny wirtualnej liczba dysków z danymi, które mogą być zainstalowane, jest różna. W związku z tym można obliczyć maksymalną liczbę IOPS, którą można osiągnąć przy użyciu poszczególnych typów maszyn wirtualnych. Zależnie od układu pliku bazy danych, dyski można rozdzielić na jeden wolumin w systemie operacyjnym gościa. Jeśli jednak bieżąca liczba operacji we/wy wdrożonego systemu SAP przekracza obliczone limity największego typu maszyn wirtualnych platformy Azure i nie ma możliwości zrekompensowania większej ilości pamięci, obciążenie systemu SAP może być poważnie ograniczone. W takich przypadkach można dotarciu do punktu, w którym nie należy wdrażać systemu na platformie Azure.
1. W szczególności w systemach SAP, które są wdrażane lokalnie w konfiguracjach 2-warstwowych, może być konieczne skonfigurowanie systemu Azure w konfiguracji 3-warstwowej. W tym kroku należy sprawdzić, czy w warstwie aplikacji SAP znajduje się składnik, którego nie można skalować w poziomie i które nie mieszczą się w zasobach procesora i pamięci dla różnych typów maszyn wirtualnych platformy Azure. Jeśli istnieje taki składnik, system SAP i jego obciążenie nie mogą zostać wdrożone na platformie Azure. Jeśli jednak można skalować składniki aplikacji SAP na wiele maszyn wirtualnych platformy Azure, system można wdrożyć na platformie Azure.

Jeśli składniki warstwy aplikacji systemów DBMS i SAP można uruchamiać na maszynach wirtualnych platformy Azure, konfiguracja musi być zdefiniowana w odniesieniu do:

* Liczba maszyn wirtualnych platformy Azure
* Typy maszyn wirtualnych dla poszczególnych składników
* Liczba operacji we/wy na maszynie wirtualnej DBMS, aby zapewnić wystarczającą liczbę operacji wejścia/wyjścia

## <a name="managing-azure-assets"></a>Zarządzanie zasobami platformy Azure

### <a name="azure-portal"></a>Azure Portal

Azure Portal to jeden z trzech interfejsów do zarządzania wdrożeniami maszyn wirtualnych platformy Azure. Podstawowe zadania zarządzania, takie jak wdrażanie maszyn wirtualnych z obrazów, można wykonać za pomocą Azure Portal. Ponadto można również wykonać zadania tworzenia kont magazynu, sieci wirtualnych i innych składników platformy Azure Azure Portal. Jednak funkcje takie jak przekazywanie dysków VHD ze środowiska lokalnego na platformę Azure lub kopiowanie dysku VHD na platformie Azure to zadania, które wymagają narzędzi innych firm lub ich administracji za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia.

![Microsoft Azure Portal — Omówienie maszyny wirtualnej][planning-guide-figure-800]


Zadania administracyjne i konfiguracyjne dla wystąpienia maszyny wirtualnej są możliwe z poziomu Azure Portal.

Oprócz ponownego uruchamiania i zamykania maszyny wirtualnej można również dołączyć, odłączyć i utworzyć dyski danych dla wystąpienia maszyny wirtualnej, przechwycić wystąpienie na potrzeby przygotowania obrazu i skonfigurować rozmiar wystąpienia maszyny wirtualnej.

Azure Portal zapewnia podstawowe funkcje wdrażania i konfigurowania maszyn wirtualnych oraz wielu innych usług platformy Azure. Nie wszystkie dostępne funkcje są jednak objęte Azure Portal. W Azure Portal nie jest możliwe wykonywanie zadań, takich jak:

* Przekazywanie wirtualnych dysków twardych na platformę Azure
* Kopiowanie maszyn wirtualnych


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Zarządzanie za pomocą poleceń cmdlet Microsoft Azure PowerShell

Program Windows PowerShell to zaawansowane i rozszerzalne środowisko, które zostało powszechnie przyjęte przez klientów wdrażających większą liczbę systemów na platformie Azure. Po zainstalowaniu poleceń cmdlet programu PowerShell na komputerze stacjonarnym, laptopie lub dedykowanej stacji zarządzania można uruchomić polecenie cmdlet programu PowerShell zdalnie.

W [tym artykule][powershell-install-configure]opisano proces włączania lokalnego pulpitu/laptopa na potrzeby używania Azure PowerShell poleceń cmdlet i sposobu konfigurowania tych elementów do użycia w ramach subskrypcji platformy Azure.

Bardziej szczegółowe instrukcje dotyczące instalowania, aktualizowania i konfigurowania poleceń cmdlet Azure PowerShell można znaleźć w [tym rozdziale przewodnika wdrażania][deployment-guide-4.1].

Dotychczasowa obsługa klienta polega na tym, że program PowerShell (PS) korzysta z bardziej wydajnego narzędzia do wdrażania maszyn wirtualnych i tworzenia niestandardowych kroków wdrożenia maszyn wirtualnych. Wszyscy klienci z uruchomionymi wystąpieniami SAP na platformie Azure używają poleceń cmdlet PS do uzupełniania zadań zarządzania, które są wykonywane w Azure Portal lub nawet używają poleceń cmdlet systemu PS wyłącznie do zarządzania wdrożeniami na platformie Azure. Ponieważ polecenia cmdlet specyficzne dla platformy Azure korzystają z tej samej konwencji nazewnictwa co ponad 2000 poleceń cmdlet związanych z systemem Windows, jest to proste zadanie dla administratorów systemu Windows korzystające z tych poleceń cmdlet.

Zobacz przykład tutaj: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Wdrożenie rozszerzenia platformy Azure dla oprogramowania SAP (zobacz rozdział [rozszerzenia platformy Azure dla oprogramowania SAP][planning-guide-9.1] w tym dokumencie) jest możliwe tylko za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia. W związku z tym konieczne jest skonfigurowanie i skonfigurowanie programu PowerShell lub interfejsu wiersza polecenia podczas wdrażania systemu SAP NetWeaver i administrowania nim na platformie Azure.

Ponieważ platforma Azure oferuje więcej funkcji, nowe polecenia cmdlet środowiska PS zostaną dodane, które wymagają aktualizacji poleceń cmdlet. W związku z tym warto sprawdzić witrynę pobierania platformy Azure co najmniej raz w miesiącu <https://azure.microsoft.com/downloads/> dla nowej wersji poleceń cmdlet. Nowa wersja jest zainstalowana na starszej wersji.

Aby uzyskać ogólną listę poleceń programu PowerShell związanych z platformą Azure, zobacz tutaj: <https://docs.microsoft.com/powershell/azure/> .

### <a name="management-via-microsoft-azure-cli-commands"></a>Zarządzanie za pomocą poleceń interfejsu wiersza polecenia Microsoft Azure

W przypadku klientów korzystających z systemu Linux i zarządzania zasobami platformy Azure program PowerShell może nie być opcją. Firma Microsoft oferuje interfejs wiersza polecenia platformy Azure jako alternatywę.
Interfejs wiersza polecenia platformy Azure udostępnia zestaw międzyplatformowych poleceń typu "open source" do pracy z platformą Azure. Interfejs wiersza polecenia platformy Azure zapewnia wiele funkcji dostępnych w Azure Portal.

Aby uzyskać informacje na temat instalacji, konfiguracji i sposobu używania poleceń interfejsu wiersza polecenia do wykonywania zadań platformy Azure, zobacz

* [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure][xplat-cli]
* [Wdrażanie maszyn wirtualnych i zarządzanie nimi za pomocą szablonów Azure Resource Manager i interfejsu wiersza polecenia platformy Azure] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Korzystanie z klasycznego interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows z usługą Azure Resource Manager][xplat-cli-azure-resource-manager]

Zapoznaj się z rozdziałem [interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux][deployment-guide-4.5.2] w [przewodniku wdrażania][planning-guide] dotyczącym używania interfejsu wiersza polecenia platformy Azure do wdrożenia rozszerzenia platformy Azure dla oprogramowania SAP


## <a name="first-steps-planning-a-deployment"></a>Pierwsze kroki planowania wdrożenia
Pierwszy krok w ramach planowania wdrożenia nie umożliwia sprawdzenia maszyn wirtualnych dostępnych do uruchamiania oprogramowania SAP. Pierwszym krokiem może być czas, który jest czasochłonny, ale najważniejszym rozwiązaniem jest współdziałanie z zespołami ds. zgodności i zabezpieczeń w firmie na temat tego, jakie są warunki graniczne wdrażania tego typu obciążeń lub procesów firmy SAP w chmurze publicznej. Jeśli firma wdrożyła inne oprogramowanie przed platformą Azure, proces ten może być łatwy. Jeśli firma jest więcej na początku podróży, może być więcej dyskusji koniecznych do ustalenia warunków granicznych, warunków zabezpieczeń, które pozwalają na hostowanie niektórych danych SAP i procesów firmy SAP w chmurze publicznej.

Jak przydatna pomoc, możesz wskazać [oferty zgodności firmy Microsoft](/microsoft-365/compliance/offering-home) , aby uzyskać listę zgodności oferowanych przez firmę Microsoft.

Inne obszary problemów, takie jak szyfrowanie danych w przypadku przechowywania danych w czasie spoczynku lub inne szyfrowanie w usłudze Azure, zostały udokumentowane w [omówieniu usługi Azure Encryption](../../../security/fundamentals/encryption-overview.md).

Nie Szacuj nieoszacowanej fazy projektu w planowaniu. Tylko wtedy, gdy masz umowę i zasady dotyczące tego tematu, musisz przejść do następnego kroku, który jest planowaniem architektury sieci wdrażanej na platformie Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Różne sposoby wdrażania maszyn wirtualnych dla oprogramowania SAP na platformie Azure

W tym rozdziale przedstawiono różne sposoby wdrożenia maszyny wirtualnej na platformie Azure. Dalsze procedury przygotowawcze, jak również Obsługa dysków VHD i maszyn wirtualnych na platformie Azure, zostały omówione w tym rozdziale.

### <a name="deployment-of-vms-for-sap"></a>Wdrażanie maszyn wirtualnych dla oprogramowania SAP

Microsoft Azure oferuje wiele sposobów wdrażania maszyn wirtualnych i skojarzonych dysków. W związku z tym ważne jest, aby zrozumieć różnice wynikające z tego, że przygotowania maszyn wirtualnych mogą się różnić w zależności od metody wdrażania. Ogólnie rzecz biorąc, należy zapoznać się z następującymi scenariuszami:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Przeniesienie maszyny wirtualnej z lokalnego na platformę Azure na dysk nieuogólniony

Planujesz przenieść określony system SAP ze środowiska lokalnego na platformę Azure. Można to zrobić przez przekazanie wirtualnego dysku twardego, który zawiera system operacyjny, pliki binarne SAP oraz pliki binarne DBMS oraz pliki dziennika systemu DBMS do platformy Azure. W przeciwieństwie do [scenariusza #2 poniżej][planning-guide-5.1.2]można zachować nazwę hosta, identyfikator SID SAP i konta użytkowników SAP na maszynie wirtualnej platformy Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. W związku z tym nie trzeba uogólniać obrazu. Zapoznaj się z rozdziałami [przygotowanie do przeniesienia maszyny wirtualnej z lokalizacji lokalnej na platformę Azure z nieuogólnionym dyskiem][planning-guide-5.2.1] tego dokumentu dla lokalnych kroków przygotowania oraz przekazywania nieuogólnionych maszyn wirtualnych lub dysków VHD na platformę Azure. Przeczytaj [scenariusz rozdziału 3: przeniesienie maszyny wirtualnej ze środowiska lokalnego przy użyciu nieuogólnionego wirtualnego dysku twardego platformy Azure z programem SAP][deployment-guide-3.4] w [przewodniku wdrażania][deployment-guide] , aby zapoznać się ze szczegółowymi krokami wdrażania takiego obrazu na platformie Azure.

Inna opcja, która nie omawiamy szczegółowo szczegółowych informacji w tym przewodniku, polega na tym, że Azure Site Recovery replikowania serwerów aplikacji SAP NetWeaver oraz usług SAP NetWeaver centralnych na platformę Azure. Nie zaleca się używania Azure Site Recovery dla warstwy bazy danych i raczej używać mechanizmów replikacji określonych w bazie danych, takich jak system HANA. Aby uzyskać więcej informacji, zobacz rozdział [Ochrona SAP](https://docs.microsoft.com/azure/site-recovery/site-recovery-workload#protect-sap) [informacji o odzyskiwaniu po awarii dla aplikacji lokalnych](https://docs.microsoft.com/azure/site-recovery/site-recovery-workload) .

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Wdrażanie maszyny wirtualnej z obrazem specyficznym dla klienta

Ze względu na określone wymagania dotyczące poprawek w wersji systemu operacyjnego lub DBMS, udostępnione obrazy w portalu Azure Marketplace mogą nie spełniać Twoich potrzeb. W związku z tym może być konieczne utworzenie maszyny wirtualnej przy użyciu własnego obrazu prywatnego systemu operacyjnego/DBMS, który można wdrożyć kilka razy później. Aby przygotować taki obraz prywatny do duplikacji, należy wziąć pod uwagę następujące elementy:

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Więcej szczegółów można znaleźć tutaj: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Ustawienia systemu Windows (takie jak identyfikator SID systemu Windows i nazwa hosta) muszą być abstrakcyjne/uogólnione na lokalnej maszynie wirtualnej za pomocą polecenia Sysprep.
>
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Postępuj zgodnie z krokami opisanymi w tych artykułach dla [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]lub [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], aby przygotować dysk VHD do przekazania do platformy Azure.
>
>

---
Jeśli zainstalowano już zawartość SAP na lokalnej maszynie wirtualnej (szczególnie w przypadku systemów 2-warstwowych), można dostosować ustawienia systemu SAP po wdrożeniu maszyny wirtualnej platformy Azure za pomocą procedury zmiany nazwy wystąpienia obsługiwanej przez Menedżera aprowizacji oprogramowania SAP (SAP Note [1619720]). Zapoznaj się z rozdziałami [przygotowanie do wdrożenia maszyny wirtualnej z obrazem specyficznym dla klienta dla oprogramowania SAP][planning-guide-5.2.2] i [przekazaniem dysku VHD ze środowiska lokalnego do platformy Azure][planning-guide-5.3.2] tego dokumentu na potrzeby lokalnych kroków przygotowania i przekazania uogólnionej maszyny wirtualnej na platformę Azure. Przeczytaj [scenariusz rozdział 2: Wdrażanie maszyny wirtualnej z obrazem niestandardowym dla oprogramowania SAP][deployment-guide-3.3] w [przewodniku wdrażania][deployment-guide] , aby zapoznać się ze szczegółowymi krokami wdrażania takiego obrazu na platformie Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Wdrażanie maszyny wirtualnej poza portalem Azure Marketplace

Aby wdrożyć maszynę wirtualną, należy użyć obrazu maszyny wirtualnej firmy Microsoft lub innej firmy z witryny Azure Marketplace. Po wdrożeniu maszyny wirtualnej na platformie Azure postępuj zgodnie z tymi samymi wskazówkami i narzędziami, aby zainstalować oprogramowanie SAP i/lub DBMS w ramach maszyny wirtualnej, tak jak w środowisku lokalnym. Aby zapoznać się z bardziej szczegółowym opisem wdrożenia, zobacz artykuł [scenariusz 1: Wdrażanie maszyny wirtualnej poza portalem Azure Marketplace dla oprogramowania SAP][deployment-guide-3.2] w [przewodniku wdrażania][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Przygotowywanie maszyn wirtualnych za pomocą oprogramowania SAP dla platformy Azure

Przed przekazaniem maszyn wirtualnych do platformy Azure musisz upewnić się, że maszyny wirtualne i wirtualne dyski twarde spełniają określone wymagania. Istnieją małe różnice w zależności od używanej metody wdrażania.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Przygotowanie do przeniesienia maszyny wirtualnej z lokalizacji lokalnej na platformę Azure przy użyciu dysku nieuogólnionego

Typową metodą wdrażania jest przeniesienie istniejącej maszyny wirtualnej, która uruchamia system SAP ze środowiska lokalnego na platformę Azure. Ta maszyna wirtualna i system SAP w maszynie wirtualnej powinny być uruchamiane na platformie Azure przy użyciu tej samej nazwy hosta i być może tego samego identyfikatora SID SAP. W takim przypadku system operacyjny gościa maszyny wirtualnej nie powinien być uogólniony dla wielu wdrożeń. Jeśli sieć lokalna została rozszerzona na platformę Azure, nawet te same konta domeny mogą być używane w ramach maszyny wirtualnej, ponieważ były używane przed lokalną.

Wymagania dotyczące przygotowywania własnego dysku maszyny wirtualnej platformy Azure:

* Początkowo wirtualny dysk twardy zawierający system operacyjny może mieć maksymalny rozmiar 127 GB. To ograniczenie zostało wyeliminowane na koniec marca 2015. Teraz wirtualny dysk twardy zawierający system operacyjny może mieć rozmiar do 1 TB, jak również inny dysk VHD hostowanej usługi Azure Storage.
* Musi być w formacie stałego dysku VHD. Dynamiczne dyski VHD lub wirtualne dyski twarde w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statyczne dyski VHD podczas przekazywania dysku VHD za pomocą programu PowerShell polecenia cmdlet lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinny być ponownie zainstalowane na platformie Azure, muszą być również w stałym formacie VHD. Przeczytaj [ten artykuł](../../managed-disks-overview.md) , aby uzyskać limity rozmiaru dysków danych. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statyczne dyski VHD podczas przekazywania dysku VHD za pomocą programu PowerShell polecenia cmdlet lub interfejsu wiersza polecenia
* Dodaj kolejne konto lokalne z uprawnieniami administratora, które mogą być używane przez pomoc techniczną firmy Microsoft lub które mogą być przypisane jako kontekst dla usług i aplikacji do uruchomienia, dopóki maszyna wirtualna nie zostanie wdrożona i będzie można używać większej liczby odpowiednich użytkowników.
* Dodaj inne konta lokalne, ponieważ mogą one być konieczne w przypadku określonego scenariusza wdrażania.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> W tym scenariuszu do przekazania i wdrożenia maszyny wirtualnej na platformie Azure nie jest wymagane uogólnienie (Sysprep) maszyny wirtualnej.
> Upewnij się, że dysk D:\ nie jest używany.
> Ustaw autoinstalację dysków dla dołączonych dysków zgodnie z opisem w rozdziale [Ustawianie opcji instalacji automatycznej dla dołączonych dysków][planning-guide-5.5.3] w tym dokumencie.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> W tym scenariuszu nie jest wymagane uogólnianie i wdrażanie maszyny wirtualnej na platformie Azure.
> Upewnij się, że/mnt/Resource nie jest używany i że wszystkie dyski są zainstalowane za pośrednictwem identyfikatora UUID. W przypadku dysku systemu operacyjnego upewnij się, że wpis programu inicjującego uwzględnia również instalację opartą na identyfikatorze UUID.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Przygotowanie do wdrożenia maszyny wirtualnej z obrazem specyficznym dla klienta dla oprogramowania SAP

Pliki VHD zawierające uogólniony system operacyjny są przechowywane w kontenerach na kontach usługi Azure Storage lub w postaci obrazów dysków zarządzanych. Nową maszynę wirtualną można wdrożyć z takiego obrazu, odwołując dysk VHD lub obraz dysku zarządzanego jako źródło w plikach szablonów wdrożenia zgodnie z opisem w rozdziale [Scenariusz 2: Wdrażanie maszyny wirtualnej z niestandardowym obrazem dla SAP][deployment-guide-3.3] [przewodnika wdrażania][deployment-guide].

Wymagania dotyczące przygotowywania własnego obrazu maszyny wirtualnej platformy Azure:

* Początkowo wirtualny dysk twardy zawierający system operacyjny może mieć maksymalny rozmiar 127 GB. To ograniczenie zostało wyeliminowane na koniec marca 2015. Teraz wirtualny dysk twardy zawierający system operacyjny może mieć rozmiar do 1 TB, jak również inny dysk VHD hostowanej usługi Azure Storage.
* Musi być w formacie stałego dysku VHD. Dynamiczne dyski VHD lub wirtualne dyski twarde w formacie VHDx nie są jeszcze obsługiwane na platformie Azure. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statyczne dyski VHD podczas przekazywania dysku VHD za pomocą programu PowerShell polecenia cmdlet lub interfejsu wiersza polecenia
* Wirtualne dyski twarde, które są zainstalowane na maszynie wirtualnej i powinny być ponownie zainstalowane na platformie Azure, muszą być również w stałym formacie VHD. Przeczytaj [ten artykuł](../../managed-disks-overview.md) , aby uzyskać limity rozmiaru dysków danych. Dynamiczne wirtualne dyski twarde zostaną przekonwertowane na statyczne dyski VHD podczas przekazywania dysku VHD za pomocą programu PowerShell polecenia cmdlet lub interfejsu wiersza polecenia
* Dodaj inne konta lokalne, ponieważ mogą one być konieczne w przypadku określonego scenariusza wdrażania.
* Jeśli obraz zawiera instalację oprogramowania SAP NetWeaver i zmiana nazwy hosta z oryginalnej nazwy w punkcie wdrożenia platformy Azure jest prawdopodobnie zalecana, należy skopiować najnowsze wersje dysku DVD Menedżera aprowizacji oprogramowania SAP do szablonu. Umożliwi to łatwe użycie funkcji zmiany nazwy przez SAP w celu dostosowania zmienionej nazwy hosta i/lub zmianę identyfikatora SID systemu SAP w ramach wdrożonego obrazu maszyny wirtualnej zaraz po rozpoczęciu nowej kopii.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Upewnij się, że dysk D:\ nie jest używany do ustawiania automatycznej instalacji dysków dla dołączonych dysków, zgodnie z opisem w rozdziale [Ustawianie Ustawienia autoinstalacji dla dołączonych dysków][planning-guide-5.5.3] w tym dokumencie.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Upewnij się, że/mnt/Resource nie jest używany i że wszystkie dyski są zainstalowane za pośrednictwem identyfikatora UUID. W przypadku dysku systemu operacyjnego upewnij się, że wpis programu inicjującego uwzględnia również instalację opartą na identyfikatorze UUID.
>
>

---
* Graficzny interfejs użytkownika SAP (na potrzeby celów administracyjnych i konfiguracji) może być wstępnie zainstalowany w tym szablonie.
* Inne oprogramowanie wymagane do pomyślnego uruchomienia maszyn wirtualnych w scenariuszach obejmujących wiele lokalizacji można zainstalować, o ile to oprogramowanie może współdziałać z nazwą maszyny wirtualnej.

Jeśli maszyna wirtualna jest wystarczająco gotowa, aby była ogólna i ostatecznie niezależna od kont/użytkowników, które nie są dostępne w scenariuszu wdrożenia na platformie Azure, jest wykonywany ostatni krok przygotowania do uogólniania takiego obrazu.

##### <a name="generalizing-a-vm"></a>Uogólnianie maszyny wirtualnej
---
> ![Logo Windows.][Logo_Windows] Windows
>
> Ostatnim krokiem jest zalogowanie się do maszyny wirtualnej przy użyciu konta administratora. Otwórz okno poleceń systemu Windows jako *administrator*. Przejdź do%windir%\Windows\System32\Sysprep i wykonaj sysprep.exe.
> Zostanie wyświetlone małe okno. Ważne jest, aby zaznaczyć opcję **generalize** (domyślnie unchecked) i zmienić domyślnie opcję zamykania na "Shutdown". W tej procedurze przyjęto założenie, że proces Sysprep jest wykonywany lokalnie w systemie operacyjnym gościa maszyny wirtualnej.
> Jeśli chcesz wykonać procedurę z maszyną wirtualną już uruchomioną na platformie Azure, wykonaj kroki opisane w [tym artykule](../../windows/capture-image-resource.md).
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> [Jak przechwycić maszynę wirtualną z systemem Linux do użycia jako szablon usługi Resource Manager?][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferowanie maszyn wirtualnych i wirtualnych dysków twardych między środowiskiem lokalnym i platformą Azure
Ponieważ przekazywanie obrazów maszyn wirtualnych i dysków na platformę Azure nie jest możliwe za pośrednictwem Azure Portal, należy użyć Azure PowerShell poleceń cmdlet lub interfejsu wiersza polecenia. Inną możliwością jest użycie narzędzia "AzCopy". Narzędzie może kopiować wirtualne dyski twarde między środowiskiem lokalnym i platformą Azure (w obu kierunkach). Umożliwia także kopiowanie dysków VHD między regionami platformy Azure. Zapoznaj się z [tą dokumentacją][storage-use-azcopy] , aby pobrać i użyć AzCopy.

Trzecią alternatywą jest użycie różnych narzędzi zorientowanych na graficzny interfejs użytkownika innych firm. Należy jednak upewnić się, że te narzędzia obsługują obiekty blob na stronie platformy Azure. W naszym przypadku musimy używać magazynu Azure Page BLOB (różnice są opisane tutaj: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> ). Narzędzia udostępniane przez platformę Azure są wydajne w przypadku kompresowania maszyn wirtualnych i wirtualnych dysków twardych, które muszą zostać przekazane. Jest to ważne, ponieważ wydajność kompresji skraca czas przekazywania (co może być w zależności od linku przekazywania do Internetu z lokalnego obiektu i regionu wdrożenia platformy Azure). Jest to słuszne założenie, że przekazanie maszyny wirtualnej lub wirtualnego dysku twardego z lokalizacji Europejskiej do centrów danych platformy Azure w Stanach Zjednoczonych trwa dłużej niż przekazywanie tych samych maszyn wirtualnych/VHD do europejskich centrów danych platformy Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Przekazywanie wirtualnego dysku twardego z lokalnego na platformę Azure
Aby przekazać istniejącą maszynę wirtualną lub wirtualny dysk twardy z sieci lokalnej, należy spełnić wymagania opisane w rozdziale [przygotowanie do przeniesienia maszyny wirtualnej z lokalizacji lokalnej na platformę Azure przy użyciu nieuogólnionego dysku][planning-guide-5.2.1] tego dokumentu.

Takie maszyny wirtualne nie muszą być uogólnione i mogą być przekazywane do stanu i kształtu po zamknięciu po stronie lokalnej. Ta sama wartość dotyczy dodatkowych dysków VHD, które nie zawierają żadnego systemu operacyjnego.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Przekazywanie wirtualnego dysku twardego i udostępnianie go dyskowi platformy Azure
W takim przypadku chcemy przekazać wirtualny dysk twardy z systemem lub bez systemu operacyjnego, a następnie zainstalować go na maszynie wirtualnej jako dysk danych lub użyć go jako dysku systemu operacyjnego. Jest to proces wieloetapowy

**Program PowerShell**

* Zaloguj się do subskrypcji za pomocą usługi *Connect-AzAccount*
* Ustaw subskrypcję kontekstu z parametrem *Set-AzContext* i identyfikatorem subskrypcji parametru lub subscriptionname — Zobacz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekaż dysk VHD z *dodatkiem Add-AzVhd* do konta usługi Azure Storage — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Obowiązkowe Utwórz dysk zarządzany na podstawie dysku VHD przy użyciu elementu *New-AzDisk*  -Zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Ustaw dysk systemu operacyjnego nowej konfiguracji maszyny wirtualnej na dysk VHD lub na dysku zarządzanym przy użyciu *opcji Set-AzVMOSDisk* — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Utwórz nową maszynę wirtualną na podstawie konfiguracji maszyny wirtualnej za pomocą elementu *New-AzVM* -Zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Dodawanie dysku danych do nowej maszyny wirtualnej za pomocą elementu *Add-AzVMDataDisk* — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Interfejs wiersza polecenia platformy Azure**

* Zaloguj się do subskrypcji przy użyciu *AZ login*
* Wybierz subskrypcję za pomocą *AZ Account Set-- `<subscription name or id` > Subscription*
* Przekaż dysk VHD za pomocą polecenia *AZ Storage BLOB upload* -zobacz [using the Azure CLI with Azure Storage][storage-azure-cli]
* Obowiązkowe Utwórz dysk zarządzany na podstawie dysku VHD za pomocą *AZ Disk Create* -Zobacz https://docs.microsoft.com/cli/azure/disk
* Utwórz nową maszynę wirtualną z przekazaniem dysku VHD lub dysku zarządzanego jako dysk systemu operacyjnego za pomocą *AZ VM Create* and Parameter *--Attach-OS-Disk*
* Dodawanie dysku danych do nowej maszyny wirtualnej za pomocą *AZ VM Disk Attach* and Parameter *--New*

**Szablon**

* Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure
* Obowiązkowe Utwórz dysk zarządzany na podstawie dysku VHD przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal
* Wdróż maszynę wirtualną z szablonem JSON odwołującym się do dysku VHD, jak pokazano w [tym przykładowym szablonie JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub przy użyciu Managed disks, jak pokazano w [tym przykładowym szablonie JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Wdrażanie obrazu maszyny wirtualnej
Aby przekazać istniejącą maszynę wirtualną lub wirtualny dysk twardy z sieci lokalnej, aby można było użyć jej jako obrazu maszyny wirtualnej platformy Azure, maszyna wirtualna lub wirtualny dysk twardy musi spełnić wymagania wymienione w rozdziale [przygotowanie do wdrożenia maszyny wirtualnej z obrazem specyficznym dla klienta dla SAP][planning-guide-5.2.2] tego dokumentu.

* Używanie *narzędzia Sysprep* w systemie Windows lub *waagent —* anulowanie aprowizacji w systemie Linux w celu uogólnienia maszyny wirtualnej — zobacz [Informacje techniczne](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) dotyczące programu Sysprep dla systemu Windows lub [jak przechwycić maszynę wirtualną z systemem Linux do użycia jako szablon Menedżer zasobów][capture-image-linux-step-2-create-vm-image] w systemie Linux
* Zaloguj się do subskrypcji za pomocą usługi *Connect-AzAccount*
* Ustaw subskrypcję kontekstu z parametrem *Set-AzContext* i identyfikatorem subskrypcji parametru lub subscriptionname — Zobacz <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Przekaż dysk VHD z *dodatkiem Add-AzVhd* do konta usługi Azure Storage — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Obowiązkowe Tworzenie obrazu dysku zarządzanego na podstawie dysku VHD przy użyciu elementu *New-AzImage*  — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Ustaw dysk systemu operacyjnego nowej konfiguracji maszyny wirtualnej na
  * Wirtualny dysk twardy z poleceniem *Set-AzVMOSDisk-SourceImageUri-fromImage* -Zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Zestaw obrazu dysku zarządzanego *— AzVMSourceImage* — Zobacz <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Utwórz nową maszynę wirtualną na podstawie konfiguracji maszyny wirtualnej za pomocą elementu *New-AzVM* -Zobacz <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Interfejs wiersza polecenia platformy Azure**

* Używanie *narzędzia Sysprep* w systemie Windows lub *waagent —* anulowanie aprowizacji w systemie Linux w celu uogólnienia maszyny wirtualnej — zobacz [Informacje techniczne](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) dotyczące programu Sysprep dla systemu Windows lub [jak przechwycić maszynę wirtualną z systemem Linux do użycia jako szablon Menedżer zasobów][capture-image-linux-step-2-create-vm-image] w systemie Linux
* Zaloguj się do subskrypcji przy użyciu *AZ login*
* Wybierz subskrypcję za pomocą *AZ Account Set-- `<subscription name or id` > Subscription*
* Przekaż dysk VHD za pomocą polecenia *AZ Storage BLOB upload* -zobacz [using the Azure CLI with Azure Storage][storage-azure-cli]
* Obowiązkowe Tworzenie obrazu dysku zarządzanego na podstawie dysku VHD za pomocą *AZ Image Create* -Zobacz https://docs.microsoft.com/cli/azure/image
* Utwórz nową maszynę wirtualną określającą przekazany plik VHD lub dysk zarządzany jako dysk systemu operacyjnego za pomocą *AZ VM Create* and Parameter *--Image*

**Szablon**

* Używanie *narzędzia Sysprep* w systemie Windows lub *waagent —* anulowanie aprowizacji w systemie Linux w celu uogólnienia maszyny wirtualnej — zobacz [Informacje techniczne](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) dotyczące programu Sysprep dla systemu Windows lub [jak przechwycić maszynę wirtualną z systemem Linux do użycia jako szablon Menedżer zasobów][capture-image-linux-step-2-create-vm-image] w systemie Linux
* Przekazywanie wirtualnego dysku twardego przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure
* Obowiązkowe Tworzenie obrazu dysku zarządzanego na podstawie dysku VHD przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal
* Wdróż maszynę wirtualną z szablonem JSON odwołującym się do dysku VHD obrazu, jak pokazano w [tym przykładowym szablonie JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) lub przy użyciu obrazu dysku zarządzanego, jak pokazano w [tym przykładowym szablonie JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Pobieranie dysków VHD lub Managed Disks do lokalnego
Infrastruktura platformy Azure jako usługa nie jest jednokierunkową ulicą umożliwiającą przekazywanie wirtualnych dysków twardych i systemów SAP. Systemy SAP można przenieść z platformy Azure z powrotem do lokalnego świata.

Podczas pobierania dyski VHD lub Managed Disks nie mogą być aktywne. Nawet w przypadku pobierania dysków, które są zainstalowane na maszynach wirtualnych, maszyna wirtualna musi zostać wyłączona i cofnięta alokacja. Jeśli chcesz tylko pobrać zawartość bazy danych, w tym celu należy użyć do skonfigurowania nowego systemu lokalnego i jeśli jest akceptowalny, aby w czasie pobierania i instalacji nowego systemu na platformie Azure nadal działały, można uniknąć długiego przestoju przez wykonanie skompresowanej kopii zapasowej bazy danych na dysku, a dopiero potem pobranie systemu operacyjnego.

#### <a name="powershell"></a>PowerShell

* Pobieranie dysku zarządzanego musisz najpierw uzyskać dostęp do bazowego obiektu BLOB dysku zarządzanego. Następnie możesz skopiować źródłowy obiekt BLOB do nowego konta magazynu i pobrać obiekt BLOB z tego konta magazynu.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Pobieranie wirtualnego dysku twardego po zatrzymaniu systemu SAP i zamknięciu maszyny wirtualnej można użyć polecenia cmdlet programu PowerShell `Save-AzVhd` w lokalnym miejscu docelowym, aby pobrać dyski VHD z powrotem do lokalnego świata. Aby to zrobić, potrzebny jest adres URL wirtualnego dysku twardego, który można znaleźć w sekcji "magazyn" Azure Portal (należy przejść do konta magazynu i kontenera magazynu, w którym został utworzony wirtualny dysk twardy), i należy wiedzieć, gdzie ma być kopiowany dysk VHD.

  Następnie można użyć polecenia przez zdefiniowanie parametru SourceUri jako adresu URL pliku VHD do pobrania i LocalFilePath jako lokalizacji fizycznej wirtualnego dysku twardego (łącznie z jego nazwą). Polecenie może wyglądać następująco:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Aby uzyskać więcej informacji na temat polecenia cmdlet Save-AzVhd, zaznacz tutaj <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd> .

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Pobieranie dysku zarządzanego musisz najpierw uzyskać dostęp do bazowego obiektu BLOB dysku zarządzanego. Następnie możesz skopiować źródłowy obiekt BLOB do nowego konta magazynu i pobrać obiekt BLOB z tego konta magazynu.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Pobieranie wirtualnego dysku twardego po zatrzymaniu systemu SAP i zamknięciu maszyny wirtualnej można użyć interfejsu wiersza polecenia platformy Azure `_azure storage blob download_` w lokalnym miejscu docelowym, aby pobrać dyski VHD z powrotem do lokalnego świata. Aby to zrobić, potrzebna jest nazwa i kontener wirtualnego dysku twardego, który można znaleźć w sekcji "magazyn" Azure Portal (należy przejść do konta magazynu i kontenera magazynu, w którym został utworzony wirtualny dysk twardy) i należy wiedzieć, gdzie ma być kopiowany wirtualny dysk twardy.

  Następnie można użyć polecenia przez zdefiniowanie obiektu BLOB parametrów i kontenera wirtualnego dysku twardego do pobrania, a miejsce docelowe jako fizyczna lokalizacja docelowa wirtualnego dysku twardego (łącznie z jego nazwą). Polecenie może wyglądać następująco:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferowanie maszyn wirtualnych i dysków na platformie Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopiowanie systemów SAP na platformie Azure

System SAP lub nawet dedykowany serwer DBMS obsługujący warstwę aplikacji SAP będzie prawdopodobnie zawierać kilka dysków, które zawierają system operacyjny z plikami binarnymi lub plikami danych i dziennika bazy danych SAP. Nie ma możliwości kopiowania dysków ani funkcjonalności platformy Azure służącej do zapisywania dysków na dysku lokalnym, który ma mechanizm synchronizacji, który tworzy migawkę wielu dysków w spójny sposób. W związku z tym stan skopiowanego lub zapisanego dysku, nawet jeśli są zainstalowane na tej samej maszynie wirtualnej, będzie różny. Oznacza to, że w konkretnym przypadku mającym różne dane i pliki dziennika zawarte w różnych dyskach baza danych w końcu byłaby niespójna.

**Wniosek: aby można było kopiować lub zapisywać dyski, które są częścią konfiguracji systemu SAP, należy zatrzymać system SAP, a także zamknąć wdrożoną maszynę wirtualną. Następnie można skopiować lub pobrać zestaw dysków, aby utworzyć kopię systemu SAP na platformie Azure lub lokalnie.**

Dyski danych mogą być przechowywane jako pliki VHD na koncie usługi Azure Storage i mogą być bezpośrednio dołączone do maszyny wirtualnej lub używane jako obraz. W takim przypadku wirtualny dysk twardy jest kopiowany do innej lokalizacji przed dołączeniem do maszyny wirtualnej. Pełna nazwa pliku VHD na platformie Azure musi być unikatowa w ramach platformy Azure. Jak wspomniano wcześniej, nazwa jest rodzajem nazwy z trzema częścią, która wygląda następująco:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Dyski danych mogą być również Managed Disks. W takim przypadku dysk zarządzany jest używany do utworzenia nowego dysku zarządzanego przed dołączeniem do maszyny wirtualnej. Nazwa dysku zarządzanego musi być unikatowa w obrębie grupy zasobów.

##### <a name="powershell"></a>PowerShell

Za pomocą poleceń cmdlet Azure PowerShell można skopiować dysk VHD, jak pokazano w [tym artykule][storage-powershell-guide-full-copy-vhd]. Aby utworzyć nowy dysk zarządzany, użyj polecenie New-AzDiskConfig i New-AzDisk, jak pokazano w poniższym przykładzie.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz użyć interfejsu wiersza polecenia platformy Azure, aby skopiować dysk VHD. Aby utworzyć nowy dysk zarządzany, użyj polecenie *AZ Disk Create* , jak pokazano w poniższym przykładzie.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Narzędzia usługi Azure Storage

* <https://storageexplorer.com/>

Profesjonalne wersje programu Eksplorator usługi Azure Storage można znaleźć tutaj:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Kopia wirtualnego dysku twardego w ramach konta magazynu to proces, który zajmuje zaledwie kilka sekund (podobnie jak w przypadku sprzętu SAN tworzenie migawek z opóźnionym kopiowaniem i kopiowaniem podczas zapisu). Po utworzeniu kopii pliku VHD można dołączyć ją do maszyny wirtualnej lub użyć jej jako obrazu, aby dołączyć kopie wirtualnego dysku twardego do maszyn wirtualnych.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopiowanie dysków między kontami usługi Azure Storage
Nie można wykonać tego zadania na Azure Portal. Możesz użyć poleceń cmdlet Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przeglądarki magazynu innej firmy. Polecenia cmdlet programu PowerShell lub poleceń interfejsu CLI mogą tworzyć obiekty blob i zarządzać nimi, co obejmuje możliwość asynchronicznego kopiowania obiektów BLOB na kontach magazynu i między regionami w ramach subskrypcji platformy Azure.

##### <a name="powershell"></a>PowerShell
Istnieje również możliwość kopiowania dysków VHD między subskrypcjami. Aby uzyskać więcej informacji, Przeczytaj [ten artykuł][storage-powershell-guide-full-copy-vhd].

Podstawowy przepływ logiki poleceń cmdlet środowiska PS wygląda następująco:

* Utwórz kontekst konta magazynu dla konta magazynu **źródłowego** przy użyciu funkcji *New-AzStorageContext* — Zobacz <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Utwórz kontekst konta magazynu dla **docelowego** konta magazynu przy użyciu funkcji *New-AzStorageContext* — Zobacz <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Rozpocznij kopiowanie za pomocą

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Sprawdź stan kopii w pętli z

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej zgodnie z powyższym opisem.

Aby zapoznać się z przykładami, zobacz [ten artykuł][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
* Rozpocznij kopiowanie za pomocą

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Sprawdź stan, jeśli kopia jest nadal w pętli z

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej zgodnie z powyższym opisem.

### <a name="disk-handling"></a>Obsługa dysków

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Struktura maszyn wirtualnych/dysków dla wdrożeń SAP

Najlepiej, aby obsługa struktury maszyny wirtualnej i skojarzonych dysków była prosta. W przypadku instalacji lokalnych klienci opracował wiele sposobów tworzenia struktury instalacji serwera.

* Jeden dysk podstawowy, który zawiera system operacyjny i wszystkie pliki binarne systemu DBMS i/lub SAP. Od marca 2015 ten dysk może mieć rozmiar do 1 TB zamiast wcześniejszych ograniczeń, które ograniczają go do 127 GB.
* Jeden lub wiele dysków, które zawierają plik dziennika systemu DBMS bazy danych SAP i plik dziennika obszaru magazynu tymczasowego systemu DBMS (Jeśli system DBMS obsługuje tę funkcję). Jeśli wymagania IOPS dziennika bazy danych są wysokie, należy rozdzielić wiele dysków w celu uzyskania dostępu do wymaganego woluminu IOPS.
* Liczba dysków zawierających jeden lub dwa pliki bazy danych programu SAP i plików danych tymczasowych systemu DBMS (Jeśli system DBMS obsługuje tę funkcję).

![Konfiguracja referencyjna maszyny wirtualnej usługi Azure IaaS dla oprogramowania SAP][planning-guide-figure-1300]


---
> ![Logo Windows.][Logo_Windows] Windows
>
> W przypadku wielu klientów znaleźliśmy konfiguracje, w których na przykład pliki binarne SAP i DBMS nie zostały zainstalowane na c:\ dysk, na którym zainstalowano system operacyjny. Istnieją różne przyczyny tego problemu, ale po powrocie do katalogu głównego zwykle było to, że dyski były małe i uaktualnienia systemu operacyjnego wymagały dodatkowego miejsca 10-15 lat temu. Oba warunki nie są stosowane do tych dni zbyt często. Dzisiaj c:\ dysk można zamapować na duże ilości dysków lub maszyn wirtualnych. Aby zachować proste wdrożenia w strukturze, zaleca się przestrzeganie następującego wzorca wdrażania dla systemów SAP NetWeaver na platformie Azure
>
> Plik stronicowania systemu operacyjnego Windows powinien znajdować się na dysku D: (dysk nietrwały)
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Umieść swapfile systemu Linux w obszarze/mnt/mnt/Resource w systemie Linux zgodnie z opisem w [tym artykule][virtual-machines-linux-agent-user-guide]. Plik wymiany można skonfigurować w pliku konfiguracji agenta systemu Linux/etc/waagent.conf. Dodaj lub Zmień następujące ustawienia:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Aby aktywować zmiany, należy ponownie uruchomić agenta systemu Linux za pomocą

```console
sudo service waagent restart
```

Aby uzyskać więcej informacji o zalecanym rozmiarze pliku wymiany, przeczytaj temat SAP Note [1597355]

---
Liczba dysków używanych dla plików danych systemu DBMS oraz typ magazynu platformy Azure, na którym są hostowane dyski, powinny być określone przez wymagania IOPS i wymagane opóźnienie. Dokładne limity przydziału zostały opisane w [tym artykule (Linux)][virtual-machines-sizes-linux] i w [tym artykule (Windows)][virtual-machines-sizes-windows].

Doświadczenie wdrożeń SAP w ciągu ostatnich dwóch lat — kilka lekcji, które mogą być podsumowywane jako:

* Ruch IOPS do różnych plików danych nie zawsze jest taki sam, ponieważ istniejące systemy klienta mogą mieć pliki danych o różnej wielkości reprezentujące ich bazy danych SAP. W związku z tym, aby lepiej korzystać z konfiguracji RAID na wielu dyskach w celu umieszczenia plików danych LUN używać miejsca z nich. Istniały sytuacje, szczególnie w przypadku usługi Azure Standard Storage, w przypadku których szybkość operacji we/wy osiągnęła przydział pojedynczego dysku do dziennika transakcji systemu DBMS. W takich scenariuszach użycie Premium Storage jest zalecane lub alternatywnie agregowanie wielu standardowych dysków magazynu za pomocą paska oprogramowania.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> * [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> * [Konfigurowanie RAID oprogramowania w systemie Linux][virtual-machines-linux-configure-raid]
> * [Konfigurowanie LVM na maszynie wirtualnej z systemem Linux na platformie Azure][virtual-machines-linux-configure-lvm]
>
>

---
* Premium Storage przedstawia znaczącą lepszą wydajność, szczególnie w przypadku krytycznych zapisów w dzienniku transakcji. W przypadku scenariuszy SAP, które oczekują na dostarczenie wydajności, takich jak wydajność, zdecydowanie zaleca się korzystanie z serii maszyn wirtualnych, które mogą korzystać z usługi Azure Premium Storage.

Należy pamiętać, że dysk, który zawiera system operacyjny, i zgodnie z zaleceniami, pliki binarne SAP i bazy danych (podstawowa maszyna wirtualna) nie są już ograniczone do 127 GB. Teraz może mieć rozmiar do 1 TB. Powinno to być wystarczające miejsce, aby zachować wszystkie niezbędne pliki, w tym na przykład dzienniki zadań wsadowych SAP.

Aby uzyskać więcej sugestii i więcej szczegółów, w odniesieniu do maszyn wirtualnych DBMS, zapoznaj się z [przewodnikiem wdrażania systemu DBMS][dbms-guide]

#### <a name="disk-handling"></a>Obsługa dysków

W większości scenariuszy należy utworzyć dodatkowe dyski w celu wdrożenia bazy danych SAP na maszynie wirtualnej. Zapoznaj się z zagadnieniami dotyczącymi liczby dysków w rozdziale [VM/Disk Structure for SAP Deployments][planning-guide-5.5.1] tego dokumentu. Azure Portal umożliwia dołączanie i odłączanie dysków po wdrożeniu podstawowej maszyny wirtualnej. Dyski można dołączać/odłączać, gdy maszyna wirtualna jest uruchomiona, a także gdy zostanie zatrzymana. Podczas dołączania dysku Azure Portal oferuje możliwość dołączenia pustego dysku lub istniejącego dysku, co w tym momencie nie jest dołączone do innej maszyny wirtualnej.

**Uwaga**: dyski można dołączać tylko do jednej maszyny wirtualnej w danym momencie.

![Dołączanie/Odłączanie dysków za pomocą usługi Azure Standard Storage][planning-guide-figure-1400]

Podczas wdrażania nowej maszyny wirtualnej możesz zdecydować, czy chcesz użyć Managed Disks, czy umieścić dyski na kontach usługi Azure Storage. Jeśli chcesz używać Premium Storage, zalecamy korzystanie z Managed Disks.

Następnie należy zdecydować, czy chcesz utworzyć nowy i pusty dysk, czy chcesz wybrać istniejący dysk, który został przekazany wcześniej, i powinien być teraz dołączony do maszyny wirtualnej.

**Ważne**: **nie** chcesz używać buforowania hosta z magazynem w warstwie Standardowa platformy Azure. Preferencja pamięci podręcznej hosta powinna pozostać domyślnie Niewybrana. Przy użyciu usługi Azure Premium Storage należy włączyć buforowanie odczytu, jeśli Charakterystyka we/wy jest głównie odczytywana, podobnie jak typowy ruch we/wy do plików danych bazy danych. W przypadku pliku dziennika transakcji bazy danych nie zaleca się buforowania.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> [Jak dołączyć dysk danych w Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Jeśli dyski są dołączone, należy zalogować się do maszyny wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli funkcja autoinstalacji nie jest włączona zgodnie z zaleceniami w rozdziale [Ustawianie opcji instalacji automatycznej dla dołączonych dysków][planning-guide-5.5.3], nowo dołączony wolumin musi zostać przełączony w tryb online i zainicjowany.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Jeśli dyski są dołączone, należy zalogować się do maszyny wirtualnej i zainicjować dyski zgodnie z opisem w [tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] .
>
>

---
Jeśli nowy dysk jest pustym dyskiem, należy również sformatować dysk. Do formatowania, szczególnie w przypadku danych i plików dziennika systemu DBMS, mają zastosowanie te same zalecenia jak w przypadku wdrożeń od zera w systemie DBMS.

Konto usługi Azure Storage nie zapewnia nieskończonych zasobów w zakresie woluminów we/wy, operacji IOPS i ilości danych. Zazwyczaj ma to wpływ na maszyny wirtualne DBMS. Najlepszym rozwiązaniem może być użycie oddzielnego konta magazynu dla każdej maszyny wirtualnej, jeśli masz kilka maszyn wirtualnych z dużą ilością operacji we/wy do wdrożenia w celu pozostawania w ramach limitu woluminu konta usługi Azure Storage. W przeciwnym razie musisz zobaczyć, jak można zrównoważyć te maszyny wirtualne między różnymi kontami magazynu bez naciskania na limity poszczególnych pojedynczych kont magazynu. Więcej szczegółów znajduje się w [przewodniku wdrażania systemu DBMS][dbms-guide]. Należy również pamiętać o tych ograniczeniach dla czystych maszyn wirtualnych serwera aplikacji SAP lub innych maszyn wirtualnych, które ostatecznie mogą wymagać dodatkowych dysków VHD. Te ograniczenia nie mają zastosowania w przypadku korzystania z dysku zarządzanego. Jeśli planujesz używać Premium Storage, zalecamy użycie dysku zarządzanego.

Innym tematem, który jest istotny dla kont magazynu, jest to, czy wirtualne dyski twarde na koncie magazynu mają mieć replikację geograficzną. Replikacja geograficzna jest włączona lub wyłączona na poziomie konta magazynu, a nie na poziomie maszyny wirtualnej. W przypadku włączenia replikacji geograficznej wirtualne dyski twarde w ramach konta magazynu byłyby replikowane do innego centrum danych platformy Azure w tym samym regionie. Przed podjęciem decyzji o tym należy wziąć pod uwagę następujące ograniczenia:

Replikacja geograficzna platformy Azure działa lokalnie na każdym wirtualnym dysku twardym w maszynie wirtualnej i nie replikuje we/wy w kolejności chronologicznej na wielu dyskach VHD w maszynie wirtualnej. W związku z tym wirtualny dysk twardy reprezentujący podstawową maszynę wirtualną, a także dodatkowe dyski VHD dołączone do maszyny wirtualnej są replikowane niezależnie od siebie. Oznacza to, że nie ma synchronizacji między zmianami w różnych wirtualnych dyskach twardych. Fakt, że we/wy są replikowane niezależnie od kolejności, w której są zapisywane, oznacza, że replikacja geograficzna nie jest wartością dla serwerów baz danych, które są rozproszone dla wielu wirtualnych dysków twardych. Oprócz systemu DBMS mogą istnieć również inne aplikacje, w których procesy zapisujące i manipulowania danymi w różnych wirtualnych dyskach twardych są ważne, aby zachować kolejność zmian. Jeśli jest to wymagane, replikację geograficzną na platformie Azure nie należy włączać. Zależnie od tego, czy potrzebna jest replikacja geograficzna dla zestawu maszyn wirtualnych, ale nie dla innego zestawu, można już klasyfikować maszyny wirtualne i powiązane z nimi dyski VHD do różnych kont magazynu, które mają włączone lub wyłączone replikację geograficzną.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Ustawianie instalacji automatycznej dla dołączonych dysków
---
> ![Logo Windows.][Logo_Windows] Windows
>
> W przypadku maszyn wirtualnych, które są tworzone na podstawie własnych obrazów lub dysków, należy sprawdzić i prawdopodobnie ustawić parametr automatycznej instalacji. Ustawienie tego parametru zezwoli na maszynę wirtualną po ponownym uruchomieniu lub ponownemu wdrożeniu na platformie Azure w celu automatycznego zainstalowania podłączonych/zainstalowanych dysków.
> Parametr jest ustawiany dla obrazów dostarczonych przez firmę Microsoft w portalu Azure Marketplace.
>
> Aby ustawić autoinstalację, zapoznaj się z dokumentacją pliku wykonywalnego wiersza polecenia diskpart.exe tutaj:
>
> * [Opcje wiersza polecenia narzędzia DiskPart](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Automatycznej instalacji](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> Okno wiersza polecenia systemu Windows powinno być otwarte jako administrator.
>
> Jeśli dyski są dołączone, należy zalogować się do maszyny wirtualnej, aby otworzyć Menedżera dysków systemu Windows. Jeśli funkcja autoinstalacji nie jest włączona zgodnie z zaleceniami w obszarze [Ustawienia rozdziału automount dla dołączonych dysków][planning-guide-5.5.3], nowo przyłączony wolumin >musi zostać przełączony w tryb online i zainicjowany.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Musisz zainicjować nowo dołączony pusty dysk, zgodnie z opisem w [tym artykule][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Należy również dodać nowe dyski do katalogu/etc/fstab.
>
>

---
### <a name="final-deployment"></a>Ostateczne wdrożenie

Aby zapoznać się z ostatecznym wdrożeniem i dokładnymi krokami, szczególnie w odniesieniu do wdrożenia rozszerzenia platformy Azure dla oprogramowania SAP, zapoznaj się z [przewodnikiem wdrażania][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Uzyskiwanie dostępu do systemów SAP działających na maszynach wirtualnych platformy Azure

W przypadku scenariuszy, w których chcesz połączyć się z tymi systemami SAP za pośrednictwem publicznej sieci Internet przy użyciu graficznego interfejsu użytkownika SAP, należy zastosować następujące procedury.

W dalszej części dokumentu będziemy omawiać inne główne scenariusze, łącząc się z systemami SAP w wdrożeniach obejmujących wiele lokalizacji, które mają połączenie typu lokacja-lokacja (tunel VPN) lub połączenie platformy Azure ExpressRoute między systemami lokalnymi i systemami platformy Azure.

### <a name="remote-access-to-sap-systems"></a>Zdalny dostęp do systemów SAP

W przypadku Azure Resource Manager nie ma już domyślnych punktów końcowych, jak w poprzednim modelu klasycznym. Wszystkie porty maszyny wirtualnej Azure Resource Manager są otwarte tak długo, jak:

1. Nie zdefiniowano sieciowej grupy zabezpieczeń dla podsieci lub interfejsu sieciowego. Ruch sieciowy do maszyn wirtualnych platformy Azure można zabezpieczyć za pomocą tak zwanego "sieciowych grup zabezpieczeń". Aby uzyskać więcej informacji, zobacz [What is a Network Security Group (NSG)?][virtual-networks-nsg] (Co to jest sieciowa grupa zabezpieczeń?).
2. Nie zdefiniowano Azure Load Balancer dla interfejsu sieciowego

Zapoznaj się z różnicą architektury klasycznego modelu i ARM zgodnie z opisem w [tym artykule][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguracja systemu SAP i łączności z graficznym interfejsem użytkownika SAP przez Internet

Zobacz ten artykuł, który opisuje szczegóły dotyczące tego tematu:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Zmienianie ustawień zapory w ramach maszyny wirtualnej

Może być konieczne skonfigurowanie zapory na maszynach wirtualnych w taki sposób, aby zezwalała na ruch przychodzący do systemu SAP.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Domyślnie Zapora systemu Windows w ramach wdrożonej maszyny wirtualnej platformy Azure jest włączona. Teraz musisz zezwolić na otwarcie portu SAP, w przeciwnym razie interfejs GUI SAP nie będzie mógł nawiązać połączenia.
> W tym celu:
>
> * Otwórz aplet kontrola Panel\System i Zapora Security\Windows w **ustawieniach zaawansowanych**.
> * Teraz kliknij prawym przyciskiem myszy reguły ruchu przychodzącego i wybierz pozycję **Nowa reguła**.
> * W poniższym kreatorze wybrano opcję utworzenia nowej reguły **portu** .
> * W następnym kroku kreatora pozostaw ustawienie przy użyciu protokołu TCP i wpisz numer portu, który chcesz otworzyć. Ponieważ nasz identyfikator wystąpienia SAP to 00, wprowadziliśmy 3200. Jeśli wystąpienie ma inny numer wystąpienia, należy otworzyć port zdefiniowany wcześniej na podstawie numeru wystąpienia.
> * W następnej części kreatora należy pozostawić zaznaczenie pozycji **Zezwalaj na połączenie** .
> * W następnym kroku kreatora należy określić, czy reguła ma zastosowanie do sieci, prywatnej i publicznej. Dostosuj je w razie potrzeby do Twoich potrzeb. Jednak połączenie z graficznym interfejsem użytkownika SAP z zewnątrz za pośrednictwem sieci publicznej wymaga zastosowania reguły do sieci publicznej.
> * W ostatnim kroku kreatora Nazwij regułę i Zapisz ją, naciskając przycisk **Zakończ**.
>
> Reguła zaczyna obowiązywać natychmiast.
>
> ![Definicja reguły portu][planning-guide-figure-1600]
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Obrazy systemu Linux w portalu Azure Marketplace nie domyślnie włączają zapory dołączenie iptables, a połączenie z systemem SAP powinno funkcjonować. Jeśli włączono dołączenie iptables lub inną zaporę, zapoznaj się z dokumentacją dołączenie iptables lub używaną zaporę, aby zezwolić na ruch przychodzący TCP do portu 32xx (gdzie XX jest numerem systemowym systemu SAP).
>
>

---
#### <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Graficzny interfejs użytkownika SAP nie łączy się bezpośrednio z żadnym wystąpieniem SAP (32xx portu), które jest uruchomione, ale najpierw nawiązuje połączenie za pośrednictwem portu otwartego w procesie serwera komunikatów SAP (port 36XX). W przeszłości ten sam port był używany przez serwer komunikatów do komunikacji wewnętrznej z wystąpieniami aplikacji. Aby zapobiec przypadkowemu komunikacji serwerów aplikacji lokalnych z serwerem komunikatów na platformie Azure, wewnętrzne porty komunikacyjne można zmienić. Zdecydowanie zaleca się zmianę wewnętrznej komunikacji między serwerem komunikatów SAP a jego wystąpieniami aplikacji na inny numer portu w systemach, które zostały sklonowane z systemów lokalnych, takich jak klonowanie rozwoju w celu testowania projektu itp. Można to zrobić przy użyciu domyślnego parametru profilu:

> rdisp/msserv_internal
>
>

zgodnie z opisem w [ustawieniach zabezpieczeń dla serwera komunikatów SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Pojedyncza maszyna wirtualna z programem SAP NetWeaver demonstracyjną/szkoleniami

![Uruchamianie pojedynczych systemów demonstracyjnych SAP maszyn wirtualnych z tymi samymi nazwami maszyn wirtualnych, izolowanych na platformie Azure Cloud Services][planning-guide-figure-1700]

W tym scenariuszu wdrażamy typowy scenariusz szkolenia/systemu demonstracyjnego, w którym kompletny scenariusz szkolenia/demonstracji jest zawarty w jednej maszynie wirtualnej. Przyjęto założenie, że wdrożenie odbywa się za pomocą szablonów obrazów maszyn wirtualnych. Przyjęto również, że wiele maszyn wirtualnych demonstracyjnych/szkoleniowych należy wdrożyć z maszynami wirtualnymi o tej samej nazwie. Całe systemy szkoleniowe nie mają łączności z zasobami lokalnymi i są przeciwieństwem do wdrożenia hybrydowego.

Przyjęto założenie, że utworzono obraz maszyny wirtualnej zgodnie z opisem w sekcji rozdział [Przygotowywanie maszyn wirtualnych za pomocą oprogramowania SAP for Azure][planning-guide-5.2] w tym dokumencie.

Sekwencja zdarzeń do wdrożenia scenariusza wygląda następująco:

##### <a name="powershell"></a>PowerShell

* Utwórz nową grupę zasobów dla każdego szkolenia/demonstracji poziomej

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Utwórz nowe konto magazynu, jeśli nie chcesz używać Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Utwórz nową sieć wirtualną dla każdego szkolenia/poziom pokazu, aby umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która zezwala tylko na ruch przychodzący do portu 3389, aby umożliwić dostęp Pulpit zdalny i port 22 dla protokołu SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Utwórz nowy publiczny adres IP, za pomocą którego można uzyskać dostęp do maszyny wirtualnej z Internetu

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Utwórz nowy interfejs sieciowy dla maszyny wirtualnej

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie miała taką samą nazwę. Identyfikator SID SAP wystąpień SAP NetWeaver w tych maszynach wirtualnych będzie taki sam, jak również. Nazwa maszyny wirtualnej w grupie zasobów platformy Azure musi być unikatowa, ale w różnych grupach zasobów platformy Azure można uruchamiać maszyny wirtualne o tej samej nazwie. Domyślne konto "Administrator" w systemie Windows lub "root" dla systemu Linux jest nieprawidłowe. W związku z tym nowa nazwa użytkownika administratora musi być zdefiniowana wraz z hasłem. Należy również zdefiniować rozmiar maszyny wirtualnej.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcjonalnie dodaj dodatkowe dyski i Przywróć niezbędną zawartość. Wszystkie nazwy obiektów BLOB (adresy URL do obiektów BLOB) muszą być unikatowe w ramach platformy Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>Interfejs wiersza polecenia

Poniższy przykładowy kod może być używany w systemie Linux. W przypadku systemu Windows należy użyć programu PowerShell zgodnie z powyższym opisem lub dostosować przykład do użycia% rgName% zamiast $rgName i ustawić zmienną środowiskową przy użyciu *zestawu*poleceń systemu Windows.

* Utwórz nową grupę zasobów dla każdego szkolenia/demonstracji poziomej

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Tworzenie nowego konta magazynu

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Utwórz nową sieć wirtualną dla każdego szkolenia/poziom pokazu, aby umożliwić użycie tej samej nazwy hosta i adresów IP. Sieć wirtualna jest chroniona przez sieciową grupę zabezpieczeń, która zezwala tylko na ruch przychodzący do portu 3389, aby umożliwić dostęp Pulpit zdalny i port 22 dla protokołu SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Utwórz nowy publiczny adres IP, za pomocą którego można uzyskać dostęp do maszyny wirtualnej z Internetu

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Utwórz nowy interfejs sieciowy dla maszyny wirtualnej

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Tworzy maszynę wirtualną. W tym scenariuszu każda maszyna wirtualna będzie miała taką samą nazwę. Identyfikator SID SAP wystąpień SAP NetWeaver w tych maszynach wirtualnych będzie taki sam, jak również. Nazwa maszyny wirtualnej w grupie zasobów platformy Azure musi być unikatowa, ale w różnych grupach zasobów platformy Azure można uruchamiać maszyny wirtualne o tej samej nazwie. Domyślne konto "Administrator" w systemie Windows lub "root" dla systemu Linux jest nieprawidłowe. W związku z tym nowa nazwa użytkownika administratora musi być zdefiniowana wraz z hasłem. Należy również zdefiniować rozmiar maszyny wirtualnej.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcjonalnie dodaj dodatkowe dyski i Przywróć niezbędną zawartość. Wszystkie nazwy obiektów BLOB (adresy URL do obiektów BLOB) muszą być unikatowe w ramach platformy Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Szablon

Możesz użyć przykładowych szablonów w repozytorium Azure-Start-Templates w witrynie GitHub.

* [Prosta maszyna wirtualna z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Prosta maszyna wirtualna z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Maszyna wirtualna z obrazu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementowanie zestawu maszyn wirtualnych komunikujących się na platformie Azure

Ten scenariusz niehybrydowy jest typowym scenariuszem dla celów szkoleniowych i demonstracyjnych, gdzie oprogramowanie reprezentujące scenariusz demonstracyjny/szkoleniowe jest rozłożone na wiele maszyn wirtualnych. Różne składniki zainstalowane w różnych maszynach wirtualnych muszą komunikować się ze sobą. W tym scenariuszu nie jest wymagana żadna lokalna komunikacja sieciowa ani scenariusz między lokalizacjami.

Ten scenariusz stanowi rozszerzenie instalacji opisanej w rozdziale [Pojedyncza maszyna wirtualna z programem SAP NetWeaver demonstracyjną/uczeniem][planning-guide-7.1] tego dokumentu. W takim przypadku do istniejącej grupy zasobów zostaną dodane więcej maszyn wirtualnych. W poniższym przykładzie poziom szkolenia obejmuje maszynę wirtualną SAP ASCS/SCS, maszynę wirtualną z systemem DBMS oraz maszynę wirtualną wystąpienia serwera aplikacji SAP.

Przed rozpoczęciem tworzenia tego scenariusza należy wziąć pod uwagę podstawowe ustawienia, które zostały już wykonane w scenariuszu przed.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nazwa grupy zasobów i maszyny wirtualnej

Wszystkie nazwy grup zasobów muszą być unikatowe. Opracowuj własny schemat nazewnictwa zasobów, takich jak `<rg-name` sufiks>.

Nazwa maszyny wirtualnej musi być unikatowa w obrębie grupy zasobów.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Konfigurowanie sieci do komunikacji między różnymi maszynami wirtualnymi

![Zestaw maszyn wirtualnych w ramach Virtual Network platformy Azure][planning-guide-figure-1900]

Aby zapobiec konfliktom nazw z klonami tego samego Landscapes szkolenia/demonstracji, musisz utworzyć Virtual Network platformy Azure dla każdego poziomego. Rozpoznawanie nazw DNS będzie udostępniane przez platformę Azure lub można skonfigurować własny serwer DNS poza platformą Azure (nie można dokładniej omawiać tego w tym miejscu). W tym scenariuszu nie konfigurujemy własnego systemu DNS. W przypadku wszystkich maszyn wirtualnych w ramach jednej Virtual Network platformy Azure zostanie włączona komunikacja za pośrednictwem nazw hostów.

Przyczyny oddzielenia szkolenia lub pokazu Landscapes przez sieci wirtualne i nie tylko grup zasobów mogą być następujące:

* System SAP w trybie poziomym musi być własnym AD/OpenLDAP, a serwer domeny musi być częścią każdego landscapesu.
* System SAP w trybie ustawowy ma składniki, które muszą współpracować ze stałymi adresami IP.

Więcej informacji o sieciach wirtualnych platformy Azure i sposobach ich definiowania można znaleźć w [tym artykule][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Wdrażanie maszyn wirtualnych SAP z łącznością sieci firmowej (między różnymi lokalizacjami)

Uruchamiasz środowisko SAP i chcesz podzielić wdrożenie między systemami bez systemu operacyjnego w przypadku serwerów wysokiej klasy DBMS, lokalnych środowisk zwirtualizowanych dla warstw aplikacji i mniejszych 2-warstwowych systemów SAP i Azure IaaS. Podstawowa założenie polega na tym, że systemy SAP w obrębie jednego punktu SAP muszą komunikować się ze sobą i wieloma innymi składnikami oprogramowania wdrożonymi w firmie niezależnie od ich formularza wdrożenia. Nie powinno się także wprowadzać żadnych różnic w formularzu wdrożenia dla użytkownika końcowego łączącego się z interfejsem GUI SAP lub innymi interfejsami. Te warunki mogą być spełnione tylko w przypadku, gdy w systemie platformy Azure są rozszerzone usługi Active Directory/OpenLDAP i DNS, w ramach połączeń między lokacjami/z połączeniami z obsługą połączeń z usługą Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenariusz krajobrazu SAP

Scenariusz obejmujący wiele lokalizacji lub hybrydowy można w przybliżeniu opisać jak w poniższej ilustracji:

![Łączność między lokacjami lokalnymi i zasobami platformy Azure][planning-guide-figure-2100]

Minimalnym wymaganiem jest użycie bezpiecznych protokołów komunikacyjnych, takich jak SSL/TLS na potrzeby dostępu do przeglądarki lub połączeń opartych na sieci VPN, aby uzyskać dostęp do usług platformy Azure. Przyjęto założenie, że firmy obsługują połączenie sieci VPN między siecią firmową i platformą Azure. Niektóre firmy mogą puste otworzyć wszystkie porty. Niektóre inne firmy mogą chcieć określić, które porty muszą być otwarte itd.

W poniższej tabeli wymieniono typowe porty komunikacyjne SAP. Zasadniczo wystarczy otworzyć port bramy SAP Gateway.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Usługa | Nazwa portu | Przykład `<nn`> = 01 | Domyślny zakres (min-max) | Komentarz |
| --- | --- | --- | --- | --- |
| Automatycznego |sapdp `<nn>` Zobacz * |3201 |3200 – 3299 |Dyspozytor SAP używany przez interfejs GUI oprogramowania SAP dla systemu Windows i środowiska Java |
| Serwer komunikatów |sapms `<sid`> Zobacz * * |3600 |Bezpłatna sapms`<anySID`> |SID = SAP-system-ID |
| Brama |sapgw `<nn`> Zobacz * |3301 |free |Brama SAP używana do komunikacji CPIC i RFC |
| Router SAP |sapdp99 |3299 |free |W programie/etc/Services można ponownie przypisać tylko nazwy usług (centralne wystąpienie) do dowolnej wartości po instalacji. |

*) nn = numer wystąpienia SAP

* *) SID = SAP-system-ID

Więcej szczegółowych informacji na temat portów wymaganych dla różnych produktów lub usług SAP według produktów SAP można znaleźć tutaj <https://scn.sap.com/docs/DOC-17124> .
W tym dokumencie powinno być możliwe otwieranie dedykowanych portów w urządzeniu sieci VPN niezbędnym dla określonych produktów i scenariuszy oprogramowania SAP.

Inne środki bezpieczeństwa podczas wdrażania maszyn wirtualnych w tym scenariuszu mogą polegać na utworzeniu [sieciowej grupy zabezpieczeń][virtual-networks-nsg] w celu zdefiniowania reguł dostępu.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Drukowanie na lokalnej drukarce sieciowej z wystąpienia SAP na platformie Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Drukowanie za pośrednictwem protokołu TCP/IP w scenariuszu obejmującym wiele lokalizacji

Skonfigurowanie lokalnych drukarek sieciowych opartych na protokole TCP/IP na maszynie wirtualnej platformy Azure jest ogólnie takie samo jak w sieci firmowej, przy założeniu, że nawiązano połączenie sieci VPN typu lokacja-lokacja lub ExpressRoute.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> W tym celu:
>
> * Niektóre drukarki sieciowe są dołączone do Kreatora konfiguracji, co ułatwia skonfigurowanie drukarki na maszynie wirtualnej platformy Azure. Jeśli żadne oprogramowanie kreatora nie zostało dystrybuowane z drukarką, ręcznym sposobem skonfigurowania drukarki jest utworzenie nowego portu TCP/IP drukarki.
> * Otwórz Panel sterowania — > urządzenia i drukarki — > dodać drukarkę
> * Wybierz pozycję Dodaj drukarkę przy użyciu adresu TCP/IP lub nazwy hosta
> * Wpisz adres IP drukarki
> * Port drukarki Standard 9100
> * W razie potrzeby ręcznie zainstaluj odpowiedni sterownik drukarki.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> * Podobnie jak w przypadku systemu Windows, wykonaj standardową procedurę instalacji drukarki sieciowej
> * po prostu postępuj zgodnie z publicznymi przewodnikami systemu Linux dla [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) lub [Red Hat i Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) sposobu dodawania drukarki.
>
>

---
![Drukowanie w sieci][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Drukarka oparta na hoście za pośrednictwem protokołu SMB (Drukarka udostępniona) w scenariuszu obejmującym wiele lokalizacji

Drukarki oparte na hoście nie są zgodne z siecią przez projektowanie. Drukarka oparta na hoście może być współużytkowana przez komputery w sieci, o ile drukarka jest podłączona do komputera z włączonym systemem. Połącz sieć firmową, a także opcję lokacja-lokacja lub ExpressRoute i Udostępnij drukarkę lokalną. Protokół SMB używa systemu NetBIOS zamiast DNS jako usługi nazw. Nazwa hosta NetBIOS może się różnić od nazwy hosta DNS. W przypadku standardowej nazwy hosta NetBIOS i nazwy hosta DNS są identyczne. Domena DNS nie ma sensu w przestrzeni nazw NetBIOS. W związku z tym w przestrzeni nazw NetBIOS nie można używać w pełni kwalifikowanej nazwy hosta DNS składającej się z nazwy hosta DNS i domeny DNS.

Udział drukarki jest identyfikowany przez unikatową nazwę w sieci:

* Nazwa hosta protokołu SMB (zawsze jest wymagana).
* Nazwa udziału (zawsze wymagana).
* Nazwa domeny, jeśli udział drukarki nie znajduje się w tej samej domenie co system SAP.
* Ponadto w celu uzyskania dostępu do udziału drukarki może być wymagana nazwa użytkownika i hasło.

Instrukcje:

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Udostępnianie drukarki lokalnej.
> Na maszynie wirtualnej platformy Azure Otwórz Eksploratora Windows i wpisz nazwę udziału drukarki.
> Kreator instalacji drukarki przeprowadzi Cię przez proces instalacji.
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Poniżej przedstawiono kilka przykładów dokumentacji dotyczącej konfigurowania drukarek sieciowych w systemie Linux, a także rozdział dotyczący drukowania w systemie Linux. Działa tak samo jak w przypadku maszyny wirtualnej z systemem Linux na platformie Azure, o ile maszyna wirtualna jest częścią sieci VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL lub Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Drukarka USB (przekazywanie drukarek)

Na platformie Azure możliwość Usługi pulpitu zdalnego zapewniania użytkownikom dostępu do swoich lokalnych urządzeń drukarki w sesji zdalnej jest niedostępna.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Więcej szczegółów dotyczących drukowania w systemie Windows można znaleźć tutaj: <https://technet.microsoft.com/library/jj590748.aspx> .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integracja systemów SAP Azure w systemie poprawek i transportowych (TMS) w wielu lokalizacjach

System zmian i transportu SAP (TMS) musi zostać skonfigurowany do eksportowania i importowania żądań transportu w różnych systemach. Przyjęto założenie, że wystąpienia deweloperskie systemu SAP (DEV) znajdują się na platformie Azure, a systemy kontroli jakości (pytań i odpowiedzi) działają w środowisku lokalnym. Ponadto Załóżmy, że istnieje centralny katalog transportowy.

##### <a name="configuring-the-transport-domain"></a>Konfigurowanie domeny transportu

Skonfiguruj domenę transportu w systemie wskazanym jako kontroler domeny transportu, zgodnie z opisem w temacie [Konfigurowanie kontrolera domeny transportu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Zostanie utworzony system TMSADM użytkownika i zostanie wygenerowana wymagana lokalizacja docelowa RFC. Te połączenia RFC można sprawdzić przy użyciu SM59 transakcji. Rozpoznawanie nazwy hosta musi być włączone w całej domenie transportu.

Instrukcje:

* W naszym scenariuszu postanowiono, że lokalny system QAS będzie kontrolerem domeny CTS. Wywołaj transakcję STMS. Zostanie wyświetlone okno dialogowe TMS. Zostanie wyświetlone okno dialogowe Konfigurowanie domeny transportowej. (To okno dialogowe pojawia się tylko wtedy, gdy nie skonfigurowano jeszcze domeny transportu).
* Upewnij się, że automatycznie utworzone TMSADM użytkownika są autoryzowane (SM59-> ABAP Connection-> TMSADM@E61.DOMAIN_E61 -> szczegóły-> narzędzia (M)-> test autoryzacji). Początkowy ekran transakcji STMS powinien wskazywać, że ten system SAP działa teraz jako kontroler domeny transportu, jak pokazano poniżej:

![Początkowy ekran transakcji STMS na kontrolerze domeny][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Uwzględnianie systemów SAP w domenie transportu

Sekwencja dołączania systemu SAP w domenie transportowej wygląda następująco:

* W systemie DEWELOPERSKIm na platformie Azure przejdź do systemu transportowego (Client 000) i Wywołaj transakcję STMS. Wybierz inną konfigurację z okna dialogowego i Kontynuuj z opcją Dołącz system w domenie. Określ kontroler domeny jako hosta docelowego ([w tym systemy SAP w domenie transportu](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). System oczekuje na uwzględnienie w domenie transportu.
* Ze względów bezpieczeństwa należy ponownie przejść do kontrolera domeny w celu potwierdzenia Twojego żądania. Wybierz pozycję przegląd systemu i zatwierdź system oczekujący. Następnie potwierdź monit, a konfiguracja zostanie dystrybuowana.

Ten system SAP zawiera teraz niezbędne informacje o wszystkich innych systemach SAP w domenie transportowej. W tym samym czasie dane adresowe nowego systemu SAP są wysyłane do wszystkich innych systemów SAP, a system SAP jest wprowadzany w profilu transportu programu do kontroli transportowej. Sprawdź, czy specyfikacje RFC i dostęp do katalogu transportowego w domenie działają.

Kontynuuj konfigurację systemu transportowego, tak jak opisano to w artykule [zmiana i system transportowy](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)dokumentacji.

Instrukcje:

* Upewnij się, że STMS lokalnie jest skonfigurowany.
* Upewnij się, że nazwa hosta kontrolera domeny transportu może zostać rozpoznana przez maszynę wirtualną na platformie Azure i na końcu.
* Wywołaj transakcję STMS — > innej konfiguracji — > dołączyć system do domeny.
* Potwierdź połączenie w lokalnym systemie TMS.
* Skonfiguruj trasy transportu, grupy i warstwy w zwykły sposób.

W przypadku scenariuszy obejmujących wiele lokalizacji połączonych między lokacjami, opóźnienie między środowiskiem lokalnym i platformą Azure nadal może być istotne. Jeśli korzystamy z sekwencji transportowania obiektów za pomocą systemów deweloperskich i testowych do środowiska produkcyjnego lub uważasz, że stosujemy transporty lub pakiety pomocy technicznej do różnych systemów, należy pamiętać, że zależnie od lokalizacji centralnego katalogu transportowego niektóre systemy będą napotykać na odczyt lub zapis danych w centralnym katalogu transportowym. Ta sytuacja jest podobna do konfiguracji krajobrazu oprogramowania SAP, w której różne systemy są rozłożone w różnych centrach danych o dużej odległości między centrami danych.

Aby obejść to opóźnienie i zapewnić, że systemy pracują szybko w przypadku odczytywania lub zapisywania do lub z katalogu transportowego, można skonfigurować dwie domeny transportu STMS (jedno dla lokalnego i jednego z systemów na platformie Azure i połączyć domeny transportu. Zapoznaj się z tą dokumentacją, która objaśnia zasady związane z tą koncepcją w oprogramowaniu SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Instrukcje:

* Skonfiguruj domenę transportu dla każdej lokalizacji (lokalnie i na platformie Azure) przy użyciu usługi Transaction STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Połącz domeny z linkiem domeny i Potwierdź połączenie między tymi dwiema domenami.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Dystrybuuj konfigurację do połączonego systemu.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Ruch RFC między wystąpieniami SAP znajdującymi się na platformie Azure i lokalnie (między środowiskami lokalnymi)

Ruch RFC między systemami, które są lokalne i na platformie Azure, muszą pracować. Aby skonfigurować SM59 transakcji połączenia w systemie źródłowym, w którym należy zdefiniować połączenie RFC do systemu docelowego. Konfiguracja jest podobna do standardowej instalacji połączenia RFC.

Przyjęto założenie, że w scenariuszu obejmującym wiele lokalizacji maszyny wirtualne, które uruchamiają systemy SAP, które muszą komunikować się ze sobą, znajdują się w tej samej domenie. W związku z tym Konfiguracja połączenia RFC między systemami SAP nie różni się od kroków instalacji i danych wejściowych w scenariuszach lokalnych.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Uzyskiwanie dostępu do lokalnego udziałów plików z wystąpień SAP znajdujących się na platformie Azure lub odwrotnie

Wystąpienia SAP zlokalizowane na platformie Azure muszą uzyskiwać dostęp do udziałów plików znajdujących się w siedzibie firmy. Ponadto lokalne wystąpienia SAP muszą uzyskiwać dostęp do udziałów plików, które znajdują się na platformie Azure. Aby włączyć udziały plików, należy skonfigurować uprawnienia i opcje udostępniania w systemie lokalnym. Upewnij się, że otwarto porty połączenia sieci VPN lub ExpressRoute między platformą Azure a centrum danych.

## <a name="supportability"></a>Możliwości obsługi

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Rozszerzenie platformy Azure dla oprogramowania SAP

Aby dostarczyć część informacji o infrastrukturze platformy Azure o znaczeniu krytycznych systemów SAP do wystąpień agentów hostów SAP zainstalowanych na maszynach wirtualnych, rozszerzenie platformy Azure (VM) dla oprogramowania SAP musi zostać zainstalowane dla wdrożonych maszyn wirtualnych. Ze względu na to, że wymagania dotyczące oprogramowania SAP dotyczyły aplikacji SAP, firma Microsoft zdecydowała się nie zaimplementować ogólnie wymaganych funkcji na platformie Azure, ale pozostawić ją klientom do wdrożenia niezbędnych rozszerzeń i konfiguracji maszyny wirtualnej w Virtual Machines uruchomionych na platformie Azure. Jednak zarządzanie wdrożeniem i cyklem życia rozszerzenia maszyny wirtualnej platformy Azure dla oprogramowania SAP będzie przede wszystkim zautomatyzowane przez platformę Azure.

#### <a name="solution-design"></a>Projekt rozwiązania

Rozwiązanie opracowane w celu umożliwienia agentowi hosta SAP uzyskania wymaganych informacji jest oparte na architekturze agenta maszyny wirtualnej platformy Azure i struktury rozszerzenia. Celem agenta maszyny wirtualnej platformy Azure i struktury rozszerzeń jest umożliwienie instalacji aplikacji programowych dostępnych w galerii rozszerzenia maszyny wirtualnej platformy Azure w ramach maszyny wirtualnej. Zasadą tego koncepcji jest umożliwienie (w przypadku rozszerzenia platformy Azure dla SAP) wdrożenia specjalnych funkcji na maszynę wirtualną oraz konfiguracji takiego oprogramowania w czasie wdrażania.

Agent maszyny wirtualnej platformy Azure, który umożliwia obsługę określonych rozszerzeń maszyn wirtualnych platformy Azure w ramach maszyny wirtualnej, jest domyślnie wstrzykiwany do maszyn wirtualnych z systemem Windows przy tworzeniu maszyny wirtualnej w Azure Portal. W przypadku SUSE, Red Hat lub Oracle Linux, Agent maszyny wirtualnej jest już częścią obrazu portalu Azure Marketplace. W takim przypadku jeden z nich przekaże maszynę wirtualną z systemem Linux z lokalnego na platformę Azure, a agent maszyny wirtualnej musi zostać zainstalowany ręcznie.

Podstawowe bloki konstrukcyjne rozwiązania, które zapewniają informacje o infrastrukturze platformy Azure dla agenta hosta SAP na platformie Azure, wyglądają następująco:

![Składniki rozszerzenia Microsoft Azure][planning-guide-figure-2400]

Jak pokazano na powyższym diagramie blokowym, jedna część rozwiązania jest hostowana w obrazie maszyny wirtualnej platformy Azure i w galerii rozszerzeń platformy Azure, która jest globalnie replikowanym repozytorium zarządzanym przez operacje na platformie Azure. Jest odpowiedzialny za wspólne działanie zespołu SAP/MS pracującego nad implementacją platformy SAP na platformie Azure, aby pracować z operacjami platformy Azure w celu publikowania nowych wersji rozszerzenia platformy Azure dla oprogramowania SAP.

Podczas wdrażania nowej maszyny wirtualnej z systemem Windows Agent maszyny wirtualnej platformy Azure zostanie automatycznie dodany do maszyny wirtualnej. Funkcja tego agenta służy do koordynowania ładowania i konfigurowania rozszerzeń platformy Azure dla maszyn wirtualnych. W przypadku maszyn wirtualnych z systemem Linux Agent maszyny wirtualnej platformy Azure jest już częścią obrazu systemu operacyjnego Azure Marketplace.

Istnieje jednak krok, który nadal musi być wykonywany przez klienta. Jest to włączenie i konfiguracja zbierania danych o wydajności. Proces związany z konfiguracją jest zautomatyzowany przez skrypt programu PowerShell lub polecenie interfejsu wiersza polecenia. Skrypt programu PowerShell można pobrać w centrum skryptów Microsoft Azure zgodnie z opisem w [Podręczniku wdrażania][deployment-guide].

Ogólna architektura rozszerzenia platformy Azure dla oprogramowania SAP wygląda następująco:

![Rozszerzenie platformy Azure dla oprogramowania SAP ][planning-guide-figure-2500]

**Aby zapoznać się z dokładnymi instrukcjami i szczegółowymi krokami dotyczącymi używania tych poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia podczas wdrożeń, wykonaj instrukcje podane w [przewodniku wdrażania][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integracja wystąpienia oprogramowania SAP zlokalizowanego na platformie Azure w SAProuter

Wystąpienia SAP działające na platformie Azure muszą być również dostępne z SAProuter.

![Połączenie sieciowe SAP-router][planning-guide-figure-2600]

SAProuter umożliwia komunikację TCP/IP między systemami uczestniczącymi, jeśli nie ma bezpośredniego połączenia IP. Zapewnia to, że nie jest konieczne połączenie kompleksowe między partnerami komunikacyjnymi na poziomie sieci. SAProuter domyślnie nasłuchuje na porcie 3299.
Aby połączyć wystąpienia SAP za pomocą SAProuter, należy podać ciąg SAProuter i nazwę hosta z każdą próbą nawiązania połączenia.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver jako Java

Do tej pory fokusem dokumentu jest NetWeaver SAP General lub The SAP NetWeaver ABAP Stack. W tej małych sekcjach wymieniono określone zagadnienia dotyczące stosu SAP Java. Jednym z najważniejszych aplikacji opartych na języku Java NetWeaver języka SAP jest Enterprise Portal SAP. Inne aplikacje oparte na oprogramowaniu SAP NetWeaver, takie jak SAP PI i Menedżer rozwiązań SAP, używają zarówno stosów SAP NetWeaver ABAP i Java. W związku z tym istnieje potrzeba uwzględnienia konkretnych aspektów związanych ze stosem Java NetWeaver.

### <a name="sap-enterprise-portal"></a>Enterprise Portal SAP

Instalacja portalu SAP na maszynie wirtualnej platformy Azure nie różni się od instalacji lokalnej w przypadku wdrażania w scenariuszach obejmujących wiele lokalizacji. Ponieważ serwer DNS jest wykonywany lokalnie, ustawienia portów poszczególnych wystąpień można wykonać zgodnie z konfiguracją lokalną. Zalecenia i ograniczenia opisane w niniejszym dokumencie mają do tej pory zastosowanie w przypadku aplikacji, takich jak SAP Enterprise Portal lub stosu Java NetWeaver środowiska SAP.

![Narażony Portal SAP][planning-guide-figure-2700]

Specjalnym scenariuszem wdrożenia przez niektórych klientów jest bezpośrednie narażenie Enterprise Portal SAP przez Internet, podczas gdy host maszyny wirtualnej jest połączony z siecią firmową za pośrednictwem tunelu VPN między lokacjami lub ExpressRoute. W takim scenariuszu należy upewnić się, że określone porty są otwarte i nie są blokowane przez zaporę lub sieciową grupę zabezpieczeń.

Początkowy identyfikator URI portalu to http (s): `<Portalserver`>:5XX00/irj, gdzie port jest sformułowany jako udokumentowany przez SAP w <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> .

![Konfiguracja punktu końcowego][planning-guide-figure-2800]

Jeśli chcesz dostosować adres URL i/lub porty Enterprise Portal SAP, zapoznaj się z tą dokumentacją:

* [Zmień adres URL portalu](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Zmień domyślne numery portów, numery portów portalu](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Wysoka dostępność (HA) i odzyskiwanie po awarii (DR) dla oprogramowania SAP NetWeaver uruchomionego na platformie Azure Virtual Machines

### <a name="definition-of-terminologies"></a>Definicja terminologii

Termin **wysoka dostępność (ha)** jest zwykle związany z zestawem technologii, które minimalizują przerwy w działaniu, zapewniając ciągłość działania usług informatycznych za pomocą nadmiarowych, odpornych na uszkodzenia lub chronionych w trybie failover składników w **tym samym** centrum danych. W naszym przypadku w ramach jednego regionu świadczenia usługi Azure.

**Odzyskiwanie po awarii (Dr)** służy również do minimalizowania przerw w działaniu usług IT oraz odzyskiwania, ale w **różnych** centrach danych, które zwykle znajdują setki kilometrów. W naszym przypadku zazwyczaj między różnymi regionami świadczenia usługi Azure w tym samym regionie geopolitycznym lub zgodnie z ustalonym przez Ciebie klientem jako klient.

### <a name="overview-of-high-availability"></a>Przegląd wysokiej dostępności

Na platformie Azure można rozdzielić dyskusję na temat wysokiej dostępności oprogramowania SAP na dwie części:

* **Wysoka dostępność infrastruktury platformy Azure**, na przykład ha obliczeń (maszyn wirtualnych), Sieć, magazyn itp. i jej korzyści w celu zwiększenia dostępności aplikacji SAP.
* **Wysoka dostępność aplikacji SAP**, na przykład ha oprogramowania SAP Components:
  * Serwery aplikacji SAP
  * Wystąpienie SAP ASCS/SCS
  * Serwer bazy danych

i jak można je połączyć z infrastrukturą Azure o wysokiej dostępności.

Wysoka dostępność oprogramowania SAP na platformie Azure zawiera pewne różnice w porównaniu z wysoką dostępnością oprogramowania SAP w lokalnym środowisku fizycznym lub wirtualnym. Poniższy dokument dotyczący oprogramowania SAP opisuje standardowe konfiguracje wysokiej dostępności SAP w środowiskach zwirtualizowanych w systemie Windows: <https://scn.sap.com/docs/DOC-44415> . W systemie Linux nie istnieje zintegrowana z sapinst konfiguracją SAP-HA, tak jak w przypadku systemu Windows. W przypadku środowiska SAP HA w środowisku lokalnym dla systemu Linux Znajdź więcej informacji tutaj: <https://scn.sap.com/docs/DOC-8541> .

### <a name="azure-infrastructure-high-availability"></a>Wysoka dostępność infrastruktury platformy Azure

Obecnie jest dostępna umowa SLA na jedną maszynę wirtualną wynoszącą 99,9%. Aby poznać sposób, w jaki może wyglądać dostępność pojedynczej maszyny wirtualnej, możesz skompilować produkt z różnymi dostępnymi umowy SLA platformy Azure: <https://azure.microsoft.com/support/legal/sla/> .

Podstawą obliczenia jest 30 dni miesięcznie lub 43200 minut. W związku z tym 0,05% przestoju odnosi się do 21,6 minut. Jak zwykle dostępność różnych usług będzie pomnożyć w następujący sposób:

(Usługa dostępności #1/100) * (usługa dostępności #2/100) * (usługa dostępności #3/100)

Typu

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 lub Ogólna dostępność 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Wysoka dostępność maszyny wirtualnej (VM)

Istnieją dwa typy zdarzeń platformy Azure, które mogą mieć wpływ na dostępność maszyn wirtualnych: planowana konserwacja i nieplanowana konserwacja.

* Zdarzenia planowanej konserwacji to okresowe aktualizacje tworzone przez firmę Microsoft dla podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy działania maszyn wirtualnych.
* Zdarzenia nieplanowanej konserwacji występują w przypadku wystąpienia awarii sprzętu lub infrastruktury fizycznej zawierającej maszynę wirtualną. Mogą być to awarie sieci lokalnej, błędy na dysku lokalnym lub inne awarie na poziomie regału. Po wykryciu tego błędu platforma Azure automatycznie przeprowadzi migrację maszyny wirtualnej z serwera fizycznego o złej kondycji, który hostuje maszynę wirtualną w dobrej kondycji. Te zdarzenia występują rzadko, ale mogą również spowodować ponowne uruchomienie maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [dostępność maszyn wirtualnych z systemem Windows na platformie Azure](../../windows/manage-availability.md) i [dostępność maszyn wirtualnych z systemem Linux na platformie Azure](../../linux/manage-availability.md).

#### <a name="azure-storage-redundancy"></a>Nadmiarowość usługi Azure Storage

Dane na koncie usługi Microsoft Azure Storage są zawsze replikowane w celu zapewnienia trwałości i wysokiej dostępności, które spełniają warunki umowy SLA usługi Azure Storage, nawet w przypadku przejściowych awarii sprzętu.

Ponieważ usługa Azure Storage domyślnie zachowuje trzy obrazy danych, RAID5 lub RAID1 na wielu dyskach platformy Azure nie jest konieczna.

Aby uzyskać więcej informacji, zobacz [nadmiarowość usługi Azure Storage](../../../storage/common/storage-redundancy.md).

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Korzystanie z ponownego uruchamiania maszyny wirtualnej infrastruktury platformy Azure w celu uzyskania większej dostępności aplikacji SAP

Jeśli zdecydujesz się nie używać takich funkcji, jak Windows Server Failover Clustering (WSFC) lub Pacemaker w systemie Linux (obecnie obsługiwane tylko przez program SLES 12 i nowsze), funkcja ponownego uruchamiania maszyny wirtualnej platformy Azure jest wykorzystywana do ochrony systemu SAP przed planowanym i nieplanowanym przestojem infrastruktury serwera fizycznego platformy Azure i ogólną platformą platformy Azure.

> [!NOTE]
> Należy pamiętać, że ponowne uruchomienie maszyny wirtualnej platformy Azure obejmuje głównie ochronę maszyn wirtualnych, a nie aplikacji. Ponowne uruchomienie maszyny wirtualnej nie oferuje wysokiej dostępności dla aplikacji SAP, ale oferuje pewien poziom dostępności infrastruktury i dlatego jest to pośrednio wyższa dostępność systemów SAP. Umowa SLA nie jest również dostępna dla czasu, w którym zajmie się ponownym uruchomieniem maszyny wirtualnej po zaplanowanym lub nieplanowanym przestoju hosta. W związku z tym ta metoda wysokiej dostępności nie jest odpowiednia dla kluczowych składników systemu SAP, takich jak (A) SCS lub DBMS.
>
>

Innym ważnym elementem infrastruktury dla wysokiej dostępności jest magazyn. Na przykład umowa SLA usługi Azure Storage to dostępność na 99,9%. Jeśli jeden z nich wdraża wszystkie maszyny wirtualne z dyskami w jednym koncie usługi Azure Storage, potencjalna niedostępność usługi Azure Storage spowoduje niedostępność wszystkich maszyn wirtualnych umieszczonych w tym koncie usługi Azure Storage, a także wszystkie składniki SAP działające w tych maszynach wirtualnych.

Zamiast umieszczania wszystkich maszyn wirtualnych w jednym koncie usługi Azure Storage, można także użyć dedykowanych kont magazynu dla każdej maszyny wirtualnej, a w ten sposób zwiększyć ogólną dostępność aplikacji dla maszyn wirtualnych i SAP przy użyciu wielu niezależnych kont usługi Azure Storage.

Usługa Azure Managed disks jest automatycznie umieszczana w domenie błędów maszyny wirtualnej, do której są dołączone. Jeśli umieścisz dwie maszyny wirtualne w zestawie dostępności i użyjesz Managed Disks, platforma zajmie również się dystrybucją Managed Disks do różnych domen błędów. Jeśli planujesz używać Premium Storage, zdecydowanie zalecamy korzystanie z funkcji zarządzania dyskami.

Przykładowa architektura systemu SAP NetWeaver, która korzysta z usług Azure Infrastructure HA i Storage, może wyglądać następująco:

![Wykorzystanie infrastruktury platformy Azure HA do osiągnięcia wyższej dostępności aplikacji SAP][planning-guide-figure-2900]

Przykładowa architektura systemu SAP NetWeaver, która używa infrastruktury platformy Azure o wysokiej dostępności i Managed Disks może wyglądać następująco:

![Wykorzystanie infrastruktury platformy Azure HA do osiągnięcia wyższej dostępności aplikacji SAP][planning-guide-figure-2901]

W przypadku krytycznych składników SAP osiągnięto następujące dotąd:

* Wysoka dostępność serwerów aplikacji SAP (AS)

  Wystąpienia serwera aplikacji SAP są nadmiarowe składniki. Każde wystąpienie programu SAP AS jest wdrażane na jego własnej maszynie wirtualnej, która jest uruchomiona w innej domenie błędów i uaktualnień platformy Azure (patrz rozdział [domen błędów][planning-guide-3.2.1] i [domen uaktualnienia][planning-guide-3.2.2]). Jest to zapewniane przy użyciu zestawów dostępności platformy Azure (zobacz rozdział [dostępności platformy Azure][planning-guide-3.2.3]). Potencjalna planowana lub nieplanowana niedostępność platformy Azure lub domeny uaktualnienia spowoduje niedostępną liczbę maszyn wirtualnych z systemem SAP jako wystąpieniami.

  Każde wystąpienie SAP AS jest umieszczane na własnym koncie usługi Azure Storage — potencjalna niedostępność jednego konta usługi Azure Storage spowoduje niedostępność tylko jednej maszyny wirtualnej ze swoim wystąpieniem SAP AS. Należy jednak pamiętać, że istnieje ograniczenie kont usługi Azure Storage w ramach jednej subskrypcji platformy Azure. Aby zapewnić automatyczne uruchamianie wystąpienia (A) SCS po ponownym uruchomieniu maszyny wirtualnej, upewnij się, że parametr Autostart został ustawiony w (A) SCS początkowy profil, opisany w rozdziale [Używanie Autostart dla wystąpień SAP][planning-guide-11.5].
  Aby uzyskać więcej informacji, przeczytaj również rozdział [wysoka dostępność dla serwerów aplikacji SAP][planning-guide-11.4.1] .

  Nawet jeśli używasz Managed Disks, te dyski również są przechowywane na koncie usługi Azure Storage i mogą być niedostępne w przypadku awarii magazynu.

* *Więcej* Dostępność wystąpienia oprogramowania SAP (A) SCS

  Tutaj korzystamy z ponownego uruchamiania maszyny wirtualnej platformy Azure, aby chronić maszynę wirtualną z zainstalowanym wystąpieniem SCS SAP (A). W przypadku planowanych lub nieplanowanych przestojów serwerów platformy Azure maszyny wirtualne zostaną ponownie uruchomione na innym dostępnym serwerze. Jak wspomniano wcześniej, usługa Azure VM restart służy głównie do ochrony maszyn wirtualnych, a nie aplikacji, w tym przypadku wystąpienia (A) SCS. Po ponownym uruchomieniu maszyny wirtualnej uzyskamy niejawnie wyższą dostępność wystąpienia SAP (A) SCS. Aby upewnić się, że po ponownym uruchomieniu maszyny wirtualnej jest uruchamiane automatyczne uruchamianie programu (A) SCS, pamiętaj, aby ustawić parametr Autostart w (A) SCS początkowy profil opisany w rozdziale [using Autostart for SAP Instances][planning-guide-11.5]. Oznacza to, że wystąpienie (A) SCS jako pojedynczy punkt awarii (SPOF) działające w ramach jednej maszyny wirtualnej będzie współczynnikiem Determinative dla dostępności całego oprogramowania SAP.

* *Więcej* Dostępność serwera DBMS

  W tym miejscu, podobnie jak w przypadku użycia wystąpienia usługi SAP (A) SCS, używamy ponownego uruchamiania maszyny wirtualnej platformy Azure w celu ochrony maszyny wirtualnej przy użyciu zainstalowanego oprogramowania DBMS, a firma Microsoft zapewnia wyższą dostępność oprogramowania DBMS przez ponowne uruchomienie maszyny wirtualnej.
  System DBMS działający w ramach jednej maszyny wirtualnej jest również SPOF i jest współczynnikiem Determinative do zapewnienia dostępności całego środowiska SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Wysoka dostępność aplikacji SAP na platformie Azure IaaS

Aby zapewnić pełną dostępność systemu SAP, musimy chronić wszystkie krytyczne składniki systemu SAP, na przykład nadmiarowe serwery aplikacji SAP i unikatowe składniki (na przykład pojedynczy punkt awarii), takie jak rozwiązanie SAP (A) SCS i system DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Wysoka dostępność dla serwerów aplikacji SAP

W przypadku serwerów aplikacji SAP/okien dialogowych nie trzeba myśleć o konkretnym rozwiązaniu wysokiej dostępności. Wysoka dostępność jest osiągana przez nadmiarowość i dlatego ma wystarczającą ilość z nich w różnych maszynach wirtualnych. Wszystkie te dane powinny być umieszczane w tym samym zestawie dostępności platformy Azure, aby uniknąć, że maszyny wirtualne mogą być aktualizowane w tym samym czasie podczas planowanego przestoju konserwacji. Podstawowe funkcje, które są oparte na różnych uaktualnieniach i domenach błędów w ramach jednostki skalowania platformy Azure, zostały już wprowadzone w obszarze [domeny uaktualnień][planning-guide-3.2.2]rozdziałów. Zestawy dostępności platformy Azure zostały przedstawione w rozdziale [zestawy dostępności platformy Azure][planning-guide-3.2.3] tego dokumentu.

Nie istnieje nieskończona liczba domen błędów i uaktualnień, które mogą być używane przez zestaw dostępności platformy Azure w ramach jednostki skalowania platformy Azure. Oznacza to, że umieszczenie wielu maszyn wirtualnych w jednym zestawie dostępności, szybsze lub późniejsze więcej niż jedna maszyna wirtualna jest zakończyła się w tej samej domenie błędów lub uaktualnienia.

Wdrożenie kilku wystąpień serwera aplikacji SAP na swoich dedykowanych maszynach wirtualnych i założenie, że mamy pięć domen uaktualnienia, na końcu zostanie zastosowany następujący obraz. Rzeczywista Maksymalna liczba domen błędów i aktualizacji w ramach zestawu dostępności może ulec zmianie w przyszłości:

![HA serwerów aplikacji SAP na platformie Azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Wysoka dostępność dla usług SAP Central na platformie Azure

Aby uzyskać informacje o architekturze wysokiej dostępności usług SAP Central na platformie Azure, zapoznaj się z artykułem [Architektura wysokiej dostępności artykułu i scenariusze dotyczące oprogramowania SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) jako informacji o wpisie. Artykuł wskazuje bardziej szczegółowe opisy dla konkretnych systemów operacyjnych.

#### <a name="high-availability-for-the-sap-database-instance"></a>Wysoka dostępność wystąpienia bazy danych SAP

Typowa konfiguracja systemu SAP DBMS HA jest oparta na dwóch maszynach wirtualnych z systemem DBMS, w których funkcja wysokiej dostępności w systemie DBMS jest używana do replikowania danych z aktywnego wystąpienia systemu DBMS na drugą maszynę wirtualną do wystąpienia pasywnego systemu DBMS.

Funkcja wysokiej dostępności i odzyskiwania po awarii dla systemu DBMS ogólnie, a także konkretne systemy DBMS są opisane w [przewodniku wdrażania systemu DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Kompleksowa wysoka dostępność dla kompletnego systemu SAP

Poniżej przedstawiono dwa przykłady kompletnej architektury SAP NetWeaver HA na platformie Azure — jeden dla systemu Windows i jeden dla systemów Linux.

Tylko dyski niezarządzane: poniższe koncepcje mogą wymagać naruszenia bezpieczeństwa w przypadku wdrażania wielu systemów SAP, a liczba wdrożonych maszyn wirtualnych przekracza maksymalny limit kont magazynu na subskrypcję. W takich przypadkach wirtualne dyski twarde maszyn wirtualnych muszą być połączone w ramach jednego konta magazynu. Zwykle można to zrobić przez połączenie wirtualnych dysków twardych z maszynami wirtualnymi warstwy aplikacji SAP różnych systemów SAP.  W jednym koncie usługi Azure Storage połączone są również różne wirtualne dyski twarde różnych maszyn wirtualnych DBMS różnych systemów SAP. W związku z tym utrzymywanie limitów IOPS na kontach usługi Azure Storage ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )


##### <a name="windows-logologo_windows-ha-on-windows"></a>![Logo Windows.][Logo_Windows] HA w systemie Windows

![Architektura HA aplikacji SAP NetWeaver z SQL Server na platformie Azure IaaS][planning-guide-figure-3200]

Następujące konstrukcje platformy Azure są używane dla systemu SAP NetWeaver, aby zminimalizować wpływ na problemy z infrastrukturą i stosowanie poprawek hosta:

* Kompletny system jest wdrażany na platformie Azure (wymagane jest wystąpienie SCS w warstwie DBMS, a) i kompletna warstwa aplikacji musi działać w tej samej lokalizacji.
* Pełny system działa w ramach jednej subskrypcji platformy Azure (wymagane).
* Pełny system działa w ramach jednej Virtual Network platformy Azure (wymagane).
* Rozdzielenie maszyn wirtualnych jednego systemu SAP na trzy zestawy dostępności jest możliwe nawet ze wszystkimi maszynami wirtualnymi należącymi do tego samego Virtual Network.
* Każda warstwa (na przykład DBMS, ASCS, serwery aplikacji) musi korzystać z dedykowanego zestawu dostępności.
* Wszystkie maszyny wirtualne, na których są uruchomione wystąpienia systemu DBMS, są w jednym zestawie dostępności. Przyjęto założenie, że na system istnieje więcej niż jedna maszyna wirtualna z wystąpieniami systemu DBMS, ponieważ są używane natywne funkcje wysokiej dostępności w systemie DBMS, takie jak SQL Server AlwaysOn lub Oracle Data Guard.
* Wszystkie maszyny wirtualne z wystąpieniami systemu DBMS używają własnego konta magazynu. Pliki danych i dziennika systemu DBMS są replikowane z jednego konta magazynu do innego konta magazynu przy użyciu funkcji wysokiej dostępności systemu DBMS, które synchronizują dane. Niedostępność jednego konta magazynu spowoduje niedostępność jednego węzła klastra SQL systemu Windows, ale nie całej usługi SQL Server.
* Wszystkie maszyny wirtualne z systemem (A) SCS wystąpienie jednego systemu SAP znajdują się w jednym zestawie dostępności. Klaster trybu failover systemu Windows Server (WSFC) jest skonfigurowany w ramach tych maszyn wirtualnych do ochrony wystąpienia programu (A) SCS.
* Wszystkie maszyny wirtualne z systemem (A) SCS używają własnych kont magazynu. Z Pliki wystąpienia SCS i folder globalny SAP są replikowane z jednego konta magazynu do innego konta magazynu przy użyciu replikacji danych usługi oprogramowanie SIOS. Niedostępność jednego konta magazynu spowoduje niedostępność jednego (A) SCS węzła klastra systemu Windows, ale nie całej (A) usługi SCS.
* WSZYSTKIE maszyny wirtualne reprezentujące warstwę serwera aplikacji SAP znajdują się w trzecim zestawie dostępności.
* WSZYSTKIE maszyny wirtualne z serwerami aplikacji SAP używają własnych kont magazynu. Niedostępność jednego konta magazynu spowoduje niedostępność jednego serwera aplikacji SAP, gdzie inne serwery aplikacji SAP nadal działają.

Na poniższej ilustracji przedstawiono te same poziomy, przy użyciu Managed Disks.

![Architektura HA aplikacji SAP NetWeaver z SQL Server na platformie Azure IaaS][planning-guide-figure-3201]

##### <a name="linux-logologo_linux-ha-on-linux"></a>![Logo systemu Linux.][Logo_Linux] HA w systemie Linux

Architektura SAP HA w systemie Linux na platformie Azure jest zasadniczo taka sama jak w przypadku systemu Windows, jak opisano powyżej. Zapoznaj się z listą obsługiwanych rozwiązań wysokiej dostępności w programie SAP Note [1928533] .

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Używanie funkcji Autostart dla wystąpień SAP

SAP oferuje funkcje do uruchamiania wystąpień SAP bezpośrednio po rozpoczęciu systemu operacyjnego w ramach maszyny wirtualnej. Dokładne kroki zostały udokumentowane w artykule z bazy wiedzy SAP [1909114]. Jednak SAP nie zaleca się używania tego ustawienia już z powodu braku kontroli w kolejności ponownego uruchamiania wystąpień, przy założeniu, że na maszynę wirtualną występuje więcej niż jedna maszyna wirtualna lub wiele wystąpień. Zakładając, że typowy scenariusz platformy Azure w jednym wystąpieniu serwera aplikacji SAP na maszynie wirtualnej, a przypadek pojedynczej maszyny wirtualnej został ostatecznie uruchomiony, Autostart nie ma znaczenia i można ją włączyć, dodając następujący parametr:

`Autostart = 1`

W profilu początkowym wystąpienia SAP ABAP i/lub Java.

> [!NOTE]
> Parametr Autostart może również mieć pewne downfalls. W bardziej szczegółowym czasie parametr wyzwala uruchomienie wystąpienia SAP ABAP lub Java po uruchomieniu powiązanej usługi systemu Windows/Linux wystąpienia. Zdarza się to w przypadku uruchamiania systemu operacyjnego. Jednak ponowne uruchomienia usług SAP są również typowym rozwiązaniem dla funkcji zarządzania cyklem życia oprogramowania SAP, takich jak Suma lub inne aktualizacje lub uaktualnienia. Te funkcje nie oczekują, że wystąpienie zostanie automatycznie uruchomione ponownie. W związku z tym należy wyłączyć parametr Autostart przed uruchomieniem takich zadań. Parametru Autostart nie należy również używać dla wystąpień SAP, które są klastrowane, takich jak ASCS/SCS/CI.
>
>

Zobacz dodatkowe informacje dotyczące autostartu wystąpień oprogramowania SAP tutaj:

* [Uruchamianie/zatrzymywanie oprogramowania SAP wraz z uruchamianiem/zatrzymywaniem serwera UNIX](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Uruchamianie i zatrzymywanie agentów zarządzania SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Jak włączyć funkcję autostartu bazy danych HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Większe 3-warstwowe systemy SAP
Zagadnienia wysokiej dostępności dotyczące 3-warstwowych konfiguracji SAP zostały już omówione we wcześniejszych sekcjach. Ale co się stało z systemami, w których wymagania dotyczące serwera DBMS są zbyt duże, aby znajdowały się na platformie Azure, ale warstwa aplikacji SAP została wdrożona na platformie Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Lokalizacja 3-warstwowych konfiguracji SAP
Nie jest obsługiwane podział warstwy aplikacji ani warstwy aplikacji i DBMS między środowiskiem lokalnym i platformą Azure. System SAP jest całkowicie wdrożony lokalnie lub na platformie Azure. Nie jest również obsługiwane, aby niektóre serwery aplikacji działały lokalnie i inne na platformie Azure. To jest punkt początkowy dyskusji. Nie obsługujemy również składników DBMS systemu SAP i warstwy serwera aplikacji SAP wdrożonych w dwóch różnych regionach świadczenia usługi Azure. Na przykład system DBMS w warstwie zachodnie stany USA i SAP w regionie Środkowe stany USA. Przyczyną braku obsługi takich konfiguracji jest czułość opóźnienia architektury SAP NetWeaver.

Jednak w ciągu ostatniego roku partnerzy centrum danych opracowała wspólne lokalizacje z regionami świadczenia usługi Azure. Te wspólne lokalizacje często znajdują się w pobliżu fizycznych centrów danych platformy Azure w regionie świadczenia usługi Azure. Krótka odległość i połączenie zasobów w wspólnej lokalizacji za pomocą ExpressRoute na platformie Azure może skutkować opóźnieniem mniejszym niż 2 milisekund. W takich przypadkach, aby znaleźć warstwę systemu DBMS (w tym magazyn SAN/NAS) w takiej samej lokalizacji i możliwe jest stosowanie warstwy aplikacji SAP na platformie Azure. [Duże wystąpienia Hana](./hana-overview-architecture.md).

### <a name="offline-backup-of-sap-systems"></a>Kopia zapasowa offline systemów SAP
Zależnie od wybranej konfiguracji SAP (2-warstwowej lub 3-warstwowej) może być konieczna kopia zapasowa. Zawartość samej maszyny wirtualnej Plus, aby utworzyć kopię zapasową bazy danych programu. Należy oczekiwać wykonania kopii zapasowych związanych z systemem DBMS przy użyciu metod bazy danych. Szczegółowy opis różnych baz danych można znaleźć w [podręczniku systemu DBMS][dbms-guide]. Z drugiej strony można utworzyć kopię zapasową danych SAP w trybie offline (w tym również zawartość bazy danych) zgodnie z opisem w tej sekcji lub w trybie online, zgodnie z opisem w następnej sekcji.

Kopia zapasowa offline będzie zasadniczo wymagała zamknięcia maszyny wirtualnej za pomocą Azure Portal i kopii podstawowego dysku maszyny wirtualnej oraz wszystkich dołączonych dysków do maszyny wirtualnej. Spowoduje to zachowanie obrazu punktu w czasie maszyny wirtualnej i skojarzonego z nim dysku. Zalecane jest skopiowanie kopii zapasowych na inne konto usługi Azure Storage. W związku z tym należy zastosować procedurę opisaną w rozdziale [kopiowanie dysków między kontami usługi Azure Storage][planning-guide-5.4.2] tego dokumentu.


Przywrócenie tego stanu będzie obejmować usunięcie podstawowej maszyny wirtualnej, a także oryginalnych dysków podstawowej maszyny wirtualnej i dysków zainstalowanych, skopiowanie zapisanych dysków na oryginalne konto magazynu lub grupę zasobów dla dysków zarządzanych, a następnie ponowne wdrożenie systemu.
W tym artykule przedstawiono przykład sposobu tworzenia skryptu tego procesu w programie PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Pamiętaj, aby zainstalować nową licencję SAP od momentu przywrócenia kopii zapasowej maszyny wirtualnej, jak opisano powyżej, tworzy nowy klucz sprzętowy.

### <a name="online-backup-of-an-sap-system"></a>Kopia zapasowa online systemu SAP

Kopia zapasowa systemu DBMS jest wykonywana przy użyciu metod specyficznych dla systemu DBMS, zgodnie z opisem w [podręczniku systemu DBMS][dbms-guide].

W przypadku innych maszyn wirtualnych w systemie SAP można utworzyć kopię zapasową przy użyciu funkcji kopii zapasowej maszyny wirtualnej platformy Azure. Kopia zapasowa maszyny wirtualnej platformy Azure to standardowa metoda tworzenia kopii zapasowej kompletnej maszyny wirtualnej na platformie Azure. Azure Backup przechowuje kopie zapasowe na platformie Azure i ponownie umożliwia przywracanie maszyny wirtualnej.

> [!NOTE]
> Począwszy od systemu gru 2015 przy użyciu kopii zapasowej maszyny wirtualnej nie zachowuje unikatowego identyfikatora maszyny wirtualnej, który jest używany na potrzeby licencjonowania SAP. Oznacza to, że przywracanie z kopii zapasowej maszyny wirtualnej wymaga instalacji nowego klucza licencji SAP, ponieważ przywrócona maszyna wirtualna jest uważana za nową maszynę wirtualną, a nie zastępującą dotychczasową, która została zapisana.
>
> ![Logo Windows.][Logo_Windows] Windows
>
> Teoretycznie można utworzyć kopię zapasową maszyn wirtualnych z uruchomionymi bazami danych w spójny sposób, jeśli system DBMS obsługuje usługę VSS systemu Windows (Usługa kopiowania woluminów w tle <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> ), na przykład SQL Server.
> Należy jednak pamiętać, że w oparciu o kopie zapasowe maszyn wirtualnych platformy Azure nie jest możliwe przywracanie do punktu w czasie. Dlatego zaleca się wykonanie kopii zapasowych baz danych z funkcjonalnością systemu DBMS zamiast polegania na kopii zapasowej maszyny wirtualnej platformy Azure.
>
> Aby zapoznać się z tworzeniem kopii zapasowej maszyny wirtualnej platformy Azure, Zacznij tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-vms> .
>
> Inne możliwości to użycie kombinacji Data Protection Manager zainstalowanych na maszynie wirtualnej platformy Azure i Azure Backup do tworzenia kopii zapasowych i przywracania baz danych. Więcej informacji można znaleźć tutaj: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction> .
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Nie istnieje odpowiednik usługi VSS systemu Windows w systemie Linux. W związku z tym tylko kopie zapasowe spójne z plikami są możliwe, ale nie kopie zapasowe spójne z aplikacjami. Kopia zapasowa SAP DBMS powinna być wykonywana przy użyciu funkcji systemu DBMS. System plików, który zawiera dane związane z SAP, może być zapisany na przykład przy użyciu funkcji pułapki, zgodnie z opisem w tym miejscu: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Witryna Azure as DR dla środowiska produkcyjnego SAP Landscapes

Od połowy 2014 rozszerzenia do różnych składników w zakresie funkcji Hyper-V, programu System Center i platformy Azure umożliwiają użycie platformy Azure jako witryny DR dla maszyn wirtualnych działających lokalnie na podstawie funkcji Hyper-V.

Blog zawierający szczegółowe informacje o sposobie wdrażania tego rozwiązania został opisany tutaj: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery> .

## <a name="summary-for-high-availability-for-sap-systems"></a>Podsumowanie wysokiej dostępności dla systemów SAP

Kluczowe punkty wysokiej dostępności dla systemów SAP na platformie Azure to:

* W tym momencie single point of failure SAP nie można zabezpieczyć dokładnie tak samo jak w przypadku wdrożeń lokalnych. Przyczyną jest to, że udostępnione klastry dysków nie mogą jeszcze być wbudowane w platformę Azure bez używania oprogramowania innej firmy.
* W przypadku warstwy DBMS należy użyć funkcji systemu DBMS, która nie jest zależna od współużytkowanej technologii klastra dysków. Szczegóły zostały udokumentowane w [podręczniku systemu DBMS][dbms-guide].
* Aby zminimalizować wpływ problemów w ramach domen błędów w ramach konserwacji infrastruktury lub hosta platformy Azure, należy użyć zestawów dostępności platformy Azure:
  * Zalecane jest posiadanie jednego zestawu dostępności dla warstwy aplikacji SAP.
  * Zalecane jest posiadanie oddzielnego zestawu dostępności dla warstwy systemu SAP DBMS.
  * NIE zaleca się stosowania tego samego zestawu dostępności dla maszyn wirtualnych z różnymi systemami SAP.
  * Zalecane jest użycie Managed Disks w warstwie Premium.
* Aby utworzyć kopię zapasową warstwy systemu SAP DBMS, należy zapoznać się z [przewodnikiem][dbms-guide]dotyczącym systemu DBMS.
* Tworzenie kopii zapasowych wystąpień okien dialogowych SAP sprawia, że jest to nieco szybsze, ponieważ zazwyczaj można ponownie wdrożyć proste wystąpienia okna dialogowego.
* Tworzenie kopii zapasowej maszyny wirtualnej, która zawiera katalog globalny systemu SAP i z nią wszystkie profile różnych wystąpień, jest zrozumiała i powinna być wykonywana przy użyciu usługi Kopia zapasowa systemu Windows lub, na przykład, pułapka w systemie Linux. Ponieważ istnieją różnice między systemami Windows Server 2008 (R2) i Windows Server 2012 (R2), które ułatwiają tworzenie kopii zapasowych przy użyciu nowszych wersji systemu Windows Server, zalecamy uruchomienie systemu Windows Server 2012 (R2) jako systemu operacyjnego gościa systemu Windows.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułami:

- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./deployment-guide.md)
- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md)
- [Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
