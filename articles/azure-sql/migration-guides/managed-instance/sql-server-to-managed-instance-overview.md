---
title: 'SQL Server do wystąpienia zarządzanego SQL: Przegląd migracji'
description: Dowiedz się więcej na temat narzędzi i opcji dostępnych do migrowania baz danych SQL Server do wystąpienia zarządzanego usługi Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078982"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Omówienie migracji: SQL Server do wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Poznaj opcje i zagadnienia dotyczące migrowania baz danych SQL Server do wystąpienia zarządzanego usługi Azure SQL. 

Możliwe jest Migrowanie SQL Server baz danych działających lokalnie lub na: 

- SQL Server w usłudze Azure Virtual Machines.  
- Amazon Web Services (AWS) elastyczna chmura obliczeniowa (EC2). 
- Usługa relacyjnej bazy danych AWS (RDS). 
- Aparat obliczeniowy w Google Cloud Platform (GCP).  
- SQL w chmurze dla SQL Server w GCP. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Omówienie

[Wystąpienie zarządzane usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md) to zalecana opcja docelowa dla obciążeń SQL Server, które wymagają w pełni zarządzanej usługi bez konieczności zarządzania maszynami wirtualnymi ani ich systemami operacyjnymi. Wystąpienie zarządzane SQL umożliwia przenoszenie aplikacji lokalnych na platformę Azure przy minimalnych zmianach aplikacji lub baz danych. Oferuje kompletną izolację wystąpień z natywną obsługą sieci wirtualnej. 

## <a name="considerations"></a>Zagadnienia do rozważenia 

Kluczowe czynniki do uwzględnienia podczas oceniania opcji migracji są następujące: 
- Liczba serwerów i baz danych
- Rozmiar baz danych
- Akceptowalny przestój biznesowy podczas procesu migracji 

Jedną z kluczowych zalet migrowania baz danych SQL Server do wystąpienia zarządzanego SQL jest możliwość migrowania całego wystąpienia lub tylko podzbioru pojedynczych baz danych. Uważnie Zaplanuj, aby uwzględnić następujące elementy w procesie migracji: 
- Wszystkie bazy danych, które muszą być umieszczone w tym samym wystąpieniu 
- Obiekty na poziomie wystąpienia wymagane dla aplikacji, w tym logowania, poświadczenia, zadania i operatory programu SQL Agent oraz wyzwalacze na poziomie serwera 

> [!NOTE]
> Wystąpienie zarządzane Azure SQL gwarantuje dostępność na 99,99% nawet w przypadku krytycznych scenariuszy. Nie można wyłączyć narzutów spowodowanych przez niektóre funkcje w wystąpieniu zarządzanym SQL. Aby uzyskać więcej informacji, zobacz [najważniejsze przyczyny różnic wydajności między wystąpieniem zarządzanym SQL i](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) wpisem w blogu SQL Server. 


## <a name="choose-an-appropriate-target"></a>Wybierz odpowiedni element docelowy

Poniższe ogólne wytyczne mogą pomóc w wyborze odpowiedniej warstwy usług i cech wystąpienia zarządzanego SQL, aby ułatwić dopasowanie do [linii bazowej wydajności](sql-server-to-managed-instance-performance-baseline.md): 

- Użyj linii bazowej użycia procesora CPU, aby zainicjować wystąpienie zarządzane, które jest zgodne z liczbą rdzeni, których używa wystąpienie SQL Server. Może być konieczne skalowanie zasobów, aby odpowiadały [charakterystyce generacji sprzętu](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Użyj linii bazowej użycia pamięci, aby wybrać [opcję rdzeń wirtualny](../../managed-instance/resource-limits.md#service-tier-characteristics) , która odpowiednio pasuje do przydzielonej pamięci. 
- Użyj czasu oczekiwania we/wy dla podsystemu plików, aby wybrać między Ogólnego przeznaczenia (opóźnienie większe niż 5 ms) i Krytyczne dla działania firmy (opóźnienie mniejsze niż 3 ms). 
- Użyj przepływności linii bazowej do wstępnego przydzielenia rozmiaru plików danych i dziennika, aby osiągnąć oczekiwaną wydajność operacji we/wy. 

Podczas wdrażania można wybrać zasoby obliczeniowe i magazynowe, a następnie [zmienić je później przy użyciu Azure Portal](../../database/scale-resources.md), bez ponoszenia przestojów aplikacji. 

> [!IMPORTANT]
> Wszelkie niezgodności w [wymaganiach dotyczących sieci wirtualnej dla wystąpień zarządzanych](../../managed-instance/connectivity-architecture-overview.md#network-requirements) mogą uniemożliwić tworzenie nowych wystąpień lub korzystanie z istniejących. Dowiedz się więcej na temat [tworzenia nowych](../../managed-instance/virtual-network-subnet-create-arm-template.md)   i [konfigurowania istniejących](../../managed-instance/vnet-existing-add-subnet.md)   sieci. 

Kolejnym zagadnieniem w wyborze docelowej warstwy usług w wystąpieniu zarządzanym Azure SQL (Ogólnego przeznaczenia a Krytyczne dla działania firmy) jest dostępność niektórych funkcji, takich jak In-Memory OLTP, które są dostępne tylko w warstwie Krytyczne dla działania firmy. 

### <a name="sql-server-vm-alternative"></a>Alternatywa maszyny wirtualnej SQL Server

Firma może mieć wymagania, które sprawiają, że [SQL Server na platformie Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) bardziej odpowiedni cel niż wystąpienie zarządzane usługi Azure SQL. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Ta usługa platformy Azure obsługuje migrację w trybie offline dla aplikacji, które mogą zapewnić przestoje podczas procesu migracji. W przeciwieństwie do ciągłej migracji w trybie online, migracja w trybie offline uruchamia jednorazowe Przywracanie pełnej kopii zapasowej bazy danych ze źródła do obiektu docelowego. | 
|[Natywna kopia zapasowa i przywracanie](../../managed-instance/restore-sample-database-quickstart.md) | Wystąpienie zarządzane SQL obsługuje przywracanie natywnych kopii zapasowych bazy danych SQL Server (pliki. bak). Jest to najprostsza opcja migracji dla klientów, którzy mogą zapewnić pełne kopie zapasowe bazy danych w usłudze Azure Storage. Pełne i różnicowe kopie zapasowe są również obsługiwane i udokumentowane w [sekcji dotyczącej zasobów migracji](#migration-assets) w dalszej części tego artykułu.| 
|[Rejestruj powtarzanie usługi](../../managed-instance/log-replay-service-migrate.md) | Ta usługa w chmurze jest włączona dla wystąpienia zarządzanego SQL na podstawie SQL Server technologii dostarczania dzienników. Jest to opcja migracji dla klientów, którzy mogą zapewnić pełne, różnicowe i dzienniki kopii zapasowych bazy danych w usłudze Azure Storage. Usługa powtarzania dzienników służy do przywracania plików kopii zapasowej z usługi Azure Blob Storage do wystąpienia zarządzanego SQL.| 
| | |

W poniższej tabeli wymieniono alternatywne narzędzia migracji: 

|**Technologia** |**Opis**  |
|---------|---------|
|[Replikacja transakcyjna](../../managed-instance/replication-transactional-overview.md) | Replikowanie danych z tabel źródłowych SQL Server bazy danych do wystąpienia zarządzanego SQL przez udostępnienie opcji migracji typu subskrybenta wydawcy podczas zachowywania spójności transakcyjnej. | 
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Narzędzie do kopiowania masowego (bcp)](/sql/tools/bcp-utility) kopiuje dane z wystąpienia SQL Server do pliku danych. Użyj narzędzia, aby wyeksportować dane ze źródła i zaimportować plik danych do docelowego wystąpienia zarządzanego SQL. </br></br> Aby zapewnić wysoką szybkość operacji kopiowania zbiorczego do przenoszenia danych do wystąpienia zarządzanego usługi Azure SQL, można użyć [Narzędzia Smart bulk copy](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) do zmaksymalizowania szybkości transferu, wykorzystując zadania kopiowania równoległego. | 
|[Kreator importu eksportu/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) to plik systemu Windows z rozszerzeniem. BACPAC, który hermetyzuje schemat i dane bazy danych. Można użyć BACPAC do eksportowania danych ze źródła SQL Server i zaimportowania danych z powrotem do wystąpienia zarządzanego Azure SQL. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  [Działanie kopiowania](../../../data-factory/copy-activity-overview.md) w programie Azure Data Factory migruje dane ze źródłowej bazy danych SQL Server do wystąpienia zarządzanego SQL przy użyciu wbudowanych łączników i [środowiska Integration Runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory obsługuje szeroką gamę [łączników](../../../data-factory/connector-overview.md) służących do przenoszenia danych ze źródeł SQL Server do wystąpienia zarządzanego SQL. |

## <a name="compare-migration-options"></a>Porównanie opcji migracji

Porównaj opcje migracji, aby wybrać ścieżkę, która jest odpowiednia dla potrzeb Twojej firmy. 

Poniższa tabela zawiera porównanie zalecanych opcji migracji: 

|Opcja migracji  |Kiedy stosować  |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | — Migruj pojedyncze bazy danych lub wiele baz danych na dużą skalę. </br> — Może obsłużyć przestoje podczas procesu migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM |  — Migracje na dużą skalę można zautomatyzować za pomocą [programu PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> -Czas na zakończenie migracji zależy od rozmiaru bazy danych i ma wpływ na czas wykonywania kopii zapasowej i przywracania. </br> -Wystarczające przestoje mogą być wymagane. |
|[Natywna kopia zapasowa i przywracanie](../../managed-instance/restore-sample-database-quickstart.md) | — Migrowanie poszczególnych baz danych aplikacji biznesowych.  </br> — Szybka i łatwa migracja bez oddzielnej usługi lub narzędzia migracji.  </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Kopia zapasowa bazy danych używa wielu wątków do optymalizowania transferu danych do usługi Azure Blob Storage, ale przepustowość partnera i rozmiar bazy danych mogą mieć wpływ na szybkość transferu. </br> -Przestoje powinny pomieścić czas wymagany do wykonania pełnej kopii zapasowej i przywracania (czyli rozmiaru operacji na danych).| 
|[Rejestruj powtarzanie usługi](../../managed-instance/log-replay-service-migrate.md) | — Migrowanie poszczególnych baz danych aplikacji biznesowych.  </br> -Do migracji bazy danych jest wymagana większa kontrola.  </br> </br> Obsługiwane źródła: </br> -SQL Server (2008 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM | — Migracja obejmuje tworzenie pełnych kopii zapasowych bazy danych na SQL Server i kopiowanie plików kopii zapasowej do usługi Azure Blob Storage. Usługa powtarzania dzienników służy do przywracania plików kopii zapasowej z usługi Azure Blob Storage do wystąpienia zarządzanego SQL. </br> -Bazy danych przywracane podczas procesu migracji będą w trybie przywracania i nie można ich użyć do odczytu lub zapisu do momentu zakończenia procesu.| 
| | | |

Poniższa tabela zawiera porównanie alternatywnych opcji migracji: 

|Metoda lub technologia |Kiedy stosować |Zagadnienia do rozważenia  |
|---------|---------|---------|
|[Replikacja transakcyjna](../../managed-instance/replication-transactional-overview.md) | — Migrowanie przez ciągłe publikowanie zmian z tabel źródłowych baz danych do docelowych tabel bazy danych wystąpienia zarządzanego SQL. </br> -Wykonaj pełne lub częściowe migracje baz danych wybranych tabel (podzbiór bazy danych).  </br> </br> Obsługiwane źródła: </br> -SQL Server (od 2012 do 2019) z pewnymi ograniczeniami </br> -AWS EC2  </br> -GCP COMPUTE SQL Server VM | </br> -Konfiguracja jest stosunkowo złożona w porównaniu z innymi opcjami migracji.   </br> — Udostępnia opcję ciągłej replikacji danych (bez przełączania baz danych w tryb offline).</br> -Replikacja transakcyjna ma ograniczenia, które należy wziąć pod uwagę podczas konfigurowania wydawcy w wystąpieniu SQL Server źródłowym. Zobacz [ograniczenia dotyczące publikowania obiektów](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) , aby dowiedzieć się więcej.  </br> — Możliwość [monitorowania aktywności replikacji](/sql/relational-databases/replication/monitor/monitoring-replication) jest dostępna.    |
|[Kopiowanie zbiorcze](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| — Wykonaj pełne lub częściowe migracje danych. </br> — Może obsłużyć przestoje. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM   | -Wymaga przestoju podczas eksportowania danych ze źródła i importowania ich do obiektu docelowego. </br> -Formaty plików i typy danych używane podczas eksportowania lub importowania muszą być spójne ze schematami tabel. |
|[Kreator importu eksportu/BACPAC](../../database/database-import.md)| — Migrowanie poszczególnych baz danych aplikacji biznesowych. </br>— Odpowiednie dla mniejszych baz danych.  </br>  Nie wymaga osobnej usługi lub narzędzia migracji. </br> </br> Obsługiwane źródła: </br> -SQL Server (2005 do 2019) lokalnie lub na maszynie wirtualnej platformy Azure </br> -AWS EC2 </br> -AWS RDS </br> -GCP COMPUTE SQL Server VM  |   </br> -Wymaga przestoju, ponieważ dane muszą zostać wyeksportowane w źródle i zaimportowane w miejscu docelowym.   </br> -Formaty plików i typy danych używane do eksportowania lub importowania muszą być spójne ze schematami tabel, aby uniknąć obcięcia lub niezgodności typów danych. </br> — Czas poświęcony na Eksportowanie bazy danych o dużej liczbie obiektów może być znacznie wyższy. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Migruj i/lub Przekształć dane ze źródłowej bazy danych SQL Server.</br> — Scalanie danych z wielu źródeł danych do wystąpienia zarządzanego usługi Azure SQL jest zwykle przeznaczone dla obciążeń analizy biznesowej.   </br> -Wymaga utworzenia potoków przenoszenia danych w Data Factory, aby przenieść dane ze źródła do miejsca docelowego.   </br> - [Koszt](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) jest ważnym zagadnieniem i opiera się na czynnikach, takich jak wyzwalacze potokowe, uruchomienia działań i czas trwania przenoszenia danych. |
| | | |

## <a name="feature-interoperability"></a>Współdziałanie funkcji 

Podczas migrowania obciążeń, które są zależne od innych funkcji SQL Server, należy wziąć pod uwagę. 

### <a name="sql-server-integration-services"></a>Usługi SQL Server Integration Services

Migruj pakiety SQL Server Integration Services (SSIS) i projekty w programie SSISDB do wystąpienia zarządzanego usługi Azure SQL przy użyciu [Azure Database Migration Service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Migracja jest obsługiwana tylko w przypadku pakietów SSIS w SSISDB począwszy od SQL Server 2012. Przed migracją Skonwertuj starsze pakiety usług SSIS. Aby dowiedzieć się więcej, zobacz [Samouczek dotyczący konwersji projektu](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Raporty usługi SQL Server Reporting Services (SSRS) można migrować do raportów z podziałem na strony w programie Power BI. Użyj [Narzędzia do migracji RDL](https://github.com/microsoft/RdlMigration) , aby pomóc w przygotowaniu i migracji raportów. Firma Microsoft opracowała to narzędzie, aby pomóc klientom w migrowaniu raportów Report Definition Language (RDL) z serwerów usług SSRS do Power BI. Jest ono dostępne w witrynie GitHub i zawiera kompleksowe wskazówki dotyczące scenariusza migracji. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services modele tabelaryczne z SQL Server 2012 i nowszych można migrować do Azure Analysis Services, który jest modelem wdrażania platformy jako usługi (PaaS) dla modeli tabelarycznych Analysis Services na platformie Azure. Więcej informacji na temat migrowania modeli lokalnych do Azure Analysis Services można znaleźć w [tym samouczku wideo](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Alternatywnie można rozważyć Migrowanie lokalnych modeli Analysis Services modele tabelaryczne do Power BI Premium przy [użyciu nowych punktów końcowych odczytu/zapisu XMLA](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Wysoka dostępność

Funkcje wysokiej dostępności SQL Server są zawsze włączone w wystąpieniach klastra trybu failover i zawsze włączone grupy dostępności są przestarzałe w docelowym wystąpieniu zarządzanym SQL. Architektura wysokiej dostępności została już wbudowana w warstwy usług [ogólnego przeznaczenia (standard Availability model)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) i [krytyczne dla działania firmy (model dostępności Premium)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) dla wystąpienia zarządzanego SQL. Model dostępności Premium zapewnia również skalowalność w poziomie, która umożliwia łączenie się z jednym z węzłów pomocniczych w celach tylko do odczytu.     

Poza architekturą wysokiej dostępności, która jest uwzględniona w wystąpieniu zarządzanym SQL, funkcja [grup autopracy awaryjnej](../../database/auto-failover-group-overview.md) umożliwia zarządzanie replikacją i trybem failover baz danych w wystąpieniu zarządzanym w innym regionie. 

### <a name="sql-agent-jobs"></a>Zadania programu SQL Agent

Użyj opcji Azure Database Migration Service offline, aby przeprowadzić migrację [zadań programu SQL Agent](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). W przeciwnym razie skryptuje zadania w języku Transact-SQL (T-SQL) za pomocą SQL Server Management Studio a następnie ręcznie je utworzyć w docelowym wystąpieniu zarządzanym SQL. 

> [!IMPORTANT]
> Obecnie Azure Database Migration Service obsługuje tylko zadania z podsystemem T-SQL. Zadania z etapami pakietu SSIS muszą być migrowane ręcznie. 

### <a name="logins-and-groups"></a>Nazwy logowania i grupy

Przenieś nazwy logowania SQL ze źródła SQL Server do wystąpienia zarządzanego usługi Azure SQL, używając Database Migration Service w trybie offline.  Za pomocą okienka [Wybieranie logowań](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) w Kreatorze migracji można migrować logowania do docelowego wystąpienia zarządzanego SQL. 

Domyślnie Azure Database Migration Service obsługuje Migrowanie tylko nazw logowania SQL. Można jednak włączyć migrację nazw logowania systemu Windows, wykonując następujące działania:

- Upewnienie się, że docelowe wystąpienie zarządzane SQL ma dostęp do odczytu Azure Active Directory (Azure AD). Użytkownik mający rolę administratora globalnego może skonfigurować ten dostęp za pośrednictwem Azure Portal.
- Konfigurowanie Azure Database Migration Service, aby umożliwić migrację logowania użytkowników lub grup systemu Windows. Można to skonfigurować za pośrednictwem Azure Portal na stronie **Konfiguracja** . Po włączeniu tego ustawienia należy ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.

Po ponownym uruchomieniu usługi, logowania użytkowników lub grup systemu Windows są wyświetlane na liście nazw logowania dostępnych do migracji. W przypadku wszystkich migrowanych użytkowników systemu Windows lub grup, zostanie wyświetlony monit o podanie nazwy skojarzonej domeny. Konta użytkowników usługi (konta z URZĘDem nazw domen NT) i konta użytkowników wirtualnych (konta z usługą nazw domen NT) nie są obsługiwane. Aby dowiedzieć się więcej, zobacz [jak migrować użytkowników i grupy systemu Windows w wystąpieniu SQL Server do wystąpienia zarządzanego usługi Azure SQL przy użyciu języka T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternatywnie można użyć [Narzędzia programu PowerShell](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) specjalnie zaprojektowanego przez architektów migracji danych firmy Microsoft. Narzędzie używa programu PowerShell do utworzenia skryptu T-SQL w celu ponownego utworzenia logowań i wybrania użytkowników bazy danych z lokalizacji źródłowej do docelowej. 

Narzędzie programu PowerShell automatycznie mapuje konta systemu Windows Server Active Directory na konta usługi Azure AD i może przeprowadzić wyszukiwanie nazw UPN dla każdej nazwy logowania względem wystąpienia Active Directory źródłowej. Narzędzie skryptuje niestandardowe role serwera i bazy danych wraz z członkostwem w roli i uprawnieniami użytkownika. Zawarte bazy danych nie są jeszcze obsługiwane, a tylko podzbiór możliwych uprawnień SQL Server są skryptami. 

### <a name="encryption"></a>Szyfrowanie

Podczas migrowania baz danych chronionych przez  [transparent Data Encryption](../../database/transparent-data-encryption-tde-overview.md)   do wystąpienia zarządzanego przy użyciu opcji przywracania natywnego należy [migrować odpowiedni certyfikat](../../managed-instance/tde-certificate-migrate.md) z wystąpienia SQL Server źródłowego do docelowego wystąpienia zarządzanego SQL *przed* przywróceniem bazy danych. 

### <a name="system-databases"></a>Systemowe bazy danych

Przywracanie systemowych baz danych nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywanych w bazie danych Master i msdb), zaskryptowanie ich przy użyciu języka T-SQL, a następnie utwórz je ponownie w docelowym wystąpieniu zarządzanym. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (tabele zoptymalizowane pod kątem pamięci)

SQL Server zapewnia In-Memory OLTP. Umożliwia użycie tabel zoptymalizowanych pod kątem pamięci, typów tabel zoptymalizowanych pod kątem pamięci oraz natywnie skompilowanych modułów SQL do uruchamiania obciążeń o wysokiej przepływności i małych opóźnieniach na potrzeby przetwarzania transakcyjnego. 

> [!IMPORTANT]
> In-Memory OLTP jest obsługiwana tylko w warstwie Krytyczne dla działania firmy w wystąpieniu zarządzanym usługi Azure SQL. Nie jest ona obsługiwana w warstwie Ogólnego przeznaczenia.

Jeśli masz tabele zoptymalizowane pod kątem pamięci lub typy tabel zoptymalizowane pod kątem pamięci w lokalnym wystąpieniu SQL Server i chcesz przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL, należy wykonać następujące instrukcje:

- Wybierz warstwę Krytyczne dla działania firmy dla docelowego wystąpienia zarządzanego SQL, które obsługuje In-Memory OLTP.
- Jeśli chcesz przeprowadzić migrację do warstwy Ogólnego przeznaczenia w wystąpieniu zarządzanym Azure SQL, Usuń tabele zoptymalizowane pod kątem pamięci, typy tabel zoptymalizowane pod kątem pamięci i natywnie skompilowane moduły SQL, które współpracują z obiektami zoptymalizowanymi pod kątem pamięci przed migracją baz danych. Możesz użyć następującego zapytania T-SQL, aby zidentyfikować wszystkie obiekty, które należy usunąć przed migracją do warstwy Ogólnego przeznaczenia:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Aby dowiedzieć się więcej o technologiach w pamięci, zobacz [Optymalizowanie wydajności przy użyciu technologii w pamięci w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Funkcje zaawansowane 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze w wystąpieniu zarządzanym SQL. Nie trzeba na przykład zajmować się zarządzaniem kopiami zapasowymi, ponieważ usługa robi to za Ciebie. W okresie przechowywania można przywrócić wszystkie [punkty w czasie](../../database/recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba martwić się o konfigurację wysokiej dostępności, ponieważ [wysoka dostępność jest wbudowana](../../database/high-availability-sla.md). 

Aby zwiększyć bezpieczeństwo, należy rozważyć [użycie uwierzytelniania usługi Azure AD](../../database/authentication-aad-overview.md), [inspekcje](../../managed-instance/auditing-configure.md), [wykrywanie zagrożeń](../../database/azure-defender-for-sql.md), [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security)i [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń wystąpienie zarządzane SQL udostępnia zaawansowane narzędzia, które mogą ułatwić [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). [Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych w scentralizowany sposób.  [Dostrajanie automatyczne](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   w przypadku wystąpień zarządzanych ciągłe monitorowanie wydajności wykonywania planu SQL i automatyczne rozwiązywanie określonych problemów z wydajnością. 

Niektóre funkcje są dostępne dopiero po zmianie [poziomu zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) na najnowszy poziom zgodności (150). 

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać więcej pomocy, zobacz następujące zasoby, które zostały opracowane dla rzeczywistych projektów migracji.

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla obciążenia. Oferuje proste, oparte na jednym kliknięciu obliczenia i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany i jednolity proces decyzyjny dla platform docelowych.|
|[Narzędzie do ładowania](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Za pomocą usługi dbloader można ładować dane z rozdzielanych plików tekstowych do SQL Server. To narzędzie konsoli systemu Windows używa interfejsu natywnego ładowania zbiorczego klienta SQL Server. Interfejs działa na wszystkich wersjach SQL Server, wraz z wystąpieniem zarządzanym usługi Azure SQL.|
|[Narzędzie do przenoszenia lokalnych nazw logowania SQL Server do wystąpienia zarządzanego usługi Azure SQL](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Skrypt programu PowerShell może utworzyć skrypt poleceń T-SQL, aby ponownie utworzyć logowania i wybrać użytkowników bazy danych z lokalnego SQL Server do wystąpienia zarządzanego Azure SQL. Narzędzie to umożliwia automatyczne mapowanie kont Active Directory systemu Windows Server na konta usługi Azure AD oraz opcjonalne Migrowanie SQL Server natywnych nazw logowania.|
|[Automatyzacja zbierania danych monitora wydajności za pomocą narzędzia Logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Za pomocą narzędzia Logman można zbierać dane monitora wydajności (aby ułatwić zrozumienie wydajności linii bazowej) i uzyskać zalecenia dotyczące docelowego migracji. To narzędzie używa logman.exe, aby utworzyć polecenie, które będzie tworzyć, uruchamiać, zatrzymywać i usuwać liczniki wydajności ustawione w wystąpieniu SQL Server zdalnego.|
|[Migracja bazy danych do wystąpienia zarządzanego usługi Azure SQL przez przywrócenie pełnych i różnicowych kopii zapasowych](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Ten oficjalny dokument zawiera wskazówki i kroki ułatwiające przyspieszenie migracji z SQL Server do wystąpienia zarządzanego usługi Azure SQL, jeśli masz tylko pełne i różnicowe kopie zapasowe (bez możliwości tworzenia kopii zapasowych dziennika).|

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.


## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć migrację baz danych SQL Server do wystąpienia zarządzanego usługi Azure SQL, zapoznaj się z [przewodnikiem migracji wystąpienia zarządzanego usługi Azure sql SQL Server](sql-server-to-managed-instance-guide.md).

- W przypadku macierzy usług i narzędzi, które mogą ułatwić scenariusze migracji bazy danych i danych oraz zadań specjalnych, zobacz temat [usługi i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat wystąpienia zarządzanego usługi Azure SQL, zobacz:
   - [Warstwy usług w wystąpieniu zarządzanym usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Różnice między SQL Server i wystąpieniem zarządzanym usługi Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące określania kosztów i rozmiarów obciążeń migrowanych na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Aby uzyskać szczegółowe informacje na temat przeprowadzania testowania A/B w warstwie dostępu do danych, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
