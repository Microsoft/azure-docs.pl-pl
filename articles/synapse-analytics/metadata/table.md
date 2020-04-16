---
title: Udostępnione tabele metadanych usługi Azure Synapse Analytics
description: Usługa Azure Synapse Analytics udostępnia model metadanych udostępnionych, w którym tworzenie tabeli w programie Apache Spark spowoduje, że będzie ona dostępna z aparatów sql na żądanie (wersja zapoznawcza) i puli SQL bez duplikowania danych.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424580"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Udostępnione tabele metadanych usługi Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszaru roboczego udostępnianie baz danych i tabel opartych na parkietach między pulami platformy Apache Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pulami SQL.

Po utworzeniu bazy danych przez zadanie Platformy Spark można utworzyć w nim tabele z programem Spark, które używają parkietu jako formatu magazynu. Te tabele natychmiast staną się dostępne do wykonywania zapytań przez dowolną z puli platformy Azure Synapse obszaru roboczego Spark. Mogą być również używane z dowolnego zadania platformy Spark podlega uprawnienia.

Tabele utworzone, zarządzane i zewnętrzne platformy Spark są również udostępniane jako tabele zewnętrzne o tej samej `$`nazwie w odpowiedniej zsynchronizowanej bazie danych w języku SQL na żądanie i w odpowiednich schematach z prefiksem w pulach SQL, w których włączona jest synchronizacja metadanych. [Uwidacznianie tabeli Spark w języku SQL](#exposing-a-spark-table-in-sql) zapewnia więcej szczegółów na temat synchronizacji tabeli.

Ponieważ tabele są synchronizowane z SQL na żądanie i pul SQL asynchronicznie, będzie opóźnienie, dopóki się nie pojawią.

Mapowanie tabel do tabel zewnętrznych, źródeł danych i formatów plików.

## <a name="manage-a-spark-created-table"></a>Zarządzanie tabelą utworzenia platformy Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Na przykład usuń go za pomocą zadania puli platformy Spark i utwórz w nim tabele z platformy Spark.

Jeśli utworzysz obiekty w takiej bazie danych z SQL na żądanie lub spróbujesz usunąć bazę danych, operacja zakończy się pomyślnie, ale oryginalna baza danych Platformy Spark nie zostanie zmieniona.

Jeśli spróbujesz upuścić zsynchronizowany schemat w puli SQL lub spróbuj utworzyć w nim tabelę, platforma Azure zwróci błąd.

## <a name="exposing-a-spark-table-in-sql"></a>Udostępnianie tabeli platformy Spark w języku SQL

### <a name="which-spark-tables-are-shared"></a>Które tabele Spark są współużytkowane

Platforma Spark udostępnia dwa typy tabel, które usługa Azure Synapse udostępnia automatycznie w języku SQL:

- Tabele zarządzane

  Platforma Spark udostępnia wiele opcji przechowywania danych w tabelach zarządzanych, takich jak TEKST, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA i LIBSVM. Pliki te są zwykle `warehouse` przechowywane w katalogu, w którym przechowywane są dane tabeli zarządzanej.

- Tabele zewnętrzne

  Platforma Spark udostępnia również sposoby tworzenia tabel zewnętrznych `LOCATION` nad istniejącymi danymi, udostępniając opcję lub używając formatu Hive. Takie tabele zewnętrzne mogą znajdować się w różnych formatach danych, w tym w parkietach.

Usługa Azure Synapse udostępnia obecnie tylko zarządzane i zewnętrzne tabele platformy Spark, które przechowują swoje dane w formacie Parkietu za pomocą aparatów SQL. Tabele wspierane przez inne formaty nie są automatycznie synchronizowane. Możesz być w stanie zsynchronizować takie tabele jawnie samodzielnie jako tabelę zewnętrzną we własnej bazie danych SQL, jeśli aparat SQL obsługuje podstawowy format tabeli.

### <a name="how-are-spark-tables-shared"></a>Jak są udostępniane tabele Spark

Współużytkowane zarządzane i zewnętrzne tabele Platformy Spark udostępniane w aparatach SQL jako tabele zewnętrzne z następującymi właściwościami:

- Źródłem danych tabeli zewnętrznej SQL jest źródło danych reprezentujące folder lokalizacji tabeli Spark.
- Format pliku tabeli zewnętrznej SQL to Parkiet.
- Poświadczenie dostępu tabeli zewnętrznej SQL jest przekazywane.

Ponieważ wszystkie nazwy tabeli Spark są prawidłowe nazwy tabel SQL i wszystkie nazwy kolumn Spark są prawidłowe nazwy kolumn SQL, spark tabeli i kolumn nazwy będą używane dla tabeli zewnętrznej SQL.

Tabele platformy Spark zawierają różne typy danych niż aparaty SQL Synapse. Następująca tabela mapuje typy danych tabeli Spark mapują na typy SQL:

| Typ danych spark | Typ danych SQL | Komentarze |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Z sortowania`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializuje w JSON z sortowania`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serializuje w JSON z sortowania`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serializuje w JSON z sortowania`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Model zabezpieczeń

Spark baz danych i tabel, a także ich zsynchronizowane reprezentacje w aparatach SQL będą zabezpieczone na poziomie magazynu podstawowej. Ponieważ obecnie nie mają uprawnień do samych obiektów, obiekty można zobaczyć w eksploratorze obiektów.

Podmiot zabezpieczeń, który tworzy tabelę zarządzaną, jest uważany za właściciela tej tabeli i ma wszystkie prawa do tabeli, a także do podstawowych folderów i plików. Ponadto właściciel bazy danych automatycznie stanie się współwłaścicielem tabeli.

Jeśli utworzysz tabelę zewnętrzną platformy Spark lub SQL z przekazywaniem uwierzytelniania, dane są zabezpieczone tylko na poziomie folderu i plików. Jeśli ktoś zapyta tego typu tabeli zewnętrznej, tożsamość zabezpieczeń przesyłający kwerendę jest przekazywana do systemu plików, który będzie sprawdzał prawa dostępu.

Aby uzyskać więcej informacji na temat ustawiania uprawnień do folderów i plików, zobacz [Udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="examples"></a>Przykłady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Tworzenie tabeli zarządzanej wspieranej przez parkiet w programie Spark i kwerendy z języka SQL na żądanie

W tym scenariuszu masz bazę danych Spark o nazwie `mytestdb`. Zobacz [Tworzenie & łączenie się z bazą danych Spark — SQL na żądanie](database.md#create--connect-to-spark-database---sql-on-demand).

Utwórz zarządzaną tabelę spark za pomocą sparksql, uruchamiając następujące polecenie:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Spowoduje to `myParquetTable` utworzenie tabeli w bazie danych `mytestdb`. Po krótkim opóźnieniu można wyświetlić tabelę w języku SQL na żądanie. Na przykład uruchom następującą instrukcję z SQL na żądanie.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Sprawdź, `myParquetTable` czy jest uwzględniony w wynikach.

>[!NOTE]
>Tabela, która nie używa parkietu jako formatu magazynu, nie zostanie zsynchronizowana.

Następnie należy wstawić niektóre wartości do tabeli z platformy Spark, na przykład z następującymi instrukcjami C# Spark w notesie języka C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Teraz możesz odczytać dane z SQL na żądanie w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

W rezultacie powinien zostać otrzymany następujący wiersz:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Tworzenie tabeli zewnętrznej wspieranej przez parkiet w programie Spark i wykonywanie zapytań z języka SQL na żądanie

W tym przykładzie utwórz zewnętrzną tabelę Platformy Spark nad plikami danych parkietu utworzonymi w poprzednim przykładzie tabeli zarządzanej.

Na przykład z sparksql uruchomić:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Zastąp `<fs>` symbol zastępczy nazwą systemu plików, która jest `<synapse_ws>` domyślnym systemem plików obszaru roboczego, oraz symbolem zastępczym nazwą obszaru roboczego synaps, którego używasz do uruchomienia w tym przykładzie.

W poprzednim przykładzie `myExtneralParquetTable` tworzy `mytestdb`tabelę w bazie danych . Po krótkim opóźnieniu można wyświetlić tabelę w języku SQL na żądanie. Na przykład uruchom następującą instrukcję z SQL na żądanie.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Sprawdź, `myExternalParquetTable` czy jest uwzględniony w wynikach.

Teraz możesz odczytać dane z SQL na żądanie w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

W rezultacie powinien zostać otrzymany następujący wiersz:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Wykonywanie zapytań o tabele platformy Spark w puli SQL

Za pomocą tabel utworzonych w poprzednich przykładach utwórz teraz `mysqlpool` pulę SQL w obszarze roboczym o nazwie, która umożliwia synchronizację metadanych (lub użyj już utworzonej puli z [uwidaczniania bazy danych Spark w puli SQL](database.md#exposing-a-spark-database-in-a-sql-pool).

Uruchom następującą instrukcję względem puli `mysqlpool` SQL:

```sql
SELECT * FROM sys.tables;
```

Sprawdź, czy `myParquetTable` `myExternalParquetTable` tabele i są `$mytestdb`widoczne w schemacie .

Teraz możesz odczytać dane z SQL na żądanie w następujący sposób:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

Powinieneś uzyskać takie same wyniki jak w przypadku sql na żądanie powyżej.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych metadanych usługi Azure Synapse Analytics](overview.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)


