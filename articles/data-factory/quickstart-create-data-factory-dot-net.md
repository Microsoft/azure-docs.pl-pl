---
title: Tworzenie Azure Data Factory przy użyciu zestawu SDK platformy .NET
description: Utwórz Azure Data Factory i potok przy użyciu zestawu .NET SDK, aby skopiować dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: d564b96807574dd7a275d6959aea085ad16e9e2e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565946"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Szybki start: Tworzenie fabryki danych i potoku przy użyciu zestawu SDK .NET

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku szybki start opisano sposób tworzenia Azure Data Factory przy użyciu zestawu SDK platformy .NET. Potok tworzony w tej fabryce danych **kopiuje** dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym **przekształcania** danych przy użyciu Azure Data Factory, zobacz [Samouczek: Przekształcanie danych przy użyciu platformy Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

W przewodniku w tym artykule jest wykorzystywany program Visual Studio 2019. Procedury dotyczące Visual Studio 2013, 2015 lub 2017 różnią się nieznacznie.

### <a name="azure-net-sdk"></a>Zestaw Azure .NET SDK

Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/) na maszynie.

## <a name="create-an-application-in-azure-active-directory"></a>Utworzenie aplikacji w usłudze Azure Active Directory

W sekcjach w sekcji *jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów* , postępuj zgodnie z instrukcjami, aby wykonać następujące zadania:

1. W obszarze [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)Utwórz aplikację reprezentującą aplikację platformy .NET, którą tworzysz w tym samouczku. W przypadku adresu URL logowania możesz podać fikcyjny adres URL, jak pokazano w artykule (`https://contoso.org/exampleapp`).
2. W polu [Pobierz wartości do logowania](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Pobierz **Identyfikator aplikacji** i **Identyfikator dzierżawy** , a następnie zanotuj te wartości, które są używane w dalszej części tego samouczka. 
3. W obszarze [Certyfikaty i wpisy tajne](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)Pobierz **klucz uwierzytelniania** i zanotuj tę wartość, która jest używana w dalszej części tego samouczka.
4. W polu [Przypisz aplikację do roli](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) **współautor** na poziomie subskrypcji, aby aplikacja mogła tworzyć fabryki danych w subskrypcji.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Następnie Utwórz aplikację konsolową .NET C# w programie Visual Studio:

1. Uruchom **program Visual Studio**.
2. W oknie uruchamiania wybierz pozycję **Utwórz nową**  >  **aplikację konsolową projektu (.NET Framework)**. Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
3. W polu **Nazwa projektu** wprowadź **ADFv2QuickStart**.
4. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.
2. W okienku **konsoli Menedżera pakietów** Uruchom następujące polecenia, aby zainstalować pakiety. Aby uzyskać więcej informacji, zobacz [pakiet NuGet Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

1. Otwórz plik **Program.cs** , a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Dodaj do metody **Main** następujący kod, który określa zmienne. Zastąp symbole zastępcze własnymi wartościami. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza** , aby zlokalizować pozycję **Data Factory** : [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database i inne) oraz obliczenia (HDInsight i inne) używane przez fabrykę danych mogą znajdować się w innych regionach.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Dodaj do metody **Main** następujący kod, który tworzy wystąpienie klasy **DataFactoryManagementClient** . Ten obiekt jest używany do tworzenia fabryki danych, połączonej usługi, zestawów danych i potoku. Umożliwia on również monitorowanie szczegółów uruchomienia potoku.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Dodaj do metody **Main** poniższy kod, który tworzy **fabrykę danych**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej

Dodaj do metody **Main** poniższy kod, który tworzy **połączoną usługę Azure Storage**.

Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym przewodniku szybki start musisz utworzyć tylko jedną połączoną usługę Azure Storage dla źródła kopii i magazynu ujścia. jest on nazywany "AzureStorageLinkedService" w przykładzie.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Utwórz zestaw danych

Dodaj do metody **Main** następujący kod, który tworzy **zestaw danych obiektów blob platformy Azure**.

Zdefiniuj zestaw danych, który reprezentuje dane do skopiowania ze źródła do ujścia. W tym przykładzie ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku. Zestaw danych przyjmuje parametr, którego wartość jest ustawiana w działaniu wykorzystującym zestaw danych. Parametr służy do konstruowania "folderPath" wskazujący, gdzie znajdują się dane/są przechowywane.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Dodaj do metody **Main** poniższy kod, który tworzy **potok z działaniem kopiowania**.

W tym przykładzie ten potok zawiera jedno działanie i przyjmuje dwa parametry: wejściowy ścieżkę obiektu BLOB i ścieżkę wyjściowego obiektu BLOB. Wartości tych parametrów są ustawiane w chwili wyzwolenia/uruchomienia potoku. Działanie kopiowania dotyczy tego samego zestawu danych obiektu blob, który został utworzony w poprzednim kroku jako wejście i wyjście. W przypadku użycia zestawu danych jako zestawu danych wejściowych określana jest ścieżka wejściowa. Natomiast w przypadku użycia zestawu danych jako zestawu danych wyjściowych określana jest ścieżka wyjściowa. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj do metody **Main** poniższy kod, który **wyzwala uruchomienie potoku**.

Ten kod ustawia również wartości parametrów **inputPath** i **outputPath** określonych w potoku za pomocą rzeczywistych wartości ścieżek źródła i ujścia obiektu BLOB.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

1. Dodaj do metody **Main** poniższy kod, aby stale sprawdzać stan do momentu zakończenia kopiowania danych.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Dodaj do metody **Main** następujący kod, który pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar danych odczytywanych lub zapisywana.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj, aż zobaczysz szczegóły uruchomienia działania kopiowania z rozmiarem danych odczytu/zapisu. Następnie użyj narzędzi, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , aby sprawdzić, czy obiekty blob zostały skopiowane do "outputBlobPath" z "inputBlobPath", jak określono w zmiennych.

### <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

Potok automatycznie tworzy folder wyjściowy w kontenerze obiektów BLOB **adftutorial** . Następnie kopiuje plik **emp.txt** z folderu input do folderu Output. 

1. W Azure Portal na stronie kontenera **adftutorial** , która została zatrzymana w sekcji [Dodaj folder wejściowy i plik dla kontenera obiektów BLOB](#add-an-input-folder-and-file-for-the-blob-container) powyżej, wybierz pozycję **Odśwież** , aby wyświetlić folder danych wyjściowych. 
2. Na liście folder wybierz pozycję **dane wyjściowe**.
3. Upewnij się, że plik **emp.txt** jest kopiowany do folderu wyjściowego. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby programowo usunąć fabrykę danych, Dodaj następujące wiersze kodu do programu: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 
