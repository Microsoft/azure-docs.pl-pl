---
title: Korzystanie z zestawu SDK HBase .NET — Azure HDInsight
description: Użyj zestawu SDK platformy .NET HBase do tworzenia i usuwania tabel oraz do odczytu i zapisu danych.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 70c1dcc133f01cf72392f444192d977440c55cf4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866628"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Korzystanie z zestawu .NET SDK dla Apache HBase

[Apache HBase](apache-hbase-overview.md) oferuje dwie podstawowe opcje pracy z danymi: [zapytania Apache Hive i wywołania interfejsu API RESTful HBase](apache-hbase-tutorial-get-started-linux.md). Możesz współpracować bezpośrednio z interfejsem API REST przy użyciu `curl` polecenia lub podobnego narzędzia.

W przypadku aplikacji C# i .NET [Biblioteka klienta REST programu Microsoft HBase dla platformy .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) udostępnia bibliotekę kliencką na INTERFEJSIE API REST HBase.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Zestaw SDK HBase dla platformy .NET jest dostarczany jako pakiet NuGet, który można zainstalować z **konsoli Menedżera pakietów NuGet** programu Visual Studio za pomocą następującego polecenia:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Tworzenie wystąpienia nowego obiektu HBaseClient

Aby użyć zestawu SDK, Utwórz wystąpienie nowego `HBaseClient` obiektu, przekazując je `ClusterCredentials` `Uri` do klastra i nazwę użytkownika i hasło usługi Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Zastąp wartość CLUSTERname nazwą klastra usługi HDInsight HBase oraz nazwę użytkownika i hasło przy użyciu poświadczeń Apache Hadoop określonych podczas tworzenia klastra. Domyślną nazwą użytkownika usługi Hadoop jest **administrator**.

## <a name="create-a-new-table"></a>Tworzenie nowej tabeli

HBase przechowuje dane w tabelach. Tabela składa się z *Rowkey*, klucza podstawowego i co najmniej jednej grupy kolumn o nazwie *rodziny kolumn*. Dane w każdej tabeli są dystrybuowane w poziomie przez Rowkey zakres do *regionów*. Każdy region ma klucz początkowy i końcowy. Tabela może mieć jeden lub więcej regionów. Gdy dane w tabeli rośnie, HBase dzieli duże regiony na mniejsze regiony. Regiony są przechowywane na *serwerach regionów*, w których jeden z serwerów regionów może przechowywać wiele regionów.

Dane są fizycznie przechowywane w *HFiles*. Pojedynczy HFile zawiera dane dla jednej tabeli, jednego regionu i jednej rodziny kolumn. Wiersze w HFile są przechowywane posortowane na Rowkey. Każdy HFile ma indeks *drzewa B +* w celu przyspieszenia pobierania wierszy.

Aby utworzyć nową tabelę, określ `TableSchema` kolumny i. Poniższy kod sprawdza, czy tabela "RestSDKTable" już istnieje — Jeśli nie, tabela jest tworzona.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Ta nowa tabela zawiera rodziny dwóch kolumn, T1 i T2. Ponieważ rodziny kolumn są przechowywane oddzielnie w różnych HFilesach, warto mieć osobną rodzinę kolumn dla często używanych zapytań. W poniższym przykładzie [Wstaw dane](#insert-data) kolumny są dodawane do rodziny kolumn T1.

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Wstawianie danych

Aby wstawić dane, należy określić unikatowy klucz wiersza jako identyfikator wiersza. Wszystkie dane są przechowywane w `byte[]` tablicy. Poniższy kod definiuje i dodaje `title` `director` kolumny,, i `release_date` do rodziny kolumn T1, jak te kolumny są najczęściej używane. `description`Kolumny i `tagline` są dodawane do rodziny kolumn T2. W razie konieczności można podzielić dane na rodziny kolumn.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementuje [chmurę BigTable](https://cloud.google.com/bigtable/), więc format danych wygląda jak na poniższej ilustracji:

:::image type="content" source="./media/apache-hbase-rest-sdk/hdinsight-table-roles.png" alt-text="Przykładowe dane wyjściowe z Apache HBase" border="true":::

## <a name="select-data"></a>Wybieranie danych

Aby odczytać dane z tabeli HBase, przekaż nazwę tabeli i klucz wiersza do `GetCellsAsync` metody w celu zwrócenia `CellSet` .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

W tym przypadku kod zwraca tylko pierwszy pasujący wiersz, ponieważ dla unikatowego klucza powinien istnieć tylko jeden wiersz. Zwrócona wartość jest zmieniana na `string` format z `byte[]` tablicy. Możesz również przekonwertować wartość na inne typy, na przykład liczbę całkowitą dla daty wydania filmu:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Skanuj nad wierszami

HBase używa `scan` do pobierania co najmniej jednego wiersza. Ten przykład żąda wielu wierszy w partiach 10 i pobiera dane, których wartości klucza należą do zakresu od 25 do 35. Po pobraniu wszystkich wierszy Usuń skaner, aby wyczyścić zasoby.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z przykładem bazy danych Apache HBase w usłudze HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Tworzenie kompleksowej aplikacji z funkcją [analizowania tonacji w czasie rzeczywistym przy użyciu platformy Apache HBase](./apache-hbase-tutorial-get-started-linux.md)