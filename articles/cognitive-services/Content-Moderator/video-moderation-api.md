---
title: Analizowanie zawartości wideo dla nieistotnego materiału w języku C# — Content Moderator
titleSuffix: Azure Cognitive Services
description: Jak analizować zawartość wideo dla różnych niepożądanych materiałów przy użyciu zestawu Content Moderator SDK dla platformy .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b389841bdba107ba27371387d4a6e5d1f009d41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919356"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analizowanie zawartości wideo dla nieistotnego materiału w języku C #

Ten artykuł zawiera informacje i przykłady kodu ułatwiające rozpoczęcie korzystania z [zestawu Content moderator SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) w celu skanowania zawartości wideo dla dorosłych lub erotycznej zawartości.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Wymagania wstępne
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Funkcja moderowania wideo w Content Moderator jest dostępna jako bezpłatny publiczny **procesor multimedialny** w wersji zapoznawczej w Azure Media Services (AMS). Azure Media Services to wyspecjalizowana usługa platformy Azure do przechowywania i przesyłania strumieniowego zawartości wideo. 

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) , aby SUBSKRYBOWAĆ usługę AMS i utworzyć skojarzone konto usługi Azure Storage. W ramach tego konta magazynu Utwórz nowy kontener magazynu obiektów BLOB.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory

Przejdź do nowej subskrypcji usługi AMS w Azure Portal i wybierz pozycję **dostęp do interfejsu API** z menu bocznego. Wybierz pozycję **Połącz z Azure Media Services za pomocą nazwy głównej usługi**. Zanotuj wartość w polu **punkt końcowy interfejsu API REST** . będzie on potrzebny później.

W sekcji **aplikacja usługi Azure AD** wybierz pozycję **Utwórz nową** i nadaj nazwę nowej rejestracji aplikacji usługi Azure AD (na przykład "VideoModADApp"). Kliknij przycisk **Zapisz** i odczekaj kilka minut, gdy aplikacja jest skonfigurowana. Następnie w sekcji **aplikacji usługi Azure AD** na stronie powinna zostać wyświetlona nowa Rejestracja aplikacji.

Wybierz swoją rejestrację aplikacji, a następnie kliknij przycisk **Zarządzaj aplikacjami** poniżej. Zanotuj wartość w polu **Identyfikator aplikacji** ; będzie on potrzebny później. Wybierz pozycję **Ustawienia**  >  **klucze**i wprowadź opis nowego klucza (na przykład "VideoModKey"). Kliknij przycisk **Zapisz**, a następnie Zwróć uwagę na nową wartość klucza. Skopiuj ten ciąg i Zapisz go w bezpiecznym miejscu.

Dokładniejszy Przewodnik dotyczący powyższego procesu znajduje się w temacie [Rozpoczynanie pracy z uwierzytelnianiem w usłudze Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Po wykonaniu tej czynności można użyć procesora mediów do moderowania wideo na dwa różne sposoby.

## <a name="use-azure-media-services-explorer"></a>Korzystanie z Eksploratora Azure Media Services

Eksplorator Azure Media Services jest przyjazny dla użytkownika fronton dla usługi AMS. Służy do przeglądania konta AMS, przekazywania filmów wideo i skanowania zawartości przy użyciu procesora multimediów Content Moderator. Pobierz i zainstaluj go z usługi [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)lub zobacz [wpis w blogu Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) , aby uzyskać więcej informacji.

![Eksplorator Azure Media Services z Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. W programie Visual Studio Utwórz nowy projekt **aplikacja konsoli (.NET Framework)** i nadaj mu nazwę **VideoModeration**. 
1. Jeśli w rozwiązaniu istnieją inne projekty, wybierz ten projekt jako pojedynczy projekt startowy.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Zarządzaj pakietami NuGet**, a następnie znajdź i zainstaluj następujące pakiety:
    - windowsazure. MediaServices
    - windowsazure. MediaServices. Extensions

## <a name="add-video-moderation-code"></a>Dodaj kod moderowania wideo

Następnie skopiujesz kod z tego przewodnika i wkleisz go do projektu, aby zaimplementować podstawowy scenariusz moderowania zawartości.

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Dodaj następujące instrukcje `using` na początku pliku _Program.cs_.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Konfigurowanie odwołań zasobów

Dodaj następujące pola statyczne do klasy **program** w _program.cs_. Te pola zawierają informacje niezbędne do nawiązania połączenia z subskrypcją usługi AMS. Wypełnij je wartościami, które zostały podane w powyższych krokach. Zwróć uwagę, że `CLIENT_ID` jest to wartość **identyfikatora aplikacji** dla aplikacji usługi Azure AD `CLIENT_SECRET` . jest to wartość "VideoModKey", która została utworzona dla tej aplikacji.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Jeśli chcesz użyć lokalnego pliku wideo (najprostszy przypadek), Dodaj go do projektu i wprowadź ścieżkę jako `INPUT_FILE` wartość (ścieżki względne są względne dla katalogu wykonywania).

Należy również utworzyć _preset.jsw_ pliku w bieżącym katalogu i użyć go do określenia numeru wersji. Na przykład:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Załaduj wejściowe wideo

Metoda **Main** klasy **program** utworzy kontekst usługi Azure Media, a następnie kontekst usługi Azure Storage (w przypadku filmów wideo w usłudze BLOB Storage). Pozostały kod skanuje wideo z folderu lokalnego, obiektu BLOB lub wielu obiektów BLOB w kontenerze usługi Azure Storage. Możesz wypróbować wszystkie opcje, dodając komentarz do innych wierszy kodu.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Tworzenie kontekstu multimediów platformy Azure

Dodaj następującą metodę do klasy **Program**. Ta funkcja używa poświadczeń AMS do zezwalania na komunikację z usługą AMS.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Dodawanie kodu w celu utworzenia kontekstu usługi Azure Storage

Dodaj następującą metodę do klasy **Program**. Aby uzyskać dostęp do usługi BLOB Storage, należy użyć kontekstu magazynu utworzonego na podstawie poświadczeń magazynu.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Dodaj kod, aby utworzyć zasoby multimediów platformy Azure z lokalnego pliku i obiektu BLOB

Procesor multimediów Content Moderator uruchamia zadania dotyczące **zasobów** w ramach platformy Azure Media Services.
Te metody tworzą zasoby z pliku lokalnego lub skojarzonego obiektu BLOB.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Dodawanie kodu do skanowania kolekcji filmów wideo (jako obiektów BLOB) w obrębie kontenera

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Dodaj metodę w celu uruchomienia zadania Content Moderator

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Dodawanie funkcji pomocnika

Te metody pobierają plik wyjściowy Content Moderator (JSON) z zasobu Azure Media Services i ułatwiają śledzenie stanu zadania moderowania, dzięki czemu program może rejestrować stan uruchomienia w konsoli programu.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Uruchamianie programu i przeglądanie danych wyjściowych

Po zakończeniu zadania moderowania zawartości Przeanalizuj odpowiedź JSON. Składa się z następujących elementów:

- Podsumowanie informacji wideo
- **Zrzuty** jako "**fragmenty**"
- **Kluczowe klatki** jako "**Events**" z flagą **reviewRecommended "(= true lub false)"** opartą na punktacjach **dorosłych** i **erotycznej**
- wartości **Start**, **Duration**, **totalDuration**i **timestamp** są w "taktach". Podziel przez **skalę czasu** , aby uzyskać liczbę w sekundach.
 
> [!NOTE]
> - `adultScore` reprezentuje potencjalne informacje o obecności i prognozie zawartości, które mogą być uważane za jawne lub dorosłe w pewnych sytuacjach.
> - `racyScore` reprezentuje potencjalne informacje o obecności i prognozie zawartości, które mogą być uznawane za sugerowane lub dojrzałe w pewnych sytuacjach.
> - `adultScore` i `racyScore` należą do zakresu od 0 do 1. Im wyższy wynik, tym większy jest przewidywanie, że Kategoria może być stosowana. Ta wersja zapoznawcza polega na modelu statystycznym zamiast ręcznie zakodowanych wyników. Zalecamy testowanie przy użyciu własnej zawartości, aby określić, jak każda kategoria jest wyrównana do wymagań.
> - `reviewRecommended` ma wartość true lub false, w zależności od wewnętrznych progów wyniku. Klienci powinni ocenić, czy należy używać tej wartości, czy też decydować o niestandardowych progach na podstawie ich zasad dotyczących zawartości.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak generować [Recenzje wideo](video-reviews-quickstart-dotnet.md) z danych wyjściowych moderowania.

Dodaj [moderowanie transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md) do recenzji wideo.

Zapoznaj się z szczegółowym samouczkiem dotyczącym tworzenia [kompletnego rozwiązania do moderowania filmów wideo i transkrypcji](video-transcript-moderation-review-tutorial-dotnet.md).

[Pobierz rozwiązanie Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych Content moderator przewodników szybki start dla platformy .NET.
