---
title: Zmiany w dokumentacji dotyczące SQL Server na platformie Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej na temat nowych funkcji i ulepszeń SQL Server na platformie Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 5e9e51444a5ccd811773200eb6e112ff277d0901
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342884"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Zmiany w dokumentacji dotyczące SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

System Azure umożliwia wdrożenie maszyny wirtualnej za pomocą obrazu SQL Server wbudowane. Ten artykuł zawiera podsumowanie zmian w dokumentacji związanych z nowymi funkcjami i ulepszeniami w ostatnich wersjach [SQL Server na platformie Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Styczeń 2020 r.

| Zmiany | Szczegóły |
| --- | --- |
| **Obsługa Azure Government** | Teraz można rejestrować SQL Server maszyny wirtualne z dostawcą zasobów maszyny wirtualnej SQL dla maszyn wirtualnych hostowanych w chmurze [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Zmiany | Szczegóły |
 --- | --- |
| **Bezpłatna replika DR na platformie Azure** | Możesz hostować [bezpłatne wystąpienie pasywne](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) na potrzeby odzyskiwania po awarii na platformie Azure dla lokalnego wystąpienia SQL Server, jeśli masz program [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Rejestracja dostawcy zasobów zbiorczych** | Teraz można [rejestrować zbiorczo](sql-vm-resource-provider-bulk-register.md) SQL Server maszyn wirtualnych przy użyciu dostawcy zasobów. | 
|**Konfiguracja magazynu zoptymalizowanego pod kątem wydajności** | Teraz można w [pełni dostosować konfigurację magazynu](storage-configuration.md#new-vms) podczas tworzenia nowej maszyny wirtualnej SQL Server. |
|**Udział plików w warstwie Premium dla FCI** | Teraz można utworzyć wystąpienie klastra trybu failover przy użyciu [udziału plików Premium](failover-cluster-instance-premium-file-share-manually-configure.md) zamiast oryginalnej metody [bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Azure Dedicated Host** | Maszynę wirtualną SQL Server można uruchomić na [dedykowanym hoście platformy Azure](dedicated-host.md). | 
| **Przenoszenie SQL Server maszyny wirtualnej do innego regionu** | Użyj Azure Site Recovery, aby [migrować maszynę wirtualną SQL Server z jednego regionu do innego](move-sql-vm-different-region.md). |
|  **Nowe tryby instalacji programu SQL IaaS** | Teraz można zainstalować rozszerzenie SQL Server IaaS w [trybie uproszczonym](sql-server-iaas-agent-extension-automate-management.md) , aby uniknąć ponownego uruchomienia usługi SQL Server.  |
| **Modyfikacja wersji SQL Server** | Teraz możesz zmienić [Właściwość wersji](change-sql-server-edition.md) dla maszyny wirtualnej SQL Server. |
| **Zmiany dostawcy zasobów maszyny wirtualnej SQL** | [Maszynę wirtualną SQL Server można zarejestrować za pomocą dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md) przy użyciu nowych trybów IaaS języka SQL. Ta możliwość obejmuje obrazy [systemu Windows Server 2008](sql-vm-resource-provider-register.md#management-modes) .|
| **Korzystaj z obrazów własnych licencji przy użyciu Korzyść użycia hybrydowego platformy Azure** | Obrazy dotyczące przenoszenia własnych licencji wdrożone z portalu Azure Marketplace mogą teraz zmienić [Typ licencji na płatność zgodnie z rzeczywistym](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)użyciem.| 
| **Nowe SQL Server zarządzanie maszyną wirtualną w programie Azure Portal** | Teraz można zarządzać maszyną wirtualną SQL Server w Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie maszynami wirtualnymi SQL Server w Azure Portal](manage-sql-vm-portal.md).  | 
| **Rozszerzona pomoc techniczna dla SQL Server 2008/2008 R2** | [Rozszerzona pomoc techniczna](sql-server-2008-extend-end-of-support.md) dla SQL Server 2008 i SQL Server 2008 R2 *przez Migrowanie go na* maszynę wirtualną platformy Azure. | 
| **Obsługa obrazów niestandardowych** | Teraz można zainstalować [rozszerzenie SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) w przypadku niestandardowych systemów operacyjnych i obrazów SQL Server, które oferują ograniczoną funkcjonalność [elastycznej licencji](licensing-model-azure-hybrid-benefit-ahb-change.md). Podczas rejestrowania niestandardowego obrazu przy użyciu dostawcy zasobów maszyny wirtualnej SQL określ typ licencji jako "— AHUB". W przeciwnym razie rejestracja zakończy się niepowodzeniem. | 
| **Obsługa nazwanych wystąpień** | Teraz można użyć [rozszerzenia SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) z nazwanym wystąpieniem, jeśli domyślne wystąpienie zostało odinstalowane prawidłowo. | 
| **Rozszerzenie portalu** | Azure Portal środowisko wdrażania SQL Server maszyny wirtualnej zostało odnowionych w celu poprawy użyteczności. Aby uzyskać więcej informacji, zobacz krótkie Przewodnik [szybkiego startu](sql-vm-create-portal-quickstart.md) i dokładniejsze [instrukcje](create-sql-vm-portal.md) dotyczące wdrażania SQL Server maszyny wirtualnej.|
| **Udoskonalenie portalu** | Teraz można zmienić model licencjonowania dla maszyny wirtualnej SQL Server, wybierając pozycję płatność zgodnie z rzeczywistym użyciem, aby przenieść własną licencję przy użyciu [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Uproszczenie wdrażania grup dostępności za pomocą interfejsu wiersza polecenia platformy Azure SQL Server VM** | Wdrażanie grupy dostępności na maszynie wirtualnej SQL Server na platformie Azure jest teraz łatwiejsze niż kiedykolwiek wcześniej. Możesz użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) do utworzenia klastra trybu failover systemu Windows, wewnętrznego modułu równoważenia obciążenia i wszystkich odbiorników grupy dostępności z wiersza poleceń. Aby uzyskać więcej informacji, zobacz [Używanie interfejsu wiersza polecenia platformy azure SQL Server VM w celu skonfigurowania zawsze włączonej grupy dostępności dla SQL Server na maszynie wirtualnej platformy Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Zmiany | Szczegóły |
| --- | --- |
|  **Nowy dostawca zasobów dla klastra SQL Server** | Nowy dostawca zasobów (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definiuje metadane klastra trybu failover systemu Windows. Przyłączanie maszyny wirtualnej SQL Server do *SqlVirtualMachineGroups* Bootstrap usługi Windows Server Failover Clustering (WSFC) i przyłączanie maszyny wirtualnej do klastra.  |
| **Zautomatyzowana konfiguracja wdrożenia grupy dostępności przy użyciu szablonów szybkiego startu platformy Azure** |Teraz można utworzyć klaster trybu failover systemu Windows, dołączyć do niego SQL Server maszyny wirtualne, utworzyć odbiornik i skonfigurować wewnętrzny moduł równoważenia obciążenia przy użyciu dwóch szablonów szybkiego startu platformy Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z szablonów szybkiego startu platformy Azure w celu skonfigurowania grupy dostępności zawsze włączone dla SQL Server na maszynie wirtualnej platformy Azure](availability-group-quickstart-template-configure.md). | 
| **Automatyczna rejestracja dostawcy zasobów maszyny wirtualnej SQL** | SQL Server maszyny wirtualne wdrożone po tym miesiącu zostaną automatycznie zarejestrowane przy użyciu nowego dostawcy zasobów maszyny wirtualnej SQL. SQL Server maszyny wirtualne wdrożone przed tym miesiącem nadal muszą zostać zarejestrowane ręcznie. Aby uzyskać więcej informacji, zobacz [Rejestrowanie maszyny wirtualnej SQL Server na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md).|
|**Nowy dostawca zasobów maszyny wirtualnej SQL** |  Nowy dostawca zasobów (Microsoft. SqlVirtualMachine) zapewnia lepsze zarządzanie maszynami wirtualnymi SQL Server. Aby uzyskać więcej informacji na temat rejestrowania maszyn wirtualnych, zobacz artykuł [Rejestrowanie maszyny wirtualnej SQL Server na platformie Azure przy użyciu dostawcy zasobów maszyny wirtualnej SQL](sql-vm-resource-provider-register.md). |
|**Przełącz model licencjonowania** | Teraz możesz przechodzić między modelami "płatność za użycie" i "Przenieś własne" dla maszyny wirtualnej SQL Server za pomocą interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Aby uzyskać więcej informacji, zobacz [jak zmienić model licencjonowania dla SQL Server maszyny wirtualnej na platformie Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Zasoby dodatkowe

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Windows](create-sql-vm-portal.md)
* [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](migrate-to-vm-from-sql-server.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Najlepsze rozwiązania w zakresie wydajności SQL Server na platformie Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines](application-patterns-development-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Często zadawane pytania (Linux)](../linux/frequently-asked-questions-faq.md)
* [Dokumentacja SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
