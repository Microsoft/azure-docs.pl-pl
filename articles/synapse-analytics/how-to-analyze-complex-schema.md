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
ms.openlocfilehash: fdf3dc56575a45ad0c9e716054184ba2691133ba
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831706"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analizowanie złożonych typów danych w usłudze Azure Synapse Analytics

Ten artykuł dotyczy Parquet plików i kontenerów w [linku Synapse w celu Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Wyjaśniono, jak użytkownicy mogą używać platformy Spark lub SQL do odczytywania lub przekształcania danych za pomocą złożonych schematów, takich jak tablice lub zagnieżdżone struktury. Poniższy przykład jest zakończony pojedynczym dokumentem, ale można łatwo skalować do miliardów dokumentów przy użyciu platformy Spark lub SQL. Kod zawarty w tym artykule używa PySpark (Python).

## <a name="use-case"></a>Przypadek użycia

Złożone typy danych są coraz częściej popularne i stanowią wyzwanie dla inżynierów danych jako analiza zagnieżdżonego schematu i tablic w celu uwzględnienia czasochłonnych i złożonych zapytań SQL. Ponadto może być trudne zmiana nazwy lub rzutowanie typu danych kolumn zagnieżdżonych. Ponadto podczas pracy z głęboko zagnieżdżonymi obiektami powstają problemy z wydajnością.

Inżynierowie danych muszą zrozumieć, jak efektywnie przetwarzać złożone typy danych i łatwo uzyskiwać dostęp do wszystkich użytkowników.

W poniższym przykładzie Synapse Spark jest używany do odczytywania i przekształcania obiektów w płaską strukturę za pomocą ramek danych. Synapse programu SQL Server jest używany do wykonywania zapytań dotyczących takich obiektów bezpośrednio i zwracają te wyniki w postaci zwykłej tabeli.

## <a name="what-are-arrays-and-nested-structures"></a>Co to są tablice i zagnieżdżone struktury?

Poniższy obiekt pochodzi z usługi [App Insight](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). W tym obiekcie istnieją zagnieżdżone struktury i tablice zawierające zagnieżdżone struktury.

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
Podczas drukowania schematu ramki danych obiektu (o nazwie **DF**) przy użyciu polecenia zostanie `df.printschema` wyświetlona następująca reprezentacja:

* Żółty kolor reprezentuje strukturę zagnieżdżoną
* Zielony kolor reprezentuje tablicę z dwoma elementami

[![Źródło schematu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**i **_etag** zostały dodane do systemu, ponieważ dokument został pozyskany w Azure Cosmos DB magazynie transakcyjnym.

Ramka danych powyżej liczy się tylko dla 5 kolumn i 1 wiersz. Po przekształceniu, nadzorowana ramka danych będzie zawierać 13 kolumn i 2 wiersze w formacie tabelarycznym.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Spłaszczanie zagnieżdżonych struktur i rozłożenie tablic za pomocą Apache Spark

Dzięki Synapse Spark można łatwo przekształcać zagnieżdżone struktury w kolumny i elementy tablicy w wiele wierszy. Poniższe kroki mogą służyć do implementacji.

[![Kroki transformacji Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Krok 1**. zdefiniowano funkcję, aby spłaszczyć zagnieżdżony schemat. Ta funkcja może być używana bez zmian. Utwórz komórkę w [notesie PySpark](quickstart-apache-spark-notebook.md) z następującą funkcją:

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

**Krok 2**. Użyj funkcji, aby spłaszczyć zagnieżdżony schemat ramki danych (**DF**) do nowej ramki danych `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkcja Display powinna zwracać 10 kolumn i 1 wiersz. Tablica i jej zagnieżdżone elementy nadal istnieją.

**Krok 3**. Przekształć tablicę `context_custom_dimensions` w ramce danych `df_flat` w nową ramkę Dataframe `df_flat_explode` . W poniższym kodzie definiujemy również, którą kolumnę wybrać:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkcja Display powinna zwracać 10 kolumn i 2 wiersze. Następnym krokiem jest spłaszczenie zagnieżdżonych schematów przy użyciu funkcji zdefiniowanej w kroku 1.

**Krok 4**. Użyj funkcji, aby spłaszczyć zagnieżdżony schemat ramki danych `df_flat_explode` do nowej ramki danych `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkcja Display powinna zawierać 13 kolumn i 2 wiersze.

Funkcja `printSchema` ramki danych `df_flat_explode_flat` zwraca następujący wynik:

[![Schemat końcowy](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Odczytaj tablice i zagnieżdżone struktury bezpośrednio przy użyciu programu SQL Server

Wykonywanie zapytań i Tworzenie widoków i tabel za pośrednictwem takich obiektów jest możliwe bez programu SQL Server.

Po pierwsze, w zależności od tego, jak przechowywane są dane, użytkownicy powinni używać następującej taksonomii. Wszystkie elementy prezentowane w Wielkiej litery są specyficzne dla Twojego przypadku użycia:

| GROMADZON              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = AccountName; Database = DatabaseName; kolekcja = CollectionName; region = REGIONTOQUERY ', Secret = ' YOURSECRET ' |"CosmosDB" (link Synapse)|


> [!NOTE]
> Program SQL Server nie będzie obsługiwał linku połączonej usługi dla Synapse na potrzeby przekazywania na platformie Azure Cosmos i AAD. Ta funkcja jest obecnie objęta bramą w wersji zapoznawczej dla linku Synapse.

Zastąp każde pole w następujący sposób:
* "Twoje dane ZBIORCZe powyżej" = parametry połączenia ze źródłem danych, z którym się łączysz
* "Typ powyżej" = format używany do nawiązywania połączenia ze źródłem

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

Pierwszy typ operacji jest wskazany w następującym wierszu kodu, który definiuje kolumnę o nazwie `contextdataeventTime` odwołującą się do zagnieżdżonego elementu: Context. Data. eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Ten wiersz definiuje kolumnę o nazwie contextdataeventTime, która odwołuje się do elementu Nest: Context>Data>eventTime

Drugi typ operacji używa `cross apply` do tworzenia nowych wierszy dla każdego elementu w tablicy, a następnie z definiuje każdy zagnieżdżony obiekt podobny do pierwszego punktu punktora: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Jeśli tablica ma 5 elementów z 4 zagnieżdżonymi strukturami, bezserwerowa SQL zwróci 5 wierszy i 4 kolumny. Program SQL Server może wykonywać zapytania w miejscu, mapować tablicę w dwóch wierszach i wyświetlać wszystkie zagnieżdżone struktury w kolumnach.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak wysyłać zapytania Synapse do Azure Cosmos DB za pomocą platformy Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Typy zagnieżdżone zapytania Parquet](./sql/query-parquet-nested-types.md) 
