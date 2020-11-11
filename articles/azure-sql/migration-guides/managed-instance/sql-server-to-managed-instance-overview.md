---
title: SQL Server do wystąpienia zarządzanego SQL — Omówienie migracji
description: Dowiedz się więcej na temat różnych narzędzi i opcji dostępnych do migrowania baz danych SQL Server do wystąpienia zarządzanego usługi Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 211ad590ab01d0be26d799064e1227accc619585
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497365"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>Omówienie migracji: SQL Server do wystąpienia zarządzanego SQL
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Zapoznaj się z różnymi opcjami migracji i zagadnieniami dotyczącymi migracji SQL Server do wystąpienia zarządzanego Azure SQL. 

Możesz migrować SQL Server uruchomione lokalnie lub na: 

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)  
- SQL w chmurze dla SQL Server (Google Cloud Platform – GCP) 

Inne scenariusze można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Omówienie

[Wystąpienie zarządzane usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md) to zalecana opcja docelowa dla obciążeń SQL Server, które wymagają w pełni zarządzanej usługi bez konieczności zarządzania maszynami wirtualnymi ani ich systemami operacyjnymi. Wystąpienie zarządzane SQL umożliwia podniesienie i przesunięcia aplikacji lokalnych na platformę Azure z minimalnymi zmianami aplikacji lub baz danych przy jednoczesnym przeprowadzeniu pełnej izolacji wystąpień z natywną obsługą sieci wirtualnej (VNet). 

## <a name="considerations"></a>Zagadnienia do rozważenia 

Kluczowe czynniki do uwzględnienia podczas oceny opcji migracji zależą od: 
- Liczba serwerów i baz danych
- Rozmiar baz danych
- Akceptowalny przestój biznesowy podczas procesu migracji 

Jedną z kluczowych zalet migrowania serwerów SQL do wystąpienia zarządzanego SQL jest możliwość migracji całego wystąpienia lub tylko podzbioru pojedynczych baz danych. Uważnie Zaplanuj, aby uwzględnić następujące elementy w procesie migracji: 
- Wszystkie bazy danych, które muszą być umieszczone w tym samym wystąpieniu 
- Obiekty na poziomie wystąpienia wymagane dla aplikacji, w tym logowania, poświadczenia, zadania i operatory programu SQL Agent oraz wyzwalacze na poziomie serwera. 

> [!NOTE]
> Wystąpienie zarządzane Azure SQL gwarantuje dostępność na 99,99% nawet w przypadku krytycznych scenariuszy, więc obciążenie spowodowane przez niektóre funkcje w programie SQL nie mogą być wyłączone. Aby uzyskać więcej informacji, zobacz [główne przyczyny, które mogą spowodować różne wydajności na blogu SQL Server i wystąpienia zarządzanego usługi Azure SQL](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) . 


## <a name="choose-appropriate-target"></a>Wybierz odpowiedni element docelowy

Niektóre ogólne wytyczne ułatwiające wybranie odpowiedniej warstwy usług i cech wystąpienia zarządzanego SQL, aby ułatwić dopasowanie do [linii bazowej wydajności](sql-server-to-managed-instance-performance-baseline.md): 

- Użyj linii bazowej użycia procesora CPU, aby zainicjować wystąpienie zarządzane, które jest zgodne z liczbą rdzeni używanych przez wystąpienie SQL Server. Może być konieczne skalowanie zasobów, aby odpowiadały [charakterystyce generacji sprzętu](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Użyj linii bazowej użycia pamięci, aby wybrać [opcję rdzeń wirtualny](../../managed-instance/resource-limits.md#service-tier-characteristics) , która odpowiednio pasuje do przydzielonej pamięci. 
- Użyj czasu oczekiwania operacji we/wy dla podsystemu plików, aby wybrać między Ogólnego przeznaczenia (opóźnienie większe niż 5 ms) i Krytyczne dla działania firmy (opóźnienie mniejsze niż 3 ms). 
- Użyj przepływności linii bazowej do wstępnego przydzielenia rozmiaru plików danych i dziennika, aby osiągnąć oczekiwaną wydajność operacji we/wy. 

Podczas wdrażania można wybrać zasoby obliczeniowe i magazynowe, a następnie zmienić je po użyciu [Azure Portal](../../database/scale-resources.md) bez ponoszenia przestojów aplikacji. 

> [!IMPORTANT]
> Wszelkie niezgodności w [wymaganiach dotyczących sieci wirtualnej wystąpienia zarządzanego](/../../managed-instance/connectivity-architecture-overview.md#network-requirements) mogą uniemożliwić tworzenie nowych wystąpień lub korzystanie z istniejących. Dowiedz się więcej na temat [tworzenia nowych](/../../managed-instance/virtual-network-subnet-create-arm-template?branch=release-ignite-arc-data)   i [konfigurowania istniejących](/../../managed-instance/vnet-existing-add-subnet?branch=release-ignite-arc-data)   sieci. 

### <a name="sql-server-vm-alternative"></a>Alternatywa maszyny wirtualnej SQL Server

Firma może mieć wymagania, które sprawiają, że SQL Server na maszynach wirtualnych platformy Azure jest bardziej odpowiedni cel niż wystąpienie zarządzane usługi Azure SQL. 

Jeśli następujące kwestie mają zastosowanie do Twojej firmy, rozważ przechodzenie do SQL Server maszyny wirtualnej: 

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
|[Usługa Azure Database Migration Service (DMS)](/azure/dms/tutorial-sql-server-to-managed-instance)  | Usługa platformy Azure dla pierwszej firmy, która obsługuje migrację w trybie offline dla aplikacji, które mogą spowodować przestoje podczas procesu migracji. W przeciwieństwie do ciągłej migracji w trybie online, migracja w trybie offline uruchamia jednorazowe Przywracanie pełnej kopii zapasowej bazy danych ze źródła do obiektu docelowego. | 
|[Natywna kopia zapasowa i przywracanie](../../managed-instance/restore-sample-database-quickstart.md) | Wystąpienie zarządzane SQL obsługuje przywracanie natywnych kopii zapasowych bazy danych SQL Server (pliki. bak), co sprawia, że jest to najprostsza opcja migracji dla klientów, którzy mogą udostępniać pełne kopie zapasowe bazy danych w usłudze Azure Storage. Pełne i różnicowe kopie zapasowe są również obsługiwane i udokumentowane w [sekcji zasobów migracji](#migration-assets) w dalszej części tego artykułu.| 
| | |

### <a name="alternative-tools"></a>Narzędzia alternatywne

W poniższej tabeli wymieniono alternatywne narzędzia migracji: 

|Technologia |Opis  |
|---------|---------|
|[Replikacja transakcyjna](../../managed-instance/replication-transactional-overview.md) | Replikowanie danych z tabel źródłowych SQL Server baz danych do wystąpienia zarządzanego SQL przez udostępnienie opcji migracji typu subskrybenta wydawcy podczas zachowywania spójności transakcyjnej. |  |
|[Kopia Zbiorcza](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Narzędzie do kopiowania masowego (bcp)](/sql/tools/bcp-utility) kopiuje dane z wystąpienia SQL Server do pliku danych. Wyeksportuj dane ze źródła przy użyciu narzędzia BCP i zaimportuj plik danych do docelowego wystąpienia zarządzanego SQL.</br></br> W przypadku dużych operacji kopiowania zbiorczego do przenoszenia danych do Azure SQL Database [Narzędzie inteligentnego kopiowania masowego](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) może służyć do maksymalizowania szybkości transferu przez wykorzystanie równoległych zadań kopiowania. | 
|[Kreator importu eksportu/BACPAC](/azure/azure-sql/database/database-import?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) to plik systemu Windows z `.bacpac` rozszerzeniem, które hermetyzuje schemat i dane bazy danych. BACPAC może służyć do eksportowania danych ze źródła SQL Server i do zaimportowania pliku z powrotem do wystąpienia zarządzanego Azure SQL.  |  
|[Azure Data Factory (ADF)](/azure/data-factory/connector-azure-sql-managed-instance)| [Działanie kopiowania](/azure/data-factory/copy-activity-overview) w programie Azure Data Factory migruje dane ze źródłowej bazy danych SQL Server do wystąpienia zarządzanego SQL przy użyciu wbudowanych łączników i [Integration Runtime](/azure/data-factory/concepts-integration-runtime).</br> </br> ADF obsługuje szeroką gamę [łączników](/azure/data-factory/connector-overview) służących do przenoszenia danych ze źródeł SQL Server do wystąpienia zarządzanego SQL. |
| | |

## <a name="compare-migration-options"></a>Porównanie opcji migracji

Porównaj opcje migracji, aby wybrać ścieżkę odpowiednią do potrzeb Twojej firmy. 

### <a name="recommended-options"></a>Zalecane opcje

Poniższa tabela zawiera porównanie zalecanych opcji migracji: 

|Opcja migracji  |Kiedy stosować  |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Usługa Azure Database Migration Service (DMS)](/azure/dms/tutorial-sql-server-to-managed-instance) | — Migruj pojedyncze bazy danych lub wiele baz danych na dużą skalę. </br> — Może obsłużyć przestoje podczas procesu migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM |  — Migracje na dużą skalę można zautomatyzować za pomocą [programu PowerShell](/azure/dms/howto-sql-server-to-azure-sql-mi-powershell). </br> -Czas na zakończenie migracji zależy od rozmiaru bazy danych i wpływu na czas wykonywania kopii zapasowej i przywracania. </br> -Konieczne może być wystarczające przestoje. |
|[Natywna kopia zapasowa i przywracanie](../../managed-instance/restore-sample-database-quickstart.md) | — Migrowanie poszczególnych baz danych aplikacji biznesowych.  </br> — Szybka i łatwa migracja bez oddzielnej usługi lub narzędzia migracji.  </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Kopia zapasowa bazy danych używa wielu wątków do optymalizowania transferu danych do usługi Azure Blob Storage, ale przepustowość niezależnego dostawcy oprogramowania i rozmiar bazy danych mogą mieć wpływ na szybkość transferu </br> -Przestoje powinny pomieścić czas wymagany do wykonania pełnej kopii zapasowej i przywracania (czyli rozmiaru operacji na danych).| 
| | | |

### <a name="alternative-options"></a>Opcje alternatywne

Poniższa tabela zawiera porównanie alternatywnych opcji migracji: 

|Metoda/technologia |Kiedy stosować |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Replikacja transakcyjna](../../managed-instance/replication-transactional-overview.md) | — Migrowanie przez ciągłe publikowanie zmian z tabel źródłowych baz danych do docelowych tabel bazy danych wystąpienia zarządzanego SQL. </br> -Pełna lub częściowa migracja baz danych wybranych tabel (podzbiór bazy danych).  </br> </br> Obsługiwane źródła: </br> -SQL Server (2012 – 2019) z pewnymi ograniczeniami </br> -AWS EC2  </br> -GCP COMPUTE SQL Server VM | </br> -Konfiguracja jest stosunkowo złożona w porównaniu z innymi opcjami migracji.   </br> — Udostępnia opcję ciągłej replikacji danych (bez przełączania baz danych w tryb offline).</br> -Replikacja transakcyjna ma wiele ograniczeń, które należy wziąć pod uwagę podczas konfigurowania wydawcy na SQL Server źródłowej. Zobacz [ograniczenia dotyczące publikowania obiektów](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) , aby dowiedzieć się więcej.  </br> — Możliwość [monitorowania aktywności replikacji](/sql/relational-databases/replication/monitor/monitoring-replication) jest dostępna.    |
|[Kopia Zbiorcza](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| — Migrowanie pełnych lub częściowych migracji danych. </br> — Może obsłużyć przestoje. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM   | -Wymaga przestoju podczas eksportowania danych ze źródła i importowania ich do lokalizacji docelowej. </br> -Formaty plików i typy danych używane w eksportu/imporcie muszą być spójne ze schematami tabel. |
|[Kreator importu eksportu/BACPAC](/azure/azure-sql/database/database-import)| — Migrowanie poszczególnych baz danych aplikacji biznesowych. </br>— Odpowiednie dla mniejszych baz danych.  </br>  Nie wymaga osobnej usługi lub narzędzia migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 – 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM  |   </br> -Wymaga przestoju, ponieważ dane muszą zostać wyeksportowane w źródle i zaimportowane w miejscu docelowym.   </br> -Formaty plików i typy danych używane w eksportu/imporcie muszą być spójne ze schematami tabel, aby uniknąć błędów obcinania/niezgodności typów danych. </br> — Czas poświęcony na Eksportowanie bazy danych o dużej liczbie obiektów może być znacznie wyższy. |
|[Azure Data Factory (ADF)](/azure/data-factory/connector-azure-sql-managed-instance)| — Migrowanie i/lub przekształcanie danych z źródłowych SQL Server baz danych.</br> — Scalanie danych z wielu źródeł danych do wystąpienia zarządzanego usługi Azure SQL zwykle dla obciążeń analizy biznesowej.   </br> -Wymaga utworzenia potoków przenoszenia danych w podajniku APD, aby przenieść dane ze źródła do miejsca docelowego.   </br> - [Koszt](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jest ważnym zagadnieniem i opiera się na wyzwalaczach potoku, uruchomieniach działań, czasie przenoszenia danych itp. |
| | | |

## <a name="feature-interoperability"></a>Współdziałanie funkcji 

Podczas migrowania obciążeń, które są zależne od innych funkcji SQL Server, istnieją dodatkowe zagadnienia. 

#### <a name="sql-server-integration-services"></a>Usługi SQL Server Integration Services

Migruj pakiety i projekty SQL Server Integration Services (SSIS) w SSISDB do wystąpienia zarządzanego usługi Azure SQL przy użyciu [Azure Database Migration Service (DMS)](/azure/dms/how-to-migrate-ssis-packages-managed-instance). 

Migracja jest obsługiwana tylko w przypadku pakietów SSIS w SSISDB począwszy od SQL Server 2012. Przekonwertuj starsze pakiety usług SSIS przed migracją. Aby dowiedzieć się więcej, zobacz [Samouczek dotyczący konwersji projektu](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Raporty usługi SQL Server Reporting Services (SSRS) można migrować do raportów z podziałem na strony w programie Power BI. Użyj [Narzędzia do migracji RDL](https://github.com/microsoft/RdlMigration) , aby pomóc w przygotowaniu i migracji raportów. To narzędzie zostało opracowane przez firmę Microsoft, aby pomóc klientom w migracji raportów języka RDL z serwerów usług SSRS do usługi Power BI. Jest on dostępny w witrynie GitHub i zawiera kompleksowy przewodnik dotyczący scenariusza migracji. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services modele tabelaryczne z SQL Server 2012 i nowszych można migrować do Azure Analysis Services, który jest modelem wdrażania PaaS dla Analysis Services modelu tabelarycznego na platformie Azure. Więcej informacji na temat migrowania modeli Premium do Azure Analysis Services można znaleźć w tym [samouczku wideo](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Alternatywnie można również rozważyć Migrowanie lokalnych modeli Analysis Services modele tabelaryczne do [Power BI Premium przy użyciu nowych punktów końcowych odczytu/zapisu XMLA](https://docs.microsoft.com/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Funkcje punktów końcowych odczytu/zapisu Power BI XMLA są obecnie dostępne w publicznej wersji zapoznawczej i nie powinny być brane pod uwagę w przypadku obciążeń produkcyjnych, dopóki funkcjonalność nie będzie ogólnie dostępna.

#### <a name="high-availability"></a>Wysoka dostępność

Funkcje wysokiej dostępności SQL Server zawsze włączone wystąpienia klastra trybu failover i zawsze włączone grupy dostępności staną się przestarzałe w docelowym wystąpieniu zarządzanym Azure SQL, ponieważ architektura wysokiej dostępności została już wbudowana w [ogólnego przeznaczenia (model dostępności standard)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) i [krytyczne dla działania firmy (model dostępności Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) wystąpienie zarządzane SQL. Model dostępności Premium zapewnia również skalowalność w poziomie, która umożliwia łączenie się z jednym z węzłów pomocniczych w celach tylko do odczytu.     

Poza architekturą wysokiej dostępności, która jest uwzględniona w wystąpieniu zarządzanym SQL, istnieje również funkcja [grup autopracy awaryjnej](../../database/auto-failover-group-overview.md) , która umożliwia zarządzanie replikacją i trybem failover baz danych w wystąpieniu zarządzanym w innym regionie. 

#### <a name="sql-agent-jobs"></a>Zadania programu SQL Agent

Użyj opcji Azure Database Migration Service w trybie offline (DMS), aby przeprowadzić migrację [zadań programu SQL Agent](/azure/dms/howto-sql-server-to-azure-sql-mi-powershell#offline-migrations). W przeciwnym razie skryptuje zadania w języku Transact-SQL (T-SQL) przy użyciu SQL Server Management Studio a następnie ręcznie je utworzyć w docelowym wystąpieniu zarządzanym SQL. 

> [!IMPORTANT]
> Obecnie usługa Azure DMS obsługuje tylko zadania z podsystemem T-SQL. Zadania z krokami pakietu SSIS będą musiały zostać przemigrowane ręcznie. 

#### <a name="logins-and-groups"></a>Nazwy logowania i grupy

Logowania SQL z SQL Server źródłowych można przenieść do wystąpienia zarządzanego Azure SQL przy użyciu Database Migration Service (DMS) w trybie offline.  Aby przeprowadzić migrację logowań do docelowego wystąpienia zarządzanego SQL, użyj bloku **[Wybieranie nazw logowania](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** w **Kreatorze migracji** . 

Domyślnie Azure Database Migration Service obsługuje tylko Migrowanie nazw logowania SQL. Można jednak włączyć możliwość migracji nazw logowania systemu Windows przez:

Upewnienie się, że docelowe wystąpienie zarządzane SQL ma dostęp do odczytu usługi Azure AD, który można skonfigurować za pośrednictwem Azure Portal przez użytkownika z rolą **administratora firmy** lub **administratora globalnego**.
Konfigurowanie wystąpienia Azure Database Migration Service, aby umożliwić migrację logowania użytkownika/grupy systemu Windows, która jest konfigurowana za pośrednictwem Azure Portal na stronie Konfiguracja. Po włączeniu tego ustawienia należy ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

Po ponownym uruchomieniu usługi, logowania użytkownika/grupy systemu Windows są wyświetlane na liście nazw logowania dostępnych do migracji. W przypadku wszystkich migrowanych kont użytkowników/grup systemu Windows zostanie wyświetlony monit o podanie skojarzonej nazwy domeny. Konta użytkowników usługi (konto z nazwą domeny NT AUTHORITY) i wirtualne konta użytkowników (nazwa konta z usługą nazw domen NT) nie są obsługiwane.

Aby dowiedzieć się więcej, zobacz [jak migrować użytkowników i grupy systemu Windows w wystąpieniu SQL Server do wystąpienia zarządzanego usługi Azure SQL przy użyciu języka T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternatywnie można użyć [Narzędzia Narzędzia PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) specjalnie zaprojektowanego przez architektów migracji danych firmy Microsoft. Narzędzie używa programu PowerShell do utworzenia skryptu T-SQL w celu ponownego utworzenia nazw logowania i wybrania użytkowników bazy danych ze źródła do obiektu docelowego. Narzędzie automatycznie mapuje konta usługi Windows AD na konta usługi Azure AD i może wykonać wyszukiwanie nazwy UPN dla każdej nazwy logowania w Active Directory źródłowej. Narzędzie skryptuje niestandardowe role serwera i bazy danych, a także członkostwo ról, rolę bazy danych i uprawnienia użytkownika. Zawarte bazy danych nie są obecnie obsługiwane i tylko podzbiór możliwych uprawnień SQL Server są skryptami. 

#### <a name="encryption"></a>Szyfrowanie

Podczas migrowania baz danych chronionych przez  [transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   do wystąpienia zarządzanego przy użyciu opcji przywracania natywnego należy [migrować odpowiedni certyfikat](../../managed-instance/tde-certificate-migrate.md) z SQL Server źródłowych do docelowego wystąpienia zarządzanego SQL *przed* przywróceniem bazy danych. 

#### <a name="system-databases"></a>Systemowe bazy danych

Przywracanie systemowych baz danych nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywanych w bazach danych Master lub msdb), zaskryptowanie ich przy użyciu języka Transact-SQL (T-SQL), a następnie utwórz je ponownie w docelowym wystąpieniu zarządzanym. 

## <a name="leverage-advanced-features"></a>Korzystanie z zaawansowanych funkcji 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze oferowanych przez wystąpienie zarządzane SQL. Na przykład nie trzeba już martwić się o zarządzanie kopiami zapasowymi, gdy usługa go wykonuje. W okresie przechowywania można przywrócić wszystkie [punkty w czasie](../../database/recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba martwić się o skonfigurowanie wysokiej dostępności, ponieważ [wysoka dostępność jest wbudowana](../../database/high-availability-sla.md). 

Aby zwiększyć bezpieczeństwo, należy rozważyć użycie [uwierzytelniania Azure Active Directory](../../database/authentication-aad-overview.md), [inspekcji](../../managed-instance/auditing-configure.md), [wykrywania zagrożeń](../../database/advanced-data-security.md), [zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security)i [dynamicznego maskowania danych](/sql/relational-databases/security/dynamic-data-masking).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń wystąpienie zarządzane SQL udostępnia zestaw zaawansowanych narzędzi, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych w sposób scentralizowany.  [Dostrajanie automatyczne](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   w zarządzanych wystąpieniach program ciągle monitoruje wydajność statystyk wykonywania planu SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością. 

Niektóre funkcje są dostępne tylko wtedy, gdy [poziom zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) zostanie zmieniony na najnowszy poziom zgodności (150). 

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane dla rzeczywistych projektów migracji.

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Narzędzie do ładowania](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Za pomocą usługi dbloader można ładować dane z rozdzielanych plików tekstowych do SQL Server. To narzędzie konsoli systemu Windows korzysta z SQL Server natywnego interfejsu klienta bulkload, który działa na wszystkich wersjach SQL Server, w tym Azure SQL MI.|
|[Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do wystąpienia zarządzanego usługi Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skrypt programu PowerShell, który tworzy skrypt poleceń T-SQL, aby ponownie tworzyć logowania i wybierać użytkowników bazy danych z SQL Server lokalnych do wystąpienia zarządzanego Azure SQL. Narzędzie umożliwia automatyczne mapowanie kont usługi Windows AD na konta usługi Azure AD, a także możliwość opcjonalnej migracji SQL Server natywnych logowań.|
|[Automatyzacja zbierania danych monitora wydajności za pomocą narzędzia Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Narzędzie służące do zbierania danych w celu zrozumienia wydajności linii bazowej, która pomaga w rekomendacji docelowej migracji. To narzędzie używające logman.exe do tworzenia polecenia, które będzie tworzyć, uruchamiać, zatrzymywać i usuwać liczniki wydajności ustawione na SQL Server zdalnej.|
|[Oficjalny dokument — migracja bazy danych do wystąpienia zarządzanego usługi Azure SQL przez przywrócenie pełnych i różnicowych kopii zapasowych](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Niniejszy dokument zawiera wskazówki i kroki ułatwiające przyspieszenie migracji z SQL Server do wystąpienia zarządzanego usługi Azure SQL, jeśli masz tylko pełne i różnicowe kopie zapasowe (bez możliwości tworzenia kopii zapasowych dzienników).|

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.


## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację SQL Server do wystąpienia zarządzanego usługi Azure SQL, zapoznaj się z [przewodnikiem migracji wystąpień zarządzanych SQL Server do bazy danych SQL](sql-server-to-managed-instance-guide.md).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat wystąpienia zarządzanego Azure SQL, zobacz:
   - [Warstwy usług w wystąpieniu zarządzanym usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Różnice między SQL Server i wystąpieniem zarządzanym usługi Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
