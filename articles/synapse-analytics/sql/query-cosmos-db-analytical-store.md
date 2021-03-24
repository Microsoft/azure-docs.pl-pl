---
title: Wykonywanie zapytań dotyczących danych Azure Cosmos DB przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse link
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące Azure Cosmos DB przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse link.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 10262b168b91370956c9559ba688c72213ba7618
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870997"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Wykonywanie zapytań dotyczących danych Azure Cosmos DB za pomocą bezserwerowej puli SQL w usłudze Azure Synapse link

Bezserwerowa Pula SQL umożliwia analizowanie danych w kontenerach Azure Cosmos DB, które są włączone przy użyciu [linku Synapse platformy Azure](../../cosmos-db/synapse-link.md) niemal w czasie rzeczywistym bez wpływu na wydajność obciążeń transakcyjnych. Oferuje znaną składnię T-SQL do wykonywania zapytań dotyczących danych z [magazynu analitycznego](../../cosmos-db/analytical-store-introduction.md) i zintegrowanej łączności z szeroką gamę narzędzi do analizy biznesowej (BI) i zapytań ad hoc za pośrednictwem interfejsu T-SQL.

W przypadku wykonywania zapytań w Azure Cosmos DB, [pełny obszar](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) powierzchni jest obsługiwany przez funkcję [OPENROWSET](develop-openrowset.md) , która obejmuje większość [funkcji SQL i operatorów](overview-features.md). Możesz również przechowywać wyniki zapytania, które odczytuje dane z Azure Cosmos DB wraz z danymi na platformie Azure Blob Storage lub Azure Data Lake Storage za pomocą polecenia [Utwórz tabelę zewnętrzną jako SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). Obecnie nie można przechowywać wyników zapytania puli SQL bezserwerowej w celu Azure Cosmos DB przy użyciu CETAS.

W tym artykule dowiesz się, jak napisać zapytanie z pulą SQL bezserwerową, która będzie wysyłać zapytania dotyczące danych z Azure Cosmos DB kontenerów włączonych za pomocą linku Synapse platformy Azure. Następnie można dowiedzieć się więcej o tworzeniu widoków puli SQL bezserwerowych za pośrednictwem kontenerów Azure Cosmos DB i łączeniu ich z modelami Power BI w [tym samouczku](./tutorial-data-analyst.md). W tym samouczku jest stosowany kontener z [dobrze zdefiniowanym schematem Azure Cosmos DB](../../cosmos-db/analytical-store-introduction.md#schema-representation).

## <a name="overview"></a>Omówienie

Pula SQL bezserwerowa umożliwia wykonywanie zapytań dotyczących magazynu analitycznego Azure Cosmos DB przy użyciu `OPENROWSET` funkcji. 
- `OPENROWSET` przy użyciu klawisza wbudowanego. Ta składnia może służyć do wysyłania zapytań do kolekcji Azure Cosmos DB bez konieczności przygotowywania poświadczeń.
- `OPENROWSET` to przywoływane poświadczenie zawierające klucz konta Cosmos DB. Ta składnia może służyć do tworzenia widoków w Azure Cosmos DB kolekcje.

### <a name="openrowset-with-key"></a>[OPENROWSET z kluczem](#tab/openrowset-key)

Aby umożliwić obsługę zapytań i analizowanie danych w magazynie analitycznym Azure Cosmos DB, Pula SQL bezserwerowa używa następującej `OPENROWSET` składni:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Parametry połączenia Azure Cosmos DB określają nazwę konta Azure Cosmos DB, nazwę bazy danych, klucz główny konta bazy danych i opcjonalną nazwę regionu dla `OPENROWSET` funkcji.

Parametry połączenia mają następujący format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Nazwa kontenera Azure Cosmos DB jest określona bez cudzysłowów w `OPENROWSET` składni. Jeśli nazwa kontenera zawiera wszystkie znaki specjalne, na przykład kreskę (-), nazwa powinna być opakowana w nawiasy kwadratowe ( `[]` ) w `OPENROWSET` składni.

### <a name="openrowset-with-credential"></a>[OPENROWSET z poświadczeniem](#tab/openrowset-credential)

Można użyć `OPENROWSET` składni odwołującej się do poświadczeń:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

Parametry połączenia Azure Cosmos DB nie zawierają klucza w tym przypadku. Parametry połączenia mają następujący format:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Klucz główny konta bazy danych jest umieszczany w poświadczeniach na poziomie serwera lub w poświadczeniu o zakresie bazy danych. 

---

> [!IMPORTANT]
> Upewnij się, że używasz pewnego sortowania bazy danych UTF-8, na przykład, `Latin1_General_100_CI_AS_SC_UTF8` ponieważ wartości ciągów w Azure Cosmos DB magazynie analitycznym są kodowane jako tekst UTF-8.
> Niezgodność między kodowaniem tekstu w pliku a sortowaniem może spowodować nieoczekiwane błędy konwersji tekstu.
> Można łatwo zmienić domyślne sortowanie bieżącej bazy danych za pomocą instrukcji języka T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Bezserwerowa Pula SQL nie obsługuje zapytań do magazynu transakcyjnego Azure Cosmos DB.

## <a name="sample-dataset"></a>Przykładowy zestaw danych

Przykłady w tym artykule są oparte na danych z [centrum Europejskiego centrów zapobiegania chorobom i kontroli (ECDC) COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) i [COVID-19 Open Research DataSet (kabel-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Na tych stronach można zobaczyć licencję i strukturę danych. Możesz również pobrać przykładowe dane dla zestawów danych [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) i z [kabelem 19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Aby postępować zgodnie z tym artykułem, przedstawiamy sposób wykonywania zapytań dotyczących danych Azure Cosmos DB za pomocą puli SQL bezserwerowej, należy utworzyć następujące zasoby:

* Konto bazy danych Azure Cosmos DB z [włączonym łączem Azure Synapse](../../cosmos-db/configure-synapse-link.md).
* Baza danych Azure Cosmos DB o nazwie `covid` .
* Dwa kontenery Azure Cosmos DB o nazwach `Ecdc` i `Cord19` załadowane z poprzednimi przykładowymi zestawami danych.

Do celów testowych można użyć następujących parametrów połączenia: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Należy zauważyć, że to połączenie nie gwarantuje wydajności, ponieważ to konto może znajdować się w regionie zdalnym w porównaniu z punktem końcowym usługi SQL Synapse.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Eksplorowanie danych Azure Cosmos DB z automatycznym wnioskami o schemacie

Najprostszym sposobem eksplorowania danych w Azure Cosmos DB jest użycie funkcji automatycznego wnioskowania schematu. Pomijając `WITH` klauzulę z `OPENROWSET` instrukcji, można poinstruować bezserwerową pulę SQL do autowykrywania (wnioskowania) schemat magazynu analitycznego kontenera Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET z kluczem](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET z poświadczeniem](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

W poprzednim przykładzie poinstruujemy bezserwerową pulę SQL, aby połączyć się z `covid` bazą danych w ramach konta Azure Cosmos DB `MyCosmosDbAccount` uwierzytelnionego przy użyciu klucza Azure Cosmos dB (manekin w poprzednim przykładzie). Następnie uzyskano dostęp do `Ecdc` magazynu analitycznego kontenera w `West US 2` regionie. Ponieważ nie ma projekcji określonych właściwości, funkcja zwróci `OPENROWSET` wszystkie właściwości z Azure Cosmos DB elementów.

Przy założeniu, że elementy w kontenerze Azure Cosmos DB zawierają `date_rep` `cases` właściwości, i `geo_id` , wyniki tego zapytania są pokazane w poniższej tabeli:

| date_rep | padkach | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Jeśli zachodzi potrzeba eksplorowania danych z innego kontenera w tej samej bazie danych Azure Cosmos DB, można użyć tych samych parametrów połączenia i odwołać się do wymaganego kontenera jako trzeci parametr:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Jawnie określ schemat

Podczas gdy automatyczne wnioskowanie schematu w programie `OPENROWSET` zapewnia proste i łatwe w użyciu querience, scenariusze biznesowe mogą wymagać jawnego określenia schematu do odczytu odpowiednich właściwości z danych Azure Cosmos DB.

`OPENROWSET`Funkcja umożliwia jawne określenie właściwości, które mają być odczytywane z danych w kontenerze i określanie ich typów danych.

Załóżmy, że zaimportowano pewne dane z zestawu danych [ECDC COVID](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) o następującej strukturze do Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Te płaskie dokumenty JSON w Azure Cosmos DB mogą być reprezentowane jako zestaw wierszy i kolumn w języku SQL Synapse. `OPENROWSET`Funkcja umożliwia określenie podzestawu właściwości, które mają zostać odczytane, oraz dokładne typy kolumn w `WITH` klauzuli:

### <a name="openrowset-with-key"></a>[OPENROWSET z kluczem](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET z poświadczeniem](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
Wynik tego zapytania może wyglądać jak w poniższej tabeli:

| date_rep | padkach | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Aby uzyskać więcej informacji na temat typów SQL, które powinny być używane dla wartości Azure Cosmos DB, zobacz [reguły dotyczące mapowań typów SQL](#azure-cosmos-db-to-sql-type-mappings) na końcu artykułu.

## <a name="create-view"></a>Utwórz widok

Tworzenie widoków w bazach danych Master lub default nie jest zalecane ani obsługiwane. W związku z tym musisz utworzyć bazę danych użytkownika dla widoków.

Po zidentyfikowaniu schematu możesz przygotować widok na podstawie danych Azure Cosmos DB. Klucz konta Azure Cosmos DB należy umieścić w osobnym poświadczeniu i odwołać się do tego poświadczenia z `OPENROWSET` funkcji. Nie przechowuj klucza konta w definicji widoku.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Nie należy używać `OPENROWSET` bez jawnie zdefiniowanego schematu, ponieważ może to mieć wpływ na wydajność. Upewnij się, że dla kolumn używane są najmniejsze możliwe rozmiary (na przykład VARCHAR (100) zamiast domyślnego VARCHAR (8000)). Należy użyć pewnego sortowania UTF-8 jako domyślnego sortowania bazy danych lub ustawić jako jawne sortowanie kolumn, aby uniknąć [problemu z konwersją UTF-8](../troubleshoot/reading-utf8-text.md). Sortowanie `Latin1_General_100_BIN2_UTF8` zapewnia najlepszą wydajność podczas filtrowania danych przy użyciu niektórych kolumn ciągów.

## <a name="query-nested-objects-and-arrays"></a>Wykonywanie zapytań względem zagnieżdżonych obiektów i tablic

Dzięki Azure Cosmos DB można reprezentować bardziej złożone modele danych przez złożenie ich jako obiektów zagnieżdżonych lub tablic. AutoSync funkcja linku usługi Azure Synapse dla Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych, które umożliwiają wykonywanie bogatych zapytań z puli SQL bezserwerowej.

Na przykład zestaw danych z zestawu [przewodów-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) zawiera dokumenty JSON, które są zgodne z tą strukturą:

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

Zagnieżdżone obiekty i tablice w Azure Cosmos DB są reprezentowane jako ciągi JSON w wyniku zapytania, gdy `OPENROWSET` Funkcja je odczytuje. Możesz określić ścieżki do wartości zagnieżdżonych w obiektach, gdy używasz `WITH` klauzuli:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Wynik tego zapytania może wyglądać jak w poniższej tabeli:

| paper_id | tytuł | metadane | autorów |
| --- | --- | --- |
| bb11206963e831f... | Dodatkowe informacje o epidemiach ekonomicznych... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Użycie surowic Convalescent w odporności E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) bulw i B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Dowiedz się więcej [na temat analizowania złożonych typów danych w linku Synapse platformy Azure](../how-to-analyze-complex-schema.md) i [zagnieżdżonych strukturach w puli SQL bezserwerowej](query-parquet-nested-types.md).

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , podobnie jak zamiast `Mélade` , sortowanie bazy danych nie jest ustawione na sortowanie [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) .
> [Zmień sortowanie bazy danych](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na sortowanie UTF-8 przy użyciu instrukcji SQL, takiej jak `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Spłaszczanie tablic zagnieżdżonych

Azure Cosmos DB dane mogą mieć zagnieżdżone podtablice, takie jak tablica autora, z zestawu danych z [przewodu-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

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

W niektórych przypadkach może być konieczne "dołączenie" właściwości z górnego elementu (metadane) do wszystkich elementów tablicy (autorów). Bezserwerowa Pula SQL umożliwia spłaszczonie zagnieżdżonych struktur przez zastosowanie `OPENJSON` funkcji w tablicy zagnieżdżonej:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
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

Wynik tego zapytania może wyglądać jak w poniższej tabeli:

| tytuł | autorów | pierwszego | ostatni | przynależności |
| --- | --- | --- | --- | --- |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Dodatkowe informacje o epidemiach ekonomicznych... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | czwart # |`{"laboratory":"","institution":"U…` | 
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Dodatkowe informacje o epidemiach ekonomicznych... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Jeśli zobaczysz nieoczekiwane znaki w tekście `MÃƒÂ©lade` , podobnie jak zamiast `Mélade` , sortowanie bazy danych nie jest ustawione na sortowanie [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) . [Zmień sortowanie bazy danych](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) na sortowanie UTF-8 przy użyciu instrukcji SQL, takiej jak `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB z mapowaniami typów SQL

Chociaż magazyn transakcyjny Azure Cosmos DB jest schematem niezależny od, magazyn analityczny jest schematized do optymalizacji pod kątem wydajności zapytań analitycznych. Dzięki funkcji AutoSync usługi Azure Synapse link Azure Cosmos DB zarządza reprezentacją schematu w magazynie analitycznym, który obejmuje obsługę zagnieżdżonych typów danych. Ponieważ pula SQL bezserwerowa wysyła zapytanie do magazynu analitycznego, ważne jest, aby zrozumieć, jak mapować Azure Cosmos DB dane wejściowe do typów danych SQL.

Konta Azure Cosmos DB interfejsu API języka SQL (rdzeń) obsługują typy właściwości JSON number, String, Boolean, null, zagnieżdżony obiekt lub Array. Jeśli używasz klauzuli w programie, musisz wybrać typy SQL zgodne z tymi typami JSON `WITH` `OPENROWSET` . W poniższej tabeli przedstawiono typy kolumn SQL, które powinny być używane dla różnych typów właściwości w Azure Cosmos DB.

| Typ właściwości Azure Cosmos DB | Typ kolumny SQL |
| --- | --- |
| Wartość logiczna | bit |
| Liczba całkowita | bigint |
| Liczba dziesiętna | float |
| Ciąg | varchar (sortowanie bazy danych UTF-8) |
| Data i godzina (ciąg w formacie ISO) | varchar (30) |
| Data i godzina (Sygnatura czasowa systemu UNIX) | bigint |
| Zero | `any SQL type` 
| Zagnieżdżony obiekt lub tablica | varchar (max) (sortowanie bazy danych w formacie UTF-8), Zserializowany jako tekst JSON |

## <a name="full-fidelity-schema"></a>Schemat pełnej wierności

Azure Cosmos DB schemat pełnej wierności rejestruje zarówno wartości, jak i ich najlepsze dopasowanie dla każdej właściwości w kontenerze. `OPENROWSET`Funkcja w kontenerze z pełnym schematem wierności zapewnia zarówno typ, jak i rzeczywistą wartość w każdej komórce. Załóżmy, że następujące zapytanie odczytuje elementy z kontenera z pełnym schematem wierności:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
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

Dla każdej wartości można zobaczyć typ identyfikowany w Azure Cosmos DB elemencie kontenera. Większość wartości `date_rep` właściwości zawiera `date` wartości, ale niektóre z nich są nieprawidłowo przechowywane jako ciągi w Azure Cosmos DB. Schemat pełnej wierności zwróci zarówno poprawnie wpisaną `date` wartość, jak i niepoprawnie sformatowane `string` wartości.
Liczba przypadków to informacje przechowywane jako `int32` wartość, ale istnieje jedna wartość wprowadzona jako liczba dziesiętna. Ta wartość ma `float64` Typ. Jeśli istnieją wartości, które przekraczają największą `int32` liczbę, będą one przechowywane jako `int64` Typ. Wszystkie `geo_id` wartości w tym przykładzie są przechowywane jako `string` typy.

> [!IMPORTANT]
> `OPENROWSET`Funkcja bez `WITH` klauzuli ujawnia obie wartości z oczekiwanymi typami i wartościami z nieprawidłowo wprowadzonymi typami. Ta funkcja została zaprojektowana na potrzeby eksploracji danych, a nie do raportowania. Nie Analizuj wartości JSON zwracanych z tej funkcji do kompilowania raportów. Użyj jawnej [klauzuli with](#query-items-with-full-fidelity-schema) , aby utworzyć raporty. Należy wyczyścić wartości, które mają niepoprawne typy w kontenerze Azure Cosmos DB, aby zastosować poprawki w magazynie analitycznym pełnej wierności.

Jeśli zachodzi potrzeba zbadania Azure Cosmos DB kont interfejsu API usługi Mongo DB, można dowiedzieć się więcej o pełnej reprezentacji schematu w magazynie analitycznym i nazwach właściwości rozszerzonych, które mają być używane w [co Azure Cosmos DB magazynie analitycznym?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Wykonaj zapytania o elementy ze schematem pełnej wierności

Podczas wykonywania zapytania o cały schemat wierności należy jawnie określić typ SQL i oczekiwany typ właściwości Azure Cosmos DB w `WITH` klauzuli.

W poniższym przykładzie przyjęto założenie, że `string` jest to poprawny typ dla `geo_id` właściwości i `int32` jest poprawnym typem `cases` Właściwości:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Wartości dla `geo_id` i `cases` , które mają inne typy, zostaną zwrócone jako `NULL` wartości. To zapytanie będzie odwoływać się tylko do `cases` określonego typu w wyrażeniu ( `cases.int32` ).

Jeśli masz wartości z innymi typami ( `cases.int64` , `cases.float64` ), które nie mogą być czyszczone w kontenerze Azure Cosmos DB, musisz jawnie odwoływać się do nich w `WITH` klauzuli i połączyć wyniki. Następujące zapytanie agreguje zarówno `int32` , `int64` ,, i `float64` przechowywane w `cases` kolumnie:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

W tym przykładzie liczba przypadków jest przechowywana w postaci `int32` `int64` lub `float64` wartości. Wszystkie wartości muszą zostać wyodrębnione, aby obliczyć liczbę przypadków na kraj.

## <a name="known-issues"></a>Znane problemy

- Bezserwerowa Pula SQL zwróci ostrzeżenie czasu kompilacji, jeśli `OPENROWSET` sortowanie kolumn nie ma kodowania UTF-8. Można łatwo zmienić sortowanie domyślne dla wszystkich `OPENROWSET` funkcji działających w bieżącej bazie danych przy użyciu instrukcji języka T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

W poniższej tabeli przedstawiono możliwe błędy i akcje rozwiązywania problemów.

| Błąd | Główna przyczyna |
| --- | --- |
| Błędy składniowe:<br/> -Nieprawidłowa składnia w sąsiedztwie `Openrowset`<br/> - `...` nie jest rozpoznawaną `BULK OPENROWSET` opcją dostawcy.<br/> -Nieprawidłowa składnia w sąsiedztwie `...` | Możliwe przyczyny główne:<br/> — Nie używa CosmosDB jako pierwszego parametru.<br/> — Przy użyciu literału ciągu zamiast identyfikatora w trzecim parametrze.<br/> — Nie określono trzeciego parametru (nazwa kontenera). |
| Wystąpił błąd w parametrach połączenia CosmosDB. | -Nie określono konta, bazy danych lub klucza. <br/> -Istnieje kilka opcji w parametrach połączenia, które nie zostały rozpoznane.<br/> -Średnik ( `;` ) jest umieszczany na końcu ciągu połączenia. |
| Rozpoznawanie ścieżki CosmosDB nie powiodło się z błędem "niepoprawna nazwa konta" lub "niepoprawna nazwa bazy danych". | Nie można odnaleźć określonej nazwy konta, nazwy bazy danych lub kontenera albo magazyn analityczny nie został włączony do określonej kolekcji.|
| Rozpoznanie ścieżki CosmosDB nie powiodło się z powodu błędu "niepoprawna wartość wpisu tajnego" lub "wpis tajny ma wartość null lub jest pusty". | Klucz konta jest nieprawidłowy lub nie istnieje. |
| Kolumna `column name` typu `type name` nie jest zgodna z typem danych zewnętrznych `type name` . | Określony typ kolumny w `WITH` klauzuli nie jest zgodny z typem w kontenerze Azure Cosmos DB. Spróbuj zmienić typ kolumny w sposób opisany w sekcji [Azure Cosmos DB na mapowania typu SQL](#azure-cosmos-db-to-sql-type-mappings)lub Użyj `VARCHAR` typu. |
| Kolumna zawiera `NULL` wartości we wszystkich komórkach. | Prawdopodobnie Nieprawidłowa nazwa kolumny lub wyrażenie ścieżki w `WITH` klauzuli. Nazwa kolumny (lub wyrażenie ścieżki po typie kolumny) w `WITH` klauzuli musi być zgodna z nazwą właściwości w kolekcji Azure Cosmos DB. W porównaniu *z rozróżnianiem wielkości* liter. Na przykład `productCode` i `ProductCode` są różnymi właściwościami. |

Sugestie i problemy można zgłaszać na [stronie opinii o usłudze Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Użyj Power BI i bezserwerowej puli SQL z linkiem usługi Azure Synapse](../../cosmos-db/synapse-link-power-bi.md)
- [Tworzenie i Używanie widoków w puli SQL bezserwerowej](create-use-views.md)
- [Samouczek dotyczący tworzenia widoków puli SQL bezserwerowych na Azure Cosmos DB i łączenia ich z modelami Power BI za pośrednictwem zapytania bezpośredniego](./tutorial-data-analyst.md)
