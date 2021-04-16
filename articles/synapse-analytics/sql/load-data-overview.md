---
title: Projektowanie strategii ładowania danych polyBase dla dedykowanej puli SQL
description: Zamiast procesu ETL zaprojektuj proces wyodrębniania, ładowania i transformacji (ELT) do ładowania danych za pomocą dedykowanego języka SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 518843e688da7f940b36e77aee2667b4984ea5a3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567357"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Projektowanie strategii ładowania danych programu PolyBase dla dedykowanej puli SQL w programie Azure Synapse Analytics

Tradycyjne magazyny danych SMP używają procesu wyodrębniania, przekształcania i ładowania (ETL) do ładowania danych. Azure SQL to architektura masowego przetwarzania równoległego (MPP), która wykorzystuje skalowalność i elastyczność zasobów obliczeniowych i magazynowych. Korzystając z procesu wyodrębniania, ładowania i transformacji (ELT, Extract, Load, Transform), można korzystać z wbudowanych funkcji rozproszonego przetwarzania zapytań i eliminować zasoby potrzebne do przekształcania danych przed załadowaniem.

Pula SQL obsługuje wiele metod ładowania, w tym opcje inne niż polybase, takie jak BCP i interfejs API SQL BulkCopy, jednak najszybszą i najbardziej skalowalną sposobem ładowania danych jest obsługa funkcji PolyBase.  PolyBase to technologia, która uzyskuje dostęp do danych zewnętrznych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Wyodrębnianie, ładowanie i transformacja (ELT)

Wyodrębnianie, ładowanie i transformacja (ELT) to proces, za pomocą którego dane są wyodrębnione z systemu źródłowego, ładowane do magazynu danych, a następnie przekształcane.

Podstawowe kroki wdrażania procesu ELT programu PolyBase dla dedykowanej puli SQL to:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Dane trafiają do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do dedykowanych tabel przejściowych puli SQL przy użyciu technologii PolyBase.
5. Przekształcanie danych.
6. Wstaw dane do tabel produkcyjnych.

Aby uzyskać samouczek dotyczący ładowania, zobacz Ładowanie danych z usługi Azure Blob Storage do usługi [Azure Blob Storage przy użyciu programu PolyBase Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Aby uzyskać więcej informacji, zobacz blog [Loading patterns (Ładowanie wzorców).](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnianie danych źródłowych do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji przechowywania.  Celem jest przeniesienie danych do plików tekstowych obsługiwanych przez program PolyBase.

### <a name="polybase-external-file-formats"></a>Formaty plików zewnętrznych polyBase

PolyBase ładuje dane z rozdzielanych plików tekstowych zakodowanych w formacie UTF-8 i UTF-16. Oprócz rozdzielanych plików tekstowych są ładowane z plików Hadoop formaty RC File, ORC i Parquet. PolyBase może również ładować dane z skompresowanych plików Gzip i Snappy. PolyBase obecnie nie obsługuje rozszerzonych formatów ASCII, formatu o stałej szerokości i zagnieżdżonych formatów, takich jak WinZip, JSON i XML.

W przypadku eksportowania z SQL Server można użyć narzędzia wiersza polecenia [bcp,](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) aby wyeksportować dane do rozdzielanych plików tekstowych. Mapowanie typu danych parquet do Azure Synapse Analytics jest następujące:

| **Typ danych Parquet** |                      **Typ danych SQL**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             liczba rzeczywista                             |
|        double         |                            pieniędzy                             |
|        double         |                          smallmoney                          |
|        ciąg         |                            nchar                             |
|        ciąg         |                           nvarchar                           |
|        ciąg         |                             char                             |
|        ciąg         |                           varchar                            |
|        binarny         |                            binarny                            |
|        binarny         |                          varbinary                           |
|       sygnatura czasowa       |                             data                             |
|       sygnatura czasowa       |                        smalldatetime                         |
|       sygnatura czasowa       |                          datetime2                           |
|       sygnatura czasowa       |                           datetime                           |
|       sygnatura czasowa       |                             time                             |
|       data            |                             data                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Wduń dane do usługi Azure Blob Storage lub Azure Data Lake Store

Aby trafić dane do usługi Azure Storage, możesz przenieść je do usługi [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) lub azure [Data Lake Store](../../data-lake-store/data-lake-store-overview.md). W obu lokalizacjach dane powinny być przechowywane w plikach tekstowych. PolyBase może ładować się z obu lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) zwiększa przepływność, wydajność i przewidywalność sieci. ExpressRoute to usługa, która kieruje dane za pośrednictwem dedykowanego połączenia prywatnego do platformy Azure. Połączenia usługi ExpressRoute nie przekierują danych przez publiczny Internet. Połączenia oferują większą niezawodność, większą szybkość, mniejsze opóźnienia i większe zabezpieczenia niż typowe połączenia za pośrednictwem publicznego Internetu.
- [Narzędzie AZCopy](../../storage/common/storage-use-azcopy-v10.md) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. Działa to, jeśli rozmiary danych są mniejsze niż 10 TB. Aby regularnie wykonywać obciążenia za pomocą programu AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest to akceptowalne.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ma bramę, która można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z dedykowaną pulą SQL, zobacz [Ładowanie danych do dedykowanej puli SQL.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotowywanie danych do załadowania

Może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu przed załadowaniem ich do dedykowanej puli SQL. Przygotowywanie danych można wykonać, gdy dane są w źródle, podczas eksportowania danych do plików tekstowych lub gdy dane są w usłudze Azure Storage.  Najłatwiej jest pracować z danymi tak wcześnie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Przed załadowaniem danych należy zdefiniować tabele zewnętrzne w magazynie danych. Program PolyBase używa tabel zewnętrznych do definiowania danych i uzyskiwania do nich dostępu w usłudze Azure Storage. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza magazynem danych.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabel. Poniżej znajdują się potrzebne tematy dotyczące składni języka T-SQL:

- [TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formatowanie plików tekstowych

Po zdefiniowaniu obiektów zewnętrznych należy wyrównać wiersze plików tekstowych do tabeli zewnętrznej i definicji formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane do definicji tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą z nierelacyjnych źródeł, musisz przekształcić je w wiersze i kolumny. Bez względu na to, czy dane pochodzą ze źródła relacyjnych, czy nierelacyjnych, dane muszą zostać przekształcone w celu dostosowania ich do definicji kolumn dla tabeli, do której planujesz załadować dane.
- Sformatuj dane w pliku tekstowym, aby dopasować je do kolumn i typów danych w tabeli docelowej puli SQL. Niedorównanie typów danych w zewnętrznych plikach tekstowych i tabeli magazynu danych powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym za pomocą terminatora.  Pamiętaj, aby użyć znaku lub sekwencji znaków, która nie znajduje się w danych źródłowych. Użyj określonego terminatora w [formacie CREATE EXTERNAL FILE .](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Ładowanie danych do tabel przejściowych dedykowanej puli SQL przy użyciu technologii PolyBase

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przejściowe umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania umożliwia również korzystanie z wbudowanych funkcji rozproszonego przetwarzania zapytań w puli SQL na użytek przekształceń danych przed wstawianiem danych do tabel produkcyjnych.

### <a name="options-for-loading-with-polybase"></a>Opcje ładowania za pomocą programu PolyBase

Aby załadować dane za pomocą programu PolyBase, można użyć dowolnej z tych opcji ładowania:

- [Program PolyBase z T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) działa dobrze, gdy dane są w usłudze Azure Blob Storage lub Azure Data Lake Store. Zapewnia ona większą kontrolę nad procesem ładowania, ale wymaga również zdefiniowania zewnętrznych obiektów danych. Inne metody definiują te obiekty w tle podczas mapowania tabel źródłowych na tabele docelowe.  Aby aranżować obciążenia języka T-SQL, można użyć Azure Data Factory, usług SSIS lub usługi Azure Functions.
- [Program PolyBase z programem SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) działa dobrze, gdy dane źródłowe SQL Server. Usługi SSIS definiują mapowania tabeli źródłowej na docelową, a także aranżują obciążenie. Jeśli masz już pakiety usług SSIS, możesz je zmodyfikować tak, aby działały z nowym miejscem docelowym magazynu danych.
- [Innym narzędziem do orkiestracji jest Azure Data Factory PolyBase](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) z Azure Data Factory (ADF).  Definiuje potok i planuje zadania.
- [PolyBase z Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) przesyła dane z tabeli Azure Synapse Analytics do ramki danych usługi Databricks i/lub zapisuje dane z ramki danych usługi Databricks do tabeli Azure Synapse Analytics przy użyciu technologii PolyBase.

### <a name="non-polybase-loading-options"></a>Opcje ładowania inne niż polyBase

Jeśli dane nie są zgodne z technologią PolyBase, możesz użyć [narzędzia bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) lub interfejsu [API SQLBulkCopy.](/dotnet/api/system.data.sqlclient.sqlbulkcopy) Narzędzie bcp ładuje się bezpośrednio do dedykowanej puli SQL bez przechodzinia przez usługę Azure Blob Storage i jest przeznaczone tylko do małych obciążeń. Należy pamiętać, że wydajność ładowania tych opcji jest znacznie niższa niż w przypadku programu PolyBase.

## <a name="5-transform-the-data"></a>5. Przekształcanie danych

Gdy dane są w tabeli przejściowej, wykonaj przekształcenia wymagane przez obciążenie. Następnie przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstawianie danych do tabel produkcyjnych

Polecenie INSERT INTO ... Instrukcja SELECT przenosi dane z tabeli przejściowej do tabeli trwałej.

Podczas projektowania procesu ETL spróbuj uruchomić proces na małym przykładzie testowym. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieść go na platformę Azure, a następnie załadować do tabeli przejściowej.

## <a name="partner-loading-solutions"></a>Rozwiązania ładowania partnerów

Wielu naszych partnerów ma rozwiązania do ładowania. Aby dowiedzieć się więcej, zobacz listę naszych [partnerów rozwiązań.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, [zobacz Wskazówki dotyczące ładowania danych.](data-loading-best-practices.md)