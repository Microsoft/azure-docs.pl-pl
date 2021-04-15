---
title: Najlepsze rozwiązania dotyczące bez serwera puli SQL
description: Zalecenia i najlepsze rozwiązania dotyczące pracy z bez serwera pulą SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477472"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące bez serwera puli SQL w programie Azure Synapse Analytics

W tym artykule znajdziesz kolekcję najlepszych rozwiązań w zakresie korzystania z bez serwera puli SQL. Bez serwera pula SQL jest zasobem w Azure Synapse Analytics.

Bez serwera pula SQL umożliwia wykonywanie zapytań o pliki na kontach usługi Azure Storage. Nie ma możliwości lokalnego magazynowania ani pozyskiwania danych. Dlatego wszystkie pliki, których obiekty docelowe zapytania znajdują się poza bez serwera, znajdują się w puli SQL bez serwera. Wszystkie informacje związane z odczytywaniem plików z magazynu mogą mieć wpływ na wydajność zapytań.

Niektóre ogólne wskazówki są:
- Upewnij się, że aplikacje klienckie są koloczone z bez serwera pulą SQL.
  - Jeśli używasz aplikacji klienckich spoza platformy Azure (na przykład Power BI Desktop, SSMS, ADS), upewnij się, że używasz puli bez serwera w regionie, który znajduje się w pobliżu komputera klienckiego.
- Upewnij się, że magazyn (Azure Data Lake, Cosmos DB) i bez serwera pula SQL znajdują się w tym samym regionie.
- Spróbuj [zoptymalizować układ magazynu przy](#prepare-files-for-querying) użyciu partycjonowania i przechowywania plików w zakresie od 100 MB do 10 GB.
- Jeśli zwracasz dużą liczbę wyników, upewnij się, że używasz programu SSMS lub ads, a nie Synapse Studio. Synapse Studio to narzędzie internetowe, które nie jest przeznaczone dla dużych zestawów wyników. 
- Jeśli filtrujesz wyniki według kolumny ciągu, spróbuj użyć `BIN2_UTF8` sortowanie.
- Spróbuj buforować wyniki po stronie klienta przy użyciu trybu Power BI importem lub Azure Analysis Services i okresowo je odświeżać. Bez serwerowe pule SQL nie mogą zapewnić interaktywnego Power BI trybie direct query, jeśli używasz złożonych zapytań lub przetwarzasz dużą ilość danych.

## <a name="client-applications-and-network-connections"></a>Aplikacje klienckie i połączenia sieciowe

Upewnij się, że aplikacja kliency jest połączona z najbliższym możliwym obszarem roboczym usługi Synapse z optymalnym połączeniem.
- Kolokowanie aplikacji klienckiej z obszarem roboczym usługi Synapse. Jeśli używasz aplikacji, takich jak Power BI lub Azure Analysis Service, upewnij się, że znajdują się one w tym samym regionie, w którym został umieszczony obszar roboczy usługi Synapse. W razie potrzeby utwórz oddzielne obszary robocze sparowane z aplikacjami klienckimi. Umieszczenie aplikacji klienckiej i obszaru roboczego usługi Synapse w innym regionie może spowodować większe opóźnienie i wolniejsze przesyłanie strumieniowe wyników.
- Jeśli odczytujesz dane z aplikacji lokalnej, upewnij się, że obszar roboczy usługi Synapse znajduje się w regionie, który znajduje się w pobliżu Twojej lokalizacji.
- Upewnij się, że nie masz problemów z przepustowością sieci podczas odczytywania dużej ilości danych.
- Nie należy używać programu Synapse Studio do zwracania dużej ilości danych. Synapse Studio to narzędzie internetowe, które używa protokołu HTTPS do transferu danych. Użyj Azure Data Studio lub SQL Server Management Studio, aby odczytać dużą ilość danych.

## <a name="storage-and-content-layout"></a>Układ magazynu i zawartości

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Kolokowanie magazynu i bez serwera puli SQL

Aby zminimalizować opóźnienie, kolokowanie konta usługi Azure Storage lub magazynu analitycznego usługi CosmosDB i bez serwera punktu końcowego puli SQL. Konta magazynu i punkty końcowe aprowowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, jeśli uzyskujesz dostęp do innych kont magazynu przy użyciu bez serwera puli SQL, upewnij się, że są one w tym samym regionie. Jeśli nie znajduje się w tym samym regionie, będzie większe opóźnienie transferu sieci danych między regionem zdalnym a regionem punktu końcowego.

### <a name="azure-storage-throttling"></a>Ograniczanie przepustowości usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie przepustowości magazynu występuje, gdy łączna wartość IOPS lub przepływność wygenerowana przez aplikacje, usługi i bez serwera obciążenie puli SQL przekracza limity konta magazynu. W rezultacie wystąpi znaczący negatywny wpływ na wydajność zapytań.

W przypadku wykrycia ograniczania przepływności bez serwera bez serwera pula SQL ma wbudowaną obsługę w celu rozwiązania tego problemu. Bez serverless SQL pool will make requests to storage at a slower pace until throttling is resolved.

> [!TIP]
> Aby uzyskać optymalne wykonywanie zapytań, nie należy podkreślać konta magazynu z innymi obciążeniami podczas wykonywania zapytania.

### <a name="azure-ad-pass-through-performance"></a>Wydajność przechodniej usługi Azure AD

Bez serwera pula SQL umożliwia dostęp do plików w magazynie przy użyciu poświadczeń Azure Active Directory (Azure AD) pass-through lub SAS. W przypadku przechodnia usługi Azure AD wydajność może być niższa niż w przypadku sygnatury dostępu współdzielonego.

Jeśli potrzebujesz lepszej wydajności, spróbuj użyć poświadczeń SAS w celu uzyskania dostępu do magazynu.

### <a name="prepare-files-for-querying"></a>Przygotowywanie plików do wykonywania zapytań

Jeśli to możliwe, możesz przygotować pliki w celu lepszej wydajności:

- Przekonwertuj duże pliki CSV i JSON na parquet. Parquet jest formatem kolumnowym. Ponieważ plik jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV lub JSON zawierające te same dane. Bez serwera pula SQL może pominąć kolumny i wiersze, które nie są potrzebne w zapytaniu, jeśli odczytujesz pliki Parquet. Bez serwera pula SQL będzie potrzebować mniej czasu i mniejszej liczby żądań magazynu, aby ją odczytać.
- Jeśli zapytanie dotyczy jednego dużego pliku, korzystne będzie podzielenie go na wiele mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV od 100 MB do 10 GB.
- Lepiej mieć pliki o równym rozmiarze dla pojedynczej ścieżki OPENROWSET lub lokalizacji tabeli zewnętrznej.
- Partycjonowanie danych przez przechowywanie partycji w różnych folderach lub nazwach plików. Zobacz [Use filename and filepath functions to target specific partitions (Używanie funkcji filename i filepath do określonych partycji).](#use-filename-and-filepath-functions-to-target-specific-partitions)

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>Kolokowanie magazynu analitycznego usługi CosmosDB i bez serwera puli SQL

Upewnij się, że magazyn analityczny usługi CosmosDB jest umieszczony w tym samym regionie co obszar roboczy usługi Synapse. Zapytania między regionami mogą powodować ogromne opóźnienia. Użyj właściwości regionu w parametrów połączenia, aby jawnie określić region, w którym znajduje się magazyn analityczny (zobacz query [CosmosDb using serverless SQL pool](query-cosmos-db-analytical-store.md#overview)(Wykonywanie zapytań w bazie danych CosmosDb przy użyciu bez serwera puli SQL):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>Optymalizacje woluminów CSV

### <a name="use-parser_version-20-to-query-csv-files"></a>Używanie PARSER_VERSION 2.0 do wykonywania zapytań o pliki CSV

Podczas wykonywania zapytań o pliki CSV można użyć parsera zoptymalizowanego pod kątem wydajności. Aby uzyskać szczegółowe informacje, [zobacz PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Ręczne tworzenie statystyk dla plików CSV

Bez serwera pula SQL bazuje na statystykach w celu wygenerowania optymalnych planów wykonywania zapytań. W razie potrzeby statystyki dla kolumn w plikach Parquet będą tworzone automatycznie. W tej chwili statystyki nie są automatycznie tworzone dla kolumn w plikach CSV. Należy utworzyć statystyki ręcznie dla kolumn używanych w zapytaniach, szczególnie tych używanych w kolumnach DISTINCT, JOIN, WHERE, ORDER BY i GROUP BY. Sprawdź [statystyki w bezserowej puli SQL, aby](develop-tables-statistics.md#statistics-in-serverless-sql-pool) uzyskać szczegółowe informacje.


## <a name="data-types"></a>Typy danych

### <a name="use-appropriate-data-types"></a>Używanie odpowiednich typów danych

Typy danych, których używasz w zapytaniu, wpływają na wydajność. Lepszą wydajność można uzyskać, jeśli są zgodne z tymi wytycznymi: 

- Użyj najmniejszego rozmiaru danych, który obsłuży największą możliwą wartość.
  - Jeśli maksymalna długość wartości znaku wynosi 30 znaków, użyj typu danych znakowych o długości 30 znaków.
  - Jeśli wszystkie wartości kolumn znaków mają stały rozmiar, użyj **znaków lub** **nchar**. W przeciwnym razie użyj **varchar** **lub nvarchar**.
  - Jeśli maksymalna wartość w kolumnie liczb całkowitych wynosi 500, użyj wartości **smallint,** ponieważ jest to najmniejszy typ danych, który może obsłużyć tę wartość. Zakresy typów danych liczb całkowitych można znaleźć w [tym artykule.](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- Jeśli to możliwe, użyj **varchar** i **char** zamiast **nvarchar** i **nchar**.
- Jeśli to możliwe, używaj typów danych opartych na liczbach całkowitych. Operacje SORT, JOIN i GROUP BY są wykonywane szybciej na liczbach całkowitych niż na danych znakowych.
- Jeśli używasz wnioskowania schematu, sprawdź [wywnioskowane typy danych](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Sprawdzanie wywnioskować typów danych

[Wnioskowanie schematu ułatwia](query-parquet-files.md#automatic-schema-inference) szybkie pisanie zapytań i eksplorowanie danych bez znajomości schematów plików. Kosztem tej wygody jest to, że wywnioskowane typy danych mogą być większe niż rzeczywiste typy danych. Dzieje się tak, gdy w plikach źródłowych jest za mało informacji, aby upewnić się, że używany jest odpowiedni typ danych. Na przykład pliki Parquet nie zawierają metadanych dotyczących maksymalnej długości kolumny znaków. Dlatego bez serwera pula SQL wywnioskuje ją jako varchar(8000).

Możesz użyć [sp_describe_first_results_set,](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) aby sprawdzić wynikowe typy danych zapytania.

W poniższym przykładzie pokazano, jak można zoptymalizować wywnioskowane typy danych. Ta procedura służy do pokazywania wywnioskować typów danych: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Oto zestaw wyników:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

Po poznaniu wywnioskować typów danych dla zapytania można określić odpowiednie typy danych:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Optymalizacja filtru

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Wypychanie symboli wieloznacznych do niższych poziomów w ścieżce

Możesz użyć symboli wieloznacznych w ścieżce, aby [odpytać wiele plików i folderów.](query-data-storage.md#query-multiple-files-or-folders) Bez serwera pula SQL wyświetla listę plików na koncie magazynu, począwszy od pierwszego * przy użyciu interfejsu API magazynu. Eliminuje to pliki, które nie pasują do określonej ścieżki. Zmniejszenie początkowej listy plików może poprawić wydajność, jeśli istnieje wiele plików, które pasują do określonej ścieżki aż do pierwszego symbolu wieloznacznego.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Używanie funkcji filename i filepath do kierowania określonych partycji

Dane są często zorganizowane w partycje. Możesz poinstruować bez serwera pulę SQL o wykonywanie zapytań o konkretne foldery i pliki. Spowoduje to zmniejszenie liczby plików i ilości danych, które zapytanie musi odczytać i przetworzyć. Dodatkową dodatkową wartością jest to, że można osiągnąć lepszą wydajność.

Aby uzyskać więcej informacji, przeczytaj o [funkcjach filename](query-data-storage.md#filename-function) i [filepath](query-data-storage.md#filepath-function) oraz zobacz przykłady dotyczące wykonywania zapytań [dotyczących określonych plików.](query-specific-files.md)

> [!TIP]
> Zawsze rzutuj wyniki funkcji filepath i filename na odpowiednie typy danych. Jeśli używasz typów danych znakowych, upewnij się, że używasz odpowiedniej długości.

> [!NOTE]
> Funkcje używane do eliminacji partycji, ścieżki pliku i nazwy pliku nie są obecnie obsługiwane w przypadku tabel zewnętrznych innych niż te tworzone automatycznie dla każdej tabeli utworzonej w Apache Spark for Azure Synapse Analytics.

Jeśli przechowywane dane nie są podzielone na partycje, rozważ ich partycjonowanie. Dzięki temu można używać tych funkcji do optymalizowania zapytań, które są kierowane do tych plików. Zapytanie podzielone [na partycje Apache Spark tabel Azure Synapse](develop-storage-files-spark-tables.md) z bez serwera puli SQL, zapytanie będzie automatycznie dotyczyć tylko niezbędnych plików.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Używanie odpowiedniego sortowania do korzystania z wypychania predykatu dla kolumn znaków

Dane w pliku Parquet są zorganizowane w grupy wierszy. Bez serwera pula SQL pomija grupy wierszy na podstawie określonego predykatu w klauzuli WHERE, co zmniejsza liczbę we/wy, co powoduje zwiększenie wydajności zapytań. 

Należy pamiętać, że wypychanie predykatu dla kolumn znaków w plikach Parquet jest obsługiwane tylko Latin1_General_100_BIN2_UTF8 sortowaniem. Sortowanie dla określonej kolumny można określić przy użyciu klauzuli WITH. Jeśli to sortowanie nie zostanie określone przy użyciu klauzuli WITH, zostanie użyte sortowanie bazy danych.

## <a name="optimize-repeating-queries"></a>Optymalizowanie powtarzających się zapytań

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Używanie funkcji CETAS w celu zwiększenia wydajności zapytań i sprzężenia

[Instrukcja CETAS](develop-tables-cetas.md) jest jedną z najważniejszych funkcji dostępnych w bez serwerach puli SQL. CETAS to równoległa operacja, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki zapytania SELECT do zestawu plików na koncie magazynu.

Za pomocą funkcji CETAS można zmaterializować często używane części zapytań, takie jak połączone tabele referencyjne, do nowego zestawu plików. Następnie można połączyć się z tą pojedynczą tabelą zewnętrzną zamiast powtarzać typowe sprzężenia w wielu zapytaniach.

Ponieważ aplikacja CETAS generuje pliki Parquet, statystyki będą tworzone automatycznie, gdy pierwsze zapytanie będzie dotyczyć tej tabeli zewnętrznej, co spowoduje zwiększenie wydajności kolejnych zapytań kierowanych do tabeli wygenerowanej za pomocą funkcji CETAS.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [artykułem rozwiązywania](resources-self-help-sql-on-demand.md) problemów, aby zapoznać się z rozwiązaniami typowych problemów. Jeśli pracujesz z dedykowaną pulą SQL, a nie bez serwera, zobacz Najlepsze rozwiązania dotyczące dedykowanych [pul SQL,](best-practices-dedicated-sql-pool.md) aby uzyskać szczegółowe wskazówki.
