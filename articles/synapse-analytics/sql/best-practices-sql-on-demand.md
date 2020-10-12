---
title: Najlepsze rozwiązania dotyczące programu SQL na żądanie (wersja zapoznawcza)
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z usługą SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7bebfeba6da1493557d51777ba8438747e160750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476278"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące usług SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zbiór najlepszych rozwiązań dotyczących korzystania z programu SQL na żądanie (wersja zapoznawcza). SQL na żądanie jest zasobem w usłudze Azure Synapse Analytics.

## <a name="general-considerations"></a>Zagadnienia ogólne

Usługa SQL na żądanie umożliwia wykonywanie zapytań dotyczących plików na kontach usługi Azure Storage. Nie ma ona lokalnego magazynu ani możliwości pozyskiwania. Dlatego wszystkie pliki docelowe zapytania są zewnętrzne na żądanie. Wszystkie elementy związane z odczytem plików z magazynu mogą mieć wpływ na wydajność zapytań.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Lokalizowanie konta usługi Azure Storage i SQL na żądanie

Aby zminimalizować opóźnienie, umieść konto usługi Azure Storage i punkt końcowy na żądanie SQL. Konta magazynu i punkty końcowe inicjowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, Jeśli uzyskujesz dostęp do innych kont magazynu za pomocą programu SQL na żądanie, upewnij się, że znajdują się one w tym samym regionie. Jeśli nie znajdują się w tym samym regionie, nastąpi zwiększone opóźnienie transferu sieciowego danych między regionem zdalnym a regionem punktu końcowego.

## <a name="azure-storage-throttling"></a>Ograniczanie usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie magazynu odbywa się, gdy połączone operacje we/wy są generowane przez aplikacje, usługi i obciążenie na żądanie SQL, przekraczają limity konta magazynu. W efekcie wystąpi znaczący negatywny wpływ na wydajność zapytań.

W przypadku wykrycia ograniczenia przepustowości SQL na żądanie ma wbudowaną obsługę rozwiązania. Program SQL na żądanie będzie przesyłał żądania do magazynu w wolniejszym tempie, dopóki ograniczanie zostanie rozwiązane.

> [!TIP]
> W celu zapewnienia optymalnego wykonywania zapytań nie należy naciskać konta magazynu z innymi obciążeniami podczas wykonywania zapytania.

## <a name="prepare-files-for-querying"></a>Przygotuj pliki do zapytania

Jeśli to możliwe, można przygotować pliki w celu uzyskania lepszej wydajności:

- Konwertuj pliki CSV i JSON na Parquet. Parquet jest formatem kolumnowym. Ponieważ jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV lub JSON zawierające te same dane. Do odczytu na żądanie SQL wymagane są mniej czasu i mniejsze żądania magazynu.
- Jeśli zapytanie odwołuje się do pojedynczego dużego pliku, można je podzielić na kilka mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV poniżej 10 GB.
- Lepiej jest mieć pliki o równym rozmiarze dla jednej ścieżki OPENROWSET lub lokalizacji tabeli zewnętrznej.
- Podziel dane przez przechowywanie partycji w różnych folderach lub nazwach plików. Aby dowiedzieć [się więcej na temat, zobacz use filename i FilePath Functions](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Wypchnij symbole wieloznaczne do niższych poziomów w ścieżce

W ścieżce można używać symboli wieloznacznych, aby [wykonywać zapytania dotyczące wielu plików i folderów](query-data-storage.md#query-multiple-files-or-folders). Program SQL na żądanie wyświetla listę plików na koncie magazynu, rozpoczynając od pierwszego * przy użyciu interfejsu API usługi Storage. Eliminuje pliki, które nie pasują do określonej ścieżki. Zmniejszenie początkowej listy plików może zwiększyć wydajność, jeśli istnieje wiele plików zgodnych z określoną ścieżką do pierwszego symbolu wieloznacznego.

## <a name="use-appropriate-data-types"></a>Użyj odpowiednich typów danych

Typy danych używane w zapytaniu mają wpływ na wydajność. Aby uzyskać lepszą wydajność, należy postępować zgodnie z poniższymi wskazówkami: 

- Użyj najmniejszego rozmiaru danych, który będzie uwzględniać największą możliwą wartość.
  - Jeśli maksymalna długość wartości znakowej to 30 znaków, należy użyć typu danych znak o długości 30.
  - Jeśli wszystkie wartości kolumny znaków mają stały rozmiar, użyj **znaków** lub **nchar**. W przeciwnym razie użyj **varchar** lub **nvarchar**.
  - Jeśli maksymalna wartość kolumny liczba całkowita to 500, użyj wartości **smallint** , ponieważ jest to najmniejszy typ danych, który może posłużyć tej wartości. Zakresy typu danych Integer można znaleźć w [tym artykule](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15).
- Jeśli to możliwe, użyj **varchar** i **char** zamiast **nvarchar** i **nchar**.
- Jeśli to możliwe, używaj typów danych opartych na liczbie całkowitej. Operacje sortowania, przyłączania i grupowania według operacji są wykonywane szybciej na liczbach całkowitych niż w przypadku danych znakowych.
- Jeśli używasz wnioskowania schematu, [Sprawdź wywnioskowane typy danych](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Sprawdź wywnioskowane typy danych

[Wnioskowanie schematu](query-parquet-files.md#automatic-schema-inference) ułatwia szybkie Pisanie zapytań i Eksplorowanie danych bez znajomości schematów plików. Koszt tej wygody polega na tym, że wywnioskowane typy danych są większe niż rzeczywiste typy danych. Dzieje się tak, gdy w plikach źródłowych nie ma wystarczającej ilości informacji, aby upewnić się, że używany jest odpowiedni typ danych. Na przykład pliki Parquet nie zawierają metadanych o maksymalnej długości kolumny znakowej. Dzięki temu program SQL na żądanie rozpoznaje go jako varchar (8000).

Za pomocą [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) można sprawdzić typy danych wyników zapytania.

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

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Użyj funkcji filename i FilePath, aby określić docelowe partycje

Dane często są zorganizowane w partycjach. Można wydać instrukcję SQL na żądanie, aby wykonywać zapytania dotyczące określonych folderów i plików. Spowoduje to zmniejszenie liczby plików i ilości danych, które zapytanie musi odczytać i przetworzyć. Dodatkową premią jest osiągnięcie lepszej wydajności.

Aby uzyskać więcej informacji, Przeczytaj o funkcjach [filename](query-data-storage.md#filename-function) i [FilePath](query-data-storage.md#filepath-function) oraz Zobacz przykłady dotyczące [wykonywania zapytań dotyczących określonych plików](query-specific-files.md).

> [!TIP]
> Zawsze należy rzutować wyniki funkcji FilePath i filename na odpowiednie typy danych. Jeśli używasz typów danych znakowych, upewnij się, że używasz odpowiedniej długości.

> [!NOTE]
> Funkcje używane do usuwania partycji, FilePath i filename nie są obecnie obsługiwane w przypadku tabel zewnętrznych, innych niż te utworzone automatycznie dla każdej tabeli utworzonej w Apache Spark dla usługi Azure Synapse Analytics.

Jeśli przechowywane dane nie są podzielone na partycje, rozważ ich partycjonowanie. W ten sposób można używać tych funkcji do optymalizowania zapytań przeznaczonych dla tych plików. Podczas [wykonywania zapytania dotyczącego Apache Spark partycjonowanych tabel usługi Azure Synapse](develop-storage-files-spark-tables.md) z poziomu bazy danych SQL na żądanie zapytanie będzie automatycznie kierować tylko do potrzebnych plików.

## <a name="use-parser_version-20-to-query-csv-files"></a>Używanie PARSER_VERSION 2,0 do wykonywania zapytań w plikach CSV

Analizatora zoptymalizowanych pod kątem wydajności można użyć podczas wykonywania zapytań dotyczących plików CSV. Aby uzyskać szczegółowe informacje, zobacz [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Korzystanie z CETAS w celu zwiększenia wydajności zapytań i sprzężeń

[CETAS](develop-tables-cetas.md) to jedna z najważniejszych funkcji dostępnych w programie SQL na żądanie. CETAS to równoległa operacja, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki zapytania SELECT do zestawu plików na koncie magazynu.

Można użyć CETAS do przechowywania często używanych części zapytań, takich jak sprzężone tabele odwołań, do nowego zestawu plików. Następnie można dołączyć do tej pojedynczej tabeli zewnętrznej zamiast powtarzających się wspólnych sprzężeń w wielu zapytaniach.

Ponieważ CETAS generuje pliki Parquet, statystyki zostaną automatycznie utworzone, gdy pierwsze zapytanie odwołuje się do tej tabeli zewnętrznej, co spowodowało zwiększenie wydajności.

## <a name="azure-ad-pass-through-performance"></a>Wydajność przekazująca usługi Azure AD

Usługa SQL na żądanie umożliwia dostęp do plików w usłudze Storage przy użyciu poświadczeń przekazujących lub opartych na usłudze Azure Active Directory (Azure AD). Użycie funkcji przekazywania w usłudze Azure AD może być wolniejsze niż w przypadku korzystania z SAS.

Jeśli potrzebujesz lepszej wydajności, spróbuj użyć poświadczeń SAS, aby uzyskać dostęp do magazynu, dopóki nie zostanie zwiększona wydajność usługi Azure AD Pass-through.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [rozwiązywania problemów](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać informacje o typowych problemach. Jeśli pracujesz z pulami SQL, a nie SQL na żądanie, zobacz [najlepsze rozwiązania dotyczące pul SQL](best-practices-sql-pool.md) , aby uzyskać szczegółowe wskazówki.
