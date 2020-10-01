---
title: Wykonywanie zapytań dotyczących danych Azure Cosmos DB przy użyciu programu SQL Server w usłudze Azure Synapse link (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania do Azure Cosmos DB przy użyciu usługi SQL na żądanie w usłudze Azure Synapse link (wersja zapoznawcza).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 028f47fcfb4a6a4d94d672e950b4c37d739e672b
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597316"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Wykonywanie zapytań dotyczących danych Azure Cosmos DB przy użyciu programu SQL Server w usłudze Azure Synapse link (wersja zapoznawcza)

Program SQL Server (wcześniej bez programu SQL Server) umożliwia analizowanie danych w kontenerach Azure Cosmos DB, które są włączane za pomocą [linku Synapse platformy Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) w czasie niemal rzeczywistym i nie wpływają na wydajność obciążeń transakcyjnych. Oferuje znaną składnię T-SQL służącą do wykonywania zapytań dotyczących danych z [magazynu analitycznego](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i zintegrowanej łączności z szeroką gamę narzędzi do wykonywania zapytań w trybie analizy biznesowej i ad hoc za pośrednictwem interfejsu T-SQL.

> [!NOTE]
> Obsługa zapytań dotyczących magazynu analitycznego Azure Cosmos DB przy użyciu programu SQL Server jest obecnie dostępna w wersji zapoznawczej. 

Do wykonywania zapytań w Azure Cosmos DB [, pełny obszar](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) powierzchni jest obsługiwany przez funkcję [OPENROWSET](develop-openrowset.md) , łącznie z większością [funkcji i operatorów SQL](overview-features.md). Możesz również przechowywać wyniki zapytania, które odczytuje dane z Azure Cosmos DB wraz z danymi na platformie Azure Blob Storage lub Azure Data Lake Storage za pomocą polecenia [Utwórz tabelę zewnętrzną jako wybraną](develop-tables-cetas.md#cetas-in-sql-on-demand). Obecnie nie można przechowywać wyników zapytania bezserwerowego SQL do Azure Cosmos DB przy użyciu [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu programu SQL Server, które będzie wysyłać zapytania do danych z Azure Cosmos DB kontenerów z włączonym łączem Synapse. Następnie można dowiedzieć się więcej o tworzeniu widoków bezserwerowych programu SQL Server za pośrednictwem kontenerów Azure Cosmos DB i łączeniu ich z modelami Power BI w [tym](./tutorial-data-analyst.md) samouczku. 

## <a name="overview"></a>Omówienie

W celu obsługi zapytań i analizowania danych w Azure Cosmos DB analitycznym, program SQL Server używa następującej `OPENROWSET` składni:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

Parametry połączenia Azure Cosmos DB określają nazwę konta Azure Cosmos DB, nazwę bazy danych, klucz główny konta bazy danych i opcjonalną nazwę regionu do `OPENROWSET` działania. Parametry połączenia mają następujący format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Nazwa kontenera Azure Cosmos DB jest określona bez cudzysłowów w `OPENROWSET` składni. Jeśli nazwa kontenera zawiera wszystkie znaki specjalne (na przykład kreskę "-"), nazwa powinna być opakowana w `[]` nawiasy kwadratowe w `OPENROWSET` składni.

> [!NOTE]
> Program SQL Server nie obsługuje zapytań o magazyn transakcyjny Azure Cosmos DB.

## <a name="sample-data-set"></a>Przykładowy zestaw danych

Przykłady w tym artykule są oparte na danych z [centrum Europejskiego centrów zapobiegania chorobom i kontroli (ECDC) COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) i [COVID-19 Open Research DataSet (kabel-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Możesz zobaczyć licencję i strukturę danych na tych stronach i pobrać przykładowe dane dla zestawów danych [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) i [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Aby wykonać kroki opisane w tym artykule, jak utworzyć zapytanie Cosmos DB danych z programem SQL Server, należy się upewnić, że zostały utworzone następujące zasoby:
* Konto bazy danych Azure Cosmos DB z [włączonym linkiem Synapse](../../cosmos-db/configure-synapse-link.md)
* Baza danych Azure Cosmos DB o nazwie `covid`
* Załadowano dwa kontenery Azure Cosmos DB o nazwach `EcdcCases` i `Cord19` z powyższymi przykładowymi zestawami danych.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Eksplorowanie danych Azure Cosmos DB z automatycznym wnioskami o schemacie

Najprostszym sposobem eksplorowania danych w Azure Cosmos DB jest wykorzystanie funkcji automatycznego wnioskowania schematu. Pomijając `WITH` klauzulę z `OPENROWSET` instrukcji, można NAkazać programowi SQL Server bezobsługowe wykrywanie (wnioskowanie) schematu magazynu analitycznego kontenera Azure Cosmos DB.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
W powyższym przykładzie Instruujemy bezserwerowy SQL, aby połączyć się z `covid` bazą danych w ramach konta usługi Azure Cosmos DB `MyCosmosDbAccount` uwierzytelnionego przy użyciu klucza Azure Cosmos dB (fikcyjny w powyższym przykładzie). Następnie uzyskujemy dostęp do `EcdcCases` magazynu analitycznego kontenera w `West US 2` regionie. Ponieważ nie ma projekcji określonych właściwości, `OPENROWSET` funkcja zwróci wszystkie właściwości z Azure Cosmos DB elementów.

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

Azure Cosmos DB pozwala reprezentować bardziej złożone modele danych przez złożenie ich jako obiektów zagnieżdżonych lub tablic. AutoSync funkcja linku Synapse dla Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych, co pozwala na wykonywanie bogatych zapytań z programu SQL Server.

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

| title | autorów | first_autor_name |
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

Dowiedz się więcej o analizowaniu [złożonych typów danych w linków Synapse](../how-to-analyze-complex-schema.md) i [zagnieżdżonych strukturach w programie SQL Server](query-parquet-nested-types.md).

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , na przykład zamiast `Mélade` sortowania bazy danych nie jest ustawiony na sortowanie [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Zmień sortowanie bazy danych](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kilka sortowania UTF8 przy użyciu jakiejś instrukcji języka SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Spłaszczanie tablic zagnieżdżonych

Azure Cosmos DB dane mogą mieć zagnieżdżone tablice podrzędne, takie jak tablica autora, z zestawu danych [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

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

W niektórych przypadkach może być konieczne "dołączenie" właściwości z górnego elementu (metadane) do wszystkich elementów tablicy (autorów). Program SQL Server bezserwerowy umożliwia spłaszczonie zagnieżdżonych struktur przez zastosowanie `OPENJSON` funkcji w tablicy zagnieżdżonej:

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

| title | autorów | pierwszego | ostatni | przynależności |
| --- | --- | --- | --- | --- |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Dodatkowe informacje o epidemiach ekonomicznych... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | czwart # |`{"laboratory":"","institution":"U…` | 
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , na przykład zamiast `Mélade` sortowania bazy danych nie jest ustawiony na sortowanie [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . 
> [Zmień sortowanie bazy danych](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na kilka sortowania UTF8 przy użyciu jakiejś instrukcji języka SQL `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB z mapowaniami typów SQL

Należy najpierw pamiętać, że podczas Azure Cosmos DB magazynem transakcyjnym jest schemat niezależny od, magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji AutoSync linku Synapse Program Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych. Ponieważ zapytania bezserwerowe SQL są przechowywane w magazynie analitycznym, ważne jest, aby zrozumieć, jak mapować Azure Cosmos DB dane wejściowe do typów danych SQL.

Konta Azure Cosmos DB interfejsu API języka SQL (rdzeń) obsługują typy właściwości JSON number, String, Boolean, null, zagnieżdżony obiekt lub Array. Jeśli używasz klauzuli w programie, musisz wybrać typy SQL zgodne z tymi typami JSON `WITH` `OPENROWSET` . Poniżej znajdują się typy kolumn SQL, które powinny być używane dla różnych typów właściwości w Azure Cosmos DB.

| Typ właściwości Azure Cosmos DB | Typ kolumny SQL |
| --- | --- |
| Wartość logiczna | bit |
| Liczba całkowita | bigint |
| Liczba dziesiętna | float |
| Ciąg | varchar (sortowanie bazy danych UTF8) |
| Data i godzina (ciąg w formacie ISO) | varchar (30) |
| Data i godzina (Sygnatura czasowa systemu UNIX) | bigint |
| Zero | `any SQL type` 
| Zagnieżdżony obiekt lub tablica | varchar (max) (sortowanie bazy danych UTF8), serializacja jako tekst JSON |

Aby uzyskać informacje na temat wykonywania zapytań dotyczących kont Azure Cosmos DB w interfejsie API usługi Mongo DB, możesz dowiedzieć się więcej o pełnej reprezentacji schematu w magazynie analitycznym i nazwach właściwości rozszerzonych, które mają być używane w [tym miejscu](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Jak tworzyć widoki i używać ich w programie SQL na żądanie](create-use-views.md) 
- [Samouczek dotyczący tworzenia widoków na żądanie SQL dla Azure Cosmos DB i łączenia ich z modelami Power BI za pośrednictwem zapytania bezpośredniego](./tutorial-data-analyst.md)
