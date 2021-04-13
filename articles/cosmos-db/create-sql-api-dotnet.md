---
title: Szybki start — tworzenie aplikacji konsolowej .NET do zarządzania zasobami Azure Cosmos DB API SQL
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację konsolą platformy .NET do zarządzania Azure Cosmos DB kontami interfejsu API SQL.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: c7dc3a230e2cd3f990771c297de29891de347089
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363414"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Szybki start: tworzenie aplikacji konsolowej .NET do zarządzania zasobami interfejsu API SQL Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Rozpoczynanie pracy z biblioteką Azure Cosmos DB klienta interfejsu API SQL dla platformy .NET. Postępuj zgodnie z instrukcjami w tym instrukcjami, aby zainstalować pakiet .NET, skompilować aplikację i wypróbować przykładowy kod dla podstawowych operacji CRUD na danych przechowywanych w programie Azure Cosmos DB. 

Azure Cosmos DB to szybka baza danych NoSQL firmy Microsoft z otwartymi interfejsami API dla dowolnej skali. Za pomocą Azure Cosmos DB można szybko tworzyć i tworzyć zapytania dotyczące klucza/wartości, dokumentu i grafowych baz danych. Użyj biblioteki Azure Cosmos DB klienta interfejsu API SQL dla platformy .NET, aby:

* Tworzenie bazy danych i kontenera usługi Azure Cosmos
* Dodawanie przykładowych danych do kontenera
* Wykonywanie zapytań na danych 
* Usuwanie bazy danych

[Dokumentacja referencyjna interfejsu API](/dotnet/api/microsoft.azure.cosmos)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-cosmos-dotnet-v3)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz](https://azure.microsoft.com/free/) ją bezpłatnie lub wypróbuj [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bezpłatnie bez subskrypcji platformy Azure — bezpłatnie i bez zobowiązań. 
* [Zestaw .NET Core 2.1 SDK lub nowszy.](https://dotnet.microsoft.com/download/dotnet-core/2.1)

## <a name="setting-up"></a>Konfigurowanie

Ta sekcja zawiera instrukcje tworzenia konta usługi Azure Cosmos i konfigurowania projektu, który używa biblioteki klienta interfejsu API SQL Azure Cosmos DB dla platformy .NET do zarządzania zasobami. Przykładowy kod opisany w tym artykule tworzy bazę danych i członków rodziny (każdy członek rodziny jest `FamilyDatabase` elementem) w tej bazie danych. Każdy członek rodziny ma właściwości takie jak `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` . Właściwość `LastName` jest używana jako klucz partycji dla kontenera. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>tworzenie konta usługi Azure Cosmos

Jeśli do utworzenia konta usługi Azure Cosmos [używasz](https://azure.microsoft.com/try/cosmosdb/) Azure Cosmos DB Wypróbuj bezpłatnie, musisz utworzyć konto usługi Azure Cosmos DB typu **INTERFEJS API SQL.** Konto Azure Cosmos DB testowe zostało już utworzone. Nie musisz jawnie tworzyć konta, więc możesz pominąć tę sekcję i przejść do następnej sekcji.

Jeśli masz własną subskrypcję platformy Azure lub bezpłatnie utworzono subskrypcję, musisz jawnie utworzyć konto usługi Azure Cosmos. Poniższy kod spowoduje utworzenie konta usługi Azure Cosmos ze spójnością sesji. Konto jest replikowane w `South Central US` programach i `North Central US` .  

Możesz użyć Azure Cloud Shell, aby utworzyć konto usługi Azure Cosmos. Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure. Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Na przykład w tym przewodniku Szybki start wybierz **pozycję Tryb powłoki Bash.** Azure Cloud Shell wymaga również konta magazynu, można je utworzyć po wyświetleniu monitu.

Wybierz przycisk **Wypróbuj** obok poniższego kodu, wybierz pozycję Tryb **powłoki Bash,** a następnie wybierz pozycję Utwórz **konto magazynu** i zaloguj się do Cloud Shell. Następnie skopiuj i wklej następujący kod, aby Azure Cloud Shell i uruchom go. Nazwa konta usługi Azure Cosmos musi być globalnie unikatowa. Pamiętaj o zaktualizowaniu wartości przed `mysqlapicosmosdb` uruchomieniem polecenia.

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

Utworzenie konta usługi Azure Cosmos zajmuje trochę czasu. Po pomyślnym zakończeniu operacji można zobaczyć dane wyjściowe potwierdzenia. Po pomyślnym zakończeniu polecenia [](https://portal.azure.com/) zaloguj się do Azure Portal i sprawdź, czy istnieje konto usługi Azure Cosmos o określonej nazwie. Okno aplikacji można Azure Cloud Shell po utworzeniu zasobu. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Tworzenie nowej aplikacji .NET

Utwórz nową aplikację .NET w preferowanym edytorze lub w preferowanym idee. Otwórz wiersz polecenia systemu Windows lub okno terminalu z komputera lokalnego. Wszystkie polecenia zostaną uruchomione w następnych sekcjach z wiersza polecenia lub terminalu.  Uruchom następujące polecenie dotnet new, aby utworzyć nową aplikację o nazwie `todo` . Parametr --langVersion ustawia właściwość LangVersion w utworzonym pliku projektu.

```console
dotnet new console --langVersion 7.1 -n todo
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować za pomocą:

```console
cd todo
dotnet build
```

Oczekiwane dane wyjściowe z kompilacji powinny wyglądać podobnie do tych:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instalowanie Azure Cosmos DB pakietów

Nadal w katalogu aplikacji zainstaluj bibliotekę klienta Azure Cosmos DB dla programu .NET Core za pomocą polecenia dotnet add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Skopiuj poświadczenia konta usługi Azure Cosmos z Azure Portal

Przykładowa aplikacja musi uwierzytelnić się na twoim koncie usługi Azure Cosmos. Aby się uwierzytelnić, należy przekazać poświadczenia konta usługi Azure Cosmos do aplikacji. Pobierz poświadczenia konta usługi Azure Cosmos, wykonać następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz okienko **Klucze** i skopiuj dane **URI** i **KLUCZ PODSTAWOWY** konta. W następnym kroku dodasz wartości URI i keys do zmiennej środowiskowej.

### <a name="set-the-environment-variables"></a>Ustawianie zmiennych środowiskowych

Po skopiowaniu wartości **URI** i **PRIMARY KEY** konta zapisz je w nowej zmiennej środowiskowej na komputerze lokalnym, na którym działa aplikacja. Aby ustawić zmienną środowiskową, otwórz okno konsoli i uruchom następujące polecenie. Pamiętaj, aby zastąpić `<Your_Azure_Cosmos_account_URI>` wartości `<Your_Azure_Cosmos_account_PRIMARY_KEY>` i .

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

Przed rozpoczęciem tworzenia aplikacji przyjrzyjmy się hierarchii zasobów w programie Azure Cosmos DB i modelowi obiektów używanego do tworzenia tych zasobów i uzyskiwania do nich dostępu. Ten Azure Cosmos DB tworzy zasoby w następującej kolejności:

* Konto usługi Azure Cosmos 
* Bazy danych 
* Kontenery 
* Elementy

Aby dowiedzieć się więcej na temat hierarchii różnych jednostek, zobacz artykuł Praca z bazami danych, kontenerami [i elementami Azure Cosmos DB](account-databases-containers-items.md) artykule. Do interakcji z tymi zasobami użyjemy następujących klas .NET:

* [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) — ta klasa zapewnia logiczną reprezentację usługi Azure Cosmos DB klienta. Obiekt klienta służy do konfigurowania i wykonywania żądań względem usługi.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) — ta metoda tworzy (jeśli nie istnieje) lub pobiera (jeśli już istnieje) zasób bazy danych jako operację asynchroniczną. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync)— ta metoda tworzy (jeśli nie istnieje) lub pobiera kontener (jeśli już istnieje) jako operację asynchroniczną. Możesz sprawdzić kod stanu z odpowiedzi, aby określić, czy kontener został nowo utworzony (201), czy został zwrócony istniejący kontener (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) — ta metoda tworzy element w kontenerze. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) — ta metoda tworzy element w kontenerze, jeśli jeszcze nie istnieje, lub zastępuje element, jeśli już istnieje. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator) — ta metoda tworzy zapytanie dotyczące elementów w kontenerze w bazie danych usługi Azure Cosmos przy użyciu instrukcji SQL z wartościami sparametryzowane. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) — usuwa określoną bazę danych z konta usługi Azure Cosmos. `DeleteAsync` Metoda usuwa tylko bazę danych. Usuwanie wystąpienia powinno nastąpić oddzielnie (co dzieje się w metodzie `Cosmosclient` DeleteDatabaseAndCleanupAsync). 

 ## <a name="code-examples"></a><a id="code-examples"></a>Przykłady kodu

Przykładowy kod opisany w tym artykule tworzy bazę danych rodziny w Azure Cosmos DB. Baza danych rodziny zawiera szczegóły rodziny, takie jak imię i nazwisko, adres, lokalizacja, powiązani z nią rodziców, dzieci i zwierzęta domowe. Przed wypełnieniem danych na koncie usługi Azure Cosmos zdefiniuj właściwości elementu rodziny. Utwórz nową klasę o nazwie na poziomie głównym przykładowej aplikacji i dodaj do niego `Family.cs` następujący kod:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Dodawanie dyrektyw using & definiowanie obiektu klienta

W katalogu projektu otwórz plik w edytorze i dodaj następujące dyrektywy `Program.cs` using w górnej części aplikacji:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Do pliku **Program.cs** dodaj kod, aby odczytać zmienne środowiskowe ustawione w poprzednim kroku. `CosmosClient`Zdefiniuj `Database` obiekty , i `Container` . Następnie dodaj kod do metody main, która wywołuje `GetStartedDemoAsync` metodę, w której zarządzasz zasobami konta usługi Azure Cosmos. 

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

`CreateDatabaseAsync`Zdefiniuj metodę w `program.cs` klasie . Ta metoda tworzy metodę `FamilyDatabase` , jeśli jeszcze nie istnieje.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Tworzenie kontenera

`CreateContainerAsync`Zdefiniuj metodę w `program.cs` klasie . Ta metoda tworzy metodę `FamilyContainer` , jeśli jeszcze nie istnieje. 

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

Utwórz element rodziny, dodając `AddItemsToContainerAsync` metodę z następującym kodem. Aby utworzyć `CreateItemAsync` `UpsertItemAsync` element, można użyć metod lub :

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

### <a name="query-the-items"></a>Wykonywanie zapytań o elementy

Po wstawieniu elementu możesz uruchomić zapytanie, aby uzyskać szczegółowe informacje o rodzinie "Andersen". Poniższy kod pokazuje, jak wykonać zapytanie bezpośrednio przy użyciu zapytania SQL. Zapytanie SQL w celu uzyskania szczegółów rodziny "Anderson" to: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . `QueryItemsAsync`Zdefiniuj metodę `program.cs` w klasie i dodaj do niego następujący kod:


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

Na koniec możesz usunąć bazę danych, `DeleteDatabaseAndCleanupAsync` dodając metodę za pomocą następującego kodu:

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

Po zdefiniowanych wszystkich wymaganych metodach wykonaj je za pomocą metody w `GetStartedDemoAsync` metodzie . Metoda `DeleteDatabaseAndCleanupAsync` została skomentowana w tym kodzie, ponieważ nie zobaczysz żadnych zasobów, jeśli ta metoda jest wykonywana. Możesz go odkomentować po Azure Cosmos DB, czy zasoby zostały utworzone w Azure Portal. 

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

Po dodaniu wszystkich wymaganych metod zapisz `Program.cs` plik. 

## <a name="run-the-code"></a>Uruchamianie kodu

Następnie skompilować i uruchomić aplikację, aby utworzyć Azure Cosmos DB zasobów. Pamiętaj, aby otworzyć nowe okno wiersza polecenia. Nie używaj tego samego wystąpienia, które było używane do ustawienia zmiennych środowiskowych. Ponieważ zmienne środowiskowe nie są ustawione w bieżącym otwartym oknie. Aby wyświetlić aktualizacje, należy otworzyć nowy wiersz polecenia. 

```console
dotnet build
```

```console
dotnet run
```

Następujące dane wyjściowe są generowane po uruchomieniu aplikacji. Możesz również zalogować się do Azure Portal i sprawdzić, czy zasoby zostały utworzone:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Możesz sprawdzić, czy dane zostały utworzone, logując się do Azure Portal i wyświetlić wymagane elementy na koncie usługi Azure Cosmos. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy konto usługi Azure Cosmos i odpowiednią grupę zasobów nie będą już potrzebne, możesz je usunąć za pomocą interfejsu wiersza polecenia platformy Azure Azure PowerShell usługi Azure. Następujące polecenie pokazuje, jak usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób tworzenia konta usługi Azure Cosmos, tworzenia bazy danych i kontenera przy użyciu aplikacji platformy .NET Core. Teraz możesz zaimportować dodatkowe dane do konta usługi Azure Cosmos, zgodnie z instrukcjami w poniższym artykule. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
