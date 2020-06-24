---
title: Analizuj schemat z tablicami i zagnieżdżonymi strukturami
description: Jak analizować tablice i zagnieżdżone struktury przy użyciu Apache Spark i SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908171"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analizowanie złożonych typów danych w Synapse

Ten artykuł dotyczy plików Parquet i kontenerów w **usłudze Azure Synapse Link Azure Cosmos DB**. Wyjaśniono, jak użytkownicy mogą używać platformy Spark lub SQL do odczytywania lub przekształcania danych ze złożonym schematem, takim jak tablice lub zagnieżdżone struktury. Poniższy przykład jest realizowany przy użyciu jednego dokumentu, ale można łatwo skalować do miliardów dokumentów przy użyciu platformy Spark lub SQL. Poniższy kod używa PySpark (Python).

## <a name="use-case"></a>Przypadek użycia

W przypadku nowoczesnych typów danych złożone typy danych często są popularne do obsługi i reprezentowania wyzwania dla inżynierów danych. Analizowanie schematu i tablic zagnieżdżonych stwarza pewne wyzwania:
* Złożone do zapisywania zapytań SQL
* Trudne Zmienianie nazwy/Rzutowanie wartości DataType kolumn zagnieżdżonych
* Problemy z wydajnością trafień przy użyciu głęboko zagnieżdżonych obiektów

Inżynierowie danych muszą zrozumieć, jak efektywnie przetwarzać te typy danych i ułatwiać ich dostęp przez wszystkich użytkowników.

W poniższym przykładzie Synapse Spark będzie używany do odczytywania i przekształcania obiektów przez ramki danych w płaską strukturę. Synapse programu SQL Server jest używany do bezpośredniej kwerendy dotyczącej tych obiektów i zwracania wyników jako zwykłej tabeli.

## <a name="what-are-arrays-and-nested-structures"></a>Co to są tablice i zagnieżdżone struktury?

Poniższy obiekt pochodzi z usługi App Insight. W tym obiekcie istnieją zagnieżdżone struktury, ale również tablice zawierające struktury zagnieżdżone.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Przykład schematu dla tablic i zagnieżdżonych struktur
Podczas drukowania schematu ramki danych tego obiektu (o nazwie **DF**) z poleceniem **DF. printschema**zobaczymy następującą reprezentację:

* żółty kolor reprezentuje strukturę zagnieżdżoną
* zielony kolor reprezentuje tablicę z dwoma elementami

[![Źródło schematu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts i _etag zostały dodane w systemie, ponieważ dokument został pozyskany w Azure Cosmos DB magazynie transakcyjnym.

Ramka danych powyżej liczy się tylko dla 5 kolumn i 1 wiersz. Po przekształceniu, nadzorowana ramka danych będzie zawierać 13 kolumn i 2 wiersze w formacie tabelarycznym.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Spłaszczanie zagnieżdżonych struktur i rozłożenie tablic za pomocą Apache Spark

Dzięki Synapse Spark przekształceń zagnieżdżonych struktur do kolumn i elementów tablicy w wielu wierszach jest prosta. Poniższe kroki mogą być używane przez wszystkich użytkowników do ich implementacji.

[![Kroki transformacji Spark](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Krok 1**. zdefiniowano funkcję do spłaszczania zagnieżdżonego schematu. Ta funkcja może być używana bez zmian. Utwórz komórkę w notesie Pyspark z tą funkcją:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Krok 2**. Użyj funkcji, aby spłaszczyć zagnieżdżony schemat ramki danych **DF** do nowej ramki danych **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkcja Display powinna zwracać 10 kolumn i 1 wiersz. Tablica i jej zagnieżdżone elementy nadal istnieją.

**Krok 3**. przekształcamy teraz tablicę **context_custom_dimensions** w ramce danych **df_flat** do nowej ramki Dataframe **df_flat_explode**. W poniższym kodzie definiujemy również wybraną kolumnę:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkcja Display powinna zwracać następujący wynik: 10 kolumn i 2 wiersze. Następnym krokiem jest spłaszczenie zagnieżdżonych schematów przy użyciu funkcji zdefiniowanej w kroku 1.

**Krok 4**. Użyj funkcji, aby spłaszczyć zagnieżdżony schemat ramki danych **df_flat_explode** do nowej ramki danych **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkcja Display powinna zawierać 13 kolumn i 2 wiersze:

Funkcja printSchema ramki danych df_flat_explode_flat zwraca następujący wynik:

[![Schemat końcowy](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Odczytaj tablice i zagnieżdżone struktury bezpośrednio przy użyciu programu SQL Server

Wykonywanie zapytań, Tworzenie widoków i tabel za pośrednictwem takich obiektów jest możliwe bez programu SQL Server.

Po pierwsze, w zależności od tego, jak przechowywane są dane, użytkownicy powinni używać następującej taksonomii. WIELKIE litery są specyficzne dla Twojego przypadku użycia:

| GROMADZON              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = AccountName; Database = DatabaseName; kolekcja = CollectionName; region = REGIONTOQUERY, Secret = ' YOURSECRET ' |"CosmosDB" (link Synapse)|



**Program SQL Server** nie będzie obsługiwał usługi połączonej z usługą Azure Synapse w celu przekazywania Azure Cosmos DB i AAD. Ta funkcja jest obecnie objęta bramą w wersji zapoznawczej dla linku Synapse.

Zastąp poniżej:
* "Powyższe dane ZBIORCZe" przez parametry połączenia źródła danych, z którym się łączysz
* "Typ powyżej" przez format używany do nawiązywania połączenia ze źródłem

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Istnieją dwa różne typy operacji:
* Wiersz kodu poniżej zdefiniuje kolumnę o nazwie contextdataeventTime, która odwołuje się do zagnieżdżonego elementu: Context. Data. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Ten wiersz definiuje kolumnę o nazwie contextdataeventTime, która odwołuje się do elementu Nest: Context>Data>eventTime

* **zastosowanie krzyżowe** służy do tworzenia nowych wierszy dla każdego elementu w tablicy, a następnie za pomocą definiują każdy zagnieżdżony obiekt podobny do pierwszego punktu punktora: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Jeśli tablica ma 5 elementów z 4 zagnieżdżonymi strukturami, bezserwerowa SQL zwróci 5 wierszy i 4 kolumny.

Program SQL Server może wykonywać zapytania w miejscu, mapować tablicę w dwóch wierszach i wyświetlać wszystkie zagnieżdżone struktury w kolumnach.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak wysyłać zapytania do usługi Azure Synapse dla Azure Cosmos DB przy użyciu platformy Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Typy zagnieżdżone zapytania Parquet](./sql/query-parquet-nested-types.md) 