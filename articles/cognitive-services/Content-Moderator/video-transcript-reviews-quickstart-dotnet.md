---
title: Tworzenie przeglądów transkrypcji wideo przy użyciu platformy .NET Content Moderator
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć przeglądy transkrypcji wideo przy użyciu zestawu Azure Cognitive Services Content Moderator SDK dla platformy .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853462"
---
# <a name="create-video-transcript-reviews-using-net"></a>Tworzenie przeglądów transkrypcji wideo przy użyciu platformy .NET

Ten artykuł zawiera informacje i przykłady kodu ułatwiające szybkie rozpoczęcie korzystania z [zestawu SDK Content moderator w języku C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) w celu:

- Tworzenie przeglądu wideo dla moderatorów ludzkich
- Dodaj moderowany transkrypcję do przeglądu
- Publikuj przegląd

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jeszcze tego nie zrobiono, zaloguj się lub Utwórz konto w witrynie Content Moderator [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com/) .
- W tym artykule przyjęto założenie, że masz [umiarkowane wideo](video-moderation-api.md) i [utworzono przegląd wideo](video-reviews-quickstart-dotnet.md) w narzędziu do przeglądu w celu podejmowania decyzji. Teraz chcesz dodać moderowane transkrypcje wideo w narzędziu do przeglądu.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Upewnij się, że klucz interfejsu API może wywołać interfejs API przeglądu (Tworzenie zadania)

Po wykonaniu poprzednich kroków możesz mieć dwa klucze usługi Content Moderator, jeśli wykonywanie kroków rozpoczęto w witrynie Azure Portal.

Jeśli planujesz użyć klucza interfejsu API platformy Azure w przykładzie zestawu SDK, wykonaj kroki opisane w sekcji [Używanie klucza platformy Azure przy użyciu interfejsu API przeglądu](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis), aby umożliwić aplikacji wywołanie interfejsu API przeglądu i tworzenie przeglądów.

Jeśli używasz bezpłatnej wersji próbnej klucza wygenerowanej przez narzędzie do przeprowadzania przeglądów, narzędzie to będzie już znało klucz i dlatego dodatkowe kroki nie są wymagane.

## <a name="prepare-your-video-for-review"></a>Przygotuj wideo do przeglądu

Dodaj transkrypcję do przeglądu wideo. Film wideo musi być opublikowany w trybie online. Wymagany jest punkt końcowy przesyłania strumieniowego. Punkt końcowy przesyłania strumieniowego Umożliwia odtwarzaczowi wideo narzędzia do przeglądu odtwarzanie filmu wideo.

![Miniatura pokazu wideo](images/ams-video-demo-view.PNG)

- Skopiuj **adres URL** na tej [Azure Media Services stronie DEMONSTRACYJNEJ](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) dla adresu URL manifestu.

## <a name="create-your-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Dodaj nowy projekt **Aplikacja konsoli (.NET Framework)** do rozwiązania.

1. Nazwij projekt **VideoTranscriptReviews**.

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

Dodaj następujące właściwości prywatne do przestrzeni nazw **VideoTranscriptReviews**, **programu** klasy. Zaktualizuj `AzureEndpoint` pola i `CMSubscriptionKey` przy użyciu wartości adresu URL punktu końcowego i klucza subskrypcji. Te informacje można znaleźć na karcie **Szybki Start** zasobu w Azure Portal.

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

Dodaj następującą definicję metody do VideoTranscriptReviews przestrzeni nazw, programu klasy.

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
> Klucz usługi Content Moderator ma limit szybkości wyrażany w żądaniach na sekundę (RPS). Po przekroczeniu tego limitu zestaw SDK zgłasza wyjątek z kodem błędu 429.
>
> Limit klucza warstwy bezpłatnej wynosi 1 RPS.

## <a name="add-transcript-to-video-review"></a>Dodawanie transkrypcji do przeglądu wideo

Dodaj transkrypcję do przeglądu wideo za pomocą **ContentModeratorClient. Reviews. AddVideoTranscript**. **AddVideoTranscript** ma następujące wymagane parametry:
1. Identyfikator Twojego zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. Obiekt **Stream** zawierający transkrypcję.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: format danych wideo w sieci Web](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Program używa przykładowego transkrypcji w formacie VTT. W rzeczywistym świecie rozwiązanie używa usługi Azure Media Indexer do [wygenerowania transkrypcji](../../media-services/previous/media-services-index-content.md) z filmu wideo.

Dodaj następującą definicję metody do VideotranscriptReviews przestrzeni nazw, programu klasy.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Dodaj wynik moderowania transkrypcji do przeglądu wideo

Oprócz dodawania transkrypcji do przeglądu wideo, można również dodać wynik moderowania tego transkrypcji. Można to zrobić za pomocą **ContentModeratorClient. Reviews. AddVideoTranscriptModerationResult**. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** ma następujące wymagane parametry:
1. Ciąg zawierający typ MIME, który powinien mieć wartość "Application/JSON". 
1. Nazwa zespołu Content Moderator.
1. Identyfikator przeglądu wideo zwrócony przez **CreateVideoReviews**.
1. Element IList \<TranscriptModerationBodyItem> . **TranscriptModerationBodyItem** ma następujące właściwości:
1. **Warunki**. Element IList \<TranscriptModerationBodyItemTermsItem> . **TranscriptModerationBodyItemTermsItem** ma następujące właściwości:
1. **Indeks**. Indeks warunku liczony od zera.
1. **Termin**. Ciąg, który zawiera termin.
1. **Sygnatura czasowa**. Ciąg zawierający w sekundach czas w transkrypcji, w którym znajdują się warunki.

Transkrypcja musi być w formacie WebVTT. Aby uzyskać więcej informacji, zobacz [WebVTT: format danych wideo w sieci Web](https://www.w3.org/TR/webvtt1/).

Dodaj następującą definicję metody do VideoTranscriptReviews przestrzeni nazw, programu klasy. Ta metoda przesyła transkrypcję do metody **ContentModeratorClient. Textłagodzenie. ScreenText** . Ponadto tłumaczy wynik na IList \<TranscriptModerationBodyItem> i przesyła do **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Dodaj definicję metody **Main** do VideoTranscriptReviews przestrzeni nazw, programu klasy. Na koniec Zamknij klasę program i przestrzeń nazw VideoTranscriptReviews.

> [!NOTE]
> Program używa przykładowego transkrypcji w formacie VTT. W rzeczywistym świecie rozwiązanie używa usługi Azure Media Indexer do [wygenerowania transkrypcji](../../media-services/previous/media-services-index-content.md) z filmu wideo.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Przejdź do przeglądu transkrypcji wideo

Przejdź do przeglądu transkrypcji wideo w narzędziu Content moderator recenzja na ekranie **Przegląd** >  > **transkrypcji** wideo.

Zobaczysz następujące funkcje:
- Dwa wiersze dodanego transkrypcji
- Znaleziono i podświetlony przez usługę moderowania tekstu.
- Wybranie tekstu transkrypcji spowoduje rozpoczęcie filmu wideo od tej sygnatury czasowej

![Przegląd transkrypcji wideo dla moderatorów ludzkich](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Następne kroki

Pobierz [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) i [rozwiązanie Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) dla tego i innych Content moderator przewodników szybki start dla platformy .NET.

Dowiedz się, jak generować [Recenzje wideo](video-reviews-quickstart-dotnet.md) w narzędziu do przeglądu.
