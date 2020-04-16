---
title: Zamiast ETL, projekt ELT dla puli SYNAPSE SQL | Dokumenty firmy Microsoft
description: Zamiast ETL, zaprojektuj proces wyodrębniania, ładowania i przekształcania (ELT) do ładowania danych lub puli SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429592"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Projektowanie strategii ładowania danych PolyBase dla puli SQL Synapse platformy Azure

Tradycyjne magazyny danych SMP używają procesu wyodrębniania, przekształcania i ładowania (ETL) do ładowania danych. Pula SQL platformy Azure to architektura przetwarzania równoległego (MPP), która wykorzystuje skalowalność i elastyczność zasobów obliczeniowych i magazynowych. Korzystając z procesu wyodrębniania, ładowania i przekształcania (ELT) można skorzystać z MPP i wyeliminować zasoby potrzebne do przekształcenia danych przed załadowaniem.

Podczas gdy pula SQL obsługuje wiele metod ładowania, w tym opcje inne niż Polybase, takie jak BCP i SQL BulkCopy API, najszybszym i najbardziej skalowalnym sposobem ładowania daty jest polybase.  PolyBase to technologia, która uzyskuje dostęp do danych zewnętrznych przechowywanych w magazynie obiektów Blob platformy Azure lub usługi Azure Data Lake Store za pośrednictwem języka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij, Załaduj i Przekształć (ELT) to proces, w którym dane są wyodrębniane z systemu źródłowego, ładowane do magazynu danych, a następnie przekształcane.

Podstawowe kroki implementacji puli PolyBase ELT dla SQL to:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wyląduj dane w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przemieszczania puli SQL przy użyciu PolyBase.
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.

Aby zapoznać się z samouczkiem ładowania, zobacz [PolyBase do ładowania danych z magazynu obiektów blob platformy Azure do usługi Azure SQL Data Warehouse.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Aby uzyskać więcej informacji, zobacz [Blog wczytywania wzorców](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Wyprowadzanie danych z systemu źródłowego zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do polybase obsługiwane rozdzielane pliki tekstowe.

### <a name="polybase-external-file-formats"></a>Formaty plików zewnętrznych PolyBase

PolyBase ładuje dane z plików tekstowych z kodowanymi rozdzielaniem UTF-8 i UTF-16. Oprócz rozdzielonych plików tekstowych, ładuje się z formatów plików Hadoop RC File, ORC i Parquet. PolyBase może również ładować dane z plików Skompresowanych Gzip i Snappy. PolyBase obecnie nie obsługuje rozszerzonych ASCII, format o stałej szerokości i zagnieżdżone formaty, takie jak WinZip, JSON i XML.

W przypadku eksportowania z programu SQL Server można użyć [narzędzia wiersza polecenia bcp,](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) aby wyeksportować dane do rozdzielonych plików tekstowych. Mapowanie typu danych Parquet do SQL DW jest następujące:

| **Typ danych parkietu** |                      **Typ danych SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        wartość logiczna        |                             bit                              |
|        double         |                            float                             |
|         float         |                             rzeczywiste                             |
|        double         |                            pieniędzy                             |
|        double         |                          smallmoney                          |
|        ciąg         |                            nchar                             |
|        ciąg         |                           nvarchar                           |
|        ciąg         |                             char                             |
|        ciąg         |                           varchar                            |
|        binarny         |                            binarny                            |
|        binarny         |                          varbinary                           |
|       sygnatura czasowa       |                             date                             |
|       sygnatura czasowa       |                        smalldatetime                         |
|       sygnatura czasowa       |                          datetime2                           |
|       sygnatura czasowa       |                           datetime                           |
|       sygnatura czasowa       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Wyląduj dane w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Store

Aby wyładowywać dane w magazynie platformy Azure, można przenieść je do [magazynu obiektów Blob platformy Azure](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) lub usługi Azure Data Lake [Store.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) W obu lokalizacjach dane powinny być przechowywane w plikach tekstowych. PolyBase można załadować z obu lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- [Usługa Azure ExpressRoute](../../expressroute/expressroute-introduction.md) zwiększa przepływność sieci, wydajność i przewidywalność. Usługa ExpressRoute to usługa, która kieruje dane za pośrednictwem dedykowanego połączenia prywatnego na platformę Azure. Połączenia usługi ExpressRoute nie kierują danych przez publiczny Internet. Połączenia zapewniają większą niezawodność, większą szybkość, mniejsze opóźnienia i większe bezpieczeństwo niż typowe połączenia przez publiczny Internet.
- [Narzędzie AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiary danych są mniejsze niż 10 TB. Aby regularnie wykonywać obciążenia za pomocą AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest to dopuszczalne.
- [Usługa Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ma bramę, którą można zainstalować na serwerze lokalnym. Następnie można utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć usługi Data Factory z pulą SQL, zobacz [Ładowanie danych do puli SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotowanie danych do załadunku

Może być konieczne przygotowanie i czyszczenie danych na koncie magazynu przed załadowaniem ich do puli SQL. Przygotowanie danych można wykonać, gdy dane znajdują się w źródle, podczas eksportowania danych do plików tekstowych lub po tym, jak dane znajdują się w usłudze Azure Storage.  Najłatwiej jest pracować z danymi tak wcześnie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Przed załadowaniem danych należy zdefiniować tabele zewnętrzne w magazynie danych. PolyBase używa tabel zewnętrznych do definiowania danych w usłudze Azure Storage i uzyskiwania do nich dostępu. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje na dane przechowywane poza magazynem danych.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabel. Są to tematy składni T-SQL, które będą potrzebne:

- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Na przykład tworzenia obiektów zewnętrznych, zobacz [Tworzenie tabel zewnętrznych](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) krok w samouczku ładowania.

### <a name="format-text-files"></a>Formatowanie plików tekstowych

Po zdefiniowaniu obiektów zewnętrznych należy wyrównać wiersze plików tekstowych z definicją tabeli zewnętrznej i formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być zgodne z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą ze źródła nierelacyjnego, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w taki sposób, aby były zgodne z definicjami kolumn dla tabeli, do której mają być ładowane dane.
- Formatowanie danych w pliku tekstowym w celu wyrównania z kolumnami i typami danych w tabeli docelowej puli SQL. Niewspółosiowość między typami danych w zewnętrznych plikach tekstowych a tabelą magazynu danych powoduje odrzucenie wierszy podczas ładowania.
- Rozdziel pola w pliku tekstowym za pomocą terminatora.  Pamiętaj, aby użyć znaku lub sekwencji znaków, które nie znajdują się w danych źródłowych. Użyj terminatora określonego w formacie [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Załaduj dane do tabel przejściowych puli SQL przy użyciu PolyBase

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przemieszczania. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania tabel produkcyjnych. Tabela przemieszczania daje również możliwość użycia MPP puli SQL dla przekształceń danych przed wstawieniem danych do tabel produkcyjnych.

### <a name="options-for-loading-with-polybase"></a>Opcje ładowania z PolyBase

Aby załadować dane za pomocą PolyBase, można użyć dowolnej z następujących opcji ładowania:

- [PolyBase z T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) działa dobrze, gdy dane znajdują się w magazynie obiektów Blob platformy Azure lub w magazynie usługi Azure Data Lake Store. Zapewnia największą kontrolę nad procesem ładowania, ale wymaga również definiowania obiektów danych zewnętrznych. Inne metody definiują te obiekty za kulisami podczas mapowania tabel źródłowych do tabel docelowych.  Aby zorganizować obciążenia T-SQL, można użyć usługi Azure Data Factory, SSIS lub azure funkcji.
- [PolyBase z SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) działa dobrze, gdy dane źródłowe są w programie SQL Server, lokalnie lub w chmurze. SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby pracować z nowym miejscem docelowym magazynu danych.
- [PolyBase z usługą Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) to kolejne narzędzie aranżacji.  Definiuje potok i planuje zadania.
- [PolyBase z usługą Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) przesyła dane z tabeli usługi SQL Data Warehouse do dataframe i/lub zapisuje dane z elementu dataframe databricks do tabeli usługi SQL Data Warehouse przy użyciu polybase.

### <a name="non-polybase-loading-options"></a>Opcje ładowania non-PolyBase

Jeśli dane nie są zgodne z polybase, można użyć [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lub [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). bcp ładuje się bezpośrednio do puli SQL bez przechodzenia przez magazyn obiektów Blob platformy Azure i jest przeznaczony tylko dla małych obciążeń. Uwaga: wydajność ładowania tych opcji jest znacznie wolniejsza niż PolyBase.

## <a name="5-transform-the-data"></a>5. Przekształcenie danych

Gdy dane są w tabeli przemieszczania, wykonaj przekształcenia, które wymaga obciążenia. Następnie przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstawianie danych do tabel produkcyjnych

Wkładka do ... Instrukcja SELECT przenosi dane z tabeli przemieszczania do stałej tabeli.

Podczas projektowania procesu ETL spróbuj uruchomić proces na małej próbce testowej. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieś go na platformę Azure, a następnie spróbuj załadować go do tabeli przemieszczania.

## <a name="partner-loading-solutions"></a>Rozwiązania do ładowania partnerów

Wielu naszych partnerów ma rozwiązania do ładowania. Aby dowiedzieć się więcej, zapoznaj się z listą naszych [partnerów rozwiązań.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [Wskazówki dotyczące ładowania danych](data-loading-best-practices.md).
