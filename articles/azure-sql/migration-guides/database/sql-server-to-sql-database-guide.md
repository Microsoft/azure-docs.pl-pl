---
title: 'SQL Server SQL Database: Przewodnik migracji'
description: Postępuj zgodnie z tym przewodnikiem, aby migrować bazy danych SQL Server do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: a2ab63febbb4439e50ef0f7bcc0f9797dc50c62c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260032"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>Przewodnik migracji: SQL Server do SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Ten przewodnik pomaga migrować wystąpienie SQL Server do Azure SQL Database. 

Możesz migrować SQL Server uruchomione lokalnie lub na: 

- Program SQL Server w usłudze Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Usługa Amazon relacyjnej bazy danych (AWS RDS) 
- Aparat obliczeniowy (Google Cloud Platform-GCP)  
- SQL w chmurze dla SQL Server (Google Cloud Platform – GCP) 

Aby uzyskać więcej informacji dotyczących migracji, zobacz [Omówienie migracji](sql-server-to-sql-database-overview.md). Inne scenariusze można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Przepływ procesu migracji":::

## <a name="prerequisites"></a>Wymagania wstępne 

Aby przeprowadzić migrację SQL Server do Azure SQL Database, upewnij się, że masz następujące wymagania wstępne: 

- Wybrana [Metoda migracji](sql-server-to-sql-database-overview.md#compare-migration-options) i odpowiednie narzędzia 
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) zainstalowany na komputerze, który może nawiązać połączenie ze źródłem SQL Server
- [Azure SQL Database](../../database/single-database-create-quickstart.md) docelowy


## <a name="pre-migration"></a>Przed migracją

Po zweryfikowaniu, że środowisko źródłowe jest obsługiwane, należy zacząć od etapu migracji wstępnej. Odkryj wszystkie istniejące źródła danych, Oceń wykonalność migracji i zidentyfikuj wszelkie problemy z blokowaniem, które mogą uniemożliwiać migrację. 

### <a name="discover"></a>Odnajdywanie

W fazie odnajdywania Przeprowadź skanowanie sieci, aby zidentyfikować wszystkie SQL Server wystąpienia i funkcje używane przez organizację. 

Użyj [Azure Migrate](../../../migrate/migrate-services-overview.md) do oceny przydatności do migracji serwerów lokalnych, wykonywania ustalania rozmiarów na podstawie wydajności i zapewnienia oszacowania kosztów na potrzeby uruchamiania ich na platformie Azure. 

Alternatywnie możesz ocenić bieżącą infrastrukturę IT przy użyciu [zestawu narzędzi do oceny i planowania firmy Microsoft ("mapowanie zestawu narzędzi")](https://www.microsoft.com/download/details.aspx?id=7826) . Zestaw narzędzi zapewnia zaawansowane narzędzie do tworzenia spisu, oceny i raportowania, które upraszcza proces planowania migracji. 

Aby uzyskać więcej informacji o narzędziach dostępnych do użycia w fazie odnajdywania, zobacz temat [usługi i narzędzia dostępne dla scenariuszy migracji danych](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Ocena 

Po odnalezieniu źródeł danych Oceń wszystkie lokalne SQL Server baz danych, które można migrować do Azure SQL Database w celu zidentyfikowania blokowania migracji lub problemów ze zgodnością. 

Możesz użyć Data Migration Assistant (wersja 4,1 i nowsze) do oceny baz danych, aby uzyskać: 

- [Zalecenia dotyczące platformy Azure Target](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Zalecenia dotyczące jednostki SKU platformy Azure](/sql/dma/dma-sku-recommend-sql-db)

Aby ocenić środowisko przy użyciu oceny migracji bazy danych, wykonaj następujące kroki: 

1. Otwórz [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowa Ocena**. 
1. Określ nazwę projektu, wybierz SQL Server jako typ serwera źródłowego, a następnie wybierz Azure SQL Database jako docelowy typ serwera. 
1. Wybierz typy raportów oceny, które mają zostać wygenerowane. Na przykład zgodność bazy danych i parzystość funkcji. W oparciu o typ oceny uprawnienia wymagane na SQL Server źródłowym mogą być różne.  Funkcja DMA będzie wyróżnić uprawnienia wymagane dla wybranego klasyfikatora przed uruchomieniem oceny.
    - Kategoria **parzystości funkcji** zawiera kompleksowy zestaw zaleceń, alternatyw dostępnych na platformie Azure i ograniczanie czynności ułatwiających planowanie projektu migracji. (wymagane są uprawnienia administratora systemu)
    - Kategoria **problemy ze zgodnością** identyfikuje częściowo obsługiwane lub nieobsługiwane problemy ze zgodnością funkcji, które mogą blokować migrację, a także zalecenia dotyczące ich rozwiązywania ( `CONNECT SQL` `VIEW SERVER STATE` wymagane są uprawnienia, i `VIEW ANY DEFINITION` uprawnień).
1. Określ szczegóły połączenia źródłowego dla SQL Server i Połącz się ze źródłową bazą danych.
1. Wybierz pozycję **Rozpocznij ocenę**. 
1. Po zakończeniu procesu wybierz i przejrzyj raporty oceny dotyczące blokowania migracji i problemów z parzystością funkcji. Raport oceny można również wyeksportować do pliku, który może być współużytkowany z innymi zespołami lub personelem w organizacji. 
1. Określanie poziomu zgodności bazy danych, który minimalizuje działania po migracji.  
1. Zidentyfikuj najlepszą Azure SQL Database jednostkę SKU dla obciążenia lokalnego. 

Aby dowiedzieć się więcej, zobacz [wykonywanie oceny migracji SQL Server przy użyciu Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Jeśli Ocena napotka wiele bloków, aby upewnić się, że baza danych nie jest gotowa do migracji Azure SQL Database, należy rozważyć następujące kwestie:

- [Wystąpienie zarządzane usługi Azure SQL](../managed-instance/sql-server-to-managed-instance-overview.md) , jeśli istnieje wiele zależności z zakresem wystąpienia
- [SQL Server na platformie Azure Virtual Machines](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) , jeśli SQL Database i wystąpienie zarządzane SQL nie mogą być odpowiednimi obiektami docelowymi. 



#### <a name="scaled-assessments-and-analysis"></a>Skalowane oceny i analiza
Data Migration Assistant obsługuje przeskalowane oceny i konsolidację raportów oceny na potrzeby analizy. 

Jeśli masz wiele serwerów i baz danych, które należy ocenić i przeanalizować w odpowiedniej skali, aby zapewnić szerszy wgląd w dane, zobacz następujące linki, aby dowiedzieć się więcej:

- [Wykonywanie skalowanych ocen przy użyciu programu PowerShell](/sql/dma/dma-consolidatereports)
- [Analizowanie raportów oceny przy użyciu Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> Uruchamianie ocen w dużej skali dla wielu baz danych, szczególnie dużych, można również zautomatyzować przy użyciu [narzędzia wiersza polecenia DMA](/sql/dma/dma-commandline) i przekazane do [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) w celu dalszej analizy i docelowej gotowości.

## <a name="migrate"></a>Migrate

Po ukończeniu zadań skojarzonych z etapem wstępnej migracji można przystąpić do wykonywania migracji schematu i danych. 

Migruj dane przy użyciu wybranej [metody migracji](sql-server-to-sql-database-overview.md#compare-migration-options). 

Ten przewodnik zawiera opis dwóch najpopularniejszych opcji — Data Migration Assistant i Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

Aby przeprowadzić migrację bazy danych z SQL Server do Azure SQL Database przy użyciu usługi DMA, wykonaj następujące czynności: 

1. Pobierz i zainstaluj [Asystent migracji bazy danych](https://www.microsoft.com/download/details.aspx?id=53595).
1. Utwórz nowy projekt i wybierz pozycję **migracja** jako typ projektu.
1. Ustaw typ serwera źródłowego na **SQL Server** i typ serwera docelowego do **Azure SQL Database**, wybierz zakres migracji jako **schemat i dane** , a następnie wybierz pozycję **Utwórz**.
1. W projekcie migracji Określ szczegóły serwera źródłowego, takie jak nazwa serwera, poświadczenia do łączenia się z serwerem i źródłowa baza danych do migracji.
1. Na stronie Szczegóły serwera docelowego określ Azure SQL Database nazwę serwera, poświadczenia, aby połączyć się z serwerem i docelową bazę danych do migracji.
1. Wybierz obiekty schematu i wdróż je w docelowym Azure SQL Database.
1. Na koniec wybierz pozycję **Rozpocznij migrację danych** i monitoruj postęp migracji.

Aby uzyskać szczegółowy samouczek, zobacz [Migrowanie lokalnych SQL Server lub SQL Server na maszynach wirtualnych platformy Azure w celu Azure SQL Database korzystania z Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Skaluj bazę danych do wyższej warstwy usług i rozmiaru obliczeń podczas procesu importowania, aby zmaksymalizować szybkość importowania przez zapewnienie większej ilości zasobów. Po pomyślnym zakończeniu importowania możesz przeprowadzić skalowanie w dół.</br>
> - Poziom zgodności zaimportowanej bazy danych jest oparty na poziomie zgodności źródłowej bazy danych. 


### <a name="azure-database-migration-service-dms"></a>Usługa Azure Database Migration Service (DMS)

Aby migrować bazy danych z SQL Server Azure SQL Database za pomocą usługi DMS, wykonaj następujące czynności:

1. Jeśli jeszcze tego nie zrobiono, zarejestruj dostawcę zasobów **Microsoft. datamigration** w ramach subskrypcji. 
1. Utwórz wystąpienie Azure Database Migration Service w wybranej wybranej lokalizacji (najlepiej w tym samym regionie, w którym znajduje się Azure SQL Database docelowy). Wybierz istniejącą sieć wirtualną lub Utwórz nową, aby hostować wystąpienie DMS.
1. Po utworzeniu wystąpienia DMS Utwórz nowy projekt migracji i określ typ serwera źródłowego jako **SQL Server** i docelowy typ serwera jako **Azure SQL Database**. Wybierz pozycję **migracja danych w trybie offline** jako typ działania w bloku tworzenia projektu migracji.
1. Określ szczegóły SQL Server źródłowej na stronie szczegółów **źródła migracji** oraz szczegóły Azure SQL Database docelowy na stronie Szczegóły **migracji docelowej** .
1. Zamapuj źródłową i docelową bazę danych na potrzeby migracji, a następnie wybierz tabele, które chcesz zmigrować.
1. Przejrzyj podsumowanie migracji i wybierz pozycję **Uruchom migrację**. Następnie można monitorować działanie migracji i sprawdzać postęp migracji bazy danych.

Aby uzyskać szczegółowy samouczek, zobacz [migrowanie SQL Server do Azure SQL Database za pomocą usługi DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Synchronizacja danych i uruchomienie produkcyjne

W przypadku korzystania z opcji migracji, które w sposób ciągły replikuje/synchronizują zmiany danych ze źródła do obiektu docelowego, dane źródłowe i schemat mogą ulec zmianie i dryfować względem obiektu docelowego. Podczas synchronizacji danych upewnij się, że wszystkie zmiany w źródle są przechwytywane i stosowane do obiektu docelowego podczas procesu migracji. 

Po zweryfikowaniu, że dane są takie same na serwerze źródłowym i docelowym, można uruchomienie produkcyjne ze źródła do środowiska docelowego. Ważne jest, aby zaplanować proces uruchomienie produkcyjne z zespołami biznesowymi/aplikacjami, aby zapewnić minimalną przerwę w czasie, gdy uruchomienie produkcyjne nie ma wpływu na ciągłość działania firmy. 

> [!IMPORTANT]
> Aby uzyskać szczegółowe informacje dotyczące konkretnych kroków związanych z wykonywaniem uruchomienie produkcyjne w ramach migracji za pomocą usługi DMS, zobacz [wykonywanie migracji uruchomienie produkcyjne](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover).

## <a name="migration-recommendations"></a>Zalecenia dotyczące migracji

Aby przyspieszyć migrację do Azure SQL Database, należy wziąć pod uwagę następujące zalecenia:

|  | Rywalizacja o zasoby | Zalecenie |
|--|--|--|
| **Źródło (zazwyczaj lokalnie)** |Podstawowe wąskie gardło podczas migracji w źródle to dane we/wy i opóźnienie dla pliku danych, które należy uważnie monitorować.  |W oparciu o dane we/wy i opóźnienia plików danych i w zależności od tego, czy jest to maszyna wirtualna czy serwer fizyczny, należy skontaktować się z administratorem magazynu i poznać opcje w celu ograniczenia wąskiego gardła. |
|**Cel (Azure SQL Database)**|Największym czynnikiem ograniczającym jest szybkość generowania dzienników i czas oczekiwania na plik dziennika. W przypadku Azure SQL Database można uzyskać maksymalnie 96 MB/s szybkości generowania dzienników. | Aby przyspieszyć migrację, Skaluj w górę docelową bazę danych SQL do Krytyczne dla działania firmy 5 rdzeń 8 rdzeń wirtualny, aby uzyskać maksymalną częstotliwość generowania dzienników wynoszącą 96 MB/s, a także uzyskać małe opóźnienia dla pliku dziennika. Warstwa usługi do [skalowania](https://docs.microsoft.com/azure/azure-sql/database/service-tier-hyperscale) zapewnia 100 MB/s, niezależnie od wybranego poziomu usługi |
|**Sieć** |Wymagana przepustowość sieci jest równa maksymalnej szybkości pozyskiwania dziennika 96 MB/s (768 MB/s) |W zależności od łączności sieciowej z lokalnego centrum danych na platformę Azure Sprawdź przepustowość sieci (zazwyczaj [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction#bandwidth-options)), aby uwzględnić maksymalną szybkość pozyskiwania dzienników. |
|**Maszyna wirtualna używana do Data Migration Assistant (DMA)** |Procesor to podstawowe wąskie gardło dla maszyny wirtualnej z systemem DMA |Zagadnienia, które należy wziąć pod uwagę w celu przyspieszenia migracji danych za pomocą </br>— Maszyny wirtualne intensywnie korzystające z obliczeń Azure </br>-Użyj co najmniej F8s_v2 maszyny wirtualnej (8 rdzeń wirtualny) do uruchamiania usługi DMA </br>-Upewnij się, że maszyna wirtualna jest uruchomiona w tym samym regionie platformy Azure co element docelowy |
|**Usługa Azure Database Migration Service (DMS)** |Zagadnienie związane z rywalizacją o zasoby obliczeniowe i obiekty bazy danych dla usługi DMS |Użyj Premium 4 rdzeń wirtualny. Usługa DMS automatycznie przyjmuje obiekty bazy danych, takie jak klucze obce, wyzwalacze, ograniczenia i indeksy nieklastrowane i nie wymaga ręcznej interwencji.  |


## <a name="post-migration"></a>Po migracji

Po pomyślnym ukończeniu etapu migracji przejdź przez serię zadań wykonywanych po migracji, aby upewnić się, że wszystko działa prawidłowo i wydajnie. 

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia. 

### <a name="remediate-applications"></a>Koryguj aplikacje 

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe**: Uruchom test wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

   > [!NOTE]
   > Aby uzyskać pomoc w tworzeniu i uruchamianiu testów weryfikacyjnych po migracji, należy wziąć pod uwagę rozwiązanie dotyczące jakości danych dostępne z [QuerySurge](https://www.querysurge.com/company/partners/microsoft)partnera. 


## <a name="leverage-advanced-features"></a>Korzystanie z zaawansowanych funkcji 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze oferowanych przez SQL Database, takich jak [wbudowana wysoka dostępność](../../database/high-availability-sla.md), [wykrywanie zagrożeń](../../database/azure-defender-for-sql.md)i [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). 

Niektóre funkcje SQL Server są dostępne tylko wtedy, gdy [poziom zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) zostanie zmieniony na najnowszy poziom zgodności (150). 

Aby dowiedzieć się więcej, zobacz [zarządzanie Azure SQL Database po migracji](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat SQL Database, zobacz:
    - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
