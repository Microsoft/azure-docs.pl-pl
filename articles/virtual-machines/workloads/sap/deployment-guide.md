---
title: Wdrożenie maszyn wirtualnych platformy Azure dla sap NetWeaver | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć oprogramowanie SAP na maszynach wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: sedusch
ms.openlocfilehash: 7fb87380047d046a580d1ad62b1d7107a94bb297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239883"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Wdrożenie maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Buforowanie maszyn wirtualnych i vhd)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura wdrożenia RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Wysoka dostępność i odzyskiwanie po awarii za pomocą maszyn wirtualnych platformy Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 i nowsze)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 i wcześniejsze wersje)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Używanie obrazu programu SQL Server z portalu Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Ogólne podsumowanie programu SQL Server for SAP w usłudze Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Szczegółowe informacje na temat RDBMS programu SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfiguracja magazynu)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Tworzenie kopii zapasowych i przywracanie)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Innych)
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

[deployment-guide]:deployment-guide.md (Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Zasoby SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Wdrażanie maszyny Wirtualnej przy użyciu obrazu niestandardowego)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenariusz 1: Wdrażanie maszyny Wirtualnej z portalu Azure Marketplace dla systemu SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazem niestandardowym dla systemu SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenariusz 3: Przenoszenie maszyny Wirtualnej z lokalnego przy użyciu nieogólnionej dysku twardego platformy Azure z sap)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenariusze wdrażania maszyn wirtualnych dla sap na platformie Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Wdrażanie poleceń cmdlet programu Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Pobieranie i importowanie poleceń cmdlet programu PowerShell odpowiednich dla systemu SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Dołączanie maszyny Wirtualnej do domeny lokalnej — tylko dla systemu Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Pobieranie, instalowanie i włączanie agenta maszyny wirtualnej platformy Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurowanie rozszerzenia platformy Azure dla systemu SAP)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Sprawdzanie gotowości dla rozszerzenia platformy Azure dla SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Sprawdzanie kondycji rozszerzenia platformy Azure dla konfiguracji SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Rozwiązywanie problemów z rozszerzeniem platformy Azure dla systemu SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurowanie rozszerzenia maszyny Wirtualnej)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurowanie serwera proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Sprawdzanie i rozwiązywanie problemów z kompleksowym zbieraniem danych dla agenta hosta SAP)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Zasobów)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Wysoka dostępność i odzyskiwanie po awarii dla sap NetWeaver działającego na maszynach wirtualnych platformy Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Wysoka dostępność serwerów aplikacji SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Korzystanie z autostartu dla wystąpień SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Tylko w chmurze — wdrożenia maszyn wirtualnych na platformie Azure bez zależności w lokalnej sieci klientów)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Międzylokaj — wdrażanie pojedynczych lub wielu maszyn wirtualnych SAP na platformie Azure w pełni zintegrowanych z siecią lokalną)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiony platformy Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domeny błędów)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uaktualnianie domen)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Zestawy dostępności platformy Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Koncepcja maszyn wirtualnych platformy Microsoft Azure)
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Usługa Azure w magazynie w wersji premium)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Przenoszenie maszyny Wirtualnej z lokalnego środowiska na platformę Azure za pomocą dysku nieuogólnionego)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Wdrażanie maszyny Wirtualnej z obrazem specyficznym dla klienta)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Przygotowanie do przenoszenia maszyny Wirtualnej z lokalnego środowiska na platformę Azure za pomocą dysku nieogólnionego)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Przygotowanie do wdrażania maszyny Wirtualnej z obrazem specyficznym dla klienta dla systemu SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Przygotowywanie maszyn wirtualnych za pomocą usługi SAP dla platformy Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Różnica między dyskiem platformy Azure a obrazem platformy Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Przekazywanie dysku twardego z lokalnego środowiska na platformę Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiowanie dysków między kontami usługi Azure Storage)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura VM/VHD dla wdrożeń SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Automatyczne ustawianie dla podłączonych dysków)
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Pojedyncza maszyna wirtualna z scenariuszem demonstracyjnym/szkoleniowym SAP NetWeaver)
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Pojęcia dotyczące wdrażania tylko w chmurze wystąpień SAP)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Rozwiązanie do monitorowania platformy Azure dla sap)
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Usługa Azure w magazynie w wersji premium)
[planning-guide-managed-disks]:planning-guide.md#c55b2c6e-3ca1-4476-be16-16c81927550f (Dyski zarządzane)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Sieć Platformy Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Magazyn: Magazyn Microsoft Azure i dyski danych)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktów SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Wdrażanie maszyn wirtualnych i zarządzanie nimi przy użyciu szablonów usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Maszyny wirtualne platformy Azure to rozwiązanie dla organizacji, które potrzebują zasobów obliczeniowych i magazynowych, w minimalnym czasie i bez długich cykli zaopatrzenia. Maszyny wirtualne platformy Azure umożliwiają wdrażanie klasycznych aplikacji, takich jak aplikacje oparte na sap NetWeaver, na platformie Azure. Rozszerzanie niezawodności i dostępności aplikacji bez dodatkowych zasobów lokalnych. Maszyny wirtualne platformy Azure obsługuje łączność między lokalizacjami, dzięki czemu można zintegrować maszyny wirtualne platformy Azure z domen lokalnych organizacji, chmur prywatnych i środowiska systemowego SAP.

W tym artykule omówimy kroki wdrażania aplikacji SAP na maszynach wirtualnych (VM) na platformie Azure, w tym alternatywne opcje wdrażania i rozwiązywanie problemów. W tym artykule oprzeć się na informacjach w [platformie Azure Virtual Machines planowania i implementacji dla SAP NetWeaver][planning-guide]. Uzupełnia również dokumentację instalacji SAP i sap notes, które są podstawowymi zasobami do instalowania i wdrażania oprogramowania SAP.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Konfigurowanie maszyny wirtualnej platformy Azure do wdrażania oprogramowania SAP wymaga wielu kroków i zasobów. Przed rozpoczęciem upewnij się, że spełniasz wymagania wstępne dotyczące instalowania oprogramowania SAP na maszynach wirtualnych na platformie Azure.

### <a name="local-computer"></a>Komputer lokalny

Aby zarządzać maszynami wirtualnymi z systemem Windows lub Linux, można użyć skryptu programu PowerShell i witryny Azure portal. W przypadku obu narzędzi potrzebny jest komputer lokalny z systemem Windows 7 lub nowszą wersją systemu Windows. Jeśli chcesz zarządzać tylko maszynami wirtualnymi z systemem Linux i chcesz użyć komputera z systemem Linux dla tego zadania, możesz użyć interfejsu wiersza polecenia platformy Azure.

### <a name="internet-connection"></a>Połączenie internetowe

Aby pobrać i uruchomić narzędzia i skrypty wymagane do wdrożenia oprogramowania SAP, musisz mieć połączenie z Internetem. Maszyna wirtualna platformy Azure, która jest uruchomiona rozszerzenie platformy Azure dla SAP również potrzebuje dostępu do Internetu. Jeśli maszyna wirtualna platformy Azure jest częścią sieci wirtualnej platformy Azure lub domeny lokalnej, upewnij się, że są ustawione odpowiednie ustawienia serwera proxy, zgodnie z [opisem w temacie Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subskrypcja platformy Microsoft Azure

Potrzebujesz aktywnego konta platformy Azure.

### <a name="topology-and-networking"></a>Topologia i tworzenie sieci

Należy zdefiniować topologię i architekturę wdrożenia SAP na platformie Azure:

* Konta magazynu platformy Azure, które mają być używane
* Sieć wirtualna, w której chcesz wdrożyć system SAP
* Grupa zasobów, do której chcesz wdrożyć system SAP
* Region platformy Azure, w którym chcesz wdrożyć system SAP
* Konfiguracja SAP (dwuwarstwowa lub trójwarstwowa)
* Rozmiary maszyn wirtualnych i liczba dodatkowych dysków danych, które mają być zainstalowane na maszynach wirtualnych
* Konfiguracja sap correction and transport system (CTS)

Tworzenie i konfigurowanie kont magazynu platformy Azure (jeśli jest to wymagane) lub sieci wirtualnych platformy Azure przed rozpoczęciem procesu wdrażania oprogramowania SAP. Aby uzyskać informacje dotyczące tworzenia i konfigurowania tych zasobów, zobacz [Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Rozmiar SAP

Poznaj następujące informacje dotyczące rozmiaru SAP:

* Przewidywane obciążenie SAP, na przykład za pomocą narzędzia SAP Quick Sizer i numeru SAP Application Performance Standard (SAPS)
* Wymagane zużycie zasobów procesora i pamięci systemu SAP
* Wymagane operacje wejścia/wyjścia (We/Wy) na sekundę
* Wymagana przepustowość sieci ewentualnej komunikacji między maszynami wirtualnymi na platformie Azure
* Wymagana przepustowość sieci między zasobami lokalnymi a wdrożonym na platformie Azure systemem SAP

### <a name="resource-groups"></a>Grupy zasobów

W usłudze Azure Resource Manager można używać grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Zasoby

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby SAP

Podczas konfigurowania wdrażania oprogramowania SAP potrzebne są następujące zasoby SAP:

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure

* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Uwaga SAP [1409604] ma wymaganą wersję agenta hosta SAP dla systemu Windows na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Sap Note [1984787] ma ogólne informacje na temat SUSE Linux Enterprise Server 12.
* Sap Note [2002167] ma ogólne informacje o Red Hat Enterprise Linux 7.x.
* Sap Note [2069760] ma ogólne informacje o Systemie Oracle Linux 7.x.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* Sap Note [1597355] ma ogólne informacje na temat przestrzeni wymiany dla Linuksa.
* [Strona SAP na platformie Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) zawiera wiadomości i zbiór przydatnych zasobów.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* Polecenia cmdlet programu PowerShell specyficzne dla systemu SAP, które są częścią [programu Azure PowerShell][azure-ps].
* Specyficzne dla systemu SAP polecenia interfejsu wiersza polecenia platformy Azure, które są częścią [interfejsu wiersza polecenia platformy Azure.][azure-cli]

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby systemu Windows

Te artykuły firmy Microsoft obejmują wdrożenia SAP na platformie Azure:

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla sap NetWeaver (ten artykuł)][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla usługi SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenariusze wdrażania oprogramowania SAP na maszynach wirtualnych platformy Azure

Masz wiele opcji wdrażania maszyn wirtualnych i skojarzonych dysków na platformie Azure. Ważne jest, aby zrozumieć różnice między opcjami wdrażania, ponieważ można podjąć różne kroki w celu przygotowania maszyn wirtualnych do wdrożenia na podstawie wybranego typu wdrożenia.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenariusz 1: Wdrażanie maszyny Wirtualnej z portalu Azure Marketplace dla systemu SAP

Można użyć obrazu dostarczonego przez firmę Microsoft lub przez firmę trzecią w portalu Azure Marketplace, aby wdrożyć maszynę wirtualną. Marketplace oferuje kilka standardowych obrazów systemu operacyjnego systemu Windows Server i różnych dystrybucji Linuksa. Można również wdrożyć obraz, który zawiera jednostki SKU systemu zarządzania bazami danych (DBMS), na przykład Microsoft SQL Server. Aby uzyskać więcej informacji na temat używania obrazów za pomocą jednostek SKU DBMS, zobacz [Wdrażanie DBMS maszyn wirtualnych platformy Azure dla sap NetWeaver][dbms-guide].

Poniższy schemat blokowy przedstawia sekwencję kroków specyficznych dla systemu SAP w celu wdrożenia maszyny Wirtualnej z portalu Azure Marketplace:

![Schemat blokowy wdrażania maszyny wirtualnej dla systemów SAP przy użyciu obrazu maszyny wirtualnej z portalu Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej przy użyciu portalu Azure

Najprostszym sposobem utworzenia nowej maszyny wirtualnej z obrazem z portalu Azure Marketplace jest korzystanie z witryny Azure portal.

1.  Przejdź do pozycji <https://portal.azure.com/#create/hub> (Plik > Nowy > Inny).  Lub w menu portalu Azure wybierz + **Nowy**.
1.  Wybierz **pozycję Oblicz**, a następnie wybierz typ systemu operacyjnego, który chcesz wdrożyć. Na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2. Domyślny widok listy nie pokazuje wszystkich obsługiwanych systemów operacyjnych. Wybierz **pozycję Zobacz wszystkie,** aby uzyskać pełną listę. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych do wdrażania oprogramowania SAP, zobacz UWAGA SAP [1928533].
1.  Na następnej stronie zapoznaj się z regulaminem.
1.  W polu **Wybierz model wdrożenia** wybierz pozycję Menedżer **zasobów**.
1.  Wybierz pozycję **Utwórz**.

Kreator prowadzi użytkownika przez ustawienie wymaganych parametrów do utworzenia maszyny wirtualnej, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów to:

1. **Podstawy**:
   * **Nazwa**: Nazwa zasobu (nazwa maszyny wirtualnej).
   * **Typ dysku maszyny Wirtualnej**: Wybierz typ dysku systemu operacyjnego. Jeśli chcesz używać magazynu w wersji Premium dla dysków z danymi, zalecamy użycie magazynu w wersji Premium również dla dysku systemu operacyjnego.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH:** Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi administracyjnej. W przypadku maszyny wirtualnej systemu Linux można wprowadzić klucz publicznej powłoki bezpiecznej (SSH), którego używasz do logowania się na komputerze.
   * **Subskrypcja:** Wybierz subskrypcję, której chcesz użyć do aprowizowania nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja:** Gdzie wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszynę wirtualną z siecią lokalną, upewnij się, że wybrałeś lokalizację sieci wirtualnej, która łączy platformę Azure z siecią lokalną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure networking][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide].
1. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyn wirtualnych, zobacz UWAGA SAP [1928533]. Upewnij się, że wybierzesz poprawny typ maszyny Wirtualnej, jeśli chcesz korzystać z usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują magazyn w wersji Premium. Aby uzyskać więcej informacji, zobacz [Magazyn: Magazyn Microsoft Azure i dyski danych][planning-guide-storage-microsoft-azure-storage-and-data-disks] oraz [usługa Azure Premium Storage][planning-guide-azure-premium-storage] w [usłudze Azure Virtual Machines planowania i implementacji sap NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku systemu operacyjnego. Jeśli chcesz używać magazynu w wersji Premium dla dysków z danymi, zalecamy użycie magazynu w wersji Premium również dla dysku systemu operacyjnego.
     * **Użyj dysków zarządzanych:** Jeśli chcesz używać dysków zarządzanych, wybierz pozycję Tak. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz rozdział [Dyski zarządzane][planning-guide-managed-disks] w przewodniku planowania.
     * **Konto magazynu:** Wybierz istniejące konto magazynu lub utwórz nowe. Nie wszystkie typy magazynu działają w przypadku uruchamiania aplikacji SAP. Aby uzyskać więcej informacji na temat typów magazynu, zobacz Struktura magazynu maszyny [Wirtualnej dla wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Sieć**
     * **Sieć wirtualna** i **podsieć:** Aby zintegrować maszynę wirtualną z intranetem, wybierz sieć wirtualną połączoną z siecią lokalną.
     * **Publiczny adres IP:** Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Publiczny adres IP umożliwia dostęp do maszyny wirtualnej za pośrednictwem Internetu. Upewnij się, że utworzysz również grupę zabezpieczeń sieci, aby ułatwić bezpieczny dostęp do maszyny wirtualnej.
     * **Grupa zabezpieczeń sieci:** Aby uzyskać więcej informacji, zobacz [Kontrolowanie przepływu ruchu sieciowego za pomocą sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia:** Rozszerzenia: Rozszerzenia maszyny wirtualnej można zainstalować, dodając je do wdrożenia. W tym kroku nie trzeba dodawać rozszerzeń. Rozszerzenia wymagane dla obsługi SAP są instalowane później. Zobacz rozdział [Konfigurowanie rozszerzenia platformy Azure dla sap][deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność:** Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [Zestawy dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu:** Można wybrać **opcję Wyłącz** dla diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: Można wybrać **opcję Wyłącz** do monitorowania diagnostyki.

1. **Streszczenie**:

   Przejrzyj wybrane opcje, a następnie wybierz **przycisk OK**.

Maszyna wirtualna jest wdrażana w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu

Maszynę wirtualną można utworzyć przy użyciu jednego z szablonów SAP opublikowanych w [repozytorium GitHub z szablonami szybki start platformy Azure][azure-quickstart-templates-github]. Maszynę wirtualną można również utworzyć ręcznie za pomocą [portalu Azure,][virtual-machines-windows-tutorial] [programu PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]lub [interfejsu wiersza polecenia platformy Azure.][virtual-machines-linux-tutorial]

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) — dyski zarządzane** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i dysków zarządzanych, użyj tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Aby utworzyć system trójwarstwowy przy użyciu wielu maszyn wirtualnych, użyj tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych) — dyski zarządzane** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Aby utworzyć system trójwarstwowy przy użyciu wielu maszyn wirtualnych i dysków zarządzanych, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrażania szablonu.
   * **Grupa zasobów:** Grupa zasobów do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w ramach subskrypcji.
   * **Lokalizacja:** Gdzie wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, zostanie użyta lokalizacja tej grupy zasobów.

1. **Ustawienia**:
   * **SAP System ID**: Identyfikator systemu SAP (SID).
   * **Typ systemu operacyjnego:** System operacyjny, który chcesz wdrożyć, na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) lub Oracle Linux 7.2.

     Widok listy nie pokazuje wszystkich obsługiwanych systemów operacyjnych. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych do wdrażania oprogramowania SAP, zobacz UWAGA SAP [1928533].
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba SAPS nowy system zapewnia. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
   * **Dostępność systemu** (tylko szablon trójwarstwowy): dostępność systemu.

     Wybierz **ha** dla konfiguracji, która jest odpowiednia dla instalacji o wysokiej dostępności. Two database servers and two servers for ABAP SAP Central Services (ASCS) are created two database servers and two servers for ABAP SAP Central Services (ASCS).
   * **Typ magazynu** (tylko szablon dwuwarstwowy): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure Premium SSD Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny Wirtualnej dla wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Magazyn w jakości Premium: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Nazwa użytkownika i** **hasło administratora**: nazwa użytkownika i hasło.
     Tworzony jest nowy użytkownik do logowania się do maszyny wirtualnej.
   * **Nowa lub istniejąca podsieć**: Określa, czy tworzona jest nowa sieć wirtualna i podsieć, czy używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz pozycję **Istniejąca**.
   * **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator&lt;zwykle wygląda następująco: /subscriptions/ subscription id>/resourceGroups/resource&lt;group name>/providers/Microsoft.Network/virtualNetworks/virtual&lt;network name>/subnets/subnets/subnets/subnets/subnets/&lt;nazwa podsieci>

1. **Regulamin**:  
    Przejrzyj i zaakceptuj warunki prawne.

1. Wybierz pozycję **Kup**.

Agent maszyny wirtualnej platformy Azure jest wdrażany domyślnie podczas korzystania z obrazu z portalu Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie być w stanie uzyskać dostępu do Internetu i nie będzie mogła pobrać wymaganych rozszerzeń maszyn wirtualnych ani zebrać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pośrednictwem rozszerzenia SAP dla platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Dołączanie do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym wystąpieniem usługi Active Directory lub DNS za pośrednictwem połączenia sieci VPN lokacja platformy Azure lub usługi ExpressRoute (jest to nazywane *międzylokacjowym* [planowaniem i implementacją maszyn wirtualnych dla usługi SAP NetWeaver),][planning-guide]oczekuje się, że maszyna wirtualna dołącza do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [Dołączanie maszyny Wirtualnej do domeny lokalnej (tylko system Windows).][deployment-guide-4.3]

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurowanie rozszerzenia maszyny Wirtualnej

Aby mieć pewność, że SAP obsługuje twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla SAP zgodnie [z opisem w temacie Konfigurowanie rozszerzenia platformy Azure dla sap.][deployment-guide-4.5] Sprawdź wymagania wstępne dla sap i wymagane minimalne wersje JĄdra SAP i SAP Host Agent, w zasobach wymienionych w [zasobach SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Rozszerzenie maszyny Wirtualnej do sprawdzania SAP

Sprawdź, czy rozszerzenie maszyny Wirtualnej dla SAP działa, zgodnie z opisem w [Dz.][deployment-guide-troubleshooting-chapter]

#### <a name="post-deployment-steps"></a>Kroki po wdrożeniu

Po utworzeniu maszyny Wirtualnej i maszyny Wirtualnej jest wdrażany, należy zainstalować wymagane składniki oprogramowania na maszynie Wirtualnej. Ze względu na sekwencję instalacji wdrażania/oprogramowania w tym typie wdrożenia maszyny Wirtualnej oprogramowanie, które ma zostać zainstalowane, musi być już dostępne na platformie Azure, na innej maszynie wirtualnej lub jako dysk, który można podłączyć. Można też rozważyć użycie scenariusza między lokalizacjami, w którym nałącza się łączność z zasobami lokalnymi (udziałami instalacyjnymi).

Po wdrożeniu maszyny Wirtualnej na platformie Azure postępuj zgodnie z tymi samymi wytycznymi i narzędziami, aby zainstalować oprogramowanie SAP na maszynie Wirtualnej, tak jak w środowisku lokalnym. Aby zainstalować oprogramowanie SAP na maszynie Wirtualnej platformy Azure, zarówno SAP, jak i firma Microsoft zalecają przekazywanie i przechowywanie nośnika instalacyjnego SAP na dyskach wirtualnych platformy Azure lub dysków zarządzanych lub utworzenie maszyny Wirtualnej platformy Azure, która działa jako serwer plików zawierający wszystkie wymagane nośniki instalacyjne SAP.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenariusz 2: Wdrażanie maszyny Wirtualnej z obrazem niestandardowym dla systemu SAP

Ponieważ różne wersje systemu operacyjnego lub systemu dbms mają różne wymagania dotyczące poprawek, obrazy, które można znaleźć w portalu Azure Marketplace, mogą nie spełniać Twoich potrzeb. Zamiast tego można utworzyć maszynę wirtualną przy użyciu własnego obrazu maszyny Wirtualnej systemu operacyjnego/dbms, który można wdrożyć ponownie później.
Aby utworzyć obraz prywatny dla systemu Linux, można użyć różnych kroków niż utworzyć go dla systemu Windows.

---
> ![Windows][Logo_Windows] Windows
>
> Aby przygotować obraz systemu Windows, którego można użyć do wdrożenia wielu maszyn wirtualnych, ustawienia systemu Windows (takie jak identyfikator SID systemu Windows i nazwa hosta) muszą być abstrakcyjne lub uogólnione na lokalnej maszynie wirtualnej. Możesz użyć [sysprep,](https://msdn.microsoft.com/library/hh825084.aspx) aby to zrobić.
>
> ![Linux][Logo_Linux] Linux
>
> Aby przygotować obraz systemu Linux, który służy do wdrażania wielu maszyn wirtualnych, niektóre ustawienia systemu Linux muszą być abstrakcyjne lub uogólnione na lokalnej maszynie wirtualnej. Można użyć, `waagent -deprovision` aby to zrobić. Aby uzyskać więcej informacji, zobacz [Przechwytywanie maszyny wirtualnej systemu Linux uruchomionej na platformie Azure][virtual-machines-linux-capture-image] i [podręczniku użytkownika agenta systemu Azure Linux.][virtual-machines-linux-agent-user-guide-command-line-options]
>
>

---
Można przygotować i utworzyć obraz niestandardowy, a następnie użyć go do utworzenia wielu nowych maszyn wirtualnych. Jest to opisane w [platformie Azure Virtual Machines planowania i implementacji dla SAP NetWeaver][planning-guide]. Skonfiguruj zawartość bazy danych za pomocą menedżera aprowizacji oprogramowania SAP w celu zainstalowania nowego systemu SAP (przywraca kopię zapasową bazy danych z dysku podłączonego do maszyny wirtualnej) lub bezpośrednio przywracając kopię zapasową bazy danych z magazynu platformy Azure, jeśli usługa dbms ją obsługuje. Aby uzyskać więcej informacji, zobacz [Wdrażanie usługi DBMS maszyn wirtualnych platformy Azure dla sap NetWeaver][dbms-guide]. Jeśli system SAP został już zainstalowany na lokalnej maszynie wirtualnej (szczególnie w przypadku systemów dwuwarstwowych), można dostosować ustawienia systemu SAP po wdrożeniu maszyny Wirtualnej platformy Azure przy użyciu procedury zmiany nazwy systemu obsługiwanej przez menedżera aprowizacji oprogramowania SAP (UWAGA SAP [1619720]). W przeciwnym razie można zainstalować oprogramowanie SAP po wdrożeniu maszyny Wirtualnej platformy Azure.

Poniższy schemat blokowy przedstawia sekwencję kroków specyficznych dla systemu SAP w celu wdrożenia maszyny Wirtualnej z obrazu niestandardowego:

![Schemat blokowy wdrażania maszyny Wirtualnej dla systemów SAP przy użyciu obrazu maszyny Wirtualnej w prywatnym portalu Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej przy użyciu portalu Azure

Najprostszym sposobem utworzenia nowej maszyny wirtualnej z obrazu dysku zarządzanego jest użycie witryny Azure portal. Aby uzyskać więcej informacji na temat tworzenia obrazu dysku zarządzania, przeczytaj [artykuł Przechwytywanie zarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)

1.  Przejdź do pozycji <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> (Plik > Nowy > Inny). Lub w menu portalu Azure wybierz pozycję **Obrazy**.
1.  Wybierz obraz dysku zarządzanego, który chcesz wdrożyć, i kliknij **pozycję Utwórz maszynę wirtualną**

Kreator prowadzi użytkownika przez ustawienie wymaganych parametrów do utworzenia maszyny wirtualnej, oprócz wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów to:

1. **Podstawy**:
   * **Nazwa**: Nazwa zasobu (nazwa maszyny wirtualnej).
   * **Typ dysku maszyny Wirtualnej**: Wybierz typ dysku systemu operacyjnego. Jeśli chcesz używać magazynu w wersji Premium dla dysków z danymi, zalecamy użycie magazynu w wersji Premium również dla dysku systemu operacyjnego.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH:** Wprowadź nazwę użytkownika i hasło użytkownika, który jest tworzony podczas inicjowania obsługi administracyjnej. W przypadku maszyny wirtualnej systemu Linux można wprowadzić klucz publicznej powłoki bezpiecznej (SSH), którego używasz do logowania się na komputerze.
   * **Subskrypcja:** Wybierz subskrypcję, której chcesz użyć do aprowizowania nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów maszyny Wirtualnej. Można wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja:** Gdzie wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszynę wirtualną z siecią lokalną, upewnij się, że wybrałeś lokalizację sieci wirtualnej, która łączy platformę Azure z siecią lokalną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure networking][planning-guide-microsoft-azure-networking] in [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide].
1. **Rozmiar**:

     Aby uzyskać listę obsługiwanych typów maszyn wirtualnych, zobacz UWAGA SAP [1928533]. Upewnij się, że wybierzesz poprawny typ maszyny Wirtualnej, jeśli chcesz korzystać z usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują magazyn w wersji Premium. Aby uzyskać więcej informacji, zobacz [Magazyn: Magazyn Microsoft Azure i dyski danych][planning-guide-storage-microsoft-azure-storage-and-data-disks] oraz [usługa Azure Premium Storage][planning-guide-azure-premium-storage] w [usłudze Azure Virtual Machines planowania i implementacji sap NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku systemu operacyjnego. Jeśli chcesz używać magazynu w wersji Premium dla dysków z danymi, zalecamy użycie magazynu w wersji Premium również dla dysku systemu operacyjnego.
     * **Użyj dysków zarządzanych:** Jeśli chcesz używać dysków zarządzanych, wybierz pozycję Tak. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz rozdział [Dyski zarządzane][planning-guide-managed-disks] w przewodniku planowania.
   * **Sieć**
     * **Sieć wirtualna** i **podsieć:** Aby zintegrować maszynę wirtualną z intranetem, wybierz sieć wirtualną połączoną z siecią lokalną.
     * **Publiczny adres IP:** Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Publiczny adres IP umożliwia dostęp do maszyny wirtualnej za pośrednictwem Internetu. Upewnij się, że utworzysz również grupę zabezpieczeń sieci, aby ułatwić bezpieczny dostęp do maszyny wirtualnej.
     * **Grupa zabezpieczeń sieci:** Aby uzyskać więcej informacji, zobacz [Kontrolowanie przepływu ruchu sieciowego za pomocą sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia:** Rozszerzenia: Rozszerzenia maszyny wirtualnej można zainstalować, dodając je do wdrożenia. W tym kroku nie trzeba dodawać rozszerzenia. Rozszerzenia wymagane dla obsługi SAP są instalowane później. Zobacz rozdział [Konfigurowanie rozszerzenia platformy Azure dla sap][deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność:** Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [Zestawy dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu:** Można wybrać **opcję Wyłącz** dla diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: Można wybrać **opcję Wyłącz** do monitorowania diagnostyki.

1. **Streszczenie**:

   Przejrzyj wybrane opcje, a następnie wybierz **przycisk OK**.

Maszyna wirtualna jest wdrażana w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu

Aby utworzyć wdrożenie przy użyciu obrazu prywatnego systemu operacyjnego z witryny Azure portal, należy użyć jednego z następujących szablonów SAP. Szablony te są publikowane w [repozytorium GitHub szablonów szybki start.][azure-quickstart-templates-github] Można również ręcznie utworzyć maszynę wirtualną za pomocą programu [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) — obraz dysku zarządzanego** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i obrazu dysku zarządzanego, użyj tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych)** (sap-3-warstwowy obraz użytkownika)][sap-templates-3-tier-user-image]

  Aby utworzyć system trójwarstwowy przy użyciu wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego, użyj tego szablonu.
* [**Szablon konfiguracji trójwarstwowej (wiele maszyn wirtualnych) — obraz dysku zarządzanego** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Aby utworzyć system trójwarstwowy przy użyciu wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego i obrazu dysku zarządzanego, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrażania szablonu.
   * **Grupa zasobów:** Grupa zasobów do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w ramach subskrypcji.
   * **Lokalizacja:** Gdzie wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, zostanie użyta lokalizacja tej grupy zasobów.
1. **Ustawienia**:
   * **SAP ID systemu:** Identyfikator systemu SAP.
   * **Typ systemu operacyjnego:** Typ systemu operacyjnego, który chcesz wdrożyć (Windows lub Linux).
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba SAPS nowy system zapewnia. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
   * **Dostępność systemu** (tylko szablon trójwarstwowy): dostępność systemu.

     Wybierz **ha** dla konfiguracji, która jest odpowiednia dla instalacji o wysokiej dostępności. Two database servers and two servers for ASCS are created two database servers and two servers for ASCS are created two database servers and two servers for ASCS.
   * **Typ magazynu** (tylko szablon dwuwarstwowy): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure Premium SSD Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny Wirtualnej dla wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Magazyn w wersji Premium: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Identyfikator URI VHD obrazu użytkownika** (tylko szablon obrazu dysku niezarządzanego): identyfikator URI prywatnego&lt;obrazu systemu operacyjnego VHD, na przykład https:// accountname>.blob.core.windows.net/vhds/userimage.vhd.
   * **Konto magazynu obrazów użytkownika** (tylko szablon obrazu dysku niezarządzanego): nazwa konta magazynu, na &lt;którym jest przechowywany prywatny obraz&lt;systemu operacyjnego, na przykład nazwa konta> w https:// nazwie konta>.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (tylko szablon obrazu dysku zarządzanego): identyfikator obrazu dysku zarządzanego, którego chcesz użyć
   * **Nazwa użytkownika i** **hasło administratora**: Nazwa użytkownika i hasło.

     Tworzony jest nowy użytkownik do logowania się do maszyny wirtualnej.
   * **Nowa lub istniejąca podsieć**: Określa, czy tworzona jest nowa sieć wirtualna i podsieć, czy używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz pozycję **Istniejąca**.
   * **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator&lt;zwykle wygląda następująco: /subscriptions/ subscription id>/resourceGroups/resource&lt;group name>/providers/Microsoft.Network/virtualNetworks/virtual&lt;network name>/subnets/subnets/subnets/subnets/subnets/&lt;nazwa podsieci>

1. **Regulamin**:  
    Przejrzyj i zaakceptuj warunki prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalowanie agenta maszyn wirtualnych (tylko w systemie Linux)

Aby użyć szablonów opisanych w poprzedniej sekcji, agent systemu Linux musi być już zainstalowany w obrazie użytkownika lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej w obrazie użytkownika, zgodnie z opisem w [obszarze Pobierz, zainstaluj i włącz agenta maszyny Wirtualnej platformy Azure][deployment-guide-4.4]. Jeśli nie używasz szablonów, możesz również zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Dołączanie do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym wystąpieniem usługi Active Directory lub DNS za pośrednictwem połączenia sieci VPN lokacja platformy Azure lub usługi Azure ExpressRoute (jest to nazywane *międzylokacjowym* [planowaniem i implementacją maszyn wirtualnych dla usługi SAP NetWeaver),][planning-guide]oczekuje się, że maszyna wirtualna dołącza do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego kroku, zobacz [Dołączanie maszyny Wirtualnej do domeny lokalnej (tylko system Windows).][deployment-guide-4.3]

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie mieć dostępu do Internetu i nie będzie mogła pobrać wymaganych rozszerzeń maszyn wirtualnych ani zebrać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pośrednictwem rozszerzenia SAP dla platformy Azure, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurowanie rozszerzenia maszyny wirtualnej platformy Azure dla systemu SAP

Aby mieć pewność, że SAP obsługuje twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla SAP zgodnie [z opisem w temacie Konfigurowanie rozszerzenia platformy Azure dla sap.][deployment-guide-4.5] Sprawdź wymagania wstępne dla sap i wymagane minimalne wersje JĄdra SAP i SAP Host Agent, w zasobach wymienionych w [zasobach SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Sprawdzanie rozszerzenia sap VM

Sprawdź, czy rozszerzenie maszyny Wirtualnej dla SAP działa, zgodnie z opisem w [Dz.][deployment-guide-troubleshooting-chapter]


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenariusz 3: Przenoszenie lokalnej maszyny Wirtualnej przy użyciu nieogólnionej dysku twardego platformy Azure z sap

W tym scenariuszu planujesz przenieść określony system SAP ze środowiska lokalnego na platformę Azure. Można to zrobić, przekazując vhd, który ma system operacyjny, pliki binarne SAP i ostatecznie pliki binarne DBMS, a także vhds z plikami danych i dziennika systemu dbms, do platformy Azure. W przeciwieństwie do scenariusza opisanego w [scenariuszu 2: Wdrażanie maszyny Wirtualnej z obrazem niestandardowym dla sap][deployment-guide-3.3], w tym przypadku zachować hostname, SAP SID i SAP kont użytkowników w maszynie Wirtualnej platformy Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. Nie trzeba uogólniać systemu operacyjnego. W tym scenariuszu stosuje się najczęściej do scenariuszy między lokalnymi, w których część środowiska SAP działa lokalnie, a część jest uruchamiana na platformie Azure.

W tym scenariuszu agent maszyny Wirtualnej **nie** jest automatycznie instalowany podczas wdrażania. Ponieważ agent maszyny Wirtualnej i rozszerzenie platformy Azure dla sap są wymagane do uruchamiania SAP NetWeaver na platformie Azure, należy pobrać, zainstalować i włączyć oba składniki ręcznie po utworzeniu maszyny wirtualnej.

Aby uzyskać więcej informacji na temat agenta maszyny wirtualnej platformy Azure, zobacz następujące zasoby.

---
> ![Windows][Logo_Windows] Windows
>
> [Usługa Azure Virtual Machine Agent — omówienie][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux][virtual-machines-linux-agent-user-guide]
>
>

---

Poniższy schemat blokowy przedstawia sekwencję kroków przenoszenia lokalnej maszyny Wirtualnej przy użyciu nieogólnionego dysku wirtualnego platformy Azure:

![Schemat blokowy wdrażania maszyny Wirtualnej dla systemów SAP przy użyciu dysku maszyny Wirtualnej][deployment-guide-figure-400]

Jeśli dysk jest już przekazany i zdefiniowany na platformie Azure (zobacz [planowanie i implementacja maszyn wirtualnych platformy Azure dla SAP NetWeaver),][planning-guide]wykonaj zadania opisane w następnych kilku sekcjach.

#### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć wdrożenie przy użyciu prywatnego dysku systemu operacyjnego za pośrednictwem portalu Azure, użyj szablonu SAP opublikowanego w [repozytorium GitHub szablonów szybki startu platformy Azure.][azure-quickstart-templates-github] Można również ręcznie utworzyć maszynę wirtualną przy użyciu programu PowerShell.

* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Szablon konfiguracji dwuwarstwowej (tylko jedna maszyna wirtualna) — dysk zarządzany** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i dysku zarządzanego, użyj tego szablonu.

W witrynie Azure portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrażania szablonu.
   * **Grupa zasobów:** Grupa zasobów do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w ramach subskrypcji.
   * **Lokalizacja:** Gdzie wdrożyć szablon. Jeśli wybrano istniejącą grupę zasobów, zostanie użyta lokalizacja tej grupy zasobów.
1. **Ustawienia**:
   * **SAP ID systemu:** Identyfikator systemu SAP.
   * **Typ systemu operacyjnego:** Typ systemu operacyjnego, który chcesz wdrożyć (Windows lub Linux).
   * **Rozmiar systemu SAP**: Rozmiar systemu SAP.

     Liczba SAPS nowy system zapewnia. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
   * **Typ magazynu** (tylko szablon dwuwarstwowy): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynu, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure Premium SSD Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny Wirtualnej dla wdrożeń RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Magazyn w jakości Premium: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Identyfikator URI dysku systemu operacyjnego VHD** (tylko szablon dysku niezarządzanego): identyfikator URI&lt;prywatnego dysku systemu operacyjnego, na przykład https:// accountname>.blob.core.windows.net/vhds/osdisk.vhd.
   * **Identyfikator dysku systemu operacyjnego Zarządzany dysk** (tylko szablon dysku zarządzanego): identyfikator dysku zarządzanego systemu operacyjnego dysku, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **Nowa lub istniejąca podsieć**: Określa, czy tworzona jest nowa sieć wirtualna i podsieć, czy też używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną połączoną z siecią lokalną, wybierz pozycję **Istniejąca**.
   * **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator&lt;zwykle wygląda następująco: /subscriptions/ subscription id>/resourceGroups/resource&lt;group name>/providers/Microsoft.Network/virtualNetworks/virtual&lt;network name>/subnets/subnets/subnets/subnets/subnets/&lt;nazwa podsieci>

1. **Regulamin**:  
    Przejrzyj i zaakceptuj warunki prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent"></a>Instalowanie agenta maszyny Wirtualnej

Aby użyć szablonów opisanych w poprzedniej sekcji, agent maszyny Wirtualnej musi być zainstalowany na dysku systemu operacyjnego lub wdrożenie zakończy się niepowodzeniem. Pobierz i zainstaluj agenta maszyny Wirtualnej na maszynie Wirtualnej, zgodnie z opisem w [obszarze Pobierz, zainstaluj i włącz agenta maszyny Wirtualnej platformy Azure][deployment-guide-4.4].

Jeśli nie używasz szablonów opisanych w poprzedniej sekcji, można również zainstalować agenta maszyny Wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Dołączanie do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym wystąpieniem usługi Active Directory lub DNS za pośrednictwem połączenia sieci VPN lokacja platformy Azure lub usługi ExpressRoute (jest to nazywane *międzylokacjowym* [planowaniem i implementacją maszyn wirtualnych dla usługi SAP NetWeaver),][planning-guide]oczekuje się, że maszyna wirtualna dołącza do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [Dołączanie maszyny Wirtualnej do domeny lokalnej (tylko system Windows).][deployment-guide-4.3]

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub usługi ExpressRoute, maszyna wirtualna może nie mieć dostępu do Internetu i nie będzie mogła pobrać wymaganych rozszerzeń maszyn wirtualnych ani zebrać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pośrednictwem rozszerzenia SAP dla platformy Azure, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurowanie rozszerzenia maszyny wirtualnej platformy Azure dla systemu SAP

Aby mieć pewność, że SAP obsługuje twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla SAP zgodnie [z opisem w temacie Konfigurowanie rozszerzenia platformy Azure dla sap.][deployment-guide-4.5] Sprawdź wymagania wstępne dla sap i wymagane minimalne wersje JĄdra SAP i SAP Host Agent, w zasobach wymienionych w [zasobach SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Sprawdzanie maszyny wirtualnej SAP

Sprawdź, czy rozszerzenie maszyny Wirtualnej dla sap działa, zgodnie z opisem w [Czeki i rozwiązywanie problemów z kompleksowym zbieraniem danych dla sap host agenta.][deployment-guide-troubleshooting-chapter]

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualizowanie konfiguracji rozszerzenia platformy Azure dla systemu SAP

Zaktualizuj konfigurację rozszerzenia platformy Azure dla sap w dowolnym z następujących scenariuszy:
* Wspólny zespół firmy Microsoft/SAP rozszerza możliwości rozszerzenia maszyny Wirtualnej i żąda więcej lub mniej liczników.
* Firma Microsoft wprowadza nową wersję podstawowej infrastruktury platformy Azure, która dostarcza dane, a rozszerzenie platformy Azure dla sap musi zostać dostosowane do tych zmian.
* Montujesz dodatkowe dyski danych na maszynie wirtualnej platformy Azure lub usuwasz dysk danych. W tym scenariuszu zaktualizuj zbieranie danych związanych z magazynem. Zmiana konfiguracji przez dodanie lub usunięcie punktów końcowych lub przypisanie adresów IP do maszyny Wirtualnej nie wpływa na konfigurację rozszerzenia.
* Można zmienić rozmiar maszyny Wirtualnej platformy Azure, na przykład z rozmiaru A5 do innego rozmiaru maszyny Wirtualnej.
* Dodajesz nowe interfejsy sieciowe do maszyny Wirtualnej platformy Azure.

Aby zaktualizować ustawienia, zaktualizuj konfigurację rozszerzenia Platformy Azure dla sap, wykonując kroki opisane w [temacie Konfigurowanie rozszerzenia platformy Azure dla systemu SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Szczegółowe zadania związane z wdrażaniem oprogramowania SAP

W tej sekcji przedstawiono szczegółowe kroki wykonywania określonych zadań w procesie konfiguracji i wdrażania.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Wdrażanie poleceń cmdlet programu Azure PowerShell

1. Przejdź do programu [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. W obszarze **Narzędzia wiersza polecenia**w obszarze **Program PowerShell**wybierz pozycję **Zainstaluj system Windows**.
1. W oknie dialogowym Menedżer pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzurePowershellGet.3f.3f.3f.3fnew.exe) wybierz opcję **Uruchom**.
1. Aby uruchomić Instalator Microsoft Web Platform (Microsoft Web PI), wybierz opcję **Tak**.
1. Pojawi się strona, która wygląda następująco:

   ![Strona instalacji poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Wybierz **pozycję Zainstaluj**, a następnie zaakceptuj Postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
1. Program PowerShell jest zainstalowany. Wybierz **pozycję Zakończ,** aby zamknąć kreatora instalacji.

Często sprawdzaj dostępność aktualizacji poleceń cmdlet programu PowerShell, które zwykle są aktualizowane co miesiąc. Najprostszym sposobem sprawdzenia dostępności aktualizacji jest wykonać poprzednie kroki instalacji, aż do strony instalacji pokazanej w kroku 5. Data wydania i numer wydania poleceń cmdlet są zawarte na stronie pokazanej w kroku 5. O ile nie określono inaczej w sap note [1928533] lub SAP Note [2015553], zaleca się pracę z najnowszą wersją poleceń cmdlet programu Azure PowerShell.

Aby sprawdzić wersję poleceń cmdlet programu Azure PowerShell zainstalowanych na komputerze, uruchom to polecenie programu PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

Wynik wygląda następująco:

![Wynik sprawdzania wersji polecenia cmdlet programu Azure PowerShell][deployment-guide-figure-600]
<a name="figure-6"></a>

Jeśli wersja polecenia cmdlet platformy Azure zainstalowana na komputerze jest bieżącą wersją, pierwsza strona kreatora instalacji wskazuje ją przez dodanie **(Zainstalowane)** do tytułu produktu (zobacz poniższy zrzut ekranu). Polecenia cmdlet platformy PowerShell Azure są aktualne. Aby zamknąć kreatora instalacji, wybierz pozycję **Zakończ**.

![Strona instalacji poleceń cmdlet programu Azure PowerShell wskazujących, że zainstalowano najnowszą wersję poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-700]
<a name="figure-7"></a>

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Wdrażanie interfejsu wiersza polecenia platformy Azure

1. Przejdź do programu [Microsoft Azure Downloads](https://azure.microsoft.com/downloads/).
1. W obszarze **Narzędzia wiersza polecenia**w obszarze **Interfejs wiersza polecenia platformy Azure**wybierz łącze **Zainstaluj** dla swojego systemu operacyjnego.
1. W oknie dialogowym Menedżer pobierania firmy Microsoft dla pobranego pliku (na przykład WindowsAzureXPlatCLI.3f.3f.3f.3fnew.exe) wybierz opcję **Uruchom**.
1. Aby uruchomić Instalator Microsoft Web Platform (Microsoft Web PI), wybierz opcję **Tak**.
1. Pojawi się strona, która wygląda następująco:

   ![Strona instalacji poleceń cmdlet programu Azure PowerShell][deployment-guide-figure-500]<a name="figure-5"></a>

1. Wybierz **pozycję Zainstaluj**, a następnie zaakceptuj Postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
1. Jest zainstalowany plik CLI platformy Azure. Wybierz **pozycję Zakończ,** aby zamknąć kreatora instalacji.

Często sprawdzaj dostępność aktualizacji interfejsu wiersza polecenia platformy Azure, która zwykle jest aktualizowana co miesiąc. Najprostszym sposobem sprawdzenia dostępności aktualizacji jest wykonać poprzednie kroki instalacji, aż do strony instalacji pokazanej w kroku 5.

Aby sprawdzić wersję interfejsu wiersza polecenia platformy Azure zainstalowaną na komputerze, uruchom to polecenie:

```console
azure --version
```

Wynik wygląda następująco:

![Wynik sprawdzania wersji interfejsu wiersza polecenia platformy Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Dołączanie maszyny Wirtualnej do domeny lokalnej (tylko system Windows)

Jeśli wdrożysz maszyny wirtualne SAP w scenariuszu międzylokacyjnym, w którym lokalna usługa Active Directory i dns są rozszerzane na platformie Azure, oczekuje się, że maszyny wirtualne dołączają do domeny lokalnej. Szczegółowe kroki, które należy wykonać, aby dołączyć do maszyny Wirtualnej do domeny lokalnej i dodatkowe oprogramowanie wymagane do członkostwa w domenie lokalnej, zależy od klienta. Zazwyczaj, aby dołączyć do maszyny Wirtualnej do domeny lokalnej, należy zainstalować dodatkowe oprogramowanie, takie jak oprogramowanie ochrony przed złośliwym oprogramowaniem oraz oprogramowanie do tworzenia kopii zapasowych lub monitorowania.

W tym scenariuszu należy również upewnić się, że jeśli ustawienia serwera proxy internetowego są wymuszone, gdy maszyna wirtualna dołącza do domeny w twoim środowisku, lokalne konto systemu Windows (S-1-5-18) w maszynie wirtualnej gościa ma takie same ustawienia serwera proxy. Najprostszą opcją jest wymuszenie serwera proxy przy użyciu zasad grupy domeny, która ma zastosowanie do systemów w domenie.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Pobieranie, instalowanie i włączanie agenta maszyny wirtualnej platformy Azure

W przypadku maszyn wirtualnych, które są wdrażane z obrazu systemu operacyjnego, który nie jest uogólniony (na przykład obraz, który nie pochodzi z narzędzia Przygotowywanie systemu Windows lub narzędzia sysprep, należy ręcznie pobrać, zainstalować i włączyć agenta maszyny wirtualnej platformy Azure.

Jeśli wdrożysz maszynę wirtualną z portalu Azure Marketplace, ten krok nie jest wymagany. Obrazy z portalu Azure Marketplace mają już agenta maszyny wirtualnej platformy Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Pobierz agenta maszyny wirtualnej platformy Azure:
   1.  Pobierz [pakiet instalatora usługi Azure VM Agent](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Pakiet MSI agenta maszyny Wirtualnej jest przechowywany lokalnie na komputerze lub serwerze osobistym.
1. Zainstaluj agenta maszyny wirtualnej platformy Azure:
   1.  Połącz się z wdrożoną maszyną wirtualną platformy Azure przy użyciu protokołu RDP (Remote Desktop Protocol).
   1.  Otwórz okno Eksploratora Windows na maszynie Wirtualnej i wybierz katalog docelowy dla pliku MSI agenta maszyny Wirtualnej.
   1.  Przeciągnij plik MSI instalatora agenta platformy Azure z komputera/serwera lokalnego do katalogu docelowego agenta maszyny Wirtualnej na maszynie wirtualnej.
   1.  Kliknij dwukrotnie plik MSI na maszynie wirtualnej.
1. W przypadku maszyn wirtualnych, które są przyłączone do domen lokalnych, upewnij się, że ewentualne ustawienia serwera proxy internetowego mają zastosowanie również do konta systemu windows local system (S-1-5-18) na maszynie wirtualnej, zgodnie z opisem w [temacie Konfigurowanie serwera proxy][deployment-guide-configure-proxy]. Agent maszyny Wirtualnej działa w tym kontekście i musi mieć możliwość nawiązania połączenia z platformą Azure.

Do aktualizacji agenta maszyny wirtualnej platformy Azure nie jest wymagana żadna interakcja z użytkownikiem. Agent maszyny Wirtualnej jest automatycznie aktualizowany i nie wymaga ponownego uruchomienia maszyny Wirtualnej.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Użyj następujących poleceń, aby zainstalować agenta maszyny Wirtualnej dla systemu Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) lub Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Jeśli agent jest już zainstalowany, aby zaktualizować agenta systemu Azure Linux, wykonaj kroki opisane w [aktualizacji agenta systemu Azure Linux na maszynie Wirtualnej do najnowszej wersji z GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurowanie serwera proxy

Kroki podejmowane w celu skonfigurowania serwera proxy w systemie Windows różnią się od sposobu konfigurowania serwera proxy w systemie Linux.

#### <a name="windows"></a>Windows

Aby uzyskać dostęp do Internetu, należy poprawnie skonfigurować ustawienia serwera proxy. Jeśli ustawienia serwera proxy nie są ustawiane przez zasady grupy, można skonfigurować ustawienia konta system lokalny.

1. Przejdź do **ekranu startowego**, wprowadź **plik gpedit.msc**, a następnie wybierz pozycję **Enter**.
1. Wybierz pozycję**Szablony administracyjne** >  **konfiguracji** > komputera**Składniki** > systemu Windows**Internet Explorer**. Upewnij się, że ustawienie Umożliwia wyłączenie lub nieskonfigurowanie **ustawień serwera proxy na komputerze (a nie na użytkownika).**
1. W **Panelu sterowania**przejdź do opcji**internetowych**Centrum sieci **i udostępniania** > .
1. Na karcie **Połączenia** wybierz przycisk **Ustawienia sieci LAN.**
1. Wyczyść pole wyboru **Automatycznie wykryj ustawienia**.
1. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN,** a następnie wprowadź adres i port serwera proxy.
1. Wybierz przycisk **Zaawansowane.**
1. W polu **Wyjątki** wprowadź adres IP **168.63.129.16**. Kliknij przycisk **OK**.

#### <a name="linux"></a>Linux

Skonfiguruj poprawny serwer proxy w pliku konfiguracyjnym agenta gościa platformy Microsoft Azure, który znajduje się pod adresem \\etc\\waagent.conf.

Ustaw następujące parametry:

1. **Host serwera proxy HTTP**. Na przykład ustaw go na **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Port serwera proxy HTTP**. Na przykład ustaw go na **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Uruchom ponownie agenta.

   ```console
   sudo service waagent restart
   ```

Ustawienia serwera \\proxy\\w etc waagent.conf dotyczą również wymaganych rozszerzeń maszyn wirtualnych. Jeśli chcesz użyć repozytoriów platformy Azure, upewnij się, że ruch do tych repozytoriów nie przechodzi przez lokalny intranet. Jeśli utworzono trasy zdefiniowane przez użytkownika, aby włączyć wymuszone tunelowanie, upewnij się, że dodano trasę, która kieruje ruch do repozytoriów bezpośrednio do Internetu, a nie za pośrednictwem połączenia sieci VPN lokacja-lokacja.

* **SLES**

  Należy również dodać trasy dla adresów \\\\IP wymienionych w etc regionserverclnt.cfg. Na poniższym rysunku przedstawiono przykład:

  ![Wymuszone tunelowanie][deployment-guide-figure-50]


* **RHEL**

  Należy również dodać trasy dla adresów IP \\hostów wymienionych w\\etc\\yum.repos.d rhui-load-balancers. Na przykład zobacz poprzednią ilustrację.

* **Oracle Linux**

  Nie ma żadnych repozytoriów dla systemu Oracle Linux na platformie Azure. Musisz skonfigurować własne repozytoria dla Oracle Linux lub użyć publicznych repozytoriów.

Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika i przekazywanie adresów IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurowanie rozszerzenia platformy Azure dla systemu SAP

Po przygotowaniu maszyny Wirtualnej zgodnie z opisem w [scenariuszach wdrażania maszyn wirtualnych dla sap na platformie Azure][deployment-guide-3]agent maszyny wirtualnej jest zainstalowany na maszynie wirtualnej. Następnym krokiem jest wdrożenie rozszerzenia azure dla SAP, który jest dostępny w repozytorium rozszerzenia platformy Azure w globalnych centrach danych platformy Azure. Aby uzyskać więcej informacji, zobacz [Planowanie i implementacja maszyn wirtualnych platformy Azure dla sap NetWeaver][planning-guide-9.1].

Za pomocą programu PowerShell lub narzędzia Azure CLI można zainstalować i skonfigurować rozszerzenie platformy Azure dla systemu SAP. Aby zainstalować rozszerzenie na maszynie wirtualnej z systemem Windows lub Linux przy użyciu komputera z systemem Windows, zobacz [Azure PowerShell][deployment-guide-4.5.1]. Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Linux przy użyciu pulpitu systemu Linux, zobacz [interfejs wiersza polecenia platformy Azure][deployment-guide-4.5.2].

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Maszyny wirtualne programu Azure PowerShell dla systemów Linux i Windows

Aby zainstalować rozszerzenie platformy Azure dla sap przy użyciu programu PowerShell:

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Wdrażanie poleceń cmdlet programu Azure PowerShell][deployment-guide-4.1].  
1. Uruchom następujące polecenie programu PowerShell.
    Aby uzyskać listę dostępnych środowisk, uruchom program `commandlet Get-AzEnvironment`. Jeśli chcesz korzystać z globalnej platformy Azure, twoim środowiskiem jest **AzureCloud**. W przypadku platformy Azure w Chinach wybierz pozycję **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po wprowadzeniu danych konta i zidentyfikowaniu maszyny wirtualnej platformy Azure skrypt wdraża wymagane rozszerzenia i włącza wymagane funkcje. Może to potrwać kilka minut.
Aby uzyskać `Set-AzVMAEMExtension`więcej informacji na temat , zobacz [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Pomyślne wykonanie narzędzia cmdlet platformy Azure specyficzne dla platformy SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

Konfiguracja `Set-AzVMAEMExtension` wykonuje wszystkie kroki, aby skonfigurować zbieranie danych hosta dla sap.

Dane wyjściowe skryptu zawierają następujące informacje:

* Potwierdzenie, że skonfigurowano zbieranie danych dla dysku systemu operacyjnego i wszystkich dodatkowych dysków z danymi.
* Następne dwa komunikaty potwierdzają konfigurację metryk magazynu dla określonego konta magazynu.
* Jeden wiersz danych wyjściowych daje stan rzeczywistej aktualizacji rozszerzenia maszyny Wirtualnej dla konfiguracji SAP.
* Inny wiersz danych wyjściowych potwierdza, że konfiguracja została wdrożona lub zaktualizowana.
* Ostatnia linia danych wyjściowych jest informacyjna. Pokazuje opcje testowania rozszerzenia maszyny Wirtualnej dla konfiguracji SAP.
* Aby sprawdzić, czy wszystkie kroki rozszerzenia maszyny wirtualnej platformy Azure dla konfiguracji SAP zostały pomyślnie wykonane i czy infrastruktura platformy Azure dostarcza niezbędnych danych, należy wykonać sprawdzanie gotowości dla rozszerzenia Azure dla SAP, zgodnie z opisem w [obszarze Sprawdzanie gotowości dla usługi Azure Extension dla SAP.][deployment-guide-5.1]
* Poczekaj 15-30 minut, aby usługa Azure Diagnostics zebrała odpowiednie dane.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Maszyny wirtualne interfejsu wiersza polecenia platformy Azure dla systemu Linux

Aby zainstalować rozszerzenie platformy Azure dla sap przy użyciu interfejsu wiersza polecenia platformy Azure:

   1. Zainstaluj klasyczną platformę cli platformy Azure, zgodnie [z opisem][azure-cli]w install the Azure classic CLI .
   1. Zaloguj się za pomocą konta platformy Azure:

      ```console
      azure login
      ```

   1. Przełącz się do trybu usługi Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Włącz rozszerzenie platformy Azure dla sap:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalowanie przy użyciu interfejsu wiersza polecenia platformy Azure 2.0

   1. Zainstaluj narzędzie CLI platformy Azure 2.0, zgodnie z opisem w [install Azure CLI 2.0][azure-cli-2].
   1. Zaloguj się za pomocą konta platformy Azure:

      ```azurecli
      az login
      ```

   1. Instalowanie rozszerzenia AEM interfejsu WIERSZA polecenia platformy Azure
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Zainstaluj rozszerzenie za pomocą
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Sprawdź, czy rozszerzenie platformy Azure dla sap jest aktywny na maszynie wirtualnej systemu Azure Linux. Sprawdź, czy \\\\plik\\var lib AzureEnhancedMonitor\\PerfCounters istnieje. Jeśli istnieje, w wierszu polecenia uruchom to polecenie, aby wyświetlić informacje zebrane przez rozszerzenie platformy Azure dla sap:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Dane wyjściowe wyglądają następująco:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="checks-and-troubleshooting-for-end-to-end-data-collection-for-sap-host-agent"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Sprawdzanie i rozwiązywanie problemów z kompleksowym zbieraniem danych dla agenta hosta SAP

Po wdrożeniu maszyny Wirtualnej platformy Azure i skonfigurowaniu odpowiedniego rozszerzenia platformy Azure dla sap sprawdź, czy wszystkie składniki rozszerzenia działają zgodnie z oczekiwaniami.

Uruchom sprawdzanie gotowości dla rozszerzenia platformy Azure dla SAP, zgodnie z opisem w [czeku gotowości dla rozszerzenia platformy Azure dla SAP.][deployment-guide-5.1] Jeśli wszystkie wyniki sprawdzania gotowości są dodatnie i wszystkie odpowiednie liczniki wydajności są wyświetlane OK, rozszerzenie platformy Azure dla SAP zostało pomyślnie skonfigurowane. Instalację agenta hosta SAP można kontynuować zgodnie z opisem w zasobach [SAP][deployment-guide-2.2]Notes. Jeśli sprawdzanie gotowości wskazuje, że liczniki brakuje, uruchom sprawdzanie kondycji rozszerzenia platformy Azure dla SAP, zgodnie z opisem w [health check for Azure Extension dla konfiguracji SAP.][deployment-guide-5.2] Aby uzyskać więcej opcji rozwiązywania problemów, zobacz [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla sap][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Sprawdzanie gotowości dla rozszerzenia platformy Azure dla SAP

Ta kontrola zapewnia, że wszystkie metryki wydajności, które pojawiają się wewnątrz aplikacji SAP są dostarczane przez podstawowe rozszerzenie platformy Azure dla SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Uruchamianie sprawdzania gotowości na maszynie Wirtualnej systemu Windows

1. Zaloguj się do maszyny wirtualnej platformy Azure (przy użyciu konta administratora nie jest konieczne).
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia zmień katalog na folder instalacyjny rozszerzenia\\platformy\\Azure\\dla SAP: C: Wtyczki pakietów Microsoft.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt; \\wersja>upuszczania

   *Wersja* w ścieżce do rozszerzenia może się różnić. Jeśli w folderze instalacyjnym są widoczne foldery wielu wersji rozszerzenia, sprawdź konfigurację usługi AzureEnhancedMonitoring Windows, a następnie przełącz się do folderu wskazanego jako Ścieżka do pliku *wykonywalnego*.

   ![Właściwości usługi z uruchomionym rozszerzeniem platformy Azure dla sap][deployment-guide-figure-1000]

1. W wierszu polecenia uruchom **plik azperflib.exe** bez żadnych parametrów.

   > [!NOTE]
   > Azperflib.exe działa w pętli i aktualizuje zebrane liczniki co 60 sekund. Aby zakończyć pętlę, zamknij okno wiersza polecenia.
   >
   >

Jeśli rozszerzenie platformy Azure dla SAP nie jest zainstalowane lub usługa AzureEnhancedMonitoring nie jest uruchomiona, rozszerzenie nie zostało poprawnie skonfigurowane. Aby uzyskać szczegółowe informacje dotyczące wdrażania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla sap][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe jest składnikiem, którego nie można używać do własnych celów. Jest to składnik, który dostarcza dane infrastruktury platformy Azure związane z maszyną wirtualną dla agenta hosta SAP wyłącznie.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Sprawdź dane wyjściowe azperflib.exe

Dane wyjściowe programu Azperflib.exe są wyświetlane we wszystkich licznikach wydajności platformy Azure zapełnianych platformy Azure. U dołu listy zebranych liczników wskaźnik podsumowania i kondycji pokazuje stan rozszerzenia platformy Azure dla sap.

![Wynik kontroli kondycji poprzez wykonanie azperflib.exe, co wskazuje, że nie ma problemów][deployment-guide-figure-1100]
<a name="figure-11"></a>

Sprawdź wynik zwrócony dla **liczników całkowitego** wyniku, który jest zgłaszany jako pusty, a stan **kondycji**, pokazano na powyższej ilustracji.

Interpretuj wartości wynikowe w następujący sposób:

| Wartości wyników Azperflib.exe | Rozszerzenie usługi Azure dla stanu kondycji SAP |
| --- | --- |
| **Wywołania interfejsu API — niedostępne** | Liczniki, które nie są dostępne, mogą nie mieć zastosowania do konfiguracji maszyny wirtualnej lub są błędami. Zobacz **Stan kondycji**. |
| **Liczniki razem - puste** |Następujące dwa liczniki magazynu platformy Azure mogą być puste: <ul><li>Ms. serwera opóźnienia operacji odczytu magazynu</li><li>Odczyt pamięci masowej Opóźnienie operacji E2E ms</li></ul>Wszystkie inne liczniki muszą mieć wartości. |
| **Stan zdrowia** |Tylko OK, jeśli stan zwrotu pokazuje **OK**. |
| **Diagnostyka** |Szczegółowe informacje o stanie zdrowia. |

Jeśli wartość **stanu kondycji** nie jest **ok,** postępuj zgodnie z instrukcjami w [sprawdź kondycję dla usługi Azure Extension dla konfiguracji SAP][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Uruchom sprawdzanie gotowości na maszynie Wirtualnej z systemem Linux

1. Połącz się z maszyną wirtualną platformy Azure przy użyciu funkcji SSH.

1. Sprawdź dane wyjściowe rozszerzenia platformy Azure dla sap.

   a.  Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Oczekiwany wynik**: Zwraca listę liczników wydajności. Plik nie powinien być pusty.

   b. Uruchom polecenie `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Oczekiwany wynik**: Zwraca jeden wiersz, w którym błąd jest **brak**, na przykład **3;config; Błąd;;0;0; none;0;1456416792;tst-servercs;**

   d. Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Oczekiwany wynik:** Zwraca wartość jako pustą lub nie istnieje.

Jeśli poprzednie sprawdzenie nie powiodło się, uruchom te dodatkowe kontrole:

1. Upewnij się, że waagent jest zainstalowany i włączony.

   a.  Uruchom polecenie `sudo ls -al /var/lib/waagent/`

     **Oczekiwany wynik:** Wyświetla listę zawartości katalogu waagent.

   b.  Uruchom polecenie `ps -ax | grep waagent`

   **Oczekiwany wynik**: Wyświetla jeden wpis podobny do:`python /usr/sbin/waagent -daemon`

1. Upewnij się, że rozszerzenie platformy Azure dla sap jest zainstalowany i uruchomiony.

   a.  Uruchom polecenie `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Oczekiwany wynik:** Wyświetla listę zawartości katalogu Rozszerzenia platformy Azure dla SAP.

   b. Uruchom polecenie `ps -ax | grep AzureEnhanced`

   **Oczekiwany wynik**: Wyświetla jeden wpis podobny do:`python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Zainstaluj agenta hosta SAP zgodnie z opisem w SAP Note [1031096]i sprawdź dane wyjściowe `saposcol`.

   a.  Uruchom polecenie `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Uruchom polecenie `dump ccm`

   d.  Sprawdź, czy **metryka Virtualization_Configuration\Enhanced Monitoring Access** jest **prawdziwa.**

Jeśli masz już zainstalowany serwer aplikacji SAP NetWeaver ABAP, otwórz usługę ST06 i sprawdź, czy włączone jest rozszerzone monitorowanie.

Jeśli którakolwiek z tych kontroli nie powiedzie się, a szczegółowe informacje na temat ponownego wdrożenia rozszerzenia można znaleźć w [rozwiązywaniu problemów z rozszerzeniem platformy Azure dla systemu SAP.][deployment-guide-5.3]

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Sprawdzanie kondycji rozszerzenia platformy Azure dla konfiguracji SAP

Jeśli niektóre dane infrastruktury nie są dostarczane poprawnie, jak wskazano w teście opisanym w [Test gotowości dla rozszerzenia platformy Azure dla SAP,][deployment-guide-5.1]uruchom `Test-AzVMAEMExtension` polecenie cmdlet, aby sprawdzić, czy infrastruktura platformy Azure i rozszerzenie platformy Azure dla SAP są poprawnie skonfigurowane.

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet programu Azure PowerShell, zgodnie z opisem w obszarze [Wdrażanie poleceń cmdlet programu Azure PowerShell.][deployment-guide-4.1]
1. Uruchom następujące polecenie programu PowerShell. Aby uzyskać listę dostępnych środowisk, uruchom `Get-AzEnvironment`polecenie cmdlet . Aby korzystać z globalnej platformy Azure, wybierz środowisko **AzureCloud.** W przypadku platformy Azure w Chinach wybierz pozycję **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Wprowadź dane konta i zidentyfikuj maszynę wirtualną platformy Azure.

   ![Strona wprowadzania narzędzia cmdlet platformy Azure specyficzne dla platformy SAP VMConfigForSAP_GUI][deployment-guide-figure-1200]

1. Skrypt testuje konfigurację wybranej maszyny wirtualnej.

   ![Dane wyjściowe pomyślnego testu rozszerzenia platformy Azure dla systemu SAP][deployment-guide-figure-1300]

Upewnij się, że każdy wynik kontroli kondycji jest **OK**. Jeśli niektóre czeki nie są wyświetlane **w porządku,** uruchom polecenie cmdlet aktualizacji zgodnie z opisem w [temacie Konfigurowanie rozszerzenia platformy Azure dla sap][deployment-guide-4.5]. Poczekaj 15 minut i powtórz kontrole opisane w [czeku gotowości dla rozszerzenia platformy Azure dla sap][deployment-guide-5.1] i sprawdzania kondycji dla usługi Azure Extension dla konfiguracji [SAP.][deployment-guide-5.2] Jeśli kontrole nadal wskazują na problem z niektórymi lub wszystkimi licznikami, zobacz Rozwiązywanie problemów z [rozszerzeniem platformy Azure dla sap][deployment-guide-5.3].

> [!Note]
> Niektóre ostrzeżenia mogą wystąpić w przypadkach, gdy używasz zarządzanych standardowych dysków platformy Azure. Ostrzeżenia będą wyświetlane zamiast testów zwracających "OK". Jest to normalne i przeznaczone w przypadku tego typu dysku. Zobacz też: [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla systemu SAP][deployment-guide-5.3]
> 

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Rozwiązywanie problemów z rozszerzeniem platformy Azure dla systemu SAP

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Liczniki wydajności platformy Azure w ogóle się nie wyświetlają

Usługa AzureEnhancedMonitoring Windows zbiera metryki wydajności na platformie Azure. Jeśli usługa nie została zainstalowana poprawnie lub jeśli nie jest uruchomiona na maszynie Wirtualnej, nie można zbierać żadnych metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia Azure dla systemu SAP jest pusty

###### <a name="issue"></a>Problem

Katalog instalacyjny\\\\C:\\Pakiety Wtyczki Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;wersja>\\upuszczanie jest pusta.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Określ, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera `Set-AzVMAEMExtension` lub ponowne uruchomienie skryptu konfiguracji.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Usługa rozszerzenia platformy Azure dla sap nie istnieje

###### <a name="issue"></a>Problem

Usługa AzureEnhancedMonitoring Windows nie istnieje.

Azperflib.exe output wyrzuca błąd:

![Wykonanie programu azperflib.exe wskazuje, że usługa rozszerzenia platformy Azure dla systemu SAP nie jest uruchomiona][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Rozwiązanie

Jeśli usługa nie istnieje, rozszerzenie platformy Azure dla sap nie zostało poprawnie zainstalowane. Ponownie rozmieszczaj rozszerzenie przy użyciu kroków opisanych w scenariuszu wdrażania w [scenariuszach wdrażania maszyn wirtualnych dla sap na platformie Azure.][deployment-guide-3]

Po wdrożeniu rozszerzenia po godzinie sprawdź ponownie, czy liczniki wydajności platformy Azure są dostępne na maszynie Wirtualnej platformy Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Usługa rozszerzenia platformy Azure dla usługi SAP istnieje, ale nie można go uruchomić

###### <a name="issue"></a>Problem

Usługa AzureEnhancedMonitoring Windows istnieje i jest włączona, ale nie można go uruchomić. Aby uzyskać więcej informacji, sprawdź dziennik zdarzeń aplikacji.

###### <a name="solution"></a>Rozwiązanie

Konfiguracja jest niepoprawna. Uruchom ponownie rozszerzenie platformy Azure dla sap w maszynie wirtualnej, zgodnie z [opisem w Konfigurowanie rozszerzenia platformy Azure dla SAP][deployment-guide-4.5].

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Brakuje niektórych liczników wydajności platformy Azure

Usługa AzureEnhancedMonitoring Windows zbiera metryki wydajności na platformie Azure. Usługa pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z usługi Azure Diagnostics. Liczniki magazynu są używane z rejestrowania na poziomie subskrypcji magazynu.

Jeśli rozwiązywanie problemów przy użyciu sap note [1999351] nie `Set-AzVMAEMExtension` rozwiąże problemu, uruchom ponownie skrypt konfiguracji. Może być trzeba czekać godzinę, ponieważ analizy magazynu lub liczniki diagnostyki może nie zostać utworzony natychmiast po ich włączeniu. Jeśli problem będzie się powtarzał, otwórz komunikat działu obsługi klienta SAP na składniku BC-OP-NT-AZR dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Liczniki wydajności platformy Azure w ogóle się nie wyświetlają

Metryki wydajności na platformie Azure są zbierane przez demona. Jeśli demon nie jest uruchomiony, nie można zbierać żadnych metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia Azure dla systemu SAP jest pusty

###### <a name="issue"></a>Problem

Katalog \\var\\lib\\waagent\\ nie ma podkatalogu rozszerzenia platformy Azure dla sap.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Określ, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera i/lub ponowne uruchomienie skryptu `Set-AzVMAEMExtension` konfiguracji.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Wykonanie Set-AzVMAEMExtension i Test-AzVMAEMExtension wyświetla komunikaty ostrzegawcze informujące, że standardowe dyski zarządzane nie są obsługiwane

###### <a name="issue"></a>Problem

Podczas wykonywania komunikatów Set-AzVMAEMExtension lub Test-AzVMAEMExtension, takich jak te są pokazane:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Wykonywanie azperfli.exe, jak opisano wcześniej można uzyskać wynik, który wskazuje stan niezrozumiejny. 

###### <a name="solution"></a>Rozwiązanie

Wiadomości są spowodowane faktem, że standardowe dyski zarządzane nie dostarczają interfejsów API używanych przez rozszerzenie SAP dla SAP do sprawdzania statystyk standardowych kont usługi Azure Storage. Nie jest to kwestia niepokoju. Powodem wprowadzenia danych zbierania dla kont magazynu dysków standardowych było ograniczanie często występujących we/wy we/wy. Dyski zarządzane unikną takiego ograniczania przepustowości, ograniczając liczbę dysków na koncie magazynu. W związku z tym brak tego typu danych nie jest krytyczna.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Brakuje niektórych liczników wydajności platformy Azure

Metryki wydajności na platformie Azure są zbierane przez demona, który pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z usługi Azure Diagnostics. Liczniki magazynu pochodzą z dzienników w subskrypcji magazynu.

Aby uzyskać pełną i aktualną listę znanych problemów, zobacz UWAGA SAP [1999351], która zawiera dodatkowe informacje dotyczące rozwiązywania problemów dla usługi Azure Extension for SAP.

Jeśli rozwiązywanie problemów przy użyciu sap Note [1999351] `Set-AzVMAEMExtension` nie rozwiąże problemu, uruchom ponownie skrypt konfiguracji zgodnie z opisem w [Konfigurowanie rozszerzenia platformy Azure dla SAP][deployment-guide-4.5]. Może być trzeba czekać godzinę, ponieważ analizy magazynu lub liczniki diagnostyki może nie zostać utworzony natychmiast po ich włączeniu. Jeśli problem będzie się powtarzał, otwórz komunikat działu obsługi klienta SAP na składniku BC-OP-NT-AZR dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej systemu Linux.
