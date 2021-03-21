---
title: Udostępnione tabele metadanych
description: Usługa Azure Synapse Analytics oferuje współużytkowany model metadanych, w którym Tworzenie tabeli w puli Apache Spark bezserwerowej udostępni ją z puli SQL bezserwerowej i dedykowanej puli SQL bez duplikowania danych.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: a8080720480beaeb7bc8692f2dcddddad5da0e3c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548465"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Tabele udostępnione metadanych usługi Azure Synapse Analytics


Usługa Azure Synapse Analytics umożliwia korzystanie z różnych aparatów obliczeniowych obszarów roboczych w celu udostępniania baz danych i tabel z Parquetmi, między pulami Apache Spark i bezserwerową pulą SQL.

Po utworzeniu bazy danych przez zadanie platformy Spark można utworzyć w niej tabele z platformą Spark, która używa Parquet jako formatu magazynu. Nazwy tabel zostaną przekonwertowane na małe litery i muszą być zapytania przy użyciu nazwy małymi literami. Te tabele staną się natychmiast dostępne do wykonywania zapytań za pomocą dowolnych pul platformy Azure Synapse Workspace. Mogą być również używane z dowolnego zadania platformy Spark z uprawnieniami.

Tabele utworzone, zarządzane i zewnętrzne są również udostępniane jako tabele zewnętrzne o tej samej nazwie w odpowiedniej zsynchronizowanej bazie danych w puli SQL bezserwerowej. [Uwidacznianie tabeli Spark w programie SQL Server](#expose-a-spark-table-in-sql) zawiera więcej szczegółów na temat synchronizacji tabel.

Ponieważ tabele są synchronizowane z bezserwerową pulą SQL, to opóźnienie zostanie wyświetlone dopiero po ich wyświetleniu.

## <a name="manage-a-spark-created-table"></a>Zarządzanie tabelą utworzoną przez platformę Spark

Użyj platformy Spark do zarządzania utworzonymi bazami danych platformy Spark. Można na przykład usunąć ją za pośrednictwem bezserwerowego zadania puli Apache Spark i utworzyć w nim tabele z platformy Spark.

W przypadku tworzenia obiektów w takiej bazie danych z puli SQL bezserwerowej lub próby porzucenia bazy danych operacja zakończy się niepowodzeniem. Nie można zmienić oryginalnej bazy danych Spark za pomocą puli SQL bezserwerowej.

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

Podmiot zabezpieczeń tworzący zarządzaną tabelę jest traktowany jako właściciel tej tabeli i ma wszystkie prawa do tabeli, a także do bazowych folderów i plików. Ponadto właściciel bazy danych automatycznie stanie się współwłaścicielem tabeli.

Jeśli utworzysz zewnętrzną tabelę platformy Spark lub SQL z przekazywaniem uwierzytelniania, dane będą zabezpieczone tylko na poziomach folderów i plików. Jeśli ktoś wysyła zapytanie do tego typu tabeli zewnętrznej, tożsamość zabezpieczeń przesyłającego zapytanie jest przekazywana do systemu plików, co powoduje sprawdzenie praw dostępu.

Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień do folderów i plików, zobacz [udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="examples"></a>Przykłady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Tworzenie zarządzanej tabeli obsługiwanej przez Parquet w Spark i wykonywanie zapytań z puli SQL bezserwerowej

W tym scenariuszu istnieje baza danych Spark o nazwie `mytestdb` . Zobacz [Tworzenie bazy danych Spark i nawiązywanie z nią połączenia przy użyciu puli SQL bezserwerowej](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Utwórz zarządzaną tabelę Spark za pomocą SparkSQL, uruchamiając następujące polecenie:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

To polecenie tworzy tabelę `myparquettable` w bazie danych `mytestdb` . Nazwy tabel zostaną przekonwertowane na małe litery. Po krótkim opóźnieniu można zobaczyć tabelę w puli SQL bezserwerowej. Na przykład Uruchom poniższą instrukcję z puli SQL bezserwerowej.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Sprawdź, czy `myparquettable` jest uwzględniony w wynikach.

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
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Teraz można odczytywać dane z puli SQL bezserwerowej w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Należy uzyskać następujący wiersz jako wynik:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Tworzenie tabeli zewnętrznej obsługiwanej przez Parquet w Spark i wykonywanie zapytań z puli SQL bezserwerowej

W tym przykładzie należy utworzyć zewnętrzną tabelę Spark dla plików danych Parquet utworzonych w poprzednim przykładzie dla zarządzanej tabeli.

Na przykład z SparkSQL Run:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Zastąp symbol zastępczy `<fs>` nazwą systemu plików, który jest domyślnym systemem plików obszaru roboczego i symbolem zastępczym `<synapse_ws>` z nazwą obszaru roboczego Synapse, którego używasz do uruchamiania tego przykładu.

Poprzedni przykład tworzy tabelę `myextneralparquettable` w bazie danych `mytestdb` . Po krótkim opóźnieniu można zobaczyć tabelę w puli SQL bezserwerowej. Na przykład Uruchom poniższą instrukcję z puli SQL bezserwerowej.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Sprawdź, czy `myexternalparquettable` jest uwzględniony w wynikach.

Teraz można odczytywać dane z puli SQL bezserwerowej w następujący sposób:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
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


