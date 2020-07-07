---
title: Azure Cosmos DB interfejs API tabel przy użyciu zestawu SDK .NET Standard
description: Dowiedz się, jak przechowywać dane strukturalne i wysyłać do nich zapytania przy użyciu konta Azure Cosmos DB interfejs API tabel
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.openlocfilehash: 2133b68bf942cee87548783fb40d08c9bfe876ff
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851633"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Rozpoczynanie pracy z interfejsem API tabel usługi Azure Cosmos DB oraz usługą Azure Table Storage przy użyciu zestawu SDK platformy .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Za pomocą interfejs API tabel Azure Cosmos DB lub magazynu tabel platformy Azure można przechowywać strukturalne dane NoSQL w chmurze, zapewniając Magazyn kluczy/atrybutów ze schematem, który nie jest zaprojektowany. Ponieważ Azure Cosmos DB interfejs API tabel i magazyn tabel są mniej schematem, można łatwo dostosować dane w miarę rozwoju aplikacji. Za pomocą interfejs API tabel Azure Cosmos DB lub magazynu tabel można przechowywać elastyczne zestawy danych, takie jak dane użytkowników dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach lub inne typy metadanych, których wymaga Twoja usługa. 

W tym samouczku opisano przykład, w którym pokazano, jak używać [biblioteki tabel Microsoft Azure Cosmos DB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) z usługami Azure Cosmos DB interfejs API tabel i Azure Table Storage. Musisz użyć połączenia specyficznego dla usługi platformy Azure. Te scenariusze zostały omówione przy użyciu przykładów języka C#, które ilustrują sposób tworzenia tabel, wstawiania/aktualizowania danych, wykonywania zapytań dotyczących danych i usuwania tabel.

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia pracy z przykładem wymagane są następujące elementy:

* [Program Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Biblioteka tabel Microsoft Azure CosmosDB dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) — Ta biblioteka jest obecnie dostępna dla .NET Standard i programu .NET Framework. 

* [Azure Cosmos DB interfejs API tabel konta](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Tworzenie projektu konsoli .NET

W programie Visual Studio Utwórz nową aplikację konsolową platformy .NET. Poniższe kroki pokazują, jak utworzyć aplikację konsolową w programie Visual Studio 2019. Biblioteki tabel Azure Cosmos DB można używać w dowolnym typie aplikacji platformy .NET, w tym w usłudze w chmurze platformy Azure lub aplikacji sieci Web oraz aplikacji klasycznych i mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. Wybierz pozycję **aplikacja konsoli (.NET Core)**, a następnie wybierz przycisk **dalej**.

1. W polu **Nazwa projektu** wprowadź nazwę aplikacji, na przykład **CosmosTableSamples**. (W razie konieczności można podać inną nazwę).

1. Wybierz pozycję **Utwórz**.

Wszystkie przykłady kodu z tego przykładu można dodać do metody Main () pliku **program.cs** aplikacji konsolowej.

## <a name="install-the-required-nuget-package"></a>Zainstaluj wymagany pakiet NuGet

Aby uzyskać pakiet NuGet, wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. Wyszukaj w trybie online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) pozycję, [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) i wybierz pozycję **Zainstaluj** , aby zainstalować bibliotekę tabel Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

1. W [Azure Portal](https://portal.azure.com/)przejdź do konta usługi Azure Cosmos lub konta Table Storage. 

1. Otwórz okienko **Parametry połączenia** lub **klucze dostępu** . Użyj przycisku kopiowania po prawej stronie okna, aby skopiować **PODSTAWOWE PARAMETRY POŁĄCZENIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Wyświetlanie i kopiowanie PODSTAWOWYCH PARAMETRÓW POŁĄCZENIA w okienku parametrów połączenia":::
   
1. Aby skonfigurować parametry połączenia, w programie Visual Studio kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**.

1. Wybierz pozycję **Dodaj** , a następnie pozycję **nowy element**. Utwórz nowy plik **Settings.jsna serwerze** z typem pliku jako plik **konfiguracji JSON języka TypeScript** . 

1. Zastąp kod w Settings.jsw pliku następującym kodem i przypisz podstawowe parametry połączenia:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i dodaj klasę o nazwie **appSettings.cs**.

1. Dodaj następujący kod do pliku AppSettings.cs. Ten plik odczytuje parametry połączenia z Settings.jsw pliku i przypisuje je do parametru konfiguracji:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analizowanie i weryfikowanie szczegółów połączenia 

1. Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i dodaj klasę o nazwie **Common.cs**. Napiszesz kod, aby zweryfikować szczegóły połączenia i utworzyć tabelę w tej klasie.

1. Zdefiniuj metodę `CreateStorageAccountFromConnectionString` , jak pokazano poniżej. Ta metoda analizuje szczegóły parametrów połączenia i sprawdza, czy nazwa konta i szczegóły klucza konta podane w pliku "Settings.json" są prawidłowe. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Tworzenie tabeli 

Klasa [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) umożliwia pobieranie tabel i jednostek przechowywanych w usłudze Table Storage. Ponieważ nie mamy żadnych tabel na koncie Cosmos DB interfejs API tabel, dodajmy `CreateTableAsync` metodę do klasy **Common.cs** , aby utworzyć tabelę:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

Jeśli wystąpi błąd "wyjątek niedostępności usługi 503", istnieje możliwość, że wymagane porty dla trybu łączności są blokowane przez zaporę. Aby rozwiązać ten problem, należy otworzyć wymagane porty lub użyć łączności w trybie bramy, jak pokazano w poniższym kodzie:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Zdefiniuj jednostkę 

Jednostki mapują obiekty do obiektów C# przy użyciu klasy niestandardowej pochodnej od [klasy tableentity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki.

Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **Nowy folder** i nadaj jej nazwę **model**. W folderze model Dodaj klasę o nazwie **CustomerEntity.cs** i Dodaj do niej następujący kod.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Ten kod definiuje klasę jednostki używającą imienia klienta jako klucza wiersza i nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki o różnych kluczach partycji, ale użycie różnych kluczy partycji umożliwia większą skalowalność operacji równoległych. Jednostki, które mają być przechowywane w tabelach, muszą być obsługiwanego typu, na przykład muszą pochodzić od klasy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Właściwości jednostki, które zamierzasz przechowywać w tabeli, muszą być publicznymi właściwościami typu i obsługiwać zarówno pobieranie, jak i ustawianie wartości. Ponadto typ jednostki musi ujawniać konstruktor bez parametrów.

## <a name="insert-or-merge-an-entity"></a>Wstawianie lub scalanie jednostki

Poniższy przykład kodu tworzy obiekt Entity i dodaje go do tabeli. Metoda InsertOrMerge w klasie [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) służy do wstawiania lub scalania jednostki. Metoda [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) jest wywoływana, aby wykonać operację. 

Kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i dodaj klasę o nazwie **SamplesUtils.cs**. Ta klasa przechowuje wszystkie kody wymagane do wykonywania operacji CRUD na jednostkach. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

## <a name="get-an-entity-from-a-partition"></a>Pobieranie jednostki z partycji

Jednostkę można pobrać z partycji za pomocą metody get w klasie [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . Poniższy przykład kodu Pobiera klucz wiersza klucza partycji, adres e-mail i numer telefonu jednostki klienta. Ten przykład drukuje również jednostki żądań zużyte do zapytania dla jednostki. Aby wykonać zapytanie dotyczące jednostki, Dołącz następujący kod do pliku **SamplesUtils.cs** : 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Można z łatwością usunąć jednostkę po jej pobraniu, korzystając z tego samego wzorca co w przypadku aktualizowania jednostki. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta. Aby usunąć jednostkę, Dołącz następujący kod do pliku **SamplesUtils.cs** : 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Wykonywanie operacji CRUD na przykładowych danych

Po zdefiniowaniu metod tworzenia tabeli, wstawiania lub scalania jednostek należy uruchomić te metody na przykładowych danych. Aby to zrobić, kliknij prawym przyciskiem myszy projekt **CosmosTableSamples**. Wybierz pozycję **Dodaj**, **nowy element** i dodaj klasę o nazwie **BasicSamples.cs** i Dodaj do niej następujący kod. Ten kod tworzy tabelę, dodaje do niej jednostki. Jeśli chcesz usunąć jednostkę i tabelę na końcu projektu, Usuń Komentarze z `table.DeleteIfExistsAsync()` i `SamplesUtils.DeleteEntityAsync(table, customer)` metod z następującego kodu:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Poprzedni kod tworzy tabelę rozpoczynającą się od "demonstracji", a wygenerowany identyfikator GUID jest dołączany do nazwy tabeli. Następnie dodaje jednostkę klienta z imię i nazwisko jako "Harpa Waltera", a później aktualizuje numer telefonu tego użytkownika. 

W tym samouczku przedstawiono tworzenie kodu do wykonywania podstawowych operacji CRUD na danych przechowywanych na koncie interfejs API tabel. Można również wykonywać Zaawansowane operacje, takie jak — wsadowe Wstawianie danych, wykonywanie zapytań dotyczących wszystkich danych w obrębie partycji, wykonywanie zapytań względem zakresu danych w obrębie partycji, wyświetlanie listy tabel na koncie, których nazwy zaczynają się od określonego prefiksu. Możesz pobrać kompletną przykładową postać [Azure-Cosmos-Table-dotnet-Core-](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) Rozpoczynanie pracy z repozytorium GitHub. Klasa [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) ma więcej operacji, które można wykonywać na danych.  

## <a name="run-the-project"></a>Uruchamianie projektu

Z projektu **CosmosTableSamples**. Otwórz klasę o nazwie **program.cs** i Dodaj do niej następujący kod służący do wywoływania BasicSamples podczas działania projektu.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Teraz Skompiluj rozwiązanie i naciśnij klawisz F5, aby uruchomić projekt. Po uruchomieniu projektu w wierszu polecenia zostaną wyświetlone następujące dane wyjściowe:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Dane wyjściowe z wiersza polecenia":::

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że nie można odnaleźć Settings.jsw pliku podczas uruchamiania projektu, można go rozwiązać, dodając następujący wpis XML do ustawień projektu. Kliknij prawym przyciskiem myszy pozycję CosmosTableSamples, wybierz pozycję Edytuj CosmosTableSamples. csproj i Dodaj następujący element: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Teraz możesz zalogować się do Azure Portal i sprawdzić, czy dane istnieją w tabeli. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Wyniki w portalu":::

## <a name="next-steps"></a>Następne kroki

Teraz możesz przechodzić do następnego samouczka i dowiedzieć się, jak migrować dane do konta interfejs API tabel Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak wykonywać zapytania dotyczące danych](../cosmos-db/table-import.md)
