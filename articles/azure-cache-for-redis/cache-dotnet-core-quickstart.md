---
title: 'Szybki Start: korzystanie z usługi Azure cache for Redis z aplikacjami .NET Core'
description: Z tego przewodnika Szybki start dowiesz się, jak uzyskiwać dostęp do usługi Azure Cache for Redis w aplikacjach platformy .NET Core
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 1cb35ce995a27030612499cc3147943112e1c50e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89662376"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-core-app"></a>Szybki Start: korzystanie z usługi Azure cache for Redis z aplikacją platformy .NET Core

W tym przewodniku szybki start dodaliśmy usługę Azure cache for Redis do aplikacji platformy .NET Core w celu uzyskania dostępu do bezpiecznej, dedykowanej pamięci podręcznej dostępnej z dowolnej aplikacji na platformie Azure. Należy używać klienta [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) z kodem C# w aplikacji konsolowej platformy .NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Zanotuj **NAZWĘ HOSTA** i **podstawowy** klucz dostępu. Później użyjesz tych wartości do utworzenia wpisu tajnego *CacheConnection*.



## <a name="create-a-console-app"></a>tworzenie aplikacji konsoli

Otwórz nowe okno poleceń i wykonaj następujące polecenie, aby utworzyć nową aplikację konsolową platformy .NET Core:

```
dotnet new console -o Redistest
```

W oknie poleceń przełącz na nowy katalog projektów *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Dodawanie narzędzia Secret Manager do projektu

W tej sekcji dodasz narzędzie [Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Narzędzie Secret manager przechowuje poufne dane potrzebne w pracy deweloperskiej poza Twoim drzewem projektu. Takie podejście zapobiega przypadkowemu ujawnieniu wpisów tajnych aplikacji w kodzie źródłowym.

Otwórz plik *Redistest.csproj*. Dodaj element `DotNetCliToolReference`, aby uwzględnić narzędzia *Microsoft.Extensions.SecretManager.Tools*. Dodaj również element `UserSecretsId` w sposób pokazany poniżej, a następnie zapisz plik.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

Uruchom następujące polecenie, aby dodać do projektu pakiet *Microsoft.Extensions.Configuration.UserSecrets*:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Uruchom następujące polecenie, aby przywrócić pakiety:

```
dotnet restore
```

W oknie poleceń wykonaj następujące polecenie, aby zapisać nowy wpis tajny o nazwie *CacheConnection* po zastąpieniu symboli zastępczych (w tym nawiasów ostrych) nazwą pamięci podręcznej i podstawowym kluczem dostępu:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<primary-access-key>"
```

Dodaj następującą instrukcję akcji `using` do pliku *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration;
```

Dodaj następujące elementy członkowskie do klasy `Program` w pliku *Program.cs*. Ten kod inicjuje konfigurację do uzyskiwania dostępu do wpisu tajnego użytkownika dla parametrów połączenia usługi Azure Cache for Redis.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>Konfigurowanie klienta pamięci podręcznej

W tej sekcji skonfigurujesz aplikację konsolową umożliwiającą korzystanie z klienta [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) na platformie .NET.

W oknie poleceń uruchom następujące polecenie w katalogu projektów *Redistest*:

```
dotnet add package StackExchange.Redis
```

Po ukończeniu instalacji klient pamięci podręcznej *StackExchange.Redis* będzie dostępny do użycia z projektem.


## <a name="connect-to-the-cache"></a>Łączenie z pamięcią podręczną

Dodaj następującą instrukcję akcji `using` do pliku *Program.cs*:

```csharp
using StackExchange.Redis;
```

Połączenie z usługą Azure Cache for Redis jest zarządzane przez klasę `ConnectionMultiplexer`. Ta klasa powinna być udostępniana i wielokrotnie używana w aplikacji klienta. Nie należy tworzyć nowego połączenia dla każdej operacji. 

W pliku *Program.cs* dodaj następujące elementy członkowskie do klasy `Program` aplikacji konsolowej:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = Configuration[SecretName];
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

W tym podejściu do udostępniania wystąpienia klasy `ConnectionMultiplexer` w aplikacji użyto właściwości statycznej, która zwraca połączone wystąpienie. Ten kod zapewnia bezpieczny wątkowo sposób inicjowania tylko jednego połączonego wystąpienia klasy `ConnectionMultiplexer`. Parametr `abortConnect` ma wartość false, co oznacza, że wywołanie zostanie wykonane pomyślnie, nawet jeśli połączenie z usługą Azure Cache for Redis nie zostanie nawiązane. Kluczowa funkcja klasy `ConnectionMultiplexer` polega na automatycznym przywracaniu łączności z pamięcią podręczną po rozwiązaniu problemu z siecią lub usunięciu innych przyczyn.

Wartość wpisu tajnego *CacheConnection* jest dostępna przy użyciu dostawcy konfiguracji programu Secret Manager oraz używana jako parametr hasła.

## <a name="executing-cache-commands"></a>Wykonywanie poleceń pamięci podręcznej

W pliku *Program.cs* dodaj następujący kod do procedury `Main` klasy `Program` aplikacji konsolowej:

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = lazyConnection.Value.GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

    lazyConnection.Value.Dispose();
}
```

Zapisz plik *Program.cs*.

Usługa Azure Cache for Redis ma konfigurowalną liczbę baz danych (domyślnie 16), których można użyć do logicznego odseparowania danych w tej usłudze. Kod łączy się z domyślną bazą danych DB 0. Aby uzyskać więcej informacji, zobacz [What are Redis databases?](cache-development-faq.md#what-are-redis-databases) (Co to są bazy danych Redis?) i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Domyślna konfiguracja serwera Redis).

Elementy pamięci podręcznej można zapisywać i pobierać za pomocą metod `StringSet` i `StringGet`.

Usługa Redis przechowuje większość danych w formie ciągów Redis, ale ciągi te mogą zawierać wiele typów danych, w tym serializowane dane binarne, które mogą być używane podczas przechowywania obiektów platformy .NET w pamięci podręcznej.

Aby utworzyć aplikację, uruchom w oknie polecenia następujące polecenie:

```
dotnet build
```

Następnie uruchom aplikację za pomocą następującego polecenia:

```
dotnet run
```

W poniższym przykładzie widać, że klucz `Message` miał już w pamięci podręcznej wartość, która została ustawiona za pomocą konsoli Redis w witrynie Azure Portal. Aplikacja zaktualizowała tę wartość w pamięci podręcznej. Aplikacja również wykonała polecenia `PING` i `CLIENT LIST`.

![Częściowa aplikacja konsolowa](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Praca z obiektami platformy .NET w pamięci podręcznej

Usługa Azure Cache for Redis może buforować obiekty platformy .NET oraz pierwotne typy danych, ale zanim będzie możliwe buforowanie obiektu platformy .NET, trzeba go serializować. Odpowiedzialność za serializację obiektu .NET spoczywa na deweloperze aplikacji, który ma możliwość wybrania serializatora.

Prostym sposobem na wykonanie serializacji obiektów jest użycie metod serializacji `JsonConvert` w środowisku [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) oraz serializacja do i z formatu JSON. W tej sekcji dodasz obiekt platformy .NET do pamięci podręcznej.

Uruchom następujące polecenie, aby dodać pakiet *Newtonsoft.json* do aplikacji:

```
dotnet add package Newtonsoft.json
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

    public Employee(string EmployeeId, string Name, int Age)
    {
        this.Id = EmployeeId;
        this.Name = Name;
        this.Age = Age;
    }
}
```

W dolnej części procedury `Main()` w pliku *Program.cs* i przed wywołaniem do `Dispose()` dodaj następujące wiersze kodu do pamięci podręcznej i pobierz serializowany obiekt platformy .NET:

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

Zapisz plik *Program.cs* i ponownie skompiluj aplikację przy użyciu następującego polecenia:

```
dotnet build
```

Uruchom aplikację za pomocą następującego polecenia, aby przetestować serializację obiektów platformy .NET:

```
dotnet run
```

![Ukończono tworzenie aplikacji konsolowej](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuwanie](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.



<a name="next-steps"></a>

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób użycia usługi Azure Cache for Redis z poziomu aplikacji platformy .NET Core. Przejdź do kolejnego Szybkiego startu, w którym wyjaśniono, jak używać usługi Microsoft Azure Cache for Redis z poziomu aplikacji internetowej ASP.NET.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)