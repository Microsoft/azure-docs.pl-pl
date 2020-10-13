---
title: Omówienie programu SQL Server na maszynach wirtualnych platformy Azure z systemem Windows | Microsoft Docs
description: Dowiedz się, jak uruchamiać pełne wersje SQL Server na platformie Azure Virtual Machines w chmurze bez konieczności zarządzania sprzętem lokalnym.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ae00703805bef6f5e9758bafcb781eda91cd93f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272161"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Co to jest SQL Server w usłudze Azure Virtual Machines (system Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[SQL Server na platformie Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) umożliwia korzystanie z pełnych wersji SQL Server w chmurze bez konieczności zarządzania sprzętem lokalnym. SQL Server maszyny wirtualne (VM) upraszczają także koszty licencjonowania, gdy płacisz zgodnie z rzeczywistym użyciem.

Maszyny wirtualne platformy Azure działają w wielu różnych [regionach geograficznych](https://azure.microsoft.com/regions/) na świecie. Oferują one także szereg różnych [rozmiarów maszyn](../../../virtual-machines/windows/sizes.md). Galeria obrazów maszyn wirtualnych umożliwia utworzenie maszyny wirtualnej z programem SQL Server we właściwej wersji i edycji oraz z odpowiednim systemem operacyjnym. Dzięki temu maszyny wirtualne są dobrym rozwiązaniem dla wielu różnych obciążeń SQL Server.

Jeśli dopiero zaczynasz SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z *tematem SQL Server na temat* usługi Azure VM — Omówienie z naszej szczegółowej [serii wideo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Aktualizacje automatyczne

SQL Server na platformie Azure Virtual Machines mogą korzystać z [zautomatyzowanego stosowania poprawek](automated-patching.md) w celu zaplanowania okna obsługi w celu automatycznego zainstalowania ważnych aktualizacji systemu windows i SQL Server.

## <a name="automated-backups"></a>Automatyczne kopie zapasowe

SQL Server na platformie Azure Virtual Machines mogą korzystać z [zautomatyzowanej kopii zapasowej](automated-backup.md), która regularnie tworzy kopie zapasowe bazy danych w usłudze BLOB Storage. Tej techniki można również używać ręcznie. Aby uzyskać więcej informacji, zobacz [Use Azure Storage for SQL Server Backup and Restore](azure-storage-sql-server-backup-restore-use.md) (Używanie usługi Azure Storage do tworzenia kopii zapasowych programu SQL Server i ich przywracania).

Platforma Azure oferuje również rozwiązanie do tworzenia kopii zapasowych klasy korporacyjnej dla SQL Server działających na maszynach wirtualnych platformy Azure. W pełni zarządzane rozwiązanie do tworzenia kopii zapasowych obsługuje zawsze włączone grupy dostępności, długoterminowe przechowywanie danych, odzyskiwanie do punktu w czasie oraz centralne zarządzanie i monitorowanie. Aby uzyskać więcej informacji, zobacz [Azure Backup SQL Server na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Wysoka dostępność

Jeśli potrzebujesz wysokiej dostępności, rozważ skonfigurowanie grup dostępności programu SQL Server. Obejmuje to wiele wystąpień SQL Server na platformie Azure Virtual Machines w sieci wirtualnej. Rozwiązanie wysokiej dostępności można skonfigurować ręcznie lub użyć szablonów w Azure Portal na potrzeby automatycznej konfiguracji. Aby zapoznać się z omówieniem wszystkich opcji wysokiej dostępności, zobacz [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Wydajność

Maszyny wirtualne platformy Azure mają różne rozmiary dopasowane do różnych wymagań dotyczących obciążenia. SQL Server maszyny wirtualne zapewniają również zautomatyzowaną konfigurację magazynu, która jest zoptymalizowana pod kątem wymagań dotyczących wydajności. Aby uzyskać więcej informacji na temat konfigurowania magazynu dla maszyn wirtualnych SQL Server, zobacz [Konfiguracja magazynu dla SQL Server maszyn wirtualnych](storage-configuration.md). Aby dostosować wydajność, zobacz [najlepsze rozwiązania w zakresie wydajności SQL Server w usłudze Azure Virtual Machines](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Rozpoczynanie pracy z maszynami wirtualnymi SQL Server

Aby rozpocząć, wybierz obraz maszyny wirtualnej z programem SQL Server w wymaganej wersji i edycji oraz z odpowiednim systemem operacyjnym. Poniższe sekcje zawierają bezpośrednie linki do obrazów maszyn wirtualnych programu SQL Server w galerii witryny Azure Portal.

> [!TIP]
> Aby uzyskać więcej informacji na temat sposobu zrozumienia cen dla SQL Server obrazów, zobacz [wskazówki dotyczące cen SQL Server na platformie Azure Virtual Machines](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Płatność zgodnie z rzeczywistym użyciem
Poniższa tabela zawiera macierz obrazów z programem SQL Server korzystających z modelu płatności zgodnie z rzeczywistym użyciem.

| Wersja | System operacyjny | Wersja |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 z dodatkiem R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 z dodatkiem R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 z dodatkiem R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Aby wyświetlić dostępne SQL Server on Linux obrazy maszyn wirtualnych, zobacz [omówienie SQL Server na platformie Azure Virtual Machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Teraz można zmienić model licencjonowania maszyny wirtualnej z programem SQL Server opłacanej za użycie na własną licencję. Aby uzyskać więcej informacji, zobacz [How to Change the Licensing model for a SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Bring your own license
Możesz również skorzystać z modelu dostarczania własnej licencji (Bring Your Own License, BYOL). W tym scenariuszu płacisz wyłącznie za maszynę wirtualną i nie ponosisz żadnych dodatkowych kosztów licencjonowania programu SQL Server.  Użycie własnej licencji może w dłuższym okresie przynieść oszczędności w przypadku ciągłych obciążeń produkcyjnych. Wymagania dotyczące korzystania z tej opcji zawiera artykuł [Pricing guidance for SQL Server Azure VMs (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server)](pricing-guidance.md#byol).

Aby zapewnić własną licencję, możesz przekonwertować istniejącą maszynę wirtualną z opcją płatność za użycie SQL Server lub wdrożyć obraz z prefiksem **{BYOL}**. Aby uzyskać więcej informacji na temat przełączania modelu licencjonowania między opcją płatność za użycie i BYOL, zobacz [jak zmienić model licencjonowania dla maszyny wirtualnej SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Wersja | System operacyjny | Wersja |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [standardowa BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [standardowa BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 z dodatkiem R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [standardowa BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 z dodatkiem R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Istnieje możliwość wdrożenia starszego obrazu SQL Server, który nie jest dostępny w Azure Portal przy użyciu programu PowerShell. Aby wyświetlić wszystkie dostępne obrazy przy użyciu programu PowerShell, użyj następującego polecenia:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Aby uzyskać więcej informacji o wdrażaniu maszyn wirtualnych programu SQL Server przy użyciu programu PowerShell, zobacz [How to provision SQL Server virtual machines with Azure PowerShell (Jak aprowizować maszyny wirtualne programu SQL Server przy użyciu programu Azure PowerShell)](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną
Po utworzeniu maszyny wirtualnej z programem SQL Server nawiąż z nią połączenie z poziomu aplikacji lub narzędzia, np. SQL Server Management Studio (SSMS). Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrowanie danych
Jeśli masz istniejącą bazę danych, należy przenieść ją na nowo zainicjowaną maszynę wirtualną SQL Server. Aby uzyskać listę opcji migracji i wskazówki, zobacz [Migrating a Database to SQL Server on an Azure VM](migrate-to-vm-from-sql-server.md) (Migrowanie bazy danych do programu SQL Server na maszynie wirtualnej portalu Azure).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Tworzenie zasobów usługi Azure SQL i zarządzanie nimi za pomocą Azure Portal

Azure Portal zawiera jedną stronę, na której można zarządzać [wszystkimi zasobami usługi Azure SQL, w](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) tym maszynami wirtualnymi SQL.

Aby uzyskać dostęp do strony **zasoby SQL platformy Azure** , wybierz pozycję **Azure sql** w menu Azure Portal lub Wyszukaj i wybierz pozycję **Azure SQL** z dowolnej strony.

![Wyszukiwanie w usłudze Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Usługa Azure SQL umożliwia szybkie i łatwe uzyskiwanie dostępu do wszystkich baz danych SQL platformy Azure, pul elastycznych, serwerów logicznych, wystąpień zarządzanych i maszyn wirtualnych. Usługa Azure SQL nie jest usługą ani zasobem. 

Aby zarządzać istniejącymi zasobami, wybierz żądany element z listy. Aby utworzyć nowe zasoby Azure SQL, wybierz pozycję **+ Dodaj**. 

![Tworzenie zasobu usługi Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Po wybraniu **+ Dodaj**Wyświetl dodatkowe informacje o różnych opcjach, wybierając pozycję **Pokaż szczegóły** na dowolnym kafelku.

![Szczegóły kafelka bazy danych](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Aby uzyskać szczegółowe informacje, zobacz:

- [Tworzenie pojedynczej bazy danych](../../database/single-database-create-quickstart.md)
- [Tworzenie elastycznej puli](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Tworzenie wystąpienia zarządzanego](../../managed-instance/instance-create-quickstart.md)
- [Tworzenie maszyny wirtualnej SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Zasady odświeżania obrazu maszyny wirtualnej SQL Server
Platforma Azure utrzymuje tylko jeden obraz maszyny wirtualnej dla każdego połączenia obsługiwanego systemu operacyjnego, wersji i wydania. Oznacza to, że z czasem obrazy będą odświeżane, a ich starsze wersje usuwane. Aby uzyskać więcej informacji, zobacz sekcję **Obrazy** w temacie [Często zadawane pytania dotyczące maszyn wirtualnych programu SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Program poprawy jakości obsługi klienta
Program poprawy jakości obsługi klienta jest domyślnie włączony. Wysyła on okresowo raporty do firmy Microsoft w celu umożliwienia doskonalenia programu SQL Server. Program poprawy jakości obsługi klienta nie wymaga przeprowadzenia żadnych czynności związanych z zarządzaniem. Wyjątek stanowi sytuacja, gdy ma on zostać wyłączony po ukończeniu aprowizacji. Program poprawy jakości obsługi klienta możesz dostosować lub wyłączyć, łącząc się z maszyną wirtualną za pomocą pulpitu zdalnego. Następnie uruchom narzędzie **SQL Server Error and Usage Reporting**. Postępuj zgodnie z instrukcjami, aby wyłączyć raportowanie. Aby uzyskać więcej informacji na temat zbierania danych, zobacz [Zasady zachowania poufności informacji programu SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Powiązane produkty i usługi
### <a name="windows-virtual-machines"></a>Maszyny wirtualne z systemem Windows
* [Omówienie usługi Azure Virtual Machines](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Magazyn
* [Wprowadzenie do usługi Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Networking
* [Przegląd Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [Adresy IP na platformie Azure](../../../virtual-network/public-ip-addresses.md)
* [Tworzenie w pełni kwalifikowanej nazwy domeny w witrynie Azure Portal](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Dokumentacja SQL Server](https://docs.microsoft.com/sql/index)
* [Porównanie usługi Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z usługą SQL Server na platformie Azure Virtual Machines:

* [Tworzenie maszyny wirtualnej z programem SQL Server w witrynie Azure Portal](sql-vm-create-portal-quickstart.md)

Uzyskaj odpowiedzi na często zadawane pytania dotyczące SQL Server maszyn wirtualnych:

* [Często zadawane pytania dotyczące programu SQL Server w usłudze Azure Virtual Machines](frequently-asked-questions-faq.md)

Wyświetlanie architektur referencyjnych na potrzeby uruchamiania aplikacji N-warstwowych na SQL Server w IaaS

* [Aplikacja N-warstwowa systemu Windows na platformie Azure z SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Uruchamianie aplikacji N-warstwowej w wielu regionach platformy Azure w celu zapewnienia wysokiej dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
