---
title: Opracowywanie Azure Functions z Media Services
description: W tym temacie przedstawiono sposób rozpoczynania opracowywania Azure Functions z Media Services przy użyciu Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ef8e3a2e733247f713fe0240cb8ef6069803fcb7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013962"
---
# <a name="develop-azure-functions-with-media-services"></a>Opracowywanie Azure Functions z Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym artykule pokazano, jak rozpocząć tworzenie Azure Functions korzystających z Media Services. Funkcja platformy Azure zdefiniowana w tym artykule monitoruje kontener konta magazynu o nazwie **Input** dla nowych plików MP4. Gdy plik zostanie usunięty do kontenera magazynu, wyzwalacz obiektu BLOB wykonuje funkcję. Aby przejrzeć Azure Functions, zobacz  [Omówienie](../../azure-functions/functions-overview.md) i inne tematy w sekcji **Azure Functions** .

Jeśli chcesz eksplorować i wdrażać istniejące Azure Functions, które używają Azure Media Services, Wyewidencjonuj [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). To repozytorium zawiera przykłady, które używają Media Services do wyświetlania przepływów pracy związanych z pozyskiwaniem zawartości bezpośrednio z magazynu obiektów blob, kodowania i zapisywania zawartości z powrotem do magazynu obiektów BLOB. Zawiera również przykłady monitorowania powiadomień zadań za pośrednictwem elementów webhook i kolejek platformy Azure. Możesz również opracowywać funkcje na podstawie przykładów w repozytorium [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) . Aby wdrożyć funkcje, naciśnij przycisk **Wdróż na platformie Azure** .

## <a name="prerequisites"></a>Wymagania wstępne

- Przed utworzeniem pierwszej funkcji musisz mieć aktywne konto platformy Azure. Jeśli nie masz jeszcze konta platformy Azure, [dostępne są konta bezpłatne](https://azure.microsoft.com/free/).
- Jeśli zamierzasz utworzyć Azure Functions, które wykonują akcje na koncie Azure Media Services (AMS) lub nasłuchiwanie zdarzeń wysyłanych przez Media Services, należy utworzyć konto AMS, zgodnie z opisem w [tym miejscu](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. Przejdź do [witryny Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta Azure.
2. Utwórz aplikację funkcji, zgodnie z opisem w [tym miejscu](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Konto magazynu określone w zmiennej środowiskowej **StorageConnection** (Zobacz następny krok) powinno znajdować się w tym samym regionie, w którym znajduje się aplikacja.

## <a name="configure-function-app-settings"></a>Skonfiguruj ustawienia aplikacji funkcji

Podczas opracowywania funkcji Media Services warto dodać zmienne środowiskowe, które będą używane w całej funkcji. Aby skonfigurować ustawienia aplikacji, kliknij link Konfiguruj ustawienia aplikacji. Aby uzyskać więcej informacji, zobacz  [jak skonfigurować ustawienia aplikacji funkcji platformy Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

Funkcja zdefiniowana w tym artykule zakłada, że w ustawieniach aplikacji znajdują się następujące zmienne środowiskowe:

**AMSAADTenantDomain**: punkt końcowy dzierżawy usługi Azure AD. Aby uzyskać więcej informacji na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [ten](media-services-use-aad-auth-to-access-ams-api.md) artykuł.

**AMSRESTAPIEndpoint**: identyfikator URI reprezentujący punkt końcowy interfejsu API REST. 

**AMSClientId**: identyfikator klienta aplikacji usługi Azure AD.

**AMSClientSecret**: wpis tajny klienta aplikacji usługi Azure AD.

**StorageConnection**: połączenie magazynu konta skojarzonego z kontem Media Services. Ta wartość jest używana w **function.js** plik i plik **Run. CSX** (opisane poniżej).

## <a name="create-a-function"></a>Tworzenie funkcji

Po wdrożeniu aplikacji funkcji można ją znaleźć między **App Services** Azure Functions.

1. Wybierz aplikację funkcji, a następnie kliknij pozycję **Nowa funkcja**.
2. Wybierz scenariusz języka **C#** i **przetwarzania danych** .
3. Wybierz szablon **BlobTrigger** . Ta funkcja jest wyzwalana za każdym razem, gdy obiekt BLOB zostanie przekazany do kontenera **wejściowego** . Nazwa **wejściowa** jest określona w **ścieżce** w następnym kroku.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie szablonu z wybraną pozycją BlobTrigger.](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Po wybraniu opcji **BlobTrigger** na stronie pojawią się więcej kontrolek.

    ![Zrzut ekranu przedstawia okno dialogowe Nazwa funkcji.](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kliknij pozycję **Utwórz**. 

## <a name="files"></a>Pliki

Funkcja platformy Azure jest skojarzona z plikami kodu i innymi plikami opisanymi w tej sekcji. Gdy używasz Azure Portal do tworzenia funkcji, **function.json** i **Run. CSX** są tworzone dla Ciebie. Musisz dodać lub przekazać **project.js** pliku. Pozostała część tej sekcji zawiera krótkie wyjaśnienie poszczególnych plików i pokazuje ich definicje.

![Zrzut ekranu przedstawia pliki JSON w projekcie.](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.jsw pliku definiuje powiązania funkcji i inne ustawienia konfiguracji. Środowisko uruchomieniowe używa tego pliku do określenia zdarzeń do monitorowania oraz przekazywania danych do i zwracania danych z wykonywania funkcji. Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP i elementów webhook w usłudze Azure Functions](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Ustaw właściwość **Disabled** na **wartość true** , aby zapobiec wykonywaniu funkcji. 

Zastąp zawartość istniejącego function.jsw pliku następującym kodem:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

project.jsw pliku zawiera zależności. Poniżej znajduje się przykład **project.js** pliku zawierającego wymagane pakiety Azure Media Services .NET z narzędzia NuGet. Należy zauważyć, że numery wersji są zmieniane na najnowsze aktualizacje pakietów, dlatego należy potwierdzić najnowsze wersje. 

Dodaj następującą definicję, aby project.js. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>Uruchom. CSX

Jest to kod języka C# dla funkcji.  Funkcja zdefiniowana poniżej służy do monitorowania kontenera konta magazynu o nazwie **Input** (który został określony w ścieżce) dla nowych plików MP4. Gdy plik zostanie usunięty do kontenera magazynu, wyzwalacz obiektu BLOB wykonuje funkcję.
    
W przykładzie zdefiniowanym w tej sekcji przedstawiono 

1. Jak uzyskać zasób do konta Media Services (przez przetestowanie obiektu BLOB do zasobu usługi AMS) i 
2. Jak przesłać zadanie kodowania korzystające z Media Encoder Standard "adaptacyjne przesyłanie strumieniowe".

W scenariuszu w czasie rzeczywistym najprawdopodobniej chcesz śledzić postęp zadania, a następnie publikować zakodowany element zawartości. Aby uzyskać więcej informacji, zobacz [Używanie elementów webhook platformy Azure do monitorowania powiadomień dotyczących zadań Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Aby uzyskać więcej przykładów, zobacz [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Zastąp zawartość istniejącego pliku Run. CSX następującym kodem: po zakończeniu definiowania funkcji kliknij pozycję **Zapisz i uruchom**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testowanie funkcji

Aby przetestować funkcję, należy przekazać plik MP4 do kontenera **wejściowego** konta magazynu określonego w parametrach połączenia.  

1. Wybierz konto magazynu określone w zmiennej środowiskowej **StorageConnection** .
2. Kliknij pozycję **obiekty blob**.
3. Kliknij pozycję **+ Kontener**. Nazwij **dane wejściowe** kontenera.
4. Naciśnij przycisk **Przekaż** i przejdź do pliku MP4, który chcesz przekazać.

>[!NOTE]
> W przypadku korzystania z wyzwalacza obiektu BLOB w planie zużycia może wystąpić maksymalnie 10 minut opóźnienia w przetwarzaniu nowych obiektów BLOB po przejściu aplikacji funkcji do trybu bezczynności. Po uruchomieniu aplikacji funkcji obiekty blob są przetwarzane natychmiast. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania usługi BLOB Storage](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz rozpocząć tworzenie aplikacji Media Services. 
 
Aby uzyskać więcej informacji i dowiedzieć się więcej na temat przykładów i rozwiązań dotyczących używania Azure Functions i Logic Apps za pomocą Azure Media Services do tworzenia niestandardowych przepływów pracy tworzenia zawartości, zobacz [przykład integracji funkcji Media Services .NET w witrynie GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Zobacz też temat [Używanie elementów webhook platformy Azure do monitorowania powiadomień o zadaniach Media Services przy użyciu platformy .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
