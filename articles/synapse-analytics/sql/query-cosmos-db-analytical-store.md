---
title: Wykonywanie zapytań dotyczących danych Azure Cosmos DB przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse link (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania do Azure Cosmos DB przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse link (wersja zapoznawcza).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9f57d435134bffbb8e7576adffeacb92bf687124
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310304"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Wykonywanie zapytań dotyczących danych Azure Cosmos DB za pomocą puli SQL bezserwerowej w usłudze Azure Synapse link (wersja zapoznawcza)

Synapse bezserwerowa Pula SQL umożliwia analizowanie danych w kontenerach Azure Cosmos DB, które są włączone za pomocą [usługi Azure Synapse link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) niemal w czasie rzeczywistym bez wpływu na wydajność obciążeń transakcyjnych. Oferuje znaną składnię T-SQL służącą do wykonywania zapytań dotyczących danych z [magazynu analitycznego](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i zintegrowanej łączności z szeroką gamę narzędzi do wykonywania zapytań w trybie analizy biznesowej i ad hoc za pośrednictwem interfejsu T-SQL.

Do wykonywania zapytań w Azure Cosmos DB [, pełny obszar](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) powierzchni jest obsługiwany przez funkcję [OPENROWSET](develop-openrowset.md) , łącznie z większością [funkcji i operatorów SQL](overview-features.md). Możesz również przechowywać wyniki zapytania, które odczytuje dane z Azure Cosmos DB wraz z danymi na platformie Azure Blob Storage lub Azure Data Lake Storage za pomocą polecenia [Utwórz tabelę zewnętrzną jako wybraną](develop-tables-cetas.md#cetas-in-serverless-sql-pool). Obecnie nie można przechowywać wyników zapytania puli SQL bezserwerowej w celu Azure Cosmos DB przy użyciu CETAS. 

W tym artykule dowiesz się, jak napisać zapytanie z pulą SQL bezserwerową, która będzie wysyłać zapytania do danych z Azure Cosmos DB kontenerów z włączonym łączem Synapse. Następnie można dowiedzieć się więcej o tworzeniu widoków puli SQL bezserwerowych za pośrednictwem kontenerów Azure Cosmos DB i łączeniu ich z modelami Power BI w [tym](./tutorial-data-analyst.md) samouczku. 

> [!IMPORTANT]
> W tym samouczku jest stosowany kontener ze [zdefiniowanym schematem Azure Cosmos DB](../../cosmos-db/analytical-store-introduction.md#schema-representation). W zapytaniu niezależnym od serwera Pula SQL zapewnia [Azure Cosmos DB schemat pełnej wierności](#full-fidelity-schema) , który zostanie zmieniony na podstawie opinii o wersji zapoznawczej. Nie należy polegać na schemacie zestawu wyników `OPENROWSET` funkcji bez `WITH` klauzuli, która odczytuje dane z kontenera z pełnym schematem wierności, ponieważ środowisko zapytania może zostać zmienione i wyrównane z dobrze zdefiniowanym schematem. Opublikuj swoją opinię na [forum opinii usługi Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics) lub skontaktuj się z [zespołem produktu](mailto:cosmosdbsynapselink@microsoft.com) , aby uzyskać opinię.

## <a name="overview"></a>Omówienie

Aby umożliwić obsługę zapytań i analizowanie danych w Azure Cosmos DB magazynie analitycznym, Pula SQL bezserwerowa używa następującej `OPENROWSET` składni:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Parametry połączenia Azure Cosmos DB określają nazwę konta Azure Cosmos DB, nazwę bazy danych, klucz główny konta bazy danych i opcjonalną nazwę regionu do `OPENROWSET` działania. 

> [!IMPORTANT]
> Upewnij się, że używasz aliasu po `OPENROWSET` . Istnieje [znany problem](#known-issues) powodujący problem z połączeniem, który Synapse bezserwerowy punkt końcowy SQL, jeśli nie określisz aliasu po `OPENROWSET` funkcji.

Parametry połączenia mają następujący format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Nazwa kontenera Azure Cosmos DB jest określona bez cudzysłowów w `OPENROWSET` składni. Jeśli nazwa kontenera zawiera wszystkie znaki specjalne (na przykład kreskę "-"), nazwa powinna być opakowana w `[]` nawiasy kwadratowe w `OPENROWSET` składni.

> [!NOTE]
> Bezserwerowa Pula SQL nie obsługuje zapytań do magazynu transakcyjnego Azure Cosmos DB.

## <a name="sample-data-set"></a>Przykładowy zestaw danych

Przykłady w tym artykule są oparte na danych z [centrum Europejskiego centrów zapobiegania chorobom i kontroli (ECDC) COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) i [COVID-19 Open Research DataSet (kabel-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Możesz zobaczyć licencję i strukturę danych na tych stronach i pobrać przykładowe dane dla zestawów danych [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) i [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Aby wykonać czynności opisane w tym artykule, jak utworzyć zapytanie Cosmos DB danych z pulą SQL bez użycia serwera, należy się upewnić, że zostały utworzone następujące zasoby:
* Konto bazy danych Azure Cosmos DB z [włączonym linkiem Synapse](../../cosmos-db/configure-synapse-link.md)
* Baza danych Azure Cosmos DB o nazwie `covid`
* Załadowano dwa kontenery Azure Cosmos DB o nazwach `EcdcCases` i `Cord19` z powyższymi przykładowymi zestawami danych.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Eksplorowanie danych Azure Cosmos DB z automatycznym wnioskami o schemacie

Najprostszym sposobem eksplorowania danych w Azure Cosmos DB jest wykorzystanie funkcji automatycznego wnioskowania schematu. Pomijając `WITH` klauzulę z `OPENROWSET` instrukcji, można poinstruować bezserwerowe pule SQL, aby automatycznie wykrywać (wnioskować) schemat magazynu analitycznego kontenera Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
W powyższym przykładzie poinstruujemy bezserwerową pulę SQL, aby połączyć się z `covid` bazą danych w ramach konta usługi Azure Cosmos DB `MyCosmosDbAccount` uwierzytelnionego przy użyciu klucza Azure Cosmos dB (fikcyjny w powyższym przykładzie). Następnie uzyskujemy dostęp do `EcdcCases` magazynu analitycznego kontenera w `West US 2` regionie. Ponieważ nie ma projekcji określonych właściwości, `OPENROWSET` funkcja zwróci wszystkie właściwości z Azure Cosmos DB elementów. 

W założeniu, że elementy w kontenerze Cosmos DB mają `date_rep` `cases` właściwości,, i `geo_id` , wyniki tego zapytania są pokazane w poniższej tabeli:

| date_rep | padkach | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Jeśli zachodzi potrzeba eksplorowania danych z innego kontenera w tej samej bazie danych Azure Cosmos DB, można użyć tych samych parametrów połączenia i odwołania do kontenera jako trzeci parametr:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Jawnie określ schemat

Podczas gdy automatyczne wnioskowanie schematu w programie `OPENROWSET` zapewnia proste i łatwe w użyciu querience, scenariusze biznesowe mogą wymagać jawnego określenia schematu do odczytu odpowiednich właściwości z danych Azure Cosmos DB.

`OPENROWSET` umożliwia jawne określenie właściwości, które mają być odczytywane z danych w kontenerze i określanie ich typów danych. Załóżmy, że zaimportowano pewne dane z [zestawu danych ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) z następującą strukturą do Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Te płaskie dokumenty JSON w Azure Cosmos DB mogą być reprezentowane jako zestaw wierszy i kolumn w języku SQL Synapse. `OPENROWSET` funkcja umożliwia określenie podzestawu właściwości, które mają zostać odczytane, oraz dokładne typy kolumn w `WITH` klauzuli:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Wynik tego zapytania może wyglądać następująco:

| date_rep | padkach | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Przejrzyj [reguły mapowania typu SQL](#azure-cosmos-db-to-sql-type-mappings) na końcu artykułu, aby uzyskać więcej informacji na temat typów SQL, które powinny być używane dla wartości Azure Cosmos DB.

## <a name="querying-nested-objects-and-arrays"></a>Wykonywanie zapytań względem zagnieżdżonych obiektów i tablic

Azure Cosmos DB pozwala reprezentować bardziej złożone modele danych przez złożenie ich jako obiektów zagnieżdżonych lub tablic. AutoSync funkcja linku Synapse dla Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych umożliwiających wykonywanie bogatych zapytań z puli SQL bezserwerowej.

Na przykład zestaw danych z [przewodu-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) zawiera dokumenty JSON następujące po następującej strukturze:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Zagnieżdżone obiekty i tablice w Azure Cosmos DB są reprezentowane jako ciągi JSON w wyniku zapytania, gdy `OPENROWSET` Funkcja je odczytuje. Jedną z opcji odczytu wartości z tych typów złożonych jako kolumny SQL jest użycie funkcji JSON języka SQL:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Wynik tego zapytania może wyglądać następująco:

| tytuł | autorów | first_autor_name |
| --- | --- | --- |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Jako alternatywną opcję można także określić ścieżki do wartości zagnieżdżonych w klauzuli objectss when using `WITH` :

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Dowiedz się więcej o analizowaniu [złożonych typów danych w linków Synapse](../how-to-analyze-complex-schema.md) i [zagnieżdżonych strukturach w puli SQL bezserwerowej](query-parquet-nested-types.md).

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , na przykład zamiast `Mélade` sortowania bazy danych nie jest ustawiony na sortowanie [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Zmień sortowanie bazy danych](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kilka sortowania UTF8 przy użyciu jakiejś instrukcji języka SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flattening-nested-arrays"></a>Spłaszczanie tablic zagnieżdżonych

Azure Cosmos DB dane mogą mieć zagnieżdżone podtablice, takie jak tablica autora, z zestawu danych [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

W niektórych przypadkach może być konieczne "dołączenie" właściwości z górnego elementu (metadane) do wszystkich elementów tablicy (autorów). Pula SQL bezserwerowa umożliwia spłaszczonie zagnieżdżonych struktur, stosując `OPENJSON` funkcję w tablicy zagnieżdżonej:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Wynik tego zapytania może wyglądać następująco:

| tytuł | autorów | pierwszego | ostatni | przynależności |
| --- | --- | --- | --- | --- |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Dodatkowe informacje o epidemiach ekonomicznych... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | czwart # |`{"laboratory":"","institution":"U…` | 
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , na przykład zamiast `Mélade` sortowania bazy danych nie jest ustawiony na sortowanie [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Zmień sortowanie bazy danych](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kilka sortowania UTF8 przy użyciu jakiejś instrukcji języka SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB z mapowaniami typów SQL

Należy najpierw pamiętać, że podczas Azure Cosmos DB magazynem transakcyjnym jest schemat niezależny od, magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji AutoSync linku Synapse Program Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych. Ponieważ pula SQL bezserwerowa wysyła zapytanie do magazynu analitycznego, ważne jest, aby zrozumieć, jak mapować Azure Cosmos DB dane wejściowe do typów danych SQL.

Konta Azure Cosmos DB interfejsu API języka SQL (rdzeń) obsługują typy właściwości JSON number, String, Boolean, null, zagnieżdżony obiekt lub Array. Jeśli używasz klauzuli w programie, musisz wybrać typy SQL zgodne z tymi typami JSON `WITH` `OPENROWSET` . Poniżej znajdują się typy kolumn SQL, które powinny być używane dla różnych typów właściwości w Azure Cosmos DB.

| Typ właściwości Azure Cosmos DB | Typ kolumny SQL |
| --- | --- |
| Boolean | bit |
| Liczba całkowita | bigint |
| Liczba dziesiętna | float |
| String | varchar (sortowanie bazy danych UTF8) |
| Data i godzina (ciąg w formacie ISO) | varchar (30) |
| Data i godzina (Sygnatura czasowa systemu UNIX) | bigint |
| Zero | `any SQL type` 
| Zagnieżdżony obiekt lub tablica | varchar (max) (sortowanie bazy danych UTF8), serializacja jako tekst JSON |

## <a name="full-fidelity-schema"></a>Schemat pełnej wierności

Azure Cosmos DB schemat pełnej wierności rejestruje zarówno wartości, jak i ich najlepsze dopasowanie dla każdej właściwości w kontenerze.
`OPENROWSET` Funkcja w kontenerze ze schematem o pełnej wierności danych zawiera zarówno typ, jak i rzeczywistą wartość w każdej komórce. Załóżmy, że następujące zapytanie odczytuje elementy z kontenera z pełnym schematem wierności:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

Wynik tego zapytania zwróci typy i wartości sformatowane jako tekst JSON: 

| date_rep | padkach | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "DB"} | {"String": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"date": "2020-08-07"} | {"Float64": "339.0"} | {"String": "RS"} |

Dla każdej wartości można zobaczyć typ identyfikowany w Cosmos DB elemencie kontenera. Większość wartości `date_rep` właściwości zawiera `date` wartości, ale niektóre z nich są nieprawidłowo przechowywane jako ciągi w Cosmos DB. Schemat pełnej wierności zwróci zarówno poprawnie wpisaną `date` wartość, jak i niepoprawnie sformatowane `string` wartości.
Liczba przypadków to informacje przechowywane jako `int32` wartość, ale istnieje jedna wartość wprowadzona jako liczba dziesiętna. Ta wartość ma `float64` Typ. Jeśli istnieją wartości przekraczające największą `int32` liczbę, zostaną one zapisane jako `int64` Typ. Wszystkie `geo_id` wartości w tym przykładzie są przechowywane jako `string` typy.

> [!IMPORTANT]
> `OPENROWSET` Funkcja bez `WITH` klauzuli ujawnia obie wartości z oczekiwanymi typami i wartościami z nieprawidłowo wprowadzonymi typami. Ta funkcja Func została zaprojektowana na potrzeby eksploracji danych, a nie do raportowania. Nie Analizuj wartości JSON zwracanych z tej funkcji, aby tworzyć raporty i używać jawnej [klauzuli with](#querying-items-with-full-fidelity-schema) w celu tworzenia raportów.
> Należy wyczyścić wartości, które mają niepoprawne typy w kontenerze Azure Cosmos DB, aby zastosować współrzędną w magazynie analitycznym z pełną dokładnością. 

Aby uzyskać informacje na temat wykonywania zapytań dotyczących kont Azure Cosmos DB w interfejsie API usługi Mongo DB, możesz dowiedzieć się więcej o pełnej reprezentacji schematu w magazynie analitycznym i nazwach właściwości rozszerzonych, które mają być używane w [tym miejscu](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="querying-items-with-full-fidelity-schema"></a>Wykonywanie zapytania o elementy z schematem pełnej wierności

Podczas wykonywania zapytania o cały schemat wierności należy jawnie określić typ SQL i oczekiwany Cosmos DB typu właściwości w `WITH` klauzuli. Nie używaj `OPENROWSET` klauzuli bez `WITH` w raportach, ponieważ format zestawu wyników może zostać zmieniony w wersji zapoznawczej na podstawie opinii.

W poniższym przykładzie przyjęto założenie, że `string` jest to poprawny typ dla `geo_id` właściwości i `int32` poprawnego typu dla `cases` Właściwości:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Wartości dla `geo_id` i `cases` , które mają inne typy, zostaną zwrócone jako `NULL` wartości. To zapytanie będzie odwoływać się tylko do `cases` określonego typu w wyrażeniu ( `cases.int32` ).

Jeśli masz wartości z innymi typami ( `cases.int64` , `cases.float64` ), które nie mogą być czyszczone w kontenerze Cosmos DB, musisz jawnie odwoływać się do nich w `WITH` klauzuli i połączyć wyniki. Następujące zapytanie agreguje zarówno `int32` , `int64` ,, i `float64` przechowywane w `cases` kolumnie:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

W tym przykładzie liczba przypadków jest przechowywana `int32` `int64` w postaci lub wartości, `float64` a wszystkie wartości muszą zostać wyodrębnione w celu obliczenia liczby przypadków na kraj. 

## <a name="known-issues"></a>Znane problemy

- Alias **należy** określić po `OPENROWSET` funkcji (na przykład `OPENROWSET (...) AS function_alias` ). Pominięcie aliasu może spowodować problem z połączeniem i Synapse punkt końcowy SQL bez serwera może być tymczasowo niedostępny. Ten problem zostanie rozwiązany w lis 2020.
- Funkcja zapytania, która zapewnia bezserwerową pulę SQL, dla [Azure Cosmos DB schemat pełnej wierności](#full-fidelity-schema) jest tymczasowym zachowaniem, które zostanie zmienione na podstawie opinii o wersji zapoznawczej. Nie należy polegać na schemacie, który `OPENROWSET` Funkcja bez `WITH` klauzuli zapewnia w publicznej wersji zapoznawczej, ponieważ środowisko zapytania może być wyrównane z dobrze zdefiniowanym schematem na podstawie opinii klientów. Skontaktuj się z [zespołem produktu Synapse link](mailto:cosmosdbsynapselink@microsoft.com) , aby przekazać opinię.

Możliwe błędy i akcje rozwiązywania problemów są wymienione w poniższej tabeli:

| Błąd | Główna przyczyna |
| --- | --- |
| Błędy składniowe:<br/> -Nieprawidłowa składnia w sąsiedztwie "OPENROWSET"<br/> - `...` nie jest rozpoznawaną opcją dostawcy ZBIORCZego OPENROWSET.<br/> -Nieprawidłowa składnia w sąsiedztwie `...` | Możliwe przyczyny główne<br/> -Nie należy używać "CosmosDB" jako pierwszego parametru,<br/> — Przy użyciu literału ciągu zamiast identyfikatora w trzecim parametrze<br/> — Nie określono trzeciego parametru (nazwa kontenera) |
| Wystąpił błąd w parametrach połączenia CosmosDB | — Nie określono konta, bazy danych, klucza <br/> -Istnieje kilka opcji parametrów połączenia, które nie zostały rozpoznane.<br/> -Średnik `;` jest umieszczany na końcu ciągu połączenia |
| Rozpoznawanie ścieżki CosmosDB nie powiodło się z powodu błędu "Nieprawidłowa nazwa konta" lub "niepoprawna nazwa bazy danych" | Nie można odnaleźć określonej nazwy konta, nazwy bazy danych lub kontenera lub nie włączono magazynu analitycznego o określonej kolekcji.|
| Rozpoznanie ścieżki CosmosDB nie powiodło się z powodu błędu "niepoprawna wartość tajna" lub "wpis tajny ma wartość null lub jest pusty" | Brak klucza konta lub jest on nieprawidłowy. |
| Kolumna `column name` typu `type name` nie jest zgodna z typem danych zewnętrznych `type name` | Określony typ kolumny w `WITH` klauzuli nie jest zgodny z typem w kontenerze Cosmos DB. Spróbuj zmienić typ kolumny, zgodnie z opisem w sekcji [Azure Cosmos DB na mapowania typu SQL](#azure-cosmos-db-to-sql-type-mappings) lub Użyj `VARCHAR` typu. |
| Kolumna zawiera `NULL` wartości we wszystkich komórkach. | Prawdopodobnie zła nazwa kolumny lub wyrażenie ścieżki w `WITH` klauzuli. Nazwa kolumny (lub wyrażenie ścieżki po typie kolumny) `WITH` musi być zgodna z nazwą właściwości w kolekcji Cosmos DB. W porównaniu z **rozróżnianiem wielkości**  liter (na przykład `productCode` i `ProductCode` są to różne właściwości). |

Sugestie i problemy można zgłaszać na [stronie opinii o usłudze Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Użyj Power BI i bezserwerowej puli SQL z linkiem usługi Azure Synapse](../../cosmos-db/synapse-link-power-bi.md)
- [Jak utworzyć i używać widoków w puli SQL bezserwerowej](create-use-views.md) 
- [Samouczek dotyczący tworzenia widoków puli SQL bezserwerowych na Azure Cosmos DB i łączenia ich z modelami Power BI za pośrednictwem zapytania bezpośredniego](./tutorial-data-analyst.md)
