---
title: Aplikacje z wieloma dzierżawcami z zabezpieczeniami na poziomie wiersza i elastycznych baz danych
description: Korzystaj z narzędzi elastycznych baz danych z zabezpieczeniami na poziomie wierszy, aby kompilować aplikacje z wysoce skalowalną warstwą danych.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793215"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplikacje z wieloma dzierżawcami z narzędziami Elastic Database i zabezpieczeniami na poziomie wierszy
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Narzędzia Elastic Database](elastic-scale-get-started.md) i [zabezpieczenia na poziomie wiersza][rls] współpracują, aby umożliwić skalowanie warstwy danych aplikacji wielodostępnej przy użyciu Azure SQL Database. Wspólnie te technologie pomagają tworzyć aplikacje, które mają wysoce skalowalną warstwę danych. Warstwa danych obsługuje wielodostępne fragmentów i używa **ADO.NET SqlClient** lub **Entity Framework** . Aby uzyskać więcej informacji, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS z Azure SQL Database](./saas-tenancy-app-design-patterns.md).

- **Narzędzia Elastic Database** umożliwiają deweloperom skalowanie warstwy danych przy użyciu standardowych praktyk fragmentowania przy użyciu bibliotek .NET i szablonów usług platformy Azure. Zarządzanie fragmentów za pomocą [biblioteki klienta Elastic Database][s-d-elastic-database-client-library] pomaga zautomatyzować i usprawnić wiele zadań infrastruktury zwykle skojarzonych z fragmentowania.
- **Zabezpieczenia na poziomie wiersza** umożliwiają deweloperom bezpieczne przechowywanie danych dla wielu dzierżawców w tej samej bazie danych. Zasady zabezpieczeń na poziomie wiersza filtrują wiersze, które nie należą do dzierżawy wykonującej zapytanie. Scentralizowanie logiki filtru wewnątrz bazy danych upraszcza konserwację i zmniejsza ryzyko wystąpienia błędu zabezpieczeń. Alternatywą jest użycie całego kodu klienta w celu wymuszenia zabezpieczeń.

Korzystając z tych funkcji, aplikacja może przechowywać dane dla wielu dzierżawców w tej samej bazie danych fragmentu. Koszt IT jest krótszy dla dzierżawy, gdy dzierżawcy współużytkują bazę danych. Jednak ta sama aplikacja może również oferować dzierżawy w warstwie Premium opcję płacenia za własne dedykowane fragmentu pojedynczej dzierżawy. Jedną z korzyści związanych z izolacją pojedynczej dzierżawy są gwarancje wydajności firmy. W bazie danych z jedną dzierżawą nie ma żadnej innej konkurencyjnej dzierżawy zasobów.

Celem jest użycie interfejsów API [routingu opartego na danych](elastic-scale-data-dependent-routing.md) biblioteki klienta Elastic Database do automatycznego łączenia poszczególnych dzierżawców z poprawną bazą danych fragmentu. Tylko jeden fragmentu zawiera konkretną wartość TenantId dla danej dzierżawy. TenantId jest *kluczem fragmentowania* . Po nawiązaniu połączenia zasady zabezpieczeń na poziomie wiersza w ramach bazy danych zapewniają, że dana dzierżawa będzie mogła uzyskiwać dostęp tylko do tych wierszy danych, które zawierają TenantId.

> [!NOTE]
> Identyfikator dzierżawy może składać się z więcej niż jednej kolumny. W przypadku wygody tej dyskusji nie zakładamy, że TenantId pojedynczej kolumny.

![Architektura aplikacji do tworzenia blogów][1]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

### <a name="prerequisites"></a>Wymagania wstępne

- Korzystanie z programu Visual Studio (2012 lub nowszego)
- Tworzenie trzech baz danych w Azure SQL Database
- Pobierz przykładowy projekt: [Narzędzia elastycznych baz danych dla platformy Azure SQL — fragmentów z wieloma dzierżawcami](https://go.microsoft.com/?linkid=9888163)
  - Wprowadź informacje dotyczące baz danych na początku **program.cs**

Ten projekt rozszerza sposób opisany w [narzędziu Elastic DB Tools for Azure SQL — integracja Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) przez dodanie obsługi wielodostępnych baz danych fragmentu. Projekt tworzy prostą aplikację konsolową do tworzenia blogów i wpisów. Projekt zawiera cztery dzierżawy oraz dwie wielodostępne bazy danych fragmentu. Ta konfiguracja jest zilustrowana na powyższym diagramie.

Skompiluj i uruchom aplikację. Spowoduje to uruchomienie Bootstrap Menedżera mapy fragmentu narzędzi elastycznych baz danych, a następnie wykonuje następujące testy:

1. Za pomocą Entity Framework i LINQ Utwórz nowy blog, a następnie Wyświetl wszystkie blogi dla każdej dzierżawy
2. Korzystanie z usługi ADO.NET SqlClient, wyświetlanie wszystkich blogów dla dzierżawy
3. Spróbuj wstawić blog dla niewłaściwej dzierżawy, aby sprawdzić, czy wystąpił błąd

Zwróć uwagę, że ponieważ zabezpieczenia na poziomie wiersza nie zostały jeszcze włączone w bazach danych fragmentu, każdy z tych testów ujawnia problem: dzierżawy mogą wyświetlać Blogi, które nie należą do nich, a aplikacja nie będzie mogła wstawić blogu dla niewłaściwej dzierżawy. W pozostałej części tego artykułu opisano, jak rozwiązać te problemy, wymuszając izolację dzierżawy z poziomu zabezpieczenia na poziomie wiersza. Istnieją dwa kroki:

1. **Warstwa aplikacji** : Zmodyfikuj kod aplikacji w taki sposób, aby zawsze ustawił bieżącą TenantId w \_ kontekście sesji po otwarciu połączenia. Przykładowy projekt już ustawia TenantId w ten sposób.
2. **Warstwa danych** : Utwórz zasady zabezpieczeń na poziomie wiersza w każdej bazie danych fragmentu w celu filtrowania wierszy na podstawie TenantId przechowywanych w \_ kontekście sesji. Utwórz zasady dla każdej bazy danych fragmentu, w przeciwnym razie wiersze w wielodostępnym fragmentów nie są filtrowane.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Warstwa aplikacji: Ustaw TenantId w kontekście sesji \_

Najpierw Nawiąż połączenie z bazą danych fragmentu przy użyciu interfejsów API routingu zależnego od danych w bibliotece klienta Elastic Database. Aplikacja nadal musi poinformować bazę danych, która TenantId korzysta z tego połączenia. TenantId informuje zasady zabezpieczeń na poziomie wiersza, które wiersze muszą być odfiltrowane jako należące do innych dzierżawców. Zapisz bieżący TenantId w [ \_ kontekście sesji](/sql/t-sql/functions/session-context-transact-sql) połączenia.

Alternatywą dla \_ kontekstu sesji jest użycie [ \_ informacji kontekstowych](/sql/t-sql/functions/context-info-transact-sql). Ale \_ kontekst sesji jest lepszym rozwiązaniem. \_Kontekst sesji jest łatwiejszy w użyciu, domyślnie zwraca wartość null i obsługuje pary klucz-wartość.

### <a name="entity-framework"></a>Entity Framework

W przypadku aplikacji używających Entity Framework najprostszym podejściem jest ustawienie \_ kontekstu sesji w ramach przesłonięcia ElasticScaleContext opisanego w obszarze [Routing zależny od danych za pomocą narzędzia EF DbContext](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Utwórz i wykonaj SqlCommand, które ustawia TenantId w kontekście sesji \_ dla shardingKey określonego dla połączenia. Następnie należy zwrócić brokera połączeń za pomocą routingu zależnego od danych. W ten sposób wystarczy napisać kod tylko raz, aby ustawić \_ kontekst sesji.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Teraz kontekst sesji \_ jest ustawiany automatycznie przy użyciu określonego TenantId za każdym razem, gdy ElasticScaleContext jest wywoływana:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

W przypadku aplikacji korzystających z ADO.NET SqlClient Utwórz funkcję otoki wokół metody ShardMap. OpenConnectionForKey. Przed zwróceniem połączenia otoka automatycznie ustawia TenantId w \_ kontekście sesji na bieżącą TenantId. Aby zapewnić, że \_ kontekst sesji zawsze jest ustawiony, należy otworzyć tylko połączenia za pomocą tej funkcji otoki.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Warstwa danych: Tworzenie zasad zabezpieczeń na poziomie wiersza

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Utwórz zasady zabezpieczeń, aby odfiltrować wiersze, do których każdy dzierżawca ma dostęp

Teraz, gdy aplikacja ustawia kontekst sesji \_ z bieżącym TenantId przed wykonaniem zapytania, zasady zabezpieczeń na poziomie wiersza mogą filtrować zapytania i wykluczać wiersze, które mają różne TenantId.

Zabezpieczenia na poziomie wiersza są implementowane w języku Transact-SQL. Zdefiniowana przez użytkownika funkcja definiuje logikę dostępu, a zasady zabezpieczeń wiążą tę funkcję z dowolną liczbą tabel. Dla tego projektu:

1. Funkcja sprawdza, czy aplikacja jest połączona z bazą danych i czy TenantId przechowywanych w kontekście sesji jest \_ zgodna z TenantIdem danego wiersza.
    - Aplikacja jest połączona, a nie z innym użytkownikiem programu SQL Server.

2. Predykat filtru pozwala na przekazywanie wierszy, które spełniają filtr TenantId w przypadku zapytań SELECT, UPDATE i DELETE.
    - Predykat bloku zapobiega wstawianiu lub aktualizowaniu wierszy, które nie kończą filtrowania.
    - Jeśli \_ nie ustawiono kontekstu sesji, funkcja zwraca wartość null, a wiersze nie są widoczne lub nie można ich wstawiać.

Aby włączyć zabezpieczenia na poziomie wiersza na wszystkich fragmentów, wykonaj następujące polecenie T-SQL, używając programu Visual Studio (SSDT), narzędzia SSMS lub skryptu programu PowerShell zawartego w projekcie. Lub jeśli używasz [zadań Elastic Database](./elastic-jobs-overview.md), Możesz zautomatyzować wykonywanie tego T-SQL na wszystkich fragmentów.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> W złożonym projekcie może być konieczne dodanie predykatu do setek tabel, które mogą być żmudnym. Występuje procedura składowana pomocnika, która automatycznie generuje zasady zabezpieczeń i dodaje predykat dla wszystkich tabel w schemacie. Aby uzyskać więcej informacji, zapoznaj się z wpisem w blogu na stronie [zastosuj Row-Level zabezpieczenia do wszystkich tabel — skrypt pomocnika](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Teraz po ponownym uruchomieniu przykładowej aplikacji dzierżawy będą widzieć tylko wiersze należące do nich. Ponadto aplikacja nie może wstawiać wierszy należących do dzierżawców innych niż aktualnie połączone z bazą danych fragmentu. Ponadto aplikacja nie może zaktualizować TenantId w żadnym z wierszy, które może zobaczyć. Jeśli aplikacja podejmie próbę wykonania tej czynności, zostanie zgłoszony wyjątek dbupdateexception.

Jeśli dodasz nową tabelę później, Zmień zasady zabezpieczeń, aby dodać predykaty filtru i bloku w nowej tabeli.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Dodawanie ograniczeń domyślnych w celu automatycznego wypełniania TenantId dla operacji wstawiania

Można ustawić ograniczenie domyślne dla każdej tabeli, aby automatycznie wypełnić TenantId wartością bieżącą przechowywaną w \_ kontekście sesji podczas wstawiania wierszy. Poniżej przedstawiono przykład.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Teraz aplikacja nie musi określać TenantId podczas wstawiania wierszy:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Jeśli używasz ograniczeń domyślnych dla projektu Entity Framework, zaleca się, aby *nie* uwzględnić kolumny TenantId w modelu danych EF. To zalecenie wynika z faktu, że Entity Framework zapytania automatycznie dostarczają wartości domyślne, które zastępują ograniczenia domyślne utworzone w języku T-SQL, który używa \_ kontekstu sesji.
> Aby użyć ograniczeń domyślnych w projekcie przykładowym, na przykład należy usunąć TenantId z DataClasses.cs (i uruchomić Add-Migration w konsoli Menedżera pakietów) i użyć T-SQL, aby upewnić się, że pole istnieje tylko w tabelach bazy danych. W ten sposób EF automatycznie poda nieprawidłowe wartości domyślne podczas wstawiania danych.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Obowiązkowe Włącz dostęp do *administratora* wszystkich wierszy

Niektóre aplikacje mogą chcieć utworzyć *administratora* , który może uzyskiwać dostęp do wszystkich wierszy. Administratora może włączyć raportowanie dla wszystkich dzierżawców we wszystkich fragmentówach. Lub administratora może wykonywać operacje dzielenia i scalania na fragmentów, które obejmują przeniesienie wierszy dzierżawy między bazami danych.

Aby włączyć administratora, Utwórz nowego użytkownika SQL ( `superuser` w tym przykładzie) w każdej bazie danych fragmentu. Następnie Zmień zasady zabezpieczeń za pomocą nowej funkcji predykatu, która umożliwia temu użytkownikowi dostęp do wszystkich wierszy. Ta funkcja jest przyznany dalej.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Konserwacja

- **Dodawanie nowego fragmentów** : Wykonaj skrypt T-SQL, aby włączyć zabezpieczenia na poziomie wiersza na dowolnym nowym fragmentów, w przeciwnym razie zapytania dotyczące tych fragmentów nie są filtrowane.
- **Dodawanie nowych tabel** : Dodaj predykat Filter i Block do zasad zabezpieczeń na wszystkich fragmentów za każdym razem, gdy zostanie utworzona nowa tabela. W przeciwnym razie zapytania w nowej tabeli nie są filtrowane. To dodanie może być zautomatyzowane przy użyciu wyzwalacza DDL, zgodnie z opisem w artykule [zastosuj Row-Level zabezpieczenia automatycznie do nowo utworzonych tabel (blog)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393).

## <a name="summary"></a>Podsumowanie

Narzędzia Elastic Database i zabezpieczenia na poziomie wiersza mogą być używane razem w celu skalowania warstwy danych aplikacji z obsługą zarówno wielodostępnych, jak i fragmentów jednej dzierżawy. Fragmentów z wieloma dzierżawami mogą służyć do wydajniejszego przechowywania danych. Ta wydajność jest wymawiana, gdy duża liczba dzierżawców ma tylko kilka wierszy danych. Fragmentów z jedną dzierżawą mogą obsługiwać dzierżawy w warstwie Premium, które mają bardziej rygorystyczne wymagania dotyczące wydajności i izolacji. Aby uzyskać więcej informacji, zobacz [Informacje o zabezpieczeniach na poziomie wiersza][rls].

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Co to jest pula elastyczna Azure?](elastic-pool-overview.md)
- [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](elastic-scale-introduction.md)
- [Wzorce projektowe dla wielodostępnych aplikacji SaaS wykorzystujących usługę Azure SQL Database](./saas-tenancy-app-design-patterns.md)
- [Uwierzytelnianie w aplikacjach wielodostępnych za pomocą usługi Azure AD i OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Aplikacja Tailspin Surveys](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Pytania i żądania funkcji

Aby uzyskać odpowiedzi na pytania, skontaktuj się z nami na [stronie pytania firmy&Microsoft dotyczącym SQL Database](/answers/topics/azure-sql-database.html). I Dodaj dowolne żądania funkcji do [forum opinii SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md