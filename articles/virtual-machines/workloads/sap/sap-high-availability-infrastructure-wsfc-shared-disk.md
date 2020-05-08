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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5e0eda72f39a70f02b596a8fd69728336eac333
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594818"
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


> ![Windows][Logo_Windows] Windows
>

W tym artykule opisano kroki, które należy wykonać w celu przygotowania infrastruktury platformy Azure do zainstalowania i skonfigurowania systemu SAP wysokiej dostępności w klastrze trybu failover systemu Windows przy użyciu *udostępnionego dysku klastra* jako opcji klastrowania wystąpienia SAP ASCS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem instalacji zapoznaj się z tym artykułem:

* [Przewodnik po architekturze: klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowywanie infrastruktury dla szablonu architektury 1
Szablony Azure Resource Manager dla oprogramowania SAP upraszczają wdrażanie wymaganych zasobów.

Szablony trzech warstw w Azure Resource Manager obsługują również scenariusze o wysokiej dostępności. Na przykład szablon architektoniczny 1 ma dwa klastry. Każdy klaster jest single point of failure SAP dla oprogramowania SAP ASCS/SCS i DBMS.

Oto, gdzie można uzyskać Azure Resource Manager szablonów dla przykładowego scenariusza opisywanego w tym artykule:

* [Obraz witryny Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Obraz portalu Azure Marketplace przy użyciu usługi Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/)
* [Obraz niestandardowy przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Aby przygotować infrastrukturę dla szablonu architektury 1:

- W Azure Portal w okienku **Parametry** w polu **SYSTEMAVAILABILITY** wybierz pozycję **ha**.

  ![Rysunek 1. Ustawianie Azure Resource Manager parametrów wysokiej dostępności SAP][sap-ha-guide-figure-3000]

_**Rysunek 1.** Ustaw Azure Resource Manager parametry wysokiej dostępności SAP_


  Szablony tworzą:

  * **Maszyny wirtualne**:
    * Maszyny wirtualne serwera aplikacji SAP: \<SAPSystemSID\>-di-\<Number\>
    * Maszyny wirtualne klastra ASCS/SCS: \<SAPSystemSID\>-ASCS-\<Number\>
    * Klaster DBMS: \<SAPSystemSID\>-DB-\<Number\>

  * **Karty sieciowe dla wszystkich maszyn wirtualnych ze skojarzonymi adresami IP**:
    * \<SAPSystemSID\>-nic-di-\<Number\>
    * \<SAPSystemSID\>-nic-ASCS-\<Number\>
    * \<SAPSystemSID\>-nic-DB-\<Number\>

  * **Konta usługi Azure Storage (tylko dyski niezarządzane)**:

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: \<SAPSystemSID\>-avset-di
    * Maszyny wirtualne w klastrze SAP ASCS/SCS \<:\>SAPSystemSID-avset-ASCS
    * Maszyny wirtualne klastra DBMS: \<SAPSystemSID\>-avset-DB

  * **Wewnętrzny moduł równoważenia obciążenia platformy Azure**:
    * Ze wszystkimi portami wystąpienia ASCS/SCS i adresem \<IP SAPSystemSID\>-lb-ASCS
    * Ze wszystkimi portami dla SQL Server systemie DBMS i adresem \<IP\>SAPSystemSID-lb-DB

  * **Sieciowa Grupa zabezpieczeń**: \<SAPSystemSID\>-sieciowej grupy zabezpieczeń-ASCS-0  
    * Z otwartym portem zewnętrznym Remote Desktop Protocol (RDP) \<z\>maszyną wirtualną SAPSystemSID-ASCS-0

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i wewnętrznych modułów równoważenia obciążenia platformy Azure są domyślnie dynamiczne. Zmień je na statyczne adresy IP. Opisujemy, jak to zrobić w dalszej części artykułu.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Wdrażanie maszyn wirtualnych z łącznością sieci firmowej (między różnymi lokalizacjami) do użycia w środowisku produkcyjnym
W przypadku produkcyjnych systemów SAP Wdróż maszyny wirtualne platformy Azure z łącznością sieci firmowej przy użyciu usługi Azure VPN Gateway lub Azure ExpressRoute.

> [!NOTE]
> Możesz użyć wystąpienia usługi Azure Virtual Network. Sieć wirtualna i podsieć została już utworzona i przygotowana.
>
>

1. W Azure Portal w okienku **Parametry** w polu **NEWOREXISTINGSUBNET** wybierz pozycję **istniejące**.
2. W polu **SUBNETID** Dodaj pełny ciąg przygotowanego identyfikatora podsieci sieci platformy Azure, w którym planujesz wdrożyć maszyny wirtualne platformy Azure.
3. Aby uzyskać listę wszystkich podsieci sieciowych platformy Azure, Uruchom to polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **ID** zawiera wartość identyfikatora podsieci.
4. Aby uzyskać listę wszystkich wartości identyfikatorów podsieci, uruchom następujące polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Identyfikator podsieci wygląda następująco:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Wdrażaj wystąpienia SAP tylko w chmurze dla testów i demonstracji
System SAP o wysokiej dostępności można wdrożyć w modelu wdrażania tylko w chmurze. Ten rodzaj wdrożenia jest przydatny w przypadku demonstracji i testowania przypadków użycia. Nie jest to odpowiednie dla przypadków użycia w środowisku produkcyjnym.

- W Azure Portal w okienku **Parametry** w polu **NEWOREXISTINGSUBNET** wybierz pozycję **Nowy**. Pozostaw puste pole **SUBNETID** .

  Szablon Azure Resource Manager SAP automatycznie tworzy sieć wirtualną i podsieć platformy Azure.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną dedykowaną maszynę wirtualną dla Active Directory i usługi DNS w tym samym wystąpieniu platformy Azure Virtual Network. Szablon nie tworzy tych maszyn wirtualnych.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowywanie infrastruktury dla szablonu architektury 2

Za pomocą tego szablonu Azure Resource Manager dla oprogramowania SAP można uprościć wdrażanie wymaganych zasobów infrastruktury dla szablonu architektury SAP w systemie 2.

Oto, gdzie można uzyskać Azure Resource Manager szablonów dla tego scenariusza wdrażania:

* [Obraz witryny Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Obraz portalu Azure Marketplace przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/)
* [Obraz niestandardowy przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowywanie infrastruktury dla szablonu architektury 3

Możesz przygotować infrastrukturę i skonfigurować rozwiązanie SAP dla usługi wiele identyfikatorów SID. Można na przykład dodać do *istniejącej* konfiguracji klastra dodatkowe wystąpienie SAP ASCS/SCS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dodatkowego wystąpienia SAP ASCS/SCS dla istniejącej konfiguracji klastra w celu utworzenia konfiguracji protokołu SAP wielu identyfikatorów SID w Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster z obsługą identyfikatorów SID, możesz użyć [szablonów szybkiego startu o wiele identyfikatorów SID w serwisie GitHub](https://github.com/Azure/azure-quickstart-templates).

Aby utworzyć nowy klaster z obsługą wiele identyfikatorów SID, należy wdrożyć następujące trzy szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Szablon bazy danych](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje zawierają więcej szczegółów na temat szablonów i parametrów, które należy podać w szablonach.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>Szablon ASCS/SCS

Szablon ASCS/SCS wdraża dwie maszyny wirtualne, których można użyć do utworzenia klastra trybu failover systemu Windows Server, który hostuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon wieloidentyfikatorowy ASCS/SCS, w szablonie [ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] z szablonem wieloidentyfikatorowym lub [ASCS/SCS przy użyciu Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]wprowadź wartości dla następujących parametrów:

- **Prefiks zasobu**: Ustaw prefiks zasobu, który jest używany do prefiksowania wszystkich zasobów utworzonych podczas wdrażania. Ponieważ zasoby nie należą tylko do jednego systemu SAP, prefiks zasobu nie jest identyfikatorem SID jednego systemu SAP.  Prefiks musi zawierać od 3 do 6 znaków.
- **Typ stosu**: Wybierz typ stosu systemu SAP. W zależności od typu stosu Azure Load Balancer ma jeden (tylko ABAP lub Java) lub dwa (ABAP + Java) prywatnych adresów IP na system SAP.
- **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
- **Liczba systemowa SAP**: Wybierz liczbę systemów SAP, które chcesz zainstalować w tym klastrze.
- **Dostępność systemu**: Wybierz **ha**.
- **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Nowa lub istniejąca podsieć**: Ustaw, czy chcesz utworzyć nową sieć wirtualną i podsieć, czy też użyć istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
- **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda następująco:

  Identyfikator\<\>subskrypcji/subscriptions//ResourceGroups/\<nazwa\>grupy zasobów/Providers/Microsoft.Network/virtualNetworks/\<nazwa sieci wirtualnej\>/Subnets/\<nazwa podsieci\>

Szablon wdraża jedno Azure Load Balancer wystąpienie, które obsługuje wiele systemów SAP:

- Wystąpienia ASCS są konfigurowane dla wystąpienia o numerze 00, 10, 20...
- Wystąpienia SCS są konfigurowane dla wystąpienia numer 01, 11, 21...
- Serwer replikacji ASCS (tylko system wykres WYWOŁUJĄCYCH) (tylko system Linux) jest konfigurowany dla wystąpienia o numerze 02, 12, 22...
- Wystąpienia SCS wykres WYWOŁUJĄCYCH (tylko system Linux) są konfigurowane dla wystąpienia o numerze 03, 13, 23...

Moduł równoważenia obciążenia zawiera 1 wirtualne adresy IP (2 dla systemu Linux), adres VIP 1x dla ASCS/SCS i 1x VIP dla wykres WYWOŁUJĄCYCH (tylko system Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>Porty SAP ASCS/SCS
Poniższa lista zawiera wszystkie reguły równoważenia obciążenia (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Porty specyficzne dla systemu Windows dla każdego systemu SAP: 445, 5985
- Porty ASCS (numer wystąpienia x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (numer wystąpienia x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS wykres WYWOŁUJĄCYCH Ports w systemie Linux (numer wystąpienia X2): 33x2, 5x213, 5x214, 5x216
- SCS wykres WYWOŁUJĄCYCH Ports w systemie Linux (numer wystąpienia x3): 33x3, 5x313, 5x314, 5x316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujących portów sondowania (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Port sondy wewnętrznego modułu równoważenia obciążenia ASCS/SCS: 620x0
- Port sondy wewnętrznego modułu równoważenia obciążenia wykres WYWOŁUJĄCYCH (tylko system Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Szablon bazy danych

Szablon bazy danych wdraża jedną lub dwie maszyny wirtualne, których można użyć do zainstalowania systemu zarządzania relacyjnymi bazami danych (RDBMS) dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon wieloidentyfikatorowego bazy danych, w szablonie [bazy danych o identyfikatorze wielościeżkowym][sap-templates-3-tier-multisid-db-marketplace-image] lub [wieloidentyfikatorze bazy danych przy użyciu Managed disks][sap-templates-3-tier-multisid-db-marketplace-image-md]wprowadź wartości dla następujących parametrów:

- **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrożonych zasobów.
- **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
- **DbType**: Wybierz typ bazy danych, którą chcesz zainstalować w klastrze. Wybierz pozycję **SQL** , jeśli chcesz zainstalować Microsoft SQL Server. Wybierz pozycję **Hana** , jeśli planujesz zainstalować SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano poprawny typ systemu operacyjnego. Wybierz pozycję Windows for SQL i wybierz dystrybucję **systemu** Linux dla platformy Hana. Azure Load Balancer, która jest połączona z maszynami wirtualnymi, jest skonfigurowana do obsługi wybranego typu bazy danych:
  * **SQL**: Port równoważenia obciążenia modułu równoważenia obciążenia 1433. Upewnij się, że używasz tego portu dla SQL Server konfiguracji funkcji AlwaysOn.
  * **Hana**: Równoważenie obciążenia portów 35015 i 35017 usługi równoważenia obciążenia. Upewnij się, że SAP HANA jest zainstalowana z numerem wystąpienia **50**.
  Moduł równoważenia obciążenia używa portu sondy 62550.
- **Rozmiar systemu SAP**: Ustaw liczbę punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
- **Dostępność systemu**: Wybierz **ha**.
- **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Identyfikator podsieci**: Wprowadź identyfikator podsieci, która była używana podczas wdrażania szablonu ASCS/SCS, lub identyfikator podsieci, która została utworzona w ramach wdrożenia szablonu ASCS/SCS.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Szablon serwerów aplikacji

Szablon serwery aplikacji wdraża co najmniej dwie maszyny wirtualne, które mogą być używane jako wystąpienia serwera aplikacji SAP dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon z obsługą wiele identyfikatorów SID dla serwerów aplikacji, w [szablonie wiele identyfikatorów SID lub][sap-templates-3-tier-multisid-apps-marketplace-image] na [serwerach aplikacji szablon wielosid przy użyciu Managed disks][sap-templates-3-tier-multisid-apps-marketplace-image-md]wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrożonych zasobów.
  -  **Typ systemu operacyjnego**: Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP**: liczba punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
  -  **Dostępność systemu**: Wybierz **ha**.
  -  **Nazwa użytkownika administratora i hasło administratora**: Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Identyfikator podsieci**: Wprowadź identyfikator podsieci, która była używana podczas wdrażania szablonu ASCS/SCS, lub identyfikator podsieci, która została utworzona w ramach wdrożenia szablonu ASCS/SCS.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtual Network platformy Azure
W naszym przykładzie przestrzeń adresowa wystąpienia usługi Azure Virtual Network to 10.0.0.0/16. Istnieje jedna podsieć o nazwie Subnet z zakresem adresów 10.0.0.0/24. Wszystkie maszyny wirtualne i wewnętrzne moduły równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian w ustawieniach sieci w systemie operacyjnym gościa. Obejmuje to adresy IP, serwery DNS i podsieć. Skonfiguruj wszystkie ustawienia sieci na platformie Azure. Usługa Dynamic Host Configuration Protocol (DHCP) propaguje Twoje ustawienia.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Adresy IP DNS

Aby ustawić wymagane adresy IP DNS, wykonaj następujące czynności:

1. W Azure Portal w okienku **serwery DNS** upewnij się, że opcja **serwery DNS** sieci wirtualnej jest ustawiona na wartość **niestandardowy DNS**.
2. Wybierz swoje ustawienia w zależności od typu sieci. Więcej informacji zawierają następujące zasoby:
   * Dodaj adresy IP lokalnych serwerów DNS.  
   Lokalne serwery DNS można rozłożyć na maszyny wirtualne, które są uruchomione na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których jest uruchomiona usługa DNS.
   * W przypadku wdrożeń maszyn wirtualnych izolowanych na platformie Azure: Wdróż dodatkową maszynę wirtualną w tym samym wystąpieniu Virtual Network, które służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure, które zostały skonfigurowane do uruchamiania usługi DNS.

   ![Rysunek 2. Konfigurowanie serwerów DNS dla Virtual Network platformy Azure][sap-ha-guide-figure-3001]

   _**Rysunek 2.** Konfigurowanie serwerów DNS dla Virtual Network platformy Azure_

   > [!NOTE]
   > W przypadku zmiany adresów IP serwerów DNS należy ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę i propagować nowe serwery DNS.
   >
   >

W naszym przykładzie usługa DNS jest zainstalowana i skonfigurowana na tych maszynach wirtualnych z systemem Windows:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Nazwy hostów i statyczne adresy IP dla wystąpienia klastrowanego SAP ASCS/SCS i wystąpienia klastrowanego DBMS

W przypadku wdrożenia lokalnego wymagane są następujące zastrzeżone nazwy hostów i adresy IP:

| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Wirtualny statyczny adres IP |
| --- | --- | --- |
| Nazwa hosta wirtualnego SAP ASCS/SCS pierwszego klastra (do zarządzania klastrami) |PR1-ASCS-Vir |10.0.0.42 |
| Nazwa hosta wirtualnego wystąpienia SAP ASCS/SCS |PR1 — ASCS — SAP |10.0.0.43 |
| Nazwa hosta wirtualnego SAP DBMS drugiego klastra (Zarządzanie klastrem) |PR1-DBMS-Vir |10.0.0.32 |

Podczas tworzenia klastra utwórz nazwy hostów wirtualnych PR1-ASCS-Vir i PR1-DBMS-Vir oraz skojarzone adresy IP zarządzające klastrem. Aby uzyskać informacje o tym, jak to zrobić, zobacz [zbieranie węzłów klastra w konfiguracji klastra][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Można ręcznie utworzyć pozostałe dwie nazwy hostów wirtualnych, PR1-ASCS-SAP i PR1-DBMS-SAP oraz skojarzone adresy IP na serwerze DNS. W klastrowanym wystąpieniu SAP ASCS/SCS i klastrowanym wystąpieniu systemu DBMS są używane te zasoby. Aby uzyskać informacje o tym, jak to zrobić, zobacz [Tworzenie nazwy wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ustaw statyczne adresy IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze należy ustawić statyczne adresy IP dla wszystkich maszyn wirtualnych. Zrób to w konfiguracji Virtual Network platformy Azure, a nie w systemie operacyjnym gościa.

1. W Azure Portal wybierz pozycję **Grupa** > zasobów**Ustawienia** > **karta** > sieciowa**adres IP**.
2. W okienku **adresy IP** w obszarze **przypisanie**wybierz pozycję **statyczny**. W polu **adres IP** wprowadź adres IP, którego chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować tę zmianę.  
   >
   >

   ![Rysunek 3. Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej][sap-ha-guide-figure-3002]

   _**Rysunek 3.** Ustaw statyczne adresy IP dla karty sieciowej każdej maszyny wirtualnej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, czyli dla wszystkich maszyn wirtualnych, w tym maszyn wirtualnych, które mają być używane dla Active Directory lub usługi DNS.

W naszym przykładzie mamy te maszyny wirtualne i statyczne adresy IP:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwsze wystąpienie serwera aplikacji SAP |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Drugie wystąpienie serwera aplikacji SAP |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ostatnie wystąpienie serwera aplikacji SAP |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Pierwszy węzeł klastra dla wystąpienia ASCS/SCS |PR1-ASCS-0 |PR1-nic-ASCS-0 |10.0.0.40 |
| Drugi węzeł klastra dla wystąpienia ASCS/SCS |PR1-ASCS-1 |PR1-nic-ASCS-1 |10.0.0.41 |
| Pierwszy węzeł klastra dla wystąpienia DBMS |PR1-DB-0 |PR1-nic-DB-0 |10.0.0.30 |
| Drugi węzeł klastra dla wystąpienia DBMS |PR1-dB-1 |PR1-nic-dB-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ustawianie statycznego adresu IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon Azure Resource Manager SAP służy do tworzenia wewnętrznego modułu równoważenia obciążenia platformy Azure, który jest używany przez klaster wystąpienia SAP ASCS/SCS i klaster DBMS.

> [!IMPORTANT]
> Adres IP nazwy hosta wirtualnego SAP ASCS/SCS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia SAP ASCS/SCS: PR1-lb-ASCS.
> Adres IP nazwy wirtualnej systemu DBMS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia systemu DBMS: PR1-lb-DBMS.
>
>

Aby ustawić statyczny adres IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure:

1. Początkowe wdrożenie ustawia adres IP wewnętrznego modułu równoważenia obciążenia na **dynamiczny**. W Azure Portal w okienku **adresy IP** w obszarze **przypisanie**wybierz pozycję **statyczny**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **PR1-lb-ASCS** na adres IP nazwy hosta wirtualnego wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **PR1-lb-DBMS** na adres IP nazwy hosta wirtualnego systemu DBMS.

   ![Rysunek 4. Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Rysunek 4.** Ustaw statyczne adresy IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS_

W naszym przykładzie mamy dwa wewnętrzne moduły równoważenia obciążenia platformy Azure, które mają następujące statyczne adresy IP:

| Rola wewnętrznego modułu równoważenia obciążenia platformy Azure | Nazwa wewnętrznego modułu równoważenia obciążenia platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| Wewnętrzny moduł równoważenia obciążenia wystąpienia oprogramowania SAP ASCS/SCS |PR1-lb-ASCS |10.0.0.43 |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS |PR1-lb-DBMS |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon Azure Resource Manager SAP tworzy wymagane porty:
* Wystąpienie ABAP ASCS z domyślnym wystąpieniem numer 00
* Wystąpienie środowiska Java SCS z domyślnym numerem wystąpienia 01

W przypadku instalowania wystąpienia oprogramowania SAP ASCS/SCS należy użyć domyślnego wystąpienia o numerze 00 dla wystąpienia ASCS ABAP i domyślnego numeru wystąpienia 01 dla wystąpienia języka Java SCS.

Następnie utwórz wymagane punkty końcowe wewnętrznego równoważenia obciążenia dla portów SAP NetWeaver.

Aby utworzyć wymagane punkty końcowe wewnętrznego równoważenia obciążenia, należy najpierw utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver ABAP ASCS:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretne porty dla (wystąpienie ASCS z numerem wystąpienia 00) (wykres WYWOŁUJĄCYCH z 10) |
| --- | --- | --- |
| Serwer/ *lbrule3200* |32\<numerwystąpienia\> |3200 |
| Serwer komunikatów ABAP/ *lbrule3600* |36\<numerwystąpienia\> |3600 |
| Wewnętrzny komunikat ABAP/ *lbrule3900* |39\<numerwystąpienia\> |3900 |
| Serwer komunikatów HTTP/ *Lbrule8100* |81\<numerwystąpienia\> |8100 |
| Usługa SAP — uruchamianie usługi ASCS HTTP/ *Lbrule50013* |5\<numerwystąpienia\>13 |50013 |
| Usługa SAP Start ASCS HTTPS/ *Lbrule50014* |5\<numerwystąpienia\>14 |50014 |
| Replikacja/ *Lbrule50016* w kolejce |5\<numerwystąpienia\>16 |50016 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51013* |5\<numerwystąpienia\>13 |51013 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51014* |5\<numerwystąpienia\>14 |51014 |
| Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| *Lbrule445* udziału plików | |445 |

**Tabela 1:** Numery portów wystąpień programu SAP NetWeaver ABAP ASCS

Następnie utwórz te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver Java SCS:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretne porty dla (wystąpienie SCS z numerem wystąpienia 01) (wykres WYWOŁUJĄCYCH z 11) |
| --- | --- | --- |
| Serwer/ *lbrule3201* |32\<numerwystąpienia\> |3201 |
| Serwer bramy/ *lbrule3301* |33\<numerwystąpienia\> |3301 |
| Serwer wiadomości Java/ *lbrule3900* |39\<numerwystąpienia\> |3901 |
| Serwer komunikatów HTTP/ *Lbrule8101* |81\<numerwystąpienia\> |8101 |
| Usługa SAP — uruchamianie usługi SCS HTTP/ *Lbrule50113* |5\<numerwystąpienia\>13 |50113 |
| Usługa SAP Start SCS HTTPS/ *Lbrule50114* |5\<numerwystąpienia\>14 |50114 |
| Replikacja/ *Lbrule50116* w kolejce |5\<numerwystąpienia\>16 |50116 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51113* |5\<numerwystąpienia\>13 |51113 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51114* |5\<numerwystąpienia\>14 |51114 |
| *Lbrule5985* WinRM | |5985 |
| *Lbrule445* udziału plików | |445 |

**Tabela 2:** Numery portów wystąpień programu SAP NetWeaver Java SCS

![Rysunek 5. domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3004]

_**Rysunek 5.** Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

Ustaw adres IP modułu równoważenia obciążenia PR1-lb-DBMS na adres IP nazwy hosta wirtualnego systemu DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Jeśli chcesz użyć różnych numerów dla wystąpień SAP ASCS lub SCS, należy zmienić nazwy i wartości ich portów z wartości domyślnych.

1. W Azure Portal wybierz > kolejno ** \<pozycje\>SID-lb-ASCS****reguły równoważenia obciążenia**.
2. Dla wszystkich reguł równoważenia obciążenia należących do wystąpienia SAP ASCS lub SCS Zmień następujące wartości:

   * Nazwa
   * Port
   * Port zaplecza

   Na przykład jeśli chcesz zmienić domyślną liczbę wystąpień ASCS z 00 na 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji *lbrule3200*portu.

   ![Ilustracja 6. zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3005]

   _**Rysunek 6.** Zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Dodawanie maszyn wirtualnych z systemem Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych należy dodać maszyny wirtualne do domeny.

![Rysunek 7. Dodawanie maszyny wirtualnej do domeny][sap-ha-guide-figure-3006]

_**Rysunek 7.** Dodawanie maszyny wirtualnej do domeny_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Dodaj wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS

Azure Load Balancer ma wewnętrzny moduł równoważenia obciążenia, który zamyka połączenia, gdy połączenia są bezczynne przez określony czas (limit czasu bezczynności). Procesy robocze SAP w wystąpieniach okna dialogowego otwierają połączenia z procesem dodawania do kolejki SAP zaraz po wysłaniu pierwszego żądania kolejki/usuwania kolejki. Te połączenia zwykle pozostają ustanowione do momentu ponownego uruchomienia procesu pracy lub kolejki. Jeśli jednak połączenie jest w stanie bezczynności przez określony czas, wewnętrzny moduł równoważenia obciążenia platformy Azure zamknie połączenia. Nie jest to problem, ponieważ proces roboczy SAP ponownie nawiązuje połączenie z procesem dodawania do kolejki, jeśli już nie istnieje. Te działania są udokumentowane w śladach dla deweloperów procesów SAP, ale w tych śladach tworzą dużą ilość dodatkowej zawartości. Dobrym pomysłem jest zmiana protokołu TCP/IP `KeepAliveTime` i `KeepAliveInterval` obu węzłów klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP opisanymi w dalszej części artykułu.

Aby dodać wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS, należy najpierw dodać te wpisy rejestru systemu Windows na obu węzłach klastra systemu Windows dla oprogramowania SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (liczba dziesiętna) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** Zmiana pierwszego parametru TCP/IP

Następnie Dodaj ten wpis rejestru systemu Windows na obu węzłach klastra systemu Windows dla oprogramowania SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (liczba dziesiętna) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** Zmień drugi parametr TCP/IP

Aby zastosować zmiany, uruchom ponownie oba węzły klastra.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia oprogramowania SAP ASCS/SCS

Konfigurowanie klastra trybu failover systemu Windows Server dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbierz węzły klastra w konfiguracji klastra.
- Skonfiguruj monitor udziału plików klastra.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Zbieranie węzłów klastra w konfiguracji klastra

1. W Kreatorze dodawania ról i funkcji Dodaj klaster trybu failover do obu węzłów klastra.
2. Skonfiguruj klaster trybu failover za pomocą Menedżer klastra trybu failover. W Menedżer klastra trybu failover wybierz pozycję **Utwórz klaster**, a następnie Dodaj tylko nazwę pierwszego klastra (węzeł A). Nie należy jeszcze dodawać drugiego węzła; drugi węzeł zostanie dodany w późniejszym kroku.

   ![Ilustracja 8. Dodawanie nazwy serwera lub maszyny wirtualnej pierwszego węzła klastra][sap-ha-guide-figure-3007]

   _**Rysunek 8.** Dodaj nazwę serwera lub maszyny wirtualnej pierwszego węzła klastra_

3. Wprowadź nazwę sieci (nazwę hosta wirtualnego) klastra.

   ![Rysunek 9. Wprowadź nazwę klastra][sap-ha-guide-figure-3008]

   _**Rysunek 9.** Wprowadź nazwę klastra_

4. Po utworzeniu klastra uruchom test weryfikacji klastra.

   ![Ilustracja 10. Uruchomienie sprawdzania poprawności klastra][sap-ha-guide-figure-3009]

   _**Rysunek 10.** Uruchom sprawdzanie poprawności klastra_

   Można zignorować ostrzeżenia dotyczące dysków w tym momencie w procesie. W późniejszym czasie dodasz monitor udostępniania plików i dyski udostępnione oprogramowanie SIOS. Na tym etapie nie trzeba martwić się o posiadanie kworum.

   ![Ilustracja 11. nie znaleziono dysku kworum][sap-ha-guide-figure-3010]

   _**Rysunek 11.** Nie znaleziono dysku kworum_

   ![Ilustracja 12. podstawowy zasób klastra wymaga nowego adresu IP][sap-ha-guide-figure-3011]

   _**Rysunek 12.** Zasób klastra podstawowego wymaga nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastra. Nie można uruchomić klastra, dopóki nie zmienisz adresu IP podstawowej usługi klastra, ponieważ adres IP serwera wskazuje jeden z węzłów maszyny wirtualnej. Zrób to na stronie **Właściwości** zasobu adresu IP podstawowej usługi klastra.

   Na przykład musimy przypisać adres IP (w naszym przykładzie 10.0.0.42) dla nazwy hosta wirtualnego klastra PR1-ASCS-Vir.

   ![Ilustracja 13. w oknie dialogowym właściwości Zmień adres IP][sap-ha-guide-figure-3012]

   _**Rysunek 13:** W oknie dialogowym **Właściwości** Zmień adres IP_

   ![Ilustracja 14. przypisanie adresu IP zastrzeżonego dla klastra][sap-ha-guide-figure-3013]

   _**Ilustracja 14.** Przypisywanie adresu IP zastrzeżonego dla klastra_

6. Wprowadź nazwę wirtualnego hosta klastra w trybie online.

   ![Ilustracja 15. Usługa klastra podstawowego jest uruchomiona z prawidłowym adresem IP][sap-ha-guide-figure-3014]

   _**Ilustracja 15.** Podstawowa usługa klastra jest uruchomiona z prawidłowym adresem IP_

7. Dodaj drugi węzeł klastra.

   Teraz, gdy podstawowa usługa klastra jest uruchomiona, można dodać drugi węzeł klastra.

   ![Rysunek 16. Dodawanie drugiego węzła klastra][sap-ha-guide-figure-3015]

   _**Rysunek 16.** Dodaj drugi węzeł klastra_

8. Wprowadź nazwę drugiego hosta węzła klastra.

   ![Ilustracja 17. Wprowadź nazwę drugiego hosta węzła klastra][sap-ha-guide-figure-3016]

   _**Ilustracja 17.** Wprowadź drugą nazwę hosta węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że pole wyboru **Dodaj wszystkie odpowiednie magazyny do klastra** *nie* jest zaznaczone.  
   >
   >

   ![Ilustracja 18. nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Ilustracja 18.** *Nie* zaznaczaj pola wyboru_

   Można zignorować ostrzeżenia dotyczące kworum i dysków. Należy ustawić kworum i udostępnić dysk później, zgodnie z opisem w temacie [Install oprogramowanie SIOS DataKeeper Cluster Edition dla dysku udziału klastra SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Ilustracja 19. ignorowanie ostrzeżeń dotyczących kworum dysku][sap-ha-guide-figure-3018]

   _**Rysunek 19.** Ignoruj ostrzeżenia dotyczące kworum dysku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udziału plików klastra obejmuje następujące zadania:

- Utwórz udział plików.
- Ustaw kworum monitora udziału plików w Menedżer klastra trybu failover.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Tworzenie udziału plików

1. Wybierz monitor udziału plików zamiast dysku kworum. OPROGRAMOWANIE SIOS DataKeeper obsługuje tę opcję.

   W przykładach w tym artykule monitor udziału plików znajduje się na serwerze Active Directory lub DNS działającym na platformie Azure. Monitor udziału plików ma nazwę domcontr-0. Ze względu na to, że skonfigurowano połączenie sieci VPN z platformą Azure (za pośrednictwem VPN Gateway lub Azure ExpressRoute), Active Directory lub usługa DNS jest lokalna i nie jest odpowiednia do uruchamiania monitora udostępniania plików.

   > [!NOTE]
   > Jeśli Active Directory lub usługa DNS działa tylko lokalnie, nie należy konfigurować monitora udostępniania plików w systemie operacyjnym Windows Active Directory lub DNS działającym lokalnie. Opóźnienie sieci między węzłami klastra działającymi na platformie Azure a Active Directory lub DNS w środowisku lokalnym może być zbyt duże i powodować problemy z łącznością. Należy pamiętać, aby skonfigurować Monitor udostępniania plików na maszynie wirtualnej platformy Azure, która działa blisko węzła klastra.  
   >
   >

   Dysk kworum wymaga co najmniej 1 024 MB wolnego miejsca. Zalecamy 2 048 MB wolnego miejsca na dysku kworum.

2. Dodaj obiekt nazwy klastra.

   ![Ilustracja 20. przypisanie uprawnień do udziału dla obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Ilustracja 20.** Przypisz uprawnienia do udziału dla obiektu nazwy klastra_

   Upewnij się, że uprawnienia obejmują urząd do zmiany danych w udziale dla obiektu nazwy klastra (w naszym przykładzie PR1-ASCS-Vir $).

3. Aby dodać obiekt nazwy klastra do listy, wybierz pozycję **Dodaj**. Zmień filtr, aby wyszukiwać obiekty komputerów, a także te, które przedstawiono na rysunku 22.

   ![Ilustracja 21. zmiana typów obiektów w celu uwzględnienia komputerów][sap-ha-guide-figure-3020]

   _**Rysunek 21.** Zmień **typy obiektów** w celu uwzględnienia komputerów_

   ![Ilustracja 22. Zaznacz pole wyboru komputery][sap-ha-guide-figure-3021]

   _**Ilustracja 22:** Zaznacz pole wyboru **komputery**_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 21. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 20.

5. Wybierz kartę **zabezpieczenia** udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwa klastra.

   ![Ilustracja 23. Ustawianie atrybutów zabezpieczeń obiektu nazwa klastra na kworum udziałów plików][sap-ha-guide-figure-3022]

   _**Rysunek 23.** Ustawianie atrybutów zabezpieczeń obiektu nazwa klastra na kworum udziałów plików_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ustaw kworum monitora udziału plików w Menedżer klastra trybu failover

1. Otwórz Kreatora konfiguracji ustawienia kworum.

   ![Ilustracja 24. Uruchamianie Kreatora konfiguracji ustawień kworum klastra][sap-ha-guide-figure-3023]

   _**Rysunek 24.** Uruchom Kreatora konfigurowania ustawień kworum klastra_

2. Na stronie **Wybieranie opcji konfiguracji kworum** wybierz pozycję **Wybierz monitor kworum**.

   ![Ilustracja 25. konfiguracje kworum, z których można wybierać][sap-ha-guide-figure-3024]

   _**Ilustracja 25:** Konfiguracje kworum, z których można wybierać_

3. Na stronie **Wybieranie monitora kworum** wybierz opcję **Konfiguruj Monitor udziału plików**.

   ![Ilustracja 26. Wybieranie monitora udziału plików][sap-ha-guide-figure-3025]

   _**Rysunek 26:** Wybierz monitor udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików (w naszym przykładzie \\domcontr-0\FSW). Aby wyświetlić listę zmian, które można wprowadzić, wybierz pozycję **dalej**.

   ![Ilustracja 27. Definiowanie lokalizacji udziału plików dla udziału monitora][sap-ha-guide-figure-3026]

   _**Rysunek 27:** Zdefiniuj lokalizację udziału plików dla udziału monitora_

5. Wybierz żądane zmiany, a następnie wybierz przycisk **dalej**. Należy pomyślnie ponownie skonfigurować konfigurację klastra, jak pokazano na rysunku 28:  

   ![Ilustracja 28. potwierdzenie, że klaster został ponownie skonfigurowany][sap-ha-guide-figure-3027]

   _**Ilustracja 28.** Potwierdzenie, że klaster został ponownie skonfigurowany_

Po pomyślnym zainstalowaniu klastra trybu failover systemu Windows należy zmienić niektóre progi, aby dostosować wykrywanie trybu failover do warunków na platformie Azure. Parametry, które mają zostać zmienione, są udokumentowane w obszarze [progi sieci klastra trybu failover dostrajania][tuning-failover-cluster-network-thresholds]. Przy założeniu, że dwie maszyny wirtualne tworzące konfigurację klastra systemu Windows dla ASCS/SCS znajdują się w tej samej podsieci, Zmień następujące parametry na te wartości:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Te ustawienia zostały przetestowane przez klientów i oferują dobry kompromis. Są one wystarczająco odporne, ale zapewniają również tryb failover, który jest wystarczająco szybki w rzeczywistych warunkach błędów w oprogramowaniu SAP lub w awarii węzła lub maszyny wirtualnej.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition dla dysku udziału klastra SAP ASCS/SCS

Masz już działającą konfigurację klastra trybu failover systemu Windows Server na platformie Azure. Do zainstalowania wystąpienia oprogramowania SAP ASCS/SCS potrzebny jest zasób dysku udostępnionego. Nie można utworzyć udostępnionych zasobów dyskowych na platformie Azure. OPROGRAMOWANIE SIOS DataKeeper Cluster Edition to rozwiązanie innych firm, za pomocą którego można tworzyć udostępnione zasoby dyskowe.

Instalowanie oprogramowanie SIOS DataKeeper Cluster w przypadku dysku udziału klastra SAP ASCS/SCS obejmuje następujące zadania:

- Dodaj Microsoft .NET Framework 3,5.
- Zainstaluj oprogramowanie SIOS DataKeeper.
- Skonfiguruj oprogramowanie SIOS DataKeeper.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Dodaj .NET Framework 3,5
.NET Framework 3,5 nie jest automatycznie aktywowany ani instalowany w systemie Windows Server 2012 R2. Ponieważ oprogramowanie SIOS DataKeeper wymaga, aby program .NET znajdował się we wszystkich węzłach, w których zainstalowano program DataKeeper, należy zainstalować .NET Framework 3,5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania .NET Framework 3,5:

- Użyj Kreatora dodawania ról i funkcji w systemie Windows, jak pokazano na rysunku 29:

  ![Ilustracja 29. Instalowanie .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3028]

  _**Rysunek 29:** Instalowanie .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji_

  ![Rysunek 30: pasek postępu instalacji podczas instalowania .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3029]

  _**Rysunek 30:** Pasek postępu instalacji podczas instalowania .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji_

- Użyj narzędzia wiersza polecenia Dism. exe. W przypadku tego typu instalacji należy uzyskać dostęp do katalogu SxS na nośniku instalacyjnym systemu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wprowadź następujące polecenie:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>Zainstaluj program oprogramowanie SIOS DataKeeper

Zainstaluj program oprogramowanie SIOS DataKeeper Cluster Edition na każdym węźle w klastrze. Aby utworzyć wirtualny magazyn udostępniony z oprogramowanie SIOS DataKeeper, Utwórz zsynchronizowane dublowanie i Symuluj magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania oprogramowanie SIOS należy utworzyć użytkownika domeny DataKeeperSvc.

> [!NOTE]
> Dodaj użytkownika domeny DataKeeperSvc do lokalnej grupy administratorów na obu węzłach klastra.
>
>

Aby zainstalować program oprogramowanie SIOS DataKeeper:

1. Zainstaluj oprogramowanie oprogramowanie SIOS na obu węzłach klastra.

   ![Instalator oprogramowanie SIOS][sap-ha-guide-figure-3030]

   ![Rysunek 31. Pierwsza strona instalacji elementu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Rysunek 31.** Pierwsza strona instalacji elementu oprogramowanie SIOS DataKeeper_

2. W oknie dialogowym wybierz pozycję **tak**.

   ![Ilustracja 32: usługa DataKeeper informuje o tym, że usługi zostaną wyłączone][sap-ha-guide-figure-3032]

   _**Rysunek 32:** Element informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym zalecamy wybranie **konta domeny lub serwera**.

   ![Rysunek 33: wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Rysunek 33:** Wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper_

4. Wprowadź nazwę użytkownika i hasło konta domeny, które zostały utworzone dla oprogramowanie SIOS DataKeeper.

   ![Rysunek 34: Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Rysunek 34:** Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper_

5. Zainstaluj klucz licencji dla wystąpienia oprogramowanie SIOS DataKeeper, jak pokazano na rysunku 35.

   ![Rysunek 35: Wprowadź klucz licencji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Rysunek 35:** Wprowadź klucz licencji oprogramowanie SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Skonfiguruj oprogramowanie SIOScy

Po zainstalowaniu usługi oprogramowanie SIOS DataKeeper na obu węzłach Uruchom konfigurację. Celem konfiguracji jest posiadanie synchronicznej replikacji danych między dodatkowymi dyskami dołączonymi do każdej maszyny wirtualnej.

1. Uruchom narzędzie do zarządzania i konfiguracji programu DataKeeper, a następnie wybierz pozycję **Połącz serwer**.

   ![Ilustracja 36: oprogramowanie SIOS narzędzia do zarządzania i konfiguracji][sap-ha-guide-figure-3036]

   _**Rysunek 36:** Narzędzie do zarządzania i konfiguracji oprogramowanie SIOSer_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł.

   ![Rysunek 37: Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł][sap-ha-guide-figure-3037]

   _**Rysunek 37:** Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Rysunek 38: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Rysunek 38:** Tworzenie zadania replikacji_

   Kreator przeprowadzi Cię przez proces tworzenia zadania replikacji.

4. Zdefiniuj nazwę zadania replikacji.

   ![Rysunek 39: Definiowanie nazwy zadania replikacji][sap-ha-guide-figure-3039]

   _**Rysunek 39:** Zdefiniuj nazwę zadania replikacji_

   ![Rysunek 40: Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym][sap-ha-guide-figure-3040]

   _**Rysunek 40:** Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Rysunek 41: Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego][sap-ha-guide-figure-3041]

   _**Rysunek 41:** Zdefiniuj nazwę, adres TCP/IP i wolumin dysku bieżącego węzła docelowego_

6. Zdefiniuj algorytmy kompresji. W naszym przykładzie zalecamy kompresję strumienia replikacji. W szczególności w sytuacjach ponownych synchronizacji kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Kompresja wykorzystuje zasoby procesora CPU i pamięci RAM maszyny wirtualnej. W miarę wzrostu szybkości kompresji jest to ilość używanych zasobów procesora CPU. Można dostosować to ustawienie później.

7. Innym ustawieniem należy sprawdzić, czy replikacja odbywa się asynchronicznie czy synchronicznie. W przypadku ochrony konfiguracji oprogramowania SAP ASCS/SCS należy użyć replikacji synchronicznej.  

   ![Rysunek 42: Definiowanie szczegółów replikacji][sap-ha-guide-figure-3042]

   _**Rysunek 42:** Zdefiniuj Szczegóły replikacji_

8. Zdefiniuj, czy wolumin replikowany przez zadanie replikacji ma być reprezentowany przez konfigurację klastra trybu failover systemu Windows Server jako dysk udostępniony. W przypadku konfiguracji SAP ASCS/SCS wybierz opcję **tak** , aby klaster systemu Windows widział wolumin replikowany jako dysk udostępniony, który może być używany jako wolumin klastra.

   ![Rysunek 43: wybierz pozycję tak, aby ustawić wolumin replikowany jako wolumin klastra][sap-ha-guide-figure-3043]

   _**Rysunek 43:** Wybierz pozycję **tak** , aby ustawić wolumin replikowany jako wolumin klastra_

   Po utworzeniu woluminu narzędzie zarządzania i konfiguracji programu DataManagement wskazuje, że zadanie replikacji jest aktywne.

   ![Rysunek 44: funkcja dublowania synchronicznego dla dysku udziału ASCS/SCS jest aktywna][sap-ha-guide-figure-3044]

   _**Rysunek 44:** Funkcja dublowania synchronicznego dla dysku udziału SAP ASCS/SCS jest aktywna_

   Menedżer klastra trybu failover teraz przedstawia dysk jako dysk programu do przechowywania danych, jak pokazano na rysunku 45:

   ![Rysunek 45: Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik][sap-ha-guide-figure-3045]

   _**Rysunek 45:** Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik_

## <a name="next-steps"></a>Następne kroki

* [Instalowanie oprogramowania SAP NetWeaver HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
