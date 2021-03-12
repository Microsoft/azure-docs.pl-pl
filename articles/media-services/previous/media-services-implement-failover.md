---
title: Implementowanie przesyłania strumieniowego trybu failover za pomocą Azure Media Services | Microsoft Docs
description: W tym artykule pokazano, jak zaimplementować scenariusz przesyłania strumieniowego trybu failover za pomocą Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1636e49099851337d82494ebe168b8ec5194fa20
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011939"
---
# <a name="implement-failover-streaming-with-media-services-v2"></a>Implementowanie przesyłania strumieniowego trybu failover z Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym instruktażu przedstawiono sposób kopiowania zawartości (obiektów BLOB) z jednego zasobu do drugiego w celu obsługi nadmiarowości na potrzeby przesyłania strumieniowego na żądanie. Ten scenariusz jest przydatny, jeśli chcesz skonfigurować usługę Azure Content Delivery Network w celu przełączenia w tryb failover między dwoma centrami danych, w przypadku awarii w jednym centrum danych. W tym instruktażu jest używany zestaw SDK Azure Media Services, interfejs API REST Azure Media Services i zestaw SDK usługi Azure Storage w celu przedstawienia następujących zadań:

1. Skonfiguruj konto Media Services w "centrum danych A".
2. Przekaż plik Mezzanine do źródłowego zasobu.
3. Zakoduj element zawartości w plikach MP4 z szybkością wiele bitów. 
4. Utwórz lokalizator sygnatury dostępu współdzielonego tylko do odczytu. Jest to dla zasobu źródłowego, aby miał dostęp do odczytu do kontenera na koncie magazynu skojarzonym z zasobem źródłowym.
5. Pobierz nazwę kontenera zasobu źródłowego z lokalizatora sygnatury dostępu współdzielonego, który został utworzony w poprzednim kroku. Jest to niezbędne do kopiowania obiektów BLOB między kontami magazynu (wyjaśniono w dalszej części tematu).
6. Utwórz lokalizator pochodzenia dla elementu zawartości, który został utworzony przez zadanie kodowania. 

Następnie, aby obsłużyć tryb failover:

1. Skonfiguruj konto Media Services w "centrum danych B".
2. Utwórz docelowy pusty element zawartości na docelowym koncie Media Services.
3. Utwórz lokalizator sygnatury dostępu współdzielonego. Jest to miejsce docelowe pustego elementu zawartości, aby miało dostęp do zapisu w kontenerze na docelowym koncie magazynu skojarzonym z zasobem docelowym.
4. Użyj zestawu SDK usługi Azure Storage, aby skopiować obiekty blob (pliki zasobów) między źródłowym kontem magazynu w pozycji "centrum danych A" i docelowe konto magazynu w "centrum danych B". Te konta magazynu są skojarzone z interesującymi zasobami.
5. Skojarz obiekty blob (pliki zasobów), które zostały skopiowane do docelowego kontenera obiektów blob z elementem zawartości docelowej. 
6. Utwórz lokalizator pochodzenia dla zasobu w "centrum danych B" i określ identyfikator lokalizatora, który został wygenerowany dla zasobu w "centrum danych A".

Zapewnia to adresy URL przesyłania strumieniowego, w przypadku których ścieżki względne adresów URL są takie same (tylko podstawowe adresy URL różnią się). 

Następnie, aby obsłużyć wszelkie przerwy, można utworzyć Content Delivery Network na początku tych lokalizatorów. 

Obowiązują następujące zastrzeżenia:

* Bieżąca wersja zestawu SDK Media Services nie obsługuje programistycznego generowania informacji IAssetFile, które spowodują skojarzenie elementu zawartości z plikami zasobów. Zamiast tego należy użyć interfejsu API REST CreateFileInfos Media Services. 
* Zasoby zaszyfrowane magazynu (AssetCreationOptions. StorageEncrypted) nie są obsługiwane w przypadku replikacji (ponieważ klucz szyfrowania jest inny w przypadku kont Media Services). 
* Jeśli chcesz korzystać z funkcji dynamicznego tworzenia pakietów, upewnij się, że punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość, jest w stanie **uruchomienia** .

## <a name="prerequisites"></a>Wymagania wstępne

* Dwa konta Media Services w nowej lub istniejącej subskrypcji platformy Azure. Zobacz, [jak utworzyć konto Media Services](media-services-portal-create-account.md).
* System operacyjny: Windows 7, Windows 2008 R2 lub Windows 8.
* .NET Framework 4,5 lub .NET Framework 4.
* Visual Studio 2010 z dodatkiem SP1 lub nowszą wersją (Professional, Premium, Ultimate lub Express).

## <a name="set-up-your-project"></a>konfigurowanie projektu

W tej sekcji utworzysz i skonfigurujesz projekt aplikacji konsolowej C#.

1. Użyj programu Visual Studio, aby utworzyć nowe rozwiązanie, które zawiera projekt aplikacji konsolowej C#. Wprowadź **HandleRedundancyForOnDemandStreaming** jako nazwę, a następnie kliknij przycisk **OK**.
2. Utwórz folder **SupportFiles** na tym samym poziomie co plik projektu **HandleRedundancyForOnDemandStreaming. csproj** . W folderze **SupportFiles** Utwórz foldery **OutputFiles** i **MP4Files** . Skopiuj plik MP4 do folderu **MP4Files** . (W tym przykładzie używany jest plik **ignite.mp4** ). 
3. Użyj narzędzia **NuGet** , aby dodać odwołania do bibliotek DLL związanych z Media Services. W **menu głównym programu Visual Studio** wybierz kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **konsola Menedżera pakietów**. W oknie konsoli wpisz **install-package windowsazure. MediaServices**, a następnie naciśnij klawisz ENTER.
4. Dodaj inne odwołania, które są wymagane dla tego projektu: System. Runtime. Serialization i system. Web.
5. Zastąp instrukcje **using** , które zostały dodane do pliku **Programs.cs** domyślnie przy użyciu następujących elementów:

```csharp
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Runtime.Serialization.Json;
```

## <a name="add-code-that-handles-redundancy-for-on-demand-streaming"></a>Dodaj kod, który obsługuje nadmiarowość na potrzeby przesyłania strumieniowego na żądanie

W tej sekcji utworzysz możliwość obsługi nadmiarowości.

1. Dodaj następujące pola poziomu klasy do klasy program.

    ```csharp
    private static readonly string storageNameTarget = "amsstorageacct2";
    private static readonly string storageKeyTarget = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";
    private static readonly string storageNameSource = "amsstorageacct1";
    private static readonly string storageKeySource = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";

    private static readonly string sourceApiServer = "https://amsacct1.restv2.westus2-2.media.azure.net/api/";
    private static readonly string targetApiServer = "https://amsacct2.restv2.eastus.media.azure.net/api/";
    private static string tokenSPSource = null;
    private static string tokenSPTarget = null;

    private static CloudMediaContext contextSource = null;
    private static CloudMediaContext contextTarget = null;

    // Base support files path.  Update this field to point to the base path  
    // for the local support files folder that you create. 
    private static readonly string SupportFiles = Path.GetFullPath(@"../..\SupportFiles");

    // Paths to support files (within the above base path). 
    private static readonly string SingleInputMp4Path = Path.GetFullPath(SupportFiles + @"\MP4Files\ignite.mp4");
    private static readonly string OutputFilesFolder = Path.GetFullPath(SupportFiles + @"\OutputFiles");
    ```
2. Zastąp domyślną definicję metody głównej następującym. Definicje metod, które są wywoływane z poziomu głównego, są zdefiniowane poniżej.
        
    ```csharp
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentialsForAcct1 =
         new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        AzureAdTokenCredentials tokenCredentialsForAcct2 =
        new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        var tokenProviderSource = new AzureAdTokenProvider(tokenCredentialsForAcct1);
        var tokenProviderTarget = new AzureAdTokenProvider(tokenCredentialsForAcct2);
        try
        {
        tokenSPSource = tokenProviderSource.GetAccessToken().Item1.ToString();
        tokenSPTarget = tokenProviderTarget.GetAccessToken().Item1.ToString();

        contextSource = new CloudMediaContext(new Uri(sourceApiServer), tokenProviderSource);
        contextTarget = new CloudMediaContext(new Uri(targetApiServer), tokenProviderTarget);


        IAsset assetSingleFile = CreateAssetAndUploadSingleFile(contextSource,
                         AssetCreationOptions.None,
                         SingleInputMp4Path);

         IJob job = CreateEncodingJob(contextSource, assetSingleFile);

        if (job.State != JobState.Error)
        {
            IAsset sourceOutputAsset = job.OutputMediaAssets[0];
            // Get the locator for Smooth Streaming
            var sourceOriginLocator = GetStreamingOriginLocator(contextSource, sourceOutputAsset);

            Console.WriteLine("Locator Id: {0}", sourceOriginLocator.Id);

            // 1.Create a read-only SAS locator for the source asset to have read access to the container in the source Storage account (associated with the source Media Services account)
            var readSasLocator = GetSasReadLocator(contextSource, sourceOutputAsset);

            // 2.Get the container name of the source asset from the read-only SAS locator created in the previous step
            string containerName = (new Uri(readSasLocator.Path)).Segments[1];

            // 3.Create a target empty asset in the target Media Services account
            var targetAsset = CreateTargetEmptyAsset(contextTarget, containerName);

            // 4.Create a write SAS locator for the target empty asset to have write access to the container in the target Storage account (associated with the target Media Services account)
            ILocator writeSasLocator = CreateSasWriteLocator(contextTarget, targetAsset);

            // Get asset container name.
            string targetContainerName = (new Uri(writeSasLocator.Path)).Segments[1];
            Console.WriteLine(targetContainerName);

            // 5.Copy the blobs in the source container (source asset) to the target container (target empty asset)
            CopyBlobsFromDifferentStorage(containerName, targetContainerName, storageNameSource, storageKeySource, storageNameTarget, storageKeyTarget);

            // 6.Use the CreateFileInfos Media Services REST API to automatically generate all the IAssetFile’s for the target asset. 
            //      This API call is not supported in the current Media Services SDK for .NET. 
            CreateFileInfos(targetApiServer, tokenSPTarget, targetAsset.Id);
            // Check if the AssetFiles are now  associated with the asset.
            Console.WriteLine("Asset files associated with the {0} asset:", targetAsset.Name);
            foreach (var af in targetAsset.AssetFiles)
            {
            Console.WriteLine(af.Name);
            }

            // 7.Copy the Origin locator of the source asset to the target asset by using the same Id
            var replicatedLocatorPath = CreateTargetOriginLocatorWithRest(contextSource, contextTarget, sourceOriginLocator.Id, targetAsset.Id);

            // Create a full URL to the manifest file. Use this for playback
            // in streaming media clients. 
            string originalUrlForClientStreaming = sourceOriginLocator.Path + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Original Locator Path: {0}\n", originalUrlForClientStreaming);

            string replicatedUrlForClientStreaming = replicatedLocatorPath + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Replicated Locator Path: {0}", replicatedUrlForClientStreaming);

            readSasLocator.Delete();
            writeSasLocator.Delete();
        }

        }
        catch (Exception e)
        {
        Console.WriteLine("Exception:" + e.ToString());
        }
    }
    ```
3. Następujące definicje metod są wywoływane z metody Main. Aby uzyskać więcej informacji na temat każdej z tych metod, zobacz komentarze.

    >[!NOTE]
    >Istnieje limit 1 000 000 zasad dla różnych zasad Media Services (na przykład w przypadku zasad lokalizatora lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni i uprawnień dostępu. Na przykład użyj tego samego identyfikatora dla zasad dla lokalizatorów, które mają być nadal wykonywane przez długi czas (zasady bez przekazywania). Aby uzyskać więcej informacji, zobacz [ten temat](media-services-dotnet-manage-entities.md#limit-access-policies).

    ```csharp
    public static IAsset CreateAssetAndUploadSingleFile(CloudMediaContext context,
                         AssetCreationOptions assetCreationOptions,
                         string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

        var asset = context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0}", assetFile.Name);

        return asset;
    }

    public static IJob CreateEncodingJob(CloudMediaContext context, IAsset asset)
    {
        // Declare a new job.
        IJob job = context.Jobs.Create("My encoding job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName(context,
                            "Media Encoder Standard");

        // Create a task with the encoding details, using a string preset.
        // In this case "Adaptive Streaming" preset is used.
        ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "Adaptive Streaming",
        TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        var outputAssetName = "OutputAsset_" + Guid.NewGuid();
        task.OutputAssets.AddNew(outputAssetName,
        AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
        // in your output folder.
        LogJobDetails(context, job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // Get an updated job reference.
        job = context.Jobs.Where(j => j.Id == job.Id).FirstOrDefault();
        // Since we the output asset contains a set of Smooth Streaming files,
        // set the .ism file to be the primary file
        if (job.State != JobState.Error)
        SetPrimaryFile(job.OutputMediaAssets[0]);

        return job;
    }

    public static ILocator GetStreamingOriginLocator(CloudMediaContext context, IAsset assetToStream)
    {
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        IAssetFile manifestFile = GetPrimaryFile(assetToStream);

        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            

        IAccessPolicy policy = context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = context.Locators.CreateLocator(LocatorType.OnDemandOrigin,
        assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

        // Return the locator. 
        return originLocator;
    }

    public static ILocator GetSasReadLocator(CloudMediaContext context, IAsset asset)
    {
        IAccessPolicy accessPolicy = context.AccessPolicies.Create("File Download Policy",
        TimeSpan.FromDays(30), AccessPermissions.Read);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, accessPolicy);

        return sasLocator;
    }

    public static ILocator CreateSasWriteLocator(CloudMediaContext context, IAsset asset)
    {

        IAccessPolicy writePolicy = context.AccessPolicies.Create("Write Policy",
        TimeSpan.FromDays(30), AccessPermissions.Write);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, writePolicy);

        return sasLocator;
    }

    public static IAsset CreateTargetEmptyAsset(CloudMediaContext context, string containerName)
    {
        // Create a new asset.
        IAsset assetToBeProcessed = context.Assets.Create(containerName,
        AssetCreationOptions.None);

        return assetToBeProcessed;
    }

    public static string CreateTargetOriginLocatorWithRest(CloudMediaContext contextSource, CloudMediaContext contextTarget,  string locatorIdToReplicate, string targetAssetId)
    {
        string locatorNewPath = "";

        if (!string.IsNullOrEmpty(tokenSPTarget))
        {
        var asset = contextTarget.Assets.Where(a => a.Id == targetAssetId).FirstOrDefault();

        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
        var accessPolicy = contextTarget.AccessPolicies.Create("RestTest", TimeSpan.FromDays(100),
                                    AccessPermissions.Read);
        if (asset != null)
        {
            string redirectedServiceUri = null;

            var xmlResponse = CreateLocator(targetApiServer, out redirectedServiceUri, tokenSPTarget,
                                asset.Id, accessPolicy.Id,
                                (int)LocatorType.OnDemandOrigin,
                                DateTime.UtcNow.AddMinutes(-10), locatorIdToReplicate);

            Console.WriteLine("Redirected to: " + redirectedServiceUri);
            if (xmlResponse != null)
            {
            Console.WriteLine(String.Format("Locator Id: {0}",
                            xmlResponse.GetElementsByTagName("Id")[0].InnerText));
            Console.WriteLine(String.Format("Locator Path: {0}",
                xmlResponse.GetElementsByTagName("Path")[0].InnerText));

            locatorNewPath = xmlResponse.GetElementsByTagName("Path")[0].InnerText;
            }
        }
        }

        return locatorNewPath;
    }

    public static void SetPrimaryFile(IAsset asset)
    {
        var ismAssetFiles = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase));

        if (ismAssetFiles.Count() != 1)
        throw new ArgumentException("The asset should have only one, .ism file");

        ismAssetFiles.First().IsPrimary = true;
        ismAssetFiles.First().Update();
    }

    public static IAssetFile GetPrimaryFile(IAsset asset)
    {
        // Cast the reference to a true IAssetFile type. 
        IAssetFile theManifest = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).
            FirstOrDefault();

        return theManifest;
    }

    public static void CopyBlobsFromDifferentStorage(string sourceContainerName, string targetContainerName,
                        string srcAccountName, string srcAccountKey,
                        string destAccountName, string destAccountKey)
    {
        var srcAccount = new CloudStorageAccount(new StorageCredentials(srcAccountName, srcAccountKey), true);
        var destAccount = new CloudStorageAccount(new StorageCredentials(destAccountName, destAccountKey), true);

        var cloudBlobClient = srcAccount.CreateCloudBlobClient();
        var targetBlobClient = destAccount.CreateCloudBlobClient();

        var sourceContainer = cloudBlobClient.GetContainerReference(sourceContainerName);
        var targetContainer = targetBlobClient.GetContainerReference(targetContainerName);
        targetContainer.CreateIfNotExists();

        string blobToken = sourceContainer.GetSharedAccessSignature(new SharedAccessBlobPolicy()
        {
        // Specify the expiration time for the signature.
        SharedAccessExpiryTime = DateTime.Now.AddDays(1),
        // Specify the permissions granted by the signature.
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read
        });

        foreach (var sourceBlob in sourceContainer.ListBlobs())
        {
        string fileName = (sourceBlob as ICloudBlob).Name;
        var sourceCloudBlob = sourceContainer.GetBlockBlobReference(fileName);
        sourceCloudBlob.FetchAttributes();

        if (sourceCloudBlob.Properties.Length > 0)
        {
            // In Azure Media Services, the files are stored as block blobs. 
            // Page blobs are not supported by Azure Media Services.  
            var destinationBlob = targetContainer.GetBlockBlobReference(fileName);
            destinationBlob.StartCopy(new Uri(sourceBlob.Uri.AbsoluteUri + blobToken));

            while (true)
            {
            // The StartCopyFromBlob is an async operation, 
            // so we want to check if the copy operation is completed before proceeding. 
            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
            destinationBlob.FetchAttributes();
            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
            {
                break;
            }
            //It's still not completed. So wait for some time.
            System.Threading.Thread.Sleep(1000);
            }
        }

        Console.WriteLine(fileName);
        }

        Console.WriteLine("Done copying.");
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(CloudMediaContext context, string mediaProcessorName)
    {

        var processor = context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

    // This method is a handler for events that track job progress.   
    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);

        switch (e.CurrentState)
        {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(null, job.Id);
            break;
        default:
            break;
        }
    }

    private static void LogJobStop(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        // Log job errors if they exist.  
        if (job.State == JobState.Error)
        {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
            builder.AppendLine("  Task Id: " + task.Id);
            builder.AppendLine("    Error Code: " + detail.Code);
            builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
        }
        builder.AppendLine("***************************\n");
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    private static void LogJobDetails(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nJob ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());

        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobDetails-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    // Replace ":" with "_" in Job id values so they can 
    // be used as log file names.  
    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }

    // Write method output to the output files folder.
    private static void WriteToFile(string outFilePath, string fileContent)
    {
        StreamWriter sr = File.CreateText(outFilePath);
        sr.WriteLine(fileContent);
        sr.Close();
    }

    //////////////////////////////////////////////////////
    /// The following methods use REST calls.
    //////////////////////////////////////////////////////
    public static XmlDocument CreateLocator(string mediaServicesApiServerUri,
                        out string redirectedMediaServicesApiServerUri,
                        string bearerToken, string assetId,
                        string accessPolicyId, int locatorType,
                        DateTime startTime, string locatorIdToReplicate = null,
                        bool autoRedirect = true)
    {
        if (string.IsNullOrEmpty(mediaServicesApiServerUri))
        {
        mediaServicesApiServerUri = "https://media.windows.net/api/";
        }
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (string.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("BearerToken");
        if (string.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");
        if (string.IsNullOrEmpty(accessPolicyId)) throw new ArgumentNullException("accessPolicyId");

        redirectedMediaServicesApiServerUri = null;
        XmlDocument xmlResponse = null;

        StringBuilder sb = new StringBuilder();
        sb.Append("{ \"AssetId\" : \"" + assetId + "\"");
        sb.Append(", \"AccessPolicyId\" : \"" + accessPolicyId + "\"");
        sb.Append(", \"Type\" : \"" + locatorType + "\"");
        if (startTime != DateTime.MinValue)
        sb.Append(", \"StartTime\" : \"" + startTime.ToString("G", CultureInfo.CreateSpecificCulture("en-us")) + "\"");
        if (!string.IsNullOrEmpty(locatorIdToReplicate))
        sb.Append(", \"Id\" : \"" + locatorIdToReplicate + "\"");
        sb.Append("}");

        string requestbody = sb.ToString();

        try
        {
        var request = GenerateRequest("POST", mediaServicesApiServerUri, "Locators",
            null, bearerToken, requestbody);
        var response = (HttpWebResponse)request.GetResponse();

        switch (response.StatusCode)
        {
            case HttpStatusCode.MovedPermanently:
            //Recurse once with the mediaServicesApiServerUri redirect Location:
            if (autoRedirect)
            {
                redirectedMediaServicesApiServerUri = response.Headers["Location"];
                string secondRedirection = null;
                xmlResponse = CreateLocator(redirectedMediaServicesApiServerUri,
                            out secondRedirection, bearerToken,
                            assetId, accessPolicyId, locatorType,
                            startTime, locatorIdToReplicate, false);
            }
            else
            {
                Console.WriteLine("Redirection to {0} failed.",
                mediaServicesApiServerUri);
                return null;
            }
            break;
            case HttpStatusCode.Created:
            using (Stream responseStream = response.GetResponseStream())
            {
                using (StreamReader stream = new StreamReader(responseStream))
                {
                string responseString = stream.ReadToEnd();
                var reader = JsonReaderWriterFactory.
                    CreateJsonReader(Encoding.UTF8.GetBytes(responseString),
                    new XmlDictionaryReaderQuotas());

                xmlResponse = new XmlDocument();
                reader.Read();
                xmlResponse.LoadXml(reader.ReadInnerXml());
                }
            }
            break;

            default:
            Console.WriteLine(response.StatusDescription);
            break;
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }

        return xmlResponse;
    }

    public static void CreateFileInfos(string mediaServicesApiServerUri,
                    string bearerToken,
                    string assetId
                    )
    {
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (String.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("bearerToken");
        if (String.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");

        try
        {
        var request = GenerateRequest("GET", mediaServicesApiServerUri, "CreateFileInfos",
            String.Format(CultureInfo.InvariantCulture, "assetid='{0}'", assetId), bearerToken, null);

        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            if (response.StatusCode == HttpStatusCode.MovedPermanently)
            {
            string redirectedMediaServicesApiUrl = response.Headers["Location"];

            CreateFileInfos(redirectedMediaServicesApiUrl, bearerToken, assetId);
            }
            else if ((response.StatusCode != HttpStatusCode.OK) &&
            (response.StatusCode != HttpStatusCode.Accepted) &&
            (response.StatusCode != HttpStatusCode.Created) &&
            (response.StatusCode != HttpStatusCode.NoContent))
            {
            throw new Exception("Invalid response received ");
            }
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }
    }

    private static HttpWebRequest GenerateRequest(string verb,
                            string mediaServicesApiServerUri,
                            string resourcePath, string query,
                            string bearerToken, string requestbody)
    {
        var uriBuilder = new UriBuilder(mediaServicesApiServerUri);
        uriBuilder.Path += resourcePath;
        if (query != null)
        {
        uriBuilder.Query = query;
        }

        HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uriBuilder.Uri);
        request.AllowAutoRedirect = false; //We manage our own redirects.
        request.Method = verb;

        if (resourcePath == "$metadata")
        request.MediaType = "application/xml";
        else
        {
        request.ContentType = "application/json;odata=verbose";
        request.Accept = "application/json;odata=verbose";
        }

        request.Headers.Add("DataServiceVersion", "3.0");
        request.Headers.Add("MaxDataServiceVersion", "3.0");
        request.Headers.Add("x-ms-version", "2.19");
        request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + bearerToken);

        if (requestbody != null)
        {
        var requestBytes = Encoding.ASCII.GetBytes(requestbody);
        request.ContentLength = requestBytes.Length;

        var requestStream = request.GetRequestStream();
        requestStream.Write(requestBytes, 0, requestBytes.Length);
        requestStream.Close();
        }
        else
        {
        request.ContentLength = 0;
        }
        return request;
    }
    ```
    
## <a name="content-protection"></a>Ochrona zawartości

W przykładzie w tym temacie przedstawiono wyczyszczenie przesyłania strumieniowego. W celu przeprowadzenia chronionego przesyłania strumieniowego istnieje kilka innych rzeczy, które należy skonfigurować, należy użyć tego samego **AssetDeliveryPolicy**, tego samego adresu URL **ContentKeyAuthorizationPolicy** lub zewnętrznego serwera kluczy, a także należy zduplikować klucze zawartości o takim samym identyfikatorze.

Aby uzyskać więcej informacji na temat ochrony zawartości, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy](media-services-protect-with-aes128.md).

## <a name="see-also"></a>Zobacz też

[Monitorowanie powiadomień dotyczących zadań Media Services za pomocą elementów webhook platformy Azure](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="next-steps"></a>Następne kroki

Za pomocą Menedżera ruchu można teraz kierować żądania między dwoma centrami danych, a tym samym przechodzić w tryb failover w przypadku awarii.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

