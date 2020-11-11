---
title: SQL Server do wystąpienia zarządzanego SQL — Przewodnik migracji
description: Postępuj zgodnie z tym przewodnikiem, aby przeprowadzić migrację baz danych SQL Server do wystąpienia zarządzanego Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 0aba809fd18dfd74a344a32b2335aba9426c9845
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496994"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Przewodnik migracji: SQL Server do wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ten przewodnik pomaga migrować wystąpienie SQL Server do wystąpienia zarządzanego usługi Azure SQL. 

Możesz migrować SQL Server uruchomione lokalnie lub na: 

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)  
- SQL w chmurze dla SQL Server (Google Cloud Platform – GCP) 

Aby uzyskać więcej informacji dotyczących migracji, zobacz [Omówienie migracji](sql-server-to-managed-instance-overview.md). Inne scenariusze można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Przepływ procesu migracji":::

## <a name="prerequisites"></a>Wymagania wstępne 

Aby przeprowadzić migrację SQL Server do wystąpienia zarządzanego usługi Azure SQL, upewnij się, że przechodzą następujące wymagania wstępne: 

- Wybierz [metodę migracji](sql-server-to-managed-instance-overview.md#compare-migration-options) i odpowiednie narzędzia, które są wymagane dla wybranej metody
- Zainstaluj [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) na komputerze, który może nawiązać połączenie ze źródłem SQL Server


## <a name="pre-migration"></a>Przed migracją

Po zweryfikowaniu, że środowisko źródłowe jest obsługiwane, należy zacząć od etapu migracji wstępnej. Odkryj wszystkie istniejące źródła danych, Oceń wykonalność migracji i zidentyfikuj wszelkie problemy z blokowaniem, które mogą uniemożliwiać migrację.  

### <a name="discover"></a>Odnajdywanie

W fazie odnajdywania Przeprowadź skanowanie sieci, aby zidentyfikować wszystkie SQL Server wystąpienia i funkcje używane przez organizację. 

Użyj [Azure Migrate](../../../migrate/migrate-services-overview.md) do oceny przydatności do migracji serwerów lokalnych, wykonywania ustalania rozmiarów na podstawie wydajności i zapewnienia oszacowania kosztów na potrzeby uruchamiania ich na platformie Azure. 

Alternatywnie możesz ocenić bieżącą infrastrukturę IT przy użyciu [zestawu narzędzi do oceny i planowania firmy Microsoft ("mapowanie zestawu narzędzi")](https://www.microsoft.com/download/details.aspx?id=7826) . Zestaw narzędzi zapewnia zaawansowane narzędzie do tworzenia spisu, oceny i raportowania, które upraszcza proces planowania migracji. 

Aby uzyskać więcej informacji o narzędziach dostępnych do użycia w fazie odnajdywania, zobacz temat [usługi i narzędzia dostępne dla scenariuszy migracji danych](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Ocena 

Po odnalezieniu źródeł danych Oceń wszystkie lokalne wystąpienia SQL Server, które można migrować do wystąpienia zarządzanego usługi Azure SQL, aby zidentyfikować blokowania lub problemy ze zgodnością. 

Możesz użyć Data Migration Assistant (wersja 4,1 i nowsze) do oceny baz danych, aby uzyskać: 

- [Zalecenia dotyczące platformy Azure Target](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Zalecenia dotyczące jednostki SKU platformy Azure](/sql/dma/dma-sku-recommend-sql-db)

Aby ocenić środowisko przy użyciu oceny migracji bazy danych, wykonaj następujące kroki: 

1. Otwórz [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowa Ocena**. 
1. Określ nazwę projektu, wybierz SQL Server jako typ serwera źródłowego, a następnie wybierz wystąpienie zarządzane Azure SQL jako typ serwera docelowego. 
1. Wybierz typy raportów oceny, które mają zostać wygenerowane. Na przykład zgodność bazy danych i parzystość funkcji. W oparciu o typ oceny uprawnienia wymagane na SQL Server źródłowym mogą być różne.  Funkcja DMA będzie wyróżnić uprawnienia wymagane dla wybranego klasyfikatora przed uruchomieniem oceny.
    - Kategoria **parzystości funkcji** zawiera kompleksowy zestaw zaleceń, alternatyw dostępnych na platformie Azure i ograniczanie czynności ułatwiających planowanie projektu migracji. (wymagane są uprawnienia administratora systemu)
    - Kategoria **problemy ze zgodnością** identyfikuje częściowo obsługiwane lub nieobsługiwane problemy ze zgodnością funkcji, które mogą blokować migrację, a także zalecenia dotyczące ich rozwiązywania ( `CONNECT SQL` `VIEW SERVER STATE` wymagane są uprawnienia, i `VIEW ANY DEFINITION` uprawnień).
1. Określ szczegóły połączenia źródłowego dla SQL Server i Połącz się ze źródłową bazą danych.
1. Wybierz pozycję **Rozpocznij ocenę**. 
1. Po zakończeniu procesu wybierz i przejrzyj raporty oceny dotyczące blokowania migracji i problemów z parzystością funkcji. Raport oceny można również wyeksportować do pliku, który może być współużytkowany z innymi zespołami lub personelem w organizacji. 
1. Określanie poziomu zgodności bazy danych, który minimalizuje działania po migracji.  
1. Zidentyfikuj najlepszą jednostkę SKU wystąpienia zarządzanego usługi Azure SQL dla obciążenia lokalnego. 

Aby dowiedzieć się więcej, zobacz [wykonywanie oceny migracji SQL Server przy użyciu Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Jeśli wystąpienie zarządzane SQL nie jest odpowiednim elementem docelowym dla obciążenia, SQL Server na maszynach wirtualnych platformy Azure może być żywotnym alternatywnym celem dla Twojej firmy. 

#### <a name="scaled-assessments-and-analysis"></a>Skalowane oceny i analiza

Data Migration Assistant obsługuje przeskalowane oceny i konsolidację raportów oceny na potrzeby analizy. Jeśli masz wiele serwerów i baz danych, które należy ocenić i przeanalizować w odpowiedniej skali, aby zapewnić szerszy wgląd w dane, kliknij poniższe linki, aby dowiedzieć się więcej.

- [Wykonywanie skalowanych ocen przy użyciu programu PowerShell](/sql/dma/dma-consolidatereports)
- [Analizowanie raportów oceny przy użyciu Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>Uruchamianie ocen w skali dla wielu baz danych może być również zautomatyzowane przy użyciu [narzędzia wiersza polecenia DMA](/sql/dma/dma-commandline) , które umożliwia przekazywanie wyników do [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) na potrzeby dalszej analizy i docelowej gotowości.

### <a name="create-a-performance-baseline"></a>Utwórz linię bazową wydajności

Jeśli potrzebujesz porównać wydajność obciążeń z wystąpieniem zarządzanym SQL, korzystając z oryginalnego obciążenia działającego na SQL Server, Utwórz linię bazową wydajności do użycia na potrzeby porównania. Zobacz [linię bazową wydajności](sql-server-to-managed-instance-performance-baseline.md) , aby dowiedzieć się więcej. 

### <a name="create-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego SQL 

Na podstawie informacji w fazie odnajdywania i oceniania Utwórz odpowiednie docelowe wystąpienie zarządzane SQL. Można to zrobić przy użyciu szablonu [Azure Portal](../../managed-instance/instance-create-quickstart.md), [programu PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)lub [Azure Resource Manager (ARM)](/../../managed-instance/create-template-quickstart.md). 


## <a name="migrate"></a>Migrate

Po ukończeniu zadań skojarzonych z etapem wstępnej migracji można przystąpić do wykonywania migracji schematu i danych. 

Migruj dane przy użyciu wybranej [metody migracji](sql-server-to-managed-instance-overview.md#compare-migration-options). 

Ten przewodnik zawiera opis dwóch najpopularniejszych opcji — Azure Database Migration Service (DMS) i natywnych kopii zapasowych i przywracania. 

### <a name="database-migration-service"></a>Database Migration Service

Aby przeprowadzić migrację za pomocą usługi DMS, wykonaj następujące czynności:

1. Zarejestruj dostawcę zasobów **Microsoft. datamigration** w ramach subskrypcji, jeśli wykonujesz tę operację po raz pierwszy.
1. Utwórz wystąpienie Azure Database Migration Service w wybranej lokalizacji (najlepiej w tym samym regionie, w którym znajduje się docelowe wystąpienie zarządzane Azure SQL) i wybierz istniejącą sieć wirtualną lub Utwórz nową, aby hostować wystąpienie usługi DMS.
1. Po utworzeniu wystąpienia DMS Utwórz nowy projekt migracji i określ typ serwera źródłowego jako **SQL Server** i docelowy typ serwera jako **Azure SQL Database wystąpienie zarządzane**. Wybierz typ działania w bloku tworzenia projektu — migracja danych w trybie online lub offline. 
1.  Określ szczegóły SQL Server źródłowej na stronie szczegółów **źródła migracji** oraz szczegóły docelowego wystąpienia zarządzanego Azure SQL na stronie Szczegóły **lokalizacji docelowej migracji** . Wybierz opcję **Dalej**.
1. Wybierz bazę danych, którą chcesz zmigrować. 
1. Podaj ustawienia konfiguracji, aby określić **udział sieciowy SMB** zawierający pliki kopii zapasowej bazy danych. Użyj poświadczeń użytkownika systemu Windows z usługą DMS, która może uzyskać dostęp do udziału sieciowego. Podaj **szczegóły konta usługi Azure Storage**. 
1. Przejrzyj podsumowanie migracji, a następnie wybierz pozycję **Uruchom migrację**. Następnie można monitorować działanie migracji i sprawdzać postęp migracji bazy danych.
1. Po przywróceniu bazy danych wybierz pozycję **Start uruchomienie produkcyjne**. Proces migracji kopiuje kopię zapasową dziennika końcowego po udostępnieniu jej w udziale sieciowym SMB i przywraca ją w miejscu docelowym. 
1. Zatrzymaj cały ruch przychodzący do źródłowej bazy danych i zaktualizuj parametry połączenia do nowej bazy danych wystąpienia zarządzanego Azure SQL. 

Aby zapoznać się z szczegółowym samouczkiem dotyczącym tej opcji migracji, zobacz [migrowanie SQL Server do wystąpienia zarządzanego usługi Azure SQL w trybie online za pomocą usługi DMS](/azure/dms/tutorial-sql-server-managed-instance-online). 
   


### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie 

Jedną z kluczowych możliwości wystąpienia zarządzanego usługi Azure SQL w celu umożliwienia szybkiej i łatwej migracji bazy danych jest natywne przywrócenie plików kopii zapasowej bazy danych ( `.bak` ) przechowywanych w usłudze [Azure Storage](https://azure.microsoft.com/services/storage/). Wykonywanie kopii zapasowych i przywracanie jest operacją asynchroniczną na podstawie rozmiaru bazy danych. 

Poniższy diagram przedstawia ogólny przegląd procesu:

![Diagram przedstawia SQL Server ze strzałką z etykietą kopia ZAPASowa/przekazywanie do usługi Azure Storage, a druga strzałka z etykietą Przywróć z adresu URL przepływania z usługi Azure Storage do zarządzanego wystąpienia SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> Czas tworzenia kopii zapasowej, przekazywania go do usługi Azure Storage i wykonywania operacji przywracania natywnego w wystąpieniu zarządzanym Azure SQL zależy od rozmiaru bazy danych. Należy zapewnić wystarczającą przestoje w celu podzielenia operacji dla dużych baz danych. 


Aby przeprowadzić migrację przy użyciu kopii zapasowej i przywracania, wykonaj następujące kroki: 

1. Utwórz kopię zapasową bazy danych w usłudze Azure Blob Storage. Na przykład użyj [kopii zapasowej do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) w [SQL Server Management Studio](/ssms/download-sql-server-management-studio-ssms). Użyj [narzędzia Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) do obsługi baz danych starszych niż SQL Server 2012 SP1 zastosujesz pakietu CU2. 
1. Połącz się z wystąpieniem zarządzanym usługi Azure SQL przy użyciu SQL Server Management Studio. 
1. Utwórz poświadczenia przy użyciu sygnatury dostępu współdzielonego, aby uzyskać dostęp do konta usługi Azure Blob Storage za pomocą kopii zapasowych bazy danych. Na przykład:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Przywróć kopię zapasową z kontenera obiektów BLOB usługi Azure Storage. Na przykład: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Po zakończeniu przywracania Wyświetl bazę danych w **Eksplorator obiektów** w SQL Server Management Studio. 

Aby dowiedzieć się więcej o tej opcji migracji, zobacz [przywracanie bazy danych do wystąpienia zarządzanego usługi Azure SQL Database za pomocą programu SSMS](https://docs.microsoft.com/azure/azure-sql/managed-instance/restore-sample-database-quickstart).

> [!NOTE]
> Operacja przywracania bazy danych jest asynchroniczna i ponowienie. Może wystąpić błąd w SQL Server Management Studio, jeśli przerwania połączenia lub limit czasu wygaśnie. Azure SQL Database będzie ponawiać próbę przywrócenia bazy danych w tle, a postęp przywracania można śledzić przy użyciu widoków [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .



## <a name="data-sync-and-cutover"></a>Synchronizacja danych i uruchomienie produkcyjne

W przypadku korzystania z opcji migracji, które w sposób ciągły replikuje/synchronizują zmiany danych ze źródła do obiektu docelowego, dane źródłowe i schemat mogą ulec zmianie i dryfować względem obiektu docelowego. Podczas synchronizacji danych upewnij się, że wszystkie zmiany w źródle są przechwytywane i stosowane do obiektu docelowego podczas procesu migracji. 

Po zweryfikowaniu, że dane są takie same na źródłowym i docelowym, można uruchomienie produkcyjne ze źródła do środowiska docelowego. Ważne jest, aby zaplanować proces uruchomienie produkcyjne z zespołami biznesowymi/aplikacjami, aby zapewnić minimalną przerwę w czasie, gdy uruchomienie produkcyjne nie ma wpływu na ciągłość działania firmy. 

> [!IMPORTANT]
> Aby uzyskać szczegółowe informacje dotyczące konkretnych kroków związanych z wykonywaniem uruchomienie produkcyjne w ramach migracji za pomocą usługi DMS, zobacz [wykonywanie migracji uruchomienie produkcyjne](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


## <a name="post-migration"></a>Po migracji

Po pomyślnym ukończeniu etapu migracji przejdź przez serię zadań wykonywanych po migracji, aby upewnić się, że wszystko działa prawidłowo i wydajnie. 

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia. 

### <a name="remediate-applications"></a>Koryguj aplikacje 

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych** : Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego** : środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne** : Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe** : Uruchom test wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

   > [!NOTE]
   > Aby uzyskać pomoc w tworzeniu i uruchamianiu testów weryfikacyjnych po migracji, należy wziąć pod uwagę rozwiązanie dotyczące jakości danych dostępne z [QuerySurge](https://www.querysurge.com/company/partners/microsoft)partnera. 



## <a name="leverage-advanced-features"></a>Korzystanie z zaawansowanych funkcji 

Korzystaj z zaawansowanych funkcji opartych na chmurze oferowanych przez wystąpienie zarządzane SQL, takich jak [wbudowana wysoka dostępność](../../database/high-availability-sla.md), [wykrywanie zagrożeń](../../database/advanced-data-security.md)i [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). 

[Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych w sposób scentralizowany.

Niektóre funkcje SQL Server są dostępne tylko wtedy, gdy [poziom zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) zostanie zmieniony na najnowszy poziom zgodności (150). 


## <a name="next-steps"></a>Następne kroki

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
