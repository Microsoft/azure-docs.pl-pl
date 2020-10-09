---
title: 'Samouczek: Tworzenie aplikacji sieci Web (w pamięci podręcznej) — pamięć podręczna platformy Azure dla Redis'
description: Dowiedz się, jak utworzyć aplikację internetową za pomocą usługi Azure cache for Redis, która używa wzorca z możliwością buforowania.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 71f1e2b50daf333e19bc11bce119f37cec28d146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209204"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Samouczek: tworzenie rankingu z odkładaniem do pamięci podręcznej na platformie ASP.NET

W tym samouczku zaktualizujesz aplikację internetową platformy ASP.NET o nazwie *ContosoTeamStats*, utworzoną w ramach [przewodnika Szybki start platformy ASP.NET dla usługi Azure Cache for Redis](cache-web-app-howto.md), w taki sposób, aby zawierała ranking korzystający ze [wzorca odkładania do pamięci podręcznej](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) i usługi Azure Cache for Redis. Przykładowa aplikacja wyświetla listę statystyk zespołu z bazy danych i demonstruje różne sposoby korzystania z usługi Azure Cache for Redis do przechowywania danych w pamięci podręcznej i pobierania ich w celu podniesienia wydajności. Wykonanie instrukcji z samouczka umożliwi uzyskanie działającej aplikacji internetowej wykonującej odczyt i zapis w bazie danych, zoptymalizowanej przy użyciu usługi Azure Cache for Redis i hostowanej na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zwiększ przepływność danych i ogranicz obciążenie bazy danych, przechowując i pobierając dane przy użyciu usługi Azure Cache for Redis.
> * Użyj sortowanego zestawu Redis do pobrania pierwszych pięciu zespołów.
> * Aprowizuj zasoby platformy Azure dla aplikacji przy użyciu szablonu usługi Resource Manager.
> * Opublikuj aplikację na platformie Azure przy użyciu programu Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są:

* Ten samouczek jest kontynuacją [przewodnika Szybki start platformy ASP.NET dla usługi Azure Cache for Redis](cache-web-app-howto.md). Jeśli jeszcze nie został on przez Ciebie ukończony, ukończ go najpierw.
* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    * Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    * Programowanie na platformie Azure
    * Programowanie aplikacji klasycznych dla platformy .NET z użyciem programu SQL Server Express LocalDB lub [SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express).

## <a name="add-a-leaderboard-to-the-project"></a>Dodawanie rankingu do projektu

W tej części samouczka dla projektu *ContosoTeamStats* zostanie skonfigurowany ranking przedstawiający statystyki zwycięstw, przegranych i remisów fikcyjnych drużyn.

### <a name="add-the-entity-framework-to-the-project"></a>Dodawanie platformy Entity Framework do projektu

1. W programie Visual Studio otwórz rozwiązanie *ContosoTeamStats* utworzone w [przewodniku Szybki start platformy ASP.NET dla usługi Azure Cache for Redis](cache-web-app-howto.md).
2. Kliknij pozycje **Narzędzia > Menedżer pakietów NuGet > Konsola menedżera pakietów**.
3. Uruchom następujące polecenie w oknie **Konsola menedżera pakietów**, aby zainstalować platformę EntityFramework:

    ```powershell
    Install-Package EntityFramework
    ```

Aby uzyskać więcej informacji dotyczących tego pakietu, zobacz stronę pakietu NuGet programu [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Dodawanie modelu zespołu

1. Kliknij prawym przyciskiem myszy pozycję **Modele** w **Eksploratorze rozwiązań**, a następnie wybierz kolejno pozycje **Dodaj**, **Klasa**.

1. Wprowadź `Team` dla nazwy klasy i kliknij przycisk **Dodaj**.

    ![Dodawanie klasy modelu](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Zastąp instrukcje `using` na początku pliku *Team.cs* poniższymi instrukcjami `using`:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Zastąp definicję klasy `Team` poniższym fragmentem kodu, który zawiera zaktualizowaną definicję klasy `Team` oraz niektóre inne klasy pomocnika platformy Entity Framework. W tym samouczku stosowane jest podejście Code First z platformą Entity Framework. Takie podejście umożliwia platformie Entity Framework utworzenie bazy danych z poziomu kodu. Więcej informacji na temat podejścia typu Code First na platformie Entity Framework używanego w tym samouczku znajduje się w artykule [Code first to a new database](/ef/ef6/modeling/code-first/workflows/new-database) (Rozwiązanie Code First dla nowej bazy danych).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Web.config**, aby go otworzyć.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Dodaj następującą sekcję `connectionStrings` wewnątrz sekcji `configuration`. Nazwa parametrów połączenia musi być zgodna z nazwą klasy kontekstu bazy danych platformy Entity Framework, którą jest `TeamContext`.

    W tych parametrach połączenia przyjęto założenie, że spełniono [wymagania wstępne](#prerequisites) i zainstalowano SQL Server Express LocalDB, które jest częścią obciążenia programowania dla programu *.NET Desktop* zainstalowanego z programem Visual Studio 2019.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    W poniższym przykładzie pokazano nową sekcję `connectionStrings` znajdującą się po sekcji `configSections` wewnątrz sekcji `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Dodawanie elementu TeamsController i widoków

1. Skompiluj projekt w programie Visual Studio. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy folder **Kontrolery** i wybierz kolejno pozycje **Dodaj**, **Kontroler**.

1. Wybierz pozycję **Kontroler MVC 5 z widokami używający narzędzia Entity Framework** i kliknij przycisk **Dodaj**. Jeśli po kliknięciu przycisku **Dodaj** występuje błąd, upewnij się, czy został wcześniej utworzony projekt.

    ![Dodawanie klasy kontrolera](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Wybierz pozycję **Zespół (ContosoTeamStats.Models)** z listy rozwijanej **Klasa modelu**. Wybierz pozycję **TeamContext (ContosoTeamStats.Models)** z listy rozwijanej **Klasa kontekstu danych**. Wpisz nazwę `TeamsController` w polu tekstowym **Kontroler** (jeśli nie została automatycznie wypełniona). Kliknij przycisk **Dodaj**, aby utworzyć klasę kontrolera i dodać widoki domyślne.

    ![Konfigurowanie kontrolera](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. W **Eksploratorze rozwiązań** rozwiń folder **Global.asax** i kliknij dwukrotnie plik **Global.asax.cs**, aby go otworzyć.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Dodaj dwie poniższe instrukcje `using` na początku pliku pod innymi instrukcjami `using`:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Dodaj poniższy wiersz kodu na końcu metody `Application_Start`:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. W **Eksploratorze rozwiązań** rozwiń folder `App_Start` i kliknij dwukrotnie pozycję `RouteConfig.cs`.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. W metodzie `RegisterRoutes` zastąp element `controller = "Home"` w trasie `Default` elementem `controller = "Teams"`, jak pokazano w poniższym przykładzie:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Konfigurowanie widoku układu

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, a następnie folder **Współużytkowane**, a następnie kliknij dwukrotnie pozycję **_Layout.cshtml**. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Zmień zawartość elementu `title` i zastąp ciąg `My ASP.NET Application` ciągiem `Contoso Team Stats`, jak pokazano w poniższym przykładzie:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. W sekcji `body` dodaj następującą nową instrukcję `Html.ActionLink` dla projektu *Contoso Team Stats* zaraz pod linkiem dla elementu *Azure Cache for Redis Test*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Zmiany kodu](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Naciśnij klawisze **Ctrl+F5**, aby utworzyć i uruchomić aplikację. Ta wersja aplikacji odczytuje wyniki bezpośrednio z bazy danych. Zwróć uwagę na akcje **Utwórz nowe**, **Edytuj**, **Szczegóły** oraz **Usuń**, które zostały automatycznie dodane do aplikacji przez szkielet **Kontroler MVC 5 z widokami używający narzędzia Entity Framework**. W następnej sekcji samouczka dodasz pamięć podręczną Azure Cache for Redis w celu optymalizacji dostępu do danych i zapewnienia dodatkowych funkcji w aplikacji.

    ![Aplikacja startowa](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Konfigurowanie aplikacji dla usługi Azure Cache for Redis

W tej sekcji samouczka skonfigurujesz przykładową aplikację do przechowywania i pobierania statystyk zespołu Contoso z wystąpienia usługi Azure Cache for Redis za pomocą klienta pamięci podręcznej [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Dodawanie połączenia pamięci podręcznej do kontrolera zespołów

Pakiet biblioteki klienta *StackExchange.Redis* został już zainstalowany w ramach przewodnika Szybki start. Oprócz tego skonfigurowano ustawienia aplikacji *CacheConnection* tak, aby była używana lokalnie i z opublikowaną usługą App Service. Użyj tej samej biblioteki klienta i informacji *CacheConnection* w elemencie *TeamsController*.

1. W **Eksploratorze rozwiązań** rozwiń folder **Kontrolery** i kliknij dwukrotnie plik **TeamsController.cs**, aby go otworzyć.

    ![Kontroler zespołów](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Dodaj dwie poniższe instrukcje `using` do pliku **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Dodaj dwie poniższe właściwości do klasy `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Aktualizacja elementu TeamsController do odczytywania informacji z pamięci podręcznej lub bazy danych

W tym przykładzie statystyki zespołu można pobrać z bazy danych lub z pamięci podręcznej. Statystyki zespołu są przechowywane w pamięci podręcznej jako zserializowany obiekt `List<Team>`, a także jako posortowany zestaw korzystający z typów danych Redis. Podczas pobierania elementów z zestawu posortowanego możesz pobrać część z nich, wszystkie lub przesłać zapytanie o określone elementy. W tym przykładzie prześlesz do zestawu posortowanego zapytanie dotyczące najlepszych 5 zespołów uporządkowanych według liczby zwycięstw.

Do korzystania z usługi Azure Cache for Redis nie jest wymagane przechowywanie statystyk zespołu w wielu formatach w pamięci podręcznej. W tym samouczku używa się wielu formatów, aby przedstawić różne sposoby i różne typy danych, których można użyć do buforowania danych.

1. Dodaj poniższe instrukcje `using` na początku pliku `TeamsController.cs` razem z innymi instrukcjami `using`:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Zastąp bieżącą implementację metody `public ActionResult Index()` następującą implementacją:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Dodaj następujące trzy metody do klasy `TeamsController`, aby wdrożyć typy akcji `playGames`, `clearCache` i `rebuildDB` z instrukcji switch dodanej w poprzednim fragmencie kodu.

    Metoda `PlayGames` aktualizuje statystyki zespołu, symulując sezon gier, zapisuje wyniki w bazie danych i czyści obecnie nieaktualne dane z pamięci podręcznej.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Metoda `RebuildDB` ponownie inicjuje bazę danych z domyślnym zestawem zespołów, generuje ich statystyki i czyści obecnie nieaktualne dane z pamięci podręcznej.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Metoda `ClearCachedTeams` usuwa wszystkie buforowane statystyki zespołów z pamięci podręcznej.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Dodaj poniższe cztery metody do klasy `TeamsController`, aby wdrożyć różne sposoby pobierania statystyk zespołu z pamięci podręcznej i bazy danych. Każda z tych metod zwraca obiekt `List<Team>`, który jest następnie wyświetlany w widoku.

    Metoda `GetFromDB` odczytuje statystyki zespołu z bazy danych.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    Metoda `GetFromList` odczytuje statystyki zespołu z pamięci podręcznej jako zserializowany obiekt `List<Team>`. Jeśli statystyki nie będą obecne w pamięci podręcznej, wystąpi chybienie w pamięci podręcznej. W przypadku chybienia w pamięci podręcznej statystyki zespołu są odczytywane z bazy danych, a następnie zapisywane w pamięci podręcznej do użytku przy następnym żądaniu. W tym przykładzie używana jest serializacja JSON.NET do serializacji obiektów platformy .NET podczas zapisu w pamięci podręcznej i odczytu z niej. Aby uzyskać więcej informacji, zobacz [Praca z obiektami .NET w usłudze Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    Metoda `GetFromSortedSet` odczytuje statystyki zespołu z buforowanego zestawu posortowanego. W przypadku braku trafienia pamięci podręcznej, statystyki zespołu są odczytywane z bazy danych i przechowywane w pamięci podręcznej jako zestaw posortowany.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    Metoda `GetFromSortedSetTop5` odczytuje pierwsze pięć zespołów z buforowanego zestawu posortowanego. Rozpoczyna od sprawdzenia pamięci podręcznej pod kątem istnienia klucza `teamsSortedSet`. Jeśli klucz ten nie jest obecny, wywołana zostaje metoda `GetFromSortedSet` w celu odczytu statystyk zespołu i zapisania ich w pamięci podręcznej. Następnie zostaje przesłane zapytanie do buforowanego zestawu posortowanego, które zwraca pięć najlepszych zespołów.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Aktualizacja metod Utwórz, Edytuj i Usuń w celu pracy z pamięcią podręczną

Kod tworzenia szkieletu, który został wygenerowany w ramach tego przykładu, zawiera metody dodawania, edytowania i usuwania zespołów. Za każdym razem, gdy zespół jest dodawany, edytowany lub usuwany, dane w pamięci podręcznej stają się nieaktualne. W tej sekcji zmodyfikujesz te trzy metody w celu czyszczenia buforowanych zespołów w taki sposób, aby pamięć podręczna była odświeżana.

1. Przejdź do metody `Create(Team team)` w klasie `TeamsController`. Dodaj wywołanie metody `ClearCachedTeams`, jak pokazano w poniższym przykładzie:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Przejdź do metody `Edit(Team team)` w klasie `TeamsController`. Dodaj wywołanie metody `ClearCachedTeams`, jak pokazano w poniższym przykładzie:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Przejdź do metody `DeleteConfirmed(int id)` w klasie `TeamsController`. Dodaj wywołanie metody `ClearCachedTeams`, jak pokazano w poniższym przykładzie:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Dodawanie metod buforowania do widoku indeksu zespołów

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, a następnie folder **Zespoły** i kliknij dwukrotnie plik **Index.cshtml**.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. W początkowej części pliku odszukaj następujący element akapitu:

    ![Tabela akcji](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Ten link tworzy nowy zespół. Zastąp element akapitu poniższą tabelą. Ta tabela zawiera linki akcji do tworzenia nowego zespołu, odtwarzania nowego sezonu gier, czyszczenia pamięci podręcznej, pobierania zespołów z pamięci podręcznej w wielu formatach, pobierania zespołów z bazy danych i odbudowy bazy danych przy użyciu nowych przykładowych danych.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Przewiń w dół pliku **index. cshtml** i Dodaj następujący element, tak aby `tr` był ostatnim wierszem w ostatniej tabeli w pliku:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    W tym wierszu wyświetlana jest wartość `ViewBag.Msg` zawierająca raport o stanie dotyczący bieżącej operacji. Wartość `ViewBag.Msg` zostaje ustawiona po kliknięciu jednego z linków akcji z poprzedniego kroku.

    ![Komunikat o stanie](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Naciśnij klawisz **F6**, aby utworzyć projekt.

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie na swojej maszynie, aby sprawdzić funkcje dodane w celu obsługi zespołów.

Podczas tego testu aplikacja i baza danych działają lokalnie. Usługa Azure Cache for Redis jest jednak hostowana zdalnie na platformie Azure. To powoduje, że pamięć podręczna będzie prawdopodobnie mieć wydajność nieznacznie niższą niż baza danych. Aby uzyskać najlepszą wydajność, aplikacja klienta i wystąpienie usługi Azure Cache for Redis powinny być w tej samej lokalizacji. W następnej części wdrożysz wszystkie zasoby na platformie Azure, aby zobaczyć, jak pamięć podręczna podnosi wydajność.

Aby uruchomić aplikację lokalnie:

1. Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację.

    ![Aplikacja uruchomiona lokalnie](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Przetestuj wszystkie nowe metody dodane do widoku. Ponieważ w tych testach pamięć podręczna jest zdalna, wydajność bazy danych powinna nieco przewyższać wydajność pamięci podręcznej.

## <a name="publish-and-run-in-azure"></a>Publikowanie i uruchamianie na platformie Azure

### <a name="provision-a-database-for-the-app"></a>Inicjowanie obsługi administracyjnej bazy danych aplikacji

W tej sekcji zostanie zainicjowana Nowa baza danych w SQL Database, aby aplikacja mogła korzystać z usługi na platformie Azure.

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Utwórz zasób** w prawym lewym rogu.

1. Na stronie **Nowy** kliknij pozycje **Bazy danych** > **SQL Database**.

1. Dla nowej bazy danych SQL Database użyj następujących ustawień:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa bazy danych** | *ContosoTeamsDatabase* | Prawidłowe nazwy baz danych znajdują się w temacie [identyfikatory baz danych](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Subskrypcja** | *Twoja subskrypcja*  | Wybierz tę samą subskrypcję, która została użyta do utworzenia pamięci podręcznej i hostowania usługi App Service. |
   | **Grupa zasobów**  | *TestResourceGroup* | Kliknij pozycję **Użyj istniejącej** i użyj tej samej grupy zasobów, w której umieszczono pamięć podręczną i usługę App Service. |
   | **Wybierz źródło** | **Pusta baza danych** | Zacznij od pustej bazy danych. |

1. W obszarze **Serwer** kliknij pozycje **Skonfiguruj wymagane ustawienia** > **Utwórz nowy serwer** i podaj następujące informacje, a następnie kliknij przycisk **Wybierz**:

   | Ustawienie       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa serwera** | Dowolna nazwa unikatowa w skali globalnej | Prawidłowe nazwy serwera opisano w artykule [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Reguły i ograniczenia nazewnictwa). |
   | **Identyfikator logowania administratora serwera** | Dowolna prawidłowa nazwa | Prawidłowe nazwy logowania można znaleźć w temacie [identyfikatory baz danych](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Hasło** | Dowolne prawidłowe hasło | Hasło musi mieć co najmniej 8 znaków i musi zawierać znaki z trzech z następujących kategorii: wielkie litery, małe litery, cyfry i znaki inne niż alfanumeryczne. |
   | **Lokalizacja** | *East US* | Wybierz ten sam region, w którym utworzono pamięć podręczną i usługę App Service. |

1. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**, aby utworzyć nową bazę danych i nowy serwer.

1. Po utworzeniu nowej bazy danych kliknij pozycję **Pokaż parametry połączenia bazy danych** i skopiuj parametry połączenia **ADO.NET**.

    ![Pokazywanie parametrów połączeń](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. W witrynie Azure Portal przejdź do usługi App Service i kliknij pozycję **Ustawienia aplikacji**, a następnie pozycję **Dodaj nowe parametry połączenia** w sekcji Parametry połączenia.

1. Dodaj nowe parametry połączenia o nazwie *TeamContext*, aby zachować zgodność z klasą kontekstu bazy danych platformy Entity Framework. Wklej parametry połączenia dla nowej bazy danych jako wartość. Pamiętaj, aby zastąpić następujące symbole zastępcze w parametrach połączenia, a następnie kliknij pozycję **Zapisz**:

    | Symbol zastępczy | Sugerowana wartość |
    | --- | --- |
    | *{your_username}* | Użyj **identyfikatora logowania administratora serwera** dla właśnie utworzonego serwera. |
    | *{your_password}* | Użyj hasła dla właśnie utworzonego serwera. |

    Gdy dodasz nazwę użytkownika i hasło jako ustawienie aplikacji, wartości te nie zostaną umieszczone w kodzie. To podejście pomaga chronić te poświadczenia.

### <a name="publish-the-application-updates-to-azure"></a>Publikowanie aktualizacji aplikacji na platformie Azure

W tym kroku samouczka opublikujesz aktualizacje aplikacji na platformie Azure w celu uruchamiania jej w chmurze.

1. Kliknij prawym przyciskiem myszy projekt **ContosoTeamStats** w programie Visual Studio i wybierz polecenie **Publikuj**.

    ![Opublikuj](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Kliknij pozycję **Opublikuj**, aby użyć tego samego profilu publikowania, który został utworzony w ramach przewodnika Szybki start.

3. Po zakończeniu publikowania program Visual Studio uruchomi aplikację w domyślnej przeglądarce internetowej.

    ![Dodano pamięć podręczną](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    W poniższej tabeli opisano każdy link akcji z przykładowej aplikacji:

    | Akcja | Opis |
    | --- | --- |
    | Kliknięcie przycisku Nowe |Tworzenie nowego zespołu. |
    | Play Season (Odtwarzaj sezon) |Odtwarzanie sezonu gier, aktualizacja statystyk zespołu i czyszczenie nieaktualnych danych zespołu z pamięci podręcznej. |
    | Clear Cache (Wyczyść pamięć podręczną) |Czyszczenie statystyk zespołu z pamięci podręcznej. |
    | List from Cache (Lista z pamięci podręcznej) |Pobieranie statystyk zespołu z pamięci podręcznej. W przypadku braku trafienia pamięci podręcznej ładuje statystyki z bazy danych i zapisuje je w pamięci podręcznej do użytku następnym razem. |
    | Sorted Set from Cache (Zestaw posortowany z pamięci podręcznej) |Pobieranie statystyk zespołu z pamięci podręcznej przy użyciu zestawu posortowanego. W przypadku braku trafienia pamięci podręcznej ładuje statystyki z bazy danych i zapisuje je w pamięci podręcznej przy użyciu zestawu posortowanego. |
    | Top 5 Teams from Cache (5 najlepszych zespołów z pamięci podręcznej) |Pobieranie najlepszych 5 zespołów z pamięci podręcznej przy użyciu zestawu posortowanego. W przypadku braku trafienia pamięci podręcznej ładuje statystyki z bazy danych i zapisuje je w pamięci podręcznej przy użyciu zestawu posortowanego. |
    | Load from DB (Ładuj z bazy danych) |Pobieranie statystyk zespołu z bazy danych. |
    | Rebuild DB (Odbuduj bazę danych) |Odbudowywanie bazy danych i ponowne jej ładowanie z użyciem przykładowych danych zespołu. |
    | Edytuj / Szczegóły / Usuń |Edytowanie zespołu, wyświetlanie szczegółów dla zespołu, usuwanie zespołu. |

Klikaj różne akcje i eksperymentuj z pobieraniem danych z różnych źródeł. Zwróć uwagę na różnice czasu, który jest potrzebny do realizacji różnych sposobów pobierania danych z bazy danych i pamięci podręcznej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z przykładową aplikacją z samouczka możesz usunąć używane zasoby platformy Azure w celu oszczędności kosztów i zasobów. Wszystkie zasoby powinny być zawarte w tej samej grupie zasobów, więc można je usunąć razem w ramach jednej operacji, usuwając grupę zasobów. Instrukcje dla tego tematu korzystały z grupy zasobów o nazwie *TestResources*.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku.
>

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.
2. Wpisz nazwę grupy zasobów w polu tekstowym **Filtruj elementy...**.
3. Kliknij pozycję **...** z prawej strony grupy zasobów i kliknij pozycję **Usuń grupę zasobów**.

    ![Usuwanie](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.

    Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak skalować usługę Azure Cache for Redis](./cache-how-to-scale.md)
