---
title: Udostępnione tabele metadanych
description: Usługa Azure Synapse Analytics oferuje współużytkowany model metadanych, w którym Tworzenie tabeli w Apache Spark będzie miało dostęp z poziomu usług SQL na żądanie (wersja zapoznawcza) i aparatów puli SQL bez duplikowania danych.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: d19376d21081d899d8ff7226c6d7c5b76267fabf
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280458"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabele udostępnione metadanych usługi Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obliczeniowych obszarów roboczych w celu udostępniania baz danych i tabel z kopią zapasową Parquet między pulami Apache Spark (wersja zapoznawcza) i aparatem SQL na żądanie (wersja zapoznawcza).

Po utworzeniu bazy danych przez zadanie platformy Spark można utworzyć w niej tabele z platformą Spark, która używa Parquet jako formatu magazynu. Te tabele staną się natychmiast dostępne do wykonywania zapytań za pomocą dowolnych pul platformy Azure Synapse Workspace. Mogą być również używane z dowolnego zadania platformy Spark z uprawnieniami.

Tabele utworzone, zarządzane i zewnętrzne są również udostępniane jako tabele zewnętrzne o tej samej nazwie w odpowiedniej zsynchronizowanej bazie danych w programie SQL na żądanie. [Uwidacznianie tabeli Spark w programie SQL Server](#expose-a-spark-table-in-sql) zawiera więcej szczegółów na temat synchronizacji tabel.

Ponieważ tabele są zsynchronizowane z usługą SQL na żądanie asynchronicznie, nastąpi opóźnienie do momentu ich wyświetlenia.

## <a name="manage-a-spark-created-table"></a>Zarządzanie tabelą utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Można na przykład usunąć ją za pośrednictwem zadania puli platformy Spark i utworzyć w niej tabele z platformy Spark.

W przypadku tworzenia obiektów w takiej bazie danych z poziomu serwera SQL na żądanie lub próby porzucenia bazy danych operacja zakończy się pomyślnie, ale oryginalna baza danych platformy Spark nie zostanie zmieniona.

## <a name="expose-a-spark-table-in-sql"></a>Uwidacznianie tabeli Spark w programie SQL

### <a name="shared-spark-tables"></a>Udostępnione tabele platformy Spark

Platforma Spark oferuje dwa typy tabel, które usługa Azure Synapse udostępnia automatycznie w języku SQL:

- Zarządzane tabele

  Platforma Spark udostępnia wiele opcji przechowywania danych w tabelach zarządzanych, takich jak TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA i LIBSVM. Te pliki są zwykle przechowywane w katalogu, w `warehouse` którym są przechowywane zarządzane dane tabeli.

- Tabele zewnętrzne

  Platforma Spark udostępnia również sposoby tworzenia tabel zewnętrznych przez istniejące dane, udostępniając `LOCATION` opcję lub korzystając z formatu Hive. Takie tabele zewnętrzne mogą znajdować się w różnych formatach danych, w tym Parquet.

Usługa Azure Synapse obecnie udostępnia tylko zarządzane i zewnętrzne tabele Spark, które przechowują dane w formacie Parquet przy użyciu aparatów SQL. Tabele obsługiwane przez inne formaty nie są automatycznie synchronizowane. Można synchronizować takie tabele jawnie jako tabelę zewnętrzną we własnej bazie danych SQL, jeśli aparat SQL obsługuje format podstawowy tabeli.

### <a name="share-spark-tables"></a>Udostępnianie tabel platformy Spark

Zarządzane i zewnętrzne tabele Spark uwidocznione w aparacie SQL jako tabele zewnętrzne o następujących właściwościach:

- Źródło danych tabeli zewnętrznej SQL to źródło danych reprezentujące folder lokalizacji tabeli Spark.
- Format pliku tabeli zewnętrznej SQL to Parquet.
- Poświadczenia dostępu do tabeli zewnętrznej SQL są przekazywane.

Ponieważ wszystkie nazwy tabel platformy Spark są prawidłowymi nazwami tabel SQL, a wszystkie nazwy kolumn platformy Spark są prawidłowymi nazwami kolumn SQL, nazwy tabel i kolumn platformy Spark będą używane dla tabeli zewnętrznej SQL.

Tabele platformy Spark zapewniają różne typy danych niż aparaty SQL Synapse. W poniższej tabeli przedstawiono mapowanie typów danych tabeli platformy Spark na typy SQL:

| Typ danych Spark | Typ danych SQL | Komentarze |
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
| `string`    |    `varchar(max)`   | Z sortowaniem `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializacja w formacie JSON z sortowaniem `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serializacja w formacie JSON z sortowaniem `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serializacja w formacie JSON z sortowaniem `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Model zabezpieczeń

Bazy danych i tabele platformy Spark oraz zsynchronizowane reprezentacje w aparacie SQL zostaną zabezpieczone na podstawowym poziomie magazynu. Ponieważ nie mają obecnie uprawnień do samych obiektów, obiekty mogą być widoczne w Eksploratorze obiektów.

Podmiot zabezpieczeń tworzący zarządzaną tabelę jest traktowany jako właściciel tej tabeli i ma wszystkie prawa do tabeli, a także bazowe foldery i pliki. Ponadto właściciel bazy danych automatycznie stanie się współwłaścicielem tabeli.

Jeśli utworzysz tabelę zewnętrzną Spark lub SQL z przekazywaniem uwierzytelniania, dane są zabezpieczane tylko na poziomach folderów i plików. Jeśli ktoś wysyła zapytanie do tego typu tabeli zewnętrznej, tożsamość zabezpieczeń przesyłającego zapytania jest przekazywana do systemu plików, co spowoduje sprawdzenie praw dostępu.

Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień do folderów i plików, zobacz [udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="examples"></a>Przykłady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Tworzenie zarządzanej tabeli obsługiwanej przez Parquet w usłudze Spark i zapytanie z usługi SQL na żądanie

W tym scenariuszu istnieje baza danych Spark o nazwie `mytestdb` . Zobacz [Tworzenie bazy danych Spark i nawiązywanie z nią połączenia na żądanie SQL](database.md#create-and-connect-to-spark-database-with-sql-on-demand).

Utwórz zarządzaną tabelę Spark za pomocą SparkSQL, uruchamiając następujące polecenie:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

To polecenie tworzy tabelę `myParquetTable` w bazie danych `mytestdb` . Po krótkim opóźnieniu można zobaczyć tabelę w SQL na żądanie. Na przykład Uruchom poniższą instrukcję z poziomu usługi SQL na żądanie.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Sprawdź, czy `myParquetTable` jest uwzględniony w wynikach.

>[!NOTE]
>Tabela, która nie korzysta z Parquet, ponieważ jej format przechowywania nie zostanie zsynchronizowany.

Następnie Wstaw niektóre wartości do tabeli z platformy Spark, na przykład z następującymi instrukcjami języka C# Spark w notesie C#:

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

Teraz można odczytywać dane z programu SQL na żądanie w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Należy uzyskać następujący wiersz jako wynik:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Tworzenie tabeli zewnętrznej obsługiwanej przez Parquet w usłudze Spark i wykonywanie zapytań z poziomu bazy danych SQL na żądanie

W tym przykładzie należy utworzyć zewnętrzną tabelę Spark dla plików danych Parquet utworzonych w poprzednim przykładzie dla zarządzanej tabeli.

Na przykład z SparkSQL Run:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Zastąp symbol zastępczy `<fs>` nazwą systemu plików, który jest domyślnym systemem plików obszaru roboczego i symbolem zastępczym `<synapse_ws>` z nazwą obszaru roboczego Synapse, którego używasz do uruchamiania tego przykładu.

Poprzedni przykład tworzy tabelę `myExtneralParquetTable` w bazie danych `mytestdb` . Po krótkim opóźnieniu można zobaczyć tabelę w SQL na żądanie. Na przykład Uruchom poniższą instrukcję z poziomu usługi SQL na żądanie.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Sprawdź, czy `myExternalParquetTable` jest uwzględniony w wynikach.

Teraz można odczytywać dane z programu SQL na żądanie w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Należy uzyskać następujący wiersz jako wynik:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych metadanych usługi Azure Synapse Analytics](overview.md)
- [Dowiedz się więcej o udostępnionej bazie danych metadanych usługi Azure Synapse Analytics](database.md)


