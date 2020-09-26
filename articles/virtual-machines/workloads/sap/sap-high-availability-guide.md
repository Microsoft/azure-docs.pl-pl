---
title: Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver
description: W tym artykule dowiesz się więcej na temat Virtual Machines platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a330fc18f39ffd7007e2a41a28016df69a2b739
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314661"
---
# <a name="high-availability-azure-virtual-machines-for-sap-netweaver"></a>Virtual Machines platformy Azure o wysokiej dostępności dla oprogramowania SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md



Usługa Azure Virtual Machines to rozwiązanie dla organizacji, które potrzebują zasobów obliczeniowych, magazynu i sieci, w minimalnym czasie i bez długotrwałych cykli zakupów. Za pomocą usługi Azure Virtual Machines można wdrażać aplikacje klasyczne, takie jak SAP NetWeaver-based ABAP, Java i ABAP + Java. Zwiększ niezawodność i dostępność bez dodatkowych zasobów lokalnych. Usługa Azure Virtual Machines obsługuje łączność między środowiskami lokalnymi, dzięki czemu możesz zintegrować usługę Azure Virtual Machines z domenami lokalnymi organizacji, chmurami prywatnymi i systemem SAP system.

W tym artykule opisano kroki, które należy wykonać w celu wdrożenia systemów SAP o wysokiej dostępności na platformie Azure przy użyciu modelu wdrażania Azure Resource Manager. Przeprowadzimy Cię przez następujące główne zadania:

* Znajdź odpowiednie informacje i wskazówki dotyczące oprogramowania SAP, wymienione w sekcji [resources][sap-ha-guide-2] . W tym artykule opisano dokumentację instalacji SAP oraz uwagi SAP, które są zasobami podstawowymi, które mogą pomóc w instalacji i wdrożeniu oprogramowania SAP na określonych platformach.
* Poznaj różnice między modelem wdrażania Azure Resource Manager i klasycznym modelem wdrażania platformy Azure.
* Dowiedz się więcej na temat trybów kworum usługi Windows Server Failover Clustering, aby wybrać model odpowiedni dla danego wdrożenia platformy Azure.
* Dowiedz się więcej o magazynie udostępnionym usługi Windows Server Failover Clustering w usługach platformy Azure.
* Dowiedz się, jak chronić składniki pojedynczego punktu awarii, takie jak zaawansowane Programowanie aplikacji biznesowej (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) i systemy zarządzania bazami danych (DBMS) oraz nadmiarowe składniki, takie jak serwer aplikacji SAP, na platformie Azure.
* Wykonaj krok po kroku instalację i konfigurację systemu SAP o wysokiej dostępności w klastrze usługi Windows Server Failover Clustering na platformie Azure przy użyciu Azure Resource Manager.
* Zapoznaj się z dodatkowymi krokami wymaganymi do korzystania z klastra trybu failover systemu Windows Server na platformie Azure, ale które nie są wymagane w przypadku wdrożenia lokalnego.

Aby uprościć wdrażanie i konfigurację, w tym artykule używamy szablonów Menedżer zasobów o wysokiej dostępności w oprogramowaniu SAP. Szablony automatyzują wdrażanie całej infrastruktury niezbędnej dla systemu SAP o wysokiej dostępności. Infrastruktura obsługuje również ustalanie rozmiarów systemu SAP przez oprogramowanie SAP Application Performance Standard (soki).

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Wymagany
Przed rozpoczęciem upewnij się, że spełniasz wymagania wstępne opisane w poniższych sekcjach. Upewnij się również, że wszystkie zasoby wymienione w sekcji [resources][sap-ha-guide-2] są zaznaczone.

W tym artykule używamy szablonów Azure Resource Manager dla [trzech warstw SAP NetWeaver przy użyciu Managed disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Aby zapoznać się z przydatnym omówieniem szablonów, zobacz [szablony Azure Resource Manager SAP](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Produkcyjnych
Te artykuły obejmują wdrożenia oprogramowania SAP na platformie Azure:

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP NetWeaver][dbms-guide]
* [Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver (ten przewodnik)][sap-ha-guide]

> [!NOTE]
> Jeśli to możliwe, udostępniamy link do przewodnika instalacji oprogramowania SAP (zobacz [przewodniki instalacji SAP][sap-installation-guides]). Aby uzyskać wymagania wstępne i informacje o procesie instalacji, warto uważnie przeczytać przewodniki instalacji oprogramowania SAP NetWeaver. W tym artykule omówiono tylko określone zadania dla systemów opartych na systemie SAP NetWeaver, których można używać z usługą Azure Virtual Machines.
>
>

Te uwagi SAP są powiązane z tematem SAP na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i rozmiary |
| [2015553] |SAP on Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Ulepszone monitorowanie platformy Azure dla oprogramowania SAP |
| [2178632] |Metryki monitorowania kluczy dla SAP na Microsoft Azure |
| [1999351] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2243692] |Korzystanie z usługi Azure SSD w warstwie Premium Storage dla wystąpienia SAP DBMS |

Dowiedz się więcej o [ograniczeniach subskrypcji platformy Azure][azure-resource-manager/management/azure-subscription-service-limits-subscription], w tym ogólnych ograniczeniach i maksymalnych ograniczeniach.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Rozwiązanie SAP o wysokiej dostępności z Azure Resource Manager a klasycznym modelem wdrażania platformy Azure
Azure Resource Manager i klasyczny model wdrażania platformy Azure różnią się w następujących obszarach:

- Grupy zasobów
- Zależność wewnętrznego modułu równoważenia obciążenia platformy Azure w grupie zasobów platformy Azure
- Obsługa scenariuszy obejmujących wiele identyfikatorów SID SAP

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Grupy zasobów
W Azure Resource Manager można używać grup zasobów do zarządzania wszystkimi zasobami aplikacji w ramach subskrypcji platformy Azure. Zintegrowane podejście, w grupie zasobów, wszystkie zasoby mają ten sam cykl życia. Na przykład wszystkie zasoby są tworzone w tym samym czasie i są usuwane w tym samym czasie. Dowiedz się więcej o [grupach zasobów](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Zależność wewnętrznego modułu równoważenia obciążenia platformy Azure w grupie zasobów platformy Azure

W klasycznym modelu wdrażania platformy Azure istnieje zależność między wewnętrznym modułem równoważenia obciążenia platformy Azure (Azure Load Balancer usługą) a usługą w chmurze. Każdy wewnętrzny moduł równoważenia obciążenia potrzebuje jednej usługi w chmurze.

W Azure Resource Manager każdy zasób platformy Azure musi być umieszczony w grupie zasobów platformy Azure i jest również prawidłowy dla Azure Load Balancer. Nie ma jednak potrzeby posiadania jednej grupy zasobów platformy Azure na moduł równoważenia obciążenia platformy Azure, np. jedna grupa zasobów platformy Azure może zawierać wiele modułów równoważenia obciążenia platformy Azure. Środowisko jest prostsze i bardziej elastyczne. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Obsługa scenariuszy obejmujących wiele identyfikatorów SID SAP

W Azure Resource Manager można zainstalować wiele wystąpień ASCS/SCS identyfikatora systemu SAP w jednym klastrze. Wystąpienia wielu identyfikatorów SID są możliwe z powodu obsługi wielu adresów IP dla każdego wewnętrznego modułu równoważenia obciążenia platformy Azure.

Aby używać klasycznego modelu wdrażania platformy Azure, postępuj zgodnie z procedurami opisanymi w temacie [SAP NetWeaver na platformie Azure: klastrowanie wystąpień SAP ASCS/SCS przy użyciu funkcji klaster trybu failover systemu Windows Server na platformie Azure z usługą oprogramowanie SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Zdecydowanie zalecamy używanie Azure Resource Manager model wdrażania dla instalacji SAP. Oferuje wiele korzyści, które nie są dostępne w klasycznym modelu wdrażania. Dowiedz się więcej o [modelach wdrażania][virtual-machines-azure-resource-manager-architecture-benefits-arm] platformy Azure.   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Klaster trybu failover z systemem Windows Server
Klaster pracy awaryjnej systemu Windows Server to podstawa instalacji oprogramowania SAP ASCS/SCS o wysokiej dostępności w systemie Windows.

Klaster trybu failover jest grupą serwerów niezależnych od 1 + n (węzłów), które współpracują ze sobą w celu zwiększenia dostępności aplikacji i usług. Jeśli wystąpi awaria węzła, klaster trybu failover systemu Windows Server oblicza liczbę błędów, które mogą wystąpić podczas utrzymywania prawidłowego klastra w celu dostarczania aplikacji i usług. Możesz wybrać inny tryb kworum, aby osiągnąć klaster trybu failover.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Tryby kworum
Korzystając z klastra trybu failover systemu Windows Server, można wybrać jeden z czterech trybów kworum:

* **Większość węzłów**. Każdy węzeł klastra może głosować. Klaster działa tylko z większością głosów, czyli z więcej niż połowa głosów. Zalecamy korzystanie z tej opcji w przypadku klastrów, które mają nieparzystą liczbę węzłów. Na przykład trzy węzły w klastrze z siedmiu węzłami mogą kończyć się niepowodzeniem, a klaster nadal osiąga większość i nadal działa.  
* **Większość węzłów i dysków**. Każdy węzeł i wydzielony dysk (Monitor dysku) w magazynie klastra mogą głosować, gdy są dostępne i w komunikacji. Klaster działa tylko z większością głosów, czyli z więcej niż połowa głosów. Ten tryb ma sens w środowisku klastra z parzystą liczbą węzłów. Jeśli połowa węzłów i dysku są w trybie online, klaster pozostaje w dobrej kondycji.
* **Większość węzłów i udziałów plików**. Każdy węzeł oraz wytworzony udział plików (Monitor udostępniania plików), który może zostać zagłosowany przez administratora, bez względu na to, czy węzły i udziały plików są dostępne i w komunikacji. Klaster działa tylko z większością głosów, czyli z więcej niż połowa głosów. Ten tryb ma sens w środowisku klastra z parzystą liczbą węzłów. Jest to podobne do trybu większość węzłów i dysków, ale używa udziału plików monitora zamiast dysku monitora. Ten tryb jest łatwy do zaimplementowania, ale jeśli sam udział plików nie ma wysokiej dostępności, może stać się single point of failure.
* **Brak większości: tylko dysk**. Klaster ma kworum, jeśli jeden węzeł jest dostępny i w komunikacji z określonym dyskiem w magazynie klastra. Tylko węzły, które również komunikują się z tym dyskiem, mogą dołączyć do klastra. Zalecamy, aby nie korzystać z tego trybu.


## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Lokalne usługi Windows Server Failover Clustering
Rysunek 1 przedstawia klaster dwóch węzłów. Jeśli połączenie sieciowe między węzłami zakończy się niepowodzeniem, a oba węzły zostaną uruchomione, dysk kworum lub udział plików określa, który węzeł będzie kontynuował udostępnianie aplikacji i usług klastra. Węzeł, który ma dostęp do dysku kworum lub udziału plików, jest węzłem, który gwarantuje, że usługi będą kontynuowane.

Ponieważ w tym przykładzie użyto klastra z dwoma węzłami, używany jest tryb kworum większość węzłów i udziałów plików. Większość węzłów i dysków jest również prawidłową opcją. W środowisku produkcyjnym zalecamy używanie dysku kworum. Aby zapewnić wysoką dostępność, można użyć technologii systemu sieci i magazynu.

![Rysunek 1. przykład konfiguracji klastra trybu failover z systemem Windows Server dla oprogramowania SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-1000]

_**Rysunek 1.** Przykład konfiguracji klastra trybu failover systemu Windows Server dla oprogramowania SAP ASCS/SCS na platformie Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Magazyn udostępniony
Rysunek 1 przedstawia także klaster magazynu udostępnionego z dwoma węzłami. W lokalnym klastrze magazynu udostępnionego wszystkie węzły w klastrze wykrywają magazyn udostępniony. Mechanizm blokowania chroni dane przed uszkodzeniem. Wszystkie węzły mogą wykryć, czy inny węzeł ulegnie awarii. Jeśli jeden węzeł ulegnie awarii, pozostały węzeł przejmuje własność zasobów magazynu i zapewnia dostępność usług.

> [!NOTE]
> Dyski udostępnione nie są potrzebne w celu zapewnienia wysokiej dostępności w niektórych aplikacjach DBMS, takich jak SQL Server. SQL Server zawsze w przypadku replikowania danych i plików dziennika systemu DBMS z dysku lokalnego jednego węzła klastra na dysk lokalny innego węzła klastra. W takim przypadku Konfiguracja klastra systemu Windows nie wymaga dysku udostępnionego.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Rozpoznawanie sieci i nazw
Komputery klienckie docierają do klastra za pośrednictwem wirtualnego adresu IP i nazwy hosta wirtualnego, który zapewnia serwer DNS. Węzły lokalne i serwer DNS mogą obsługiwać wiele adresów IP.

W typowej konfiguracji należy użyć co najmniej dwóch połączeń sieciowych:

* Dedykowane połączenie z magazynem
* Klaster — wewnętrzne połączenie sieciowe dla pulsu
* Sieć publiczna używana przez klientów do łączenia się z klastrem

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Klaster trybu failover z systemem Windows Server na platformie Azure
W porównaniu z wdrożeniami w chmurze bez systemu operacyjnego usługa Azure Virtual Machines wymaga dodatkowych kroków, aby skonfigurować klaster trybu failover systemu Windows Server. Podczas tworzenia udostępnionego dysku klastra należy ustawić kilka adresów IP i nazw hostów wirtualnych dla wystąpienia SAP ASCS/SCS.

W tym artykule omówiono kluczowe pojęcia i dodatkowe kroki wymagane do utworzenia klastra usług centralnych o wysokiej dostępności SAP na platformie Azure. Pokazujemy, jak skonfigurować narzędzie innej firmy oprogramowanie SIOS DataKeeper i jak skonfigurować wewnętrzny moduł równoważenia obciążenia platformy Azure. Za pomocą tych narzędzi można utworzyć klaster trybu failover systemu Windows przy użyciu monitora udostępniania plików na platformie Azure.

![Rysunek 2. Konfiguracja klastra trybu failover z systemem Windows Server na platformie Azure bez dysku udostępnionego][sap-ha-guide-figure-1001]

_**Rysunek 2.** Konfiguracja klastra trybu failover systemu Windows Server na platformie Azure bez dysku udostępnionego_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Dysk udostępniony na platformie Azure z usługą oprogramowanie SIOS DataKeeper
Magazyn udostępniony klastra jest potrzebny do wystąpienia usługi SAP ASCS/SCS o wysokiej dostępności. Od września 2016 Platforma Azure nie oferuje magazynu udostępnionego, którego można użyć do utworzenia klastra magazynu udostępnionego. Aby utworzyć dublowany magazyn, który symuluje magazyn udostępniony klastra, można użyć oprogramowania oprogramowanie SIOS DataKeeper klastra. Rozwiązanie oprogramowanie SIOS zapewnia synchroniczną replikację danych w czasie rzeczywistym. Oto jak można utworzyć zasób dysku udostępnionego dla klastra:

1. Dołącz dodatkowy dysk do każdej maszyny wirtualnej w konfiguracji klastra systemu Windows.
2. Uruchom oprogramowanie SIOS DataKeeper Cluster Edition w obu węzłach maszyn wirtualnych.
3. Skonfiguruj program oprogramowanie SIOS DataKeeper Cluster w taki sposób, aby replikować zawartość dodatkowego woluminu dołączonego do dysku ze źródłowej maszyny wirtualnej do dodatkowego woluminu dołączonego do dysku docelowej maszyny wirtualnej. OPROGRAMOWANIE SIOS DataKeeper abstrakcyjne źródłowe i docelowe woluminy lokalne, a następnie prezentuje je do klastra trybu failover systemu Windows Server jako jednego dysku udostępnionego.

Uzyskaj więcej informacji na temat [oprogramowanie SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Rysunek 3. Konfiguracja klastra trybu failover z systemem Windows Server na platformie Azure z usługą oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 3.** Konfiguracja klastra trybu failover systemu Windows Server na platformie Azure z usługą oprogramowanie SIOS DataKeeper_

> [!NOTE]
> Dyski udostępnione nie są potrzebne, aby zapewnić wysoką dostępność w przypadku niektórych produktów DBMS, takich jak SQL Server. SQL Server zawsze w przypadku replikowania danych i plików dziennika systemu DBMS z dysku lokalnego jednego węzła klastra na dysk lokalny innego węzła klastra. W takim przypadku Konfiguracja klastra systemu Windows nie wymaga dysku udostępnionego.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Rozpoznawanie nazw na platformie Azure
Platforma Azure Cloud Platform nie oferuje opcji konfigurowania wirtualnych adresów IP, takich jak zmiennoprzecinkowe adresy IP. Do skonfigurowania wirtualnego adresu IP w celu uzyskania dostępu do zasobu klastra w chmurze potrzebne jest alternatywne rozwiązanie.
Platforma Azure ma wewnętrzny moduł równoważenia obciążenia w usłudze Azure Load Balancer. W przypadku wewnętrznego modułu równoważenia obciążenia klienci docierają do klastra za pośrednictwem wirtualnego adresu IP klastra.
Należy wdrożyć wewnętrzny moduł równoważenia obciążenia w grupie zasobów zawierającej węzły klastra. Następnie należy skonfigurować wszystkie niezbędne reguły przekazywania portów z portami sondowania wewnętrznego modułu równoważenia obciążenia.
Klienci mogą łączyć się za pośrednictwem nazwy hosta wirtualnego. Serwer DNS rozpoznaje adres IP klastra, a wewnętrzny moduł równoważenia obciążenia obsługuje przekazywanie portów do aktywnego węzła klastra.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Rozwiązanie SAP NetWeaver o wysokiej dostępności w infrastrukturze platformy Azure jako usługi (IaaS)
Aby zapewnić wysoką dostępność aplikacji SAP, na przykład dla składników oprogramowania SAP, należy chronić następujące składniki:

* Wystąpienie serwera aplikacji SAP
* Wystąpienie SAP ASCS/SCS
* Serwer DBMS

Aby uzyskać więcej informacji na temat ochrony składników SAP w scenariuszach wysokiej dostępności, zobacz temat [Azure Virtual Machines Planning and implementation for SAP NetWeaver][planning-guide-11].

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Serwer aplikacji SAP o wysokiej dostępności
Zwykle nie jest potrzebne konkretne rozwiązanie wysokiej dostępności dla serwera aplikacji SAP i wystąpień okna dialogowego. Wysoką dostępność można osiągnąć dzięki nadmiarowości i można skonfigurować wiele wystąpień okna dialogowego w różnych wystąpieniach Virtual Machines platformy Azure. W dwóch wystąpieniach usługi Azure Virtual Machines należy zainstalować co najmniej dwa wystąpienia aplikacji SAP.

![Rysunek 4. serwer aplikacji SAP o wysokiej dostępności][sap-ha-guide-figure-2000]

_**Rysunek 4.** Serwer aplikacji SAP o wysokiej dostępności_

Wszystkie maszyny wirtualne obsługujące wystąpienia serwera aplikacji SAP należy umieścić w tym samym zestawie dostępności platformy Azure. Zestaw dostępności platformy Azure gwarantuje, że:

* Wszystkie maszyny wirtualne są częścią tej samej domeny uaktualnienia. Na przykład domena uaktualnienia gwarantuje, że maszyny wirtualne nie są aktualizowane w tym samym czasie podczas planowanego przestoju konserwacji.
* Wszystkie maszyny wirtualne są częścią tej samej domeny błędów. Domena błędów, na przykład, sprawdza, czy maszyny wirtualne są wdrożone, tak aby żadna single point of failure nie miała wpływu na dostępność wszystkich maszyn wirtualnych.

Dowiedz się więcej o tym, jak [zarządzać dostępnością maszyn wirtualnych] [... /manage-availability.md].

Tylko dysk niezarządzany: ponieważ konto usługi Azure Storage jest potencjalnym single point of failure, ważne jest, aby mieć co najmniej dwa konta magazynu platformy Azure, w którym są dystrybuowane co najmniej dwie maszyny wirtualne. W idealnym instalacji dyski każdej maszyny wirtualnej, na której działa wystąpienie okna dialogowego SAP, zostaną wdrożone na innym koncie magazynu.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Wystąpienie środowiska SAP ASCS/SCS o wysokiej dostępności
Rysunek 5 to przykład wystąpienia o wysokiej dostępności SAP ASCS/SCS.

![Rysunek 5: wystąpienie SAP ASCS/SCS o wysokiej dostępności][sap-ha-guide-figure-2001]

_**Rysunek 5.** Wystąpienie środowiska SAP ASCS/SCS o wysokiej dostępności_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Wysoka dostępność wystąpienia oprogramowania SAP ASCS/SCS z funkcją klaster trybu failover systemu Windows Server na platformie Azure
W porównaniu z wdrożeniami w chmurze bez systemu operacyjnego usługa Azure Virtual Machines wymaga dodatkowych kroków, aby skonfigurować klaster trybu failover systemu Windows Server. Aby zbudować klaster trybu failover systemu Windows, potrzebny jest udostępniony dysk klastra, kilka adresów IP, kilka nazw hostów wirtualnych i wewnętrzny moduł równoważenia obciążenia platformy Azure do klastrowania wystąpienia SAP ASCS/SCS. Omawiamy to bardziej szczegółowo w dalszej części artykułu.

![Ilustracja 6. klaster trybu failover z systemem Windows Server dla konfiguracji oprogramowania SAP ASCS/SCS na platformie Azure przy użyciu funkcji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Rysunek 6.** Klaster trybu failover systemu Windows Server dla konfiguracji oprogramowania SAP ASCS/SCS na platformie Azure z usługą oprogramowanie SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Wystąpienie systemu DBMS o wysokiej dostępności
System DBMS również jest pojedynczym punktem kontaktu w systemie SAP. Należy chronić je za pomocą rozwiązania o wysokiej dostępności. Na rysunku nr 7 SQL Server przedstawiono na platformie Azure rozwiązanie o wysokiej dostępności, z funkcją klaster trybu failover systemu Windows Server i wewnętrznym modułem równoważenia obciążenia platformy Azure. SQL Server zawsze w przypadku replikowania danych i plików dziennika systemu DBMS przy użyciu własnej replikacji w systemie DBMS. W takim przypadku nie są wymagane dyski udostępnione klastra, co upraszcza całą konfigurację.

![Rysunek 7. przykład środowiska SAP DBMS o wysokiej dostępności z funkcją SQL Server zawsze włączona][sap-ha-guide-figure-2003]

_**Rysunek 7.** Przykładem rozwiązania SAP DBMS o wysokiej dostępności z funkcją SQL Server Always On_

Aby uzyskać więcej informacji na temat klastrowania SQL Server na platformie Azure przy użyciu modelu wdrażania Azure Resource Manager, zobacz następujące artykuły:

* [Skonfiguruj zawsze włączone grupy dostępności na platformie Azure Virtual Machines ręcznie, używając Menedżer zasobów] [Virtual-Machines-Windows-Portal-SQL-AlwaysOn-Availability-Groups-Manual]
* [Konfigurowanie wewnętrznego modułu równoważenia obciążenia platformy Azure dla zawsze włączonych grup dostępności na platformie Azure] [Virtual-Machines-Windows-Portal-SQL-AlwaysOn-int-Listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Kompleksowe scenariusze wdrażania wysokiej dostępności

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenariusz wdrażania przy użyciu szablonu architektury 1

Na rysunku nr 8 przedstawiono przykład architektury wysokiej dostępności SAP NetWeaver na platformie Azure dla **jednego** systemu SAP. Ten scenariusz jest ustawiany w następujący sposób:

- Jeden dedykowany klaster jest używany dla wystąpienia SAP ASCS/SCS.
- Dla wystąpienia systemu DBMS jest używany jeden dedykowany klaster.
- Wystąpienia serwera aplikacji SAP są wdrażane na własnych dedykowanych maszynach wirtualnych.

![Ilustracja 8. szablon architektoniczny wysokiej dostępności SAP z dedykowanym klastrem dla systemów ASCS/SCS i DBMS][sap-ha-guide-figure-2004]

_**Rysunek 8.** Szablon architektoniczny o wysokiej dostępności SAP — dedykowane klastry dla systemów ASCS/SCS i DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenariusz wdrażania przy użyciu szablonu architektury 2

Na rysunku nr 9 przedstawiono przykład architektury wysokiej dostępności SAP NetWeaver na platformie Azure dla **jednego** systemu SAP. Ten scenariusz jest ustawiany w następujący sposób:

- Jeden dedykowany klaster jest używany **zarówno** w przypadku wystąpienia SAP ASCS/SCS, jak i w systemie DBMS.
- Wystąpienia serwera aplikacji SAP są wdrażane na własnych dedykowanych maszynach wirtualnych.

![Rysunek 9. szablon architektoniczny wysokiej dostępności SAP z dedykowanym klastrem dla ASCS/SCS i dedykowanego klastra dla systemu DBMS][sap-ha-guide-figure-2005]

_**Rysunek 9.** Szablon architektoniczny wysokiej dostępności SAP z dedykowanym klastrem dla ASCS/SCS i dedykowany klaster dla systemu DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenariusz wdrażania przy użyciu szablonu architektury 3

Na rysunku nr 10 przedstawiono przykład architektury wysokiej dostępności SAP NetWeaver na platformie Azure dla **dwóch** systemów SAP z &lt; SID1 &gt; i &lt; SID2 &gt; . Ten scenariusz jest ustawiany w następujący sposób:

- Jeden dedykowany klaster jest używany **dla wystąpienia** SID1 SAP ASCS/SCS *i* wystąpienia SAP ASCS/SCS SID2 (jeden klaster).
- Jeden dedykowany klaster jest używany w systemie DBMS SID1, a inny dedykowany klaster jest używany dla systemu DBMS SID2 (dwa klastry).
- Wystąpienia serwera aplikacji SAP dla systemu SAP SID1 mają własne dedykowane maszyny wirtualne.
- Wystąpienia serwera aplikacji SAP dla systemu SAP SID2 mają własne dedykowane maszyny wirtualne.

![Rysunek 10. szablon architektoniczny wysokiej dostępności SAP z dedykowanym klastrem dla różnych wystąpień ASCS/SCS][sap-ha-guide-figure-6003]

_**Rysunek 10.** Szablon architektoniczny wysokiej dostępności SAP z dedykowanym klastrem dla różnych wystąpień ASCS/SCS_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Przygotowywanie infrastruktury

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Przygotowywanie infrastruktury dla szablonu architektury 1
Szablony Azure Resource Manager dla oprogramowania SAP upraszczają wdrażanie wymaganych zasobów.

Szablony trzech warstw w Azure Resource Manager obsługują również scenariusze wysokiej dostępności, takie jak szablon architektury 1, który ma dwa klastry. Każdy klaster jest single point of failure SAP dla oprogramowania SAP ASCS/SCS i DBMS.

Oto, gdzie można uzyskać Azure Resource Manager szablonów dla przykładowego scenariusza opisywanego w tym artykule:

* [Obraz witryny Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Obraz portalu Azure Marketplace korzystający Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/)
* [Obraz niestandardowy przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Aby przygotować infrastrukturę dla szablonu architektury 1:

- W Azure Portal w bloku **Parametry** w polu **SYSTEMAVAILABILITY** wybierz pozycję **ha**.

  ![Ilustracja 11. Ustawianie Azure Resource Manager parametrów wysokiej dostępności SAP][sap-ha-guide-figure-3000]

_**Rysunek 11.** Ustaw Azure Resource Manager parametry wysokiej dostępności SAP_


  Szablony tworzą:

  * **Maszyny wirtualne**:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*>-di-<*Number*>
    * Maszyny wirtualne klastra ASCS/SCS: <*SAPSystemSID*>-ASCS-<*Number*>
    * Klaster DBMS: <*SAPSystemSID*>-db-<*Number*>

  * **Karty sieciowe dla wszystkich maszyn wirtualnych ze skojarzonymi adresami IP**:
    * <*SAPSystemSID*>-karta sieciowa-di <*numer*>
    * <*SAPSystemSID*>-nic-ASCS — *numer* <>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Konta usługi Azure Storage (tylko dyski niezarządzane)**

  * **Grupy dostępności** dla:
    * Maszyny wirtualne serwera aplikacji SAP: <*SAPSystemSID*>-avset-di
    * Maszyny wirtualne w klastrze SAP ASCS/SCS: <*SAPSystemSID*>-avset-ASCS
    * Maszyny wirtualne klastra DBMS: <*SAPSystemSID*>-avset-DB

  * **Wewnętrzny moduł równoważenia obciążenia platformy Azure**:
    * Ze wszystkimi portami wystąpienia ASCS/SCS i adresem IP <*SAPSystemSID*>-lb-ASCS
    * Ze wszystkimi portami dla SQL Server systemie DBMS i adresem IP <*SAPSystemSID*>-lb-DB

  * **Sieciowa Grupa zabezpieczeń**: <*SAPSystemSID*>-sieciowej grupy zabezpieczeń-ASCS-0  
    * Z otwartym portem zewnętrznym Remote Desktop Protocol (RDP) na maszynie wirtualnej <*SAPSystemSID*>-ASCS-0

> [!NOTE]
> Wszystkie adresy IP kart sieciowych i wewnętrznych modułów równoważenia obciążenia platformy Azure są domyślnie **dynamiczne** . Zmień je na **statyczne** adresy IP. Opisujemy, jak to zrobić w dalszej części artykułu.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Wdrażanie maszyn wirtualnych z łącznością sieci firmowej (między różnymi lokalizacjami) do użycia w środowisku produkcyjnym
W przypadku produkcyjnych systemów SAP należy wdrożyć maszyny wirtualne platformy Azure z łącznością sieci firmowej przy użyciu sieci VPN typu lokacja-lokacja lub usługi Azure ExpressRoute.

> [!NOTE]
> Możesz użyć wystąpienia usługi Azure Virtual Network. Sieć wirtualna i podsieć została już utworzona i przygotowana.
>
>

1. W Azure Portal, w bloku **Parametry** , w polu **NEWOREXISTINGSUBNET** wybierz pozycję **istniejące**.
2. W polu **SUBNETID** Dodaj pełny ciąg przygotowanych SUBNETID sieci platformy Azure, w którym planujesz wdrożyć maszyny wirtualne platformy Azure.
3. Aby uzyskać listę wszystkich podsieci sieciowych platformy Azure, Uruchom to polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Pole **ID** wskazuje **SUBNETID**.
4. Aby uzyskać listę wszystkich wartości **SUBNETID** , uruchom następujące polecenie programu PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** wygląda następująco:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Wdrażaj wystąpienia SAP tylko w chmurze dla testów i demonstracji
System SAP o wysokiej dostępności można wdrożyć w modelu wdrażania tylko w chmurze. Ten rodzaj wdrożenia jest przydatny w przypadku demonstracji i testowania przypadków użycia. Nie jest to odpowiednie dla przypadków użycia w środowisku produkcyjnym.

- W Azure Portal, w bloku **Parametry** , w polu **NEWOREXISTINGSUBNET** wybierz pozycję **Nowy**. Pozostaw puste pole **SUBNETID** .

  Szablon Azure Resource Manager SAP automatycznie tworzy sieć wirtualną i podsieć platformy Azure.

> [!NOTE]
> Należy również wdrożyć co najmniej jedną dedykowaną maszynę wirtualną dla Active Directory i DNS w tym samym wystąpieniu usługi Azure Virtual Network. Szablon nie tworzy tych maszyn wirtualnych.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Przygotowywanie infrastruktury dla szablonu architektury 2

Za pomocą tego szablonu Azure Resource Manager dla oprogramowania SAP można uprościć wdrażanie wymaganych zasobów infrastruktury dla szablonu architektury SAP Architecture 2.

Oto, gdzie można uzyskać Azure Resource Manager szablonów dla tego scenariusza wdrażania:

* [Obraz witryny Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/)  
* [Obraz portalu Azure Marketplace korzystający Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Obraz niestandardowy](https://github.com/Azure/azure-quickstart-templates/)
* [Obraz niestandardowy przy użyciu Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Przygotowywanie infrastruktury dla szablonu architektury 3

Możesz przygotować infrastrukturę i skonfigurować rozwiązanie SAP dla usługi **wiele identyfikatorów SID**. Można na przykład dodać do *istniejącej* konfiguracji klastra dodatkowe wystąpienie SAP ASCS/SCS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dodatkowego wystąpienia SAP ASCS/SCS w istniejącej konfiguracji klastra w celu utworzenia konfiguracji protokołu SAP dla wielu identyfikatorów SID w Azure Resource Manager][sap-ha-multi-sid-guide].

Jeśli chcesz utworzyć nowy klaster z obsługą identyfikatorów SID, możesz użyć [szablonów szybkiego startu o wiele identyfikatorów SID w serwisie GitHub](https://github.com/Azure/azure-quickstart-templates).
Aby utworzyć nowy klaster z obsługą wiele identyfikatorów SID, należy wdrożyć następujące trzy szablony:

* [Szablon ASCS/SCS](#ASCS-SCS-template)
* [Szablon bazy danych](#database-template)
* [Szablon serwerów aplikacji](#application-servers-template)

Poniższe sekcje zawierają więcej szczegółów na temat szablonów i parametrów, które należy podać w szablonach.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> Szablon ASCS/SCS

Szablon ASCS/SCS wdraża dwie maszyny wirtualne, których można użyć do utworzenia klastra trybu failover systemu Windows Server, który hostuje wiele wystąpień ASCS/SCS.

Aby skonfigurować szablon wieloidentyfikatorowy ASCS/SCS, w szablonie [ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] z szablonem wieloidentyfikatorowym lub [ASCS/SCS przy użyciu Managed disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]wprowadź wartości dla następujących parametrów:

  - **Prefiks zasobu**.  Ustaw prefiks zasobu, który jest używany do prefiksowania wszystkich zasobów utworzonych podczas wdrażania. Ponieważ zasoby nie należą tylko do jednego systemu SAP, prefiks zasobu nie jest identyfikatorem SID jednego systemu SAP.  Prefiks musi zawierać od **3 do 6 znaków**.
  - **Typ stosu**. Wybierz typ stosu systemu SAP. W zależności od typu stosu Azure Load Balancer ma jeden (tylko ABAP lub Java) lub dwa (ABAP + Java) prywatnych adresów IP na system SAP.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Liczba systemów SAP**. Wybierz liczbę systemów SAP, które chcesz zainstalować w tym klastrze.
  -  **Dostępność systemu**. Wybierz pozycję **ha**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Nowa lub istniejąca podsieć**. Określ, czy należy utworzyć nową sieć wirtualną i podsieć, czy powinna być używana istniejąca podsieć. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
  -  **Identyfikator podsieci**. Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Ten identyfikator zwykle wygląda następująco:/subscriptions/<*Identyfikator subskrypcji*>/ResourceGroups/<*nazwa grupy zasobów*>/Providers/Microsoft.Network/virtualnetworks/<*nazwa sieci wirtualnej*>/Subnets/<*Nazwa podsieci*>

Szablon wdraża jedno Azure Load Balancer wystąpienie, które obsługuje wiele systemów SAP.

- Wystąpienia ASCS są konfigurowane dla wystąpienia o numerze 00, 10, 20...
- Wystąpienia SCS są konfigurowane dla wystąpienia numer 01, 11, 21...
- Serwer replikacji ASCS (tylko system wykres WYWOŁUJĄCYCH) (tylko system Linux) jest konfigurowany dla wystąpienia o numerze 02, 12, 22...
- Wystąpienia SCS wykres WYWOŁUJĄCYCH (tylko system Linux) są konfigurowane dla wystąpienia o numerze 03, 13, 23...

Moduł równoważenia obciążenia zawiera adresy VIP: 1 (2 dla systemu Linux), adres VIP 1x dla ASCS/SCS i 1x VIP dla wykres WYWOŁUJĄCYCH (tylko system Linux).

Poniższa lista zawiera wszystkie reguły równoważenia obciążenia (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Porty specyficzne dla systemu Windows dla każdego systemu SAP: 445, 5985
- Porty ASCS (numer wystąpienia x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porty SCS (numer wystąpienia x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS wykres WYWOŁUJĄCYCH Ports w systemie Linux (numer wystąpienia X2): 33x2, 5x213, 5x214, 5x216
- SCS wykres WYWOŁUJĄCYCH Ports w systemie Linux (numer wystąpienia x3): 33x3, 5x313, 5x314, 5x316

Moduł równoważenia obciążenia jest skonfigurowany do używania następujących portów sondowania (gdzie x jest numerem systemu SAP, na przykład 1, 2, 3...):
- Port sondy wewnętrznego modułu równoważenia obciążenia ASCS/SCS: 620x0
- Port sondy wewnętrznego modułu równoważenia obciążenia wykres WYWOŁUJĄCYCH (tylko system Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Szablon bazy danych

Szablon bazy danych wdraża jedną lub dwie maszyny wirtualne, których można użyć do zainstalowania systemu zarządzania relacyjnymi bazami danych (RDBMS) dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon wieloidentyfikatorowego bazy danych, w szablonie [bazy danych o identyfikatorze wielościeżkowym][sap-templates-3-tier-multisid-db-marketplace-image] lub [wieloidentyfikatorze bazy danych przy użyciu Managed disks][sap-templates-3-tier-multisid-db-marketplace-image-md]wprowadź wartości dla następujących parametrów:

- **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator będzie używany jako prefiks dla wdrożonych zasobów.
- **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
- **DbType**. Wybierz typ bazy danych, którą chcesz zainstalować w klastrze. Wybierz pozycję **SQL** , jeśli chcesz zainstalować Microsoft SQL Server. Wybierz pozycję **Hana** , jeśli planujesz zainstalować SAP HANA na maszynach wirtualnych. Upewnij się, że wybrano poprawny typ systemu operacyjnego: wybierz pozycję Windows for SQL i wybierz dystrybucję **systemu** Linux dla platformy Hana. Azure Load Balancer, która jest połączona z maszynami wirtualnymi, zostanie skonfigurowany do obsługi wybranego typu bazy danych:
  * **SQL**. Moduł równoważenia obciążenia będzie równoważyć obciążenie portu 1433. Upewnij się, że ten port jest używany do SQL Server zawsze w instalacji.
  * **Hana**. Moduł równoważenia obciążenia będzie obciążany portami 35015 i 35017. Upewnij się, że SAP HANA jest zainstalowana z numerem wystąpienia **50**.
  Moduł równoważenia obciążenia będzie używać portu sondy 62550.
- **Rozmiar systemu SAP**. Ustaw liczbę punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
- **Dostępność systemu**. Wybierz pozycję **ha**.
- **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
- **Identyfikator podsieci**. Wprowadź identyfikator podsieci, która była używana podczas wdrażania szablonu ASCS/SCS, lub identyfikator podsieci, która została utworzona w ramach wdrożenia szablonu ASCS/SCS.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Szablon serwerów aplikacji

Szablon serwery aplikacji wdraża co najmniej dwie maszyny wirtualne, które mogą być używane jako wystąpienia serwera aplikacji SAP dla jednego systemu SAP. Na przykład w przypadku wdrażania szablonu ASCS/SCS dla pięciu systemów SAP należy wdrożyć ten szablon pięć razy.

Aby skonfigurować szablon z obsługą wieloidentyfikatorów SID dla serwerów aplikacji, w szablonie [z szablonami wielosid lub na][sap-templates-3-tier-multisid-apps-marketplace-image] [serwerach aplikacji z użyciem Managed disks][sap-templates-3-tier-multisid-apps-marketplace-image-md]wprowadź wartości dla następujących parametrów:

  -  **Identyfikator systemu SAP**. Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator będzie używany jako prefiks dla wdrożonych zasobów.
  -  **Typ systemu operacyjnego**. Wybierz system operacyjny maszyn wirtualnych.
  -  **Rozmiar systemu SAP**. Liczba punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
  -  **Dostępność systemu**. Wybierz pozycję **ha**.
  -  **Nazwa użytkownika administratora i hasło administratora**. Utwórz nowego użytkownika, którego można użyć do zalogowania się na komputerze.
  -  **Identyfikator podsieci**. Wprowadź identyfikator podsieci, która była używana podczas wdrażania szablonu ASCS/SCS, lub identyfikator podsieci, która została utworzona w ramach wdrożenia szablonu ASCS/SCS.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Usługa Azure Virtual Network
W naszym przykładzie przestrzeń adresowa sieci wirtualnej platformy Azure to 10.0.0.0/16. Istnieje jedna podsieć o nazwie **Subnet**z zakresem adresów 10.0.0.0/24. Wszystkie maszyny wirtualne i wewnętrzne moduły równoważenia obciążenia są wdrażane w tej sieci wirtualnej.

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian w ustawieniach sieci w systemie operacyjnym gościa. Obejmuje to adresy IP, serwery DNS i podsieć. Skonfiguruj wszystkie ustawienia sieci na platformie Azure. Usługa Dynamic Host Configuration Protocol (DHCP) propaguje Twoje ustawienia.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Adresy IP DNS

Aby ustawić wymagane adresy IP DNS, wykonaj następujące czynności.

1. W Azure Portal w bloku **serwery DNS** upewnij się, że opcja **serwery DNS** sieci wirtualnej jest ustawiona na wartość **niestandardowy DNS**.
2. Wybierz swoje ustawienia w zależności od typu sieci. Więcej informacji można znaleźć w następujących zasobach:
   * Dodaj adresy IP lokalnych serwerów DNS.  
   Lokalne serwery DNS można rozłożyć na maszyny wirtualne, które są uruchomione na platformie Azure. W tym scenariuszu można dodać adresy IP maszyn wirtualnych platformy Azure, na których jest uruchomiona usługa DNS.
   * W przypadku wdrożeń maszyn wirtualnych izolowanych na platformie Azure: Wdróż dodatkową maszynę wirtualną w tym samym wystąpieniu Virtual Network, które służy jako serwer DNS. Dodaj adresy IP maszyn wirtualnych platformy Azure, które zostały skonfigurowane do uruchamiania usługi DNS.

   ![Ilustracja 12. Konfigurowanie serwerów DNS dla Virtual Network platformy Azure][sap-ha-guide-figure-3001]

   _**Rysunek 12.** Konfigurowanie serwerów DNS dla Virtual Network platformy Azure_

   > [!NOTE]
   > W przypadku zmiany adresów IP serwerów DNS należy ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować zmianę i propagować nowe serwery DNS.
   >
   >

W naszym przykładzie usługa DNS jest zainstalowana i skonfigurowana na tych maszynach wirtualnych z systemem Windows:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Nazwa karty sieciowej | Statyczny adres IP |
| --- | --- | --- | --- |
| Pierwszy serwer DNS |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Drugi serwer DNS |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nazwy hostów i statyczne adresy IP dla wystąpienia klastrowanego SAP ASCS/SCS i wystąpienia klastrowanego DBMS

W przypadku wdrożenia lokalnego wymagane są następujące zastrzeżone nazwy hostów i adresy IP:

| Rola nazwy hosta wirtualnego | Nazwa hosta wirtualnego | Wirtualny statyczny adres IP |
| --- | --- | --- |
| Nazwa hosta wirtualnego SAP ASCS/SCS pierwszego klastra (do zarządzania klastrami) |PR1-ASCS-Vir |10.0.0.42 |
| Nazwa hosta wirtualnego wystąpienia SAP ASCS/SCS |PR1 — ASCS — SAP |10.0.0.43 |
| Nazwa hosta wirtualnego SAP DBMS drugiego klastra (Zarządzanie klastrem) |PR1-DBMS-Vir |10.0.0.32 |

Podczas tworzenia klastra utwórz nazwy hostów wirtualnych **PR1-ASCS-Vir** i **PR1-DBMS-Vir** oraz skojarzone adresy IP zarządzające klastrem. Aby uzyskać informacje o tym, jak to zrobić, zobacz [zbieranie węzłów klastra w konfiguracji klastra][sap-ha-guide-8.12.1].

Można ręcznie utworzyć pozostałe dwie nazwy hostów wirtualnych, **PR1-ASCS-SAP** i **PR1-DBMS-SAP**oraz skojarzone adresy IP na serwerze DNS. W klastrowanym wystąpieniu SAP ASCS/SCS i klastrowanym wystąpieniu systemu DBMS są używane te zasoby. Aby uzyskać informacje o tym, jak to zrobić, zobacz [Tworzenie nazwy wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Ustaw statyczne adresy IP dla maszyn wirtualnych SAP
Po wdrożeniu maszyn wirtualnych do użycia w klastrze należy ustawić statyczne adresy IP dla wszystkich maszyn wirtualnych. Zrób to w konfiguracji Virtual Network platformy Azure, a nie w systemie operacyjnym gościa.

1. W Azure Portal wybierz pozycję **Grupa zasobów**  >  ustawienia**karta sieciowa**  >  **Settings**  >  **adres IP**.
2. W bloku **adresy IP** w obszarze **przypisanie**wybierz pozycję **statyczny**. W polu **adres IP** wprowadź adres IP, którego chcesz użyć.

   > [!NOTE]
   > Jeśli zmienisz adres IP karty sieciowej, musisz ponownie uruchomić maszyny wirtualne platformy Azure, aby zastosować tę zmianę.  
   >
   >

   ![Ilustracja 13. Ustawianie statycznych adresów IP dla karty sieciowej każdej maszyny wirtualnej][sap-ha-guide-figure-3002]

   _**Rysunek 13:** Ustaw statyczne adresy IP dla karty sieciowej każdej maszyny wirtualnej_

   Powtórz ten krok dla wszystkich interfejsów sieciowych, czyli dla wszystkich maszyn wirtualnych, w tym maszyn wirtualnych, które mają być używane dla usługi Active Directory/DNS.

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

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Ustawianie statycznego adresu IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon Azure Resource Manager SAP służy do tworzenia wewnętrznego modułu równoważenia obciążenia platformy Azure, który jest używany przez klaster wystąpienia SAP ASCS/SCS i klaster DBMS.

> [!IMPORTANT]
> Adres IP nazwy hosta wirtualnego SAP ASCS/SCS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia SAP ASCS/SCS: **PR1-lb-ASCS**.
> Adres IP nazwy wirtualnej systemu DBMS jest taki sam jak adres IP wewnętrznego modułu równoważenia obciążenia systemu DBMS: **PR1-lb-DBMS**.
>
>

Aby ustawić statyczny adres IP dla wewnętrznego modułu równoważenia obciążenia platformy Azure:

1. Początkowe wdrożenie ustawia adres IP wewnętrznego modułu równoważenia obciążenia na **dynamiczny**. W Azure Portal w bloku **adresy IP** w obszarze **przypisanie**wybierz pozycję **statyczny**.
2. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **PR1-lb-ASCS** na adres IP nazwy hosta wirtualnego wystąpienia SAP ASCS/SCS.
3. Ustaw adres IP wewnętrznego modułu równoważenia obciążenia **PR1-lb-DBMS** na adres IP nazwy hosta wirtualnego systemu DBMS.

   ![Ilustracja 14. Ustawianie statycznych adresów IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Ilustracja 14.** Ustaw statyczne adresy IP dla wewnętrznego modułu równoważenia obciążenia dla wystąpienia SAP ASCS/SCS_

W naszym przykładzie mamy dwa wewnętrzne moduły równoważenia obciążenia platformy Azure, które mają następujące statyczne adresy IP:

| Rola wewnętrznego modułu równoważenia obciążenia platformy Azure | Nazwa wewnętrznego modułu równoważenia obciążenia platformy Azure | Statyczny adres IP |
| --- | --- | --- |
| Wewnętrzny moduł równoważenia obciążenia wystąpienia oprogramowania SAP ASCS/SCS |PR1-lb-ASCS |10.0.0.43 |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS |PR1-lb-DBMS |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Szablon Azure Resource Manager SAP tworzy wymagane porty:
* Wystąpienie ABAP ASCS z domyślnym wystąpieniem numer **00**
* Wystąpienie środowiska Java SCS z domyślnym numerem wystąpienia **01**

W przypadku instalowania wystąpienia oprogramowania SAP ASCS/SCS należy użyć domyślnego wystąpienia o numerze **00** dla wystąpienia ASCS ABAP i domyślnego numeru wystąpienia **01** dla wystąpienia języka Java SCS.

Następnie utwórz wymagane punkty końcowe wewnętrznego równoważenia obciążenia dla portów SAP NetWeaver.

Aby utworzyć wymagane punkty końcowe wewnętrznego równoważenia obciążenia, należy najpierw utworzyć te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver ABAP ASCS:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretne porty dla (wystąpienie ASCS z numerem wystąpienia 00) (wykres WYWOŁUJĄCYCH z 10) |
| --- | --- | --- |
| Serwer/ *lbrule3200* |32<*numerwystąpienia*> |3200 |
| Serwer komunikatów ABAP/ *lbrule3600* |36<*numerwystąpienia*> |3600 |
| Wewnętrzny komunikat ABAP/ *lbrule3900* |39<*numerwystąpienia*> |3900 |
| Serwer komunikatów HTTP/ *Lbrule8100* |81<*numerwystąpienia*> |8100 |
| Usługa SAP — uruchamianie usługi ASCS HTTP/ *Lbrule50013* |5<*numerwystąpienia*>13 |50013 |
| Usługa SAP Start ASCS HTTPS/ *Lbrule50014* |5<*numerwystąpienia*>14 |50014 |
| Replikacja/ *Lbrule50016* w kolejce |5<*numerwystąpienia*>16 |50016 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51013* |5<*numerwystąpienia*>13 |51013 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51014* |5<*numerwystąpienia*>14 |51014 |
| *LBRULE5985* zasobów systemu Windows | |5985 |
| *Lbrule445* udziału plików | |445 |

_**Tabela 1:** Numery portów wystąpień programu SAP NetWeaver ABAP ASCS_

Następnie utwórz te punkty końcowe równoważenia obciążenia dla portów SAP NetWeaver Java SCS:

| Nazwa reguły równoważenia obciążenia/usługi | Domyślne numery portów | Konkretne porty dla (wystąpienie SCS z numerem wystąpienia 01) (wykres WYWOŁUJĄCYCH z 11) |
| --- | --- | --- |
| Serwer/ *lbrule3201* |32<*numerwystąpienia*> |3201 |
| Serwer bramy/ *lbrule3301* |33<*numerwystąpienia*> |3301 |
| Serwer wiadomości Java/ *lbrule3900* |39<*numerwystąpienia*> |3901 |
| Serwer komunikatów HTTP/ *Lbrule8101* |81<*numerwystąpienia*> |8101 |
| Usługa SAP — uruchamianie usługi SCS HTTP/ *Lbrule50113* |5<*numerwystąpienia*>13 |50113 |
| Usługa SAP Start SCS HTTPS/ *Lbrule50114* |5<*numerwystąpienia*>14 |50114 |
| Replikacja/ *Lbrule50116* w kolejce |5<*numerwystąpienia*>16 |50116 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51113* |5<*numerwystąpienia*>13 |51113 |
| Usługa SAP Start wykres WYWOŁUJĄCYCH HTTP *Lbrule51114* |5<*numerwystąpienia*>14 |51114 |
| *LBRULE5985* zasobów systemu Windows | |5985 |
| *Lbrule445* udziału plików | |445 |

_**Tabela 2:** Numery portów wystąpień programu SAP NetWeaver Java SCS_

![Ilustracja 15. domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3004]

_**Ilustracja 15.** Domyślne reguły równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

Ustaw adres IP modułu równoważenia obciążenia **PR1-lb-DBMS** na adres IP nazwy hosta wirtualnego systemu DBMS.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure

Jeśli chcesz użyć różnych numerów dla wystąpień SAP ASCS lub SCS, należy zmienić nazwy i wartości ich portów z wartości domyślnych.

1. W Azure Portal wybierz pozycję ** < *SID*>-lb-ASCS**  >  **reguły równoważenia obciążenia**usługi równoważenia obciążenia.
2. Dla wszystkich reguł równoważenia obciążenia należących do wystąpienia SAP ASCS lub SCS Zmień następujące wartości:

   * Nazwa
   * Port
   * Port zaplecza

   Na przykład jeśli chcesz zmienić domyślną liczbę wystąpień ASCS z 00 na 31, musisz wprowadzić zmiany dla wszystkich portów wymienionych w tabeli 1.

   Oto przykład aktualizacji *lbrule3200*portu.

   ![Rysunek 16. zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure][sap-ha-guide-figure-3005]

   _**Rysunek 16.** Zmiana reguł domyślnego równoważenia obciążenia ASCS/SCS dla wewnętrznego modułu równoważenia obciążenia platformy Azure_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Dodawanie maszyn wirtualnych z systemem Windows do domeny

Po przypisaniu statycznego adresu IP do maszyn wirtualnych należy dodać maszyny wirtualne do domeny.

![Ilustracja 17. Dodawanie maszyny wirtualnej do domeny][sap-ha-guide-figure-3006]

_**Ilustracja 17.** Dodawanie maszyny wirtualnej do domeny_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Dodaj wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS

Azure Load Balancer ma wewnętrzny moduł równoważenia obciążenia, który zamyka połączenia, gdy połączenia są bezczynne przez określony czas (limit czasu bezczynności). Procesy robocze SAP w wystąpieniach okna dialogowego otwierają połączenia z procesem dodawania do kolejki SAP zaraz po wysłaniu pierwszego żądania kolejki/usuwania kolejki. Te połączenia zwykle pozostają ustanowione do momentu ponownego uruchomienia procesu pracy lub kolejki. Jeśli jednak połączenie jest w stanie bezczynności przez określony czas, wewnętrzny moduł równoważenia obciążenia platformy Azure zamknie połączenia. Nie jest to problem, ponieważ proces roboczy SAP ponownie nawiązuje połączenie z procesem dodawania do kolejki, jeśli już nie istnieje. Te działania są udokumentowane w śladach dla deweloperów procesów SAP, ale w tych śladach tworzą dużą ilość dodatkowej zawartości. Dobrym pomysłem jest zmiana protokołu TCP/IP `KeepAliveTime` i `KeepAliveInterval` obu węzłów klastra. Połącz te zmiany w parametrach TCP/IP z parametrami profilu SAP opisanymi w dalszej części artykułu.

Aby dodać wpisy rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS, należy najpierw dodać te wpisy rejestru systemu Windows na obu węzłach klastra systemu Windows dla oprogramowania SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveTime` |
| Typ zmiennej |REG_DWORD (liczba dziesiętna) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabela 3:** Zmiana pierwszego parametru TCP/IP_

Następnie Dodaj te wpisy rejestru systemu Windows na obu węzłach klastra systemu Windows dla oprogramowania SAP ASCS/SCS:

| Ścieżka | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nazwa zmiennej |`KeepAliveInterval` |
| Typ zmiennej |REG_DWORD (liczba dziesiętna) |
| Wartość |120000 |
| Link do dokumentacji |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabela 4:** Zmień drugi parametr TCP/IP_

**Aby zastosować zmiany, uruchom ponownie oba węzły klastra**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Konfigurowanie klastra usługi Windows Server Failover Clustering dla wystąpienia SAP ASCS/SCS

Konfigurowanie klastra usługi Windows Server Failover Clustering dla wystąpienia SAP ASCS/SCS obejmuje następujące zadania:

- Zbieranie węzłów klastra w konfiguracji klastra
- Konfigurowanie monitora udziału plików klastra

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Zbieranie węzłów klastra w konfiguracji klastra

1. W Kreatorze dodawania ról i funkcji Dodaj klaster trybu failover do obu węzłów klastra.
2. Skonfiguruj klaster trybu failover za pomocą Menedżer klastra trybu failover. W Menedżer klastra trybu failover wybierz pozycję **Utwórz klaster**, a następnie Dodaj tylko nazwę pierwszego klastra, węzeł A. Nie należy jeszcze dodawać drugiego węzła; drugi węzeł zostanie dodany w późniejszym kroku.

   ![Ilustracja 18. Dodawanie serwera lub nazwy maszyny wirtualnej pierwszego węzła klastra][sap-ha-guide-figure-3007]

   _**Ilustracja 18.** Dodaj nazwę serwera lub maszyny wirtualnej pierwszego węzła klastra_

3. Wprowadź nazwę sieci (nazwę hosta wirtualnego) klastra.

   ![Ilustracja 19. wprowadzanie nazwy klastra][sap-ha-guide-figure-3008]

   _**Rysunek 19.** Wprowadź nazwę klastra_

4. Po utworzeniu klastra uruchom test weryfikacji klastra.

   ![Ilustracja 20. Uruchamianie sprawdzania poprawności klastra][sap-ha-guide-figure-3009]

   _**Ilustracja 20.** Uruchom sprawdzanie poprawności klastra_

   Można zignorować ostrzeżenia dotyczące dysków w tym momencie w procesie. W późniejszym czasie dodasz monitor udostępniania plików i dyski udostępnione oprogramowanie SIOS. Na tym etapie nie trzeba martwić się o posiadanie kworum.

   ![Ilustracja 21. nie znaleziono dysku kworum][sap-ha-guide-figure-3010]

   _**Rysunek 21.** Nie znaleziono dysku kworum_

   ![Ilustracja 22: zasób podstawowego klastra wymaga nowego adresu IP][sap-ha-guide-figure-3011]

   _**Ilustracja 22:** Podstawowy zasób klastra wymaga nowego adresu IP_

5. Zmień adres IP podstawowej usługi klastra. Nie można uruchomić klastra, dopóki nie zmienisz adresu IP podstawowej usługi klastra, ponieważ adres IP serwera wskazuje jeden z węzłów maszyny wirtualnej. Zrób to na stronie **Właściwości** zasobu adresu IP podstawowej usługi klastra.

   Na przykład musimy przypisać adres IP (w naszym przykładzie **10.0.0.42**) dla nazwy hosta wirtualnego klastra **PR1-ASCS-Vir**.

   ![Ilustracja 23. w oknie dialogowym właściwości Zmień adres IP][sap-ha-guide-figure-3012]

   _**Rysunek 23.** W oknie dialogowym **Właściwości** Zmień adres IP_

   ![Rysunek 24. przypisanie adresu IP zastrzeżonego dla klastra][sap-ha-guide-figure-3013]

   _**Rysunek 24.** Przypisywanie adresu IP zastrzeżonego dla klastra_

6. Wprowadź nazwę wirtualnego hosta klastra w trybie online.

   ![Ilustracja 25. Usługa klaster podstawowa jest uruchomiona i ma prawidłowy adres IP][sap-ha-guide-figure-3014]

   _**Ilustracja 25:** Usługa podstawowe klastra jest uruchomiona i ma prawidłowy adres IP_

7. Dodaj drugi węzeł klastra.

   Teraz, gdy podstawowa usługa klastra jest uruchomiona, można dodać drugi węzeł klastra.

   ![Ilustracja 26. Dodawanie drugiego węzła klastra][sap-ha-guide-figure-3015]

   _**Rysunek 26:** Dodaj drugi węzeł klastra_

8. Wprowadź nazwę drugiego hosta węzła klastra.

   ![Ilustracja 27. wprowadź drugą nazwę hosta węzła klastra][sap-ha-guide-figure-3016]

   _**Rysunek 27:** Wprowadź drugą nazwę hosta węzła klastra_

   > [!IMPORTANT]
   > Upewnij się, że pole wyboru **Dodaj wszystkie odpowiednie magazyny do klastra** **nie** jest zaznaczone.  
   >
   >

   ![Ilustracja 28. nie zaznaczaj pola wyboru][sap-ha-guide-figure-3017]

   _**Ilustracja 28.** **Nie** zaznaczaj pola wyboru_

   Można zignorować ostrzeżenia dotyczące kworum i dysków. Należy ustawić kworum i udostępnić dysk później, zgodnie z opisem w temacie [Installing oprogramowanie SIOS DataKeeper Cluster Edition for SAP ASCS/SCS Cluster Share Disk][sap-ha-guide-8.12.3].

   ![Ilustracja 29. ignorowanie ostrzeżeń dotyczących kworum dysku][sap-ha-guide-figure-3018]

   _**Rysunek 29:** Ignoruj ostrzeżenia dotyczące kworum dysku_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurowanie monitora udziału plików klastra

Konfigurowanie monitora udziału plików klastra obejmuje następujące zadania:

- Tworzenie udziału plików
- Ustawianie kworum monitora udziału plików w Menedżer klastra trybu failover

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Tworzenie udziału plików

1. Wybierz monitor udziału plików zamiast dysku kworum. OPROGRAMOWANIE SIOS DataKeeper obsługuje tę opcję.

   W przykładach w tym artykule monitor udziału plików znajduje się na serwerze Active Directory/DNS działającym na platformie Azure. Monitor udziału plików ma nazwę **domcontr-0**. Ze względu na to, że skonfigurowano połączenie sieci VPN z platformą Azure (za pośrednictwem sieci VPN typu lokacja-lokacja lub Azure ExpressRoute), usługa Active Directory/DNS jest lokalna i nie jest odpowiednia do uruchamiania monitora udostępniania plików.

   > [!NOTE]
   > Jeśli usługa Active Directory/DNS działa tylko lokalnie, nie należy konfigurować monitora udostępniania plików w systemie operacyjnym Windows Active Directory/DNS działającym lokalnie. Opóźnienie sieci między węzłami klastra działającymi na platformie Azure i lokalnymi Active Directory/DNS może być zbyt duże i powodować problemy z łącznością. Należy pamiętać, aby skonfigurować Monitor udostępniania plików na maszynie wirtualnej platformy Azure, która działa blisko węzła klastra.  
   >
   >

   Dysk kworum wymaga co najmniej 1 024 MB wolnego miejsca. Zalecamy 2 048 MB wolnego miejsca na dysku kworum.

2. Dodaj obiekt nazwy klastra.

   ![Ilustracja 30. Przypisywanie uprawnień do udziału dla obiektu nazwy klastra][sap-ha-guide-figure-3019]

   _**Rysunek 30:** Przypisz uprawnienia do udziału dla obiektu nazwy klastra_

   Upewnij się, że uprawnienia obejmują urząd do zmiany danych w udziale dla obiektu nazwy klastra (w naszym przykładzie **PR1-ASCS-Vir $**).

3. Aby dodać obiekt nazwy klastra do listy, wybierz pozycję **Dodaj**. Zmień filtr, aby wyszukiwać obiekty komputerów, a także te, które przedstawiono na rysunku 31.

   ![Ilustracja 31. zmiana typów obiektów w celu uwzględnienia komputerów][sap-ha-guide-figure-3020]

   _**Rysunek 31.** Zmień typy obiektów w celu uwzględnienia komputerów_

   ![Rysunek 32: zaznacz pole wyboru komputery][sap-ha-guide-figure-3021]

   _**Rysunek 32:** Zaznacz pole wyboru **komputery**_

4. Wprowadź obiekt nazwy klastra, jak pokazano na rysunku 31. Ponieważ rekord został już utworzony, można zmienić uprawnienia, jak pokazano na rysunku 30.

5. Wybierz kartę **zabezpieczenia** udziału, a następnie ustaw bardziej szczegółowe uprawnienia dla obiektu nazwa klastra.

   ![Ilustracja 33: ustawianie atrybutów zabezpieczeń obiektu nazwy klastra w kworum udziału plików][sap-ha-guide-figure-3022]

   _**Rysunek 33:** Ustawianie atrybutów zabezpieczeń obiektu nazwa klastra na kworum udziałów plików_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Ustaw kworum monitora udziału plików w Menedżer klastra trybu failover

1. Otwórz Kreatora konfiguracji ustawienia kworum.

   ![Ilustracja 34: Uruchamianie Kreatora konfiguracji ustawień kworum klastra][sap-ha-guide-figure-3023]

   _**Rysunek 34:** Uruchom Kreatora konfigurowania ustawień kworum klastra_

2. Na stronie **wybierz konfigurację kworum** wybierz pozycję **Wybierz monitor kworum**.

   ![Rysunek 35: konfiguracje kworum, z których można wybierać][sap-ha-guide-figure-3024]

   _**Rysunek 35:** Konfiguracje kworum, z których można wybierać_

3. Na stronie **Wybieranie monitora kworum** wybierz opcję **Konfiguruj Monitor udziału plików**.

   ![Rysunek 36: wybierz Monitor udziału plików][sap-ha-guide-figure-3025]

   _**Rysunek 36:** Wybierz monitor udziału plików_

4. Wprowadź ścieżkę UNC do udziału plików (w naszym przykładzie \\ domcontr-0\FSW). Aby wyświetlić listę zmian, które można wprowadzić, wybierz pozycję **dalej**.

   ![Ilustracja 37: definiowanie lokalizacji udziału plików dla udziału monitora][sap-ha-guide-figure-3026]

   _**Rysunek 37:** Zdefiniuj lokalizację udziału plików dla udziału monitora_

5. Wybierz żądane zmiany, a następnie wybierz przycisk **dalej**. Należy pomyślnie ponownie skonfigurować konfigurację klastra, jak pokazano na rysunku 38.  

   ![Rysunek 38: potwierdzenie, że klaster został ponownie skonfigurowany][sap-ha-guide-figure-3027]

   _**Rysunek 38:** Potwierdzenie, że klaster został ponownie skonfigurowany_

Po pomyślnym zainstalowaniu klastra trybu failover systemu Windows należy wprowadzić zmiany w niektórych progach w celu dostosowania wykrywania trybu failover do warunków na platformie Azure. Parametry, które mają zostać zmienione, są udokumentowane w tym blogu: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Przy założeniu, że dwie maszyny wirtualne, które tworzą konfigurację klastra systemu Windows dla ASCS/SCS znajdują się w tej samej podsieci, należy zmienić następujące parametry na następujące wartości:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Te ustawienia zostały przetestowane przez klientów i zapewniono dobry kompromis, który jest wystarczająco odporny na jedną stronę. Z drugiej strony te ustawienia zapewniają szybką pracę w trybie failover w rzeczywistych warunkach błędu w przypadku awarii oprogramowania SAP lub węzła/maszyny wirtualnej. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Zainstaluj oprogramowanie SIOS DataKeeper Cluster Edition dla dysku udziału klastra SAP ASCS/SCS

Masz już działającą konfigurację klastra trybu failover systemu Windows Server na platformie Azure. Jednak w celu zainstalowania wystąpienia oprogramowania SAP ASCS/SCS potrzebny jest zasób dysku udostępnionego. Nie można utworzyć udostępnionych zasobów dyskowych na platformie Azure. OPROGRAMOWANIE SIOS DataKeeper Cluster Edition to rozwiązanie innej firmy, którego można użyć do tworzenia udostępnionych zasobów dyskowych.

Instalowanie oprogramowanie SIOS DataKeeper Cluster w przypadku dysku udziału klastra SAP ASCS/SCS obejmuje następujące zadania:

- Dodawanie .NET Framework 3,5
- Instalowanie składnika oprogramowanie SIOS DataKeeper
- Konfigurowanie oprogramowanie SIOS

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Dodaj .NET Framework 3,5
Microsoft .NET Framework 3,5 nie jest automatycznie aktywowany ani instalowany w systemie Windows Server 2012 R2. Ponieważ oprogramowanie SIOS DataKeeper wymaga, aby .NET Framework znajdować się we wszystkich węzłach, w których zainstalowano program DataKeeper, należy zainstalować .NET Framework 3,5 w systemie operacyjnym gościa wszystkich maszyn wirtualnych w klastrze.

Istnieją dwa sposoby dodawania .NET Framework 3,5:

- Użyj Kreatora dodawania ról i funkcji w systemie Windows, jak pokazano na rysunku 39.

  ![Ilustracja 39: Instalowanie .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3028]

  _**Rysunek 39:** Zainstaluj .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji_

  ![Rysunek 40: pasek postępu instalacji podczas instalowania .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji][sap-ha-guide-figure-3029]

  _**Rysunek 40:** Pasek postępu instalacji podczas instalowania .NET Framework 3,5 przy użyciu Kreatora dodawania ról i funkcji_

- Użyj narzędzia wiersza polecenia dism.exe. W przypadku tego typu instalacji należy uzyskać dostęp do katalogu SxS na nośniku instalacyjnym systemu Windows. W wierszu polecenia z podwyższonym poziomem uprawnień wpisz:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Zainstaluj program oprogramowanie SIOS DataKeeper

Zainstaluj program oprogramowanie SIOS DataKeeper Cluster Edition na każdym węźle w klastrze. Aby utworzyć wirtualny magazyn udostępniony z oprogramowanie SIOS DataKeeper, Utwórz zsynchronizowane dublowanie i Symuluj magazyn udostępniony klastra.

Przed zainstalowaniem oprogramowania oprogramowanie SIOS Utwórz **DataKeeperSvc**użytkownika domeny.

> [!NOTE]
> Dodaj użytkownika **DataKeeperSvc** do lokalnej grupy **administratorów** na obu węzłach klastra.
>
>

Aby zainstalować program oprogramowanie SIOS DataKeeper:

1. Zainstaluj oprogramowanie oprogramowanie SIOS na obu węzłach klastra.

   ![Instalator oprogramowanie SIOS][sap-ha-guide-figure-3030]

   ![Rysunek 41: pierwsza strona instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Rysunek 41:** Pierwsza strona instalacji elementu oprogramowanie SIOS DataKeeper_

2. W oknie dialogowym pokazanym na rysunku 42 wybierz pozycję **tak**.

   ![Ilustracja 42: usługa DataKeeper informuje o tym, że usługi zostaną wyłączone][sap-ha-guide-figure-3032]

   _**Rysunek 42:** Element informuje, że usługa zostanie wyłączona_

3. W oknie dialogowym przedstawionym na rysunku 43 zalecamy wybranie **konta domeny lub serwera**.

   ![Rysunek 43: wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Rysunek 43:** Wybór użytkownika dla elementu oprogramowanie SIOS DataKeeper_

4. Wprowadź nazwę użytkownika i hasło konta domeny, które zostały utworzone dla oprogramowanie SIOS DataKeeper.

   ![Rysunek 44: Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Rysunek 44:** Wprowadź nazwę użytkownika domeny i hasło dla instalacji oprogramowanie SIOS DataKeeper_

5. Zainstaluj klucz licencji dla wystąpienia oprogramowanie SIOS DataKeeper, jak pokazano na rysunku 45.

   ![Rysunek 45: Wprowadź klucz licencji oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Rysunek 45:** Wprowadź klucz licencji oprogramowanie SIOS DataKeeper_

6. Po wyświetleniu monitu uruchom ponownie maszynę wirtualną.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Skonfiguruj oprogramowanie SIOScy

Po zainstalowaniu usługi oprogramowanie SIOS DataKeeper na obu węzłach należy uruchomić konfigurację. Celem konfiguracji jest posiadanie synchronicznej replikacji danych między dodatkowymi dyskami podłączonymi do każdej maszyny wirtualnej.

1. Uruchom narzędzie do zarządzania i konfiguracji programu DataKeeper, a następnie wybierz pozycję **Połącz serwer**. (Na rysunku 46 ta opcja jest zakreślona czerwonym kolorem).

   ![Ilustracja 46: oprogramowanie SIOS narzędzia do zarządzania i konfiguracji][sap-ha-guide-figure-3036]

   _**Rysunek 46:** Narzędzie do zarządzania i konfiguracji oprogramowanie SIOSer_

2. Wprowadź nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł.

   ![Rysunek 47: Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł][sap-ha-guide-figure-3037]

   _**Rysunek 47:** Wstaw nazwę lub adres TCP/IP pierwszego węzła, z którym narzędzie do zarządzania i konfiguracji ma nawiązać połączenie, i w drugim kroku drugi węzeł_

3. Utwórz zadanie replikacji między dwoma węzłami.

   ![Rysunek 48: Tworzenie zadania replikacji][sap-ha-guide-figure-3038]

   _**Rysunek 48:** Tworzenie zadania replikacji_

   Kreator przeprowadzi Cię przez proces tworzenia zadania replikacji.
4. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła źródłowego.

   ![Rysunek 49: Definiowanie nazwy zadania replikacji][sap-ha-guide-figure-3039]

   _**Rysunek 49:** Zdefiniuj nazwę zadania replikacji_

   ![Rysunek 50: Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym][sap-ha-guide-figure-3040]

   _**Rysunek 50:** Zdefiniuj dane podstawowe dla węzła, które powinny być bieżącym węzłem źródłowym_

5. Zdefiniuj nazwę, adres TCP/IP i wolumin dysku węzła docelowego.

   ![Rysunek 51: Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem docelowym][sap-ha-guide-figure-3041]

   _**Rysunek 51:** Zdefiniuj dane podstawowe dla węzła, który powinien być bieżącym węzłem docelowym_

6. Zdefiniuj algorytmy kompresji. W naszym przykładzie zalecamy kompresję strumienia replikacji. W szczególności w sytuacjach ponownych synchronizacji kompresja strumienia replikacji znacznie skraca czas ponownej synchronizacji. Należy pamiętać, że kompresja korzysta z zasobów procesora i pamięci RAM maszyny wirtualnej. Szybkość kompresji zwiększa się, więc ilość używanych zasobów procesora CPU. Możesz również dostosować to ustawienie później.

7. Innym ustawieniem należy sprawdzić, czy replikacja odbywa się asynchronicznie czy synchronicznie. *W przypadku ochrony konfiguracji oprogramowania SAP ASCS/SCS należy użyć replikacji synchronicznej*.  

   ![Rysunek 52: Definiowanie szczegółów replikacji][sap-ha-guide-figure-3042]

   _**Rysunek 52:** Zdefiniuj Szczegóły replikacji_

8. Zdefiniuj, czy wolumin replikowany przez zadanie replikacji ma być reprezentowany przez konfigurację klastra usługi Windows Server Failover Clustering jako dysk udostępniony. W przypadku konfiguracji SAP ASCS/SCS wybierz opcję **tak** , aby klaster systemu Windows widział wolumin replikowany jako dysk udostępniony, który może być używany jako wolumin klastra.

   ![Rysunek 53: wybierz pozycję tak, aby ustawić wolumin replikowany jako wolumin klastra][sap-ha-guide-figure-3043]

   _**Rysunek 53:** Wybierz pozycję **tak** , aby ustawić wolumin replikowany jako wolumin klastra_

   Po utworzeniu woluminu narzędzie zarządzania i konfiguracji programu DataManagement wskazuje, że zadanie replikacji jest aktywne.

   ![Rysunek 54: funkcja dublowania synchronicznego dla dysku udziału ASCS/SCS jest aktywna][sap-ha-guide-figure-3044]

   _**Rysunek 54:** Funkcja dublowania synchronicznego dla dysku udziału SAP ASCS/SCS jest aktywna_

   Menedżer klastra trybu failover teraz przedstawia dysk jako dysk programu do przechowywania danych, jak pokazano na rysunku 55.

   ![Rysunek 55: Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik][sap-ha-guide-figure-3045]

   _**Rysunek 55:** Menedżer klastra trybu failover pokazuje dysk, który jest replikowany przez plik_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Zainstaluj system SAP NetWeaver

Konfiguracja systemu DBMS nie zostanie opisywana, ponieważ konfiguracje różnią się w zależności od używanej przez Ciebie system DBMS. Jednak założono, że problemy z wysoką dostępnością w systemie DBMS są rozwiązywane z tymi funkcjami, które są obsługiwane przez różnych dostawców systemów DBMS przez platformę Azure. Na przykład zawsze włączone lub dublowania baz danych dla SQL Server i Oracle Data Guard dla baz danych Oracle. W scenariuszu używanym w tym artykule nie Dodaliśmy więcej ochrony do systemu DBMS.

Nie ma specjalnych zagadnień, w przypadku których różne usługi DBMS współpracują z tym rodzajem klastrowanej konfiguracji oprogramowania SAP ASCS/SCS na platformie Azure.

> [!NOTE]
> Procedury instalacji systemów SAP NetWeaver ABAP, systemów Java i ABAP + w systemach Java są prawie identyczne. Najbardziej znaczącą różnicą jest to, że system SAP ABAP ma jedno wystąpienie ASCS. System SAP Java ma jedno wystąpienie SCS. System SAP ABAP + Java ma jedno wystąpienie ASCS i jedno wystąpienie SCS uruchomione w tej samej grupie klastrów trybu failover firmy Microsoft. Wszystkie różnice instalacji dla każdego stosu instalacji oprogramowania SAP NetWeaver są jawnie wymienione. Można założyć, że wszystkie inne części są takie same.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Instalowanie oprogramowania SAP z wystąpieniem ASCS o wysokiej dostępności

> [!IMPORTANT]
> Upewnij się, że plik stronicowania nie jest umieszczony na woluminach dublowanych danych. Usługa DataKeeper nie obsługuje dublowanych woluminów. Możesz pozostawić plik stronicowania na tymczasowym dysku D maszyny wirtualnej platformy Azure, co jest ustawieniem domyślnym. Jeśli jeszcze tego nie zrobiono, Przenieś plik strony systemu Windows na dysk D: maszyny wirtualnej platformy Azure.
>
>

Instalowanie oprogramowania SAP z wystąpieniem ASCS/SCS o wysokiej dostępności obejmuje następujące zadania:

- Tworzenie nazwy wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS
- Instalowanie pierwszego węzła klastra SAP
- Modyfikowanie profilu SAP wystąpienia ASCS/SCS
- Dodawanie portu sondy
- Otwieranie portu sondowania zapory systemu Windows

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Utwórz nazwę wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS

1. W Menedżerze DNS systemu Windows Utwórz wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS.

   > [!IMPORTANT]
   > Adres IP przypisany do nazwy hosta wirtualnego wystąpienia ASCS/SCS musi być taki sam jak adres IP przypisany do Azure Load Balancer (** < *SID*>-lb-ASCS**).  
   >
   >

   Adres IP nazwy hosta wirtualnej SAP ASCS/SCS (**PR1-ASCS-SAP**) jest taki sam jak adres IP Azure Load Balancer (**PR1-lb-ASCS**).

   ![Rysunek 56: Zdefiniuj wpis DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-3046]

   _**Rysunek 56:** Zdefiniuj wpis DNS dla nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP_

2. Aby zdefiniować adres IP przypisany do nazwy hosta wirtualnego, wybierz pozycję Domena **Menedżera DNS**  >  **Domain**.

   ![Rysunek 57: Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Rysunek 57:** Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalowanie pierwszego węzła klastra SAP

1. Wykonaj pierwszy węzeł klastra opcja w węźle klastra A. Na przykład na hoście **PR1-ASCS-0** .
2. Aby zachować domyślne porty dla wewnętrznego modułu równoważenia obciążenia platformy Azure, wybierz:

   * **ABAP system**: **ASCS** wystąpienie numer **00**
   * **System Java**: wystąpienie **SCS** numer **01**
   * **ABAP + system Java**: **ASCS** wystąpienie numer **00** i **SCS** wystąpienie numer **01**

   Aby użyć numeru wystąpienia innego niż 00 dla wystąpienia ABAP ASCS i 01 dla wystąpienia SCS Java, najpierw należy zmienić domyślne reguły równoważenia obciążenia platformy Azure, zgodnie z opisem w artykule [zmiana domyślnych reguł równoważenia][sap-ha-guide-8.9]obciążenia na platformie Azure dla wewnętrznego modułu równoważenia obciążenia.

Kolejne kilka zadań nie są opisane w standardowej dokumentacji instalacji SAP.

> [!NOTE]
> W dokumentacji instalacji SAP opisano, jak zainstalować pierwszy węzeł klastra ASCS/SCS.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Musisz dodać nowy parametr profilu. Parametr profil uniemożliwia zamknięcie połączeń między procesami roboczymi SAP a serwerem z kolejki, gdy są one w stanie bezczynności przez zbyt długi czas. Scenariusz problemu został opisany w temacie [Dodawanie wpisów rejestru na obu węzłach klastra wystąpienia SAP ASCS/SCS][sap-ha-guide-8.11]. W tej sekcji wprowadzono również dwie zmiany w przypadku niektórych podstawowych parametrów połączenia TCP/IP. W drugim kroku należy ustawić serwer kolejki, aby wysyłał `keep_alive` sygnał, aby nie osiągnęły progu bezczynności wewnętrznego modułu równoważenia obciążenia platformy Azure.

Aby zmodyfikować profil SAP wystąpienia ASCS/SCS:

1. Dodaj ten parametr profilu do profilu wystąpienia SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   W naszym przykładzie ścieżka:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Na przykład, do profilu wystąpienia SAP SCS i odpowiedniej ścieżki:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Aby zastosować zmiany, uruchom ponownie wystąpienie SAP ASCS/SCS.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Dodaj port sondy

Korzystając z funkcji sondowania wewnętrznego modułu równoważenia obciążenia, można sprawić, aby cała Konfiguracja klastra działała z Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia platformy Azure zwykle dystrybuuje przychodzące obciążenie równo między uczestniczącymi maszynami wirtualnymi. Nie będzie to jednak działało w niektórych konfiguracjach klastra, ponieważ aktywne jest tylko jedno wystąpienie. Drugie wystąpienie jest pasywne i nie może akceptować żadnego obciążenia. Funkcja sondowania pomaga, gdy wewnętrzny moduł równoważenia obciążenia platformy Azure przypisuje działania tylko do aktywnego wystąpienia. Korzystając z funkcji sondowania, wewnętrzny moduł równoważenia obciążenia może wykryć, które wystąpienia są aktywne, a następnie wskazać tylko wystąpienie z obciążeniem.

Aby dodać port sondy:

1. Sprawdź bieżące ustawienie **ProbePort** , uruchamiając następujące polecenie programu PowerShell. Wykonaj ją z poziomu jednej z maszyn wirtualnych w konfiguracji klastra.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Zdefiniuj port sondy. Domyślny numer portu sondy to **0**. W naszym przykładzie używany jest port sondy **62000**.

   ![Rysunek 58: Port sondy konfiguracji klastra domyślnie ma wartość 0][sap-ha-guide-figure-3048]

   _**Rysunek 58:** Domyślny port sondy konfiguracji klastra to 0_

   Numer portu jest zdefiniowany w szablonach Azure Resource Manager SAP. Numer portu można przypisać w programie PowerShell.

   Aby ustawić nową wartość ProbePort dla ** *identyfikatora SID* <protokołu SAP>** zasobu klastra IP, uruchom następujący skrypt programu PowerShell. Zaktualizuj zmienne programu PowerShell dla danego środowiska. Po uruchomieniu skryptu zostanie wyświetlony monit o ponowne uruchomienie grupy klastra SAP w celu aktywowania zmian.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Po przełączeniu roli klastra usługi **SAP <*SID* > ** do trybu online Sprawdź, czy **ProbePort** jest ustawiona na nową wartość.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Rysunek 59: sondowanie portu klastra po ustawieniu nowej wartości][sap-ha-guide-figure-3049]

   _**Rysunek 59:** Sondowanie portu klastra po ustawieniu nowej wartości_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Otwórz port sondowania zapory systemu Windows

Należy otworzyć port sondy zapory systemu Windows na obu węzłach klastra. Użyj poniższego skryptu, aby otworzyć port sondowania zapory systemu Windows. Zaktualizuj zmienne programu PowerShell dla danego środowiska.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** jest ustawiona na **62000**. Teraz możesz uzyskać dostęp do udziału plików ** \\ \ascsha-clsap\sapmnt** z innych hostów, takich jak z **ascsha-przetwarzający**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalowanie wystąpienia bazy danych

Aby zainstalować wystąpienie bazy danych, postępuj zgodnie z procesem opisanym w dokumentacji instalacji SAP.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Zainstaluj drugi węzeł klastra

Aby zainstalować drugi klaster, wykonaj kroki opisane w przewodniku instalacji SAP.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Zmień typ uruchomienia wystąpienia usługi SAP wykres WYWOŁUJĄCYCH systemu Windows

Zmień typ uruchomienia usługi SAP wykres WYWOŁUJĄCYCH systemu Windows na **Automatyczne (opóźnione uruchomienie)** na obu węzłach klastra.

![Rysunek 60: Zmień typ usługi dla wystąpienia SAP wykres WYWOŁUJĄCYCH na opóźniony automatyczny][sap-ha-guide-figure-3050]

_**Rysunek 60:** Zmień typ usługi dla wystąpienia SAP wykres WYWOŁUJĄCYCH na opóźniony automatyczny_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Zainstaluj podstawowy serwer aplikacji SAP

Zainstaluj wystąpienie serwera aplikacji podstawowej (PAS) <*Identyfikator SID*>-di-0 na maszynie wirtualnej, która została wyznaczyna do hostowania tego pas. Nie ma żadnych zależności dotyczących ustawień platformy Azure lub usługi DataKeeper.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Zainstaluj dodatkowy serwer aplikacji SAP

Zainstaluj dodatkowy serwer aplikacji SAP (AAS) na wszystkich maszynach wirtualnych, które zostały wskazane do hostowania wystąpienia serwera aplikacji SAP. Na przykład na <*identyfikator sid*>-di-1 w celu <*identyfikatora SID*>-di- &lt; n &gt; .

> [!NOTE]
> Spowoduje to zakończenie instalacji systemu SAP NetWeaver o wysokiej dostępności. Następnie kontynuuj Testowanie pracy w trybie failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testowanie trybu failover wystąpienia SAP ASCS/SCS i replikacji oprogramowanie SIOS
Można łatwo testować i monitorować wystąpienie usługi SAP ASCS/SCS w trybie failover oraz replikację dysków oprogramowanie SIOS za pomocą Menedżer klastra trybu failover oraz narzędzia do zarządzania i konfigurowania danych programu oprogramowanie SIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Wystąpienie SAP ASCS/SCS jest uruchomione w węźle klastra A

Grupa klastra **SAP PR1** jest uruchomiona w węźle klastra A. Na przykład na **PR1-ASCS-0**. Przypisz udostępnione dyski S, które są częścią grupy klastra **SAP PR1** , a których wystąpienie ASCS/SCS używa, do węzła klastra A.

![Rysunek 61: Menedżer klastra trybu failover: Grupa klastra SAP <identyfikator SID> jest uruchomiona w węźle klastra A][sap-ha-guide-figure-5000]

_**Rysunek 61:** Menedżer klastra trybu failover: Grupa klastra <z *identyfikatorem SID* SAP> jest uruchomiona w węźle klastra A_

W narzędziu do zarządzania i konfiguracji usługi oprogramowanie SIOS DataKeeper można zobaczyć, że dane udostępnionego dysku są synchronicznie replikowane z dysku woluminu źródłowego w węźle klastra A na wolumin docelowy dysku S w węźle klastra B. Na przykład jest replikowana z **PR1-ASCS-0 [10.0.0.40]** do **PR1-ASCS-1 [10.0.0.41]**.

![Rysunek 62: w oprogramowanie SIOS DataKeeper wolumin lokalny można replikować z węzła klastra A do węzła klastra B][sap-ha-guide-figure-5001]

_**Rysunek 62:** W oprogramowanie SIOS DataKeeper, Replikuj wolumin lokalny z węzła klastra A do węzła klastra B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Tryb failover z węzła A do węzła B

1. Wybierz jedną z tych opcji, aby zainicjować tryb failover <*identyfikatora SID* SAP> grupy klastra z węzła klastra a na węzeł klastra B:
   - Użyj Menedżer klastra trybu failover  
   - Korzystanie z programu PowerShell klastra trybu failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Uruchom ponownie węzeł klastra A w systemie operacyjnym gościa systemu Windows (spowoduje to zainicjowanie automatycznej pracy awaryjnej usługi SAP <*Identyfikator SID*> grupy klastra z węzła A do węzła B).  
3. Uruchom ponownie węzeł klastra A z Azure Portal (spowoduje to zainicjowanie automatycznej pracy awaryjnej usługi SAP <*SID*> grupie klastra z węzła A do węzła B).  
4. Uruchom ponownie węzeł klastra A przy użyciu Azure PowerShell (spowoduje to zainicjowanie automatycznej pracy awaryjnej usługi SAP <*SID*> grupie klastra z węzła A do węzła B).

   Po przejściu w tryb failover w węźle klastra B zostanie uruchomiona <*Identyfikator SID*>j grupy klastrów. Na przykład jest uruchomiona w systemie **PR1-ASCS-1**.

   ![Rysunek 63: w Menedżer klastra trybu failover, identyfikator SID <protokołu SAP> grupy klastra jest uruchomiony w węźle klastra B][sap-ha-guide-figure-5002]

   _**Rysunek 63**: w Menedżer klastra trybu failover, *Identyfikator SID* <protokołu SAP> grupy klastra jest uruchomiony w węźle klastra B_

   Udostępniony dysk jest teraz zainstalowany w węźle klastra B. oprogramowanie SIOS DataKeeper replikuje dane z woluminu źródłowego dysku S w węźle klastra B do docelowego dysku woluminu w węźle klastra A. Na przykład replikacja z **PR1-ASCS-1 [10.0.0.41]** do **PR1-ASCS-0 [10.0.0.40]**.

   ![Rysunek 64: oprogramowanie SIOS DataKeeper replikuje wolumin lokalny z węzła klastra B do węzła klastra A][sap-ha-guide-figure-5003]

   _**Rysunek 64:** OPROGRAMOWANIE SIOS DataKeeper replikuje wolumin lokalny z węzła klastra B do węzła klastra A_
