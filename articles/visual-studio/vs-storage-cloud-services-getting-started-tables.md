---
title: Wprowadzenie do usługi Table Storage przy użyciu programu Visual Studio (Cloud Services)
description: Jak rozpocząć korzystanie z usługi Azure Table Storage w projekcie usługi w chmurze w programie Visual Studio po nawiązaniu połączenia z kontem magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545797"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Wprowadzenie do usługi Azure Table Storage i usług połączonych programu Visual Studio (projekty usług Cloud Services)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak rozpocząć korzystanie z usługi Azure Table Storage w programie Visual Studio po utworzeniu lub przywoływaniu konta usługi Azure Storage w projekcie usług w chmurze za pomocą okna dialogowego **Dodawanie połączonych usług** programu Visual Studio. Operacja **Dodaj podłączone usługi** instaluje odpowiednie pakiety NuGet w celu uzyskania dostępu do usługi Azure Storage w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu.

Usługa Azure Table Storage umożliwia przechowywanie dużych ilości danych strukturalnych. Usługa to magazyn danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych.

Aby rozpocząć, musisz najpierw utworzyć tabelę na koncie magazynu. Pokażemy Ci, jak utworzyć tabelę platformy Azure w kodzie, a także jak wykonywać podstawowe operacje tabeli i jednostek, takie jak dodawanie, modyfikowanie, odczytywanie i odczytywanie jednostek tabeli. Przykłady są zapisywane w kodzie języka C \# i używają [Microsoft Azure Storage biblioteki klienckiej dla platformy .NET](/previous-versions/azure/dn261237(v=azure.100)).

**Uwaga:** Niektóre interfejsy API, które wykonują wywołania do usługi Azure Storage, są asynchroniczne. Aby uzyskać więcej informacji [, zobacz Programowanie asynchroniczne z Async i await](/previous-versions/hh191443(v=vs.140)) . W poniższym kodzie przyjęto, że są używane asynchroniczne metody programowania.

* Zobacz Rozpoczynanie [pracy z usługą Azure Table Storage przy użyciu platformy .NET](../cosmos-db/tutorial-develop-table-dotnet.md) , aby uzyskać więcej informacji na temat programistycznego manipulowania tabelami.
* Aby uzyskać ogólne informacje o usłudze Azure Storage, zobacz [dokumentację usługi Storage](https://azure.microsoft.com/documentation/services/storage/) .
* Zobacz [dokumentację Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) , aby uzyskać ogólne informacje na temat usług Azure Cloud Services.
* Aby uzyskać więcej informacji na temat programowania aplikacji ASP.NET, zobacz [ASP.NET](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Dostęp do tabel w kodzie
Aby uzyskać dostęp do tabel w projektach usług w chmurze, należy uwzględnić następujące elementy w dowolnych plikach źródłowych języka C#, które uzyskują dostęp do usługi Azure Table Storage.

1. Upewnij się, że deklaracje przestrzeni nazw na początku pliku C# zawierają te instrukcje **using** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Pobierz obiekt **CloudStorageAccount** , który reprezentuje informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Skorzystaj ze wszystkich powyższych kodów przed kodem w poniższych przykładach.
   > 
   > 
3. Pobierz obiekt **CloudTableClient** , aby odwoływać się do obiektów tabeli na koncie magazynu.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Pobierz obiekt odwołania w **chmurze** , aby odwołać się do określonej tabeli i jednostek.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tworzenie tabeli w kodzie
Aby utworzyć tabelę platformy Azure, po prostu Dodaj wywołanie **CreateIfNotExistsAsync** do elementu po otrzymaniu obiektu w **chmurze** zgodnie z opisem w sekcji "tabele dostępu w kodzie".

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli
Aby dodać jednostkę do tabeli, należy utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki o nazwie **CustomerEntity** , która używa imienia klienta jako klucza wiersza i nazwisko jako klucz partycji.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Operacje tabeli obejmujące jednostki są wykonywane przy użyciu obiektu w postaci **chmury** , który został utworzony wcześniej w "tabele dostępu w kodzie". Obiekt **TableOperation** reprezentuje operację, która ma zostać wykonana. Poniższy przykład kodu pokazuje, jak utworzyć obiekt w **chmurze** i obiekt **CustomerEntity** . Aby przygotować operację, tworzony jest **TableOperation** , aby wstawić jednostkę klienta do tabeli. Na koniec operacja jest wykonywana przez wywołanie **CloudTable.ExecuteAsync**.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```


## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek
Można wstawić wiele jednostek do tabeli w jednej operacji zapisu. Poniższy przykład kodu tworzy dwa obiekty jednostki ("Jan Kowalski" i "Ben Kowalski"), dodaje je do obiektu **obiektu tablebatchoperation** przy użyciu metody INSERT, a następnie uruchamia operację, wywołując **CloudTable.ExecuteBatchAsync**.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji
Aby zbadać tabelę dla wszystkich jednostek w partycji, użyj obiektu **TableQuery** . Poniższy przykład kodu określa filtr jednostek, gdzie „Smith” jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);

return View();
```


## <a name="get-a-single-entity"></a>Pobieranie pojedynczej jednostki
Można napisać zapytanie w celu pobrania pojedynczej określonej jednostki. Poniższy kod używa obiektu **TableOperation** , aby określić klienta o nazwie "Ben Kowalski". Ta metoda zwraca tylko jedną jednostkę, a nie kolekcję, a zwrócona wartość w **pozycji tableresult. Result** to obiekt **CustomerEntity** . Określanie kluczy partycji i wierszy w zapytaniu jest najszybszym sposobem na pobranie pojedynczej jednostki z usługi **Table** Service.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Usuwanie jednostki
Możesz usunąć jednostkę po jej znalezieniu. Poniższy kod szuka jednostki klienta o nazwie "Ben Kowalski" i jeśli ją wykryje, spowoduje jej usunięcie.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    await peopleTable.ExecuteAsync(deleteOperation);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]