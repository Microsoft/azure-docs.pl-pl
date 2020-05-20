---
title: Najlepsze rozwiązania dotyczące usług SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas pracy z usługą SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 07ceb8eebed5657f87417dc24281008dd0863851
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650393"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące usług SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zbiór najlepszych rozwiązań dotyczących korzystania z programu SQL na żądanie (wersja zapoznawcza). SQL na żądanie jest dodatkowym zasobem w usłudze Azure Synapse Analytics.

## <a name="general-considerations"></a>Zagadnienia ogólne

Usługa SQL na żądanie umożliwia wykonywanie zapytań dotyczących plików na kontach usługi Azure Storage. Nie ma ona lokalnego magazynu ani możliwości pozyskiwania. W związku z tym wszystkie pliki, których celem jest zapytanie, są zewnętrzne na żądanie SQL. Wszystkie elementy związane z odczytem plików z magazynu mogą mieć wpływ na wydajność zapytań.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Lokalizowanie konta usługi Azure Storage i SQL na żądanie

Aby zminimalizować opóźnienie, umieść konto usługi Azure Storage i punkt końcowy na żądanie SQL. Konta magazynu i punkty końcowe inicjowane podczas tworzenia obszaru roboczego znajdują się w tym samym regionie.

Aby uzyskać optymalną wydajność, Jeśli uzyskujesz dostęp do innych kont magazynu za pomocą programu SQL na żądanie, upewnij się, że znajdują się one w tym samym regionie. Jeśli nie znajdują się w tym samym regionie, nastąpi zwiększone opóźnienie transferu sieciowego danych między regionami zdalnymi i punktami końcowymi.

## <a name="azure-storage-throttling"></a>Ograniczanie usługi Azure Storage

Wiele aplikacji i usług może uzyskać dostęp do konta magazynu. Ograniczanie magazynu odbywa się, gdy połączone operacje we/wy są generowane przez aplikacje, usługi i obciążenie na żądanie SQL, przekraczają limity konta magazynu. W efekcie wystąpi znaczący negatywny wpływ na wydajność zapytań.

Po wykryciu ograniczenia przepustowości SQL na żądanie ma wbudowaną obsługę tego scenariusza. Program SQL na żądanie będzie przesyłał żądania do magazynu w wolniejszym tempie, dopóki ograniczanie zostanie rozwiązane.

> [!TIP]
> W celu zapewnienia optymalnego wykonywania zapytań nie należy naciskać konta magazynu z innymi obciążeniami podczas wykonywania zapytania.

## <a name="prepare-files-for-querying"></a>Przygotuj pliki do zapytania

Jeśli to możliwe, można przygotować pliki w celu uzyskania lepszej wydajności:

- Konwertowanie plików CSV i JSON na Parquet-Parquet ma format kolumnowy. Ponieważ jest skompresowany, jego rozmiary plików są mniejsze niż pliki CSV lub JSON z tymi samymi danymi. Do odczytu na żądanie SQL wymagane są mniej czasu i żądania pamięci masowej.
- Jeśli zapytanie odwołuje się do pojedynczego dużego pliku, można je podzielić na kilka mniejszych plików.
- Spróbuj zachować rozmiar pliku CSV poniżej 10 GB.
- Lepiej jest mieć pliki o równym rozmiarze dla jednej ścieżki OPENROWSET lub lokalizacji tabeli zewnętrznej.
- Podziel dane przez przechowywanie partycji w różnych folderach lub nazwach plików — zaznacz [opcję Użyj funkcji filename i FilePath, aby określić docelowe partycje](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Wypchnij symbole wieloznaczne do niższych poziomów w ścieżce

W ścieżce można używać symboli wieloznacznych, aby [wykonywać zapytania dotyczące wielu plików i folderów](develop-storage-files-overview.md#query-multiple-files-or-folders). Program SQL na żądanie wyświetla listę plików na koncie magazynu zaczynających się od pierwszych * przy użyciu interfejsu API usługi Storage i eliminuje pliki, które nie pasują do określonej ścieżki. Zmniejszenie początkowej listy plików może zwiększyć wydajność, jeśli istnieje wiele plików zgodnych z określoną ścieżką do pierwszego symbolu wieloznacznego.

## <a name="use-appropriate-data-types"></a>Użyj odpowiednich typów danych

Typy danych, których można użyć w wydajności wpływu na zapytanie. Lepsza wydajność można uzyskać, jeśli: 

- Użyj najmniejszego rozmiaru danych, który będzie uwzględniać największą możliwą wartość.
  - Jeśli maksymalna długość wartości znakowej to 30 znaków, należy użyć typu danych znak o długości 30.
  - Jeśli wszystkie wartości kolumny znaków mają stały rozmiar, użyj znaków lub nchar. W przeciwnym razie użyj varchar lub nvarchar.
  - Jeśli maksymalna wartość kolumny w liczbie całkowitej to 500, użyj wartości smallint, ponieważ jest to najmniejszy typ danych, który może posłużyć tej wartości. W [tym miejscu](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)możesz znaleźć zakresy danych liczb całkowitych.
- Jeśli to możliwe, użyj varchar i char zamiast nvarchar i nchar.
- Jeśli to możliwe, używaj typów danych opartych na liczbie całkowitej. Operacje sortowania, przyłączania i grupowania według operacji są wykonywane szybciej na liczbach całkowitych niż w przypadku danych znaków.
- Jeśli używasz wnioskowania schematu, [Sprawdź wywnioskowany typ danych](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Sprawdź wywnioskowane typy danych

[Wnioskowanie schematu](query-parquet-files.md#automatic-schema-inference) ułatwia szybkie Pisanie zapytań i Eksplorowanie danych bez znajomości schematu pliku. Ten komfort obejmuje koszt wywnioskowanych typów danych, które są większe niż faktycznie. Dzieje się tak, gdy w plikach źródłowych nie ma wystarczającej ilości informacji, aby upewnić się, że używany jest odpowiedni typ danych. Na przykład pliki Parquet nie zawierają metadanych o maksymalnej długości kolumny znakowej i wymagania SQL na żądanie w postaci varchar (8000). 

Wyniki zapytania można sprawdzać za pomocą [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

Poniższy przykład pokazuje, jak można zoptymalizować wywnioskowane typy danych. Procedura służy do wyświetlania wywnioskowanych typów danych. 
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

Tutaj znajduje się zestaw wyników.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Po znalezieniu wywnioskowanych typów danych dla zapytania można określić odpowiednie typy danych:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Używanie funkcji FileInfo i FilePath do określonych partycji

Dane często są zorganizowane w partycjach. Można wydać instrukcję SQL na żądanie, aby wykonywać zapytania dotyczące określonych folderów i plików. Ta funkcja zmniejszy liczbę plików i ilość danych, które zapytanie musi odczytać i przetworzyć. Dodatkową premią jest osiągnięcie lepszej wydajności.

Aby uzyskać więcej informacji, zapoznaj się z funkcjami [filename](develop-storage-files-overview.md#filename-function) i [FilePath](develop-storage-files-overview.md#filepath-function) i przykładami dotyczącymi [zapytań określonych plików](query-specific-files.md).

> [!TIP]
> Zawsze należy rzutować wynik funkcji FilePath i FileInfo na odpowiednie typy danych. Jeśli używasz typów danych znakowych, upewnij się, że jest używana odpowiednia długość.

> [!NOTE]
> Funkcje używane do usuwania partycji, FilePath i FileInfo nie są obecnie obsługiwane w przypadku tabel zewnętrznych innych niż te utworzone automatycznie dla każdej tabeli utworzonej w Apache Spark dla usługi Azure Synapse Analytics.

Jeśli przechowywane dane nie są podzielone na partycje, należy rozważyć ich partycjonowanie, aby można było używać tych funkcji do optymalizowania zapytań przeznaczonych dla tych plików. Podczas [wykonywania zapytania dotyczącego partycjonowanych tabel platformy Spark](develop-storage-files-spark-tables.md) z poziomu bazy danych SQL na żądanie zapytanie będzie automatycznie kierować tylko te pliki.

## <a name="use-parser_version-20-for-querying-csv-files"></a>Używanie PARSER_VERSION 2,0 do wykonywania zapytań dotyczących plików CSV

Analizatora wydajności zoptymalizowany można użyć podczas wykonywania zapytania dotyczącego plików CSV. Sprawdź [PARSER_VERSION](develop-openrowset.md) , aby uzyskać szczegółowe informacje.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Korzystanie z CETAS w celu zwiększenia wydajności zapytań i sprzężeń

[CETAS](develop-tables-cetas.md) to jedna z najważniejszych funkcji dostępnych w programie SQL na żądanie. CETAS to równoległa operacja, która tworzy metadane tabeli zewnętrznej i eksportuje wyniki zapytania SELECT do zestawu plików na koncie magazynu.

Można użyć CETAS do przechowywania często używanych części zapytań, takich jak sprzężone tabele odwołań, do nowego zestawu plików. Następnie można przyłączyć się do tej pojedynczej tabeli zewnętrznej zamiast powtarzających się wspólnych sprzężeń w wielu zapytaniach.

Ponieważ CETAS generuje pliki Parquet, statystyki zostaną automatycznie utworzone, gdy pierwsze zapytanie odwołuje się do tej tabeli zewnętrznej, co spowodowało zwiększenie wydajności.

## <a name="aad-pass-through-performance"></a>Wydajność przekazywania w usłudze AAD

Funkcja SQL na żądanie umożliwia dostęp do plików w magazynie przy użyciu funkcji przekazywania lub poświadczeń usługi AAD. Może wystąpić wolniejsze działanie z przekazywaniem usługi AAD do sygnatury dostępu współdzielonego. 

Jeśli potrzebujesz lepszej wydajności, spróbuj użyć poświadczeń SAS, aby uzyskać dostęp do magazynu, dopóki nie zostanie zwiększona wydajność usługi AAD.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [rozwiązywania problemów](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać typowe problemy i rozwiązania. Jeśli pracujesz z pulą SQL, a nie SQL na żądanie, zobacz [najlepsze rozwiązania dotyczące puli SQL](best-practices-sql-pool.md) , aby uzyskać szczegółowe wskazówki.
