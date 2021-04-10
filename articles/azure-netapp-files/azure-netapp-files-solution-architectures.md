---
title: Architektury rozwiązań używające Azure NetApp Files | Microsoft Docs
description: Zawiera informacje o najlepszych rozwiązaniach dotyczących architektur rozwiązań przy użyciu Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 47c641eedbbe096a75223286563acb0a2405b1a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935213"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architektury rozwiązań używające usługi Azure NetApp Files
Ten artykuł zawiera informacje o najlepszych rozwiązaniach, które mogą pomóc w zrozumieniu architektur rozwiązań na potrzeby używania Azure NetApp Files.  

Na poniższym diagramie przedstawiono podsumowanie kategorii architektury rozwiązań, które Azure NetApp Files oferuje:

![Kategorie architektury rozwiązania](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Aplikacje OSS systemu Linux i rozwiązania baz danych

Ta sekcja zawiera odwołania do rozwiązań dla aplikacji OSS systemu Linux i baz danych. 

### <a name="oracle"></a>Oracle

* [Wskazówki dotyczące najlepszych rozwiązań firmy Oracle na platformie Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Obrazy maszyn wirtualnych Oracle i ich wdrożenie na Microsoft Azure: opcje konfiguracji magazynu udostępnionego](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Wydajność bazy danych Oracle na pojedynczych woluminach usługi Azure NetApp Files](performance-oracle-single-volumes.md)
* [Korzyści związane z korzystaniem z usługi Azure NetApp Files w bazie danych Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Usługa Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Aplikacje systemu Windows i rozwiązania SQL Server

Ta sekcja zawiera odwołania do aplikacji systemu Windows i rozwiązań SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Udostępnianie plików i globalne buforowanie plików

* [Utworzyć własny system plików NFS systemu Azure? Udziały plików w systemie Wrestling Linux w chmurze](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Globalna pamięć podręczna plików/wdrażanie Azure NetApp Files](https://youtu.be/91LKb1qsLIM)
* [Zgodność z chmurą dla Azure NetApp Files](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Wdrażanie SQL Server za pośrednictwem protokołu SMB z Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Wdrażanie SQL Server Always-On klastra trybu failover za pośrednictwem protokołu SMB z Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Wdrażanie grup dostępności Always-On przy użyciu Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc) 
* [Zalety korzystania z Azure NetApp Files wdrożenia SQL Server](solutions-benefits-azure-netapp-files-sql-server.md)

## <a name="sap-on-azure-solutions"></a>Rozwiązania SAP na platformie Azure

Ta sekcja zawiera odwołania do rozwiązań SAP w systemie Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>Ogólne SAP i SAP NetWeaver 

* [Aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Windows z Azure NetApp Files (SMB) dla aplikacji SAP](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux for SAP — Przewodnik dotyczący wiele identyfikatorów SID](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Wysoka dostępność SAP HANA skalowanie Azure NetApp Files na Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na SUSE Linux Enterprise Server](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [SAP HANA skalowanie w poziomie z węzłem gotowości na maszynach wirtualnych platformy Azure z Azure NetApp Files na Red Hat Enterprise Linux](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [SAP HANA skalowanie w poziomie za pomocą HSR i Pacemaker na platformie RHEL — Azure Virtual Machines](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Narzędzie do tworzenia migawek spójnych w aplikacji Azure (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>AnyDB SAP

* [Wdrożenie Oracle Azure Virtual Machines DBMS dla obciążeń SAP — Azure Virtual Machines](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Wdrażanie oprogramowania SAP AnyDB (Oracle 19c) przy użyciu Azure NetApp Files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [Wdróż rozwiązanie SAP IQ-NLS HA przy użyciu Azure NetApp Files na SUSE Linux Enterprise Server](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [Jak zarządzać licencją programu SAP IQ w scenariuszu o wysokiej dostępności](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>Społeczność techniczna SAP i wpisy w blogu 

* [Azure NetApp Files — SAP HANA kopii zapasowej w ciągu kilku sekund](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files — przywracanie bazy danych HANA z kopii zapasowej migawki](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files — SAP HANA odciążania kopii zapasowej za pomocą synchronizacji z chmurą](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Przyspiesz kopiowanie systemu SAP HANA przy użyciu Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Woluminy w chmurze ONTAP i Azure NetApp Files: łatwa migracja systemu SAP HANA](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [Decyzje dotyczące architektury w celu zmaksymalizowania inwestycji ANF w programie HANA N + M Scale-Out architekturze — część 1](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [Decyzje dotyczące architektury w celu zmaksymalizowania inwestycji ANF w programie HANA N + M Scale-Out architekturze — część 2](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [Decyzje dotyczące architektury w celu zmaksymalizowania inwestycji ANF w programie HANA N + M Scale-Out architekturze — część 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Ustalanie wielkości i konsolidacja woluminów SAP przy użyciu Azure NetApp Files](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Rozwiązania VMware platformy Azure

* [Azure NetApp Files z rozwiązaniem VMware platformy Azure — instalacje systemu operacyjnego gościa](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Rozwiązania infrastruktury pulpitów wirtualnych

Ta sekcja zawiera odwołania do rozwiązań infrastruktury pulpitu wirtualnego.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Korzyści związane z używaniem usługi Azure NetApp Files w usłudze Windows Virtual Desktop](solutions-windows-virtual-desktop.md)
* [Opcje magazynu dla kontenerów profilów FSLogix w programie Virtual Desktop systemu Windows](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu Azure NetApp Files](../virtual-desktop/create-fslogix-profile-container.md)
* [Usługa Windows Virtual Desktop w skali przedsiębiorstwa](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [FSLogix firmy Microsoft dotyczące najlepszych rozwiązań dla Azure NetApp Files przedsiębiorstw](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [Konfigurowanie Azure NetApp Files do dołączania aplikacji MSIX](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>Rozwiązania HPC

Ta sekcja zawiera odwołania do rozwiązań obliczeniowych o wysokiej wydajności (HPC). 

### <a name="generic-hpc"></a>Ogólne HPC

* [Azure NetApp Files: maksymalne wykorzystanie magazynu w chmurze](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Uruchamianie obciążeń MPI z użyciem Azure Batch i Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: CycleCloud środowiska HPC na Azure NetApp Files](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Olej i gaz

* [Obliczenia o wysokiej wydajności (HPC): ropa naftowa i gaz na platformie Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Uruchamianie oprogramowania do symulacji zbiorników na platformie Azure](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatyzacja projektowania elektronicznego (EDA)

* [Zalety korzystania z usługi Azure NetApp Files na potrzeby automatyzacji projektowania elektroniki](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [CycleCloud Azure: EDA Lab z Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Platforma Azure dla branży półprzewodnikowej](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analiza

* [Azure NetApp Files: udostępniony system plików do użycia z siatką SYGNATURy dostępu współdzielonego na Microsoft Azure](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files: udostępniony system plików do użycia z siatką SAS na platformie MS Azure — RHEL 8.3/nconnect UPDATE](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [Najlepsze rozwiązania dotyczące używania Microsoft Azure z SAS®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Rozwiązania usług platformy Azure

Ta sekcja zawiera rozwiązania dla usług platformy Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Usługi Azure Kubernetes Services i Kubernetes

* [Integracja Azure NetApp Files z usługą Azure Kubernetes Service](../aks/azure-netapp-files.md)
* [Nieaktualna Kubernetes wydajność na platformie Azure dzięki Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = dynamiczny i trwały magazyn dla Kubernetes](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident-Storage Orchestrator for Containers](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Platforma Magento handlu elektronicznego w usłudze Azure Kubernetes Service (AKS)](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Usługa Azure Batch

* [Uruchamianie obciążeń MPI z użyciem Azure Batch i Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
