---
title: SQL Server SQL Server na maszynie wirtualnej platformy Azure (Omówienie migracji)
description: Poznaj różne strategie migracji, gdy chcesz migrować SQL Server do SQL Server na maszynach wirtualnych platformy Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 134685f56de569b39e28165d7ed25868300810f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497293"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Przegląd migracji: SQL Server do SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Dowiedz się więcej na temat różnych strategii migracji w celu migrowania SQL Server do SQL Server na platformie Azure Virtual Machines. 

Możesz migrować SQL Server uruchomione lokalnie lub na:

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)

Inne scenariusze można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Omówienie

Migruj do [SQL Server na platformie Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) , jeśli chcesz używać znanego środowiska SQL Server z kontrolą systemu operacyjnego i chcesz korzystać z funkcji udostępnianych w chmurze, takich jak wbudowana wysoka dostępność maszyny wirtualnej, [Automatyczne kopie zapasowe](../../virtual-machines/windows/automated-backup.md)i [Automatyczne stosowanie poprawek](../../virtual-machines/windows/automated-patching.md). 

Zaoszczędź na kosztach, wprowadzając własną licencję z [modelem licencjonowania korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) lub rozszerzając wsparcie dla SQL Server 2008 i SQL Server 2008 R2, pobierając [bezpłatne aktualizacje zabezpieczeń](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choosing-appropriate-target"></a>Wybieranie odpowiedniego elementu docelowego

Usługa Azure Virtual Machines działa w wielu różnych regionach platformy Azure, a także oferuje różne [rozmiary maszyn](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) i [Opcje magazynu](https://docs.microsoft.com/azure/virtual-machines/disks-types). Podczas określania poprawnego rozmiaru maszyny wirtualnej i magazynu dla SQL Server obciążenia zapoznaj się z [wytycznymi dotyczącymi wydajności SQL Server na platformie Azure Virtual Machines.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance) Aby określić rozmiar maszyny wirtualnej i wymagania dotyczące magazynu dla obciążenia. zaleca się, aby były one skalowane przez Performance-Based [Azure Migrate oceny](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments). Jeśli ta opcja nie jest dostępna, zapoznaj się z poniższym artykułem dotyczącym tworzenia własnych podstaw [wydajności](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Należy również uwzględnić poprawną instalację i konfigurację SQL Server na maszynie wirtualnej. Zalecane jest używanie [galerii obrazów maszyn wirtualnych SQL Azure](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) w następujący sposób, aby można było utworzyć SQL Server maszynę wirtualną z odpowiednią wersją, wydaniem i systemem operacyjnym. Spowoduje to również automatyczne zarejestrowanie maszyny wirtualnej platformy Azure przy użyciu [dostawcy zasobów](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) SQL Server, co umożliwi włączenie takich funkcji jak automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek.

## <a name="migration-strategies"></a>Strategie migracji

Istnieją dwie strategie migracji, które umożliwiają Migrowanie baz danych użytkowników do wystąpienia SQL Server na maszynach wirtualnych platformy Azure: **Migruj** i **Unieś i Przenieś**. 

Odpowiednie podejście do Twojej firmy zwykle zależy od następujących czynników: 

- Rozmiar i skala migracji
- Szybkość migracji
- Obsługa aplikacji w celu zmiany kodu
- Należy zmienić wersję SQL Server, system operacyjny lub oba te elementy.
- Cykl pomocy technicznej dla istniejących produktów
- Okno dla przestojów aplikacji podczas migracji

W poniższej tabeli opisano różnice między dwiema strategiami migracji:
<br />

| **Strategia migracji** | **Opis** | **Kiedy stosować** |
| --- | --- | --- |
| **Unieś & Shift** | Aby przenieść całą fizyczną lub wirtualną SQL Server z bieżącej lokalizacji na wystąpienie SQL Server na maszynie wirtualnej platformy Azure bez wprowadzania zmian w systemie operacyjnym lub SQL Server wersji, należy użyć strategii migracji i przesunięcia. Aby ukończyć podnieśność i przesunięcia do migracji, zobacz [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> Serwer źródłowy pozostaje w trybie online i usług, podczas gdy serwer źródłowy i docelowy synchronizują dane, umożliwiając niemal bezproblemowe Migrowanie. | Służy do migracji pojedynczej do bardzo dużej skali, nawet mającej zastosowanie do scenariuszy, takich jak wyjście centrum danych. <br /><br /> Nie jest wymagane wprowadzanie zmian w kodzie do baz danych lub aplikacji SQL użytkownika, co umożliwia szybsze przeprowadzanie migracji. <br /><br />Nie są wymagane żadne dodatkowe kroki do migrowania usług analizy biznesowej, takich jak  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)i [SSAS](/analysis-services/analysis-services-overview). |
|**Migrate** | Użyj strategii migracji, gdy chcesz uaktualnić SQL Server docelowy i/lub wersję systemu operacyjnego. <br /> <br /> Wybierz maszynę wirtualną platformy Azure z witryny Azure Marketplace lub przygotowanego obrazu SQL Server zgodnego z wersją SQL Server źródłowej. | Należy używać w przypadku, gdy wymagane jest użycie funkcji dostępnych w nowszych wersjach SQL Server lub jeśli istnieje potrzeba uaktualnienia starszych wersji SQL Server i/lub systemu operacyjnego, które nie są już obsługiwane.  <br /> <br /> Może wymagać wprowadzenia zmian w bazie danych aplikacji lub użytkownika w celu obsługi uaktualnienia SQL Server. <br /><br />W przypadku migracji usług [analizy biznesowej](#business-intelligence) mogą wystąpić dodatkowe zagadnienia. |


## <a name="lift-and-shift"></a>Migrowanie metodą „lift-and-shift”  

Poniższa tabela zawiera szczegółowe informacje o dostępnej metodzie dotyczącej strategii migracji **i przesunięcia** w celu migrowania bazy danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure:
<br />

|**Metoda** | **Minimalna wersja źródła** | **Minimalna wersja docelowa** | **Ograniczenie rozmiaru kopii zapasowej źródła** |  **Uwagi** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 z dodatkiem SP4| SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Istniejące SQL Server przenoszone do wystąpienia SQL Server na maszynie wirtualnej platformy Azure. Może skalować obciążenia migracji do 35 000 maszyn wirtualnych. <br /><br /> Serwery źródłowe pozostają w trybie online i obsługują żądania podczas synchronizacji danych serwera, co minimalizuje przestoje. <br /><br /> **Automatyzacja & obsługa skryptów** : [Azure Site Recovery skryptów](../../../migrate/how-to-migrate-at-scale.md) i [Przykładowa migracja skalowana i planowanie na platformie Azure](/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

Ze względu na łatwość konfigurowania zalecanym rozwiązaniem migracji jest utworzenie natywnego SQL Server [kopii zapasowej](/sql/t-sql/statements/backup-transact-sql) lokalnie, a następnie skopiowanie pliku na platformę Azure. Ta metoda obsługuje większe bazy danych (>1 TB) dla wszystkich wersji SQL Server począwszy od 2008 i większych kopii zapasowych bazy danych (>1 TB). Jednak w przypadku baz danych, które są uruchamiane w SQL Server 2014, które są mniejsze niż 1 TB i które mają dobrą łączność z platformą Azure, to lepszym rozwiązaniem jest [SQL Server kopia zapasowa do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . 

Podczas migrowania SQL Server baz danych do wystąpienia SQL Server na maszynach wirtualnych platformy Azure ważne jest, aby wybrać podejście, które jest potrzebne, gdy trzeba uruchomienie produkcyjne na serwerze docelowym, ponieważ ma to wpływ na okno przestoju aplikacji.

W poniższej tabeli przedstawiono wszystkie dostępne metody migrowania bazy danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure:
<br />

|**Metoda** | **Minimalna wersja źródła** | **Minimalna wersja docelowa** | **Ograniczenie rozmiaru kopii zapasowej źródła** | **Uwagi** |
| --- | --- | --- | --- | --- |
| **[Tworzenie kopii zapasowej do pliku](/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore)** | SQL Server 2008 z dodatkiem SP4 | SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Jest to prosta i dobrze sprawdzona technika służąca do przemieszczania baz danych między maszynami. Użyj kompresji, aby zminimalizować rozmiar kopii zapasowej na potrzeby transferu. <br /><br /> **Automatyzacja & obsługa skryptów** : [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) i [AzCopy do magazynu obiektów BLOB](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Utwórz kopię zapasową do adresu URL](/windows/migrate-to-vm-from-sql-server.md#backup-to-url-and-restore-from-url)** | SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 | SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2| 12,8 TB dla SQL Server 2016, w przeciwnym razie 1 TB | Alternatywny sposób przenoszenia pliku kopii zapasowej na maszynę wirtualną przy użyciu usługi Azure Storage. Użyj kompresji, aby zminimalizować rozmiar kopii zapasowej na potrzeby transferu. <br /><br /> **Automatyzacja & obsługa skryptów** :  [T-SQL lub plan konserwacji](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Asystent migracji bazy danych (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Usługa [DMA](/sql/dma/dma-overview) ocenia SQL Server lokalnie, a następnie bezproblemowo uaktualnia do nowszych wersji SQL Server lub migruje do SQL Server na maszynach wirtualnych platformy azure, Azure SQL Database lub wystąpieniu zarządzanym Azure SQL. <br /><br /> Nie powinien być używany w bazach danych użytkowników obsługujących funkcję FILESTREAM.<br /><br /> DMA obejmuje również możliwość migrowania [nazw logowania SQL i systemu Windows](/sql/dma/dma-migrateserverlogins) oraz oceniania [pakietów SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Automatyzacja & obsługa skryptów** : [interfejs wiersza polecenia](/sql/dma/dma-commandline) |
| **[Odłączanie i dołączanie](/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 z dodatkiem SP4 | SQL Server 2014 | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Tej metody należy użyć, jeśli planujesz [przechowywać te pliki przy użyciu usługi Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) i dołączać je do wystąpienia SQL Server na maszynie wirtualnej platformy Azure, szczególnie przydatne w przypadku bardzo dużych baz danych, lub gdy czas tworzenia kopii zapasowej i przywracania jest zbyt długi. <br /><br /> **Automatyzacja & obsługa skryptów** :  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) i [AzCopy do magazynu obiektów BLOB](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Wysyłanie dziennika](/sql/database-engine/log-shipping/about-log-shipping-sql-server)** | SQL Server 2008 z dodatkiem SP4 (tylko system Windows) | SQL Server 2008 z dodatkiem SP4 (tylko system Windows) | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Wysyłanie dziennika replikuje transakcyjne pliki dziennika z lokalnego do wystąpienia SQL Server na maszynie wirtualnej platformy Azure. <br /><br /> Zapewnia to minimalny czas przestoju podczas pracy w trybie failover i wymaga mniejszej ilości konfiguracji niż w przypadku konfigurowania zawsze włączonych grup dostępności. <br /><br /> **Automatyzacja & obsługa skryptów** : [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Rozproszona Grupa dostępności](/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [Grupa dostępności rozproszonej](/sql/database-engine/availability-groups/windows/distributed-availability-groups) jest specjalnym typem grupy dostępności obejmującej dwie oddzielne grupy dostępności. Grupy dostępności, które uczestniczą w rozproszonej grupie dostępności, nie muszą znajdować się w tej samej lokalizacji i obejmują obsługę wielu domen. <br /><br /> Ta metoda minimalizuje przestoje i jest używana, jeśli masz skonfigurowaną grupę dostępności lokalnie. <br /><br /> **Automatyzacja & obsługa skryptów** : [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> W przypadku dużych transferów danych z ograniczoną liczbą opcji sieci można zobaczyć [duże transfery danych z ograniczoną łącznością](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej znajduje się lista kluczowych zagadnień, które należy wziąć pod uwagę podczas przeglądania metod migracji:

- Aby uzyskać optymalną wydajność transferu danych, należy przeprowadzić migrację baz danych i plików na wystąpienie SQL Server na maszynie wirtualnej platformy Azure przy użyciu skompresowanego pliku kopii zapasowej. W przypadku większych baz danych, oprócz kompresji, należy [podzielić plik kopii zapasowej na mniejsze pliki](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) w celu zwiększenia wydajności podczas tworzenia kopii zapasowej i transferu. 
- W przypadku migrowania z SQL Server 2014 lub nowszej należy rozważyć [zaszyfrowanie kopii zapasowych](/sql/relational-databases/backup-restore/backup-encryption) w celu ochrony danych podczas transferu sieciowego.
- Aby zminimalizować przestoje podczas migracji bazy danych, należy użyć opcji zawsze włączone grupy dostępności. 
- Aby zminimalizować przestoje bez nakładu pracy związanego z konfiguracją grupy dostępności, użyj opcji wysyłania dziennika. 
- Aby ograniczyć liczbę opcji sieci, należy użyć metod migracji w trybie offline, takich jak tworzenie kopii zapasowych i przywracanie, lub [usług transferu dysku](../../../storage/common/storage-solution-large-dataset-low-network.md) dostępnych na platformie Azure.
- Aby zmienić wersję SQL Server na SQL Server na maszynie wirtualnej platformy Azure, zobacz [zmiana SQL Server Edition](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Analiza biznesowa 

Podczas migrowania SQL Server usług analizy biznesowej poza zakresem migracji bazy danych użytkownika mogą wystąpić dodatkowe zagadnienia. 

Te usługi to na przykład:

- [**Usługi SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Obsługiwane wersje

Podczas przygotowywania do migrowania baz danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure należy rozważyć obsługiwane wersje SQL Server. Aby zapoznać się z listą aktualnie obsługiwanych SQL Server wersji na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).


## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację baz danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z [przewodnikiem migracji poszczególnych baz danych](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać informacje o licencjonowaniu, zobacz
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
