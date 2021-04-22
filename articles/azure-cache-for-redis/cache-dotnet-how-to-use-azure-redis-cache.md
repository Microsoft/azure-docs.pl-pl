---
title: 'Szybki start: używanie Azure Cache for Redis w .NET Framework'
description: Z tego przewodnika Szybki start dowiesz się, jak uzyskiwać dostęp do pamięci podręcznej Azure Cache for Redis z poziomu aplikacji platformy .NET.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: effab14316c4a959f22467b9cc50984b6571da55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872159"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Szybki start: używanie Azure Cache for Redis w .NET Framework

W tym przewodniku Szybki start dołączasz Azure Cache for Redis do aplikacji .NET Framework, aby mieć dostęp do bezpiecznej, dedykowanej pamięci podręcznej, która jest dostępna z dowolnej aplikacji na platformie Azure. Klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) należy używać z kodem C# w aplikacji konsolowej .NET.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w witrynie GitHub

Jeśli chcesz przejść bezpośrednio do kodu, zobacz .NET Framework [Szybki](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) start w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 lub wyższą](https://dotnet.microsoft.com/download/dotnet-framework), co jest wymagane przez klienta StackExchange.Redis.

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Utwórz na swoim komputerze plik o nazwie *CacheSecrets.config* i umieść go w lokalizacji, gdzie nie zostanie zaewidencjonowany za pomocą kodu źródłowego przykładowej aplikacji. W tym przewodniku Szybki start plik *CacheSecrets.config* znajduje się tu: *C:\AppSecrets\CacheSecrets.config*.

Edytuj plik *CacheSecrets.config* i dodaj następującą zawartość:

```xml
<appSettings>
    <add key="CacheConnection" value="<host-name>,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

Zastąp element `<host-name>` nazwą hosta pamięci podręcznej.

Zastąp element `<access-key>` kluczem podstawowym pamięci podręcznej.


## <a name="create-a-console-app"></a>tworzenie aplikacji konsoli

W Visual Studio kliknij pozycję **File** New Project  >  **(Plik nowego**  >  **projektu).**

Wybierz **pozycję Aplikacja konsoli (.NET Framework)** i **Dalej,** aby skonfigurować aplikację. Wpisz nazwę **projektu**, sprawdź, .NET Framework jest wybrana wersja **4.6.1** lub nowsza, a następnie kliknij przycisk Utwórz, aby utworzyć nową aplikację konsolowa. 

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Konfigurowanie klienta pamięci podręcznej

W tej sekcji skonfigurujesz aplikację konsolową umożliwiającą korzystanie z klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) na platformie .NET.

W Visual Studio narzędzia NuGet Menedżer pakietów Menedżer pakietów i uruchom następujące polecenie w  >    >  oknie Menedżer pakietów konsoli.

```powershell
Install-Package StackExchange.Redis
```

Po ukończeniu instalacji klient pamięci podręcznej *StackExchange.Redis* będzie dostępny do użycia z projektem.


## <a name="connect-to-the-cache"></a>Łączenie z pamięcią podręczną

W programie Visual Studio otwórz plik *App.config* i zaktualizuj go, aby uwzględnić atrybut `appSettings` `file`, który odwołuje się do pliku *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **Odwołania** i kliknij pozycję **Dodaj odwołanie**. Dodaj odwołanie do zestawu **System.Configuration**.

Dodaj następujące instrukcje `using` do pliku *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

Połączenie z usługą Azure Cache for Redis jest zarządzane przez klasę `ConnectionMultiplexer`. Ta klasa powinna być udostępniana i wielokrotnie używana w aplikacji klienta. Nie należy tworzyć nowego połączenia dla każdej operacji. 

Nigdy nie należy przechowywać poświadczeń w kodzie źródłowym. Aby przykład był prosty, używam tylko pliku konfiguracji zewnętrznych wpisów tajnych. Lepszym rozwiązaniem byłoby użycie [usługi Azure Key Vault z certyfikatami](/rest/api/keyvault/certificate-scenarios).

W pliku *Program.cs* dodaj następujące elementy członkowskie do klasy `Program` aplikacji konsolowej:

```csharp
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
```


W tym podejściu do udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji użyto właściwości statycznej, która zwraca połączone wystąpienie. Ten kod zapewnia bezpieczny wątkowo sposób inicjowania tylko jednego połączonego wystąpienia klasy `ConnectionMultiplexer`. Parametr `abortConnect` ma wartość false, co oznacza, że wywołanie zostanie wykonane pomyślnie, nawet jeśli połączenie z usługą Azure Cache for Redis nie zostanie nawiązane. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

Wartość ustawienia appSetting *CacheConnection* jest używana do odwoływania się do parametrów połączenia pamięci podręcznej z witryny Azure Portal jako parametru hasła.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>Obsługa typu RedisConnectionException i SocketException przez ponowne nawiązanie połączenia

Zalecanym najlepszym rozwiązaniem podczas wywoływania metod na jest próba automatycznego rozwiązania wyjątków i przez zamknięcie i ponowne nawiązaniu `ConnectionMultiplexer` `RedisConnectionException` `SocketException` połączenia.

Dodaj następujące instrukcje `using` do pliku *Program.cs*:

```csharp
using System.Net.Sockets;
using System.Threading;
```

W *programie Program.cs* dodaj następujące składowe do `Program` klasy :

```csharp
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

## <a name="executing-cache-commands"></a>Wykonywanie poleceń pamięci podręcznej

Dodaj następujący kod do procedury `Main` klasy `Program` aplikacji konsolowej:

```csharp
static void Main(string[] args)
{
    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Usługa Azure Cache for Redis ma konfigurowalną liczbę baz danych (domyślnie 16), których można użyć do logicznego odseparowania danych w tej usłudze. Kod łączy się z domyślną bazą danych DB 0. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-development-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis).

Elementy pamięci podręcznej można zapisywać i pobierać za pomocą metod `StringSet` i `StringGet`.

Usługa Redis przechowuje większość danych w formie ciągów Redis, ale ciągi te mogą zawierać wiele typów danych, w tym serializowane dane binarne, które mogą być używane podczas przechowywania obiektów platformy .NET w pamięci podręcznej.

Naciśnij klawisze **Ctrl+F5**, aby skompilować i uruchomić aplikację konsolową.

W poniższym przykładzie widać, że klucz `Message` miał już w pamięci podręcznej wartość, która została ustawiona za pomocą konsoli Redis w witrynie Azure Portal. Aplikacja zaktualizowała tę wartość w pamięci podręcznej. Aplikacja również wykonała polecenia `PING` i `CLIENT LIST`.

![Częściowa aplikacja konsolowa](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Praca z obiektami platformy .NET w pamięci podręcznej

Usługa Azure Cache for Redis może buforować obiekty platformy .NET oraz pierwotne typy danych, ale zanim będzie możliwe buforowanie obiektu platformy .NET, trzeba go serializować. Odpowiedzialność za serializację obiektu .NET spoczywa na deweloperze aplikacji, który ma możliwość wybrania serializatora.

Prostym sposobem na wykonanie serializacji obiektów jest użycie metod serializacji `JsonConvert` w środowisku [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) oraz serializacja do i z formatu JSON. W tej sekcji dodasz obiekt platformy .NET do pamięci podręcznej.

W Visual Studio narzędzia NuGet Menedżer pakietów Menedżer pakietów i uruchom następujące polecenie w  >    >  oknie Menedżer pakietów konsoli.

```powershell
Install-Package Newtonsoft.Json
```

Dodaj następującą instrukcję `using` na początku pliku *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Dodaj następującą definicję klasy `Employee` do pliku *Program.cs*:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

W dolnej części procedury `Main()` w pliku *Program.cs* i przed wywołaniem do `CloseConnection()` dodaj następujące wiersze kodu do pamięci podręcznej i pobierz serializowany obiekt platformy .NET:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Naciśnij klawisze **Ctrl+F5**, aby skompilować i uruchomić aplikację konsolową na potrzeby testowania serializacji obiektów platformy .NET. 

![Ukończono tworzenie aplikacji konsolowej](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuń](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.



<a name="next-steps"></a>

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób użycia usługi Azure Cache for Redis z poziomu aplikacji platformy .NET. Przejdź do kolejnego Szybkiego startu, w którym wyjaśniono, jak używać usługi Microsoft Azure Cache for Redis z poziomu aplikacji internetowej ASP.NET.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)

Chcesz zoptymalizować i zaoszczędzić na wydatkach na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
