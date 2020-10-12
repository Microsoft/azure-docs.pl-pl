---
title: Phoenix Query Server zestaw SDK REST — Azure HDInsight
description: Zainstaluj i użyj zestawu SDK REST dla Phoenix Query Server w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/01/2020
ms.openlocfilehash: 051d7b37f5f78ce28134fa7c4ee188f3dde81812
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504846"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Zestaw SDK REST serwera Apache Phoenix Query

[Apache Phoenix](https://phoenix.apache.org/) to "open source", wysoce równoległa warstwa relacyjnej bazy danych na platformie [Apache HBase](apache-hbase-overview.md). Phoenix umożliwia używanie zapytań przypominających SQL z HBase za pomocą narzędzi SSH, takich jak [SqlLine](apache-hbase-query-with-phoenix.md). Phoenix udostępnia również serwer HTTP o nazwie Phoenix Query Server (PQS), zubożonego klienta obsługującego dwa mechanizmy transportu do komunikacji z klientem: bufory JSON i protokoły. Bufory protokołu są mechanizmem domyślnym i oferują wydajniejszą komunikację niż kod JSON.

W tym artykule opisano, jak używać zestawu SDK REST PQS do tworzenia tabel, upsert wierszy osobno i zbiorczych, a następnie wybierania danych przy użyciu instrukcji SQL. W przykładach użyto [sterownika Microsoft .NET dla Apache Phoenix serwera zapytań](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Ten zestaw SDK jest oparty na interfejsach API [Avatica platformy Apache calcite](https://calcite.apache.org/avatica/) , które wyłącznie używają buforów protokołu dla formatu serializacji.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [buforów protokołu Apache calcite Avatica](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Sterownik Microsoft .NET dla programu Apache Phoenix Query Server jest dostarczany jako pakiet NuGet, który można zainstalować z **konsoli Menedżera pakietów NuGet** programu Visual Studio za pomocą następującego polecenia:

```console
Install-Package Microsoft.Phoenix.Client
```

## <a name="instantiate-new-phoenixclient-object"></a>Tworzenie wystąpienia nowego obiektu PhoenixClient

Aby rozpocząć korzystanie z biblioteki, Utwórz wystąpienie nowego `PhoenixClient` obiektu, przekazując element `ClusterCredentials` `Uri` do klastra i nazwę użytkownika i hasło klastra Apache Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Zastąp wartość CLUSTERname nazwą klastra usługi HDInsight HBase oraz nazwę użytkownika i hasło przy użyciu poświadczeń usługi Hadoop określonych podczas tworzenia klastra. Domyślną nazwą użytkownika usługi Hadoop jest **administrator**.

## <a name="generate-unique-connection-identifier"></a>Generuj unikatowy identyfikator połączenia

Aby wysłać co najmniej jedno żądanie do PQS, należy dołączyć unikatowy identyfikator połączenia w celu skojarzenia żądań z połączeniem.

```csharp
string connId = Guid.NewGuid().ToString();
```

Każdy przykład najpierw wywołuje `OpenConnectionRequestAsync` metodę, przekazując unikatowy identyfikator połączenia. Następnie zdefiniuj `ConnectionProperties` i `RequestOptions` przekazanie tych obiektów oraz wygenerowanego identyfikatora połączenia do `ConnectionSyncRequestAsync` metody. Obiekt PQS gwarantuje `ConnectionSyncRequest` , że zarówno klient, jak i serwer mają spójny widok Właściwości bazy danych.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest i jego ConnectionProperties

Aby wywołać metodę  `ConnectionSyncRequestAsync` , Przekaż `ConnectionProperties` obiekt.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Oto pewne właściwości:

| Właściwość | Opis |
| -- | -- |
| Automatycznego zatwierdzania | Wartość logiczna określająca, czy `autoCommit` włączono obsługę transakcji w Phoenix. |
| ReadOnly | Wartość logiczna określająca, czy połączenie jest tylko do odczytu. |
| TransactionIsolation | Liczba całkowita oznaczająca poziom izolacji transakcji na specyfikację JDBC — Zobacz poniższą tabelę.|
| Wykaz | Nazwa katalogu do użycia podczas pobierania właściwości połączenia. |
| Schemat | Nazwa schematu do użycia podczas pobierania właściwości połączenia. |
| IsDirty | Wartość logiczna określająca, czy właściwości zostały zmienione. |

Oto `TransactionIsolation` wartości:

| Wartość izolacji | Opis |
| -- | -- |
| 0 | Transakcje nie są obsługiwane. |
| 1 | Mogą wystąpić zanieczyszczone odczyty, odczyty niepowtarzalne i odczyty fantomów. |
| 2 | Zanieczyszczone odczyty są blokowane, ale mogą wystąpić niepowtarzalne operacje odczytu i fantomy. |
| 4 | Nieprzeczytane odczyty i niepowtarzające się odczyty są blokowane, ale mogą wystąpić operacje odczytu fantomu. |
| 8 | Wszystkie zanieczyszczone odczyty, odczyty niepowtarzalne i odczyty fantomu są całkowicie uniemożliwione. |

## <a name="create-a-new-table"></a>Utwórz nową tabelę

HBase, podobnie jak wszystkie inne RDBMS, przechowuje dane w tabelach. Phoenix używa standardowych zapytań SQL do tworzenia nowych tabel podczas definiowania typów klucza podstawowego i kolumny.

Ten przykład i wszystkie późniejsze przykłady, użyj obiektu z utworzonymi wystąpieniami, `PhoenixClient` tak jak zdefiniowano [wystąpienie nowego obiektu PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Poprzedni przykład tworzy nową tabelę o nazwie `Customers` przy użyciu `IF NOT EXISTS` opcji. `CreateStatementRequestAsync`Wywołanie tworzy nową instrukcję na serwerze Avitica (PQS). `finally`Blok zamyka zwrócone `CreateStatementResponse` i `OpenConnectionResponse` obiekty.

## <a name="insert-data-individually"></a>Wstaw dane pojedynczo

Ten przykład pokazuje pojedyncze Wstawianie danych, odwołujące się do `List<string>` kolekcji skrótów stanu i regionu:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

`StateProvince`Wartość kolumny tabeli zostanie użyta w późniejszej operacji SELECT.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Struktura wykonywania instrukcji INSERT jest podobna do tworzenia nowej tabeli. Na końcu `try` bloku transakcja zostanie jawnie zatwierdzona. W tym przykładzie powtarzamy operację wstawiania 300 razy. Poniższy przykład przedstawia bardziej wydajny proces wstawiania wsadowego.

## <a name="batch-insert-data"></a>Wstaw dane wsadowe

Poniższy kod jest niemal identyczny z kodem do wstawiania pojedynczych danych. W tym przykładzie używa `UpdateBatch` obiektu w wywołaniu `ExecuteBatchRequestAsync` , zamiast wielokrotnie wywoływanie `ExecuteRequestAsync` z przygotowaną instrukcją.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

W jednym środowisku testowym, indywidualnie wstawiając 300 nowe rekordy trwało prawie 2 minuty. Natomiast Wstawianie 300 rekordów jako partii wymaga tylko 6 sekund.

## <a name="select-data"></a>Wybieranie danych

Ten przykład pokazuje, jak ponownie użyć jednego połączenia w celu wykonywania wielu zapytań:

1. Zaznacz opcję wszystkie rekordy, a następnie Pobierz pozostałe rekordy, gdy zostanie zwrócona wartość domyślna wynosząca 100.
2. Użyj instrukcji SELECT całkowitej liczby wierszy, aby pobrać pojedynczy wynik skalarny.
3. Wykonaj instrukcję SELECT zwracającą łączną liczbę klientów na stan lub terytorium.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

`select`Wynik instrukcji powinien być następujący:

```output
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Następne kroki

* [Oprogramowanie Apache Phoenix w usłudze HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Korzystanie z zestawu Apache HBase REST SDK](apache-hbase-rest-sdk.md)
