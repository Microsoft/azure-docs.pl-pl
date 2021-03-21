---
title: 'SQL Server do SQL Database: Przegląd migracji'
description: Dowiedz się więcej na temat różnych narzędzi i opcji dostępnych do migrowania baz danych SQL Server do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 733b2375a26b0157f88bc148b52932e2f3e3f2e2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488277"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Przegląd migracji: SQL Server do SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Zapoznaj się z różnymi opcjami migracji i zagadnieniami dotyczącymi migracji SQL Server do Azure SQL Database. 

Możesz migrować SQL Server uruchomione lokalnie lub na: 

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)  
- SQL w chmurze dla SQL Server (Google Cloud Platform – GCP) 

Inne scenariusze można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Omówienie

[Azure SQL Database](../../database/sql-database-paas-overview.md) to zalecana opcja docelowa dla obciążeń SQL Server, które wymagają w pełni zarządzanej platformy jako usługi (PaaS). SQL Database obsługuje większość funkcji zarządzania bazami danych, a także wysoką dostępność, inteligentne przetwarzanie zapytań, skalowalność i możliwości wydajności wbudowane w taki sposób, aby odpowiadały wielu różnym typom aplikacji. 

SQL Database zapewnia elastyczność z wieloma [modelami wdrażania](../../database/sql-database-paas-overview.md#deployment-models) i [warstwami usług](../../database/service-tiers-vcore.md#service-tiers) , które mają zastosowanie do różnych typów aplikacji lub obciążeń.

Jedną z kluczowych zalet migracji do SQL Database jest to, że można przeprowadzić modernizację aplikacji, wykorzystując możliwości PaaS i eliminując każdą zależność od składników technicznych, które są objęte zakresem wystąpienia na przykład zadania programu SQL Agent.

Możesz również zaoszczędzić koszt przez Migrowanie SQL Server licencji lokalnych do Azure SQL Database przy użyciu [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) dla SQL Server należy wybrać [model zakupu oparty na rdzeń wirtualny](../../database/service-tiers-vcore.md).

Ten przewodnik ma na celu wyjaśnienie opcji migracji i zagadnień, które należy wykonać podczas przygotowywania do migracji baz danych SQL Server do Azure SQL Database.  

## <a name="considerations"></a>Zagadnienia do rozważenia 

Kluczowe czynniki do uwzględnienia podczas oceny opcji migracji zależą od: 

- Liczba serwerów i baz danych
- Rozmiar baz danych
- Akceptowalny przestój biznesowy podczas procesu migracji 

W przypadku opcji migracji wymienionych w tym przewodniku uwzględniono te czynniki. W przypadku migrowania danych logicznych do Azure SQL Database czas migracji może zależeć od liczby obiektów w bazie danych i rozmiaru bazy danych. 

Różne narzędzia są dostępne dla różnych obciążeń i preferencji użytkownika. Niektóre narzędzia mogą służyć do przeprowadzenia szybkiej migracji pojedynczej bazy danych przy użyciu narzędzia opartego na interfejsie użytkownika, a inne narzędzia mogą migrować wiele baz danych, które mogą być zautomatyzowane, aby obsługiwały migracje na dużą skalę. 

## <a name="choose-appropriate-target"></a>Wybierz odpowiedni element docelowy

Zapoznaj się z ogólnymi wskazówkami, które ułatwią wybór właściwego modelu wdrażania i warstwy usług Azure SQL Database. Podczas wdrażania można wybrać zasoby obliczeniowe i magazynowe, a następnie zmienić je później przy użyciu  [Azure Portal](../../database/scale-resources.md)  bez ponoszenia przestojów aplikacji.


**Modele wdrażania**: zrozumienie obciążenia aplikacji i wzorca użycia w celu podjęcia decyzji między jedną bazą danych lub pulą elastyczną. 

- [Pojedyncza baza danych](../../database/single-database-overview.md) reprezentuje w pełni zarządzaną bazę danych, która jest odpowiednia dla większości nowoczesnych aplikacji w chmurze i mikrousług.
- [Elastyczna Pula](../../database/elastic-pool-overview.md) to zbiór pojedynczych baz danych z współdzielonym zestawem zasobów, takich jak procesor CPU lub pamięć i odpowiednie do łączenia baz danych w puli z przewidywalnymi wzorcami użycia, które mogą efektywnie współużytkować ten sam zestaw zasobów.

**Modele zakupów**: wybór między modelem zakupu rdzeń wirtualny, DTU lub bezserwerowym. 

- [Model rdzeń wirtualny](../../database/service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych dla Azure SQL Database, co ułatwia wybór podczas tłumaczenia z SQL Server lokalnych. Jest to jedyna opcja, która obsługuje oszczędność kosztów licencji przy użyciu [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [Model DTU](../../database/service-tiers-dtu.md) służy do podzielenia zasobów obliczeniowych, pamięci i we/wy w celu zapewnienia mieszanych jednostek DTU. 
- [Model bezserwerowy](../../database/serverless-tier-overview.md) jest przeznaczony dla obciążeń, które wymagają automatycznego skalowania na żądanie z użyciem zasobów obliczeniowych rozliczonych na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych (w przypadku rozliczania tylko magazynu) i automatycznie wznawia bazy danych po powrocie działania. 

**Warstwy usług**: Wybierz spośród trzech warstw usług przeznaczonych dla różnych typów aplikacji.

- [Warstwa usługi ogólnego przeznaczenia/standardowa](../../database/service-tier-general-purpose.md) oferuje zrównoważoną, zorientowaną na budżetem opcję obliczeniową i magazynową, która umożliwia dostarczanie aplikacji warstwy średniej, z nadmiarowością wbudowaną w warstwę magazynowania w celu odzyskiwania po awarii. Zaprojektowana w przypadku większości obciążeń bazy danych. 
- [Warstwa usługi krytyczne dla działania firmy/Premium](../../database/service-tier-business-critical.md) jest dla aplikacji wysokiej warstwy, które wymagają wysokich stawek transakcji, operacji we/wy o małym opóźnieniu i wysokiego poziomu odporności z replikami pomocniczymi dostępnymi zarówno do trybu failover, jak i do odciążania obciążeń odczytu.
- [Warstwa usługi do skalowania](../../database/service-tier-hyperscale.md) jest dla baz danych, które mają rosnące ilości danych i muszą automatycznie skalować do 100 TB rozmiaru bazy danych. Zaprojektowana dla bardzo dużych baz danych. 

> [!IMPORTANT]
> [Stawka dziennika transakcji podlega](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) ograniczeniu Azure SQL Database w celu ograniczenia wysokich stawek za pozyskiwanie. W związku z tym podczas migracji może być konieczne skalowanie docelowych zasobów bazy danych (rdzeni wirtualnych/DTU) w celu łatwego naciskania na procesor lub przepływność. Wybierz docelową bazę danych o odpowiednim rozmiarze, ale Planuj skalowanie zasobów w górę w razie potrzeby migracji. 


### <a name="sql-server-on-azure-vm-alternative"></a>Alternatywa SQL Server na maszynie wirtualnej platformy Azure

Firma może mieć wymagania, które sprawiają, że [SQL Server na platformie Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) bardziej odpowiedni cel niż Azure SQL Database. 

Jeśli następujące kwestie dotyczą Twojej firmy, rozważ przejście do SQL Server na maszynie wirtualnej platformy Azure: 

- Jeśli wymagany jest bezpośredni dostęp do systemu operacyjnego lub systemu plików, na przykład w celu zainstalowania agentów innych firm lub niestandardowych na tej samej maszynie wirtualnej z SQL Server. 
- Jeśli masz ścisłą zależność od funkcji, które nadal nie są obsługiwane, takich jak transakcje typu FileStream/FileTable, wielopodstawowy i międzywystąpień. 
- W przypadku absolutnej konieczności pozostawania w określonej wersji SQL Server (na przykład 2012). 
- Jeśli wymagania dotyczące obliczeń są znacznie mniejsze niż oferty wystąpienia zarządzanego (jedna rdzeń wirtualny, na przykład), a konsolidacja bazy danych nie jest akceptowalną opcją. 


## <a name="migration-tools"></a>Narzędzia migracji 

Zalecane narzędzia do migracji to Data Migration Assistant i Azure Database Migration Service. Dostępne są również inne opcje migracji alternatywnej. 

### <a name="recommended-tools"></a>Zalecane narzędzia

Poniższa tabela zawiera listę zalecanych narzędzi migracji: 

|Technologia | Opis|
|---------|---------|
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | Azure Migrate usługi Azure SQL umożliwia odnajdywanie i ocenianie danych SQL w odpowiedniej skali w przypadku programu VMware, dostarczając zaleceń dotyczących wdrażania usługi Azure SQL, określanie rozmiaru docelowej i miesięczne oszacowania. | 
|[Narzędzie Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Data Migration Assistant to narzędzie pulpitu, które zapewnia bezproblemową ocenę SQL Server i migracji do Azure SQL Database (schemat i dane). Narzędzie można zainstalować lokalnie na serwerze lub na komputerze lokalnym, który ma łączność ze źródłowymi bazami danych. Proces migracji jest logicznym przenoszeniem danych między obiektami w źródłowej i docelowej bazie danych. </br> -Migruj pojedyncze bazy danych (schemat i dane)|
|[Usługa Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|Usługa platformy Azure dla pierwszej strony, która może migrować bazy danych SQL Server do Azure SQL Database przy użyciu Azure Portal lub zautomatyzowanego za pomocą programu PowerShell. Usługa Azure DMS wymaga wybrania preferowanej usługi Azure Virtual Network (VNet) podczas aprowizacji, aby upewnić się, że istnieje łączność ze źródłowymi bazami danych SQL Server. </br> — Migrowanie pojedynczych baz danych lub na dużą skalę. |
| | |


### <a name="alternative-tools"></a>Narzędzia alternatywne

W poniższej tabeli wymieniono alternatywne narzędzia migracji: 

|Technologia |Opis  |
|---------|---------|
|[Replikacja transakcyjna](../../database/replication-to-sql-database.md)|Replikowanie danych z tabel źródłowych SQL Server baz danych do SQL Database przez udostępnienie opcji migracji typu subskrybenta wydawcy podczas zachowywania spójności transakcyjnej. Przyrostowe zmiany danych są propagowane do subskrybentów w miarę ich występowania przez wydawców.|
|[Import usługi Export/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) to plik systemu Windows z `.bacpac` rozszerzeniem, które hermetyzuje schemat i dane bazy danych. BACPAC może służyć do eksportowania danych ze źródła SQL Server i importowania danych do Azure SQL Database. Plik BACPAC można zaimportować do nowego Azure SQL Database przy użyciu Azure Portal. </br></br> W przypadku skalowania i wydajności z dużymi bazami danych lub dużą liczbą baz danych należy rozważyć użycie narzędzia wiersza polecenia [sqlpackage](../../database/database-import.md#using-sqlpackage) do eksportowania i importowania baz danych.|
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Narzędzie do kopiowania masowego (bcp)](/sql/tools/bcp-utility) kopiuje dane z wystąpienia SQL Server do pliku danych. Użyj narzędzia BCP, aby wyeksportować dane ze źródła i zaimportować plik danych do SQL Database docelowej. </br></br> W celu uzyskania dużej szybkości operacji kopiowania zbiorczego do przenoszenia danych do Azure SQL Database [Narzędzie inteligentnego kopiowania masowego](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) może służyć do maksymalizowania szybkości transferu przez wykorzystanie równoległych zadań kopiowania.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|[Działanie kopiowania](../../../data-factory/copy-activity-overview.md) w Azure Data Factory migruje dane ze źródłowej bazy danych SQL Server do SQL Database przy użyciu wbudowanych łączników i [Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF obsługuje szeroką gamę [łączników](../../../data-factory/connector-overview.md) służących do przenoszenia danych ze źródeł SQL Server do SQL Database.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync to usługa oparta na Azure SQL Database, która pozwala synchronizować dane, które są wybierane dwukierunkowo dla wielu baz danych, zarówno lokalnie, jak i w chmurze.</br>Synchronizacja danych jest przydatna w przypadkach, gdy dane muszą być przechowywane w wielu bazach danych w Azure SQL Database lub SQL Server.|
| | |

## <a name="compare-migration-options"></a>Porównanie opcji migracji

Porównaj opcje migracji, aby wybrać ścieżkę odpowiednią do potrzeb Twojej firmy. 

### <a name="recommended-options"></a>Zalecane opcje

Poniższa tabela zawiera porównanie zalecanych opcji migracji: 

|Opcja migracji  |Kiedy stosować  |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Narzędzie Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | — Migrowanie pojedynczych baz danych (zarówno schematu, jak i danych).  </br> — Może obsłużyć przestoje podczas procesu migracji danych. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Działanie migracji wykonuje przenoszenie danych między obiektami bazy danych (ze źródła do miejsca docelowego) i dlatego zalecane do uruchamiania poza szczytem. </br> -DMA raport przedstawia stan migracji dla obiektu bazy danych, w tym liczbę migrowanych wierszy.  </br> — W przypadku dużych migracji (liczba baz danych/rozmiaru bazy danych) Użyj Azure Database Migration Service wymienionych poniżej.|
|[Usługa Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| — Migrowanie pojedynczych baz danych lub na dużą skalę. </br> — Może obsłużyć przestoje podczas procesu migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Migracje na dużą skalę można zautomatyzować za pomocą [programu PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> -Czas na zakończenie migracji zależy od rozmiaru bazy danych i liczby obiektów w bazie danych. </br> -Wymaga, aby źródłowa baza danych była ustawiona jako tylko do odczytu. |
| | | |

### <a name="alternative-options"></a>Opcje alternatywne

Poniższa tabela zawiera porównanie alternatywnych opcji migracji: 

|Metoda/technologia |Kiedy stosować    |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Replikacja transakcyjna](../../database/replication-to-sql-database.md)| — Migrowanie przez ciągłe publikowanie zmian z tabel źródłowych baz danych do docelowych tabel SQL Database. </br> -Pełna lub częściowa migracja baz danych wybranych tabel (podzbiór bazy danych).  </br> </br> Obsługiwane źródła: </br> - [SQL Server (2016 – 2019) z pewnymi ograniczeniami](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP COMPUTE SQL Server VM  | -Konfiguracja jest stosunkowo złożona w porównaniu z innymi opcjami migracji.   </br> — Udostępnia opcję ciągłej replikacji danych (bez przełączania baz danych w tryb offline).  </br> -Replikacja transakcyjna ma wiele ograniczeń, które należy wziąć pod uwagę podczas konfigurowania wydawcy na SQL Server źródłowej. Zobacz [ograniczenia dotyczące publikowania obiektów](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) , aby dowiedzieć się więcej. </br>— Istnieje możliwość [monitorowania aktywności replikacji](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Import usługi Export/BACPAC](../../database/database-import.md)| — Migrowanie poszczególnych baz danych aplikacji biznesowych. </br>— Odpowiednie dla mniejszych baz danych.  </br>  -Nie wymaga oddzielnej usługi lub narzędzia migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM  |  -Wymaga przestoju, ponieważ dane muszą zostać wyeksportowane w źródle i zaimportowane w miejscu docelowym.   </br> -Formaty plików i typy danych używane w eksportu/imporcie muszą być spójne ze schematami tabel, aby uniknąć błędów obcinania/niezgodności typów danych.  </br> — Czas poświęcony na Eksportowanie bazy danych o dużej liczbie obiektów może być znacznie wyższy.       |
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| — Migruj pełne lub częściowe migracje danych. </br> — Może obsłużyć przestoje. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM   | -Wymaga przestoju podczas eksportowania danych ze źródła i importowania ich do lokalizacji docelowej. </br> -Formaty plików i typy danych używane w eksportu/imporcie muszą być spójne ze schematami tabel. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| — Migrowanie i/lub przekształcanie danych z źródłowych SQL Server baz danych. </br> — Scalanie danych z wielu źródeł danych do Azure SQL Database zwykle dla obciążeń analizy biznesowej (BI).  |  -Wymaga utworzenia potoków przenoszenia danych w podajniku APD, aby przenieść dane ze źródła do miejsca docelowego.   </br> - [Koszt](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jest ważnym zagadnieniem i opiera się na wyzwalaczach potoku, uruchomieniach działań, czasie przenoszenia danych itp. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synchronizuj dane między źródłową i docelową bazą danych.</br> -Odpowiednie do uruchamiania ciągłej synchronizacji między Azure SQL Database i SQL Server lokalnych w przepływie dwukierunkowym. | -Azure SQL Database musi być bazą danych centrum do synchronizacji z usługą Premium SQL Server Database jako bazą danych elementu członkowskiego.</br> — W porównaniu z replikacją transakcyjną SQL Data Sync obsługuje dwukierunkową synchronizację danych między środowiskiem lokalnym i Azure SQL Database. </br> — Może mieć wyższy wpływ na wydajność, w zależności od obciążenia.|
| | | |

## <a name="feature-interoperability"></a>Współdziałanie funkcji 

Podczas migrowania obciążeń, które są zależne od innych funkcji SQL Server, istnieją dodatkowe zagadnienia.

#### <a name="sql-server-integration-services"></a>Usługi SQL Server Integration Services
Migruj pakiety SQL Server Integration Services (SSIS) na platformę Azure, wdrażając pakiety w środowisku uruchomieniowym Azure-SSIS w [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [obsługuje migrację pakietów SSIS](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) , zapewniając środowisko uruchomieniowe stworzone do wykonywania pakietów SSIS na platformie Azure. Alternatywnie można także ponownie napisać logikę ETL programu SSIS w sposób natywny w module ADF przy użyciu [dataflows](../../../data-factory/concepts-data-flow-overview.md).


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrowanie raportów usługi SQL Server Reporting Services (SSRS) do raportów z podziałem na strony w programie Power BI. Użyj [Narzędzia do migracji RDL](https://github.com/microsoft/RdlMigration) , aby pomóc w przygotowaniu i migracji raportów. To narzędzie zostało opracowane przez firmę Microsoft, aby pomóc klientom w migracji raportów języka RDL z serwerów usług SSRS do usługi Power BI. Jest on dostępny w witrynie GitHub i zawiera kompleksowy przewodnik dotyczący scenariusza migracji. 

#### <a name="high-availability"></a>Wysoka dostępność
Konfiguracja ręczna SQL Server funkcji wysokiej dostępności, takich jak zawsze włączone wystąpienia klastra trybu failover i zawsze włączone grupy dostępności, staje się przestarzała w docelowym Azure SQL Database, ponieważ architektura wysokiej dostępności została już wbudowana w [ogólnego przeznaczenia (model dostępności standardowa)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) i [krytyczne dla działania firmy (model dostępności Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Database. Warstwa usługi Krytyczne dla działania firmy/Premium zapewnia również skalowalność w poziomie, która umożliwia łączenie się z jednym z węzłów pomocniczych na potrzeby tylko do odczytu. 

Poza architekturą wysokiej dostępności, która jest uwzględniona w SQL Database, istnieje również funkcja [grup autopracy awaryjnej](../../database/auto-failover-group-overview.md) , która umożliwia zarządzanie replikacją i trybem failover baz danych w wystąpieniu zarządzanym w innym regionie. 

#### <a name="sql-agent-jobs"></a>Zadania programu SQL Agent
Zadania programu SQL Agent nie są bezpośrednio obsługiwane w Azure SQL Database i muszą zostać wdrożone w celu [Elastic Database zadań (wersja zapoznawcza)](../../database/job-automation-overview.md).

#### <a name="logins-and-groups"></a>Nazwy logowania i grupy
Przenieś nazwy logowania SQL ze źródła SQL Server do Azure SQL Database przy użyciu Database Migration Service (DMS) w trybie offline.  Użyj **wybranego bloku logowania** w **Kreatorze migracji** w celu migrowania logowań do SQL Database docelowej. 

Użytkowników i grup systemu Windows można również migrować za pomocą usługi DMS, włączając odpowiedni przycisk przełączania na stronie konfiguracji DMS. 

Alternatywnie można użyć [Narzędzia Narzędzia PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) specjalnie zaprojektowanego przez architektów migracji danych firmy Microsoft. Narzędzie korzysta z programu PowerShell do tworzenia skryptu Transact-SQL (T-SQL) w celu ponownego tworzenia logowań i wybierania użytkowników bazy danych z lokalizacji źródłowej do docelowej. Narzędzie automatycznie mapuje konta usługi Windows AD na konta usługi Azure AD i może wykonać wyszukiwanie nazwy UPN dla każdej nazwy logowania w Active Directory źródłowej. Narzędzie skryptuje niestandardowe role serwera i bazy danych, a także członkostwo ról, rolę bazy danych i uprawnienia użytkownika. Zawarte bazy danych nie są jeszcze obsługiwane i tylko podzbiór możliwych uprawnień SQL Server są skryptami. 


#### <a name="system-databases"></a>Systemowe bazy danych
W przypadku Azure SQL Database jedynymi odpowiednimi bazami danych są systemy [Master](/sql/relational-databases/databases/master-database) i tempdb. Aby dowiedzieć się więcej, zobacz [tempdb w Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Korzystanie z zaawansowanych funkcji 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze oferowanych przez SQL Database. Na przykład nie trzeba już martwić się o zarządzanie kopiami zapasowymi, gdy usługa go wykonuje. W okresie przechowywania można przywrócić wszystkie [punkty w czasie](../../database/recovery-using-backups.md#point-in-time-restore). 

Aby zwiększyć bezpieczeństwo, należy rozważyć użycie [uwierzytelniania Azure Active Directory](../../database/authentication-aad-overview.md), [inspekcji](../../database/auditing-overview.md), [wykrywania zagrożeń](../../database/azure-defender-for-sql.md), [zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security)i [dynamicznego maskowania danych](/sql/relational-databases/security/dynamic-data-masking).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń SQL Database udostępnia zestaw zaawansowanych narzędzi, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). [Azure SQL Analytics (wersja zapoznawcza)](../../../azure-monitor/insights/azure-sql.md) to zaawansowane rozwiązanie do monitorowania chmurowego służące do monitorowania wydajności wszystkich baz danych w Azure SQL Database na dużą skalę i w wielu subskrypcjach w jednym widoku. Azure SQL Analytics gromadzi i wizualizuje kluczowe metryki wydajności dzięki wbudowanej analizie na potrzeby rozwiązywania problemów z wydajnością.

[Dostrajanie automatyczne](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   ciągle monitoruje wydajność statystyk planu wykonywania SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością. 


## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane dla rzeczywistych projektów migracji.

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Narzędzie do ładowania](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Za pomocą usługi dbloader można ładować dane z rozdzielanych plików tekstowych do SQL Server. To narzędzie konsoli systemu Windows korzysta z SQL Server natywnego interfejsu klienta bulkload, który działa na wszystkich wersjach SQL Server, w tym Azure SQL Database.|
|[Tworzenie zbiorczych baz danych przy użyciu programu PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Obejmuje to zestaw trzech skryptów programu PowerShell, które tworzą grupę zasobów (create_rg.ps1), [serwer logiczny na platformie Azure](../../database/logical-servers.md) (create_sqlserver.ps1) i Azure SQL Database (create_sqldb.ps1). Skrypty obejmują funkcje pętli, dzięki czemu można wykonać iterację i utworzyć dowolną liczbę serwerów i baz danych.|
|[Wdrażanie schematu zbiorczego za pomocą programu MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Ten element zawartości tworzy grupę zasobów, jeden lub wiele [serwerów logicznych na platformie Azure](../../database/logical-servers.md) do hostowania Azure SQL Database, eksportuje każdy schemat z SQL Server lokalnych (lub wielu serwerów SQL Server (2005 +) i importuje go do Azure SQL Database.|
|[Konwertowanie zadań agenta SQL Server na zadania Elastic Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Ten skrypt migruje zadania agenta SQL Server źródłowego do Elastic Database zadań.|
|[Wyślij wiadomości z Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Rozwiązanie to stanowi alternatywę dla możliwości SendMail, która jest dostępna w SQL Server lokalnych. Rozwiązanie używa Azure Functions i usługi Azure SendGrid do wysyłania wiadomości e-mail z Azure SQL Database.|
|[Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skrypt programu PowerShell, który tworzy skrypt poleceń T-SQL, aby ponownie tworzyć logowania i wybierać użytkowników bazy danych z SQL Server lokalnych do Azure SQL Database. Narzędzie umożliwia automatyczne mapowanie kont usługi Windows AD na konta usługi Azure AD, a także możliwość opcjonalnej migracji SQL Server natywnych logowań.|
|[Automatyzacja zbierania danych monitora wydajności za pomocą narzędzia Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Narzędzie, które zbiera dane PerMon w celu zrozumienia wydajności linii bazowej i pomaga w zaleceń dotyczących docelowego migracji. To narzędzie używa logman.exe, aby utworzyć polecenie, które będzie tworzyć, uruchamiać, zatrzymywać i usuwać liczniki wydajności ustawione na SQL Server zdalnej|
|[Oficjalny dokument — migracja bazy danych do usługi Azure SQL DB przy użyciu BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Niniejszy dokument zawiera wskazówki i kroki ułatwiające przyspieszenie migracji z SQL Server do Azure SQL Database przy użyciu plików BACPAC.|

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.


## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację SQL Server do Azure SQL Database, zapoznaj się z [przewodnikiem migracji SQL Server do SQL Database](sql-server-to-sql-database-guide.md).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat SQL Database, zobacz:
   - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).