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
ms.openlocfilehash: 51422bd47b5bd2d7d5103c154e90eaa910396024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661022"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Analizowanie złożonych typów danych w usłudze Azure Synapse Analytics

Ten artykuł dotyczy plików Parquet i kontenerów w [usłudze Azure Synapse Link Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Platformy Spark lub SQL można używać do odczytywania lub przekształcania danych za pomocą złożonych schematów, takich jak tablice lub struktury zagnieżdżone. Poniższy przykład jest zakończony przy użyciu jednego dokumentu, ale można łatwo skalować do miliardów dokumentów za pomocą platformy Spark lub SQL. Kod zawarty w tym artykule używa PySpark (Python).

## <a name="use-case"></a>Przypadek użycia

Złożone typy danych są coraz częściej popularne i stanowią wyzwanie dla inżynierów danych. Analizowanie zagnieżdżonych schematów i tablic może dotyczyć czasochłonnych i złożonych zapytań SQL. Ponadto może być trudne zmiana nazwy lub rzutowanie typu danych kolumn zagnieżdżonych. Ponadto podczas pracy z głęboko zagnieżdżonymi obiektami można napotkać problemy z wydajnością.

Inżynierowie danych muszą zrozumieć, jak efektywnie przetwarzać złożone typy danych i łatwo uzyskiwać dostęp do wszystkich użytkowników. W poniższym przykładzie używasz platformy Spark w usłudze Azure Synapse Analytics do odczytywania i przekształcania obiektów w płaską strukturę za pomocą ramek danych. Używasz bezserwerowego modelu SQL w usłudze Azure Synapse Analytics do wykonywania zapytań dotyczących takich obiektów bezpośrednio i zwracają te wyniki w postaci zwykłej tabeli.

## <a name="what-are-arrays-and-nested-structures"></a>Co to są tablice i zagnieżdżone struktury?

Poniższy obiekt pochodzi z [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). W tym obiekcie istnieją zagnieżdżone struktury i tablice zawierające zagnieżdżone struktury.

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
Podczas drukowania schematu ramki danych obiektu (o nazwie **DF**) przy użyciu polecenia `df.printschema` zostanie wyświetlona następująca reprezentacja:

* Żółty reprezentuje zagnieżdżone struktury.
* Zielony reprezentuje tablicę z dwoma elementami.

[![Kod z wyróżnieniem żółtym i zielonym, Pokazywanie pochodzenia schematu](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` i zostały `_etag` dodane do systemu, ponieważ dokument został pozyskany do magazynu transakcyjnego Azure Cosmos DB.

Poprzednia ramka danych liczy się tylko dla 5 kolumn i 1 wiersz. Po przekształceniu, nadzorowana ramka danych będzie zawierać 13 kolumn i 2 wiersze w formacie tabelarycznym.

## <a name="flatten-nested-structures-and-explode-arrays"></a>Spłaszczanie zagnieżdżonych struktur i rozłożenie tablic

Dzięki platformie Spark w usłudze Azure Synapse Analytics można łatwo przekształcić zagnieżdżone struktury w kolumny i elementy tablicy w wiele wierszy. Wykonaj następujące kroki, aby uzyskać implementację programu.

[![Schemat blokowy przedstawiający kroki dla transformacji Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>Zdefiniuj funkcję, aby spłaszczyć zagnieżdżony schemat

Tej funkcji można użyć bez zmiany. Utwórz komórkę w [notesie PySpark](quickstart-apache-spark-notebook.md) z następującą funkcją:

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

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Użyj funkcji, aby spłaszczyć zagnieżdżony schemat

W tym kroku zostanie spłaszczony zagnieżdżony schemat ramki danych (**DF**) do nowej ramki danych ( `df_flat` ):

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funkcja Display powinna zwracać 10 kolumn i 1 wiersz. Tablica i jej zagnieżdżone elementy nadal istnieją.

### <a name="transform-the-array"></a>Przekształć tablicę

W tym miejscu możesz przekształcić tablicę, `context_custom_dimensions` w ramce danych `df_flat` do nowej ramki danych `df_flat_explode` . W poniższym kodzie definiuje się również kolumnę do wybrania:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funkcja Display powinna zwracać 10 kolumn i 2 wiersze. Następnym krokiem jest spłaszczenie zagnieżdżonych schematów przy użyciu funkcji zdefiniowanej w kroku 1.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>Użyj funkcji, aby spłaszczyć zagnieżdżony schemat

Na koniec użyj funkcji, aby spłaszczyć zagnieżdżony schemat ramki danych `df_flat_explode` do nowej ramki danych `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funkcja Display powinna zawierać 13 kolumn i 2 wiersze.

Funkcja `printSchema` ramki danych `df_flat_explode_flat` zwraca następujący wynik:

[![Kod pokazujący końcowy schemat](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Bezpośrednie odczytywanie tablic i struktur zagnieżdżonych

Dzięki modelowi bez użycia serwera SQL można wykonywać zapytania i tworzyć widoki i tabele za pośrednictwem takich obiektów.

Po pierwsze, w zależności od tego, jak przechowywane są dane, użytkownicy powinni używać następującej taksonomii. Wszystko, co jest pisane wielkimi literami, jest specyficzne dla Twojego przypadku użycia:

| Zbiorcze | Format |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = AccountName; Database = DatabaseName; kolekcja = CollectionName; region = REGIONTOQUERY ', Secret = ' YOURSECRET ' |"CosmosDB" (link Azure Synapse)|


Zastąp każde pole w następujący sposób:
* "Powyższe dane ZBIORCZe" to parametry połączenia źródła danych, z którym się łączysz.
* "Typ powyżej" jest formatem używanym do nawiązywania połączenia ze źródłem.

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

- Pierwszy typ operacji jest wskazany w następującym wierszu kodu, który definiuje kolumnę o nazwie, `contextdataeventTime` która odwołuje się do zagnieżdżonego elementu, `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Ten wiersz definiuje kolumnę o nazwie `contextdataeventTime` odwołującą się do elementu zagnieżdżonego `Context>Data>eventTime` .

- Drugi typ operacji używa `cross apply` do tworzenia nowych wierszy dla każdego elementu w tablicy. Następnie definiuje każdy zagnieżdżony obiekt. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Jeśli tablica ma 5 elementów z 4 zagnieżdżonymi strukturami, model bezserwerowy SQL zwraca 5 wierszy i 4 kolumny. Model bezserwerowy SQL może wykonywać zapytania na miejscu, mapować tablicę w dwóch wierszach i wyświetlać wszystkie zagnieżdżone struktury w kolumnach.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak wysyłać zapytania Synapse do Azure Cosmos DB za pomocą platformy Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](./sql/query-parquet-nested-types.md) 
