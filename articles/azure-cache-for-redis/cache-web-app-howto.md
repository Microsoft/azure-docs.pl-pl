---
title: Tworzenie aplikacji sieci Web ASP.NET za pomocą usługi Azure cache dla Redis
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację internetową platformy ASP.NET przy użyciu usługi Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 19c54ad62e45ecf6e31b46d0291f61dca8e8d9b3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722467"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Szybki Start: korzystanie z usługi Azure cache for Redis z aplikacją internetową ASP.NET 

W tym przewodniku szybki start użyjesz programu Visual Studio 2019 do utworzenia aplikacji sieci Web ASP.NET, która łączy się z usługą Azure cache for Redis w celu przechowywania i pobierania danych z pamięci podręcznej. Następnie aplikacja zostanie wdrożona w Azure App Service.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w usłudze GitHub

Jeśli chcesz pominąć prosty kod, zobacz [Przewodnik Szybki start ASP.NET](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) z obciążeniami **ASP.NET i** projektowaniem dla **platformy Azure** .

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Otwórz program Visual Studio, a następnie wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

2. W oknie dialogowym **Tworzenie nowego projektu** wykonaj następujące czynności:

    ![Tworzenie projektu](./media/cache-web-app-howto/cache-create-project.png)

    a. W polu wyszukiwania wprowadź _aplikację sieci Web w języku C# ASP.NET_.

    b. Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)**.

    c. Wybierz pozycję **Next** (Dalej).

3. W polu **Nazwa projektu** Nadaj projektowi nazwę. W tym przykładzie użyliśmy nazwy **ContosoTeamStats**.

4. Sprawdź, czy wybrano **.NET Framework 4.6.1** lub wyższą.

5. Wybierz przycisk **Utwórz**.
   
6. Wybierz **MVC** jako typ projektu.

7. Upewnij się, że dla ustawienia **Uwierzytelnianie** wybrano pozycję **Bez uwierzytelniania**. Domyślne ustawienie **Uwierzytelnianie** może być różne w różnych wersjach programu Visual Studio. Aby je zmienić, wybierz pozycję **Zmień uwierzytelnianie**, a następnie pozycję **Bez uwierzytelniania**.

8. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej

Następnie utworzysz pamięć podręczną dla aplikacji.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>Aby edytować plik *CacheSecrets.config*

1. Utwórz plik na komputerze o nazwie *CacheSecrets.config*. Umieść go w lokalizacji, w której nie zostanie zaewidencjonowany przy użyciu kodu źródłowego przykładowej aplikacji. W tym przewodniku Szybki start plik *CacheSecrets.config* znajduje się w lokalizacji *C:\AppSecrets\CacheSecrets.config*.

1. Edytuj plik *CacheSecrets.config*. Następnie dodaj następującą zawartość:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. Zastąp element `<cache-name>` nazwą hosta pamięci podręcznej.

1. Zastąp element `<access-key>` kluczem podstawowym pamięci podręcznej.

    > [!TIP]
    > Podczas wymiany klucza można użyć pomocniczego klucza dostępu jako klucza alternatywnego, gdy następuje ponowne generowanie podstawowego klucza dostępu.
   >
1. Zapisz plik.

## <a name="update-the-mvc-application"></a>Aktualizowanie aplikacji MVC

W tej sekcji zaktualizujesz aplikację do obsługi nowego widoku, który wyświetla prosty test dla usługi Azure Cache for Redis.

* [Aktualizowanie pliku web.config ustawieniem aplikacji dla pamięci podręcznej](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* Konfigurowanie aplikacji do korzystania z klienta programu StackExchange.Redis
* Aktualizowanie plików HomeController i Layout
* Dodawanie nowego widok RedisCache

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Aktualizacja pliku web.config ustawieniem aplikacji dla pamięci podręcznej

Jeśli aplikacja jest uruchamiana lokalnie, informacje w pliku *CacheSecrets.config* są używane do łączenia z wystąpieniem usługi Azure Cache for Redis. Później wdrożysz tę aplikację na platformie Azure. W tym czasie skonfigurujesz na platformie Azure ustawienie aplikacji, którego aplikacja używa do pobierania informacji o połączeniu pamięci podręcznej zamiast tego pliku. 

Ponieważ plik *CacheSecrets.config* nie został wdrożony na platformie Azure za pomocą aplikacji, możesz go używać tylko podczas lokalnego testowania aplikacji. Aby zapobiec złośliwemu dostępowi do danych w pamięci podręcznej, maksymalnie zabezpiecz te informacje.

#### <a name="to-update-the-webconfig-file"></a>Aktualizowanie pliku *web.config*
1. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik *web.config*, aby go otworzyć.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. W pliku *web.config* znajdź element `<appSetting>`. Następnie dodaj następujący atrybut `file`. Jeśli wcześniej używana była inna nazwa pliku lub lokalizacja, podstaw te wartości w miejsce pokazanych w przykładzie.

* Przed: `<appSettings>`
* Otrzyma  `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

Środowisko uruchomieniowe ASP.NET scala zawartość pliku zewnętrznego ze znacznikami w elemencie `<appSettings>`. Środowisko uruchomieniowe ignoruje atrybut pliku, jeśli nie można odnaleźć określonego pliku. Wpisy tajne (parametry połączenia z pamięcią podręczną) nie są dołączone jako część kodu źródłowego aplikacji. Podczas wdrażania aplikacji internetowej na platformie Azure plik *CacheSecrets.config* nie jest wdrażany.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Aby skonfigurować aplikację do korzystania z programu StackExchange.Redis

1. Aby skonfigurować aplikację do używania pakietu NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dla programu Visual Studio, wybierz pozycje **Narzędzia > Menedżer pakietów NuGet > Konsola menedżera pakietów**.

2. W oknie `Package Manager Console` uruchom następujące polecenie:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu umożliwiające aplikacji klienta uzyskanie dostępu do usługi Azure Cache for Redis przy użyciu klienta usługi Azure Cache for Redis StackExchange. Jeśli wolisz użyć wersji biblioteki klienckiej `StackExchange.Redis` o silnej nazwie, zainstaluj pakiet `StackExchange.Redis.StrongName`.

### <a name="to-update-the-homecontroller-and-layout"></a>Aby zaktualizować pliki HomeController i Layout

1. W **Eksploratorze rozwiązań** rozwiń folder **Kontrolery**, a następnie otwórz plik *HomeController.cs*.

2. Dodaj następujące `using` instrukcje w górnej części pliku.

    ```csharp
    using StackExchange.Redis;
    using System.Configuration;
    using System.Net.Sockets;
    using System.Text;
    using System.Threading;
    ```

3. Dodaj następujących członków do klasy, `HomeController` Aby obsługiwać nową `RedisCache` akcję, która uruchamia niektóre polecenia w odniesieniu do nowej pamięci podręcznej.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

        IDatabase cache = GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        // Note that this requires allowAdmin=true in the connection string
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();
        var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
        IServer server = GetServer(endpoint.Host, endpoint.Port);
        ClientInfo[] clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (ClientInfo client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }

    private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
    private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
    private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

    private static readonly object reconnectLock = new object();

    // In general, let StackExchange.Redis handle most reconnects,
    // so limit the frequency of how often ForceReconnect() will
    // actually reconnect.
    public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

    // If errors continue for longer than the below threshold, then the
    // multiplexer seems to not be reconnecting, so ForceReconnect() will
    // re-create the multiplexer.
    public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

    public static int RetryMaxAttempts => 5;

    private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

    private static Lazy<ConnectionMultiplexer> CreateConnection()
    {
        return new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    }

    private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
    {
        if (oldConnection == null)
            return;

        try
        {
            oldConnection.Value.Close();
        }
        catch (Exception)
        {
            // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
        }
    }

    /// <summary>
    /// Force a new ConnectionMultiplexer to be created.
    /// NOTES:
    ///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
    ///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
    ///     3. Call this method every time you see a connection exception. The code will:
    ///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
    ///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
    /// </summary>
    public static void ForceReconnect()
    {
        var utcNow = DateTimeOffset.UtcNow;
        long previousTicks = Interlocked.Read(ref lastReconnectTicks);
        var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
        TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return;

        lock (reconnectLock)
        {
            utcNow = DateTimeOffset.UtcNow;
            elapsedSinceLastReconnect = utcNow - previousReconnectTime;

            if (firstErrorTime == DateTimeOffset.MinValue)
            {
                // We haven't seen an error since last reconnect, so set initial values.
                firstErrorTime = utcNow;
                previousErrorTime = utcNow;
                return;
            }

            if (elapsedSinceLastReconnect < ReconnectMinFrequency)
                return; // Some other thread made it through the check and the lock, so nothing to do.

            TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
            TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

            bool shouldReconnect =
                elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
                && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

            // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
            previousErrorTime = utcNow;

            if (!shouldReconnect)
                return;

            firstErrorTime = DateTimeOffset.MinValue;
            previousErrorTime = DateTimeOffset.MinValue;

            Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
            CloseConnection(oldConnection);
            lazyConnection = CreateConnection();
            Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
        }
    }

    // In real applications, consider using a framework such as
    // Polly to make it easier to customize the retry approach.
    private static T BasicRetry<T>(Func<T> func)
    {
        int reconnectRetry = 0;
        int disposedRetry = 0;

        while (true)
        {
            try
            {
                return func();
            }
            catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
            {
                reconnectRetry++;
                if (reconnectRetry > RetryMaxAttempts)
                    throw;
                ForceReconnect();
            }
            catch (ObjectDisposedException)
            {
                disposedRetry++;
                if (disposedRetry > RetryMaxAttempts)
                    throw;
            }
        }
    }

    public static IDatabase GetDatabase()
    {
        return BasicRetry(() => Connection.GetDatabase());
    }

    public static System.Net.EndPoint[] GetEndPoints()
    {
        return BasicRetry(() => Connection.GetEndPoints());
    }

    public static IServer GetServer(string host, int port)
    {
        return BasicRetry(() => Connection.GetServer(host, port));
    }
    ```

4. W **Eksploratorze rozwiązań** rozwiń folder **Widoki** > **Udostępnione**. Następnie otwórz plik *_Layout.cshtml*

    Zastąp:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    tym:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Aby dodać nowy widok RedisCache

1. W **Eksploratorze rozwiązań** rozwiń folder **Widoki**, a następnie kliknij prawym przyciskiem myszy folder **Główny**. Wybierz pozycję **Dodaj**  >  **Widok.**...

2. W oknie dialogowym **Dodawanie widoku** wprowadź **RedisCache** jako nazwę widoku. Następnie wybierz pozycję **Dodaj**.

3. Zastąp kod w pliku *RedisCache.cshtml* następującym kodem:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Domyślnie projekt jest konfigurowany do lokalnego hostowania aplikacji w usługach [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) na potrzeby testowania i debugowania.

### <a name="to-run-the-app-locally"></a>Uruchamianie aplikacji lokalnie
1. W programie Visual Studio wybierz kolejno opcje **Debuguj**  >  **Rozpocznij debugowanie** , aby skompilować i uruchomić aplikację lokalnie na potrzeby testowania i debugowania.

2. W przeglądarce wybierz pozycję **Test usługi Azure Cache for Redis** na pasku nawigacyjnym.

3. W poniższym przykładzie klucz `Message` miał już w pamięci podręcznej wartość, która została ustawiona za pomocą konsoli usługi Azure Cache for Redis w portalu. Aplikacja zaktualizowała tę wartość w pamięci podręcznej. Aplikacja również wykonała polecenia `PING` i `CLIENT LIST`.

    ![Prosty test ukończony lokalnie](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Publikowanie i uruchamianie na platformie Azure

Po pomyślnym przetestowaniu aplikacji lokalnie możesz wdrożyć ją na platformie Azure i uruchomić w chmurze.

### <a name="to-publish-the-app-to-azure"></a>Aby opublikować aplikację na platformie Azure

1. W programie Visual Studio kliknij prawym przyciskiem myszy węzeł projektu w Eksploratorze rozwiązań. Następnie wybierz pozycję **Opublikuj**.

    ![Publikowanie](./media/cache-web-app-howto/cache-publish-app.png)

2. Wybierz pozycję **Microsoft Azure App Service**, wybierz opcję **Utwórz nowy**, a następnie wybierz pozycję **Opublikuj**.

    ![Publikowanie w usłudze App Service](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. W oknie dialogowym **Tworzenie usługi App Service** wprowadź następujące zmiany:

    | Ustawienie | Zalecana wartość | Opis |
    | ------- | :---------------: | ----------- |
    | **Nazwa aplikacji** | Użyj wartości domyślnej. | Nazwa aplikacji będzie nazwą hosta dla aplikacji po wdrożeniu na platformie Azure. Nazwa może mieć dodany sufiks znacznika czasu, aby zapewnić jej unikatowość w razie potrzeby. |
    | **Subskrypcja** | Wybierz subskrypcję platformy Azure. | Dla tej subskrypcji zostanie naliczona opłata za wszelkie powiązane koszty hostingu. Jeśli masz wiele subskrypcji platformy Azure, sprawdź, czy została wybrana odpowiednia subskrypcja.|
    | **Grupa zasobów** | Użyj tej samej grupy zasobów, w której została utworzona pamięć podręczna (na przykład *TestResourceGroup*). | Grupa zasobów pomaga zarządzać wszystkimi zasobami jako grupą. Później, gdy zechcesz usunąć aplikację, wystarczy tylko usunąć grupę. |
    | **Plan usługi App Service** | Wybierz pozycję **Nowy**, a następnie utwórz nowy plan usługi App Service o nazwie *TestingPlan*. <br />Użyj tej samej **lokalizacji**, która była używana podczas tworzenia pamięci podręcznej. <br />Jako rozmiar wybierz wartość **Bezpłatny**. | Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania. |

    ![Okno dialogowe usługi App Service](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Po skonfigurowaniu ustawień hostingu usługi App Service wybierz pozycję **Utwórz**.

5. Monitoruj okno **Dane wyjściowe** w programie Visual Studio, aby zobaczyć stan publikowania. Po opublikowaniu aplikacji zostaje zarejestrowany jej adres URL:

    ![Publikowanie danych wyjściowych](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Dodawanie ustawienia aplikacji dla pamięci podręcznej

Po opublikowaniu nowej aplikacji dodaj nowe ustawienie aplikacji. To ustawienie jest używane do zapisywania informacji o połączeniu z pamięcią podręczną. 

#### <a name="to-add-the-app-setting"></a>Aby dodać ustawienie aplikacji 

1. Wpisz nazwę aplikacji na pasku wyszukiwania w górnej części witryny Azure Portal, aby znaleźć nową utworzoną aplikację.

    ![Znajdowanie aplikacji](./media/cache-web-app-howto/cache-find-app-service.png)

2. Dodaj ustawienie nowej aplikacji o nazwie **CacheConnection** dla aplikacji, aby jej używać do łączenia się z pamięcią podręczną. Użyj tej samej wartości, która została skonfigurowana dla `CacheConnection` w pliku *CacheSecrets.config*. Wartość zawiera nazwę hosta pamięci podręcznej i klucz dostępu.

    ![Dodawanie ustawienia aplikacji](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Uruchamianie aplikacji na platformie Azure

W przeglądarce przejdź do adresu URL aplikacji. Adres URL jest wyświetlany w wynikach operacji publikowania w oknie danych wyjściowych programu Visual Studio. Jest on również udostępniany w witrynie Azure Portal na stronie omówienia utworzonej przez Ciebie aplikacji.

Wybierz pozycję **Test usługi Azure Cache for Redis** na pasku nawigacyjnym, aby przetestować dostęp do pamięci podręcznej.

![Ukończony prosty test na platformie Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Jeśli usuniesz grupę zasobów, wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.

### <a name="to-delete-a-resource-group"></a>Aby usunąć grupę zasobów

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com), a następnie wybierz pozycję **Grupy zasobów**.

2. W polu **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie wybierz pozycję **Usuń grupę zasobów**.

    ![Usuń](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie wybierz pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie jej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku użyjesz usługi Azure Cache for Redis w scenariuszu bardziej zbliżonym do rzeczywistego, aby poprawić wydajność aplikacji. Zaktualizujesz tę aplikację tak, aby buforowała wyniki rankingu przy użyciu wzorca odkładania do pamięci podręcznej za pomocą platformy ASP.NET i bazy danych.

> [!div class="nextstepaction"]
> [Tworzenie rankingu z odkładaniem do pamięci podręcznej na platformie ASP.NET](cache-web-app-cache-aside-leaderboard.md)