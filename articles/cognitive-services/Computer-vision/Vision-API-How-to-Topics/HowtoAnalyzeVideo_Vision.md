---
title: Analizuj wideo niemal w czasie rzeczywistym — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić analizę niemal w czasie rzeczywistym na klatkach, które są pobierane z strumienia wideo na żywo przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933175"
---
# <a name="analyze-videos-in-near-real-time"></a>Analizuj wideo niemal w czasie rzeczywistym

W tym artykule pokazano, jak przeprowadzić analizę niemal w czasie rzeczywistym na klatkach, które są pobierane z strumienia wideo na żywo przy użyciu interfejs API przetwarzania obrazów. Podstawowe elementy takiej analizy są następujące:

- Pobieranie ramek ze źródła wideo.
- Wybieranie ramek do przeanalizowania.
- Przesyłanie tych ramek do interfejsu API.
- Zużywanie każdego wyniku analizy zwracanego z wywołania interfejsu API.

Przykłady w tym artykule są zapisywane w języku C#. Aby uzyskać dostęp do kodu, przejdź do strony [Przykładowa analiza klatek wideo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) w witrynie GitHub.

## <a name="approaches-to-running-near-real-time-analysis"></a>Podejścia do uruchamiania analizy niemal w czasie rzeczywistym

Można rozwiązać problem związany z analizą w czasie rzeczywistym strumieni wideo przy użyciu różnych metod. W tym artykule opisano trzy z nich, zwiększając poziomy złożoności.

### <a name="design-an-infinite-loop"></a>Zaprojektuj nieskończoną pętlę

Najprostszym projektem dla analizy niemal w czasie rzeczywistym jest nieskończona pętla. W każdej iteracji tej pętli można pobrać ramkę, przeanalizować ją, a następnie użyć wyniku:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Jeśli analiza była taka sama, jak w przypadku lekkich algorytmów po stronie klienta, to podejście byłoby odpowiednie. Jeśli jednak Analiza odbywa się w chmurze, wynikłe opóźnienie oznacza, że wywołanie interfejsu API może trwać kilka sekund. W tym czasie nie są przechwytywane obrazy, a wątek nie wykonuje żadnych operacji. Maksymalna szybkość klatek jest ograniczona przez opóźnienie wywołań interfejsu API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Zezwalaj na równoległe uruchamianie wywołań interfejsu API

Chociaż prosta pętla jednowątkowa ma sens dla lekkiego algorytmu po stronie klienta, nie pasuje do opóźnienia wywołania interfejsu API chmury. Rozwiązaniem tego problemu jest umożliwienie wykonywania długotrwałego wywołania interfejsu API równolegle z obsługą ramek. W języku C# można to zrobić za pomocą równoległości opartej na zadaniach. Na przykład można uruchomić następujący kod:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

W tym podejściu każda analiza zostanie uruchomiona w osobnym zadaniu. Zadanie można uruchomić w tle, podczas gdy będziesz kontynuować przeprowadzanie nowej ramki. Podejście to pozwala uniknąć blokowania wątku głównego podczas oczekiwania na zwrócenie wywołania interfejsu API. Jednak takie podejście może zaprezentować pewne wady:
* Koszt ten gwarantuje pewne gwarancje, które zapewnia prosta wersja. Oznacza to, że wiele wywołań interfejsu API może być wykonywanych równolegle, a wyniki mogą zostać zwrócone w niewłaściwej kolejności. 
* Może to również spowodować, że wiele wątków wprowadzi jednocześnie funkcję ConsumeResult (), która może być niebezpieczna, jeśli funkcja nie jest bezpieczna wątkowo. 
* Na koniec ten prosty kod nie śledzi zadań, które zostały utworzone, dlatego wyjątki dyskretnie znikają. W tym celu należy dodać wątek "konsument", który śledzi zadania analizy, podnosi wyjątki, kasuje zadania długotrwałe i gwarantuje, że wyniki są używane w odpowiedniej kolejności, po jednym naraz.

### <a name="design-a-producer-consumer-system"></a>Projektuj system dla konsumentów

W przypadku ostatecznego podejścia Projektowanie systemu "producent-odbiorca" tworzy wątek producenta, który wygląda podobnie do wcześniej wymienionej pętli nieskończonej. Jednak zamiast zużywać wyniki analizy natychmiast po ich udostępnieniu, producent po prostu umieszcza zadania w kolejce, aby śledzić je.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Tworzony jest również wątek odbiorcy, który przyjmuje zadania z kolejki, czeka na zakończenie, a następnie wyświetla wynik lub zgłasza wyjątek, który został zgłoszony. Korzystając z kolejki, można zagwarantować, że wyniki są używane pojedynczo w prawidłowej kolejności, bez ograniczania maksymalnej liczby klatek w systemie.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>Implementowanie rozwiązania

### <a name="get-started-quickly"></a>Szybkie rozpoczynanie pracy

Aby pomóc w rozpoczęciu i uruchomieniu aplikacji tak szybko, jak to możliwe, zaimplementowano system opisany w poprzedniej sekcji. Jest on wystarczająco elastyczny, aby pomieścić wiele scenariuszy, podczas gdy jest to łatwe w użyciu. Aby uzyskać dostęp do kodu, przejdź do strony [Przykładowa analiza klatek wideo](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) w witrynie GitHub.

Biblioteka zawiera `FrameGrabber` klasę, która implementuje wcześniej omawiany system konsumentów, aby przetwarzać ramki wideo z kamery internetowej. Użytkownicy mogą określić dokładną postać wywołania interfejsu API, a Klasa używa zdarzeń, aby pozwolić kod wywołujący, gdy nowa ramka zostanie pobrana lub gdy jest dostępny nowy wynik analizy.

Aby zilustrować niektóre z tych możliwości, oferujemy dwie przykładowe aplikacje, które korzystają z biblioteki. 

Pierwsza Przykładowa aplikacja to prosta aplikacja konsolowa, która pobiera ramki z domyślnej kamery internetowej, a następnie przesyła je do usługi kroju na potrzeby wykrywania. Uproszczona wersja aplikacji jest odtwarzana w następującym kodzie:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

Druga Przykładowa aplikacja jest nieco bardziej interesująca. Umożliwia wybranie interfejsu API, który ma być wywoływany w klatkach wideo. Po lewej stronie Aplikacja pokazuje podgląd wideo na żywo. Po prawej stronie jest nakładany wynik ostatniego interfejsu API w odpowiedniej ramce.

W większości trybów istnieje widoczne opóźnienie między wideo na żywo po lewej stronie i analizą wizualizacji po prawej stronie. To opóźnienie to czas potrzebny do wywołania interfejsu API. Wyjątek znajduje się w trybie "EmotionsWithClientFaceDetect", który służy do automatycznego wykrywania czołowego na komputerze klienckim przy użyciu OpenCV przed przesłaniem jakichkolwiek obrazów do usługi Azure Cognitive Services. 

Korzystając z tej metody, można natychmiast wizualizować wykryte działania. Po powrocie wywołania interfejsu API można później zaktualizować emocji. Pokazuje to, że podejście "hybrydowe". Oznacza to, że niektóre proste przetwarzanie można wykonać na kliencie, a następnie interfejsy API usług Cognitive Services może służyć do rozszerzania tego przetwarzania o bardziej zaawansowaną analizę, gdy jest to konieczne.

![Aplikacja LiveCameraSample wyświetlająca obraz ze znacznikami](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrowanie przykładów z bazą kodu

Aby rozpocząć pracę z tym przykładem, wykonaj następujące czynności:

1. Utwórz [konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/). Jeśli masz już taki, możesz przejść do następnego kroku.
2. Utwórz zasoby dla przetwarzanie obrazów i w Azure Portal, aby uzyskać klucz i punkt końcowy. Upewnij się, że podczas instalacji wybrano warstwę bezpłatna (F0).
   - [Przetwarzanie obrazów](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Do rozpoznawania](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Po wdrożeniu zasobów kliknij pozycję **Przejdź do zasobu** , aby zebrać klucz i punkt końcowy dla każdego zasobu. 
3. Klonuj repozytorium [poznawcze-przykłady-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Otwórz przykład w programie Visual Studio 2015 lub nowszym, a następnie Skompiluj i uruchom przykładowe aplikacje:
    - W przypadku BasicConsoleSample klucz czołowy jest zakodowany bezpośrednio w [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W polu LiveCameraSample wprowadź klucze w okienku **Ustawienia** aplikacji. Klucze są utrwalane w wielu sesjach jako dane użytkownika.

Gdy wszystko jest gotowe do integracji przykładów, odwołuje się do biblioteki VideoFrameAnalyzer z własnych projektów.

Funkcje obrazu, głosu, wideo i tekstu — VideoFrameAnalyzer używają platformy Cognitive Services Azure. Firma Microsoft odbiera obrazy, dźwięk, wideo i inne przekazywane dane (za pośrednictwem tej aplikacji) i mogą ich używać do celów poprawy jakości usług. Prosimy o pomoc w ochronie prywatności osób, których dane są wysyłane do usług Azure Cognitive Services przy użyciu aplikacji.

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono sposób uruchamiania analizy niemal w czasie rzeczywistym na żywo strumieni wideo przy użyciu usług front i przetwarzanie obrazów. Wiesz również, jak można użyć naszego przykładowego kodu, aby rozpocząć pracę.

Możesz bezpłatnie przekazać Opinie i sugestie w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Aby zapewnić szersze informacje zwrotne interfejsu API, przejdź do naszej [witryny UserVoice](https://cognitive.uservoice.com/).

