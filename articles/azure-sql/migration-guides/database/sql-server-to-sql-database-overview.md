---
title: 'SQL Server do Azure SQL Database: Przegląd migracji'
description: Dowiedz się więcej na temat narzędzi i opcji dostępnych do migrowania baz danych SQL Server do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065178"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Przegląd migracji: SQL Server do Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Dowiedz się więcej na temat opcji i zagadnień dotyczących migrowania baz danych SQL Server do programu Azure SQL Database. 

Możliwe jest Migrowanie SQL Server baz danych działających lokalnie lub na: 

- SQL Server w usłudze Azure Virtual Machines.  
- Amazon Web Services (AWS) elastyczna chmura obliczeniowa (EC2).
- Usługa relacyjnej bazy danych AWS (RDS).
- Aparat obliczeniowy w Google Cloud Platform (GCP).  
- SQL w chmurze dla SQL Server w GCP. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Omówienie

[Azure SQL Database](../../database/sql-database-paas-overview.md) to zalecana opcja docelowa dla obciążeń SQL Server, które wymagają w pełni zarządzanej platformy jako usługi (PaaS). SQL Database obsługuje większość funkcji zarządzania bazami danych. Ma także wbudowaną funkcję wysokiej dostępności, inteligentnego przetwarzania zapytań, skalowalności i wydajności w celu zapewnienia przydatności wielu typów aplikacji. 

SQL Database zapewnia elastyczność z wieloma [modelami wdrażania](../../database/sql-database-paas-overview.md#deployment-models) i [warstwami usług](../../database/service-tiers-vcore.md#service-tiers) , które mają zastosowanie do różnych typów aplikacji lub obciążeń.

Jedną z najważniejszych zalet migracji do SQL Database jest możliwość modernizacji aplikacji przy użyciu funkcji PaaS. Następnie można wyeliminować każdą zależność od składników technicznych, które są objęte zakresem wystąpienia, na przykład zadania programu SQL Agent.

Koszty można także zmniejszyć przy użyciu [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server do migrowania lokalnych licencji SQL Server do Azure SQL Database. Ta opcja jest dostępna w przypadku wybrania [modelu zakupu opartego na rdzeń wirtualny](../../database/service-tiers-vcore.md).

## <a name="considerations"></a>Zagadnienia do rozważenia 

Kluczowe czynniki do uwzględnienia podczas oceniania opcji migracji są następujące: 

- Liczba serwerów i baz danych
- Rozmiar baz danych
- Akceptowalny przestój biznesowy podczas procesu migracji 

W przypadku opcji migracji wymienionych w tym przewodniku uwzględniono te czynniki. W przypadku migrowania danych logicznych do Azure SQL Database czas migracji może zależeć od liczby obiektów w bazie danych i rozmiaru bazy danych. 

Narzędzia są dostępne dla różnych obciążeń i preferencji użytkownika. Niektóre narzędzia mogą służyć do przeprowadzenia szybkiej migracji pojedynczej bazy danych za pomocą narzędzia opartego na interfejsie użytkownika. Inne narzędzia umożliwiają automatyzację migracji wielu baz danych w celu obsługi migracji na dużą skalę. 

## <a name="choose-an-appropriate-target"></a>Wybierz odpowiedni element docelowy

Zapoznaj się z ogólnymi wskazówkami, które ułatwią wybór właściwego modelu wdrażania i warstwy usług Azure SQL Database. Podczas wdrażania można wybrać zasoby obliczeniowe i magazynowe, a następnie [zmienić je później przy użyciu Azure Portal](../../database/scale-resources.md) bez ponoszenia przestojów aplikacji.

**Modele wdrażania**: informacje o obciążeniu aplikacji oraz wzorcu użycia w celu podjęcia decyzji między pojedynczą bazą danych lub pulą elastyczną. 

- [Pojedyncza baza danych](../../database/single-database-overview.md) reprezentuje w pełni zarządzaną bazę danych, która jest odpowiednia dla większości nowoczesnych aplikacji w chmurze i mikrousług.
- [Pula elastyczna](../../database/elastic-pool-overview.md) jest kolekcją pojedynczych baz danych z współdzielonym zestawem zasobów, na przykład procesora CPU lub pamięci. Jest to odpowiednie do łączenia baz danych w puli z przewidywalnymi wzorcami użycia, które mogą efektywnie współużytkować ten sam zestaw zasobów.

**Modele zakupów**: wybór między rdzeń wirtualny, jednostką transakcji bazy danych (DTU) lub modelami zakupów bezserwerowych. 

- [Model rdzeń wirtualny](../../database/service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych dla Azure SQL Database, więc jest to najprostszy wybór w przypadku tłumaczenia z SQL Server lokalnych. Jest to jedyna opcja, która obsługuje zapisywanie kosztów licencji przy użyciu [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [Model DTU](../../database/service-tiers-dtu.md) służy do podzielenia zasobów obliczeniowych, pamięci I operacji we/wy w celu zapewnienia mieszanych jednostek DTU. 
- [Model bezserwerowy](../../database/serverless-tier-overview.md) jest przeznaczony dla obciążeń, które wymagają automatycznego skalowania na żądanie przy użyciu zasobów obliczeniowych rozliczanych na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych (w przypadku rozliczania tylko magazynu). Powoduje automatyczne wznowienie baz danych po powrocie działania. 

**Warstwy usług**: Wybierz spośród trzech warstw usług przeznaczonych dla różnych typów aplikacji.

- [Warstwa usługi ogólnego przeznaczenia/standardowa](../../database/service-tier-general-purpose.md) oferuje zrównoważoną opcję budżetową z użyciem obliczeń i pamięci masowej, które są odpowiednie do dostarczania aplikacji w warstwie środkowej i niższej. Nadmiarowość jest wbudowana w warstwę magazynowania w celu odzyskiwania po awarii. Jest ona przeznaczona dla większości obciążeń bazy danych. 
- [Warstwa usługi krytyczne dla działania firmy/Premium](../../database/service-tier-business-critical.md) dotyczy aplikacji wysokiego poziomu, które wymagają wysokich stawek transakcji, operacji we/wy o niskich opóźnieniach i wysokiego poziomu odporności. Repliki pomocnicze są dostępne do przełączenia w tryb failover oraz do odciążania obciążeń odczytu.
- [Warstwa usługi do skalowania](../../database/service-tier-hyperscale.md) jest dla baz danych, które mają rosnące ilości danych i muszą automatycznie skalować do 100 TB w rozmiarze bazy danych. Jest ona przeznaczona dla bardzo dużych baz danych. 

> [!IMPORTANT]
> [Stawka dziennika transakcji podlega](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) ograniczeniu Azure SQL Database w celu ograniczenia wysokich stawek za pozyskiwanie. W związku z tym podczas migracji może być konieczne skalowanie docelowych zasobów bazy danych (rdzeni wirtualnych lub DTU) w celu łatwego naciskania na procesor lub przepływność. Wybierz odpowiednią dla docelowej bazy danych o odpowiednim rozmiarze, ale Zaplanuj skalowanie zasobów do migracji w razie potrzeby. 


### <a name="sql-server-vm-alternative"></a>Alternatywa maszyny wirtualnej SQL Server

Firma może mieć wymagania, które sprawiają, że [SQL Server na platformie Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) bardziej odpowiedni cel niż Azure SQL Database. 

Jeśli jeden z poniższych warunków ma zastosowanie do Twojej firmy, rozważ przeniesienie do SQL Server maszyny wirtualnej (VM): 

- Wymagany jest bezpośredni dostęp do systemu operacyjnego lub systemu plików, na przykład w celu zainstalowania agentów innych firm lub niestandardowych na tej samej maszynie wirtualnej z SQL Server. 
- Istnieje ścisła zależność od funkcji, które nadal nie są obsługiwane, takich jak transbaseowe i wielostronicowe transakcje. 
- Musisz pozostać w określonej wersji SQL Server (na przykład 2012). 
- Wymagania dotyczące obliczeń są znacznie mniejsze niż oferty wystąpienia zarządzanego (na przykład jeden rdzeń wirtualny), a konsolidacja bazy danych nie jest akceptowalną opcją. 


## <a name="migration-tools"></a>Narzędzia migracji 

Zalecamy korzystanie z następujących narzędzi migracji: 

|Technologia | Opis|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Ta usługa platformy Azure ułatwia odnajdywanie i ocenianie danych SQL na dużą skalę w programie VMware. Udostępnia zalecenia dotyczące wdrażania usługi Azure SQL, określanie wielkości docelowej i miesięczne oszacowania. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|To narzędzie pulpitu firmy Microsoft zapewnia bezproblemowe oceny SQL Server i migracji pojedynczej bazy danych do Azure SQL Database (schemat i dane). </br></br>Narzędzie można zainstalować lokalnie na serwerze lub na komputerze lokalnym, który ma łączność ze źródłowymi bazami danych. Proces migracji jest logicznym przenoszeniem danych między obiektami w źródłowej i docelowej bazie danych.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Ta usługa platformy Azure może migrować bazy danych SQL Server do Azure SQL Database za pomocą Azure Portal lub automatycznie za pomocą programu PowerShell. Database Migration Service wymaga wybrania preferowanej sieci wirtualnej platformy Azure podczas aprowizacji, aby zapewnić łączność ze źródłem SQL Server baz danych. Możliwe jest Migrowanie pojedynczych baz danych lub w odpowiedniej skali. |
| | |


W poniższej tabeli wymieniono alternatywne narzędzia migracji: 

|Technologia |Opis  |
|---------|---------|
|[Replikacja transakcyjna](../../database/replication-to-sql-database.md)|Replikowanie danych z tabel źródłowych SQL Server baz danych do Azure SQL Database przez udostępnienie opcji migracji typu subskrybenta wydawcy podczas zachowywania spójności transakcyjnej. Przyrostowe zmiany danych są propagowane do subskrybentów w miarę ich występowania przez wydawców.|
|[Import usługi Export/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) to plik systemu Windows z rozszerzeniem. BACPAC, który hermetyzuje schemat i dane bazy danych. Można użyć BACPAC do eksportowania danych ze źródła SQL Server i zaimportowania danych do Azure SQL Database. Plik BACPAC można zaimportować do nowej bazy danych SQL za pomocą Azure Portal. </br></br> W przypadku skalowania i wydajności z dużymi bazami danych lub dużą liczbą baz danych należy rozważyć użycie narzędzia wiersza polecenia [sqlpackage](../../database/database-import.md#using-sqlpackage) do eksportowania i importowania baz danych.|
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Narzędzie do kopiowania masowego (bcp)](/sql/tools/bcp-utility) kopiuje dane z wystąpienia SQL Server do pliku danych. Użyj narzędzia, aby wyeksportować dane ze źródła i zaimportować plik danych do docelowej bazy danych SQL. </br></br> Aby można było przenosić dane do Azure SQL Database za pomocą funkcji kopiowania zbiorczego o dużej szybkości, możesz użyć [Narzędzia inteligentnego kopiowania masowego](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) , aby zmaksymalizować szybkość transferu, wykorzystując zadania kopiowania równoległego.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|[Działanie kopiowania](../../../data-factory/copy-activity-overview.md) w programie Azure Data Factory migruje dane ze źródłowej bazy danych SQL Server do Azure SQL Database przy użyciu wbudowanych łączników i [środowiska Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory obsługuje szeroką gamę [łączników](../../../data-factory/connector-overview.md) służących do przenoszenia danych ze źródeł SQL Server do Azure SQL Database.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync to usługa oparta na Azure SQL Database, która umożliwia synchronizowanie wybranych danych w wielu bazach danych zarówno lokalnych, jak i w chmurze.</br>Synchronizacja danych jest przydatna w przypadkach, gdy dane muszą być przechowywane w wielu bazach danych w Azure SQL Database lub SQL Server.|
| | |

## <a name="compare-migration-options"></a>Porównanie opcji migracji

Porównaj opcje migracji, aby wybrać ścieżkę, która jest odpowiednia dla potrzeb Twojej firmy. 

Poniższa tabela zawiera porównanie zalecanych opcji migracji: 

|Opcja migracji  |Kiedy stosować  |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | — Migrowanie pojedynczych baz danych (zarówno schematu, jak i danych).  </br> — Może obsłużyć przestoje podczas procesu migracji danych. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Działanie migracji wykonuje przenoszenie danych między obiektami bazy danych (ze źródła do miejsca docelowego), dlatego zalecamy uruchomienie go w czasie poza szczytem. </br> -Data Migration Assistant raportuje stan migracji dla obiektu bazy danych, w tym liczbę migrowanych wierszy.  </br> — W przypadku dużych migracji (liczby baz danych lub rozmiaru bazy danych) Użyj Azure Database Migration Service.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| — Migrowanie pojedynczych baz danych lub na dużą skalę. </br> — Może obsłużyć przestoje podczas procesu migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Migracje na dużą skalę można zautomatyzować za pomocą [programu PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Czas na zakończenie migracji zależy od rozmiaru bazy danych i liczby obiektów w bazie danych. </br> -Wymaga ustawienia źródłowej bazy danych jako tylko do odczytu. |
| | | |

Poniższa tabela zawiera porównanie alternatywnych opcji migracji: 

|Metoda lub technologia |Kiedy stosować    |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Replikacja transakcyjna](../../database/replication-to-sql-database.md)| — Migrowanie przez ciągłe publikowanie zmian z tabel źródłowych baz danych do docelowych tabel SQL Database. </br> -Wykonaj pełne lub częściowe migracje baz danych wybranych tabel (podzbiór bazy danych).  </br> </br> Obsługiwane źródła: </br> - [SQL Server (2016 do 2019) z pewnymi ograniczeniami](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP COMPUTE SQL Server VM  | -Konfiguracja jest stosunkowo złożona w porównaniu z innymi opcjami migracji.   </br> — Udostępnia opcję ciągłej replikacji danych (bez przełączania baz danych w tryb offline).  </br> -Replikacja transakcyjna ma ograniczenia, które należy wziąć pod uwagę podczas konfigurowania wydawcy w wystąpieniu SQL Server źródłowym. Zobacz [ograniczenia dotyczące publikowania obiektów](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) , aby dowiedzieć się więcej. </br>— Istnieje możliwość [monitorowania aktywności replikacji](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import usługi Export/BACPAC](../../database/database-import.md)| — Migrowanie poszczególnych baz danych aplikacji biznesowych. </br>— Odpowiednie dla mniejszych baz danych.  </br>  -Nie wymaga oddzielnej usługi lub narzędzia migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM  |  -Wymaga przestoju, ponieważ dane muszą zostać wyeksportowane w źródle i zaimportowane w miejscu docelowym.   </br> -Formaty plików i typy danych używane do eksportowania lub importowania muszą być spójne ze schematami tabel, aby uniknąć obcięcia lub niezgodności typów danych.  </br> — Czas poświęcony na Eksportowanie bazy danych o dużej liczbie obiektów może być znacznie wyższy.       |
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| — Wykonaj pełne lub częściowe migracje danych. </br> — Może obsłużyć przestoje. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM   | -Wymaga przestoju podczas eksportowania danych ze źródła i importowania ich do obiektu docelowego. </br> -Formaty plików i typy danych używane podczas eksportowania lub importowania muszą być spójne ze schematami tabel. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Migruj i/lub Przekształć dane ze źródłowej bazy danych SQL Server. </br> — Scalanie danych z wielu źródeł danych do Azure SQL Database jest zwykle przeznaczone dla obciążeń analizy biznesowej.  |  -Wymaga utworzenia potoków przenoszenia danych w Data Factory, aby przenieść dane ze źródła do miejsca docelowego.   </br> - [Koszt](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jest ważnym zagadnieniem i opiera się na czynnikach, takich jak wyzwalacze potokowe, uruchomienia działań i czas trwania przenoszenia danych. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synchronizuj dane między źródłową i docelową bazą danych.</br> -Odpowiednie do uruchamiania ciągłej synchronizacji między Azure SQL Database i SQL Server lokalnych w przepływie dwukierunkowym. | -Azure SQL Database musi być bazą danych centrum do synchronizacji z lokalną bazą danych SQL Server jako składowaną bazę danych.</br> — W porównaniu z replikacją transakcyjną SQL Data Sync obsługuje wielokierunkową synchronizację danych między środowiskiem lokalnym i Azure SQL Database. </br> — Może mieć wyższy wpływ na wydajność, w zależności od obciążenia.|
| | | |

## <a name="feature-interoperability"></a>Współdziałanie funkcji 

Podczas migrowania obciążeń, które są zależne od innych funkcji SQL Server, należy wziąć pod uwagę.

### <a name="sql-server-integration-services"></a>Usługi SQL Server Integration Services
Migruj pakiety SQL Server Integration Services (SSIS) na platformę Azure przez ponowne wdrożenie pakietów w środowisku uruchomieniowym Azure-SSIS w [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [obsługuje migrację pakietów SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) , zapewniając środowisko uruchomieniowe stworzone do uruchamiania pakietów usług SSIS na platformie Azure. Alternatywnie można ponownie napisać logikę usługi SSIS ETL (wyodrębnianie, przekształcanie, ładowanie) natywnie w Azure Data Factory przy użyciu [przepływów danych](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrowanie raportów usługi SQL Server Reporting Services (SSRS) do raportów z podziałem na strony w programie Power BI. Użyj [Narzędzia do migracji RDL](https://github.com/microsoft/RdlMigration) , aby pomóc w przygotowaniu i migracji raportów. Firma Microsoft opracowała to narzędzie, aby pomóc klientom w migrowaniu raportów Report Definition Language (RDL) z serwerów usług SSRS do Power BI. Jest ono dostępne w witrynie GitHub i zawiera kompleksowe wskazówki dotyczące scenariusza migracji. 

### <a name="high-availability"></a>Wysoka dostępność
Ręczna konfiguracja SQL Server funkcji wysokiej dostępności, takich jak zawsze włączone wystąpienia klastra trybu failover i zawsze włączone grupy dostępności, jest przestarzała w docelowej bazie danych SQL. Architektura wysokiej dostępności jest już wbudowana w warstwy usług [ogólnego przeznaczenia (standard Availability model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) i [krytyczne dla działania firmy (model dostępności premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) dla Azure SQL Database. Warstwa usługi Krytyczne dla działania firmy/Premium zapewnia również skalowalność w poziomie, która umożliwia łączenie się z jednym z węzłów pomocniczych na potrzeby tylko do odczytu. 

Poza architekturą wysokiej dostępności, która jest uwzględniona w Azure SQL Database, funkcja [grup autotrybu failover](../../database/auto-failover-group-overview.md) umożliwia zarządzanie replikacją i trybem failover baz danych w wystąpieniu zarządzanym w innym regionie. 

### <a name="sql-agent-jobs"></a>Zadania programu SQL Agent
Zadania programu SQL Agent nie są bezpośrednio obsługiwane w Azure SQL Database i muszą zostać wdrożone w [zadaniach Elastic Database (wersja zapoznawcza)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Nazwy logowania i grupy
Przenieś nazwy logowania SQL ze źródła SQL Server do Azure SQL Database przy użyciu Database Migration Service w trybie offline. Użyj okienka **wybrane logowania** w Kreatorze migracji, aby przeprowadzić migrację logowań do docelowej bazy danych SQL. 

Można również migrować użytkowników i grupy systemu Windows za pośrednictwem Database Migration Service, włączając odpowiedni przełącznik na stronie **konfiguracji** Database Migration Service. 

Alternatywnie można użyć [Narzędzia programu PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) specjalnie zaprojektowanego przez architektów migracji danych firmy Microsoft. Narzędzie korzysta z programu PowerShell do tworzenia skryptu Transact-SQL (T-SQL) w celu ponownego tworzenia logowań i wybierania użytkowników bazy danych z lokalizacji źródłowej do docelowej. 

Narzędzie programu PowerShell automatycznie mapuje konta systemu Windows Server Active Directory na konta Azure Active Directory (Azure AD) i może wykonać wyszukiwanie nazw UPN dla każdego logowania względem wystąpienia Active Directory źródłowego. Narzędzie skryptuje niestandardowe role serwera i bazy danych wraz z członkostwem w roli i uprawnieniami użytkownika. Zawarte bazy danych nie są jeszcze obsługiwane, a tylko podzbiór możliwych uprawnień SQL Server są skryptami. 

### <a name="system-databases"></a>Systemowe bazy danych
W przypadku Azure SQL Database jedynymi odpowiednimi bazami danych są systemy [Master](/sql/relational-databases/databases/master-database) i tempdb. Aby dowiedzieć się więcej, zobacz [tempdb w Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Funkcje zaawansowane 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze w SQL Database. Nie trzeba na przykład zajmować się zarządzaniem kopiami zapasowymi, ponieważ usługa robi to za Ciebie. W okresie przechowywania można przywrócić wszystkie [punkty w czasie](../../database/recovery-using-backups.md#point-in-time-restore). 

Aby zwiększyć bezpieczeństwo, należy rozważyć [użycie uwierzytelniania usługi Azure AD](../../database/authentication-aad-overview.md), [inspekcje](../../database/auditing-overview.md), [wykrywanie zagrożeń](../../database/azure-defender-for-sql.md), [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security)i [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń, SQL Database udostępnia narzędzia, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). [Azure SQL Analytics (wersja zapoznawcza)](../../../azure-monitor/insights/azure-sql.md) to zaawansowane rozwiązanie do monitorowania wydajności wszystkich baz danych w Azure SQL Database w odpowiedniej skali i wielu subskrypcjach w jednym widoku. Azure SQL Analytics gromadzi i wizualizuje kluczowe metryki wydajności dzięki wbudowanej analizie na potrzeby rozwiązywania problemów z wydajnością.

[Dostrajanie automatyczne](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   ciągle monitoruje wydajność planu wykonywania SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością. 


## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać więcej pomocy, zobacz następujące zasoby, które zostały opracowane dla rzeczywistych projektów migracji.

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla obciążenia. Oferuje proste, oparte na jednym kliknięciu obliczenia i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany i jednolity proces decyzyjny dla platform docelowych.|
|[Narzędzie do ładowania](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Za pomocą usługi dbloader można ładować dane z rozdzielanych plików tekstowych do SQL Server. To narzędzie konsoli systemu Windows używa interfejsu natywnego ładowania zbiorczego klienta SQL Server. Interfejs działa na wszystkich wersjach SQL Server, wraz z Azure SQL Database.|
|[Tworzenie zbiorczych baz danych przy użyciu programu PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Można użyć zestawu trzech skryptów programu PowerShell, które tworzą grupę zasobów (create_rg.ps1), [serwer logiczny na platformie Azure](../../database/logical-servers.md) (create_sqlserver.ps1) i bazę danych SQL (create_sqldb.ps1). Skrypty obejmują funkcje pętli, dzięki czemu można wykonać iterację i utworzyć dowolną liczbę serwerów i baz danych.|
|[Wdrażanie schematu zbiorczego za pomocą MSSQL-Scripter i programu PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Ten element zawartości tworzy grupę zasobów, tworzy jeden lub wiele [serwerów logicznych na platformie Azure](../../database/logical-servers.md) do hostowania Azure SQL Database, eksportuje każdy schemat z wystąpienia lokalnego SQL Server (lub wiele wystąpień SQL Server 2005 +) i importuje schematy do Azure SQL Database.|
|[Konwertowanie zadań agenta SQL Server na zadania Elastic Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Ten skrypt migruje źródłowe zadania agenta SQL Server do zadań Elastic Database.|
|[Wyślij wiadomości e-mail z Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|To rozwiązanie jest alternatywą dla SendMail możliwości i jest dostępne dla SQL Server lokalnych. Używa Azure Functions i usługi SendGrid do wysyłania wiadomości e-mail z Azure SQL Database.|
|[Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skrypt programu PowerShell może utworzyć skrypt poleceń T-SQL, aby ponownie utworzyć logowania i wybrać użytkowników bazy danych z SQL Server lokalnych do Azure SQL Database. Narzędzie to umożliwia automatyczne mapowanie kont Active Directory systemu Windows Server na konta usługi Azure AD oraz opcjonalne Migrowanie SQL Server natywnych nazw logowania.|
|[Automatyzacja zbierania danych monitora wydajności za pomocą narzędzia Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Za pomocą narzędzia Logman można zbierać dane monitora wydajności (aby ułatwić zrozumienie wydajności linii bazowej) i uzyskać zalecenia dotyczące docelowego migracji. To narzędzie używa logman.exe, aby utworzyć polecenie, które będzie tworzyć, uruchamiać, zatrzymywać i usuwać liczniki wydajności ustawione w wystąpieniu SQL Server zdalnego.|
|[Migracja bazy danych do Azure SQL Database przy użyciu BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Ten oficjalny dokument zawiera wskazówki i kroki ułatwiające przyspieszenie migracji SQL Server do Azure SQL Database przy użyciu plików BACPAC.|

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć migrację baz danych SQL Server do Azure SQL Database, zapoznaj się z [przewodnikiem migracji SQL Server do Azure SQL Database](sql-server-to-sql-database-guide.md).

- W przypadku macierzy usług i narzędzi, które mogą ułatwić scenariusze migracji bazy danych i danych oraz zadań specjalnych, zobacz temat [usługi i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat SQL Database, zobacz:
   - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące określania kosztów i rozmiarów obciążeń migrowanych na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów A/B dla warstwy dostępu do danych, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
