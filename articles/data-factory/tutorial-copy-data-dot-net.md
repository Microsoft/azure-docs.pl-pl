---
title: Skopiuj dane z usługi Azure Blob Storage do Azure SQL Database
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące kopiowania danych z usługi Azure Blob Storage do bazy danych Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 2a9e19f24c4889a194c85e33317860db1d3a68d2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736563"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych z obiektu blob platformy Azure do bazy danych Azure SQL Database przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym samouczku utworzysz potok fabryki danych, który kopiuje dane z usługi Azure Blob Storage do bazy danych Azure SQL Database. Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure Storage i Azure SQL Database.
> * Tworzenie zestawów danych obiektu Blob platformy Azure i bazy danych Azure SQL Database.
> * Tworzenie potoku zawierającego działanie kopiowania.
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używany zestaw SDK platformy .NET. Można użyć innych mechanizmów do współpracy z Azure Data Factory; Zapoznaj się z przykładami w sekcji **Przewodniki Szybki Start**.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

* *Konto usługi Azure Storage*. Magazyn obiektów blob jest używany jako *źródłowy* magazyn danych. Jeśli nie masz konta usługi Azure Storage, zobacz [Tworzenie konta magazynu ogólnego przeznaczenia](../storage/common/storage-account-create.md).
* *Azure SQL Database*. Baza danych jest używana jako magazyn danych *ujścia*. Jeśli nie masz bazy danych w Azure SQL Database, zapoznaj się z tematem [Tworzenie bazy danych w programie Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* *Program Visual Studio*. W przewodniku w tym artykule jest wykorzystywany program Visual Studio 2019.
* *[Zestaw Azure SDK dla platformy .NET](/dotnet/azure/dotnet-tools)*.
* *Azure Active Directory aplikacji*. Jeśli nie masz aplikacji Azure Active Directory, zapoznaj się z sekcją [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) [: używanie portalu do tworzenia aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Skopiuj następujące wartości do użycia w kolejnych krokach: **Identyfikator aplikacji (klienta)**, **klucz uwierzytelniania** i **Identyfikator katalogu (dzierżawy)**. Przypisz aplikację do roli **współautor** , postępując zgodnie z instrukcjami w tym samym artykule.

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz możesz przygotować obiekt blob platformy Azure i Azure SQL Database dla tego samouczka, tworząc blog źródłowy i ujścia tabeli SQL.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

Najpierw utwórz źródłowy obiekt BLOB przez utworzenie kontenera i przekazanie wejściowego pliku tekstowego:

1. Otwórz Notatnik. Skopiuj poniższy tekst i Zapisz go lokalnie w pliku o nazwie *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Użyj narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , aby utworzyć kontener *adfv2tutorial* i przekazać plik *inputEmp.txt* do kontenera.

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

Następnie utwórz tabelę programu SQL dla ujścia:

1. Poniższy skrypt SQL umożliwia utworzenie tabeli *dbo.emp* w bazie danych Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Zezwól usługom platformy Azure na dostęp do SQL Database. Upewnij się, że zezwalasz na dostęp do usług platformy Azure na serwerze, aby usługa Data Factory mogła zapisywać dane w SQL Database. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

    1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać programem SQL Server. Wyszukaj i wybierz pozycję **serwery SQL**.

    2. Wybierz serwer.

    3. W obszarze menu programu SQL Server **Wybierz pozycję** **zapory i sieci wirtualne**.

    4. Na stronie **Zapora i sieci wirtualne** w obszarze **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** wybierz pozycję **włączone**.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Za pomocą programu Visual Studio Utwórz aplikację konsolową .NET C#.

1. Otwórz program Visual Studio.
2. W oknie **uruchamiania** wybierz pozycję **Utwórz nowy projekt**.
3. W oknie **Tworzenie nowego projektu** wybierz wersję języka C# **aplikacji konsolowej (.NET Framework)** z listy typów projektów. Następnie wybierz przycisk **Dalej**.
4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *ADFv2Tutorial*. W polu **Lokalizacja** przejdź do katalogu i/lub Utwórz katalog, w którym ma zostać zapisany projekt. Następnie wybierz pozycję **Utwórz**. Nowy projekt zostanie wyświetlony w środowisku IDE programu Visual Studio.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Następnie zainstaluj wymagane pakiety biblioteki przy użyciu Menedżera pakietów NuGet.

1. Na pasku menu wybierz kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **konsola Menedżera pakietów**.
2. W okienku **konsoli Menedżera pakietów** Uruchom następujące polecenia, aby zainstalować pakiety. Informacje o pakiecie NuGet Azure Data Factory można znaleźć w [witrynie Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

Wykonaj następujące kroki, aby utworzyć klienta fabryki danych.

1. Otwórz *program.cs*, a następnie Zastąp istniejące `using` instrukcje poniższym kodem, aby dodać odwołania do przestrzeni nazw.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Dodaj następujący kod do `Main` metody, która ustawia zmienne. Zastąp 14 symbole zastępcze własnymi wartościami.

    Aby wyświetlić listę regionów świadczenia usługi Azure, w których Data Factory są obecnie dostępne, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/). Z listy rozwijanej **produkty** wybierz pozycję **Przeglądaj**  >  **Analytics**  >  **Data Factory**. Następnie na liście rozwijanej **regiony** wybierz interesujące Cię regiony. Zostanie wyświetlona siatka ze stanem dostępności Data Factory produktów dla wybranych regionów.

    > [!NOTE]
    > Magazyny danych, takie jak usługa Azure Storage i Azure SQL Database, a także obliczenia, takie jak HDInsight, używane Data Factory mogą znajdować się w innych regionach niż wybrane do Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Dodaj następujący kod do `Main` metody, która tworzy wystąpienie `DataFactoryManagementClient` klasy. Ten obiekt jest używany do tworzenia fabryki danych, połączonej usługi, zestawów danych i potoku. Umożliwia on również monitorowanie szczegółów uruchomienia potoku.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Dodaj następujący kod do `Main` metody, która tworzy *fabrykę danych*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tym samouczku utworzysz odpowiednio dwie połączone usługi dla źródła i ujścia.

### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

Dodaj następujący kod do `Main` metody, która tworzy *połączoną usługę Azure Storage*. Aby uzyskać informacje o obsługiwanych właściwościach i szczegółach, zobacz [Właściwości połączonej usługi obiektów blob platformy Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database

Dodaj następujący kod do `Main` metody, która tworzy *Azure SQL Database połączoną usługę*. Aby uzyskać informacje o obsługiwanych właściwościach i szczegółach, zobacz [Azure SQL Database właściwości połączonej usługi](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej sekcji utworzysz dwa zestawy danych: jeden dla źródła, drugi dla ujścia.

### <a name="create-a-dataset-for-source-azure-blob"></a>Tworzenie zestawu danych źródłowego obiektu blob platformy Azure

Dodaj następujący kod do `Main` metody, która tworzy *zestaw danych obiektów blob platformy Azure*. Aby uzyskać informacje o obsługiwanych właściwościach i szczegółach, zobacz [Właściwości zestawu danych obiektów blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku i zawiera opis następujących elementów:

- Lokalizacja obiektu BLOB, z którego mają zostać skopiowane: `FolderPath` i `FileName`
- Format obiektu BLOB wskazujący, jak analizować zawartość: `TextFormat` i jej ustawienia, takie jak ogranicznik kolumny
- Struktura danych, w tym nazwy kolumn i typy danych, które mapują w tym przykładzie do tabeli SQL ujścia

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Tworzenie zestawu danych ujścia obiektu Blob platformy Azure

Dodaj następujący kod do `Main` metody, która tworzy *zestaw danych Azure SQL Database*. Aby uzyskać informacje o obsługiwanych właściwościach i szczegółach, zobacz [Azure SQL Database właściwości zestawu danych](connector-azure-sql-database.md#dataset-properties).

Zdefiniuj zestaw danych reprezentujący ujście danych w bazie danych Azure SQL Database. Ten zestaw danych odwołuje się do Azure SQL Database połączonej usługi utworzonej w poprzednim kroku. Określa on również tabelę SQL, która przechowuje skopiowane dane.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Dodaj następujący kod do `Main` metody, która tworzy *potok z działaniem kopiowania*. W tym samouczku ten potok zawiera jedno działanie: `CopyActivity` , które przyjmuje zestaw danych obiektów BLOB jako źródło i zestaw danych SQL jako ujścia. Informacje o szczegółach działania kopiowania znajdują się [w sekcji działanie kopiowania w Azure Data Factory](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj następujący kod do `Main` metody, która *wyzwala uruchomienie potoku*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

Teraz Wstaw kod, aby sprawdzić Stany przebiegu potoku i uzyskać szczegółowe informacje o przebiegu działania kopiowania.

1. Dodaj następujący kod do metody, `Main` aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Dodaj następujący kod do `Main` metody, która pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar danych odczytywanych lub zapisywana.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj aplikację, wybierając pozycję **Kompiluj**  >  **kompilację rozwiązania**. Następnie uruchom aplikację, wybierając **Debuguj**  >  **Rozpocznij debugowanie** i sprawdź wykonanie potoku.

Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i działania potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj, aż zobaczysz szczegóły uruchomienia działania kopiowania z rozmiarem odczytu/zapisu danych. Następnie przy użyciu narzędzi, takich jak SQL Server Management Studio (SSMS) lub Visual Studio, można nawiązać połączenie z Azure SQL Databaseą docelową i sprawdzić, czy określona tabela docelowa zawiera skopiowane dane.

### <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure Storage i Azure SQL Database.
> * Tworzenie zestawów danych obiektu Blob platformy Azure i bazy danych Azure SQL Database.
> * Utwórz potok zawierający działanie kopiowania.
> * Uruchom potok.
> * Monitorowanie uruchomień potoku i działań.

Przejdź do następującego samouczka, aby dowiedzieć się więcej o kopiowaniu danych lokalnych do chmury:

> [!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-powershell.md)
