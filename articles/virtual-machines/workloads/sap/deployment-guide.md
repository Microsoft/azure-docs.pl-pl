---
title: Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver | Microsoft Docs
description: Dowiedz się, jak wdrożyć oprogramowanie SAP na maszynach wirtualnych z systemem Linux na platformie Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: fe98ef297c6bed5ef3d982ed09db361244f75216
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675701"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver

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
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Buforowanie dla maszyn wirtualnych i wirtualnych dysków twardych)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Oprogramowanie RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktura wdrożenia RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Wysoka dostępność i odzyskiwanie po awarii przy użyciu maszyn wirtualnych platformy Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 z dodatkiem SP1 CU4 i nowsze)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 z dodatkiem SP1 CU3 i wcześniejszymi wersjami)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Korzystanie z SQL Server obrazu z witryny Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Ogólne SQL Server dla oprogramowania SAP na platformie Azure — podsumowanie)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specyficzne dla SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Konfiguracja magazynu)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Tworzenie kopii zapasowej i przywracanie)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Zagadnienia dotyczące wydajności tworzenia kopii zapasowych i przywracania)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Różnych)
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

[deployment-guide]:deployment-guide.md (Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Zasoby SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Wdrażanie maszyny wirtualnej przy użyciu obrazu niestandardowego)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenariusz 1: Wdrażanie maszyny wirtualnej z poziomu portalu Azure Marketplace dla oprogramowania SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenariusz 2. Wdrażanie maszyny wirtualnej przy użyciu obrazu niestandardowego dla oprogramowania SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenariusz 3: przeniesienie maszyny wirtualnej ze środowiska lokalnego przy użyciu nieuogólnionego wirtualnego dysku twardego platformy Azure przy użyciu oprogramowania SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenariusze wdrażania maszyn wirtualnych dla oprogramowania SAP na Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Wdrażanie poleceń cmdlet Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Pobieranie i importowanie poleceń cmdlet programu PowerShell dotyczących oprogramowania SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Przyłączanie maszyny wirtualnej do domeny lokalnej — tylko system Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (System)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Pobieranie, Instalowanie i włączanie agenta maszyny wirtualnej platformy Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfejs wiersza polecenia platformy Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Skonfiguruj rozszerzenie platformy Azure dla oprogramowania SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Skonfiguruj nowe rozszerzenie platformy Azure dla oprogramowania SAP za pomocą Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Skonfiguruj nowe rozszerzenie platformy Azure dla oprogramowania SAP za pomocą interfejsu wiersza polecenia platformy Azure)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Sprawdzanie gotowości dla rozszerzenia platformy Azure dla oprogramowania SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Sprawdzenie gotowości dla nowego rozszerzenia platformy Azure dla oprogramowania SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Sprawdzenie kondycji konfiguracji rozszerzenia platformy Azure dla oprogramowania SAP)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Sprawdzenie kondycji nowego rozszerzenia platformy Azure dla konfiguracji SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Rozwiązywanie problemów z nowym rozszerzeniem platformy Azure dla oprogramowania SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Rozwiązywanie problemów z usługą Azure Extension for SAP — kontakt z pomocą techniczną)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP — uruchamianie skryptu instalacyjnego)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP — Wdróż ponownie po programie Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP — Naprawa połączenia internetowego)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurowanie rozszerzenia maszyny wirtualnej)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Sprawdzenia i rozwiązywanie problemów)

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

[planning-guide]:planning-guide.md (Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Produkcyjnych)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Wysoka dostępność i odzyskiwanie po awarii dla oprogramowania SAP NetWeaver uruchomionego na platformie Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Wysoka dostępność dla serwerów aplikacji SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Używanie funkcji Autostart dla wystąpień SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Tylko w chmurze — wdrożenia maszyn wirtualnych na platformie Azure bez zależności w lokalnej sieci klienta)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Wiele lokalizacji — wdrażanie pojedynczych lub wielu maszyn wirtualnych SAP na platformie Azure w pełni zintegrowane z siecią lokalną)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiony platformy Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domeny błędów)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Uaktualnij domeny)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Zestawy dostępności platformy Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure koncepcji maszyn wirtualnych)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Przeniesienie maszyny wirtualnej z lokalnego na platformę Azure na dysk nieuogólniony)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Wdrażanie maszyny wirtualnej z obrazem specyficznym dla klienta)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Przygotowanie do przeniesienia maszyny wirtualnej z lokalizacji lokalnej na platformę Azure przy użyciu dysku nieuogólnionego)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Przygotowanie do wdrożenia maszyny wirtualnej z obrazem specyficznym dla klienta dla oprogramowania SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Przygotowywanie maszyn wirtualnych za pomocą oprogramowania SAP dla platformy Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Różnica między dyskiem platformy Azure i obrazem platformy Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Przekazywanie wirtualnego dysku twardego z lokalnego na platformę Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopiowanie dysków między kontami usługi Azure Storage)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struktura maszyny wirtualnej/wirtualnego dysku twardego dla wdrożeń SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Ustawianie instalacji automatycznej dla dołączonych dysków)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Rozwiązanie do monitorowania platformy Azure dla oprogramowania SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure sieci)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Magazyn: Microsoft Azure Storage i dyski z danymi)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Macierz dostępności produktu SAP)
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Wdrażanie maszyn wirtualnych i zarządzanie nimi przy użyciu szablonów Azure Resource Manager i interfejsu wiersza polecenia platformy Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Zarządzanie maszynami wirtualnymi przy użyciu Azure Resource Manager i programu PowerShell)
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Usługa Azure Virtual Machines to rozwiązanie dla organizacji, które potrzebują zasobów obliczeniowych i magazynu, w minimalnym czasie i bez długiego cyklu zakupów. Za pomocą usługi Azure Virtual Machines można wdrażać klasyczne aplikacje, takie jak aplikacje oparte na oprogramowaniu SAP NetWeaver, na platformie Azure. Zwiększ niezawodność i dostępność aplikacji bez dodatkowych zasobów lokalnych. Usługa Azure Virtual Machines obsługuje łączność między środowiskami lokalnymi, dzięki czemu możesz zintegrować usługę Azure Virtual Machines z domenami lokalnymi organizacji, chmurami prywatnymi i systemem SAP system.

W tym artykule omówiono procedurę wdrażania aplikacji SAP na maszynach wirtualnych na platformie Azure, łącznie z alternatywnymi opcjami wdrażania i rozwiązywaniem problemów. Ten artykuł dotyczy informacji na temat [planowania i wdrażania Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver][planning-guide]. Uzupełnia także dokumentację instalacji SAP i uwagi SAP, które są zasobami podstawowymi do instalowania i wdrażania oprogramowania SAP.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Konfigurowanie maszyny wirtualnej platformy Azure na potrzeby wdrażania oprogramowania SAP obejmuje wiele kroków i zasobów. Przed rozpoczęciem upewnij się, że spełniasz wymagania wstępne dotyczące instalowania oprogramowania SAP na maszynach wirtualnych na platformie Azure.

### <a name="local-computer"></a>Komputer lokalny

Aby zarządzać maszynami wirtualnymi z systemem Windows lub Linux, można użyć skryptu programu PowerShell i Azure Portal. W przypadku obu narzędzi potrzebny jest komputer lokalny z systemem Windows 7 lub nowszą wersją systemu Windows. Jeśli chcesz zarządzać tylko maszynami wirtualnymi z systemem Linux i chcesz użyć komputera z systemem Linux dla tego zadania, możesz użyć interfejsu wiersza polecenia platformy Azure.

### <a name="internet-connection"></a>Połączenie internetowe

Aby pobrać i uruchomić narzędzia i skrypty wymagane do wdrożenia oprogramowania SAP, musisz mieć połączenie z Internetem. Maszyna wirtualna platformy Azure, na której działa rozszerzenie platformy Azure dla oprogramowania SAP, musi również mieć dostęp do Internetu. Jeśli maszyna wirtualna platformy Azure jest częścią sieci wirtualnej platformy Azure lub domeny lokalnej, upewnij się, że ustawiono odpowiednie ustawienia serwera proxy, zgodnie z opisem w temacie [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Subskrypcja platformy Microsoft Azure

Musisz mieć aktywne konto platformy Azure.

### <a name="topology-and-networking"></a>Topologia i sieć

Należy zdefiniować topologię i architekturę wdrożenia SAP na platformie Azure:

* Konta usługi Azure Storage do użycia
* Sieć wirtualna, w której ma zostać wdrożony system SAP
* Grupa zasobów, w której ma zostać wdrożony system SAP
* Region świadczenia usługi Azure, w którym chcesz wdrożyć system SAP
* Konfiguracja SAP (dwie warstwy lub trzy warstwy)
* Rozmiary maszyn wirtualnych i liczba dodatkowych dysków z danymi, które mają zostać zainstalowane na maszynach wirtualnych
* Konfiguracja korekcji SAP i systemu transportowego (CTS)

Przed rozpoczęciem procesu wdrażania oprogramowania SAP należy utworzyć i skonfigurować konta usługi Azure Storage (w razie potrzeby) lub sieci wirtualne platformy Azure. Informacje o sposobach tworzenia i konfigurowania tych zasobów znajdują się w temacie [Azure Virtual Machines Planning and implementation for SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Ustalanie rozmiarów SAP

Poznaj następujące informacje dotyczące określania wielkości SAP:

* Prognozowane obciążenie SAP, na przykład przy użyciu narzędzia SAP Quick size i numeru SAP (Application Performance standard)
* Wymagany zasób procesora CPU i użycie pamięci przez system SAP
* Wymagana liczba operacji wejścia/wyjścia (we/wy) na sekundę
* Wymagana przepustowość sieci do ostatecznej komunikacji między maszynami wirtualnymi na platformie Azure
* Wymagana przepustowość sieci między zasobami lokalnymi i systemem SAP wdrożonym na platformie Azure

### <a name="resource-groups"></a>Grupy zasobów

W Azure Resource Manager można używać grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Zasoby

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby SAP

Podczas konfigurowania wdrożenia oprogramowania SAP wymagane są następujące zasoby SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [1409604] ma wymaganą wersję agenta hosta SAP dla systemu Windows na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca oprogramowania SAP [2002167] zawiera ogólne informacje na temat Red Hat Enterprise Linux 7. x.
* Uwaga dotycząca oprogramowania SAP [2069760] zawiera ogólne informacje na temat Oracle Linux 7. x.
* Uwaga dotycząca oprogramowania SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem platformy Azure dla oprogramowania SAP.
* W przypadku oprogramowania SAP Uwaga [1597355] znajdują się ogólne informacje o wymianie miejsca na system Linux.
* [Strona SAP na stronie usługi Azure SCN](https://wiki.scn.sap.com/wiki/x/Pia7Gg) zawiera wiadomości i zbiór przydatnych zasobów.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* Polecenia cmdlet programu PowerShell dotyczące oprogramowania SAP, które są częścią [Azure PowerShell][azure-ps].
* Polecenie interfejsu wiersza polecenia platformy Azure specyficzne dla SAP, które są częścią [interfejsu wiersza polecenia platformy Azure][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Zasoby systemu Windows

Te artykuły firmy Microsoft obejmują wdrożenia oprogramowania SAP na platformie Azure:

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenariusze wdrażania oprogramowania SAP na maszynach wirtualnych platformy Azure

Istnieje wiele opcji wdrażania maszyn wirtualnych i skojarzonych z nimi dysków na platformie Azure. Ważne jest, aby zrozumieć różnice między opcjami wdrażania, ponieważ można wykonać różne kroki, aby przygotować maszyny wirtualne do wdrożenia na podstawie wybranego typu wdrożenia.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenariusz 1: Wdrażanie maszyny wirtualnej z poziomu portalu Azure Marketplace dla oprogramowania SAP

Aby wdrożyć maszynę wirtualną, możesz użyć obrazu dostarczonego przez firmę Microsoft lub innej firmy w portalu Azure Marketplace. W witrynie Marketplace dostępne są standardowe obrazy systemu operacyjnego Windows Server i różne dystrybucje w systemie Linux. Można również wdrożyć obraz zawierający jednostki SKU systemu zarządzania bazami danych (DBMS), na przykład Microsoft SQL Server. Aby uzyskać więcej informacji o korzystaniu z obrazów z jednostkami SKU systemu DBMS, zobacz artykuł [Azure Virtual Machines DBMS Deployment for SAP NetWeaver][dbms-guide].

Poniższy schemat blokowy przedstawia sekwencję kroków czynności związanych z wdrażaniem maszyny wirtualnej w witrynie Azure Marketplace:

![Schemat blokowy wdrożenia maszyny wirtualnej dla systemów SAP za pomocą obrazu maszyny wirtualnej w portalu Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej przy użyciu Azure Portal

Najprostszym sposobem tworzenia nowej maszyny wirtualnej przy użyciu obrazu z witryny Azure Marketplace jest użycie Azure Portal.

1.  Przejdź do witryny <https://portal.azure.com/#create/hub>.  Lub w menu Azure Portal wybierz pozycję **+ Nowy**.
1.  Wybierz pozycję **obliczenia**, a następnie wybierz typ systemu operacyjnego, który chcesz wdrożyć. Na przykład: Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) lub Oracle Linux 7,2. W widoku listy domyślnej nie są wyświetlane wszystkie obsługiwane systemy operacyjne. Aby uzyskać pełną listę, wybierz pozycję **Zobacz wszystko** . Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych na potrzeby wdrażania oprogramowania SAP, zobacz temat SAP Note [1928533].
1.  Na następnej stronie Przejrzyj warunki i postanowienia.
1.  W polu **Wybierz model wdrażania** wybierz pozycję **Menedżer zasobów**.
1.  Wybierz przycisk **Utwórz**.

Kreator przeprowadzi Cię przez proces konfigurowania wymaganych parametrów w celu utworzenia maszyny wirtualnej, a także wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów są następujące:

1. **Podstawy**:
   * **Name**: nazwa zasobu (Nazwa maszyny wirtualnej).
   * **Typ dysku maszyny wirtualnej**: Wybierz typ dysku dysku systemu operacyjnego. Jeśli chcesz używać Premium Storage dla dysków z danymi, zalecamy również korzystanie z Premium Storage dla dysku systemu operacyjnego.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło, które zostały utworzone podczas aprowizacji. W przypadku maszyny wirtualnej z systemem Linux możesz wprowadzić klucz publicznego Secure Shell (SSH), którego używasz do logowania się na komputerze.
   * **Subskrypcja**: wybierz subskrypcję, której chcesz użyć do aprowizacji nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny wirtualnej. Możesz wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja**: gdzie należy wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszynę wirtualną z siecią lokalną, upewnij się, że wybrano lokalizację sieci wirtualnej, która łączy platformę Azure z siecią lokalną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Networking][planning-guide-microsoft-azure-networking] na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide].
1. **Rozmiar**:

     Listę obsługiwanych typów maszyn wirtualnych można znaleźć w temacie SAP Note [1928533]. Upewnij się, że wybrano poprawny typ maszyny wirtualnej, jeśli chcesz użyć usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują Premium Storage. Aby uzyskać więcej informacji, zobacz [Magazyn: Microsoft Azure Storage i dyski danych][planning-guide-storage-microsoft-azure-storage-and-data-disks] oraz [usługa Azure Storage dla obciążeń SAP](./planning-guide-storage.md) na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku dysku systemu operacyjnego. Jeśli chcesz używać Premium Storage dla dysków z danymi, zalecamy również korzystanie z Premium Storage dla dysku systemu operacyjnego.
     * **Użyj dysków zarządzanych**: Jeśli chcesz użyć Managed disks, wybierz pozycję tak. Aby uzyskać więcej informacji na temat Managed Disks, zobacz rozdział [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) w przewodniku planowania.
     * **Konto magazynu**: Wybierz istniejące konto magazynu lub Utwórz nowe. Nie wszystkie typy magazynów działają w przypadku uruchamiania aplikacji SAP. Aby uzyskać więcej informacji na temat typów magazynów, zobacz [Struktura magazynu maszyny wirtualnej na potrzeby wdrożeń RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Sieć**
     * **Sieć wirtualna** i **podsieć**: aby zintegrować maszynę wirtualną z intranetem, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
     * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Aby uzyskać dostęp do maszyny wirtualnej za pośrednictwem Internetu, możesz użyć publicznego adresu IP. Upewnij się, że utworzono również sieciową grupę zabezpieczeń, która pomaga w zabezpieczaniu dostępu do maszyny wirtualnej.
     * **Sieciowa Grupa zabezpieczeń**: Aby uzyskać więcej informacji, zobacz [sterowanie przepływem ruchu sieciowego za pomocą sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia**: można zainstalować rozszerzenia maszyny wirtualnej, dodając je do wdrożenia. Nie musisz dodawać rozszerzeń w tym kroku. Rozszerzenia wymagane dla pomocy technicznej SAP są instalowane później. Zobacz rozdział [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [zestawy dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu**: możesz wybrać opcję **Wyłącz** dla diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: możesz wybrać opcję **Wyłącz** dla diagnostyki monitorowania.

1. **Podsumowanie**:

   Przejrzyj wybrane opcje, a następnie wybierz przycisk **OK**.

Twoja maszyna wirtualna jest wdrożona w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu

Maszynę wirtualną można utworzyć przy użyciu jednego z szablonów SAP opublikowanych w [repozytorium Azure-szybkiego Start-templates][azure-quickstart-templates-github]w witrynie GitHub. Można również ręcznie utworzyć maszynę wirtualną przy użyciu [Azure Portal][virtual-machines-windows-tutorial], [programu PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]lub [interfejsu wiersza polecenia platformy Azure][virtual-machines-linux-tutorial].

* [**Konfiguracja dwuwarstwowa (tylko jeden szablon maszyny wirtualnej)** (system SAP-2-warstwowy w witrynie Marketplace)][sap-templates-2-tier-marketplace-image]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Konfiguracja dwuwarstwowa (tylko jedna maszyna wirtualna) — Managed disks** (system SAP-2-warstwowy-Marketplace-Image-MD)][sap-templates-2-tier-marketplace-image-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i Managed Disks, użyj tego szablonu.
* [**Konfiguracja trzech warstw (wiele maszyn wirtualnych)** (w przypadku oprogramowania SAP-3-warstwowego-Marketplace)][sap-templates-3-tier-marketplace-image]

  Aby utworzyć system z trzema warstwami przy użyciu wielu maszyn wirtualnych, użyj tego szablonu.
* [**Konfiguracja trzech warstw (wiele maszyn wirtualnych) — Managed disks** (SAP-3-warstwa-Marketplace-Image-MD)][sap-templates-3-tier-marketplace-image-md]

  Aby utworzyć system z trzema warstwami przy użyciu wielu maszyn wirtualnych i Managed Disks, użyj tego szablonu.

W Azure Portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, która ma zostać użyta do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w subskrypcji.
   * **Lokalizacja**: gdzie należy wdrożyć szablon. W przypadku wybrania istniejącej grupy zasobów zostanie użyta lokalizacja tej grupy zasobów.

1. **Ustawienia**:
   * **Identyfikator systemu SAP**: identyfikator systemowy SAP (SID).
   * **Typ systemu** operacyjnego: system operacyjny, który chcesz wdrożyć, na przykład Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) lub Oracle Linux 7,2.

     W widoku listy nie są wyświetlane wszystkie obsługiwane systemy operacyjne. Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych na potrzeby wdrażania oprogramowania SAP, zobacz temat SAP Note [1928533].
   * **Rozmiar systemu SAP**: rozmiar systemu SAP.

     Liczba punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
   * **Dostępność systemu** (tylko dla szablonu trójwarstwowej): dostępność systemu.

     Wybierz pozycję **ha** dla konfiguracji, która jest odpowiednia dla instalacji o wysokiej dostępności. Tworzone są dwa serwery baz danych i dwa serwery dla ABAP SAP Central Services (ASCS).
   * **Typ magazynu** (tylko dla szablonu dwuwarstwowego): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynów, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure SSD w warstwie Premium Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny wirtualnej na potrzeby wdrożeń RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Nazwa użytkownika administratora** i **hasło administratora**: Nazwa użytkownika i hasło.
     Nowy użytkownik zostanie utworzony w celu zalogowania się do maszyny wirtualnej.
   * **Nowa lub istniejąca podsieć**: określa, czy zostanie utworzona nowa sieć wirtualna i podsieć, czy używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Ten identyfikator zwykle wygląda następująco: &lt; Identyfikator subskrypcji/subscriptions/>/resourceGroups/nazwa &lt; grupy zasobów>/Providers/Microsoft.Network/virtualNetworks/ &lt; nazwa sieci wirtualnej>/subnets/ &lt; Nazwa podsieci>

1. Warunki **i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

Agent maszyny wirtualnej platformy Azure jest wdrażany domyślnie przy użyciu obrazu z witryny Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub ExpressRoute, maszyna wirtualna może nie być w stanie uzyskać dostępu do Internetu i nie będzie mogła pobierać wymaganych rozszerzeń maszyn wirtualnych ani zbierać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pośrednictwem rozszerzenia SAP dla platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym Active Directory lub wystąpieniem usługi DNS za pośrednictwem połączenia sieci VPN typu lokacja-lokacja lub usługi ExpressRoute (jest to nazywane *różnymi* lokalizacjami na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłącza się do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [dołączanie maszyny wirtualnej do domeny lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurowanie rozszerzenia maszyny wirtualnej

Aby upewnić się, że SAP obsługuje Twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla oprogramowania SAP zgodnie z opisem w temacie [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5]. Zapoznaj się z wymaganiami wstępnymi dotyczącymi oprogramowania SAP oraz minimalnymi wersjami jądra SAP i agenta hosta SAP w zasobach wymienionych w obszarze [zasoby SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Sprawdzenie rozszerzenia maszyny wirtualnej dla oprogramowania SAP

Sprawdź, czy rozszerzenie maszyny wirtualnej dla oprogramowania SAP działa, zgodnie z opisem w [sprawdzenia i rozwiązywaniu problemów][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Kroki po wdrożeniu

Po utworzeniu maszyny wirtualnej i wdrożeniu maszyny wirtualnej należy zainstalować wymagane składniki oprogramowania na maszynie wirtualnej. Ze względu na sekwencję instalacji/wdrożenia oprogramowania w tym typie wdrożenia maszyny wirtualnej oprogramowanie, które ma zostać zainstalowane, musi być już dostępne na platformie Azure, na innej maszynie wirtualnej lub jako dysk, który można dołączyć. Lub Rozważ użycie scenariusza obejmującego wiele lokalizacji, w którym podano łączność z zasobami lokalnymi (udziały instalacji).

Po wdrożeniu maszyny wirtualnej na platformie Azure postępuj zgodnie z tymi samymi wskazówkami i narzędziami, aby zainstalować oprogramowanie SAP na maszynie wirtualnej tak samo jak w środowisku lokalnym. Aby zainstalować oprogramowanie SAP na maszynie wirtualnej platformy Azure, w oprogramowaniu SAP i Microsoft zaleca się przekazywanie i przechowywanie nośnika instalacyjnego SAP na wirtualnych dyskach twardych platformy Azure lub Managed Disks, a także tworzenie maszyny wirtualnej platformy Azure, która działa jako serwer plików, który ma wszystkie wymagane nośniki instalacyjne SAP.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenariusz 2. Wdrażanie maszyny wirtualnej przy użyciu obrazu niestandardowego dla oprogramowania SAP

Ponieważ różne wersje systemu operacyjnego lub DBMS mają różne wymagania dotyczące poprawek, obrazy Znalezione w portalu Azure Marketplace mogą nie spełniać Twoich potrzeb. Możesz zamiast tego utworzyć maszynę wirtualną przy użyciu własnego obrazu maszyny wirtualnej systemu operacyjnego/DBMS, który można wdrożyć ponownie później.
Aby utworzyć prywatny obraz dla systemu Linux, należy wykonać różne czynności.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Aby przygotować obraz systemu Windows, którego można użyć do wdrożenia wielu maszyn wirtualnych, ustawienia systemu Windows (takie jak identyfikator SID systemu Windows i nazwa hosta) muszą być abstrakcyjne lub uogólnione na lokalnej maszynie wirtualnej. W tym celu można użyć [narzędzia Sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) .
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> Aby przygotować obraz systemu Linux, którego można użyć do wdrożenia wielu maszyn wirtualnych, niektóre ustawienia systemu Linux muszą być abstrakcyjne lub uogólnione na lokalnej maszynie wirtualnej. W tym celu można użyć `waagent -deprovision`  w tym celu. Aby uzyskać więcej informacji, zobacz [przechwytywanie maszyny wirtualnej z systemem Linux działającej na platformie Azure][virtual-machines-linux-capture-image] i [Podręcznik użytkownika agenta platformy Azure dla systemu Linux][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Możesz przygotować i utworzyć niestandardowy obraz, a następnie użyć go do utworzenia wielu nowych maszyn wirtualnych. Jest to opisane w temacie [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver][planning-guide]. Skonfiguruj zawartość bazy danych przy użyciu Menedżera aprowizacji oprogramowania SAP, aby zainstalować nowy system SAP (przywraca kopię zapasową bazy danych z dysku podłączonego do maszyny wirtualnej) lub przez bezpośrednie przywrócenie kopii zapasowej bazy danych z usługi Azure Storage, jeśli system DBMS ją obsługuje. Aby uzyskać więcej informacji, zobacz [wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver][dbms-guide]. Jeśli system SAP został już zainstalowany na lokalnej maszynie wirtualnej (szczególnie w przypadku systemów dwuwarstwowych), możesz dostosować ustawienia systemu SAP po wdrożeniu maszyny wirtualnej platformy Azure za pomocą procedury zmiany nazwy systemu obsługiwanej przez program SAP Software Provisioning Manager (SAP Uwaga [1619720]). W przeciwnym razie można zainstalować oprogramowanie SAP po wdrożeniu maszyny wirtualnej platformy Azure.

Poniższy schemat blokowy przedstawia sekwencję kroków czynności związanych z wdrażaniem maszyny wirtualnej na podstawie obrazu niestandardowego:

![Schemat blokowy wdrożenia maszyny wirtualnej dla systemów SAP za pomocą obrazu maszyny wirtualnej w prywatnej witrynie Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej przy użyciu Azure Portal

Najprostszym sposobem tworzenia nowej maszyny wirtualnej na podstawie obrazu dysku zarządzanego jest użycie Azure Portal. Aby uzyskać więcej informacji na temat tworzenia obrazu dysku zarządzanie, przeczytaj artykuł [Przechwytywanie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](../../windows/capture-image-resource.md)

1.  Przejdź do witryny <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Lub w menu Azure Portal wybierz pozycję **obrazy**.
1.  Wybierz obraz dysku zarządzanego, który chcesz wdrożyć, a następnie kliknij pozycję **Utwórz maszynę wirtualną** .

Kreator przeprowadzi Cię przez proces konfigurowania wymaganych parametrów w celu utworzenia maszyny wirtualnej, a także wszystkich wymaganych zasobów, takich jak interfejsy sieciowe i konta magazynu. Niektóre z tych parametrów są następujące:

1. **Podstawy**:
   * **Name**: nazwa zasobu (Nazwa maszyny wirtualnej).
   * **Typ dysku maszyny wirtualnej**: Wybierz typ dysku dysku systemu operacyjnego. Jeśli chcesz używać Premium Storage dla dysków z danymi, zalecamy również korzystanie z Premium Storage dla dysku systemu operacyjnego.
   * **Nazwa użytkownika i hasło** lub **klucz publiczny SSH**: Wprowadź nazwę użytkownika i hasło, które zostały utworzone podczas aprowizacji. W przypadku maszyny wirtualnej z systemem Linux możesz wprowadzić klucz publicznego Secure Shell (SSH), którego używasz do logowania się na komputerze.
   * **Subskrypcja**: wybierz subskrypcję, której chcesz użyć do aprowizacji nowej maszyny wirtualnej.
   * **Grupa zasobów**: Nazwa grupy zasobów dla maszyny wirtualnej. Możesz wprowadzić nazwę nowej grupy zasobów lub nazwę grupy zasobów, która już istnieje.
   * **Lokalizacja**: gdzie należy wdrożyć nową maszynę wirtualną. Jeśli chcesz połączyć maszynę wirtualną z siecią lokalną, upewnij się, że wybrano lokalizację sieci wirtualnej, która łączy platformę Azure z siecią lokalną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Networking][planning-guide-microsoft-azure-networking] na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide].
1. **Rozmiar**:

     Listę obsługiwanych typów maszyn wirtualnych można znaleźć w temacie SAP Note [1928533]. Upewnij się, że wybrano poprawny typ maszyny wirtualnej, jeśli chcesz użyć usługi Azure Premium Storage. Nie wszystkie typy maszyn wirtualnych obsługują Premium Storage. Aby uzyskać więcej informacji, zobacz [Magazyn: Microsoft Azure Storage i dyski danych][planning-guide-storage-microsoft-azure-storage-and-data-disks] oraz [usługa Azure Storage dla obciążeń SAP](./planning-guide-storage.md) na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide].

1. **Ustawienia**:
   * **Storage**
     * **Typ dysku**: Wybierz typ dysku dysku systemu operacyjnego. Jeśli chcesz używać Premium Storage dla dysków z danymi, zalecamy również korzystanie z Premium Storage dla dysku systemu operacyjnego.
     * **Użyj dysków zarządzanych**: Jeśli chcesz użyć Managed disks, wybierz pozycję tak. Aby uzyskać więcej informacji na temat Managed Disks, zobacz rozdział [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) w przewodniku planowania.
   * **Sieć**
     * **Sieć wirtualna** i **podsieć**: aby zintegrować maszynę wirtualną z intranetem, wybierz sieć wirtualną, która jest połączona z siecią lokalną.
     * **Publiczny adres IP**: Wybierz publiczny adres IP, którego chcesz użyć, lub wprowadź parametry, aby utworzyć nowy publiczny adres IP. Aby uzyskać dostęp do maszyny wirtualnej za pośrednictwem Internetu, możesz użyć publicznego adresu IP. Upewnij się, że utworzono również sieciową grupę zabezpieczeń, która pomaga w zabezpieczaniu dostępu do maszyny wirtualnej.
     * **Sieciowa Grupa zabezpieczeń**: Aby uzyskać więcej informacji, zobacz [sterowanie przepływem ruchu sieciowego za pomocą sieciowych grup zabezpieczeń][virtual-networks-nsg].
   * **Rozszerzenia**: można zainstalować rozszerzenia maszyny wirtualnej, dodając je do wdrożenia. Nie musisz dodawać rozszerzenia w tym kroku. Rozszerzenia wymagane dla pomocy technicznej SAP są instalowane później. Zobacz rozdział [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5] w tym przewodniku.
   * **Wysoka dostępność**: Wybierz zestaw dostępności lub wprowadź parametry, aby utworzyć nowy zestaw dostępności. Aby uzyskać więcej informacji, zobacz [zestawy dostępności platformy Azure][planning-guide-3.2.3].
   * **Monitorowanie**
     * **Diagnostyka rozruchu**: możesz wybrać opcję **Wyłącz** dla diagnostyki rozruchu.
     * **Diagnostyka systemu operacyjnego gościa**: możesz wybrać opcję **Wyłącz** dla diagnostyki monitorowania.

1. **Podsumowanie**:

   Przejrzyj wybrane opcje, a następnie wybierz przycisk **OK**.

Twoja maszyna wirtualna jest wdrożona w wybranej grupie zasobów.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Tworzenie maszyny wirtualnej przy użyciu szablonu

Aby utworzyć wdrożenie przy użyciu obrazu prywatnego systemu operacyjnego z Azure Portal, użyj jednego z następujących szablonów SAP. Te szablony są publikowane w [repozytorium Azure-Start-templates][azure-quickstart-templates-github]w witrynie GitHub. Można również ręcznie utworzyć maszynę wirtualną przy użyciu [programu PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Konfiguracja dwuwarstwowa (tylko jeden szablon maszyny wirtualnej)** (system SAP-2-warstwowy użytkownika)][sap-templates-2-tier-user-image]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Konfiguracja dwuwarstwowa (tylko jedna maszyna wirtualna) obraz dysku zarządzanego przez szablon** (SAP-2-warstwowe-User-Image-MD)][sap-templates-2-tier-user-image-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i obrazu dysku zarządzanego, użyj tego szablonu.
* [**Konfiguracja trzech warstw (z wieloma maszynami wirtualnymi)** (system SAP-3-warstwowy-User-Image)][sap-templates-3-tier-user-image]

  Aby utworzyć system z trzema warstwami przy użyciu wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego, użyj tego szablonu.
* [**Konfiguracja trzech warstw (wiele maszyn wirtualnych) obraz dysku zarządzanego przez szablon** (SAP-3-warstwowe-User-Image-MD)][sap-templates-3-tier-user-image-md]

  Aby utworzyć system z trzema warstwami przy użyciu wielu maszyn wirtualnych lub własnego obrazu systemu operacyjnego oraz obrazu dysku zarządzanego, użyj tego szablonu.

W Azure Portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, która ma zostać użyta do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w subskrypcji.
   * **Lokalizacja**: gdzie należy wdrożyć szablon. W przypadku wybrania istniejącej grupy zasobów zostanie użyta lokalizacja tej grupy zasobów.
1. **Ustawienia**:
   * **Identyfikator systemu SAP**: identyfikator systemu SAP.
   * **Typ systemu** operacyjnego: typ systemu operacyjnego, który chcesz wdrożyć (system Windows lub Linux).
   * **Rozmiar systemu SAP**: rozmiar systemu SAP.

     Liczba punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
   * **Dostępność systemu** (tylko dla szablonu trójwarstwowej): dostępność systemu.

     Wybierz pozycję **ha** dla konfiguracji, która jest odpowiednia dla instalacji o wysokiej dostępności. Tworzone są dwa serwery baz danych i dwa serwery dla ASCS.
   * **Typ magazynu** (tylko dla szablonu dwuwarstwowego): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynów, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure SSD w warstwie Premium Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny wirtualnej na potrzeby wdrożeń RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Identyfikator URI dysku VHD obrazu użytkownika** (tylko szablon obrazu dysku niezarządzanego): identyfikator URI prywatnego dysku VHD obrazu systemu operacyjnego, na przykład https:// &lt; AccountName>. blob.Core.Windows.NET/VHDs/userimage.VHD.
   * **Konto magazynu obrazu użytkownika** (tylko szablon niezarządzanego obrazu dysku): nazwa konta magazynu, w którym jest przechowywany obraz prywatnego systemu operacyjnego, na przykład &lt; AccountName> w https:// &lt; accountname>. blob.Core.Windows.NET/VHDs/userimage.VHD.
   * **userImageId** (tylko szablon obrazu dysku zarządzanego): Identyfikator obrazu dysku zarządzanego, którego chcesz użyć
   * **Nazwa użytkownika administratora** i **hasło administratora**: Nazwa użytkownika i hasło.

     Nowy użytkownik zostanie utworzony w celu zalogowania się do maszyny wirtualnej.
   * **Nowa lub istniejąca podsieć**: określa, czy zostanie utworzona nowa sieć wirtualna i podsieć, czy używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Ten identyfikator zwykle wygląda następująco: &lt; Identyfikator subskrypcji/subscriptions/>/resourceGroups/nazwa &lt; grupy zasobów>/Providers/Microsoft.Network/virtualNetworks/ &lt; nazwa sieci wirtualnej>/subnets/ &lt; Nazwa podsieci>

1. Warunki **i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent-linux-only"></a>Zainstaluj agenta maszyny wirtualnej (tylko system Linux)

Aby można było używać szablonów opisanych w poprzedniej sekcji, Agent systemu Linux musi być już zainstalowany w obrazie użytkownika lub wdrożenie zakończy się niepowodzeniem. Pobierz i Zainstaluj agenta maszyny wirtualnej w obrazie użytkownika zgodnie z opisem w artykule [pobieranie, Instalowanie i włączanie agenta maszyny wirtualnej platformy Azure][deployment-guide-4.4]. Jeśli nie korzystasz z szablonów, możesz także zainstalować agenta maszyny wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym Active Directory lub wystąpieniem usługi DNS za pośrednictwem połączenia sieci VPN typu lokacja-lokacja lub platformy Azure ExpressRoute (jest to nazywane *różnymi* lokalizacjami na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłącza się do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego kroku, zobacz [dołączanie maszyny wirtualnej do domeny lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub ExpressRoute, maszyna wirtualna może nie być w stanie uzyskać dostępu do Internetu i nie będzie mogła pobrać wymaganych rozszerzeń maszyny wirtualnej ani zebrać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pomocą rozszerzenia SAP dla platformy Azure, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurowanie rozszerzenia maszyny wirtualnej platformy Azure dla oprogramowania SAP

Aby upewnić się, że SAP obsługuje Twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla oprogramowania SAP zgodnie z opisem w temacie [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5]. Zapoznaj się z wymaganiami wstępnymi dotyczącymi oprogramowania SAP oraz minimalnymi wersjami jądra SAP i agenta hosta SAP w zasobach wymienionych w obszarze [zasoby SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Sprawdzenie rozszerzenia maszyny wirtualnej SAP

Sprawdź, czy rozszerzenie maszyny wirtualnej dla oprogramowania SAP działa, zgodnie z opisem w [sprawdzenia i rozwiązywaniu problemów][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenariusz 3: przeniesienie lokalnej maszyny wirtualnej przy użyciu nieuogólnionego wirtualnego dysku twardego platformy Azure z oprogramowaniem SAP

W tym scenariuszu planujesz przenieść określony system SAP ze środowiska lokalnego na platformę Azure. Można to zrobić przez przekazanie wirtualnego dysku twardego z systemem operacyjnym, plików binarnych SAP i ostatecznie plików binarnych systemu DBMS oraz dysków VHD z plikami danych i dziennika w systemie DBMS do platformy Azure. W przeciwieństwie do scenariusza opisanego w [scenariuszu 2: Wdrażanie maszyny wirtualnej przy użyciu obrazu niestandardowego dla oprogramowania SAP][deployment-guide-3.3], w tym przypadku należy zachować nazwę hosta, identyfikator SID SAP i konta użytkowników SAP na maszynie wirtualnej platformy Azure, ponieważ zostały one skonfigurowane w środowisku lokalnym. Nie trzeba uogólniać systemu operacyjnego. Ten scenariusz występuje najczęściej w scenariuszach obejmujących wiele lokalizacji, w których część środowiska SAP w środowisku lokalnym działa na platformie Azure.

W tym scenariuszu Agent maszyny wirtualnej **nie** jest automatycznie instalowany podczas wdrażania. Ponieważ agent maszyny wirtualnej i rozszerzenie platformy Azure dla oprogramowania SAP są wymagane do uruchomienia oprogramowania SAP NetWeaver na platformie Azure, należy pobrać, zainstalować i włączyć oba składniki ręcznie po utworzeniu maszyny wirtualnej.

Aby uzyskać więcej informacji na temat agenta maszyny wirtualnej platformy Azure, zobacz następujące zasoby.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> [Omówienie agenta maszyny wirtualnej platformy Azure][virtual-machines-windows-agent-user-guide]
>
> ![Logo systemu Linux.][Logo_Linux] Linux
>
> [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux][virtual-machines-linux-agent-user-guide]
>
>

---

Poniższy schemat blokowy przedstawia sekwencję czynności związanych z przeniesieniem lokalnej maszyny wirtualnej za pomocą nieuogólnionego wirtualnego dysku twardego platformy Azure:

![Schemat blokowy wdrożenia maszyny wirtualnej dla systemów SAP przy użyciu dysku maszyny wirtualnej][deployment-guide-figure-400]

Jeśli dysk został już przekazany i zdefiniowany na platformie Azure (zobacz temat [azure Virtual Machines Planning and implementation for SAP NetWeaver][planning-guide]), wykonaj zadania opisane w następnych kilku sekcjach.

#### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby utworzyć wdrożenie przy użyciu prywatnego dysku systemu operacyjnego za pośrednictwem Azure Portal, użyj szablonu SAP opublikowanego w [repozytorium GitHub Azure-Start-templates][azure-quickstart-templates-github]. Można również ręcznie utworzyć maszynę wirtualną przy użyciu programu PowerShell.

* [**Konfiguracja dwuwarstwowa (tylko jeden szablon maszyny wirtualnej)** (system SAP-2-warstwowy)][sap-templates-2-tier-os-disk]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej, użyj tego szablonu.
* [**Konfiguracja dwuwarstwowa (tylko jedna maszyna wirtualna) dysk zarządzany przez szablon** (SAP-2-warstwowe-User-Disk-MD)][sap-templates-2-tier-os-disk-md]

  Aby utworzyć system dwuwarstwowy przy użyciu tylko jednej maszyny wirtualnej i dysku zarządzanego, użyj tego szablonu.

W Azure Portal wprowadź następujące parametry szablonu:

1. **Podstawy**:
   * **Subskrypcja**: subskrypcja używana do wdrożenia szablonu.
   * **Grupa zasobów**: Grupa zasobów, która ma zostać użyta do wdrożenia szablonu. Można utworzyć nową grupę zasobów lub wybrać istniejącą grupę zasobów w subskrypcji.
   * **Lokalizacja**: gdzie należy wdrożyć szablon. W przypadku wybrania istniejącej grupy zasobów zostanie użyta lokalizacja tej grupy zasobów.
1. **Ustawienia**:
   * **Identyfikator systemu SAP**: identyfikator systemu SAP.
   * **Typ systemu** operacyjnego: typ systemu operacyjnego, który chcesz wdrożyć (system Windows lub Linux).
   * **Rozmiar systemu SAP**: rozmiar systemu SAP.

     Liczba punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
   * **Typ magazynu** (tylko dla szablonu dwuwarstwowego): typ magazynu do użycia.

     W przypadku większych systemów zdecydowanie zalecamy korzystanie z usługi Azure Premium Storage. Aby uzyskać więcej informacji na temat typów magazynów, zobacz następujące zasoby:
      * [Korzystanie z usługi Azure SSD w warstwie Premium Storage dla wystąpienia SAP DBMS][2367194]
      * [Struktura magazynu maszyny wirtualnej na potrzeby wdrożeń RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure][storage-premium-storage-preview-portal]
      * [Wprowadzenie do usługi Microsoft Azure Storage][storage-introduction]
   * **Identyfikator URI dysku VHD dysków systemu operacyjnego** (tylko szablon dysku niezarządzanego): identyfikator URI prywatnego dysku systemu operacyjnego, na przykład https:// &lt; AccountName>. blob.Core.Windows.NET/VHDs/OSDisk.VHD.
   * **Identyfikator dysku zarządzanego dysku systemu operacyjnego** (tylko szablon dysku zarządzanego): identyfikator dysku systemu operacyjnego dysku zarządzanego,/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/Group/Providers/Microsoft.COMPUTE/disks/win
   * **Nowa lub istniejąca podsieć**: określa, czy zostanie utworzona nowa sieć wirtualna i podsieć, czy też używana jest istniejąca podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
   * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Ten identyfikator zwykle wygląda następująco: &lt; Identyfikator subskrypcji/subscriptions/>/resourceGroups/nazwa &lt; grupy zasobów>/Providers/Microsoft.Network/virtualNetworks/ &lt; nazwa sieci wirtualnej>/subnets/ &lt; Nazwa podsieci>

1. Warunki **i postanowienia**:  
    Przejrzyj i zaakceptuj postanowienia prawne.

1. Wybierz pozycję **Kup**.

#### <a name="install-the-vm-agent"></a>Zainstaluj agenta maszyny wirtualnej

Aby można było używać szablonów opisanych w poprzedniej sekcji, Agent maszyny wirtualnej musi być zainstalowany na dysku systemu operacyjnego lub wdrożenie zakończy się niepowodzeniem. Pobierz i Zainstaluj agenta maszyny wirtualnej na maszynie wirtualnej, zgodnie z opisem w artykule [pobieranie, Instalowanie i włączanie agenta maszyny wirtualnej platformy Azure][deployment-guide-4.4].

Jeśli nie używasz szablonów opisanych w poprzedniej sekcji, można także zainstalować agenta maszyny wirtualnej później.

#### <a name="join-a-domain-windows-only"></a>Przyłącz do domeny (tylko system Windows)

Jeśli wdrożenie platformy Azure jest połączone z lokalnym Active Directory lub wystąpieniem usługi DNS za pośrednictwem połączenia sieci VPN typu lokacja-lokacja lub usługi ExpressRoute (jest to nazywane *różnymi* lokalizacjami na [platformie Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP NetWeaver][planning-guide]), oczekuje się, że maszyna wirtualna przyłącza się do domeny lokalnej. Aby uzyskać więcej informacji na temat zagadnień dotyczących tego zadania, zobacz [dołączanie maszyny wirtualnej do domeny lokalnej (tylko system Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

W zależności od konfiguracji sieci lokalnej może być konieczne skonfigurowanie serwera proxy na maszynie wirtualnej. Jeśli maszyna wirtualna jest połączona z siecią lokalną za pośrednictwem sieci VPN lub ExpressRoute, maszyna wirtualna może nie być w stanie uzyskać dostępu do Internetu i nie będzie mogła pobrać wymaganych rozszerzeń maszyny wirtualnej ani zebrać informacji o infrastrukturze platformy Azure dla agenta hosta SAP za pomocą rozszerzenia SAP dla platformy Azure, zobacz [Konfigurowanie serwera proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurowanie rozszerzenia maszyny wirtualnej platformy Azure dla oprogramowania SAP

Aby upewnić się, że SAP obsługuje Twoje środowisko, skonfiguruj rozszerzenie platformy Azure dla oprogramowania SAP zgodnie z opisem w temacie [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5]. Zapoznaj się z wymaganiami wstępnymi dotyczącymi oprogramowania SAP oraz minimalnymi wersjami jądra SAP i agenta hosta SAP w zasobach wymienionych w obszarze [zasoby SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Sprawdzenie maszyn wirtualnych SAP

Sprawdź, czy rozszerzenie maszyny wirtualnej dla oprogramowania SAP działa, zgodnie z opisem w [sprawdzenia i rozwiązywaniu problemów][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualizowanie konfiguracji rozszerzenia platformy Azure dla oprogramowania SAP

Zaktualizuj konfigurację rozszerzenia platformy Azure dla oprogramowania SAP w jednym z następujących scenariuszy:
* Wspólny zespół firmy Microsoft/SAP rozszerza możliwości rozszerzenia maszyny wirtualnej i żąda więcej lub mniej liczników.
* Firma Microsoft wprowadza nową wersję podstawowej infrastruktury platformy Azure, która dostarcza dane, a rozszerzenie platformy Azure dla oprogramowania SAP musi zostać dostosowane do tych zmian.
* Instalowanie dodatkowych dysków danych na maszynie wirtualnej platformy Azure lub usuwanie dysku z danymi. W tym scenariuszu należy zaktualizować kolekcję danych związanych z magazynem. Zmiana konfiguracji przez dodanie lub usunięcie punktów końcowych lub przypisanie adresów IP do maszyny wirtualnej nie ma wpływu na konfigurację rozszerzenia.
* Możesz zmienić rozmiar maszyny wirtualnej platformy Azure, na przykład z rozmiaru A5 na inny rozmiar maszyny wirtualnej.
* Dodajesz nowe interfejsy sieciowe do maszyny wirtualnej platformy Azure.

Aby zaktualizować ustawienia, zaktualizuj konfigurację rozszerzenia platformy Azure dla oprogramowania SAP, wykonując czynności opisane w sekcji [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Szczegółowe zadania wdrażania oprogramowania SAP

Ta sekcja zawiera szczegółowe instrukcje dotyczące wykonywania określonych zadań w procesie konfiguracji i wdrażania.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Wdrażanie poleceń cmdlet Azure PowerShell

Wykonaj kroki opisane w artykule [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)

Sprawdzaj często aktualizacje poleceń cmdlet programu PowerShell, które zwykle są aktualizowane co miesiąc. Wykonaj kroki opisane w [tym](/powershell/azure/install-az-ps#update-the-azure-powershell-module) artykule. O ile nie określono inaczej w programie SAP Uwaga [1928533] lub sap Uwaga [2015553], zalecamy korzystanie z najnowszej wersji Azure PowerShell poleceń cmdlet.

Aby sprawdzić wersję poleceń cmdlet Azure PowerShell zainstalowanych na komputerze, uruchom następujące polecenie programu PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Wdrażanie interfejsu wiersza polecenia platformy Azure

Wykonaj kroki opisane w artykule [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

Sprawdzaj często aktualizacje interfejsu wiersza polecenia platformy Azure, które zwykle są aktualizowane co miesiąc.

Aby sprawdzić wersję interfejsu wiersza polecenia platformy Azure, która jest zainstalowana na komputerze, uruchom następujące polecenie:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Przyłączanie maszyny wirtualnej do domeny lokalnej (tylko system Windows)

W przypadku wdrażania maszyn wirtualnych SAP w scenariuszu obejmującym wiele lokalizacji, w którym lokalne Active Directory i system DNS są rozszerzane na platformie Azure, oczekuje się, że maszyny wirtualne przyłączają się do domeny lokalnej. Szczegółowe kroki, które należy wykonać w celu przyłączenia maszyny wirtualnej do domeny lokalnej oraz dodatkowego oprogramowania wymaganego jako członek domeny lokalnej, różnią się w zależności od klienta. Zwykle w celu przyłączenia maszyny wirtualnej do domeny lokalnej należy zainstalować dodatkowe oprogramowanie, takie jak oprogramowanie chroniące przed złośliwym kodem oraz oprogramowanie do tworzenia kopii zapasowych i monitorowania.

W tym scenariuszu należy również upewnić się, że jeśli ustawienia internetowego serwera proxy są wymuszane w przypadku dołączenia maszyny wirtualnej do domeny w środowisku, konto systemu lokalnego systemu Windows (S-1-5-18) na maszynie wirtualnej gościa ma te same ustawienia serwera proxy. Najłatwiej jest wymusić serwer proxy przy użyciu zasady grupy domeny, która ma zastosowanie do systemów w domenie.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Pobieranie, Instalowanie i włączanie agenta maszyny wirtualnej platformy Azure

W przypadku maszyn wirtualnych, które są wdrożone z obrazu systemu operacyjnego, który nie jest uogólniony (na przykład obraz, który nie pochodzi z przygotowania systemu Windows lub narzędzia Sysprep, narzędzie), należy ręcznie pobrać, zainstalować i włączyć agenta maszyny wirtualnej platformy Azure.

W przypadku wdrażania maszyny wirtualnej z poziomu portalu Azure Marketplace ten krok nie jest wymagany. Obrazy z witryny Azure Marketplace mają już agenta maszyny wirtualnej platformy Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Pobierz agenta maszyny wirtualnej platformy Azure:
   1.  Pobierz [pakiet instalatora agenta maszyny wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Przechowuj pakiet MSI agenta maszyny wirtualnej lokalnie na komputerze osobistym lub serwerze.
1. Zainstaluj agenta maszyny wirtualnej platformy Azure:
   1.  Połącz się ze wdrożoną maszyną wirtualną platformy Azure przy użyciu Remote Desktop Protocol (RDP).
   1.  Otwórz okno Eksploratora Windows na maszynie wirtualnej i wybierz katalog docelowy dla pliku MSI agenta maszyny wirtualnej.
   1.  Przeciągnij plik MSI instalatora agenta maszyny wirtualnej platformy Azure z lokalnego komputera/serwera do katalogu docelowego agenta maszyny wirtualnej na maszynie wirtualnej.
   1.  Kliknij dwukrotnie plik MSI na maszynie wirtualnej.
1. W przypadku maszyn wirtualnych, które są dołączone do domen lokalnych, należy się upewnić, że ustawienia internetowego serwera proxy mają również zastosowanie do konta systemu lokalnego systemu Windows (S-1-5-18) na maszynie wirtualnej, zgodnie z opisem w temacie [Konfigurowanie serwera proxy][deployment-guide-configure-proxy]. Agent maszyny wirtualnej działa w tym kontekście i musi mieć możliwość nawiązania połączenia z platformą Azure.

Do zaktualizowania agenta maszyny wirtualnej platformy Azure nie jest wymagana żadna interakcja ze strony użytkownika. Agent maszyny wirtualnej jest automatycznie aktualizowany i nie wymaga ponownego uruchomienia maszyny wirtualnej.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Użyj następujących poleceń, aby zainstalować agenta maszyny wirtualnej dla systemu Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) lub Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Jeśli Agent jest już zainstalowany, aby zaktualizować agenta systemu Azure Linux, wykonaj kroki opisane w artykule [Aktualizowanie agenta systemu Linux platformy Azure na maszynie wirtualnej do najnowszej wersji z usługi GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurowanie serwera proxy

Kroki, które należy wykonać w celu skonfigurowania serwera proxy w systemie Windows, różnią się od sposobu konfigurowania serwera proxy na platformie Linux.

#### <a name="windows"></a>Windows

Aby można było uzyskać dostęp do Internetu, należy prawidłowo skonfigurować ustawienia serwera proxy dla lokalnego konta systemowego. Jeśli ustawienia serwera proxy nie są skonfigurowane przez zasady grupy, można skonfigurować ustawienia dla lokalnego konta systemowego.

1. Przejdź do **menu Start**, wprowadź **gpedit. msc**, a następnie wybierz **klawisz ENTER**.
1. Wybierz pozycję **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **Internet Explorer**. Upewnij się, że ustawienie **Ustaw ustawienia serwera proxy na maszynę (zamiast użytkownika)** jest wyłączone lub nieskonfigurowane.
1. W **Panelu sterowania** przejdź do opcji **internetowe centrum sieci i udostępniania**  >  .
1. Na karcie **połączenia** wybierz przycisk **Ustawienia sieci LAN** .
1. Wyczyść pole wyboru **Automatycznie wykryj ustawienia**.
1. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN** , a następnie wprowadź adres i port serwera proxy.
1. Wybierz przycisk **Zaawansowane** .
1. W polu **wyjątki** wprowadź adres IP **168.63.129.16**. Wybierz przycisk **OK**.

#### <a name="linux"></a>Linux

Skonfiguruj poprawny serwer proxy w pliku konfiguracji agenta gościa Microsoft Azure, który znajduje się na liście \\ etc \\ waagent. conf.

Ustaw następujące parametry:

1. **Host serwera proxy HTTP**. Na przykład ustaw go na **proxy. Corp. Local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Port serwera proxy HTTP**. Na przykład ustaw dla niego wartość **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Uruchom ponownie agenta.

   ```console
   sudo service waagent restart
   ```

Ustawienia serwera proxy w \\ etc \\ waagent. conf dotyczą również wymaganych rozszerzeń maszyn wirtualnych. Jeśli chcesz używać repozytoriów platformy Azure, upewnij się, że ruch do tych repozytoriów nie przechodzi przez lokalny intranet. Jeśli utworzono trasy zdefiniowane przez użytkownika w celu włączenia tunelowania wymuszonego, należy dodać trasę, która kieruje ruch do repozytoriów bezpośrednio do Internetu, a nie za pośrednictwem połączenia sieci VPN typu lokacja-lokacja.

* **SLES**

  Należy również dodać trasy dla adresów IP wymienionych w liście \\ etc \\ regionserverclnt. cfg. Na poniższej ilustracji przedstawiono przykład:

  ![Wymuszone tunelowanie][deployment-guide-figure-50]


* **RHEL**

  Należy również dodać trasy dla adresów IP hostów wymienionych w liście \\ etc \\ yum. repozytoria. d \\ rhui-równoważenia obciążenia. Aby zapoznać się z przykładem, zapoznaj się z poprzednią ilustracją.

* **Oracle Linux**

  Brak repozytoriów dla Oracle Linux na platformie Azure. Musisz skonfigurować własne repozytoria do Oracle Linux lub użyć publicznych repozytoriów.

Aby uzyskać więcej informacji o trasach zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Skonfiguruj rozszerzenie platformy Azure dla oprogramowania SAP

> [!NOTE]
> Ogólna Instrukcja pomocy technicznej:  
> Obsługa rozszerzenia platformy Azure dla oprogramowania SAP jest dostępna za pomocą kanałów pomocy technicznej SAP. Jeśli potrzebujesz pomocy przy rozszerzeniu platformy Azure dla oprogramowania SAP, Otwórz przypadek pomocy technicznej z [obsługą SAP](https://support.sap.com/). 

Po przygotowaniu maszyny wirtualnej zgodnie z opisem w [scenariuszach wdrażania maszyn wirtualnych dla oprogramowania SAP na platformie Azure][deployment-guide-3], Agent maszyny wirtualnej platformy Azure jest zainstalowany na tej maszynie. Następnym krokiem jest wdrożenie rozszerzenia platformy Azure dla oprogramowania SAP, które jest dostępne w repozytorium rozszerzeń platformy Azure w globalnych centrach danych platformy Azure. Aby uzyskać więcej informacji, zobacz temat [Azure Virtual Machines Planning and implementation for SAP NetWeaver][planning-guide-9.1].

Jesteśmy w trakcie zwalniania nowej wersji rozszerzenia platformy Azure dla oprogramowania SAP. Nowe rozszerzenie używa przypisanej do systemu tożsamości maszyny wirtualnej w celu uzyskania informacji o podłączonych dyskach, interfejsach sieciowych i maszynie wirtualnej. Aby można było uzyskać dostęp do tych zasobów, tożsamość systemowa maszyny wirtualnej musi mieć uprawnienie czytelnik dla maszyny wirtualnej, dysku systemu operacyjnego, dysków danych i interfejsów sieciowych. Obecnie zalecamy zainstalowanie nowego rozszerzenia w następujących scenariuszach:

1. Chcesz zainstalować rozszerzenie z Terraform, szablonami Azure Resource Manager lub innymi sposobami niż interfejs wiersza polecenia platformy Azure lub Azure PowerShell
1. Chcesz zainstalować rozszerzenie w systemie SUSE SLES 15 lub nowszym.
1. Pomoc techniczna firmy Microsoft lub SAP prosi o zainstalowanie nowego rozszerzenia
1. Chcesz użyć usługi Azure Ultra Disk lub standardowej Managed Disks

W tych scenariuszach wykonaj kroki opisane w rozdziale [Konfigurowanie nowego rozszerzenia platformy Azure dla oprogramowania SAP z Azure PowerShell][deployment-guide-configure-new-extension-ps] Azure PowerShell lub [Skonfiguruj nowe rozszerzenie platformy Azure dla oprogramowania SAP za pomocą interfejsu wiersza polecenia platformy][deployment-guide-configure-new-extension-cli] Azure dla interfejsu wiersza polecenia platformy Azure.

Wykonaj instrukcje [Azure PowerShell][deployment-guide-4.5.1] lub [interfejsu wiersza polecenia platformy Azure][deployment-guide-4.5.2] w celu zainstalowania i skonfigurowania standardowej wersji rozszerzenia platformy Azure dla oprogramowania SAP.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell dla maszyn wirtualnych z systemem Linux i Windows

Aby zainstalować rozszerzenie platformy Azure dla oprogramowania SAP przy użyciu programu PowerShell:

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet Azure PowerShell. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure PowerShell poleceń cmdlet][deployment-guide-4.1].  
1. Uruchom następujące polecenie programu PowerShell.
    Aby uzyskać listę dostępnych środowisk, uruchom polecenie cmdlet `Get-AzEnvironment` . Jeśli chcesz korzystać z globalnej platformy Azure, środowisko to **AzureCloud**. W przypadku platformy Azure z Chin wybierz pozycję **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Po wprowadzeniu danych konta skrypt wdraża wymagane rozszerzenia i włącza wymagane funkcje. Może to potrwać kilka minut.
Aby uzyskać więcej informacji na temat `Set-AzVMAEMExtension` , zobacz [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Pomyślne wykonanie polecenia cmdlet platformy Azure określonego dla oprogramowania SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension`Konfiguracja wykonuje wszystkie kroki konfigurowania zbierania danych hosta dla oprogramowania SAP.

Dane wyjściowe skryptu zawierają następujące informacje:

* Potwierdź, że skonfigurowano zbieranie danych dla dysku systemu operacyjnego i wszystkich dodatkowych dysków z danymi.
* Poniższe dwa komunikaty potwierdzają konfigurację metryk magazynu dla określonego konta magazynu.
* Jeden wiersz danych wyjściowych daje status rzeczywistej aktualizacji rozszerzenia maszyny wirtualnej na potrzeby konfiguracji oprogramowania SAP.
* Inny wiersz danych wyjściowych potwierdza, że konfiguracja została wdrożona lub zaktualizowana.
* Ostatni wiersz danych wyjściowych jest informacyjny. Przedstawiono opcje testowania rozszerzenia maszyny wirtualnej dla konfiguracji SAP.
* Aby sprawdzić, czy wszystkie kroki rozszerzenia maszyny wirtualnej platformy Azure dla konfiguracji oprogramowania SAP zostały wykonane pomyślnie, a infrastruktura platformy Azure udostępnia niezbędne dane, Kontynuuj sprawdzanie gotowości dla rozszerzenia platformy Azure dla oprogramowania SAP, zgodnie z opisem w sekcji [sprawdzanie gotowości dla rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-5.1].
* Poczekaj 15-30 minut, aby Diagnostyka Azure zebrać odpowiednie dane.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Interfejs wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux

Aby zainstalować rozszerzenie platformy Azure dla oprogramowania SAP przy użyciu interfejsu wiersza polecenia platformy Azure:

   1. Zainstaluj klasyczny interfejs wiersza polecenia platformy Azure, zgodnie z opisem w artykule [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure][azure-cli].
   1. Zaloguj się przy użyciu konta platformy Azure:

      ```console
      azure login
      ```

   1. Przełącz do trybu Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Włącz rozszerzenie platformy Azure dla oprogramowania SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalowanie przy użyciu interfejsu wiersza polecenia platformy Azure 2,0

   1. Zainstaluj interfejs wiersza polecenia platformy Azure 2,0, zgodnie z opisem w temacie [Instalowanie interfejsu wiersza polecenia platformy azure 2,0][azure-cli-2].
   1. Zaloguj się przy użyciu konta platformy Azure:

      ```azurecli
      az login
      ```

   1. Zainstaluj rozszerzenie AEM interfejsu wiersza polecenia platformy Azure
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Zainstaluj rozszerzenie za pomocą
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Sprawdź, czy rozszerzenie platformy Azure dla oprogramowania SAP jest aktywne na maszynie wirtualnej platformy Azure z systemem Linux. Sprawdź, czy plik \\ Var \\ lib \\ AzureEnhancedMonitor \\ PerfCounters istnieje. Jeśli istnieje, w wierszu polecenia Uruchom to polecenie, aby wyświetlić informacje zbierane przez rozszerzenie platformy Azure dla oprogramowania SAP:

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

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Skonfiguruj nowe rozszerzenie platformy Azure dla oprogramowania SAP za pomocą Azure PowerShell

Nowe rozszerzenie maszyny wirtualnej dla oprogramowania SAP używa zarządzanej tożsamości przypisanej do maszyny wirtualnej w celu uzyskania dostępu do danych monitorowania i konfiguracji maszyny wirtualnej. Aby zainstalować nowe rozszerzenie platformy Azure dla oprogramowania SAP przy użyciu programu PowerShell, musisz najpierw przypisać taką tożsamość do maszyny wirtualnej i udzielić tej tożsamości dostępu do wszystkich zasobów używanych przez tę maszynę wirtualną, na przykład dysków i interfejsów sieciowych.

> [!NOTE]
> Poniższe kroki wymagają uprawnień właściciela do grupy zasobów lub poszczególnych zasobów (maszyn wirtualnych, dysków danych itp.).

1. Upewnij się, że używasz agenta hosta SAP 7,21 PL 47 lub nowszego.
1. Upewnij się, że odinstalowano bieżącą wersję rozszerzenia maszyny wirtualnej dla oprogramowania SAP. Instalacja obu wersji rozszerzenia maszyny wirtualnej dla oprogramowania SAP na tej samej maszynie wirtualnej nie jest obsługiwana.
1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet Azure PowerShell (co najmniej 4.3.0). Aby uzyskać więcej informacji, zobacz [wdrażanie Azure PowerShell poleceń cmdlet][deployment-guide-4.1].
1. Uruchom następujące polecenie programu PowerShell.
    Aby uzyskać listę dostępnych środowisk, uruchom polecenie cmdlet `Get-AzEnvironment` . Jeśli chcesz korzystać z globalnej platformy Azure, środowisko to **AzureCloud**. W przypadku platformy Azure z Chin wybierz pozycję **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Skonfiguruj nowe rozszerzenie platformy Azure dla oprogramowania SAP za pomocą interfejsu wiersza polecenia platformy Azure

Nowe rozszerzenie maszyny wirtualnej dla oprogramowania SAP używa zarządzanej tożsamości przypisanej do maszyny wirtualnej w celu uzyskania dostępu do danych monitorowania i konfiguracji maszyny wirtualnej. Aby zainstalować nowe rozszerzenie platformy Azure dla oprogramowania SAP przy użyciu interfejsu wiersza polecenia platformy Azure, musisz najpierw przypisać taką tożsamość do maszyny wirtualnej i udzielić tej tożsamości dostępu do wszystkich zasobów używanych przez tę maszynę wirtualną, na przykład dyski i interfejsy sieciowe.

> [!NOTE]
> Poniższe kroki wymagają uprawnień właściciela do grupy zasobów lub poszczególnych zasobów (maszyn wirtualnych, dysków danych itp.).

1. Upewnij się, że używasz agenta hosta SAP 7,21 PL 47 lub nowszego.
1. Upewnij się, że odinstalowano bieżącą wersję rozszerzenia maszyny wirtualnej dla oprogramowania SAP. Instalacja obu wersji rozszerzenia maszyny wirtualnej dla oprogramowania SAP na tej samej maszynie wirtualnej nie jest obsługiwana.
1. Zainstaluj interfejs wiersza polecenia platformy Azure 2,0, zgodnie z opisem w temacie [Instalowanie interfejsu wiersza polecenia platformy azure 2,0][azure-cli-2].

1. Zaloguj się przy użyciu konta platformy Azure:

   ```azurecli
   az login
   ```

1. Wykonaj kroki opisane w artykule [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure][qs-configure-cli-windows-vm] , aby włączyć System-Assigned tożsamość zarządzaną dla maszyny wirtualnej. User-Assigned tożsamości zarządzane nie są obsługiwane przez rozszerzenie maszyny wirtualnej dla oprogramowania SAP. Można jednak włączyć zarówno tożsamość przypisaną przez system, jak i przypisanej do użytkownika.

   Przykład:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. Przypisz dostęp do tożsamości zarządzanej do grupy zasobów maszyny wirtualnej lub wszystkich interfejsów sieciowych, dysków zarządzanych i maszyny wirtualnej zgodnie z opisem w temacie [przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure][howto-assign-access-cli]

    Przykład:

    ```azurecli
    # Azure CLI on Linux
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId

    # Azure CLI on Windows/PowerShell
    $spID=az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines
    $rgId=az group show -g <resource-group-name> --query id --out tsv
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zainstalować rozszerzenie platformy Azure dla oprogramowania SAP.
    Rozszerzenie jest obecnie obsługiwane tylko w AzureCloud. Platformy Azure z Chin, Azure Government lub innych specjalnych środowisk nie są jeszcze obsługiwane.

    ```azurecli
    # Azure CLI on Linux
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    # Azure CLI on Windows/PowerShell
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Sprawdzenia i rozwiązywanie problemów

Po wdrożeniu maszyny wirtualnej platformy Azure i skonfigurowaniu odpowiedniego rozszerzenia platformy Azure dla oprogramowania SAP Sprawdź, czy wszystkie składniki rozszerzenia działają zgodnie z oczekiwaniami.

Uruchom sprawdzenie gotowości dla rozszerzenia platformy Azure dla oprogramowania SAP zgodnie z opisem w temacie [gotowość sprawdza rozszerzenie platformy Azure dla oprogramowania SAP][deployment-guide-5.1]. Jeśli wszystkie wyniki kontroli gotowości są pozytywne i wszystkie odpowiednie liczniki wydajności pojawiają się prawidłowo, rozszerzenie platformy Azure dla oprogramowania SAP zostało pomyślnie skonfigurowane. Można kontynuować instalację agenta hosta SAP zgodnie z opisem w temacie SAP uwagi w temacie [zasoby SAP][deployment-guide-2.2]. Jeśli sprawdzenie gotowości wskazuje, że brakuje liczników, należy uruchomić kontrolę kondycji rozszerzenia platformy Azure dla oprogramowania SAP, zgodnie z opisem w temacie [Sprawdzanie kondycji dla rozszerzenia Azure Extension for SAP Configuration][deployment-guide-5.2]. Aby uzyskać więcej opcji rozwiązywania problemów, zobacz [Rozwiązywanie problemów z usługą Azure Extension dla oprogramowania SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Sprawdzenie gotowości dla rozszerzenia platformy Azure dla oprogramowania SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje domyślne rozszerzenie maszyny wirtualnej. Jeśli zainstalowano nowe rozszerzenie maszyny wirtualnej, zobacz temat [gotowość działu check dla nowego rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-5.1-new]

Ten test sprawdza, czy wszystkie metryki wydajności, które znajdują się w aplikacji SAP, są dostarczane przez bazowe rozszerzenie platformy Azure dla oprogramowania SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Uruchamianie sprawdzania gotowości na maszynie wirtualnej z systemem Windows

1. Zaloguj się do maszyny wirtualnej platformy Azure (przy użyciu konta administratora nie jest konieczne).
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia Zmień katalog na folder instalacyjny rozszerzenia platformy Azure dla SAP: C: \\ pakiety \\ dodatki \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; wersja>\\ upuoć

   *Wersja* w ścieżce do rozszerzenia może się różnić. Jeśli widzisz foldery dla wielu wersji rozszerzenia w folderze instalacyjnym, sprawdź konfigurację usługi AzureEnhancedMonitoring systemu Windows, a następnie przejdź do folderu wskazanego jako *ścieżka do pliku wykonywalnego*.

   ![Właściwości usługi uruchamiające rozszerzenie platformy Azure dla oprogramowania SAP][deployment-guide-figure-1000]

1. W wierszu polecenia Uruchom **azperflib.exe** bez żadnych parametrów.

   > [!NOTE]
   > Azperflib.exe działa w pętli i aktualizuje zebrane liczniki co 60 sekund. Aby zakończyć pętlę, Zamknij okno wiersza polecenia.
   >
   >

Jeśli rozszerzenie platformy Azure dla oprogramowania SAP nie jest zainstalowane lub usługa AzureEnhancedMonitoring nie jest uruchomiona, rozszerzenie nie zostało poprawnie skonfigurowane. Aby uzyskać szczegółowe informacje na temat sposobu wdrażania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe jest składnikiem, który nie może być używany do celów własnych. Jest to składnik, który udostępnia dane infrastruktury platformy Azure związane z maszyną wirtualną wyłącznie dla agenta hosta SAP.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Sprawdź dane wyjściowe azperflib.exe

Azperflib.exe dane wyjściowe pokazują wszystkie wypełnione liczniki wydajności platformy Azure dla oprogramowania SAP. W dolnej części listy zebranych liczników wskaźnik podsumowanie i kondycja przedstawia stan rozszerzenia platformy Azure dla oprogramowania SAP.

![Dane wyjściowe kontroli kondycji przez wykonanie azperflib.exe, co oznacza, że nie istnieją żadne problemy][deployment-guide-figure-1100]
<a name="figure-11"></a>

Sprawdź wynik zwrócony dla **liczników łącznych** danych wyjściowych, które są raportowane jako puste i dla **stanu kondycji**, pokazane na powyższym rysunku.

Interpretuj otrzymane wartości w następujący sposób:

| Azperflib.exe wartości wyników | Rozszerzenie platformy Azure dla stanu kondycji oprogramowania SAP |
| --- | --- |
| **Wywołania interfejsu API — niedostępne** | Niedostępne liczniki mogą nie dotyczyć konfiguracji maszyny wirtualnej lub są błędy. Zobacz **stan kondycji**. |
| **Łączna liczba liczników — puste** |Następujące dwa liczniki magazynu platformy Azure mogą być puste: <ul><li>Serwer opóźnienia operacji odczytu magazynu (MS)</li><li>Opóźnienie operacji odczytu magazynu E2E MS</li></ul>Wszystkie inne liczniki muszą mieć wartości. |
| **Stan kondycji** |Tylko OK, jeśli stan powrotu zostanie wyświetlony **OK**. |
| **Diagnostyka** |Szczegółowe informacje o stanie kondycji. |

Jeśli **stan kondycji** nie jest **prawidłowy**, postępuj zgodnie z instrukcjami w temacie [Sprawdzanie kondycji dla usługi Azure Extension for SAP Configuration][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Uruchamianie sprawdzania gotowości na maszynie wirtualnej z systemem Linux

1. Połącz się z maszyną wirtualną platformy Azure przy użyciu protokołu SSH.

1. Sprawdź dane wyjściowe rozszerzenia platformy Azure dla oprogramowania SAP.

   a.  Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Oczekiwany wynik**: zwraca listę liczników wydajności. Plik nie powinien być pusty.

   b. Uruchom polecenie `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Oczekiwany wynik**: zwraca jeden wiersz, w którym wystąpił **błąd, na** przykład **3; config; Błąd;; 0; 0; Brak; 0; 1456416792; TST-servercs;**

   c. Uruchom polecenie `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Oczekiwany wynik**: zwraca wartość pustą lub nie istnieje.

Jeśli poprzednie sprawdzenie zakończyło się niepowodzeniem, uruchom następujące dodatkowe sprawdzenia:

1. Upewnij się, że waagent jest zainstalowana i włączona.

   a.  Uruchom polecenie `sudo ls -al /var/lib/waagent/`

     **Oczekiwany wynik**: wyświetla zawartość katalogu waagent.

   b.  Uruchom polecenie `ps -ax | grep waagent`

   **Oczekiwany wynik**: zawiera jeden wpis podobny do: `python /usr/sbin/waagent -daemon`

1. Upewnij się, że rozszerzenie platformy Azure dla oprogramowania SAP jest zainstalowane i uruchomione.

   a.  Uruchom polecenie `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Oczekiwany wynik**: wyświetla zawartość rozszerzenia platformy Azure dla katalogu SAP.

   b. Uruchom polecenie `ps -ax | grep AzureEnhanced`

   **Oczekiwany wynik**: zawiera jeden wpis podobny do: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Zainstaluj agenta hosta SAP zgodnie z opisem w artykule SAP Uwaga [1031096]i sprawdź dane wyjściowe `saposcol` .

   a.  Uruchom polecenie `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Uruchom polecenie `dump ccm`

   c.  Sprawdź, czy **Virtualization_Configuration Metryka dostępu \Enhanced monitorowania** ma **wartość true**.

Jeśli masz już zainstalowany serwer aplikacji SAP NetWeaver ABAP, Otwórz transakcję ST06 i sprawdź, czy monitorowanie jest włączone.

Jeśli którykolwiek z tych sprawdzeń zakończy się niepowodzeniem i szczegółowe informacje o sposobie ponownego wdrażania rozszerzenia, zobacz [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Sprawdzenie gotowości nowego rozszerzenia platformy Azure dla oprogramowania SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje nowe rozszerzenie maszyny wirtualnej. Jeśli zainstalowano domyślne rozszerzenie maszyny wirtualnej, zobacz sekcję [gotowość do sprawdzenia pod kątem rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-5.1].

Ten test sprawdza, czy wszystkie metryki wydajności, które znajdują się w aplikacji SAP, są dostarczane przez bazowe rozszerzenie platformy Azure dla oprogramowania SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Uruchamianie sprawdzania gotowości na maszynie wirtualnej z systemem Windows

1. Zaloguj się do maszyny wirtualnej platformy Azure (przy użyciu konta administratora nie jest konieczne).
1. Otwórz przeglądarkę internetową i przejdź do http://127.0.0.1:11812/azure4sap/metrics
1. Przeglądarka powinna wyświetlić lub pobrać plik XML zawierający dane monitorowania maszyny wirtualnej. Jeśli tak się nie dzieje, upewnij się, że rozszerzenie platformy Azure dla oprogramowania SAP jest zainstalowane.

##### <a name="check-the-content-of-the-xml-file"></a>Sprawdź zawartość pliku XML

Plik XML, do którego można uzyskać dostęp, http://127.0.0.1:11812/azure4sap/metrics zawiera wszystkie wypełnione liczniki wydajności platformy Azure dla SAP. Zawiera również wskaźnik podsumowania i kondycji stanu rozszerzenia platformy Azure dla oprogramowania SAP.

Sprawdź wartość elementu **Opis kondycji dostawcy** . Jeśli wartość nie jest równa **OK**, postępuj zgodnie z instrukcjami podanymi w temacie [Sprawdzanie kondycji dla nowego rozszerzenia platformy Azure dla konfiguracji SAP][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Uruchamianie sprawdzania gotowości na maszynie wirtualnej z systemem Linux

1. Połącz się z maszyną wirtualną platformy Azure przy użyciu protokołu SSH.

1. Sprawdź dane wyjściowe następującego polecenia

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Oczekiwany wynik**: zwraca dokument XML zawierający informacje o monitorowaniu dla maszyny wirtualnej, jej dysków i interfejsów sieciowych.

Jeśli poprzednie sprawdzenie zakończyło się niepowodzeniem, uruchom następujące dodatkowe sprawdzenia:

1. Upewnij się, że waagent jest zainstalowana i włączona.

   a.  Uruchom polecenie `sudo ls -al /var/lib/waagent/`

     **Oczekiwany wynik**: wyświetla zawartość katalogu waagent.

   b.  Uruchom polecenie `ps -ax | grep waagent`

   **Oczekiwany wynik**: zawiera jeden wpis podobny do: `python /usr/sbin/waagent -daemon`

1. Upewnij się, że rozszerzenie platformy Azure dla oprogramowania SAP jest zainstalowane i uruchomione.

   a.  Uruchom polecenie `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Oczekiwany wynik**: wyświetla zawartość rozszerzenia platformy Azure dla katalogu SAP.

   b. Uruchom polecenie `ps -ax | grep AzureEnhanced`

   **Oczekiwany wynik**: zawiera jeden wpis podobny do: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Zainstaluj agenta hosta SAP zgodnie z opisem w artykule SAP Uwaga [1031096]i sprawdź dane wyjściowe `saposcol` .

   a.  Uruchom polecenie `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Uruchom polecenie `dump ccm`

   c.  Sprawdź, czy **Virtualization_Configuration Metryka dostępu \Enhanced monitorowania** ma **wartość true**.

Jeśli masz już zainstalowany serwer aplikacji SAP NetWeaver ABAP, Otwórz transakcję ST06 i sprawdź, czy monitorowanie jest włączone.

Jeśli którykolwiek z tych sprawdzeń zakończy się niepowodzeniem i szczegółowe informacje o sposobie ponownego wdrażania rozszerzenia, zobacz [Rozwiązywanie problemu z nowym rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Sprawdzenie kondycji konfiguracji rozszerzenia platformy Azure dla oprogramowania SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje domyślne rozszerzenie maszyny wirtualnej. Jeśli zainstalowano nowe rozszerzenie maszyny wirtualnej, zapoznaj [się z rozdziałem Sprawdzanie kondycji, aby poznać nowe rozszerzenie platformy Azure dla konfiguracji oprogramowania SAP][deployment-guide-5.2-new].

Jeśli niektóre dane infrastruktury nie zostały prawidłowo dostarczone zgodnie z opisem w teście opisanym w temacie [gotowość do sprawdzenia rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-5.1], uruchom `Test-AzVMAEMExtension` polecenie cmdlet, aby sprawdzić, czy infrastruktura platformy Azure i rozszerzenie platformy Azure dla oprogramowania SAP są poprawnie skonfigurowane.

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet Azure PowerShell, zgodnie z opisem w temacie [wdrażanie Azure PowerShell poleceń cmdlet][deployment-guide-4.1].
1. Uruchom następujące polecenie programu PowerShell. Aby uzyskać listę dostępnych środowisk, uruchom polecenie cmdlet `Get-AzEnvironment` . Aby korzystać z globalnej platformy Azure, wybierz środowisko **AzureCloud** . W przypadku platformy Azure z Chin wybierz pozycję **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Skrypt testuje konfigurację wybranej maszyny wirtualnej.

   ![Wynik pomyślnego testowania rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-figure-1300]

Upewnij się, że każdy wynik kontroli kondycji jest **prawidłowy**. Jeśli niektóre sprawdzenia nie wyświetlają **przycisku OK**, uruchom polecenie cmdlet Update zgodnie z opisem w temacie [Configure the Azure Extension for SAP][deployment-guide-4.5]. Poczekaj 15 minut i powtórz testy opisane w temacie [sprawdzanie gotowości dla rozszerzenia Azure dla oprogramowania SAP][deployment-guide-5.1] i [Health Check for Azure Extension for SAP Configuration][deployment-guide-5.2]. Jeśli kontrole nadal wskazują na problem z niektórymi lub wszystkimi licznikami, zobacz [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3].

> [!Note]
> Niektóre ostrzeżenia można napotkać w przypadkach, w których używane są zarządzane standardowe dyski platformy Azure. Wyświetlane są ostrzeżenia zamiast testów zwracających wartość "OK". Jest to normalne i zamierzone w przypadku tego typu dysku. Zobacz też temat [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Sprawdzenie kondycji nowego rozszerzenia platformy Azure dla konfiguracji SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje nowe rozszerzenie maszyny wirtualnej. Jeśli zainstalowano domyślne rozszerzenie maszyny wirtualnej, zobacz rozdział [Health Check for the Azure Extension for SAP Configuration][deployment-guide-5.2].

Jeśli niektóre dane infrastruktury nie zostały prawidłowo dostarczone zgodnie z opisem w teście opisanym w temacie [gotowość do sprawdzenia rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-5.1-new], uruchom `Get-AzVMExtension` polecenie cmdlet, aby sprawdzić, czy rozszerzenie platformy Azure dla oprogramowania SAP zostało zainstalowane. Program `Test-AzVMAEMExtension` nie obsługuje jeszcze nowego rozszerzenia. Gdy polecenie cmdlet obsługuje nowe rozszerzenie, firma Microsoft zaktualizuje ten artykuł.

1. Upewnij się, że zainstalowano najnowszą wersję polecenia cmdlet Azure PowerShell, zgodnie z opisem w temacie [wdrażanie Azure PowerShell poleceń cmdlet][deployment-guide-4.1].
1. Uruchom następujące polecenie programu PowerShell. Aby uzyskać listę dostępnych środowisk, uruchom polecenie cmdlet `Get-AzEnvironment` . Aby korzystać z globalnej platformy Azure, wybierz środowisko **AzureCloud** . W przypadku platformy Azure z Chin wybierz pozycję **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Polecenie cmdlet testuje konfigurację rozszerzenia maszyny wirtualnej dla SAP na wybranej maszynie wirtualnej.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje domyślne rozszerzenie maszyny wirtualnej. Jeśli zainstalowano nowe rozszerzenie maszyny wirtualnej, zobacz rozdział [Rozwiązywanie problemów z nowym rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Windows.][Logo_Windows] Liczniki wydajności platformy Azure nie są wyświetlane w ogóle

Usługa AzureEnhancedMonitoring systemu Windows zbiera metryki wydajności na platformie Azure. Jeśli usługa nie została prawidłowo zainstalowana lub nie jest uruchomiona na maszynie wirtualnej, nie można zbierać metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia platformy Azure dla oprogramowania SAP jest pusty

###### <a name="issue"></a>Problem

Katalog instalacyjny C: \\ pakiety \\ wtyczki \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; wersja>\\ Drop jest puste.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Ustal, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Być może trzeba będzie ponownie uruchomić maszynę lub uruchomić ponownie `Set-AzVMAEMExtension` skrypt konfiguracji.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Rozszerzenie usługi dla platformy Azure dla oprogramowania SAP nie istnieje

###### <a name="issue"></a>Problem

Usługa AzureEnhancedMonitoring systemu Windows nie istnieje.

Dane wyjściowe Azperflib.exe zgłaszają błąd:

![Wykonanie azperflib.exe wskazuje, że usługa rozszerzenia platformy Azure dla oprogramowania SAP nie jest uruchomiona][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Rozwiązanie

Jeśli usługa nie istnieje, rozszerzenie platformy Azure dla oprogramowania SAP nie zostało poprawnie zainstalowane. Wdróż ponownie rozszerzenie, wykonując kroki opisane w scenariuszu wdrażania w [scenariuszach wdrażania maszyn wirtualnych dla oprogramowania SAP na platformie Azure][deployment-guide-3].

Po wdrożeniu rozszerzenia po upływie jednej godziny Sprawdź, czy liczniki wydajności platformy Azure są dostępne na maszynie wirtualnej platformy Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Usługa dla rozszerzenia platformy Azure dla oprogramowania SAP istnieje, ale nie można jej uruchomić

###### <a name="issue"></a>Problem

Usługa AzureEnhancedMonitoring systemu Windows istnieje i jest włączona, ale nie można jej uruchomić. Aby uzyskać więcej informacji, sprawdź dziennik zdarzeń aplikacji.

###### <a name="solution"></a>Rozwiązanie

Konfiguracja jest nieprawidłowa. Uruchom ponownie rozszerzenie platformy Azure dla oprogramowania SAP na maszynie wirtualnej zgodnie z opisem w temacie [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logo Windows.][Logo_Windows] Brakuje niektórych liczników wydajności platformy Azure

Usługa AzureEnhancedMonitoring systemu Windows zbiera metryki wydajności na platformie Azure. Usługa pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z Diagnostyka Azure. Liczniki magazynu są używane w ramach rejestrowania na poziomie subskrypcji magazynu.

Jeśli Rozwiązywanie problemów przy użyciu uwagi SAP [1999351] nie rozwiąże problemu, uruchom ponownie `Set-AzVMAEMExtension` skrypt konfiguracji. Może być konieczne odczekanie godziny, ponieważ liczniki usługi Storage Analytics lub Diagnostics mogą nie zostać utworzone natychmiast po ich włączeniu. Jeśli problem będzie nadal występował, Otwórz komunikat obsługa klienta SAP na składniku BC-OP-NT-AZR dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej z systemem Linux.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logo systemu Linux.][Logo_Linux] Liczniki wydajności platformy Azure nie są wyświetlane w ogóle

Metryki wydajności na platformie Azure są zbierane przez demona. Jeśli Demon nie jest uruchomiony, nie można zbierać metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia platformy Azure dla oprogramowania SAP jest pusty

###### <a name="issue"></a>Problem

\\ \\ Biblioteka var lib \\ waagent nie \\ ma podkatalogu dla rozszerzenia platformy Azure dla oprogramowania SAP.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Ustal, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera i/lub uruchomienie `Set-AzVMAEMExtension` skryptu konfiguracyjnego.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Wykonanie Set-AzVMAEMExtension i Test-AzVMAEMExtension wyświetlanie komunikatów ostrzegawczych informujących, że standardowe Managed Disks nie są obsługiwane

###### <a name="issue"></a>Problem

Podczas wykonywania Set-AzVMAEMExtension lub Test-AzVMAEMExtension komunikaty, takie jak te są wyświetlane:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Wykonanie azperfli.exe zgodnie z wcześniejszym opisem pozwala uzyskać wynik wskazujący, że stan nie jest w dobrej kondycji. 

###### <a name="solution"></a>Rozwiązanie

Komunikaty są spowodowane przez fakt, że standardowa Managed Disks nie dostarcza interfejsów API używanych przez rozszerzenie SAP dla SAP do sprawdzania statystyk standardowych kont usługi Azure Storage. Nie jest to kwestia problemu. Powód wprowadzenia danych zbierających dla standardowych kont Disk Storage było ograniczeniem liczby wejść i wyjść, które wystąpiły często. W przypadku dysków zarządzanych będzie możliwe uniknięcie tego ograniczenia przez ograniczenie liczby dysków na koncie magazynu. W związku z tym ten typ danych nie jest krytyczny.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logo systemu Linux.][Logo_Linux] Brakuje niektórych liczników wydajności platformy Azure

Metryki wydajności na platformie Azure są zbierane przez demon, który pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z Diagnostyka Azure. Liczniki magazynu pochodzą z dzienników w ramach subskrypcji magazynu.

Aby zapoznać się z pełną i aktualną listą znanych problemów, zobacz temat SAP Note [1999351], który zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem platformy Azure dla oprogramowania SAP.

Jeśli Rozwiązywanie problemów przy użyciu programu SAP Note [1999351] nie rozwiąże problemu, uruchom ponownie `Set-AzVMAEMExtension` skrypt konfiguracji zgodnie z opisem w temacie [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5]. Może być konieczne odczekanie na godzinę, ponieważ liczniki usługi Storage Analytics lub Diagnostics mogą nie zostać utworzone natychmiast po ich włączeniu. Jeśli problem będzie nadal występował, Otwórz komunikat obsługa klienta SAP na składniku BC-OP-NT-AZR dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej z systemem Linux.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Rozwiązywanie problemów z nowym rozszerzeniem platformy Azure dla oprogramowania SAP

> [!NOTE]
> Istnieją dwie wersje rozszerzenia maszyny wirtualnej. Ten rozdział obejmuje nowe rozszerzenie maszyny wirtualnej. Jeśli zainstalowano domyślne rozszerzenie maszyny wirtualnej, zobacz rozdział [Rozwiązywanie problemów z rozszerzeniem platformy Azure dla oprogramowania SAP][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logo Windows.][Logo_Windows] Liczniki wydajności platformy Azure nie są wyświetlane w ogóle

Proces AzureEnhancedMonitoring zbiera metryki wydajności na platformie Azure. Jeśli proces nie jest uruchomiony na maszynie wirtualnej, nie można zbierać metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia platformy Azure dla oprogramowania SAP jest pusty

###### <a name="issue"></a>Problem

Katalog instalacyjny C: \\ pakiety \\ dodatki \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows w \\ &lt; wersji> są puste.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Ustal, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera lub ponowne zainstalowanie rozszerzenia maszyny wirtualnej.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logo Windows.][Logo_Windows] Brakuje niektórych liczników wydajności platformy Azure

Proces systemu Windows AzureEnhancedMonitoring zbiera metryki wydajności na platformie Azure. Proces pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z Azure Monitor.

Jeśli Rozwiązywanie problemów przy użyciu programu SAP Note [1999351] nie rozwiąże problemu, Otwórz komunikat obsługi klienta SAP na składniku BC-op-NT-AZR dla systemu Windows lub BC-op-LNX-AZR dla maszyny wirtualnej z systemem Linux. Dołącz plik dziennika C: \\ pakiety \\ dodatki \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; w wersji>\\logapp.txt do zdarzenia.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logo systemu Linux.][Logo_Linux] Liczniki wydajności platformy Azure nie są wyświetlane w ogóle

Metryki wydajności na platformie Azure są zbierane przez demona. Jeśli Demon nie jest uruchomiony, nie można zbierać metryk wydajności.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Katalog instalacyjny rozszerzenia platformy Azure dla oprogramowania SAP jest pusty

###### <a name="issue"></a>Problem

\\ \\ Biblioteka var lib \\ waagent nie \\ ma podkatalogu dla rozszerzenia platformy Azure dla oprogramowania SAP.

###### <a name="solution"></a>Rozwiązanie

Rozszerzenie nie jest zainstalowane. Ustal, czy jest to problem z serwerem proxy (zgodnie z wcześniejszym opisem). Może być konieczne ponowne uruchomienie komputera i/lub ponowne zainstalowanie rozszerzenia maszyny wirtualnej.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logo systemu Linux.][Logo_Linux] Brakuje niektórych liczników wydajności platformy Azure

Metryki wydajności na platformie Azure są zbierane przez demon, który pobiera dane z kilku źródeł. Niektóre dane konfiguracji są zbierane lokalnie, a niektóre metryki wydajności są odczytywane z Azure Monitor.

Aby zapoznać się z pełną i aktualną listą znanych problemów, zobacz temat SAP Note [1999351], który zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem platformy Azure dla oprogramowania SAP.

Jeśli Rozwiązywanie problemów przy użyciu programu SAP Note [1999351] nie rozwiąże problemu, zainstaluj rozszerzenie ponownie, zgodnie z opisem w artykule [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5]. Jeśli problem będzie nadal występował, Otwórz komunikat obsługa klienta SAP na składniku BC-OP-NT-AZR dla systemu Windows lub BC-OP-LNX-AZR dla maszyny wirtualnej z systemem Linux. Dołącz plik dziennika/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; wersja>/logapp.txt do zdarzenia.

## <a name="azure-extension-error-codes"></a>Kody błędów rozszerzenia platformy Azure

| Identyfikator błędu | Opis błędu | Rozwiązanie |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Brak konfiguracji aplikacji. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Brak identyfikatora wdrożenia w konfiguracji aplikacji. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Brak RoleInstanceId w konfiguracji aplikacji. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Brak RoleInstanceId w konfiguracji aplikacji. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Nie można odczytać konfiguracji platformy Azure. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Brak pliku konfiguracji aplikacji. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Brak rozmiaru maszyny wirtualnej w konfiguracji aplikacji. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Licznik GlobalMemoryStatusEx nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Licznik MaxHwFrequency nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Liczniki karty sieciowej nie powiodły się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Licznik mapowania dysków nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Licznik nazw procesorów nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Licznik mapowania dysków nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Brak metryki "typ dysku" w pliku konfiguracji rozszerzenia config.xml. "Typ dysku" wraz z innymi licznikami został wprowadzony w v 2.2.0.68 12/16/2015. Jeśli rozszerzenie zostało wdrożone przed 12/16/2015, zostanie użyty stary plik konfiguracji. Struktura rozszerzeń platformy Azure automatycznie uaktualnia rozszerzenie do nowszej wersji, ale config.xml pozostaje niezmieniona. Aby zaktualizować konfigurację, Pobierz i wykonaj najnowszy skrypt Instalatora programu PowerShell. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Brak buforowania dysku. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Brak przepływności umowy SLA dotyczącej dysków. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Brak operacji we/wy na dysku. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Licznik mapowania dysków nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Ostatni licznik zmian sprzętu nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Liczniki karty sieciowej nie powiodły się | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Ze względu na program Sysprep maszyny wirtualnej zmienił się identyfikator SID systemu Windows. | [Wdróż ponownie po uruchomieniu programu Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Nie można uzyskać dostępu do analizy magazynu. <br /><br />Ponieważ dane analityczne magazynu na nowo utworzonej maszynie wirtualnej mogą potrzebować do połowy godziny, błąd może zniknąć po pewnym czasie. Jeśli błąd nadal pojawia się, uruchom skrypt instalacyjny. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Brak analityka magazynu liczników. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Analityka magazynu nie powiodło się. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="wad_004"></a>funkcji wad/004 | Zła Konfiguracja funkcji wad. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="wad_002"></a>funkcji wad/002 | Nieoczekiwany format funkcji wad. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="wad_001"></a>funkcji wad/001 | Nie znaleziono liczników funkcji wad. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="wad_040"></a>funkcji wad/040 | Znaleziono nieodświeżone liczniki funkcji wad. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="wad_003"></a>funkcji wad/003 | Nie można odczytać tabeli funkcji wad. Brak połączenia z tabelą funkcji wad. Może istnieć kilka przyczyn tego:<br /><br /> 1) nieaktualna konfiguracja <br />2) brak połączenia sieciowego z platformą Azure <br />3) problemy z instalacją funkcji wad | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script]<br />[Napraw połączenie internetowe][deployment-guide-fix-internet-connection]<br />[Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="prf_011"></a>PRF/011 | Metryki karty sieciowej monitora wydajności nie powiodły się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="prf_012"></a>PRF/012 | Metryki dysku monitora wydajności nie powiodły się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="prf_013"></a>PRF/013 | Niektóre metryki prefmon zakończyły się niepowodzeniem. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="prf_014"></a>PRF/014 | Utworzenie licznika przez Monitor wydajności nie powiodło się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Nie skonfigurowano dostawców metryk. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Nieprawidłowa konfiguracja analityka magazynu. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Metryki analityka magazynu nie powiodły się. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Jeden z dostawców metryk nie powiódł się. | [Uruchom skrypt instalacyjny][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Wątek dostawcy nie powiódł się. | [Skontaktuj się z pomocą techniczną][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Szczegółowe wskazówki dotyczące dostarczonych rozwiązań

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Uruchom skrypt instalacyjny

Wykonaj kroki opisane w rozdziale [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5] w tym przewodniku, aby ponownie zainstalować rozszerzenie. Należy pamiętać, że niektóre liczniki mogą potrzebować do 30 minut na zainicjowanie obsługi administracyjnej.

Jeśli błędy nie znikną, [skontaktuj się z pomocą techniczną][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Skontaktuj się z pomocą techniczną

Nieoczekiwany błąd lub nie ma znanego rozwiązania. Zbierz plik AzureEnhancedMonitoring_service. log znajdujący się w folderze C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) lub/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) i skontaktuj się z pomocą TECHNICZNą SAP w celu uzyskania dalszej pomocy.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Wdróż ponownie po uruchomieniu programu Sysprep

Jeśli planujesz utworzenie uogólnionego obrazu systemu operacyjnego Sysprep (który może zawierać oprogramowanie SAP), zaleca się, aby ten obraz nie zawierał rozszerzenia platformy Azure dla SAP. Rozszerzenie platformy Azure dla oprogramowania SAP należy zainstalować po wdrożeniu nowego wystąpienia uogólnionego obrazu systemu operacyjnego.

Jeśli jednak obraz uogólniony i Sysprep systemu operacyjnego zawiera już rozszerzenie platformy Azure dla oprogramowania SAP, można zastosować następujące obejście, aby ponownie skonfigurować rozszerzenie na nowo wdrożonym wystąpieniu maszyny wirtualnej:

* Na nowo wdrożonym wystąpieniu maszyny wirtualnej Usuń zawartość następujących folderów:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Wykonaj kroki opisane w rozdziale [Konfigurowanie rozszerzenia platformy Azure dla oprogramowania SAP][deployment-guide-4.5] w tym przewodniku, aby ponownie zainstalować rozszerzenie.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Napraw połączenie internetowe

Maszyna wirtualna Microsoft Azure, na której działa rozszerzenie platformy Azure dla oprogramowania SAP, wymaga dostępu do Internetu. Jeśli ta maszyna wirtualna platformy Azure jest częścią Virtual Network platformy Azure lub domeny lokalnej, upewnij się, że ustawiono odpowiednie ustawienia serwera proxy. Te ustawienia muszą być również prawidłowe dla konta LocalSystem, aby można było uzyskać dostęp do Internetu. Postępuj zgodnie z rozdziałem [Skonfiguruj serwer proxy][deployment-guide-configure-proxy] w tym przewodniku.

Ponadto, jeśli musisz ustawić statyczny adres IP dla maszyny wirtualnej platformy Azure, nie ustawiaj jej ręcznie na maszynę wirtualną platformy Azure, ale ustawisz ją przy użyciu [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md) [interfejsu wiersza polecenia platformy Azure](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) . Statyczny adres IP jest propagowany za pośrednictwem usługi DHCP platformy Azure.

Ręczne ustawienie statycznego adresu IP wewnątrz maszyny wirtualnej platformy Azure nie jest obsługiwane i może prowadzić do problemów z rozszerzeniem platformy Azure dla oprogramowania SAP.
