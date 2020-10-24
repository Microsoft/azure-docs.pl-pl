---
title: Zamiast ETL, projekt ELT
description: Implementowanie elastycznych strategii ładowania danych dla puli SQL Synapse w ramach usługi Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0533e76863d01675cee7aaca79e32821e5efc749
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92507807"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie ładowania danych w puli Synapse SQL

Tradycyjne pule SQL SMP używają procesu wyodrębniania, transformacji i ładowania (ETL) do ładowania danych. Synapse SQL w ramach usługi Azure Synapse Analytics używa architektury przetwarzania zapytań rozproszonych, która korzysta z skalowalności i elastyczności zasobów obliczeniowych i magazynu.

Użycie procesu wyodrębniania, ładowania i przekształcania (ELT) wykorzystuje wbudowane funkcje przetwarzania zapytań rozproszonych i eliminuje zasoby wymagające przekształcenia danych przed załadowaniem.

Chociaż Pula SQL obsługuje wiele metod ładowania, w tym popularnych opcji SQL Server, takich jak [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i [interfejsu API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie podstawowych tabel zewnętrznych i [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Korzystając z instrukcji Base i COPY, można uzyskać dostęp do zewnętrznych danych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL. Aby zapewnić największą elastyczność podczas ładowania, zalecamy użycie instrukcji COPY.


## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij, Załaduj i Przekształć (ELT) to proces polegający na tym, że dane są wyodrębniane z systemu źródłowego, ładowane do puli SQL, a następnie przekształcane.

Podstawowe kroki implementacji ELT są następujące:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wydziel dane do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przemieszczania za pomocą polecenia Base lub COPY.
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.

Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych z usługi Azure Blob Storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji magazynu. Celem jest przeniesienie danych do obsługiwanej rozdzielonej plików tekstowych lub CSV.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Korzystając z instrukcji Base i COPY, można ładować dane z rozdzielonych plików tekstowych lub CSV z kodowaniem UTF-8 i UTF-16. Oprócz rozdzielonych plików tekstowych lub CSV, są one ładowane z formatów plików Hadoop, takich jak ORC i Parquet. Instrukcje Base i COPY mogą również ładować dane z skompresowanych plików gzip i przyciągania.

Rozszerzony format ASCII, stała szerokość i zagnieżdżone formaty, takie jak WinZip lub XML, nie są obsługiwane. Jeśli eksportujesz z SQL Server, możesz użyć [narzędzia wiersza polecenia BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby wyeksportować dane do rozdzielanych plików tekstowych.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. wyląduj dane do usługi Azure Blob Storage lub Azure Data Lake Store

Aby wystawić dane w usłudze Azure Storage, można przenieść je do [usługi Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). W każdej lokalizacji dane powinny być przechowywane w plikach tekstowych. Instrukcji Base i COPY można załadować z jednej z lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- Usługa [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zwiększa przepustowość sieci, wydajność i przewidywalność. ExpressRoute to usługa, która przekierowuje dane za pomocą dedykowanego połączenia prywatnego z platformą Azure. Połączenia ExpressRoute nie kierują danych za pomocą publicznego Internetu. Połączenia oferują większą niezawodność, większe szybkości, krótsze opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez publiczny Internet.
- [Narzędzie AzCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiar danych jest mniejszy niż 10 TB. Aby przeprowadzić regularne ładowanie w programie AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest ona akceptowalna.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zawiera bramę, którą można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z pulą SQL, zobacz [ładowanie danych dla puli SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotuj dane do załadowania

Przed załadowaniem programu może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu. Przygotowanie danych można wykonać, gdy dane są przechowywane w źródle, podczas eksportowania danych do plików tekstowych lub po utworzeniu danych w usłudze Azure Storage.  Najłatwiej pracujesz z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-the-tables"></a>Definiowanie tabel

W przypadku używania instrukcji COPY należy najpierw zdefiniować tabele ładowane do programu w puli SQL.

Jeśli używasz bazy danych Base, musisz zdefiniować tabele zewnętrzne w puli SQL przed załadowaniem. Baza danych używa tabel zewnętrznych w celu definiowania i uzyskiwania dostępu do nich w usłudze Azure Storage. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza pulą SQL.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabeli. Informacje o składni języka T-SQL, które są potrzebne, są następujące:

- [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Podczas ładowania plików Parquet Użyj następującego mapowania typu danych SQL:

|                         Typ Parquet                         |   Parquet — typ logiczny (Adnotacja)   |  Typ danych SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           TYPU                            |                                       |       bit        |
|                     DANE BINARNE/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      float       |
|                            FLOAT                             |                                       |       liczba rzeczywista       |
|                            ELEMENTEM                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binarny      |
|                            BINARNY                            |                 UTF8                  |     nvarchar     |
|                            BINARNY                            |                PARAMETRY                 |     nvarchar     |
|                            BINARNY                            |                 PODSTAWOWE                  |     nvarchar     |
|                            BINARNY                            |                 INTERFEJSU                  | uniqueidentifier |
|                            BINARNY                            |                DOKŁADNOŚCI                |     decimal      |
|                            BINARNY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARNY                            |                 BSON                  |  varbinary (max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DOKŁADNOŚCI                |     decimal      |
|                          BYTE_ARRAY                          |               DAT                |  varchar (max),   |
|                            ELEMENTEM                             |             INT (8, prawda)              |     smallint     |
|                            ELEMENTEM                             |            INT (16, true)            |     smallint     |
|                            ELEMENTEM                             |             INT (32, true)             |       int        |
|                            ELEMENTEM                             |            INT (8, FAŁSZ)            |     tinyint      |
|                            ELEMENTEM                             |            INT (16, FAŁSZ)             |       int        |
|                            ELEMENTEM                             |           INT (32, false)            |      bigint      |
|                            ELEMENTEM                             |                 DATE                  |       date       |
|                            ELEMENTEM                             |                DOKŁADNOŚCI                |     decimal      |
|                            ELEMENTEM                             |            CZAS (MŁYNER)             |       time       |
|                            INT64                             |            INT (64, true)            |      bigint      |
|                            INT64                             |           INT (64, false)            |  Liczba dziesiętna (20, 0)   |
|                            INT64                             |                DOKŁADNOŚCI                |     decimal      |
|                            INT64                             |         CZAS (MŁYNER)                 |       time       |
|                            INT64                             | SYGNATURA CZASOWA (MŁYNEK)                  |    datetime2     |
| [Typ złożony](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 STAW                  |   varchar(max)   |
| [Typ złożony](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  ZMAPOWAĆ                  |   varchar(max)   |

>[!IMPORTANT] 
> - Dedykowane pule SQL nie obsługują obecnie typów danych Parquet z MICROS i NANOs Precision. 
> - Następujący błąd może wystąpić, jeśli typy są niezgodne z Parquet i SQL lub jeśli istnieją nieobsługiwane typy danych Parquet:  **"HdfsBridge:: recordReaderFillBuffer-nieoczekiwany błąd podczas wypełniania buforu czytnika rekordu: ClassCastException:..."**

Aby zapoznać się z przykładem tworzenia obiektów zewnętrznych, zobacz [Tworzenie tabel zewnętrznych](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Formatowanie plików tekstowych

W przypadku korzystania z bazy Base, zdefiniowane obiekty zewnętrzne muszą wyrównać wiersze plików tekstowych z definicją tabeli zewnętrznej i formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą z nierelacyjnego źródła, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w celu dopasowania z definicjami kolumn dla tabeli, do której mają zostać załadowane dane.
- Sformatuj dane w pliku tekstowym, aby wyrównać je do kolumn i typów danych w tabeli docelowej. Niezgodność między typami danych w zewnętrznych plikach tekstowych a tabelą puli SQL powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym z terminatorem.  Upewnij się, że używasz znaku lub sekwencji znaków, która nie została znaleziona w danych źródłowych. Użyj terminatora określonego przy użyciu parametru [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Załaduj dane przy użyciu bazy danych lub instrukcji COPY

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania daje również możliwość użycia architektury przetwarzania równoległego puli SQL na potrzeby transformacji danych przed wstawieniem danych do tabel produkcyjnych.

### <a name="options-for-loading"></a>Opcje ładowania

Aby załadować dane, można użyć dowolnej z następujących opcji ładowania:

- [Instrukcja Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) jest zalecanym narzędziem ładowania, ponieważ pozwala bezproblemowo i elastycznie ładować dane. Instrukcja ma wiele dodatkowych możliwości ładowania, których baza nie zapewnia. 
- [Baza Base with T-SQL](load-data-from-azure-blob-storage-using-polybase.md) wymaga zdefiniowania zewnętrznych obiektów danych.
- [Instrukcja "Base" i "Copy" z Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) to inne narzędzie aranżacji.  Definiuje potok i planuje zadania.
- [Baza](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) danych programu SSIS działa prawidłowo, gdy dane źródłowe są w SQL Server. Program SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby współpracowały z nowym miejscem docelowym magazynu danych.
- [Baza danych z Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przesyła dane z tabeli do ramki danych datakosteks i/lub zapisuje dane z ramki Databases do tabeli przy użyciu bazy danych.

### <a name="other-loading-options"></a>Inne opcje ładowania

Oprócz instrukcji Base i COPY można użyć narzędzia [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) lub [interfejsu API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Narzędzie bcp ładuje się bezpośrednio do bazy danych bez przechodzenia przez usługę Azure Blob Storage i jest przeznaczone tylko do małych obciążeń.

> [!NOTE]
> Wydajność ładowania tych opcji jest mniejsza niż baza i instrukcja COPY.

## <a name="5-transform-the-data"></a>5. Przekształć dane

Gdy dane są w tabeli przemieszczania, należy wykonać przekształcenia wymagane przez obciążenie. Następnie Przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstaw dane do tabel produkcyjnych

Wstaw do... Instrukcja SELECT przenosi dane z tabeli przemieszczania do trwałej tabeli.

Podczas projektowania procesu ETL spróbuj uruchomić proces przy użyciu małego przykładu testowego. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieść go na platformę Azure, a następnie spróbować załadować go do tabeli przejściowej.

## <a name="partner-loading-solutions"></a>Rozwiązania do ładowania partnerów

Wielu naszych partnerów ma załadowane rozwiązania. Aby dowiedzieć się więcej, zobacz listę naszych [partnerów rozwiązań](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [Wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).
