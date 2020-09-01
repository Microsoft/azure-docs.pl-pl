---
title: Korzystanie z usługi Azure Media Content Moderator do wykrywania możliwej zawartości dla osób dorosłych i erotycznej | Microsoft Docs
description: Usługa Azure Media Content Moderator Media procesor pozwala wykryć potencjalną zawartość dla dorosłych i erotycznej w filmach wideo.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.custom: devx-track-csharp
ms.openlocfilehash: 395d2239970e7efbf6973d6262df0e049306584b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266701"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Korzystanie z usługi Azure Media Content Moderator do wykrywania możliwej zawartości dla osób dorosłych i erotycznej

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Procesor multimediów **usługi Azure media Content moderator** zostanie wycofany. Aby uzyskać datę wycofania, zobacz temat [starsze składniki](legacy-components.md) .

## <a name="overview"></a>Omówienie
**Usługa Azure media Content moderator** Media procesor (MP) umożliwia korzystanie z moderowania dla filmów wideo przy użyciu wspomaganego maszynowo. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi.

Pakiet MP **usługi Azure Media Content moderator** jest obecnie w wersji zapoznawczej.

W tym artykule przedstawiono szczegółowe informacje o  **usłudze Azure Media Content moderator** i pokazano, jak używać jej z zestawem SDK Media Services dla platformy .NET.

## <a name="content-moderator-input-files"></a>Pliki wejściowe Content Moderator
Pliki wideo. Obecnie obsługiwane są następujące formaty: MP4, MOV i WMV.

## <a name="content-moderator-output-files"></a>Pliki wyjściowe Content Moderator
Moderowane dane wyjściowe w formacie JSON obejmują wykryte przez autozrzuty i ramki kluczowe. Ramki kluczowe są zwracane z wynikami nieufności dla możliwej zawartości dla dorosłych lub erotycznej. Zawierają także flagę logiczną wskazującą, czy jest zalecana Recenzja. Flaga rekomendacja przeglądu ma przypisane wartości na podstawie wewnętrznych progów dla dorosłych i erotycznej ocen.

## <a name="elements-of-the-output-json-file"></a>Elementy wyjściowego pliku JSON

Zadanie tworzy plik wyjściowy JSON zawierający metadane dotyczące wykrytych zrzutów i klatek kluczowych oraz określa, czy zawierają one zawartość dla dorosłych lub erotycznej.

Wyjściowy kod JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Główne elementy JSON

| Element | Opis |
| --- | --- |
| version |Wersja Content Moderator. |
| Dział |"Ticks" na sekundę filmu wideo. |
| przesunięcie |Przesunięcie czasu dla sygnatur czasowych. W wersji 1,0 interfejsów API wideo ta wartość będzie zawsze równa 0. Ta wartość może ulec zmianie w przyszłości. |
| szybkości |Liczba klatek na sekundę w wideo. |
| szerokość |Szerokość wyjściowej ramki wideo (w pikselach).|
| wysokość |Wysokość wyjściowej ramki wideo (w pikselach).|
| totalDuration |Czas trwania wejściowego filmu wideo w "taktach". |
| [elementy](#fragments-json-elements) |Metadane są podzielone na różne segmenty o nazwie fragmenty. Każdy fragment jest wykrytym wstępnie zrzutem z początkiem, czasem trwania, numerem interwału i zdarzeniami. |

### <a name="fragments-json-elements"></a>Fragmenty elementów JSON

|Element|Opis|
|---|---|
| start |Godzina rozpoczęcia pierwszego zdarzenia w "Takty". |
| czas trwania |Długość fragmentu w "Takty". |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu w "Takty". |
| [wydarzeniach](#events-json-elements) |Każde zdarzenie reprezentuje klip, a każdy klip zawiera ramki kluczowe wykryte i śledzone w tym czasie. Jest to tablica zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie.|

### <a name="events-json-elements"></a>Events — elementy JSON

|Element|Opis|
|---|---|
| reviewRecommended | `true` lub `false` w zależności od tego, czy **AdultScore** lub **racyScore** przekraczają wewnętrzne progi. |
| adultScore | Wynik pewności dotyczącej możliwej zawartości dla dorosłych w skali od 0,00 do 0,99. |
| racyScore | Wynik zaufania dla możliwej zawartości erotycznej w skali od 0,00 do 0,99. |
| index | indeks ramki w skali od pierwszego indeksu ramki do ostatniego indeksu ramki. |
| sygnatura czasowa | Lokalizacja ramki w "Takty". |
| shotIndex | Indeks zrzutu nadrzędnego. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Przewodnik Szybki Start dotyczący moderowania zawartości i próbkowanie danych wyjściowych

### <a name="task-configuration-preset"></a>Konfiguracja zadania (ustawienie wstępne)
Podczas tworzenia zadania za pomocą **usługi Azure Media Content moderator**należy określić ustawienie wstępne konfiguracji. Poniższe ustawienia wstępne konfiguracji dotyczą moderowania zawartości.

```json
{
    "version":"2.0"
}
```

### <a name="net-code-sample"></a>Przykładowy kod platformy .NET

Poniższy przykład kodu platformy .NET używa zestawu SDK Media Services .NET do uruchomienia zadania Content Moderator. Pobiera zasób usługi Media Services jako dane wejściowe, które zawierają wideo do moderowania.
Zapoznaj się z [Content moderator wideo szybkiego startu](../../cognitive-services/Content-Moderator/video-moderation-api.md) dla kodu źródłowego i projektu programu Visual Studio.


```csharp
/// <summary>
/// Run the Content Moderator job on the designated Asset from local file or blob storage
/// </summary>
/// <param name="asset"></param>
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

Aby uzyskać pełny kod źródłowy i projekt programu Visual Studio, zapoznaj się z [przewodnikiem Szybki Start do Content moderator wideo](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### <a name="json-output"></a>Dane wyjściowe JSON

Poniższy przykład Content Moderator danych wyjściowych JSON został obcięty.

> [!NOTE]
> Lokalizacja klatki kluczowej w sekundach = sygnatura czasowa/Skala czasu

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

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Linki pokrewne
[Omówienie Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [moderowaniu wideo Content moderator i rozwiązaniu problemu](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Pobierz pełny kod źródłowy i projekt programu Visual Studio z [przewodnika Szybki Start dotyczącego moderowania wideo](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Dowiedz się, jak generować [Recenzje wideo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderowanych danych wyjściowych i [umiarkowane transkrypcje](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) w programie .NET.

Zapoznaj się z szczegółowym [samouczkiem dotyczącym moderowania wideo .NET i przeglądu](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
