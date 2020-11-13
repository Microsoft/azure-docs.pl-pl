---
title: Zarządzanie maszynami wirtualnymi SQL Server na platformie Azure przy użyciu Azure Portal | Microsoft Docs
description: Dowiedz się, jak uzyskać dostęp do zasobu maszyny wirtualnej SQL w Azure Portal dla maszyny wirtualnej SQL Server hostowanej na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b563e8ca93487a123f97f0bbb86624dc3be2db0
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556294"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Zarządzanie maszynami wirtualnymi SQL Server na platformie Azure przy użyciu Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W [Azure Portal](https://portal.azure.com)zasób [**maszyny wirtualne SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) to niezależna usługa zarządzania umożliwiająca zarządzanie SQL Server na maszynach wirtualnych platformy Azure. Służy do wyświetlania wszystkich SQL Server maszyn wirtualnych jednocześnie i modyfikowania ustawień przeznaczonych dla SQL Server: 

![Zasób maszyn wirtualnych SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Uwagi

- Zalecamy używanie zasobu [**maszyny wirtualne SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) do wyświetlania maszyn wirtualnych SQL Server i zarządzania nimi na platformie Azure. Obecnie zasób **maszyn wirtualnych SQL** nie obsługuje zarządzania [końcami](sql-server-2008-extend-end-of-support.md) SQL Server maszyn wirtualnych. Aby zarządzać ustawieniami dla punktów końcowych SQL Server, użyj [karty konfiguracja](#access-the-sql-server-configuration-tab) przestarzałej SQL Server. 
- Zasób **maszyny wirtualne SQL** jest dostępny tylko dla SQL Server maszyn wirtualnych, które zostały [zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Dostęp do zasobu maszyn wirtualnych SQL
Aby uzyskać dostęp do zasobu **maszyny wirtualne SQL** , wykonaj następujące czynności:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję **wszystkie usługi**. 
1. W polu wyszukiwania wprowadź **maszyny wirtualne SQL** .
1. (Opcjonalnie): Wybierz gwiazdkę obok pozycji **SQL Virtual Machines** , aby dodać tę opcję do menu **Ulubione** . 
1. Wybierz pozycję **maszyny wirtualne SQL**. 

   ![Znajdź SQL Server maszyn wirtualnych w ramach wszystkich usług](./media/manage-sql-vm-portal/sql-vm-search.png)

1. W portalu znajduje się lista wszystkich maszyn wirtualnych SQL Server dostępnych w ramach subskrypcji. Wybierz tę, którą chcesz zarządzać, aby otworzyć zasób **maszyny wirtualne SQL** . Jeśli maszyna wirtualna SQL Server nie pojawia się, użyj pola wyszukiwania. 

   ![Wszystkie dostępne SQL Server maszyny wirtualne](./media/manage-sql-vm-portal/all-sql-vms.png)

   Wybranie maszyny wirtualnej SQL Server spowoduje otwarcie zasobu **maszyny wirtualnej SQL** : 


   ![Wyświetlanie zasobów maszyn wirtualnych SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> Zasób **maszyn wirtualnych SQL** jest przeznaczony dla dedykowanych ustawień SQL Server. Wybierz nazwę maszyny wirtualnej w polu **maszyna wirtualna** , aby otworzyć ustawienia specyficzne dla maszyny wirtualnej, ale nie wyłącznie do SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Dostęp do karty konfiguracja SQL Server
Karta **konfiguracja SQL Server** była przestarzała. Teraz jest to jedyna metoda zarządzania [końcowymi](sql-server-2008-extend-end-of-support.md) SQL Server maszynami wirtualnymi, a SQL Server maszyn wirtualnych, które nie zostały [zarejestrowane przy użyciu rozszerzenia agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

Aby uzyskać dostęp do karty **Konfiguracja** przestarzałej SQL Server, przejdź do zasobu **maszyny wirtualne** . Wykonaj następujące kroki:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję **wszystkie usługi**. 
1. W polu wyszukiwania wprowadź **maszyny wirtualne** .
1. (Opcjonalnie): Wybierz gwiazdkę obok pozycji **maszyny wirtualne** , aby dodać tę opcję do menu **Ulubione** . 
1. Wybierz pozycję **Maszyny wirtualne**. 

   ![Wyszukaj maszyny wirtualne](./media/manage-sql-vm-portal/vm-search.png)

1. W portalu znajduje się lista wszystkich maszyn wirtualnych w ramach subskrypcji. Wybierz tę, którą chcesz zarządzać, aby otworzyć zasób **maszyny wirtualne** . Jeśli maszyna wirtualna SQL Server nie pojawia się, użyj pola wyszukiwania. 
1. Wybierz pozycję **konfiguracja SQL Server** w okienku **Ustawienia** , aby zarządzać SQL Server maszyną wirtualną. 

   ![Konfiguracja programu SQL Server](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](doc-changes-updates-release-notes.md)


