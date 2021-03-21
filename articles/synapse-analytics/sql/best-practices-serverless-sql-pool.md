---
title: Najlepsze rozwiązania dotyczące puli SQL bezserwerowej
description: Zalecenia i najlepsze rozwiązania dotyczące pracy z pulą bezserwerową SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 75e187369eccefb255ae2bbd88de79afbc4fd4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669478"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące puli SQL bezserwerowej w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zbiór najlepszych rozwiązań dotyczących używania puli SQL bezserwerowej. Pula SQL bezserwerowa jest zasobem w usłudze Azure Synapse Analytics.

Pula SQL bezserwerowa umożliwia wykonywanie zapytań dotyczących plików na kontach usługi Azure Storage. Nie ma ona lokalnego magazynu ani możliwości pozyskiwania. Dlatego wszystkie pliki docelowe zapytań są spoza puli SQL bezserwerowej. Wszystkie elementy związane z odczytem plików z magazynu mogą mieć wpływ na wydajność zapytań.

## <a name="storage-and-content-layout"></a>Magazyn i układ zawartości

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Lokalizowanie puli SQL w magazynie i bezserwerowym

Aby zminimalizować opóźnienie, należy odszukać konto usługi Azure Storage lub CosmosDB magazyn analityczny oraz punkt końcowy puli SQL bezserwerowej. Konta magazynu i punkty końcowe inicjowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, Jeśli uzyskujesz dostęp do innych kont magazynu z pulą SQL bezserwerowa, upewnij się, że znajdują się one w tym samym regionie. Jeśli nie znajdują się w tym samym regionie, nastąpi zwiększone opóźnienie transferu sieciowego danych między regionem zdalnym a regionem punktu końcowego.

### <a name="azure-storage-throttling"></a>Ograniczanie usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie magazynu odbywa się, gdy połączone operacje we/wy są generowane przez aplikacje, usługi i obciążenie puli SQL bezserwerowe przekraczające limity konta magazynu. W efekcie wystąpi znaczący negatywny wpływ na wydajność zapytań.

Po wykryciu ograniczenia przepustowości Pula SQL bezserwerowa ma wbudowaną obsługę rozwiązania. Bezserwerowa Pula SQL będzie wykonywać żądania do magazynu w wolniejszym tempie, dopóki ograniczanie zostanie rozwiązane.

> [!TIP]
> W celu zapewnienia optymalnego wykonywania zapytań nie należy naciskać konta magazynu z innymi obciążeniami podczas wykonywania zapytania.

### <a name="azure-ad-pass-through-performance"></a>Wydajność przekazująca usługi Azure AD

Pula SQL bezserwerowa umożliwia dostęp do plików w magazynie przy użyciu funkcji przekazywania lub poświadczeń SAS usługi Azure Active Directory (Azure AD). Użycie funkcji przekazywania w usłudze Azure AD może być wolniejsze niż w przypadku korzystania z SAS.

Jeśli potrzebujesz lepszej wydajności, spróbuj użyć poświadczeń SAS, aby uzyskać dostęp do magazynu.

### <a name="prepare-files-for-querying"></a>Przygotuj pliki do zapytania

Jeśli to możliwe, można przygotować pliki w celu uzyskania lepszej wydajności:

- Przekonwertuj duże pliki CSV i JSON na Parquet. Parquet jest formatem kolumnowym. Ponieważ jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV lub JSON zawierające te same dane. Pula SQL bezserwerowa może pominąć kolumny i wiersze, które nie są potrzebne w zapytaniu, jeśli odczytujesz pliki Parquet. Bezserwerowa Pula SQL potrzebuje mniej czasu i mniejszą liczbę żądań magazynu, aby ją odczytać.
- Jeśli zapytanie odwołuje się do pojedynczego dużego pliku, można je podzielić na kilka mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV z zakresu od 100 MB do 10 GB.
- Lepiej jest mieć pliki o równym rozmiarze dla jednej ścieżki OPENROWSET lub lokalizacji tabeli zewnętrznej.
- Podziel dane przez przechowywanie partycji w różnych folderach lub nazwach plików. Aby dowiedzieć [się więcej na temat, zobacz use filename i FilePath Functions](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="csv-optimizations"></a>Optymalizacje CSV

### <a name="use-parser_version-20-to-query-csv-files"></a>Używanie PARSER_VERSION 2,0 do wykonywania zapytań w plikach CSV

Analizatora zoptymalizowanych pod kątem wydajności można użyć podczas wykonywania zapytań dotyczących plików CSV. Aby uzyskać szczegółowe informacje, zobacz [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>Ręczne tworzenie statystyk dla plików CSV

Pula SQL bezserwerowa opiera się na statystyce do generowania optymalnych planów wykonywania zapytań. W razie potrzeby dane statystyczne zostaną automatycznie utworzone dla kolumn w plikach Parquet. W tej chwili statystyki nie są tworzone automatycznie dla kolumn w plikach CSV i należy tworzyć statystyki ręcznie w przypadku kolumn używanych w zapytaniach, szczególnie w odniesieniu do tych, które są używane w różnych, sprzężeniach, gdzie, klauzuli ORDER BY i GROUP BY. Aby uzyskać szczegółowe informacje, sprawdź [statystyki w puli SQL bezserwerowej](develop-tables-statistics.md#statistics-in-serverless-sql-pool) .


## <a name="data-types"></a>Typy danych

### <a name="use-appropriate-data-types"></a>Użyj odpowiednich typów danych

Typy danych używane w zapytaniu mają wpływ na wydajność. Aby uzyskać lepszą wydajność, należy postępować zgodnie z poniższymi wskazówkami: 

- Użyj najmniejszego rozmiaru danych, który będzie uwzględniać największą możliwą wartość.
  - Jeśli maksymalna długość wartości znakowej to 30 znaków, należy użyć typu danych znak o długości 30.
  - Jeśli wszystkie wartości kolumny znaków mają stały rozmiar, użyj **znaków** lub **nchar**. W przeciwnym razie użyj **varchar** lub **nvarchar**.
  - Jeśli maksymalna wartość kolumny liczba całkowita to 500, użyj wartości **smallint** , ponieważ jest to najmniejszy typ danych, który może posłużyć tej wartości. Zakresy typu danych Integer można znaleźć w [tym artykule](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Jeśli to możliwe, użyj **varchar** i **char** zamiast **nvarchar** i **nchar**.
- Jeśli to możliwe, używaj typów danych opartych na liczbie całkowitej. Operacje sortowania, przyłączania i grupowania według operacji są wykonywane szybciej na liczbach całkowitych niż w przypadku danych znakowych.
- Jeśli używasz wnioskowania schematu, [Sprawdź wywnioskowane typy danych](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Sprawdź wywnioskowane typy danych

[Wnioskowanie schematu](query-parquet-files.md#automatic-schema-inference) ułatwia szybkie Pisanie zapytań i Eksplorowanie danych bez znajomości schematów plików. Koszt tej wygody polega na tym, że wywnioskowane typy danych mogą być większe niż rzeczywiste typy danych. Dzieje się tak, gdy w plikach źródłowych nie ma wystarczającej ilości informacji, aby upewnić się, że używany jest odpowiedni typ danych. Na przykład pliki Parquet nie zawierają metadanych o maksymalnej długości kolumny znakowej. Dlatego pula SQL bezserwerowa wnioskuje ją jako varchar (8000).

Za pomocą [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) można sprawdzić typy danych wyników zapytania.

Poniższy przykład pokazuje, jak można zoptymalizować wywnioskowane typy danych. Ta procedura służy do wyświetlania odroczonych typów danych: 
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
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Po poznaniu wywnioskowanych typów danych dla zapytania można określić odpowiednie typy danych:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Optymalizacja filtru

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Wypchnij symbole wieloznaczne do niższych poziomów w ścieżce

W ścieżce można używać symboli wieloznacznych, aby [wykonywać zapytania dotyczące wielu plików i folderów](query-data-storage.md#query-multiple-files-or-folders). Pula SQL bezserwerowa wyświetla listę plików na koncie magazynu, rozpoczynając od pierwszego * przy użyciu interfejsu API usługi Storage. Eliminuje pliki, które nie pasują do określonej ścieżki. Zmniejszenie początkowej listy plików może zwiększyć wydajność, jeśli istnieje wiele plików zgodnych z określoną ścieżką do pierwszego symbolu wieloznacznego.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Użyj funkcji filename i FilePath, aby określić docelowe partycje

Dane często są zorganizowane w partycjach. Można nakazać bezserwerową pulę SQL, aby wykonywać zapytania dotyczące określonych folderów i plików. Spowoduje to zmniejszenie liczby plików i ilości danych, które zapytanie musi odczytać i przetworzyć. Dodatkową premią jest osiągnięcie lepszej wydajności.

Aby uzyskać więcej informacji, Przeczytaj o funkcjach [filename](query-data-storage.md#filename-function) i [FilePath](query-data-storage.md#filepath-function) oraz Zobacz przykłady dotyczące [wykonywania zapytań dotyczących określonych plików](query-specific-files.md).

> [!TIP]
> Zawsze należy rzutować wyniki funkcji FilePath i filename na odpowiednie typy danych. Jeśli używasz typów danych znakowych, upewnij się, że używasz odpowiedniej długości.

> [!NOTE]
> Funkcje używane do usuwania partycji, FilePath i filename nie są obecnie obsługiwane w przypadku tabel zewnętrznych, innych niż te utworzone automatycznie dla każdej tabeli utworzonej w Apache Spark dla usługi Azure Synapse Analytics.

Jeśli przechowywane dane nie są podzielone na partycje, rozważ ich partycjonowanie. W ten sposób można używać tych funkcji do optymalizowania zapytań przeznaczonych dla tych plików. Podczas [wykonywania zapytania dotyczącego Apache Spark partycjonowanych tabel platformy Azure Synapse](develop-storage-files-spark-tables.md) z puli SQL bezserwerowej, zapytanie będzie automatycznie kierować tylko do potrzebnych plików.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>Użyj prawidłowego sortowania, aby wykorzystać przekazywanie predykatu dla kolumn znaków

Dane w pliku Parquet są zorganizowane w grupach wierszy. Pula SQL bezserwerowa pomija grupy wierszy na podstawie określonego predykatu w klauzuli WHERE, co zmniejsza wydajność operacji we/wy, co powoduje zwiększenie wydajności zapytań. 

Należy pamiętać, że przekazywanie predykatu dla kolumn znaków w plikach Parquet jest obsługiwane tylko w przypadku sortowania Latin1_General_100_BIN2_UTF8. Można określić sortowanie dla określonej kolumny przy użyciu klauzuli WITH. Jeśli nie określisz tego sortowania za pomocą klauzuli WITH, będzie używane sortowanie bazy danych.

## <a name="optimize-repeating-queries"></a>Optymalizowanie powtarzających się zapytań

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Korzystanie z CETAS w celu zwiększenia wydajności zapytań i sprzężeń

[CETAS](develop-tables-cetas.md) to jedna z najważniejszych funkcji dostępnych w puli SQL bez użycia serwera. CETAS to równoległa operacja, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki zapytania SELECT do zestawu plików na koncie magazynu.

Można użyć CETAS do zmaterializowania często używanych części zapytań, takich jak sprzężone tabele odwołań, do nowego zestawu plików. Następnie można dołączyć do tej pojedynczej tabeli zewnętrznej zamiast powtarzających się wspólnych sprzężeń w wielu zapytaniach.

Ponieważ CETAS generuje pliki Parquet, statystyki zostaną automatycznie utworzone, gdy pierwsze zapytanie odwołuje się do tej tabeli zewnętrznej, co spowodowało zwiększenie wydajności kolejnych zapytań tabeli docelowej wygenerowanej przy użyciu CETAS.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [rozwiązywania problemów](resources-self-help-sql-on-demand.md) , aby uzyskać informacje o typowych problemach. Jeśli pracujesz z dedykowaną pulą SQL, a nie bezserwerową pulą SQL, zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi dedykowanych pul SQL](best-practices-dedicated-sql-pool.md) , aby uzyskać szczegółowe wskazówki.
