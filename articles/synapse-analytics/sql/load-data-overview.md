---
title: Projektowanie strategii ładowania danych podstawowych dla dedykowanej puli SQL
description: Zamiast ETL Zaprojektuj proces wyodrębniania, ładowania i przekształcania (ELT) na potrzeby ładowania danych za pomocą dedykowanego języka SQL.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 850c03d5cbda8fb11bbc804f2cd2c848a5a2411f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589503"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Projektowanie strategii ładowania danych podstawowych dla dedykowanej puli SQL w usłudze Azure Synapse Analytics

Tradycyjne magazyny danych SMP używają procesu wyodrębniania, transformacji i ładowania (ETL) do ładowania danych. Usługa Azure SQL Pool to architektura wysoce równoległego przetwarzania (MPP), która korzysta z skalowalności i elastyczności zasobów obliczeniowych i magazynu. Korzystanie z procesu wyodrębniania, ładowania i przekształcania (ELT) może korzystać z wbudowanych funkcji przetwarzania zapytań rozproszonych i eliminowania zasobów wymaganych do przekształcenia danych przed ich załadowaniem.

Chociaż Pula SQL obsługuje wiele metod ładowania, w tym opcje inne niż podstawowe, takie jak BCP i SQL docelowa elementu BulkCopy API, najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie bazy.  Baza kodu jest technologią, która uzyskuje dostęp do danych zewnętrznych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Store za pośrednictwem języka T-SQL.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Wyodrębnij, Załaduj i Przekształć (ELT)

Wyodrębnij, Załaduj i Przekształć (ELT) to proces polegający na tym, że dane są wyodrębniane z systemu źródłowego, ładowane do magazynu danych, a następnie przekształcane.

Podstawowe kroki implementacji ELT Base dla dedykowanej puli SQL są następujące:

1. Wyodrębnij dane źródłowe do plików tekstowych.
2. Wydziel dane do usługi Azure Blob Storage lub Azure Data Lake Store.
3. Przygotuj dane do załadowania.
4. Załaduj dane do dedykowanych tabel tymczasowych puli SQL przy użyciu bazy danych.
5. Przekształć dane.
6. Wstaw dane do tabel produkcyjnych.

Aby zapoznać się z samouczkiem dotyczącym ładowania, zobacz temat Tworzenie [danych z usługi Azure Blob Storage do usługi Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Aby uzyskać więcej informacji, zobacz Blog dotyczący [ładowania wzorców](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Wyodrębnij dane źródłowe do plików tekstowych

Pobieranie danych z systemu źródłowego zależy od lokalizacji magazynu.  Celem jest przeniesienie danych do obsługiwanych, rozdzielonych plików tekstowych.

### <a name="polybase-external-file-formats"></a>Podstawowe formaty plików zewnętrznych

Baza danych ładuje dane z rozdzielanych plików tekstowych UTF-8 i UTF-16. Oprócz rozdzielanych plików tekstowych są one ładowane z plików z formatami usługi Hadoop, ORC i Parquet. Baza danych może również ładować dane ze strumienia gzip i plików skompresowanych. Baza danych nie obsługuje obecnie rozszerzonego formatu ASCII, stałej szerokości ani formatów zagnieżdżonych, takich jak WinZip, JSON i XML.

Jeśli eksportujesz z SQL Server, możesz użyć [narzędzia wiersza polecenia BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) , aby wyeksportować dane do rozdzielanych plików tekstowych. Mapowanie typu danych Parquet do usługi Azure Synapse Analytics jest następujące:

| **Parquet — typ danych** |                      **Typ danych SQL**                       |
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

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. wyląduj dane do usługi Azure Blob Storage lub Azure Data Lake Store

Aby wystawić dane w usłudze Azure Storage, można przenieść je do [usługi Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) lub [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). W każdej lokalizacji dane powinny być przechowywane w plikach tekstowych. Baza Base może ładować z jednej lokalizacji.

Narzędzia i usługi, których można użyć do przenoszenia danych do usługi Azure Storage:

- Usługa [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) zwiększa przepustowość sieci, wydajność i przewidywalność. ExpressRoute to usługa, która przekierowuje dane za pomocą dedykowanego połączenia prywatnego z platformą Azure. Połączenia ExpressRoute nie kierują danych za pomocą publicznego Internetu. Połączenia oferują większą niezawodność, większe szybkości, krótsze opóźnienia oraz lepsze zabezpieczenia niż typowe połączenia przez publiczny Internet.
- [Narzędzie AzCopy](../../storage/common/storage-use-azcopy-v10.md) przenosi dane do usługi Azure Storage za pośrednictwem publicznego Internetu. To działa, jeśli rozmiar danych jest mniejszy niż 10 TB. Aby przeprowadzić regularne ładowanie w programie AZCopy, przetestuj szybkość sieci, aby sprawdzić, czy jest ona akceptowalna.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) zawiera bramę, którą można zainstalować na serwerze lokalnym. Następnie możesz utworzyć potok, aby przenieść dane z serwera lokalnego do usługi Azure Storage. Aby użyć Data Factory z dedykowaną pulą SQL, zobacz [ładowanie danych do dedykowanej puli SQL](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Przygotuj dane do załadowania

Może być konieczne przygotowanie i oczyszczenie danych na koncie magazynu przed załadowaniem ich do dedykowanej puli SQL. Przygotowanie danych można wykonać, gdy dane są przechowywane w źródle, podczas eksportowania danych do plików tekstowych lub po utworzeniu danych w usłudze Azure Storage.  Najłatwiej pracujesz z danymi tak wcześnie w procesie, jak to możliwe.  

### <a name="define-external-tables"></a>Definiowanie tabel zewnętrznych

Przed załadowaniem danych należy zdefiniować tabele zewnętrzne w magazynie danych. Baza danych używa tabel zewnętrznych w celu definiowania i uzyskiwania dostępu do nich w usłudze Azure Storage. Tabela zewnętrzna jest podobna do widoku bazy danych. Tabela zewnętrzna zawiera schemat tabeli i wskazuje dane przechowywane poza magazynem danych.

Definiowanie tabel zewnętrznych obejmuje określenie źródła danych, formatu plików tekstowych i definicji tabeli. Poniżej przedstawiono tematy składni języka T-SQL, które będą potrzebne:

- [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formatowanie plików tekstowych

Po zdefiniowaniu obiektów zewnętrznych należy wyrównać wiersze plików tekstowych z tabelą zewnętrzną i definicją formatu pliku. Dane w każdym wierszu pliku tekstowego muszą być wyrównane z definicją tabeli.
Aby sformatować pliki tekstowe:

- Jeśli dane pochodzą z nierelacyjnego źródła, należy przekształcić je w wiersze i kolumny. Niezależnie od tego, czy dane pochodzą ze źródła relacyjnego, czy nierelacyjnego, dane muszą zostać przekształcone w celu dopasowania z definicjami kolumn dla tabeli, do której mają zostać załadowane dane.
- Sformatuj dane w pliku tekstowym, aby wyrównać je do kolumn i typów danych w tabeli docelowej puli SQL. Niezgodność między typami danych w zewnętrznych plikach tekstowych i tabeli magazynu danych powoduje odrzucenie wierszy podczas ładowania.
- Oddziel pola w pliku tekstowym z terminatorem.  Upewnij się, że używasz znaku lub sekwencji znaków, która nie została znaleziona w danych źródłowych. Użyj terminatora określonego przy użyciu parametru [Create External File Format](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Załaduj dane do dedykowanych tabel tymczasowych puli SQL przy użyciu bazy danych

Najlepszym rozwiązaniem jest załadowanie danych do tabeli przejściowej. Tabele przemieszczania umożliwiają obsługę błędów bez zakłócania pracy z tabelami produkcyjnymi. Tabela przemieszczania daje również możliwość użycia wbudowanych funkcji przetwarzania zapytań w puli SQL na potrzeby przekształceń danych przed wstawieniem danych do tabel produkcyjnych.

### <a name="options-for-loading-with-polybase"></a>Opcje ładowania z bazą

Aby załadować dane za pomocą bazy danych Base, można użyć dowolnej z następujących opcji ładowania:

- Baza danych w języku [T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) działa prawidłowo, gdy dane są przechowywane w usłudze Azure Blob storage lub Azure Data Lake Store. Zapewnia ona największą kontrolę nad procesem ładowania, ale wymaga również zdefiniowania zewnętrznych obiektów danych. Inne metody definiują te obiekty w tle podczas mapowania tabel źródłowych do tabel docelowych.  Aby zorganizować obciążenia T-SQL, można użyć Azure Data Factory, SSIS lub Azure Functions.
- [Baza](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) danych programu SSIS działa prawidłowo, gdy dane źródłowe są w SQL Server. Program SSIS definiuje mapowania tabeli źródłowej do docelowej, a także organizuje obciążenie. Jeśli masz już pakiety SSIS, możesz zmodyfikować pakiety, aby współpracowały z nowym miejscem docelowym magazynu danych.
- [Baza z Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) to inne narzędzie aranżacji.  Definiuje potok i planuje zadania.
- [Baza danych z Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) transferuje dane z tabeli usługi Azure Synapse Analytics do elementu datakostks i/lub zapisuje dane z tabeli Databases dataframes na tabelę usługi Azure Synapse Analytics przy użyciu bazy danych.

### <a name="non-polybase-loading-options"></a>Opcje ładowania inne niż podstawowe

Jeśli dane nie są zgodne z bazą danych, można użyć narzędzia [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) lub [interfejsu API SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy). BCP ładuje się bezpośrednio do dedykowanej puli SQL bez przechodzenia przez usługę Azure Blob Storage i jest przeznaczony tylko do małych obciążeń. Należy pamiętać, że wydajność ładowania tych opcji jest znacznie mniejsza niż baza Base.

## <a name="5-transform-the-data"></a>5. Przekształć dane

Gdy dane są w tabeli przemieszczania, należy wykonać przekształcenia wymagane przez obciążenie. Następnie Przenieś dane do tabeli produkcyjnej.

## <a name="6-insert-the-data-into-production-tables"></a>6. Wstaw dane do tabel produkcyjnych

Wstaw do... Instrukcja SELECT przenosi dane z tabeli przemieszczania do trwałej tabeli.

Podczas projektowania procesu ETL spróbuj uruchomić proces przy użyciu małego przykładu testowego. Spróbuj wyodrębnić 1000 wierszy z tabeli do pliku, przenieść go na platformę Azure, a następnie spróbować załadować go do tabeli przejściowej.

## <a name="partner-loading-solutions"></a>Rozwiązania do ładowania partnerów

Wielu naszych partnerów ma załadowane rozwiązania. Aby dowiedzieć się więcej, zobacz listę naszych [partnerów rozwiązań](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące ładowania, zobacz [wskazówki dotyczące ładowania danych](data-loading-best-practices.md).