---
title: Rozgałęzianie w potoku Azure Data Factory
description: W tym artykule przedstawiono sposób sterowania przepływem danych w usłudze Azure Data Factory przez rozgałęzianie działań i tworzenie łańcuchów działań.
services: data-factory
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: ab7d17ee61d733483b6d3573e9bd69b1628c7940
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496946"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Rozgałęzianie działań i tworzenie łańcuchów działań w potoku usługi Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku utworzysz potok Data Factory, który zawiera niektóre funkcje przepływu sterowania. Ten potok kopiuje z kontenera na platformie Azure Blob Storage do innego kontenera na tym samym koncie magazynu. Jeśli działanie kopiowania zakończy się powodzeniem, potok wyśle szczegóły pomyślnej operacji kopiowania w wiadomości e-mail. Te informacje mogą obejmować ilość zapisanych danych. Jeśli działanie kopiowania zakończy się niepowodzeniem, w wiadomości e-mail zostanie wysłane szczegóły błędu kopiowania, na przykład komunikat o błędzie. W samouczku pokazano, jak przekazać parametry.

Ta ilustracja zawiera przegląd scenariusza:

![Na diagramie przedstawiono platformę Azure Blob Storage, która jest celem kopii, która po powodzeniu wysyła wiadomość e-mail ze szczegółami lub, w przypadku niepowodzenia, wysyła wiadomość e-mail z informacjami o błędzie.](media/tutorial-control-flow/overview.png)

W tym samouczku przedstawiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie fabryki danych
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Używanie przekazywania parametrów i zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

W tym samouczku jest używany zestaw SDK platformy .NET. Można użyć innych mechanizmów do współpracy z Azure Data Factory. Aby uzyskać Data Factory przewodników Szybki Start, zobacz [5-minutowe Przewodniki Szybki Start](./quickstart-create-data-factory-portal.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto usługi Azure Storage. Magazyn obiektów BLOB jest używany jako magazyn danych źródłowych. Jeśli nie masz konta usługi Azure Storage, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* Eksplorator usługi Azure Storage. Aby zainstalować to narzędzie, zobacz [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
* Azure SQL Database. Baza danych jest używana jako magazyn danych będący ujściem. Jeśli nie masz bazy danych w Azure SQL Database, zapoznaj się z tematem [Tworzenie bazy danych w programie Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Programu Visual Studio. W tym artykule jest wykorzystywany program Visual Studio 2019.
* Zestaw Azure .NET SDK. Pobierz i zainstaluj [zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/).

Listę regionów świadczenia usługi Azure, w których Data Factory są obecnie dostępne, można znaleźć w temacie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych i obliczenia mogą znajdować się w innych regionach. Magazyny obejmują usługę Azure Storage i Azure SQL Database. Obliczenia obejmują HDInsight, który Data Factory używany.

Utwórz aplikację zgodnie z opisem w temacie [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Przypisz aplikację do roli **współautor** , wykonując instrukcje opisane w tym samym artykule. Potrzebujesz kilku wartości dla nowszych części tego samouczka, takich jak identyfikator **aplikacji (klienta)** i **Identyfikator katalogu (dzierżawy)**.

### <a name="create-a-blob-table"></a>Tworzenie tabeli obiektów BLOB

1. Otwórz edytor tekstów. Skopiuj poniższy tekst i Zapisz go lokalnie jako *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Otwórz Eksplorator usługi Azure Storage. Rozwiń konto magazynu. Kliknij prawym przyciskiem myszy pozycję **Blob Containers** (Kontenery obiektów blob) i wybierz polecenie **Create Blob Container** (Utwórz kontener obiektów blob).
1. Nazwij nowy kontener *adfv2branch* i wybierz pozycję **Przekaż** , aby dodać plik *input.txt* do kontenera.

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio<a name="create-visual-studio-project"></a>

Tworzenie aplikacji konsolowej .NET w języku C#:

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C# i wybierz pozycję **dalej**.
1. Nazwij projekt *ADFv2BranchTutorial*.
1. Wybierz pozycję **.NET w wersji 4.5.2** lub nowszej, a następnie wybierz pozycję **Utwórz**.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.
1. W oknie **Konsola menedżera pakietów** uruchom następujące polecenia, aby zainstalować pakiety. Aby uzyskać szczegółowe informacje, zapoznaj się z [pakietem NuGet Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) .

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

1. Otwórz *program.cs* i Dodaj następujące instrukcje:

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

1. Dodaj te zmienne statyczne do `Program` klasy. Zastąp symbole zastępcze własnymi wartościami.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Dodaj następujący kod do metody `Main`: Ten kod tworzy wystąpienie `DataFactoryManagementClient` klasy. Następnie użyjesz tego obiektu, aby utworzyć fabrykę danych, połączoną usługę, zestawy danych i potok. Można również użyć tego obiektu do monitorowania szczegółów uruchomienia potoku.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Dodaj `CreateOrUpdateDataFactory` metodę do pliku *program.cs* :

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Dodaj następujący wiersz do `Main` metody, która tworzy fabrykę danych:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

1. Dodaj `StorageLinkedServiceDefinition` metodę do pliku *program.cs* :

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Dodaj następujący wiersz do `Main` metody, która tworzy połączoną usługę Azure Storage:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Aby uzyskać więcej informacji na temat obsługiwanych właściwości i szczegółów, zobacz [Właściwości połączonej usługi](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej sekcji utworzysz dwa zestawy danych, jeden dla źródła i jeden dla ujścia.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Tworzenie zestawu danych dla źródłowego obiektu blob platformy Azure

Dodaj metodę, która tworzy *zestaw danych obiektów blob platformy Azure*. Aby uzyskać więcej informacji o obsługiwanych właściwościach i szczegółach, zobacz [Właściwości zestawu danych obiektów blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Dodaj `SourceBlobDatasetDefinition` metodę do pliku *program.cs* :

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu BLOB odwołuje się do połączonej usługi Azure Storage obsługiwanej w poprzednim kroku. Zestaw danych obiektów BLOB opisuje lokalizację obiektu BLOB do skopiowania: *FolderPath* i *filename*.

Zwróć uwagę na użycie parametrów dla *FolderPath*. `sourceBlobContainer` jest nazwą parametru, a wyrażenie jest zamieniane na wartości przesłane w przebiegu potoku. Składnia umożliwiająca zdefiniowanie parametrów: `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Tworzenie zestawu danych dla ujścia obiektu blob platformy Azure

1. Dodaj `SourceBlobDatasetDefinition` metodę do pliku *program.cs* :

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Dodaj następujący kod do `Main` metody, która tworzy zarówno zestaw danych źródłowych, jak i ujścia obiektów blob platformy Azure.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Tworzenie klasy języka C#: EmailRequest

W projekcie w języku C# Utwórz klasę o nazwie `EmailRequest` . Ta klasa definiuje właściwości, które potok wysyła w żądaniu treści podczas wysyłania wiadomości e-mail. W tym samouczku potok wysyła cztery właściwości z potoku do wiadomości e-mail:

* Komunikat. Treść wiadomości e-mail. W przypadku pomyślnego kopiowania ta właściwość zawiera ilość zapisanych danych. W przypadku kopii zakończonej niepowodzeniem ta właściwość zawiera szczegóły błędu.
* Nazwa fabryki danych. Nazwa fabryki danych.
* Nazwa potoku. Nazwa potoku.
* Nadajnik. Parametr, który przechodzi przez. Ta właściwość określa odbiorcę wiadomości e-mail.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Tworzenie punktów końcowych przepływu pracy poczty e-mail

Aby wyzwolić wysyłanie wiadomości e-mail, zdefiniuj przepływ pracy przy użyciu usługi [Logic Apps](../logic-apps/logic-apps-overview.md). Aby uzyskać szczegółowe informacje na temat tworzenia przepływu pracy Logic Apps, zobacz [jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o powodzeniu

W [Azure Portal](https://portal.azure.com)Utwórz przepływ pracy Logic Apps o nazwie *CopySuccessEmail*. Zdefiniuj wyzwalacz przepływu pracy jako `When an HTTP request is received` . W wyzwalaczu żądania wypełnij pole `Request Body JSON Schema` przy użyciu następującego kodu JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Przepływ pracy wygląda podobnie do poniższego przykładu:

![Przepływ pracy wiadomości e-mail z informacją o powodzeniu](media/tutorial-control-flow/success-email-workflow-trigger.png)

Zawartość JSON jest wyrównywana z `EmailRequest` klasą utworzoną w poprzedniej sekcji.

Dodaj akcję `Office 365 Outlook – Send an email` . W przypadku akcji **Wyślij wiadomość e-mail** Dostosuj sposób formatowania wiadomości e-mail przy użyciu właściwości przekazaną w schemacie JSON **treści** żądania. Oto przykład:

![Projektant aplikacji logiki — wysyłanie akcji poczty e-mail](media/tutorial-control-flow/customize-send-email-action.png)

Po zapisaniu przepływu pracy skopiuj i Zapisz wartość **adresu URL post protokołu HTTP** z wyzwalacza.

## <a name="fail-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o niepowodzeniu

Klonuj **CopySuccessEmail** jako inny przepływ pracy Logic Apps o nazwie *nazwie copyfailemail*. W wyzwalaczu żądania element `Request Body JSON schema` jest taki sam. Zmień format wiadomości e-mail, na przykład element `Subject`, aby przekształcić wiadomość e-mail w wiadomość z informacją o niepowodzeniu. Oto przykład:

![Projektant aplikacji logiki — przepływ pracy poczty e-mail zakończony niepowodzeniem](media/tutorial-control-flow/fail-email-workflow.png)

Po zapisaniu przepływu pracy skopiuj i Zapisz wartość **adresu URL post protokołu HTTP** z wyzwalacza.

Teraz powinny istnieć dwa adresy URL przepływów pracy, takie jak następujące przykłady:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Wróć do projektu w programie Visual Studio. Teraz dodamy kod, który tworzy potok z działaniem kopiowania i `DependsOn` właściwością. W tym samouczku potok zawiera jedno działanie, działanie kopiowania, które przyjmuje zestaw danych obiektów BLOB jako źródło i inny zestaw danych obiektu BLOB jako ujścia. Jeśli działanie kopiowania zakończy się powodzeniem lub zakończy się niepowodzeniem, program wywołuje inne zadania poczty e-mail.

W tym potoku użyto następujących funkcji:

* Parametry
* Aktywność sieci Web
* Zależność działania
* Używanie danych wyjściowych z działania jako dane wejściowe do innego działania

1. Dodaj tę metodę do projektu. Poniższe sekcje zawierają więcej szczegółów.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Dodaj następujący wiersz do `Main` metody, która tworzy potok:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametry

Pierwsza sekcja naszego kodu potoku definiuje parametry.

* `sourceBlobContainer`. Źródłowy zestaw danych obiektu BLOB wykorzystuje ten parametr w potoku.
* `sinkBlobContainer`. Zestaw danych obiektu BLOB ujścia wykorzystuje ten parametr w potoku.
* `receiver`. Dwa działania sieci Web w potoku, które wysyłają wiadomości e-mail o powodzeniu lub niepowodzeniu do odbiorcy używają tego parametru.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Aktywność sieci Web

Działanie sieci Web umożliwia wywołanie dowolnego punktu końcowego REST. Aby uzyskać więcej informacji na temat działania, zobacz [aktywność sieci Web w Azure Data Factory](control-flow-web-activity.md). Ten potok używa działania sieci Web do wywołania przepływu pracy Logic Apps poczty e-mail. Tworzysz dwa działania sieci Web: jeden, który wywołuje `CopySuccessEmail` przepływ pracy i jeden wywołuje `CopyFailWorkFlow` .

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

W `Url` Właściwości wklej punkty końcowe **adresu URL post protokołu HTTP** z przepływów pracy Logic Apps. We `Body` właściwości należy przekazać wystąpienie `EmailRequest` klasy. Żądanie wiadomości e-mail zawiera następujące właściwości:

* Komunikat. Przekazuje wartość `@{activity('CopyBlobtoBlob').output.dataWritten` . Uzyskuje dostęp do właściwości poprzedniego działania kopiowania i przekazuje wartość `dataWritten` . W przypadku wiadomości dotyczącej niepowodzenia przekaż dane wyjściowe błędu zamiast elementu `@{activity('CopyBlobtoBlob').error.message`.
* Nazwa Data Factory. Przekazuje wartość `@{pipeline().DataFactory}` tej zmiennej systemowej, pozwala uzyskać dostęp do odpowiedniej nazwy fabryki danych. Aby uzyskać listę zmiennych systemowych, zobacz [zmienne systemowe](control-flow-system-variables.md).
* Nazwa potoku. Przekazuje wartość `@{pipeline().Pipeline}` . Ta zmienna systemowa umożliwia dostęp do odpowiedniej nazwy potoku.
* Nadajnik. Przekazuje wartość `"@pipeline().parameters.receiver"` . Uzyskuje dostęp do parametrów potoku.

Ten kod tworzy nową zależność działania, która zależy od poprzedniego działania kopiowania.

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj następujący kod do `Main` metody, która wyzwala uruchomienie potoku.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Klasa metody Main

Ostatnia `Main` Metoda powinna wyglądać następująco.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Skompiluj i uruchom program, aby wyzwolić uruchomienie potoku.

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

1. Dodaj następujący kod do metody `Main`:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Ten kod stale sprawdza stan uruchomienia do momentu zakończenia kopiowania danych.

1. Dodaj następujący kod do `Main` metody, która pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytu/zapisu danych:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Aplikacja wyświetla postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie użyj narzędzi, takich jak Eksplorator usługi Azure Storage, aby sprawdzić, czy obiekt BLOB został skopiowany do *outputBlobPath* z *inputBlobPath* , jak określono w zmiennych.

Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=**_"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące zadania:

> [!div class="checklist"]
> _ Tworzenie fabryki danych
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Używanie przekazywania parametrów i zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

Teraz możesz przejść do sekcji pojęcia, aby uzyskać więcej informacji na temat Azure Data Factory.
> [!div class="nextstepaction"]
>[Potoki i działania](concepts-pipelines-activities.md)