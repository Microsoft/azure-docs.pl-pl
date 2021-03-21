---
title: SQL Server SQL Server na maszynie wirtualnej platformy Azure (Omówienie migracji)
description: Poznaj różne strategie migracji, gdy chcesz migrować SQL Server do SQL Server na maszynach wirtualnych platformy Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 0eabb48aabcb50557b342385068807eb67a9b165
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98797846"
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

Migruj do [SQL Server na platformie Azure Virtual Machines (VM)](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) , jeśli chcesz używać znanego środowiska SQL Server z kontrolą systemu operacyjnego i chcesz korzystać z funkcji udostępnianych w chmurze, takich jak wbudowana wysoka dostępność maszyny wirtualnej, [Automatyczne kopie zapasowe](../../virtual-machines/windows/automated-backup.md)i [Automatyczne stosowanie poprawek](../../virtual-machines/windows/automated-patching.md). 

Zaoszczędź na kosztach, wprowadzając własną licencję z [modelem licencjonowania korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) lub rozszerzając wsparcie dla SQL Server 2008 i SQL Server 2008 R2, pobierając [bezpłatne aktualizacje zabezpieczeń](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choose-appropriate-target"></a>Wybierz odpowiedni element docelowy

Usługa Azure Virtual Machines działa w wielu różnych regionach platformy Azure, a także oferuje różne [rozmiary maszyn](../../../virtual-machines/sizes.md) i [Opcje magazynu](../../../virtual-machines/disks-types.md). Podczas określania poprawnego rozmiaru maszyny wirtualnej i magazynu dla SQL Server obciążenia zapoznaj się z [wytycznymi dotyczącymi wydajności SQL Server na platformie Azure Virtual Machines.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) Aby określić rozmiar maszyny wirtualnej i wymagania dotyczące magazynu dla obciążenia. zaleca się, aby były one skalowane przez Performance-Based [Azure Migrate oceny](../../../migrate/concepts-assessment-calculation.md#types-of-assessments). Jeśli ta opcja nie jest dostępna, zapoznaj się z poniższym artykułem dotyczącym tworzenia własnych podstaw [wydajności](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Należy również uwzględnić poprawną instalację i konfigurację SQL Server na maszynie wirtualnej. Zalecane jest używanie [galerii obrazów maszyn wirtualnych SQL Azure](../../virtual-machines/windows/create-sql-vm-portal.md) w następujący sposób, aby można było utworzyć SQL Server maszynę wirtualną z odpowiednią wersją, wydaniem i systemem operacyjnym. Spowoduje to również automatyczne zarejestrowanie maszyny wirtualnej platformy Azure przy użyciu [dostawcy zasobów](../../virtual-machines/windows/create-sql-vm-portal.md) SQL Server, co umożliwi włączenie takich funkcji jak automatyczne tworzenie kopii zapasowych i automatyczne stosowanie poprawek.

## <a name="migration-strategies"></a>Strategie migracji

Istnieją dwie strategie migracji, które umożliwiają Migrowanie baz danych użytkowników do wystąpienia SQL Server na maszynach wirtualnych platformy Azure: **Migruj** i **Unieś i Przenieś**. 

Odpowiednie podejście do Twojej firmy zwykle zależy od następujących czynników: 

- Rozmiar i skala migracji
- Szybkość migracji
- Obsługa aplikacji w celu zmiany kodu
- Należy zmienić wersję SQL Server, system operacyjny lub oba te elementy.
- Cykl pomocy technicznej dla istniejących produktów
- Okno dla przestojów aplikacji podczas migracji

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="Przestój migracji maszyn wirtualnych":::

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
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 z dodatkiem SP4| SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |  Istniejące SQL Server przenoszone do wystąpienia SQL Server na maszynie wirtualnej platformy Azure. Może skalować obciążenia migracji do 35 000 maszyn wirtualnych. <br /><br /> Serwery źródłowe pozostają w trybie online i obsługują żądania podczas synchronizacji danych serwera, co minimalizuje przestoje. <br /><br /> **Automatyzacja & obsługa skryptów**: [Azure Site Recovery skryptów](../../../migrate/how-to-migrate-at-scale.md) i [Przykładowa migracja skalowana i planowanie na platformie Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

Ze względu na łatwość konfigurowania zalecanym rozwiązaniem migracji jest utworzenie natywnego SQL Server [kopii zapasowej](/sql/t-sql/statements/backup-transact-sql) lokalnie, a następnie skopiowanie pliku na platformę Azure. Ta metoda obsługuje większe bazy danych (>1 TB) dla wszystkich wersji SQL Server począwszy od 2008 i większych kopii zapasowych bazy danych (>1 TB). Jednak w przypadku baz danych, które są uruchamiane w SQL Server 2014, które są mniejsze niż 1 TB i które mają dobrą łączność z platformą Azure, to lepszym rozwiązaniem jest [SQL Server kopia zapasowa do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . 

Podczas migrowania SQL Server baz danych do wystąpienia SQL Server na maszynach wirtualnych platformy Azure ważne jest, aby wybrać podejście, które jest potrzebne, gdy trzeba uruchomienie produkcyjne na serwerze docelowym, ponieważ ma to wpływ na okno przestoju aplikacji.

W poniższej tabeli przedstawiono wszystkie dostępne metody migrowania bazy danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure:
<br />

|**Metoda** | **Minimalna wersja źródła** | **Minimalna wersja docelowa** | **Ograniczenie rozmiaru kopii zapasowej źródła** | **Uwagi** |
| --- | --- | --- | --- | --- |
| **[Tworzenie kopii zapasowej do pliku](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 z dodatkiem SP4 | SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |  Jest to prosta i dobrze sprawdzona technika służąca do przemieszczania baz danych między maszynami. Użyj kompresji, aby zminimalizować rozmiar kopii zapasowej na potrzeby transferu. <br /><br /> **Automatyzacja & obsługa skryptów**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) i [AzCopy do magazynu obiektów BLOB](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Utwórz kopię zapasową do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2 | SQL Server 2012 z dodatkiem SP1 ZASTOSUJESZ pakietu CU2| 12,8 TB dla SQL Server 2016, w przeciwnym razie 1 TB | Alternatywny sposób przenoszenia pliku kopii zapasowej na maszynę wirtualną przy użyciu usługi Azure Storage. Użyj kompresji, aby zminimalizować rozmiar kopii zapasowej na potrzeby transferu. <br /><br /> **Automatyzacja & obsługa skryptów**:  [T-SQL lub plan konserwacji](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Asystent migracji bazy danych (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 z dodatkiem SP4| [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |  Usługa [DMA](/sql/dma/dma-overview) ocenia SQL Server lokalnie, a następnie bezproblemowo uaktualnia do nowszych wersji SQL Server lub migruje do SQL Server na maszynach wirtualnych platformy azure, Azure SQL Database lub wystąpieniu zarządzanym Azure SQL. <br /><br /> Nie powinien być używany w bazach danych użytkowników obsługujących funkcję FILESTREAM.<br /><br /> DMA obejmuje również możliwość migrowania [nazw logowania SQL i systemu Windows](/sql/dma/dma-migrateserverlogins) oraz oceniania [pakietów SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Automatyzacja & obsługa skryptów**: [interfejs wiersza polecenia](/sql/dma/dma-commandline) |
| **[Odłączanie i dołączanie](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 z dodatkiem SP4 | SQL Server 2014 | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) | Tej metody należy użyć, jeśli planujesz [przechowywać te pliki przy użyciu usługi Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) i dołączać je do wystąpienia SQL Server na maszynie wirtualnej platformy Azure, szczególnie przydatne w przypadku bardzo dużych baz danych, lub gdy czas tworzenia kopii zapasowej i przywracania jest zbyt długi. <br /><br /> **Automatyzacja & obsługa skryptów**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) i [AzCopy do magazynu obiektów BLOB](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Wysyłanie dziennika](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 z dodatkiem SP4 (tylko system Windows) | SQL Server 2008 z dodatkiem SP4 (tylko system Windows) | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) | Wysyłanie dziennika replikuje transakcyjne pliki dziennika z lokalnego do wystąpienia SQL Server na maszynie wirtualnej platformy Azure. <br /><br /> Zapewnia to minimalny czas przestoju podczas pracy w trybie failover i wymaga mniejszej ilości konfiguracji niż w przypadku konfigurowania zawsze włączonych grup dostępności. <br /><br /> **Automatyzacja & obsługa skryptów**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Rozproszona Grupa dostępności](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Limit przestrzeni dyskowej maszyny wirtualnej platformy Azure](../../../index.yml) |  [Grupa dostępności rozproszonej](/sql/database-engine/availability-groups/windows/distributed-availability-groups) jest specjalnym typem grupy dostępności obejmującej dwie oddzielne grupy dostępności. Grupy dostępności, które uczestniczą w rozproszonej grupie dostępności, nie muszą znajdować się w tej samej lokalizacji i obejmują obsługę wielu domen. <br /><br /> Ta metoda minimalizuje przestoje i jest używana, jeśli masz skonfigurowaną grupę dostępności lokalnie. <br /><br /> **Automatyzacja & obsługa skryptów**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
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

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Obsługiwane wersje

Podczas przygotowywania do migrowania baz danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure należy rozważyć obsługiwane wersje SQL Server. Aby zapoznać się z listą aktualnie obsługiwanych SQL Server wersji na maszynach wirtualnych platformy Azure, zobacz [SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane dla rzeczywistych projektów migracji.

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Automatyzacja zbierania danych monitora wydajności za pomocą narzędzia Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Narzędzie służące do zbierania danych w celu zrozumienia wydajności linii bazowej, która pomaga w rekomendacji docelowej migracji. To narzędzie używające logman.exe do tworzenia polecenia, które będzie tworzyć, uruchamiać, zatrzymywać i usuwać liczniki wydajności ustawione na SQL Server zdalnej.|
|[Wdrażanie SQL Server na platformie Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/SQL%20Server%20Deployment%20in%20Azure%20.pdf)|Niniejszy poradnik zawiera pomoc w przeglądaniu różnych opcji, które umożliwiają przenoszenie obciążeń SQL Server do platformy Azure, w tym porównania funkcji, wysokiej dostępności i tworzenia kopii zapasowych i magazynu. |
|[SQL Server lokalny na maszynę wirtualną platformy Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/OnPremise%20SQL%20Server%20to%20Azure%20VM.pdf)|W tym dokumencie opisano kroki tworzenia kopii zapasowych i przywracania baz danych z SQL Server lokalnych w celu SQL Server na maszynie wirtualnej platformy Azure przy użyciu przykładowych skryptów.|
|[Wiele-SQL-VM-VNet-ILB](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/ARM%20Templates/Multiple-SQL-VM-VNet-ILB)|W tym dokumencie opisano kroki konfigurowania wielu maszyn wirtualnych platformy Azure w ramach konfiguracji programu SQL Server zawsze włączona Grupa dostępności.|
|[Usługi Azure Virtual Machines obsługujące SSD w warstwie Ultra na region](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Find%20Azure%20VMs%20supporting%20Ultra%20SSD)|Te skrypty programu PowerShell udostępniają program programistyczny umożliwiający pobranie listy regionów, które obsługują usługi Azure Virtual Machines obsługujące Ultra dysków SSD.|

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację baz danych SQL Server do SQL Server na maszynach wirtualnych platformy Azure, zapoznaj się z [przewodnikiem migracji poszczególnych baz danych](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o usłudze Azure SQL, zobacz:
   - [Opcje wdrożenia](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Program SQL Server na maszynach wirtualnych platformy Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać informacje o licencjonowaniu, zobacz
   - [Bring your own license z Korzyść użycia hybrydowego platformy Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Uzyskaj bezpłatną rozszerzoną obsługę dla SQL Server 2008 i SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).