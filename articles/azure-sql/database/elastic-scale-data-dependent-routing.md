---
title: Routing zależny od danych
description: Jak używać klasy ShardMapManager w aplikacjach .NET do routingu zależnego od danych, funkcja baz danych podzielonej na fragmenty w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 2e6efc08cb7d38a856098395aff363d9d7ec2bab
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442980"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Używanie routingu zależnego od danych do kierowania zapytania do odpowiedniej bazy danych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Routing zależny od danych** umożliwia kierowanie żądania do odpowiedniej bazy danych przy użyciu danych w zapytaniu. Routing zależny od danych jest podstawowym wzorcem podczas pracy z bazami danych podzielonej na fragmenty. Kontekst żądania może również służyć do kierowania żądania, zwłaszcza jeśli klucz fragmentowania nie jest częścią zapytania. Poszczególne zapytania lub transakcje w aplikacji korzystające z routingu zależnego od danych są ograniczone do uzyskiwania dostępu do jednej bazy danych na żądanie. W przypadku narzędzi elastycznych Azure SQL Database ten Routing jest realizowany za pomocą klasy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

Aplikacja nie musi śledzić różnych parametrów połączenia lub lokalizacji bazy danych skojarzonych z różnymi wycinkami danych w środowisku podzielonej na fragmenty. Zamiast tego [Menedżer mapy fragmentu](elastic-scale-shard-map-management.md) otwiera połączenia z prawidłowymi bazami danych, gdy jest to konieczne, na podstawie danych z mapy fragmentu i wartości klucza fragmentowania, który jest celem żądania aplikacji. Klucz jest zwykle *Customer_ID*, *tenant_id*, *date_key*lub inny konkretny identyfikator, który jest podstawowym parametrem żądania bazy danych.

Aby uzyskać więcej informacji, zobacz [skalowanie SQL Server przy użyciu routingu zależnego od danych](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Pobierz bibliotekę kliencką

Do pobrania:

* Wersja języka Java biblioteki, zobacz [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Wersja programu .NET biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Korzystanie z ShardMapManager w aplikacji routingu zależnej od danych

Aplikacje powinny tworzyć wystąpienia **ShardMapManager** podczas inicjowania przy użyciu wywołania fabryki **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). W tym przykładzie jest inicjowana zarówno **ShardMapManager** , jak i określony **ShardMap** . Ten przykład pokazuje metody GetSqlShardMapManager i GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Użyj najniższych poświadczeń uprawnień możliwych do pobrania mapy fragmentu

Jeśli aplikacja nie operuje na mapie fragmentu, poświadczenia używane w metodzie fabryki powinny mieć uprawnienia tylko do odczytu w globalnej bazie danych **map fragmentu** . Te poświadczenia zwykle różnią się od poświadczeń używanych do otwierania połączeń z menedżerem map fragmentu. Zobacz również [poświadczenia używane do uzyskiwania dostępu do biblioteki klienta Elastic Database](elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Wywoływanie metody OpenConnectionForKey

**Metoda ShardMap. OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) zwraca połączenie gotowe do wydawania poleceń do odpowiedniej bazy danych na podstawie wartości parametru **klucza** . Informacje fragmentu są przechowywane w pamięci podręcznej w aplikacji przez **ShardMapManager**, więc te żądania nie obejmują zazwyczaj wyszukiwania bazy danych dla globalnej bazy danych **map fragmentu** .

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* Parametr **klucza** służy jako klucz wyszukiwania do mapy fragmentu w celu określenia odpowiedniej bazy danych dla żądania.
* Parametr **ConnectionString** służy do przekazywania tylko poświadczeń użytkownika dla żądanego połączenia. Nazwa bazy danych lub nazwa serwera nie jest uwzględniona w tym parametrze *ConnectionString* , ponieważ Metoda określa bazę danych i serwer przy użyciu **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) powinien mieć ustawioną wartość **ConnectionOptions. Sprawdź** , czy środowisko, w którym mapy fragmentu mogą ulec zmianie, a wiersze mogą być przenoszone do innych baz danych w wyniku operacji dzielenia lub scalania. Ta walidacja obejmuje krótkie zapytanie do lokalnej mapy fragmentu w docelowej bazie danych (nie do globalnej mapy fragmentu) przed dostarczeniem połączenia do aplikacji.

Jeśli sprawdzanie poprawności mapy fragmentu lokalnego nie powiedzie się (wskazując, że pamięć podręczna jest nieprawidłowa), Menedżer mapy fragmentu kwerenduje mapę Global fragmentu, aby uzyskać nową poprawną wartość dla wyszukiwania, zaktualizować pamięć podręczną oraz uzyskać i zwrócić odpowiednie połączenie z bazą danych.

Użyj **ConnectionOptions. None** tylko wtedy, gdy zmiany mapowania fragmentu nie są oczekiwane, gdy aplikacja jest w trybie online. W takim przypadku wartości pamięci podręcznej można przyjąć, że zawsze są poprawne, i można bezpiecznie pominąć dodatkowe wywołanie walidacji z docelową bazą danych. Zmniejsza to ruch bazy danych. **ConnectionOptions** można również ustawić za pośrednictwem wartości w pliku konfiguracji, aby wskazać, czy fragmentowania zmiany są oczekiwane, czy nie w danym okresie czasu.  

W tym przykładzie użyto wartości parametru **CustomerID**-Integer przy użyciu obiektu **ShardMap** o nazwie **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

Metoda **OpenConnectionForKey** zwraca nowe, już otwarte połączenie z poprawną bazą danych. Połączenia używane w ten sposób nadal korzystają z puli połączeń.

**Metoda OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) jest również dostępna, jeśli aplikacja korzysta z programowania asynchronicznego.

## <a name="integrating-with-transient-fault-handling"></a>Integracja z obsługą błędów przejściowych

Najlepszym rozwiązaniem w zakresie opracowywania aplikacji do uzyskiwania dostępu do danych w chmurze jest upewnienie się, że błędy przejściowe są przechwytywane przez aplikację i że operacje są ponawiane kilka razy przed wygenerowaniem błędu. Obsługa błędów przejściowych dla aplikacji w chmurze została omówiona w przejściowej obsłudze błędów ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Przejściowa obsługa błędów może współistnieć z wzorcem routingu zależnego od danych. Kluczowym wymaganiem jest ponowienie próby wykonania całego żądania dostępu do danych, w tym bloku **using** , który uzyskał połączenie routingu zależnego od danych. Poprzedni przykład można napisać ponownie w następujący sposób.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Przykład — Routing zależny od danych z obsługą błędów przejściowych

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Pakiety niezbędne do wdrożenia przejściowej obsługi błędów są pobierane automatycznie podczas tworzenia przykładowej aplikacji Elastic Database.

## <a name="transactional-consistency"></a>Spójność transakcyjna

Właściwości transakcyjne są gwarantowane dla wszystkich operacji lokalnych dla fragmentu. Na przykład transakcje przesłane za pomocą routingu zależnego od danych są wykonywane w zakresie fragmentu docelowego dla połączenia. W tej chwili nie ma możliwości zarejestrowania wielu połączeń do transakcji i w związku z tym nie ma żadnych transakcyjnych gwarancji dla operacji wykonywanych w ramach fragmentów.

## <a name="next-steps"></a>Następne kroki

Aby odłączyć fragmentu lub ponownie dołączyć fragmentu, zobacz [Używanie klasy recoverymanager do rozwiązywania problemów z mapowaniem fragmentu](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
