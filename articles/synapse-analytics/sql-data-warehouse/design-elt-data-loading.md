---
title: Zamiast ETL, projekt ELT
description: Implementowanie elastycznych strategii ładowania danych dla puli SQL Synapse w ramach usługi Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e99fd898956e11a4827d023691111a47e5a790c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744956"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategie ładowania danych dla puli SQL Synapse

Tradycyjne pule SQL SMP używają procesu wyodrębniania, transformacji i ładowania (ETL) do ładowania danych. Synapse Pool SQL, w ramach usługi Azure Synapse Analytics, oferuje architekturę masowego przetwarzania równoległego (MPP), która korzysta z skalowalności i elastyczności zasobów obliczeniowych i magazynu.

Użycie procesu wyodrębniania, ładowania i przekształcania (ELT) wykorzystuje MPP i eliminuje zasoby konieczne do przekształcenia danych przed załadowaniem.

Chociaż Pula SQL obsługuje wiele metod ładowania, w tym popularnych opcji SQL Server, takich jak [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i [interfejsu API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie podstawowych tabel zewnętrznych i [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (wersja zapoznawcza).

Korzystając z instrukcji Base i COPY, można uzyskać dostęp do zewnętrznych danych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL. Aby zapewnić największą elastyczność podczas ładowania, zalecamy użycie instrukcji COPY.

> [!NOTE]  
> Instrukcja COPY jest obecnie w publicznej wersji zapoznawczej. Aby przekazać opinię, Wyślij wiadomość e-mail na następującą listę dystrybucyjną sqldwcopypreview@service.microsoft.com:.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Co to jest ELT?

Wyodrębnij, Załaduj i Przekształć (ELT) to proces polegający na tym, że dane są wyodrębniane z systemu źródłowego, ładowane do puli SQL, a następnie przekształcane.

Podstawowe kroki implementacji ELT są następujące:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wydziel dane do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do tabel przemieszczania za pomocą polecenia Base lub COPY.
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.

Aby zapoznać się z samouczkiem dotyczącym ładowania wieloczęściowego, zobacz [Korzystanie z bazy danych w celu załadowania z usługi Azure Blob Storage](load-data-from-azure-blob-storage-using-polybase.md).

Aby uzyskać więcej informacji, zobacz Blog dotyczący [ładowania wzorców](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do formatu Base i SKOPIOWAnie obsługiwanego tekstu lub plików CSV.

### <a name="polybase-and-copy-external-file-formats"></a>Zewnętrzne i kopiowanie formatów plików zewnętrznych

Korzystając z instrukcji Base i COPY, można ładować dane z rozdzielonych plików tekstowych lub CSV z kodowaniem UTF-8 i UTF-16. Oprócz rozdzielonych plików tekstowych lub CSV, są one ładowane z formatów plików Hadoop, takich jak ORC i Parquet. Instrukcje Base i COPY mogą również ładować dane z skompresowanych plików gzip i przyciągania.

Rozszerzony format ASCII, stała szerokość i zagnieżdżone formaty, takie jak WinZip lub XML, nie są obsługiwane. Jeśli eksportujesz z SQL Server, możesz użyć [narzędzia wiersza polecenia BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby wyeksportować dane do rozdzielanych plików tekstowych.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. wyląduj dane do usługi Azure Blob Storage lub Azure Data Lake Store

Aby wystawić dane w usłudze Azure Storage, można przenieść je do [usługi Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). W każdej lokalizacji dane powinny być przechowywane w plikach tekstowych. Instrukcji Base i COPY można załadować z jednej z lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- Usługa [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zwiększa przepustowość sieci, wydajność i przewidywalność. ExpressRoute to usługa, która przekierowuje dane za pomocą dedykowanego połączenia prywatnego z platformą Azure. Połączenia ExpressRoute nie kierują danych za pomocą publicznego Internetu. Połączenia oferują większą niezawodność, większe szybkości, krótsze opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez publiczny Internet.
- [Narzędzie AzCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiar danych jest mniejszy niż 10 TB. Aby przeprowadzić regularne ładowanie w programie AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest ona akceptowalna.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) zawiera bramę, którą można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z analizą SQL, zobacz [ładowanie danych do usługi SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotuj dane do załadowania

Przed załadowaniem programu może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu. Przygotowanie danych można wykonać, gdy dane są przechowywane w źródle, podczas eksportowania danych do plików tekstowych lub po utworzeniu danych w usłudze Azure Storage.  Najłatwiej pracujesz z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Jeśli używasz bazy danych Base, musisz zdefiniować tabele zewnętrzne w puli SQL przed załadowaniem. Tabele zewnętrzne nie są wymagane przez instrukcję COPY. Baza danych używa tabel zewnętrznych w celu definiowania i uzyskiwania dostępu do nich w usłudze Azure Storage.

Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza pulą SQL.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabeli. Informacje o składni języka T-SQL, które są potrzebne, są następujące:

- [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Podczas ładowania Parquet mapowanie typu danych SQL jest następujące:

| **Parquet — typ danych** | **Typ danych SQL** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        wartość logiczna        |        bit        |
|        double         |       float       |
|         float         |       liczba rzeczywista        |
|        double         |       pieniędzy       |
|        double         |    smallmoney     |
|        ciąg         |       nchar       |
|        ciąg         |     nvarchar      |
|        ciąg         |       char        |
|        ciąg         |      varchar      |
|        binarny         |      binarny       |
|        binarny         |     varbinary     |
|       sygnatura czasowa       |       date        |
|       sygnatura czasowa       |   smalldatetime   |
|       sygnatura czasowa       |     datetime2     |
|       sygnatura czasowa       |     datetime      |
|       sygnatura czasowa       |       time        |
|         date          |       date        |
|        decimal        |      decimal      |

Aby zapoznać się z przykładem tworzenia obiektów zewnętrznych, zobacz krok [Tworzenie tabel zewnętrznych](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) w samouczku ładowania.

### <a name="format-text-files"></a>Formatowanie plików tekstowych

W przypadku korzystania z bazy Base, zdefiniowane obiekty zewnętrzne muszą wyrównać wiersze plików tekstowych z definicją tabeli zewnętrznej i formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą z nierelacyjnego źródła, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w celu dopasowania z definicjami kolumn dla tabeli, do której mają zostać załadowane dane.
- Sformatuj dane w pliku tekstowym, aby wyrównać je do kolumn i typów danych w tabeli docelowej. Niezgodność między typami danych w zewnętrznych plikach tekstowych a tabelą puli SQL powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym z terminatorem.  Upewnij się, że używasz znaku lub sekwencji znaków, która nie została znaleziona w danych źródłowych. Użyj terminatora określonego przy użyciu parametru [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Załaduj dane przy użyciu bazy danych lub instrukcji COPY

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania daje również możliwość użycia funkcji MPP puli SQL na potrzeby przekształceń danych przed wstawieniem danych do tabel produkcyjnych.

Tabela musi być wstępnie utworzona podczas ładowania do tabeli przemieszczania z KOPIą.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opcje ładowania z użyciem instrukcji Base i COPY

Aby załadować dane za pomocą bazy danych Base, można użyć dowolnej z następujących opcji ładowania:

- Baza danych w języku [T-SQL](load-data-from-azure-blob-storage-using-polybase.md) działa prawidłowo, gdy dane są przechowywane w usłudze Azure Blob storage lub Azure Data Lake Store. Zapewnia ona największą kontrolę nad procesem ładowania, ale wymaga również zdefiniowania zewnętrznych obiektów danych. Inne metody definiują te obiekty w tle podczas mapowania tabel źródłowych do tabel docelowych.  Aby zorganizować obciążenia T-SQL, można użyć Azure Data Factory, SSIS lub Azure Functions.
- [Baza](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) danych programu SSIS działa dobrze, gdy dane źródłowe są w SQL Server, SQL Server lokalnie lub w chmurze. Program SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby współpracowały z nowym miejscem docelowym magazynu danych.
- [Instrukcja "Base" i "Copy" z Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) to inne narzędzie aranżacji.  Definiuje potok i planuje zadania.
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
