---
title: Korzystanie z biblioteki klienta Elastic Database z Dapper
description: Korzystanie z biblioteki klienta Elastic Database z Dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793368"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Korzystanie z biblioteki klienta Elastic Database z Dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten dokument jest przeznaczony dla deweloperów, którzy korzystają z usługi Dapper do kompilowania aplikacji, ale również chcą korzystać z [narzędzi elastycznych baz danych](elastic-scale-introduction.md) w celu tworzenia aplikacji, które implementują fragmentowania w celu skalowania w poziomie warstwy danych.  W tym dokumencie przedstawiono zmiany w aplikacjach opartych na Dapper, które są niezbędne do integracji z narzędziami Elastic Database. Naszym fokusem jest tworzenie Elastic Database fragmentu Management i Routing zależny od danych za pomocą Dapper. 

**Przykładowy kod**: [Narzędzia Elastic Database tools dla integracji Azure SQL Database-Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Integracja programów **Dapper** i **DapperExtensions** z biblioteką klienta Elastic Database dla Azure SQL Database jest łatwa. Aplikacje mogą używać routingu zależnego od danych przez zmianę sposobu tworzenia i otwierania nowych obiektów [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) , aby użyć wywołania [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) z [biblioteki klienta](/previous-versions/azure/dn765902(v=azure.100)). To ogranicza zmiany w aplikacji tylko wtedy, gdy nowe połączenia są tworzone i otwierane. 

## <a name="dapper-overview"></a>Dapper — Omówienie
**Dapper** jest mapowaniem obiektowo-relacyjnym. Mapuje obiekty .NET z aplikacji do relacyjnej bazy danych (i odwrotnie). Pierwsza część przykładowego kodu ilustruje, jak można zintegrować bibliotekę klienta Elastic Database z aplikacjami opartymi na Dapper. Druga część przykładowego kodu ilustruje sposób integracji w przypadku używania zarówno Dapper, jak i DapperExtensions.  

Funkcja mapowania w programie Dapper zapewnia metody rozszerzające dla połączeń z bazą danych, które upraszczają przesyłanie instrukcji języka T-SQL na potrzeby wykonywania lub wykonywania zapytań w bazie danych. Na przykład Dapper ułatwia mapowanie między obiektami .NET i parametrami instrukcji SQL na potrzeby **wykonywania** wywołań lub do korzystania z wyników zapytań SQL do obiektów .NET przy użyciu wywołań **zapytań** z Dapper. 

W przypadku korzystania z DapperExtensions nie trzeba już podawać instrukcji SQL. Metody rozszerzeń, takie jak **GetList** lub **INSERT** over a Connection Database, tworzą instrukcje SQL w tle.

Kolejną zaletą usługi Dapper, a także DapperExtensions jest to, że aplikacja kontroluje tworzenie połączenia z bazą danych. Pomaga to w współpracy z biblioteką klienta Elastic Database, która brokeruje połączenia bazy danych na podstawie mapowania podfragmentów do baz danych.

Aby uzyskać zestawy Dapper, zobacz [Dapper dot net](https://www.nuget.org/packages/Dapper/). Aby zapoznać się z rozszerzeniami Dapper, zobacz [DapperExtensions](https://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Szybki przegląd biblioteki klienta Elastic Database
Za pomocą biblioteki klienta Elastic Database definiuje się partycje danych aplikacji o nazwie *podfragmentów*, mapują je do baz danych i identyfikują je przez *klucze fragmentowania*. Możesz mieć dowolną liczbę baz danych, ile potrzebujesz, i rozpowszechnić podfragmentów w tych bazach danych. Mapowanie wartości klucza fragmentowania do baz danych jest przechowywane przez mapę fragmentu dostarczoną przez interfejsy API biblioteki. Ta funkcja jest nazywana **fragmentu zarządzaniem**. Mapa fragmentu służy również jako Broker połączeń bazy danych dla żądań, które zawierają klucz fragmentowania. Ta funkcja jest określana jako **Routing zależny od danych**.

![Mapy fragmentu i Routing zależny od danych][1]

Menedżer mapy fragmentu chroni użytkowników przed niespójnymi widokami do danych podfragmentu, które mogą wystąpić, gdy w bazach danych są wykonywane współbieżne operacje zarządzania podfragmentu. W tym celu fragmentu Maps brokera połączeń z bazą danych dla aplikacji skompilowanej za pomocą biblioteki programu. Fragmentu operacji zarządzania może mieć wpływ na podfragmentu, dzięki czemu funkcja mapowania fragmentu automatycznie kasuje połączenie z bazą danych. 

Zamiast używać tradycyjnego sposobu tworzenia połączeń dla Dapper, należy użyć [metody OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100)). Dzięki temu wszystkie operacje sprawdzania poprawności są wykonywane, a połączenia są prawidłowo zarządzane, gdy dane przenoszone między fragmentów.

### <a name="requirements-for-dapper-integration"></a>Wymagania dotyczące integracji usługi Dapper
Podczas pracy z biblioteką klienta Elastic Database i interfejsami API Dapper należy zachować następujące właściwości:

* **Skalowanie w poziomie**: chcemy dodawać i usuwać bazy danych z warstwy dane aplikacji podzielonej na fragmenty w zależności od potrzeb aplikacji. 
* **Spójność**: ponieważ aplikacja jest skalowana w poziomie przy użyciu fragmentowania, należy wykonać Routing zależny od danych. Chcemy to zrobić przy użyciu funkcji routingu zależnych od danych w bibliotece. W szczególności chcesz zachować gwarancje weryfikacji i spójności podane przez połączenia, które są obsługiwane przez brokera w Menedżerze mapy fragmentu, aby uniknąć uszkodzenia lub nieprawidłowych wyników zapytania. Zapewnia to, że połączenia z danym podfragmentu są odrzucane lub zatrzymywane, jeśli (na przykład) podfragmentu jest aktualnie przenoszony do innego fragmentu za pomocą interfejsów API z podziałem/scalaniem.
* **Mapowanie obiektu**: chcemy zachować wygodę mapowań dostarczonych przez Dapper do translacji między klasami w aplikacji i strukturami źródłowej bazy danych. 

W poniższej sekcji znajdują się wskazówki dotyczące tych wymagań dla aplikacji opartych na **Dapper** i **DapperExtensions**.

## <a name="technical-guidance"></a>Wskazówki techniczne
### <a name="data-dependent-routing-with-dapper"></a>Routing zależny od danych za pomocą Dapper
W programie Dapper aplikacja jest zazwyczaj odpowiedzialna za tworzenie i otwieranie połączeń z podstawową bazą danych. Po wpisaniu typu T przez aplikację Dapper zwraca wyniki zapytania jako kolekcje .NET typu T. Dapper wykonuje mapowanie z wierszy wyniku T-SQL do obiektów typu T. Podobnie Dapper mapuje obiekty .NET na wartości SQL lub parametry na potrzeby instrukcji języka operowania danymi (DML). Dapper oferuje tę funkcję za pomocą metod rozszerzających dla zwykłego obiektu [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) z BIBLIOTEK klienta ADO .NET SQL. Połączenie SQL zwrócone przez interfejsy API skalowania elastycznego dla DDR jest również zwykłymi obiektami [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) . Dzięki temu możemy bezpośrednio używać rozszerzeń Dapper na podstawie typu zwracanego przez interfejs API DDR biblioteki klienta, ponieważ jest to również proste połączenie z klientem SQL.

Te spostrzeżenia sprawiają, że bezpośrednie użycie połączeń obsługiwanych przez brokera w bibliotece klienta Elastic Database dla Dapper.

Ten przykład kodu (z dołączonego przykładu) ilustruje podejście, w którym klucz fragmentowania jest dostarczany przez aplikację do biblioteki w celu brokera połączenia z odpowiednim fragmentu.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

Wywołanie interfejsu API [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) zastępuje domyślne tworzenie i otwieranie połączenia klienta SQL. Wywołanie [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) przyjmuje argumenty, które są wymagane dla routingu zależnego od danych: 

* Mapa fragmentu do uzyskiwania dostępu do interfejsów routingu zależnych od danych
* Klucz fragmentowania do identyfikowania podfragmentu
* Poświadczenia (nazwa użytkownika i hasło) do nawiązania połączenia z usługą fragmentu

Obiekt mapy fragmentu tworzy połączenie z fragmentu, które przechowuje podfragmentu dla danego klucza fragmentowania. Interfejsy API klienta Elastic Database również oznakowano połączenie, aby zaimplementować jego gwarancje spójności. Ponieważ wywołanie funkcji [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) zwraca zwykły obiekt połączenia klienta SQL, kolejne wywołanie metody **Execute** Extension from Dapper następuje zgodnie z standardowym zwyczajem Dapper.

Zapytania działają bardzo w ten sam sposób — należy najpierw otworzyć połączenie przy użyciu [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) z interfejsu API klienta. Następnie użyj zwykłych metod rozszerzenia Dapper, aby zamapować wyniki zapytania SQL na obiekty .NET:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Należy zauważyć, że blok **użycie** z zakresem połączenia DDR zawiera wszystkie operacje bazy danych w bloku do jednego fragmentu, gdzie tenantId1 jest przechowywany. Zapytanie zwraca tylko blogi przechowywane w bieżącym fragmentu, ale nie te, które są przechowywane na żadnym innym fragmentów. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Routing zależny od danych z Dapper i DapperExtensions
Dapper jest ekosystemem dodatkowych rozszerzeń, które mogą zapewnić dalszą wygodę i abstrakcję bazy danych podczas tworzenia aplikacji baz danych. Przykładem jest DapperExtensions. 

Korzystanie z DapperExtensions w aplikacji nie zmienia sposobu tworzenia i zarządzania połączeniami z bazą danych. Nadal jest odpowiedzialna aplikacja na otwieranie połączeń, a normalne obiekty połączenia klienta SQL są oczekiwane przez metody rozszerzenia. Firma Microsoft może polegać na [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) , jak opisano powyżej. Jak pokazano na poniższym kodzie, jedyną zmianą jest to, że nie trzeba już pisać instrukcji T-SQL:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

A Oto przykładowy kod dla zapytania: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Obsługa błędów przejściowych
Zespół ds. rozwiązań firmy & Microsoft opublikował [blok aplikacji do obsługi błędów przejściowych](/previous-versions/msp-n-p/hh680934(v=pandp.50)) , aby pomóc deweloperom w rozwiązaniu typowych warunków błędów w chmurze. Aby uzyskać więcej informacji, zobacz [Perseverance, Secret of all Triumphss: Using the przejściowe Application obsługa błędów](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

Przykładowy kod opiera się na tymczasowej bibliotece błędów, aby chronić przed błędami przejściowymi. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponownych prób wynoszącą 10 i 5 sekund czasu oczekiwania między ponownymi próbami. Jeśli używasz transakcji, upewnij się, że zakres ponownych prób wraca do początku transakcji w przypadku błędu przejściowego.

## <a name="limitations"></a>Ograniczenia
Podejścia opisane w niniejszym dokumencie wiążą się z kilkoma ograniczeniami:

* Przykładowy kod dla tego dokumentu nie pokazuje, jak zarządzać schematem w programie fragmentów.
* Na żądanie przyjmujemy, że wszystkie jego przetwarzanie bazy danych jest zawarte w obrębie jednego fragmentuu identyfikowanego przez klucz fragmentowania dostarczony przez żądanie. Jednak to założenie nie zawsze jest przechowywane, na przykład gdy nie jest możliwe udostępnienie klucza fragmentowania. Aby rozwiązać ten konieczność, Biblioteka klienta Elastic Database zawiera [klasę MultiShardQuery](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception). Klasa implementuje abstrakcję połączenia w celu wykonywania zapytań na kilku fragmentów. Używanie MultiShardQuery w połączeniu z Dapper wykracza poza zakres tego dokumentu.

## <a name="conclusion"></a>Podsumowanie
Aplikacje korzystające z programów Dapper i DapperExtensions mogą łatwo korzystać z narzędzi elastycznych baz danych dla Azure SQL Database. W ramach kroków opisanych w tym dokumencie te aplikacje mogą korzystać z możliwości narzędzia dla routingu zależnego od danych przez zmianę sposobu tworzenia i otwierania nowych obiektów [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) w celu użycia wywołania [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) biblioteki klienta Elastic Database. Pozwala to ograniczyć zmiany aplikacji wymagane do tych miejsc, w których nowe połączenia są tworzone i otwierane. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png