---
title: Zestaw SDK platformy .NET dla Azure Stream Analytics
description: Wprowadzenie do zestawu SDK platformy .NET do zarządzania Stream Analytics. Informacje na temat konfigurowania i uruchamiania zadań analitycznych. Utwórz projekt, dane wejściowe, wyjścia i przekształcenia.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232889"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Zestaw SDK zarządzania .NET: Konfigurowanie i uruchamianie zadań analitycznych za pomocą interfejsu API Azure Stream Analytics dla platformy .NET
Informacje na temat konfigurowania i uruchamiania zadań analitycznych przy użyciu interfejsu API Stream Analytics dla platformy .NET przy użyciu zestawu .NET SDK zarządzania. Skonfiguruj projekt, twórz źródła danych wejściowych i wyjściowych, przekształcenia oraz zadania uruchamiania i zatrzymywania. W przypadku zadań analitycznych można przesyłać strumieniowo dane z magazynu obiektów blob lub z centrum zdarzeń.

Zapoznaj się z dokumentacją dotyczącą [zarządzania Stream Analytics interfejsem API dla platformy .NET](/previous-versions/azure/dn889315(v=azure.100)).

Azure Stream Analytics to w pełni zarządzana usługa, która zapewnia duże, wysoce dostępne, skalowalne, skomplikowane przetwarzanie zdarzeń na danych przesyłanych strumieniowo w chmurze. Stream Analytics umożliwia klientom Konfigurowanie zadań przesyłania strumieniowego w celu analizowania strumieni danych i pozwala im na korzystanie z analiz niemal w czasie rzeczywistym.  

> [!NOTE]
> Zaktualizowaliśmy przykładowy kod w tym artykule przy użyciu programu Azure Stream Analytics Management SDK w wersji 2. x. Aby zapoznać się z przykładowym kodem korzystającym z wersji zestawu SDK lagecy (1. x), zobacz [Korzystanie z zestawu SDK platformy .NET v1. x dla Stream Analytics]().

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tym artykułem należy spełnić następujące wymagania:

* Zainstaluj program Visual Studio 2019 lub 2015.
* Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniższy przykład to przykładowy skrypt Azure PowerShell. Aby uzyskać Azure PowerShell informacji, zobacz [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Skonfiguruj źródło danych wejściowych i docelowy wynik dla zadania, z którym ma zostać nawiązane połączenie.

## <a name="set-up-a-project"></a>Konfigurowanie projektu
Aby utworzyć zadanie analizy, użyj interfejsu API Stream Analytics dla platformy .NET, a następnie skonfiguruj projekt.

1. Utwórz aplikację konsolową .NET programu Visual Studio w języku C#.
2. W konsoli Menedżera pakietów Uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwszym z nich jest zestaw SDK platformy .NET do zarządzania Azure Stream Analytics. Druga z nich dotyczy uwierzytelniania klientów platformy Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Dodaj następującą sekcję **AppSettings** do pliku App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Zastąp wartości identyfikatorów **subskrypcji i** **ActiveDirectoryTenantId** identyfikatorem dzierżawy platformy Azure. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Dodaj następujące odwołanie w pliku. csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Dodaj następujące instrukcje **using** do pliku źródłowego (program. cs) w projekcie:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Dodaj metodę pomocnika uwierzytelniania:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Tworzenie klienta zarządzania Stream Analytics
Obiekt **StreamAnalyticsManagementClient** umożliwia zarządzanie zadaniem i składnikami zadania, takimi jak dane wejściowe, wyjściowe i transformacje.

Dodaj następujący kod na początku metody **Main** :

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

Wartość zmiennej **resourceGroupName** powinna być taka sama jak nazwa grupy zasobów utworzonej lub pobrana w ramach kroków wymagań wstępnych.

Aby zautomatyzować element prezentacji poświadczeń tworzenia zadania, zapoznaj się z tematem [uwierzytelnianie jednostki usługi za pomocą Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

W pozostałych sekcjach tego artykułu przyjęto założenie, że ten kod jest na początku metody **Main** .

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Poniższy kod tworzy zadanie Stream Analytics w ramach zdefiniowanej grupy zasobów. Później dodasz dane wejściowe, wyjściowe i przekształcenia do zadania.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Tworzenie źródła danych wejściowych Stream Analytics
Poniższy kod tworzy Stream Analytics źródło danych wejściowych z typem źródła danych wejściowych obiektu BLOB i serializacją woluminu CSV. Aby utworzyć źródło danych wejściowych centrum zdarzeń, użyj **EventHubStreamInputDataSource** zamiast **BlobStreamInputDataSource**. Analogicznie, można dostosować typ serializacji źródła danych wejściowych.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Źródła danych wejściowych, zarówno z magazynu obiektów blob, jak i centrum zdarzeń, są powiązane z konkretnym zadaniem. Aby użyć tego samego źródła danych wejściowych dla różnych zadań, należy wywołać metodę ponownie i określić inną nazwę zadania.

## <a name="test-a-stream-analytics-input-source"></a>Testowanie źródła danych wejściowych Stream Analytics
Metoda **TestConnection** testuje, czy zadanie Stream Analytics może nawiązać połączenie ze źródłem danych wejściowych, jak również z innymi aspektami specyficznymi dla typu źródła danych wejściowych. Na przykład w źródle danych wejściowych obiektu BLOB utworzonym w poprzednim kroku metoda sprawdzi, czy nazwa konta magazynu i para kluczy można użyć do nawiązania połączenia z kontem magazynu, a także sprawdzić, czy określony kontener istnieje.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
Wynik wywołania TestConnection to obiekt *ResourceTestResult* , który zawiera dwie właściwości:

- *stan*: może to być jeden z następujących ciągów: ["TestNotAttempted", "TestSucceeded", "TestFailed"]
- *błąd*: jest to typ zwrócono zawierający następujące właściwości:
   - *kod*: wymagana właściwość typu String. Wartość to standardowy system .NET. HttpStatusCode otrzymany podczas testowania.
   - *komunikat*: wymagana właściwość typu String reprezentująca błąd. 

## <a name="create-a-stream-analytics-output-target"></a>Tworzenie elementu docelowego danych wyjściowych Stream Analytics
Tworzenie elementu docelowego danych wyjściowych jest podobne do tworzenia źródła danych wejściowych Stream Analytics. Podobnie jak źródła wejściowe, cele wyjściowe są powiązane z konkretnym zadaniem. Aby użyć tego samego elementu docelowego danych wyjściowych dla różnych zadań, należy wywołać metodę ponownie i określić inną nazwę zadania.

Poniższy kod tworzy miejsce docelowe danych wyjściowych (Azure SQL Database). Można dostosować typ danych docelowego wyjścia i/lub typ serializacji.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testowanie elementu docelowego danych wyjściowych Stream Analytics
Element docelowy Stream Analytics wyjściowego również ma metodę **TestConnection** do testowania połączeń.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Tworzenie transformacji Stream Analytics
Poniższy kod tworzy transformację Stream Analytics przy użyciu zapytania "select * from Input" i określa, aby przydzielić jedną jednostkę przesyłania strumieniowego dla zadania Stream Analytics. Aby uzyskać więcej informacji na temat dostosowywania jednostek przesyłania strumieniowego, zobacz [skalowanie Azure Stream Analytics zadań](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Podobnie jak dane wejściowe i wyjściowe, transformacja jest również związana z konkretnym zadaniem Stream Analytics, w którym zostało utworzone.

## <a name="start-a-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics
Po utworzeniu zadania Stream Analytics i jego danych wejściowych, danych wyjściowych i transformacji można uruchomić zadanie, wywołując metodę **startową** .

Następujący przykładowy kod uruchamia zadanie Stream Analytics z niestandardowym czasem rozpoczęcia wyjściowego ustawionym na 12 grudnia 2012, 12:12:12 czasu UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zatrzymywanie zadania Stream Analytics
Można zatrzymać uruchomione zadanie Stream Analytics, wywołując metodę **stop** .

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Usuwanie zadania Stream Analytics
Metoda **delete** usunie zadanie, a także bazowe zasoby podrzędne, w tym dane wejściowe, dane wyjściowe i transformację zadania.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki
Znasz podstawowe informacje na temat używania zestawu SDK platformy .NET do tworzenia i uruchamiania zadań analitycznych. Więcej informacji można znaleźć w następujących artykułach:

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics zarządzania .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
