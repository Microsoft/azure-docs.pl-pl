---
title: Tworzenie przeglądów wideo przy użyciu platformy .NET Content Moderator
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje i przykłady kodu ułatwiające szybkie rozpoczęcie pracy przy użyciu zestawu SDK Content Moderator w języku C# w celu utworzenia recenzji wideo.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 55802b04e10f06c7e362033f97d24c4e20c12c62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566493"
---
# <a name="create-video-reviews-using-net"></a>Tworzenie recenzji wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu ułatwiające szybkie rozpoczęcie korzystania z [zestawu SDK Content moderator w języku C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) w celu:

- Tworzenie przeglądu wideo dla moderatorów ludzkich
- Dodawanie ramek do przeglądu
- Pobierz ramki do przeglądu
- Pobierz stan i szczegóły przeglądu
- Publikuj przegląd

## <a name="prerequisites"></a>Wymagania wstępne

- Zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .
- W tym artykule przyjęto założenie, że masz [umiarkowane wideo (zobacz Szybki Start)](video-moderation-api.md) i masz dane odpowiedzi. Jest ona potrzebna do tworzenia przeglądów opartych na ramkach dla moderatorów ludzkich.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Upewnij się, że Twój klucz interfejsu API umożliwia wywołanie interfejsu API przeglądu w celu utworzenia przeglądu.

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Przygotuj wideo i ramki wideo do przeglądu

Filmy wideo i przykładowe ramki wideo do przejrzenia muszą zostać opublikowane w trybie online, ponieważ są potrzebne adresy URL.

> [!NOTE]
> Program używa ręcznie zapisanych zrzutów ekranu z filmu wideo z losowymi wynikami dla dorosłych/erotycznej, aby zilustrować korzystanie z interfejsu API przeglądu. W świecie rzeczywistym można używać [danych wyjściowych moderowania wideo](video-moderation-api.md#run-the-program-and-review-the-output) do tworzenia obrazów i przypisywania wyników. 

Do wideo potrzebny jest punkt końcowy przesyłania strumieniowego, dzięki czemu narzędzie do przeglądu będzie odtwarzać wideo w widoku odtwarzacza.

![Miniatura pokazu wideo](images/ams-video-demo-view.PNG)

- Skopiuj **adres URL** na tej [Azure Media Services stronie DEMONSTRACYJNEJ](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) dla adresu URL manifestu.

Dla ramek wideo (obrazów) Użyj następujących obrazów:

![Miniatura ramki wideo 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura ramki wideo 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura ramki wideo 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
Ramka 1 | Ramka 2 | Ramka 3 |

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nazwij projekt **VideoReviews**.

1. Wybierz ten projekt jako pojedynczy projekt startowy rozwiązania.

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

Zainstaluj następujące pakiety NuGet dla projektu TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aktualizowanie programu za pomocą instrukcji

Zmodyfikuj instrukcje using programu w następujący sposób.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Dodawanie właściwości prywatnych

Dodaj następujące właściwości prywatne do przestrzeni nazw **VideoReviews**, **programu** klasy. Zaktualizuj `AzureEndpoint` pola i `CMSubscriptionKey` przy użyciu wartości adresu URL punktu końcowego i klucza subskrypcji. Te informacje można znaleźć na karcie **Szybki Start** zasobu w Azure Portal.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Utwórz obiekt klienta Content Moderator

Dodaj następującą definicję metody do **VideoReviews** przestrzeni nazw, **programu** klasy.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Tworzenie przeglądu wideo

Utwórz recenzję wideo za pomocą **ContentModeratorClient. Reviews. CreateVideoReviews**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien mieć wartość "Application/JSON". 
1. Nazwa zespołu Content Moderator.
1. Obiekt **IList \<CreateVideoReviewsBodyItem>** . Każdy obiekt **CreateVideoReviewsBodyItem** reprezentuje przegląd wideo. Ten przewodnik Szybki Start tworzy jeden przegląd w danym momencie.

**CreateVideoReviewsBodyItem** ma kilka właściwości. Należy ustawić co najmniej następujące właściwości:
- **Zawartość**. Adres URL filmu wideo, który ma zostać sprawdzony.
- **Identyfikatorze**. Identyfikator, który ma zostać przypisany do przeglądu wideo.
- **Stan**. Ustaw wartość na "unopublikowałd". Jeśli go nie ustawisz, zostanie on ustawiony jako "Oczekujący", co oznacza, że przegląd wideo jest publikowany i oczekuje na weryfikację przez człowieka. Po opublikowaniu recenzji wideo nie można już dodawać do niej ramek wideo, transkrypcji ani moderowania transkrypcji.

> [!NOTE]
> **CreateVideoReviews** zwraca element IList \<string> . Każdy z tych ciągów zawiera identyfikator dla recenzji wideo. Identyfikatory te są identyfikatorami GUID i nie są takie same jak wartość właściwości **identyfikatorze** . 

Dodaj następującą definicję metody do VideoReviews przestrzeni nazw, programu klasy.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Klucz usług Content Moderator ma limit liczby żądań na sekundę (RPS), a w razie przekroczenia tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-video-frames-to-the-video-review"></a>Dodawanie klatek wideo do recenzji wideo

Dodawaj klatki wideo do recenzji wideo przy użyciu **ContentModeratorClient. Reviews. AddVideoFrameUrl** (Jeśli ramki wideo są hostowane online) lub **ContentModeratorClient. Reviews. AddVideoFrameStream** (Jeśli ramki wideo są hostowane lokalnie). W tym przewodniku szybki start założono, że ramki wideo są hostowane w trybie online, a więc używają **AddVideoFrameUrl**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien mieć wartość "Application/JSON".
1. Nazwa zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. Obiekt **IList \<VideoFrameBodyItem>** . Każdy obiekt **VideoFrameBodyItem** reprezentuje ramkę wideo.

**VideoFrameBodyItem** ma następujące właściwości:
- **Sygnatura czasowa**. Ciąg zawierający w sekundach czas w filmie wideo, z którego zrobiono klatkę wideo.
- **FrameImage**. Adres URL ramki wideo.
- **Metadanych**. Element IList \<VideoFrameBodyItemMetadataItem> . **VideoFrameBodyItemMetadataItem** to po prostu para klucz/wartość. Prawidłowe klucze obejmują:
- **reviewRecommended**. Ma wartość true, jeśli zalecamy przeglądanie ramki wideo przez człowieka.
- **adultScore**. Wartość z przedziału od 0 do 1, która ocenia ważność zawartości dla dorosłych w ramce wideo.
- **a**. Prawda, jeśli film wideo zawiera zawartość dla dorosłych.
- **racyScore**. Wartość z przedziału od 0 do 1, która ocenia ważność zawartości erotycznej w ramce wideo.
- **r**. Ma wartość true, jeśli ramka wideo zawiera zawartość erotycznej.
- **ReviewerResultTags**. Element IList \<VideoFrameBodyItemReviewerResultTagsItem> . **VideoFrameBodyItemReviewerResultTagsItem** to po prostu para klucz/wartość. Aplikacja może używać tych tagów do organizowania ramek wideo.

> [!NOTE]
> Ten przewodnik Szybki Start generuje losowo wartości właściwości **adultScore** i **racyScore** . W aplikacji produkcyjnej można uzyskać te wartości z [usługi moderowania wideo](video-moderation-api.md)wdrożonej jako usługa Azure Media.

Dodaj następujące definicje metod do VideoReviews przestrzeni nazw, programu klasy.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Pobierz ramki wideo na potrzeby recenzji wideo

Możesz pobrać klatki wideo dla recenzji wideo za pomocą **ContentModeratorClient. Reviews. GetVideoFrames**. **GetVideoFrames** ma następujące wymagane parametry:
1. Nazwa zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. Indeks (liczony od zera) pierwszej ramki wideo do pobrania.
1. Liczba klatek wideo do pobrania.

Dodaj następującą definicję metody do VideoReviews przestrzeni nazw, programu klasy.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Pobierz informacje o przeglądzie wideo

Uzyskasz informacje na temat przeglądu wideo za pomocą **ContentModeratorClient. Reviews.** GetView. **Getrecenzja** ma następujące wymagane parametry:
1. Nazwa zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do VideoReviews przestrzeni nazw, programu klasy.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publikuj przegląd wideo

Przegląd wideo jest publikowany za pomocą **ContentModeratorClient. Reviews. PublishVideoReview**. **PublishVideoReview** ma następujące wymagane parametry:
1. Nazwa zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.

Dodaj następującą definicję metody do VideoReviews przestrzeni nazw, programu klasy.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów

Dodaj definicję metody **Main** do VideoReviews przestrzeni nazw, programu klasy. Na koniec Zamknij klasę program i przestrzeń nazw VideoReviews.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Uruchamianie programu i przeglądanie danych wyjściowych
Po uruchomieniu aplikacji zobaczysz dane wyjściowe w następujących wierszach:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Zapoznaj się z przeglądem wideo

Na koniec zobaczysz recenzję wideo na koncie narzędzia Content moderator recenzja na ekranie **Przejrzyj** > **wideo** .

![Przegląd wideo dla moderatorów ludzkich](images/ams-video-review.PNG)

## <a name="next-steps"></a>Następne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych Content moderator przewodników szybki start dla platformy .NET.
