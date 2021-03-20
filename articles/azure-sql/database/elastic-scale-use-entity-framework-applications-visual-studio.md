---
title: Korzystanie z biblioteki klienta Elastic Database z Entity Framework
description: Używanie Elastic Database biblioteki klienta i Entity Framework do kodowania baz danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793402"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Elastic Database bibliotekę kliencką z Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym dokumencie przedstawiono zmiany w aplikacji Entity Framework, które są konieczne do integracji z [narzędziami Elastic Database](elastic-scale-introduction.md). Fokus jest oparty na tworzeniu [mapy fragmentu](elastic-scale-shard-map-management.md) i [routingu zależnego od danych](elastic-scale-data-dependent-routing.md) z podejściem **Code First** Entity Framework. Samouczek [Code First-New Database](/ef/ef6/modeling/code-first/workflows/new-database) for EF służy jako przykładowy kod w tym dokumencie. Przykładowy kod towarzyszący temu dokumentowi jest częścią zestawu przykładów narzędzi elastycznych baz danych w przykładach Visual Studio Code.

## <a name="downloading-and-running-the-sample-code"></a>Pobieranie i uruchamianie przykładowego kodu

Aby pobrać kod dla tego artykułu:

* Wymagany jest program Visual Studio 2012 lub nowszy.
* Pobierz [przykład integracji narzędzi elastycznych baz danych dla platformy Azure SQL — Entity Framework](https://github.com/Azure/elastic-db-tools/). Rozpakuj próbkę na wybraną lokalizację.
* Uruchom program Visual Studio.
* W programie Visual Studio wybierz pozycję plik — > Otwórz projekt/rozwiązanie.
* W oknie dialogowym **Otwórz projekt** przejdź do pobranego przykładu i wybierz **EntityFrameworkCodeFirst. sln** , aby otworzyć przykład.

Aby uruchomić przykład, należy utworzyć trzy puste bazy danych w Azure SQL Database:

* Baza danych Menedżera map fragmentu
* Baza danych fragmentu 1
* Baza danych fragmentu 2

Po utworzeniu tych baz danych Wypełnij pola posiadacze miejsc w programie w **programie. cs** nazwą serwera, nazwami baz danych i poświadczeniami, aby połączyć się z bazami danych. Kompiluj rozwiązanie w programie Visual Studio. Program Visual Studio Pobiera wymagane pakiety NuGet dla biblioteki klienta Elastic Database, Entity Framework i przejściowej obsługi błędów w ramach procesu kompilacji. Upewnij się, że dla Twojego rozwiązania włączono obsługę przywracania pakietów NuGet. To ustawienie można włączyć, klikając prawym przyciskiem myszy plik rozwiązania w programie Visual Studio Eksplorator rozwiązań.

## <a name="entity-framework-workflows"></a>Przepływy pracy Entity Framework

Entity Framework deweloperzy korzystają z jednego z następujących czterech przepływów pracy do kompilowania aplikacji i zapewnienia trwałości obiektów aplikacji:

* **Code First (Nowa baza danych)**: programista EF tworzy model w kodzie aplikacji, a następnie EF generuje z niego bazę danych.
* **Code First (istniejąca baza danych)**: deweloperzy mogą generować kod aplikacji dla modelu z istniejącej bazy danych.
* **Model First**: deweloper tworzy model w programie Dr Designer, a następnie tworzy bazę danych z modelu.
* **Database First**: programista korzysta z narzędzi EF do wywnioskowania modelu z istniejącej bazy danych.

Wszystkie te podejścia polegają na klasie DbContext, aby w sposób przezroczysty zarządzać połączeniami bazy danych i schematem bazy danych aplikacji. Różne konstruktory klasy podstawowej DbContext umożliwiają różne poziomy kontroli nad tworzeniem połączeń, uruchamianiem bazy danych i tworzeniem schematu. Wyzwania wynikają głównie z faktu, że zarządzanie połączeniami z bazą danych zapewniane przez EF zawiera funkcje zarządzania połączeniami zależnych od danych interfejsów routingu, udostępnianych przez bibliotekę klienta Elastic Database.

## <a name="elastic-database-tools-assumptions"></a>Założeń narzędzi elastycznych baz danych

Aby zapoznać się z definicjami warunków, zobacz [słownik narzędzi Elastic Database](elastic-scale-glossary.md).

Za pomocą biblioteki klienta Elastic Database można definiować partycje danych aplikacji o nazwie podfragmentów. Podfragmentów są identyfikowane przez klucz fragmentowania i są mapowane do określonych baz danych. Aplikacja może mieć dowolną liczbę baz danych w miarę potrzeb i rozpowszechnić podfragmentów w celu zapewnienia wystarczającej pojemności lub wydajności z uwzględnieniem bieżących wymagań firmy. Mapowanie wartości klucza fragmentowania do baz danych jest przechowywane przez mapę fragmentu dostarczoną przez interfejsy API klienta Elastic Database. Ta funkcja jest nazywana **fragmentu Management** lub SMM for Short. Mapa fragmentu służy również jako Broker połączeń bazy danych dla żądań, które zawierają klucz fragmentowania. Ta funkcja jest określana jako **Routing zależny od danych**.

Menedżer mapy fragmentu chroni użytkowników przed niespójnymi widokami do danych podfragmentu, które mogą wystąpić podczas współbieżnych operacji zarządzania podfragmentu (takich jak Zmienianie lokalizacji danych z jednego fragmentu na inny). W tym celu mapy fragmentu zarządzane przez brokera biblioteki klienta są połączeniami bazy danych dla aplikacji. Dzięki temu funkcja mapowania fragmentu automatycznie kasuje połączenie z bazą danych, gdy operacje zarządzania fragmentu mogą mieć wpływ na podfragmentu, dla którego utworzono połączenie. Ta metoda musi być zintegrowana z niektórymi funkcjami EF, takimi jak tworzenie nowych połączeń z istniejących i Sprawdzanie istnienia bazy danych. Ogólnie rzecz biorąc, nasze obserwacje miały na celu niezawodne działanie tylko w przypadku zamkniętych połączeń z bazą danych, które mogą być bezpiecznie sklonowane dla działania EF. Zasada projektowania Elastic Database zamiast tego jest tylko dla brokera otwartych połączeń. Może to oznaczać, że zamknięcie brokera połączeń przez bibliotekę kliencką przed przekazaniem jej do trybu EF DbContext może rozwiązać ten problem. Jednak przez zamknięcie połączenia i poleganie na tym, że plik EF jest ponownie otwierany, jeden z nich jest sprawdzany i sprawdza spójności wykonywane przez bibliotekę. Funkcja migracji w programie EF używa jednak tych połączeń do zarządzania podstawowym schematem bazy danych w sposób przezroczysty dla aplikacji. W idealnym przypadku należy zachować i połączyć wszystkie te funkcje zarówno z biblioteki klienta Elastic Database, jak i EF w tej samej aplikacji. W poniższej sekcji omówiono te właściwości i wymagania bardziej szczegółowo.

## <a name="requirements"></a>Wymagania

Podczas pracy z biblioteką klienta Elastic Database i Entity Framework interfejsów API, chcesz zachować następujące właściwości:

* **Skalowanie w poziomie**: w celu dodania lub usunięcia baz danych z warstwy dane aplikacji podzielonej na fragmenty w zależności od potrzeb dotyczących pojemności aplikacji. Oznacza to kontrolę nad tworzeniem i usuwaniem baz danych oraz przy użyciu interfejsów API Menedżera mapy fragmentu Elastic Database do zarządzania bazami danych i mapowania podfragmentów.
* **Spójność**: aplikacja wykorzystuje fragmentowania i korzysta z możliwości routingu zależnego od danych w bibliotece klienta. Aby uniknąć uszkodzenia lub nieprawidłowych wyników zapytania, połączenia są obsługiwane przez Menedżera mapy fragmentu. Zachowuje także sprawdzanie poprawności i spójność.
* **Code First**: aby zachować wygodę od pierwszej odmiany kodu EF. W Code First klasy w aplikacji są mapowane w sposób przezroczysty do podstawowych struktur baz danych. Kod aplikacji współdziała z zestawami dbsets, które maskuje większość aspektów związanych z przetwarzaniem bazowej bazy danych.
* **Schemat**: Entity Framework obsługuje tworzenie początkowego schematu bazy danych i kolejne ewolucje schematu przez migracje. Dzięki zachowaniu tych możliwości dostosowanie aplikacji jest łatwe w miarę rozwoju danych.

Poniższe wskazówki przedstawiają, jak spełnić te wymagania dotyczące Code First aplikacji za pomocą narzędzi elastycznych baz danych.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Routing zależny od danych przy użyciu EF DbContext

Połączenia bazy danych z Entity Framework są zazwyczaj zarządzane za pomocą podklas **DbContext**. Utwórz te podklasy, wyprowadzając z **DbContext**. Jest to miejsce, w którym należy zdefiniować **zestawy** dbimplementujące kolekcje obiektów CLR utworzonych przez bazę danych dla aplikacji. W kontekście routingu zależnego od danych można zidentyfikować kilka przydatnych właściwości, które nie muszą być przechowywane w przypadku innych scenariuszy aplikacji kodu EF:

* Baza danych już istnieje i została zarejestrowana na mapie fragmentu Elastic Database.
* Schemat aplikacji został już wdrożony w bazie danych (wyjaśniono poniżej).
* Połączenia routingu zależnego od danych do bazy danych są obsługiwane przez mapę fragmentu.

Aby zintegrować **DbContext** z routingiem zależnym od danych dla skalowania w poziomie:

1. Tworzenie połączeń fizycznych baz danych za pomocą interfejsów klienta Elastic Database Menedżera map fragmentu.
2. Zawijaj połączenie z podklasą **DbContext**
3. Przekaż połączenie na klasy podstawowe **DbContext** , aby upewnić się, że wszystkie operacje przetwarzania po stronie EF są również wykonywane.

Poniższy przykład kodu ilustruje to podejście. (Ten kod jest również w towarzyszącym projekcie programu Visual Studio)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Główne punkty

* Nowy Konstruktor zastępuje konstruktora domyślnego w podklasie DbContext
* Nowy Konstruktor przyjmuje argumenty, które są wymagane dla routingu zależnego od danych za pomocą biblioteki klienta Elastic Database:
  
  * Mapa fragmentu do uzyskiwania dostępu do interfejsów routingu zależnych od danych,
  * klucz fragmentowania, aby zidentyfikować podfragmentu,
  * parametry połączenia z poświadczeniami dla połączenia routingu zależnego od danych do fragmentu.
* Wywołanie konstruktora klasy bazowej pobiera metodę statyczną, która wykonuje wszystkie kroki niezbędne do routingu zależnego od danych.
  
  * Używa wywołania OpenConnectionForKey interfejsów klienta Elastic Database na mapie fragmentu w celu nawiązania otwartego połączenia.
  * Mapa fragmentu tworzy połączenie otwarte z fragmentu, które przechowuje podfragmentu dla danego klucza fragmentowania.
  * To otwarte połączenie jest przesyłane z powrotem do konstruktora klasy podstawowej DbContext, aby wskazać, że to połączenie ma być używane przez EF zamiast zezwalać na automatyczne tworzenie nowego połączenia. W ten sposób połączenie zostało oznaczone przez interfejs API klienta Elastic Database, aby zapewnić spójność w ramach operacji zarządzania mapami fragmentu.

Użyj nowego konstruktora dla podklasy DbContext zamiast domyślnego konstruktora w kodzie. Oto przykład:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

Nowy Konstruktor otwiera połączenie z fragmentu, które przechowuje dane dla podfragmentu identyfikowane przez wartość **tenantid1**. Kod w bloku **using** pozostaje niezmieniony w celu uzyskania dostępu do **nieogólnymi** na blogach przy użyciu EF w fragmentu dla **tenantid1**. Powoduje to zmianę semantyki kodu w bloku using, tak że wszystkie operacje bazy danych są teraz ograniczone do jednego fragmentu, gdzie **tenantid1** jest przechowywany. Na przykład zapytanie LINQ dotyczące blogów **nieogólnymi** zwróci tylko blogi przechowywane w bieżącym fragmentu, ale nie są przechowywane w innych fragmentów.  

### <a name="transient-faults-handling"></a>Obsługa błędów przejściowych

Zespół ds. & wzorców firmy Microsoft opublikował [blok aplikacji do obsługi błędów przejściowych](/previous-versions/msp-n-p/dn440719(v=pandp.60)). Biblioteka jest używana z biblioteką klienta elastycznego skalowania w połączeniu z EF. Należy jednak upewnić się, że każdy wyjątek przejściowy wraca do miejsca, w którym można upewnić się, że nowy Konstruktor jest używany po wystąpieniu błędu przejściowego, aby dowolna nowa próba połączenia była podejmowana przy użyciu dostosowanych konstruktorów. W przeciwnym razie połączenie z poprawnym fragmentuem nie jest gwarantowane i nie ma żadnych gwarancji, że połączenie jest zachowywane, gdy wystąpią zmiany w mapie fragmentu.

Poniższy przykład kodu ilustruje, w jaki sposób można używać zasad ponawiania prób SQL wokół nowych konstruktorów podklasy **DbContext** :

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** w powyższym kodzie jest zdefiniowany jako **SqlDatabaseTransientErrorDetectionStrategy** z liczbą ponownych prób wynoszącą 10 i 5 sekund czasu oczekiwania między ponownymi próbami. Takie podejście jest podobne do wskazówek dotyczących transakcji EF i zainicjowanych przez użytkownika (zobacz [ograniczenia z ponownymi próbami wykonywania (Ef6)](/ef/ef6/fundamentals/connection-resiliency/retry-logic). Obie sytuacje wymagają, aby program aplikacji kontroluje zakres, do którego zwracany jest wyjątek przejściowy: Aby ponownie otworzyć transakcję lub (jak pokazano) ponownie utworzyć kontekst z właściwego konstruktora, który używa biblioteki klienta Elastic Database.

Konieczność kontrolowania, gdzie wyjątki przejściowe przechodzą z powrotem w zakres, wyklucza również użycie wbudowanej **SqlAzureExecutionStrategy** , która jest dostarczana z EF. **SqlAzureExecutionStrategy** może ponownie otworzyć połączenie, ale nie używać **OpenConnectionForKey** i w związku z tym ominąć wszystkie sprawdzanie poprawności wykonywane w ramach wywołania **OpenConnectionForKey** . Zamiast tego, przykładowy kod używa wbudowanego **DefaultExecutionStrategy** , które również zawiera Dr. W przeciwieństwie do **SqlAzureExecutionStrategy** działa prawidłowo w połączeniu z zasadami ponawiania z obsługi błędów przejściowych. Zasady wykonywania są ustawiane w klasie **ElasticScaleDbConfiguration** . Należy pamiętać, że nie zaleca się używania **DefaultSqlExecutionStrategy** , ponieważ sugeruje on użycie **SqlAzureExecutionStrategy** , jeśli wystąpią wyjątki przejściowe, co może prowadzić do niewłaściwego zachowania w opisany sposób. Aby uzyskać więcej informacji na temat różnych zasad ponawiania i EF, zobacz [odporność połączeń w EF](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Ponowne zapisywanie konstruktora

Przykłady kodu pokazują, że domyślny Konstruktor ponownie zapisuje wymagane dla aplikacji, aby można było używać routingu zależnego od danych z Entity Framework. Poniższa tabela służy do uogólnienia tego podejścia do innych konstruktorów.

| Bieżący Konstruktor | Ponownie napisano konstruktora dla danych | Konstruktor podstawowy | Uwagi |
| --- | --- | --- | --- |
| Element WebContext () |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |Połączenie musi być funkcją mapy fragmentu i kluczem routingu zależnym od danych. Należy przekazać automatyczne tworzenie połączeń przez EF, a zamiast tego użyć mapy fragmentu do brokera połączenia. |
| Element Context (String) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |Połączenie jest funkcją mapy fragmentu oraz klucza routingu zależnego od danych. Stała Nazwa bazy danych lub parametry połączenia nie działają zgodnie z oczekiwaniami przez mapę fragmentu. |
| Element WebContext (DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Połączenie zostanie utworzone dla danej mapy fragmentu i klucza fragmentowania z udostępnionym modelem. Skompilowany model jest przenoszona do podstawowego c'tor. |
| Element Context (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Połączenie musi zostać wywnioskowane z mapy fragmentu i klucza. Nie można go podać jako danych wejściowych (chyba że dane wejściowe już używają mapy fragmentu i klucza). Wartość logiczna jest przenoszona. |
| Element Context (String, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Połączenie musi zostać wywnioskowane z mapy fragmentu i klucza. Nie można go podać jako danych wejściowych (chyba że dane wejściowe używają mapy fragmentu i klucza). Skompilowany model jest przenoszona. |
| Element WebContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Nowy Konstruktor musi upewnić się, że wszystkie połączenia w obiekcie ObjectContext przekazane jako dane wejściowe są przekierowywane do połączenia zarządzanego przez elastyczne skalowanie. Szczegółowa dyskusja o obiektach ObjectContext wykracza poza zakres tego dokumentu. |
| Element Context (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |Połączenie musi zostać wywnioskowane z mapy fragmentu i klucza. Nie można podać połączenia jako danych wejściowych (chyba że dane wejściowe już używają mapy fragmentu i klucza). Model i wartość logiczna są przesyłane do konstruktora klasy bazowej. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Fragmentu wdrażanie schematu przy użyciu migracji EF

Automatyczne zarządzanie schematami to wygoda udostępniona przez Entity Framework. W kontekście aplikacji korzystających z narzędzi elastycznych baz danych chcesz zachować tę możliwość, aby automatycznie inicjować schemat dla nowo utworzonych fragmentów podczas dodawania baz danych do aplikacji podzielonej na fragmenty. Podstawowym przypadkiem użycia jest zwiększenie pojemności warstwy danych dla aplikacji podzielonej na fragmenty przy użyciu EF. Poleganie na możliwościach zarządzania schematami przy użyciu programu EF zmniejsza nakład pracy związany z administracją bazy danych za pomocą aplikacji podzielonej na fragmenty opartej na EF.

Wdrażanie schematu przy użyciu migracji EF działa najlepiej w przypadku **nieotwartych połączeń**. Jest to w przeciwieństwie do scenariusza dotyczącego routingu zależnego od danych, który jest oparty na otwartym połączeniu udostępnianym przez interfejs API klienta Elastic Database. Kolejną różnicą jest wymóg spójności: przy pożądanym zapewnieniu spójności wszystkich połączeń routingu zależnych od danych, które są chronione przed współbieżnością fragmentu mapy, nie jest to problem z początkowym wdrożeniem schematu do nowej bazy danych, która nie została jeszcze zarejestrowana na mapie fragmentu i nie została jeszcze przypisana do blokady podfragmentów. W związku z tym można polegać na zwykłych połączeniach z bazą danych w tym scenariuszu, w przeciwieństwie do routingu zależnego od danych.  

Prowadzi to do podejścia polegającego na tym, że wdrażanie schematu za pomocą migracji EF jest ściśle powiązane z rejestracją nowej bazy danych jako fragmentu w mapie fragmentu aplikacji. Jest to zależne od następujących wymagań wstępnych:

* Baza danych została już utworzona.
* Baza danych jest pusta — nie zawiera schematu użytkownika i nie ma żadnych danych użytkownika.
* Nie można uzyskać dostępu do bazy danych za pomocą interfejsów API klienta Elastic Database dla routingu zależnego od danych.

Po spełnieniu tych wymagań wstępnych można utworzyć zwykłe, nieotwarte polecenie **SqlConnection** , aby uruchomić migracje EF do wdrożenia schematu. Poniższy przykład kodu ilustruje to podejście.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Ten przykład pokazuje metodę **RegisterNewShard** , która rejestruje fragmentu na mapie fragmentu, wdraża schemat za pomocą migracji EF i przechowuje mapowanie klucza fragmentowania do fragmentu. Opiera się on na konstruktorze podklasy **DbContext** (**ElasticScaleContext** w przykładzie), który pobiera parametry połączenia SQL jako dane wejściowe. Kod tego konstruktora jest przesunięty do przodu, jak pokazano na poniższym przykładzie:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

Jedna z nich mogła korzystać z wersji konstruktora dziedziczonej z klasy bazowej. Jednak kod musi mieć pewność, że domyślny inicjator dla EF jest używany podczas nawiązywania połączenia. W związku z tym Krótki przewodnik po metodzie statycznej przed wywołaniem do konstruktora klasy bazowej z parametrami połączenia. Należy zauważyć, że rejestracja fragmentów powinna być uruchamiana w innej domenie lub procesie aplikacji, aby upewnić się, że ustawienia inicjatora EF nie powodują konfliktu.

## <a name="limitations"></a>Ograniczenia

Podejścia opisane w niniejszym dokumencie wiążą się z kilkoma ograniczeniami:

* Aplikacje EF korzystające z **LocalDB** najpierw muszą migrować do regularnej bazy danych SQL Server przed użyciem biblioteki klienta Elastic Database. Skalowanie aplikacji za pomocą fragmentowania z elastycznym skalowaniem nie jest możliwe z **LocalDB**. Należy pamiętać, że programowanie może nadal korzystać z **LocalDB**.
* Wszelkie zmiany w aplikacji, które wymagają zmian schematu bazy danych, muszą przekroczyć migracje EF na wszystkich fragmentów. Przykładowy kod dla tego dokumentu nie pokazuje, jak to zrobić. Rozważ użycie Update-Database z parametrem ConnectionString do iteracji we wszystkich fragmentów; lub Wyodrębnij skrypt T-SQL dla oczekującej migracji przy użyciu Update-Database z opcją-Script i Zastosuj skrypt T-SQL do fragmentów.  
* Zgodnie z żądaniem przyjmuje się, że wszystkie jego przetwarzanie bazy danych jest zawarte w obrębie jednego fragmentuu, identyfikowanego przez klucz fragmentowania dostarczony przez żądanie. Jednak to założenie nie zawsze ma wartość true. Na przykład, gdy nie jest możliwe udostępnienie klucza fragmentowania. Aby rozwiązać ten konieczność, Biblioteka klienta udostępnia klasę **MultiShardQuery** , która implementuje abstrakcję połączenia w celu wykonywania zapytań na kilku fragmentówach. Uczenie się korzystania z **MultiShardQuery** w połączeniu z EF wykracza poza zakres tego dokumentu

## <a name="conclusion"></a>Podsumowanie

Zgodnie z krokami przedstawionymi w tym dokumencie, aplikacje EF mogą korzystać z możliwości biblioteki klienta Elastic Database dla routingu zależnego od danych przez konstruktory refaktoryzacji dla podklas **DbContext** używanych w aplikacji EF. Ogranicza to zmiany wymagane do tych miejsc, w których już istnieją klasy **DbContext** . Ponadto aplikacje EF mogą nadal korzystać z automatycznego wdrażania schematu przez połączenie kroków, które wywołują wymagane migracje EF z rejestracją nowych fragmentów i mapowań na mapie fragmentu.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png