---
title: Zamiast ETL, zaprojektuj ELT
description: Implementowanie elastycznych strategii ładowania danych dla dedykowanych pul SQL w ramach Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8a8f857dcfdc271a3aaad71f4b9c26d474033383
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566107"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Strategie ładowania danych dla dedykowanej puli SQL w Azure Synapse Analytics

Tradycyjne dedykowane pule SQL SMP używają procesu wyodrębniania, przekształcania i ładowania (ETL) do ładowania danych. Synapse SQL w Azure Synapse Analytics korzysta z architektury rozproszonego przetwarzania zapytań, która korzysta ze skalowalności i elastyczności zasobów obliczeniowych i magazynowych.

Korzystanie z procesu wyodrębniania, ładowania i transformacji (ELT) wykorzystuje wbudowane możliwości rozproszonego przetwarzania zapytań i eliminuje zasoby potrzebne do przekształcenia danych przed załadowaniem.

Dedykowane pule SQL obsługują wiele metod ładowania, w tym popularne opcje SQL Server, takie jak [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i interfejs [API SqlBulkCopy,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)ale najszybszym i najbardziej skalowalnym sposobem ładowania danych są tabele zewnętrzne polyBase i instrukcja [COPY.](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Za pomocą programu PolyBase i instrukcji COPY można uzyskać dostęp do danych zewnętrznych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL. Aby uzyskać większą elastyczność podczas ładowania, zalecamy użycie instrukcji COPY.


## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnianie, ładowanie i transformacja (ELT) to proces, za pomocą którego dane są wyodrębnione z systemu źródłowego, ładowane do dedykowanej puli SQL, a następnie przekształcane.

Podstawowe kroki wdrażania procesu ELT są następujące:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Dane trafiają do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przejściowych za pomocą programu PolyBase lub polecenia COPY.
5. Przekształcanie danych.
6. Wstaw dane do tabel produkcyjnych.

Aby uzyskać samouczek dotyczący ładowania, zobacz [ładowanie danych z usługi Azure Blob Storage.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnianie danych źródłowych do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji przechowywania. Celem jest przeniesienie danych do obsługiwanego rozdzielanego tekstu lub plików CSV.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików

Za pomocą programu PolyBase i instrukcji COPY można załadować dane z rozdzielanego tekstu lub plików CSV zakodowanych w formacie UTF-8 i UTF-16. Oprócz rozdzielanych plików tekstowych lub CSV są ładowane z formatów plików hadoop, takich jak ORC i Parquet. Program PolyBase i instrukcja COPY mogą również ładować dane z skompresowanych plików Gzip i Snappy.

Rozszerzone formaty ASCII, formaty o stałej szerokości i zagnieżdżone formaty, takie jak WinZip lub XML, nie są obsługiwane. Jeśli eksportujesz z SQL Server, możesz użyć narzędzia wiersza polecenia [bcp,](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) aby wyeksportować dane do rozdzielanych plików tekstowych.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Dane trafiają do usługi Azure Blob Storage lub Azure Data Lake Store

Aby przenieść dane do usługi Azure Storage, możesz przenieść je do usługi [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub Azure Data Lake Store [Gen2.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) W obu lokalizacjach dane powinny być przechowywane w plikach tekstowych. Program PolyBase i instrukcja COPY można załadować z każdej lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zwiększa przepływność, wydajność i przewidywalność sieci. ExpressRoute to usługa, która kieruje dane za pośrednictwem dedykowanego połączenia prywatnego do platformy Azure. Połączenia usługi ExpressRoute nie przekierują danych za pośrednictwem publicznego Internetu. Połączenia oferują większą niezawodność, większą szybkość, mniejsze opóźnienia i większe zabezpieczenia niż typowe połączenia za pośrednictwem publicznego Internetu.
- [Narzędzie AZCopy przenosi](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) dane do usługi Azure Storage za pośrednictwem publicznego Internetu. Działa to, jeśli rozmiary danych są mniejsze niż 10 TB. Aby regularnie wykonywać obciążenia za pomocą programu AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest to akceptowalne.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ma bramę, która można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z dedykowanymi pulami SQL, zobacz Loading data for dedicated SQL pools (Ładowanie [danych dla dedykowanych pul SQL).](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotowywanie danych do załadowania

Może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu przed załadowaniem. Przygotowywanie danych można wykonać, gdy dane są w źródle, podczas eksportowania danych do plików tekstowych lub gdy dane są w usłudze Azure Storage.  Najłatwiej jest pracować z danymi tak na wczesnym etapie procesu, jak to możliwe.  

### <a name="define-the-tables"></a>Definiowanie tabel

W przypadku korzystania z instrukcji COPY należy najpierw zdefiniować ładowane do tabeli w dedykowanej puli SQL.

Jeśli używasz technologii PolyBase, przed załadowaniem musisz zdefiniować tabele zewnętrzne w dedykowanej puli SQL. Program PolyBase używa tabel zewnętrznych do definiowania danych i uzyskiwania do nich dostępu w usłudze Azure Storage. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza dedykowaną pulą SQL.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabel. Potrzebne artykuły referencyjne dotyczące składni języka T-SQL to:

- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Podczas ładowania plików Parquet użyj następującego mapowania typu danych SQL:

|                         Typ Parquet                         |   Typ logiczny Parquet (adnotacja)   |  Typ danych SQL   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           Boolean                            |                                       |       bit        |
|                     DANE BINARNE /BYTE_ARRAY                      |                                       |    varbinary     |
|                            Podwójne                            |                                       |      float       |
|                            Float                             |                                       |       liczba rzeczywista       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binarny      |
|                            Binarnym                            |                 UTF8                  |     nvarchar     |
|                            Binarnym                            |                Ciąg                 |     nvarchar     |
|                            Binarnym                            |                 Enum                  |     nvarchar     |
|                            Binarnym                            |                 Uuid                  | uniqueidentifier |
|                            Binarnym                            |                Dziesiętnych                |     decimal      |
|                            Binarnym                            |                 JSON                  |  nvarchar(MAX)   |
|                            Binarnym                            |                 Bson                  |  Varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                Dziesiętnych                |     decimal      |
|                          BYTE_ARRAY                          |               Interwał                |  Varchar(max)   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       data       |
|                            INT32                             |                Dziesiętnych                |     decimal      |
|                            INT32                             |            TIME (MILLIS )             |       time       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false )            |  decimal(20,0)   |
|                            INT64                             |                Dziesiętnych                |     decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       time       |
|                            INT64                             | SYGNATURA CZASOWA (MILLIS)                  |    datetime2     |
| [Typ złożony](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 Listy                  |   varchar(max)   |
| [Typ złożony](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  Mapę                  |   varchar(max)   |

>[!IMPORTANT] 
>- Dedykowane pule SQL nie obsługują obecnie typów danych Parquet z dokładnością do mikrosług i nanos. 
>- Następujący błąd może wystąpić, jeśli typy są niezgodne między parquet i SQL lub jeśli nieobsługiwane typy danych Parquet: **"HdfsBridge::recordReaderFillBuffer —** wystąpił nieoczekiwany błąd podczas wypełniania buforu czytnika rekordów: ClassCastException: ..."
>- Ładowanie wartości spoza zakresu od 0 do 127 do kolumny tinyint dla formatu plików Parquet i ORC nie jest obsługiwane.

Aby uzyskać przykład tworzenia obiektów zewnętrznych, zobacz [Tworzenie tabel zewnętrznych](../sql/develop-tables-external-tables.md?tabs=sql-pool).

### <a name="format-text-files"></a>Formatowanie plików tekstowych

Jeśli używasz technologii PolyBase, zdefiniowane obiekty zewnętrzne muszą wyrównać wiersze plików tekstowych do tabeli zewnętrznej i definicji formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą ze źródła nierelacyjnych, musisz przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnych, czy nierelacyjnych, dane muszą zostać przekształcone w celu wyrównania ich z definicjami kolumn dla tabeli, do której mają zostać załadowane dane.
- Sformatuj dane w pliku tekstowym, aby dopasować je do kolumn i typów danych w tabeli docelowej. Niedorównanie między typami danych w zewnętrznych plikach tekstowych a dedykowaną tabelą puli SQL powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym za pomocą terminatora.  Pamiętaj, aby użyć znaku lub sekwencji znaków, która nie znajduje się w danych źródłowych. Użyj określonego terminatora w [formacie CREATE EXTERNAL FILE .](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Załaduj dane przy użyciu technologii PolyBase lub instrukcji COPY

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przejściowe umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania daje również możliwość użycia dedykowanej architektury przetwarzania równoległego puli SQL do przekształceń danych przed wstawianiem danych do tabel produkcyjnych.

### <a name="options-for-loading"></a>Opcje ładowania

Aby załadować dane, można użyć dowolnej z tych opcji ładowania:

- Instrukcja [COPY jest](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) zalecanym narzędziem do ładowania, ponieważ umożliwia bezproblemowe i elastyczne ładowanie danych. Instrukcja ma wiele dodatkowych możliwości ładowania, których nie zapewnia program PolyBase. 
- [Program PolyBase z T-SQL](./load-data-from-azure-blob-storage-using-copy.md) wymaga zdefiniowania zewnętrznych obiektów danych.
- [Instrukcja PolyBase i COPY z Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) to kolejne narzędzie orkiestracji.  Definiuje potok i planuje zadania.
- [Program PolyBase z programem SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) działa dobrze, gdy dane źródłowe SQL Server. Usługi SSIS definiują mapowania tabeli źródłowej na docelową, a także aranżują obciążenie. Jeśli masz już pakiety usług SSIS, możesz je zmodyfikować tak, aby działały z nowym miejscem docelowym magazynu danych.
- [PolyBase z Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) przesyła dane z tabeli do ramki danych usługi Databricks i/lub zapisuje dane z ramki danych usługi Databricks do tabeli przy użyciu technologii PolyBase.

### <a name="other-loading-options"></a>Inne opcje ładowania

Oprócz programu PolyBase i instrukcji COPY można użyć [narzędzia bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) lub interfejsu [API SqlBulkCopy.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Narzędzie bcp ładuje się bezpośrednio do bazy danych bez przechodzinia przez usługę Azure Blob Storage i jest przeznaczone tylko do małych obciążeń.

> [!NOTE]
> Wydajność ładowania tych opcji jest niższa niż w programie PolyBase i instrukcji COPY.

## <a name="5-transform-the-data"></a>5. Przekształcanie danych

Gdy dane są w tabeli przejściowej, wykonaj przekształcenia wymagane przez obciążenie. Następnie przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstawianie danych do tabel produkcyjnych

Polecenie INSERT INTO ... Instrukcja SELECT przenosi dane z tabeli przejściowej do tabeli trwałej.

Podczas projektowania procesu ETL spróbuj uruchomić proces na małym przykładzie testowym. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieść go na platformę Azure, a następnie załadować do tabeli przejściowej.

## <a name="partner-loading-solutions"></a>Rozwiązania ładowania partnerów

Wielu naszych partnerów ma rozwiązania do ładowania. Aby dowiedzieć się więcej, zobacz listę naszych [partnerów rozwiązań.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [Wskazówki dotyczące ładowania danych](guidance-for-loading-data.md).