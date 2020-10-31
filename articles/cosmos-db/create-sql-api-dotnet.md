---
title: Szybki Start — Tworzenie aplikacji konsolowej platformy .NET do zarządzania zasobami usługi Azure Cosmos DB SQL API
description: Dowiedz się, jak utworzyć aplikację konsolową .NET, aby zarządzać Azure Cosmos DB zasobów konta interfejsu API SQL w tym przewodniku Szybki Start.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/21/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: aceb26604d67f42cdbbe1395e3a4b08675d70ea1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078530"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Szybki Start: Tworzenie aplikacji konsolowej .NET do zarządzania Azure Cosmos DB zasobów interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Wprowadzenie do Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET. Wykonaj kroki opisane w tym dokumencie, aby zainstalować pakiet .NET, skompilować aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w Azure Cosmos DB. 

Azure Cosmos DB to Microsoft Fast NoSQL Database z otwartymi interfejsami API dla dowolnej skali. Za pomocą Azure Cosmos DB można szybko tworzyć i badać bazy danych kluczy/wartości, dokumentów i grafów. Użyj Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET, aby:

* Tworzenie bazy danych i kontenera usługi Azure Cosmos
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Dokumentacja](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet&preserve-view=true)  |  interfejsu API [Kod](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/) lub [wypróbuj bezpłatnie bezpłatne Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. 
* [Zestaw .NET Core 2,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera szczegółowe instrukcje dotyczące tworzenia konta usługi Azure Cosmos oraz konfigurowania projektu korzystającego z Azure Cosmos DB biblioteki klienta interfejsu API SQL dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym artykule tworzy `FamilyDatabase` bazę danych i członków rodziny (każdy członek rodziny jest elementem) w tej bazie danych. Każdy członek rodziny ma właściwości, takie jak `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . `LastName`Właściwość jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>tworzenie konta usługi Azure Cosmos

Jeśli używasz opcji [wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/) , aby utworzyć konto usługi Azure Cosmos, musisz utworzyć konto Azure Cosmos DB typu **SQL API** . Konto testowe Azure Cosmos DB zostało już utworzone. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub masz subskrypcję bezpłatnie, należy utworzyć konto usługi Azure Cosmos jawnie. Poniższy kod utworzy konto usługi Azure Cosmos z spójnością sesji. Konto jest replikowane w `South Central US` i `North Central US` .  

Za pomocą Azure Cloud Shell można utworzyć konto usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Na potrzeby tego przewodnika Szybki Start wybierz tryb **bash** . Azure Cloud Shell również wymaga konta magazynu, można je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok poniższego kodu, wybierz tryb **bash** wybierz pozycję **Utwórz konto magazynu** i zaloguj się do Cloud Shell. Następnie skopiuj i wklej poniższy kod w celu Azure Cloud Shell i uruchomienia go. Nazwa konta usługi Azure Cosmos musi być globalnie unikatowa, pamiętaj o zaktualizowaniu `mysqlapicosmosdb` wartości przed uruchomieniem polecenia.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Tworzenie konta usługi Azure Cosmos zajmuje trochę czasu, po pomyślnym zakończeniu operacji można zobaczyć dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu wykonywania polecenia Zaloguj się do [Azure Portal](https://portal.azure.com/) i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Po utworzeniu zasobu możesz zamknąć okno Azure Cloud Shell. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji platformy .NET

Utwórz nową aplikację platformy .NET w preferowanym edytorze lub środowisku IDE. Otwórz wiersz polecenia systemu Windows lub okno terminalu na komputerze lokalnym. Wszystkie polecenia zostaną uruchomione w następnych sekcjach z poziomu wiersza polecenia lub terminalu.  Uruchom następujące polecenie dotnet New, aby utworzyć nową aplikację o nazwie `todo` . Parametr--langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

```console
dotnet new console --langVersion 7.1 -n todo
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
cd todo
dotnet build
```

Oczekiwane dane wyjściowe kompilacji powinny wyglądać następująco:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie pakietu Azure Cosmos DB

Nadal w katalogu aplikacji Zainstaluj Azure Cosmos DBą bibliotekę kliencką dla platformy .NET Core za pomocą polecenia dotnet Add Package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Skopiuj poświadczenia konta usługi Azure Cosmos z Azure Portal

Przykładowa aplikacja wymaga uwierzytelnienia na koncie usługi Azure Cosmos. Aby przeprowadzić uwierzytelnianie, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Uzyskaj poświadczenia konta usługi Azure Cosmos, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz okienko **klucze** i skopiuj **Identyfikator URI** i **klucz podstawowy** konta. W następnym kroku dodasz wartości identyfikatora URI i kluczy do zmiennej środowiskowej.

### <a name="set-the-environment-variables"></a>Ustawianie zmiennych środowiskowych

Po skopiowaniu **identyfikatora URI** i **klucza podstawowego** konta Zapisz je w nowej zmiennej środowiskowej na maszynie lokalnej, na której działa aplikacja. Aby ustawić zmienną środowiskową, Otwórz okno konsoli i uruchom następujące polecenie. Pamiętaj, aby zastąpić `<Your_Azure_Cosmos_account_URI>` `<Your_Azure_Cosmos_account_PRIMARY_KEY>` wartości i.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**macOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Model obiektów

Przed rozpoczęciem tworzenia aplikacji Przyjrzyjmy się hierarchii zasobów w Azure Cosmos DB i modelu obiektów używanym do tworzenia tych zasobów i uzyskiwania do nich dostępu. Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Elementy

Aby dowiedzieć się więcej o hierarchii różnych jednostek, zobacz [Praca z bazami danych, kontenerami i elementami w Azure Cosmos DB](account-databases-containers-items.md) artykule. Do korzystania z tych zasobów będziesz używać następujących klas platformy .NET:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient?preserve-view=true&view=azure-dotnet) — Ta klasa udostępnia logiczną reprezentację po stronie klienta dla usługi Azure Cosmos DB. Obiekt klienta służy do konfigurowania i wykonywania żądań dotyczących usługi.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet&preserve-view=true) — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczną. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true)— ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) kontener jako operację asynchroniczną. Możesz sprawdzić kod stanu z odpowiedzi, aby określić, czy kontener został nowo utworzony (201) czy został zwrócony istniejący kontener (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true) — ta metoda tworzy element w kontenerze. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet&preserve-view=true) — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje, lub zastępuje element, jeśli już istnieje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet&preserve-view=true) — ta metoda tworzy zapytanie dla elementów w kontenerze w bazie danych Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowane. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet&preserve-view=true) — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync` Metoda usuwa tylko bazę danych. Usuwanie `Cosmosclient` wystąpienia powinno nastąpić oddzielnie (które wykonuje Metoda DeleteDatabaseAndCleanupAsync. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy bazę danych rodziny w Azure Cosmos DB. Baza danych rodziny zawiera szczegóły rodziny, takie jak nazwa, adres, lokalizacja, skojarzone elementy nadrzędne, dzieci i zwierzęta domowe. Przed zapełnieniem danych na koncie usługi Azure Cosmos Zdefiniuj właściwości elementu rodziny. Utwórz nową klasę o nazwie `Family.cs` na poziomie głównym aplikacji przykładowej i Dodaj do niej następujący kod:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie dyrektyw using & Definiowanie obiektu klienta

W katalogu projektu Otwórz `Program.cs` plik w edytorze i Dodaj następujące dyrektywy using w górnej części aplikacji:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

W pliku **program.cs** Dodaj kod, aby odczytać zmienne środowiskowe, które zostały ustawione w poprzednim kroku. Zdefiniuj `CosmosClient` obiekty, `Database` i `Container` . Następnie Dodaj kod do metody Main, która wywołuje `GetStartedDemoAsync` metodę służącą do zarządzania zasobami kont usługi Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Tworzenie bazy danych 

Zdefiniuj `CreateDatabaseAsync` metodę w `program.cs` klasie. Ta metoda tworzy, `FamilyDatabase` Jeśli jeszcze nie istnieje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tworzenie kontenera

Zdefiniuj `CreateContainerAsync` metodę w `program.cs` klasie. Ta metoda tworzy, `FamilyContainer` Jeśli jeszcze nie istnieje. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Tworzenie elementu

Utwórz element rodziny poprzez dodanie metody do `AddItemsToContainerAsync` następującego kodu. Możesz użyć `CreateItemAsync` metod lub, `UpsertItemAsync` Aby utworzyć element:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>Zbadaj elementy

Po wstawieniu elementu można uruchomić zapytanie w celu uzyskania szczegółów rodziny "Andersen". Poniższy kod przedstawia sposób wykonywania zapytania bezpośrednio przy użyciu zapytania SQL. Zapytanie SQL, aby uzyskać szczegółowe informacje dotyczące rodziny "Anderson": `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Zdefiniuj `QueryItemsAsync` metodę w `program.cs` klasie i Dodaj do niej następujący kod:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Usuwanie bazy danych 

Na koniec można usunąć bazę danych, dodając do niej `DeleteDatabaseAndCleanupAsync` następujący kod:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Wykonywanie operacji CRUD

Po zdefiniowaniu wszystkich wymaganych metod wykonaj je za pomocą `GetStartedDemoAsync` metody. `DeleteDatabaseAndCleanupAsync`Metoda z komentarzem w tym kodzie, ponieważ nie będą wyświetlane żadne zasoby, jeśli ta metoda jest wykonywana. Po zweryfikowaniu, że Azure Cosmos DB zasoby zostały utworzone w Azure Portal, można usunąć z niego komentarz. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Po dodaniu wszystkich wymaganych metod Zapisz `Program.cs` plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następnie Skompiluj i uruchom aplikację, aby utworzyć zasoby Azure Cosmos DB. Upewnij się, że otwarto nowe okno wiersza polecenia, nie używaj tego samego wystąpienia, które było używane do ustawiania zmiennych środowiskowych. Ponieważ zmienne środowiskowe nie są ustawione w bieżącym otwartym oknie. Aby wyświetlić aktualizacje, należy otworzyć nowy wiersz polecenia. 

```console
dotnet build
```

```console
dotnet run
```

Po uruchomieniu aplikacji generowane są następujące dane wyjściowe. Możesz również zalogować się do Azure Portal i sprawdzić, czy zasoby są tworzone:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Możesz sprawdzić, czy dane zostały utworzone, logując się do Azure Portal i zobacz wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć konto usługi Azure Cosmos i odpowiednią grupę zasobów. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos, tworzenia bazy danych i kontenera przy użyciu aplikacji platformy .NET Core. Teraz możesz zaimportować dodatkowe dane do konta usługi Azure Cosmos za pomocą instrukcji int w następującym artykule. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)