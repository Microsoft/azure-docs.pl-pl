---
title: 'Przykład: Analiza wideo w czasie rzeczywistym — do analizy'
titleSuffix: Azure Cognitive Services
description: Użyj usługi kroju, aby przeprowadzić analizę niemal w czasie rzeczywistym na klatkach pobranych z strumienia wideo na żywo.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ab3f596000216e8555bb84d0d47aff9a6e969eeb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169900"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Przykład: jak analizować filmy wideo w czasie rzeczywistym

W przewodniku pokazano, jak można prowadzić analizę niemalże w czasie rzeczywistym na ramkach pobieranych ze strumienia wideo na żywo. Podstawowe składniki takiego systemu to:

- Uzyskanie ramek ze źródła wideo
- Wybór ramek do analizy
- Przesłanie wybranych ramek do interfejsu API
- Wykorzystanie wyniku każdej analizy, który jest zwracany z wywołania interfejsu API

Te przykłady są zapisywane w języku C#, a kod można znaleźć w witrynie GitHub tutaj [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/):.

## <a name="the-approach"></a>Podejście

Istnieje wiele sposobów podejścia do kwestii uruchamiania analizy niemalże w czasie rzeczywistym dotyczącej strumieniowych transmisji wideo. Zaczniemy od zarysowania trzech z nich, poczynając od najmniej skomplikowanego.

### <a name="a-simple-approach"></a>Podejście proste

Najprostszy projekt systemu analizy niemalże w czasie rzeczywistym to nieskończona pętla, w ramach której w każdej iteracji ramka jest pobierana i analizowana, a następnie wynik jest wykorzystywany:

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

Jeśli nasza analiza opiera się na prostym algorytmie po stronie klienta, to podejście może być odpowiednie. Jednak jeśli analiza jest wykonywana w chmurze, związane z tym opóźnienie oznacza, że wywołanie interfejsu API może potrwać kilka sekund. W tym czasie obrazy nie są przechwytywane, a wątek w zasadzie nie robi nic. Maksymalna szybkość ramek w naszym przypadku jest ograniczona przez opóźnienia wywołań interfejsu API.

### <a name="parallelizing-api-calls"></a>Równoległe wywołania interfejsu API

Podczas gdy prosta jednowątkowa pętla ma sens dla uproszczonego algorytmu po stronie klienta, nie jest ona odpowiednia, gdy występuje opóźnienie związane z wywołaniami interfejsu API w chmurze. Rozwiązanie tego problemu polega na dopuszczeniu długo działających wywołań interfejsu API równolegle z przechwytywaniem ramki. W języku C# możemy uzyskać ten efekt przy użyciu równoległości opartej na zadaniach, na przykład:

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

Ten kod umożliwia uruchamianie poszczególnych analiz w oddzielnych zadaniach, które działają w tle, gdy są przechwytywane klatki. Ta metoda umożliwia uniknięcie zablokowania głównego wątku podczas oczekiwania na zakończenie wywołania interfejsu API, lecz przy utracie niektórych gwarancji dawanych przez prostą wersję. Wiele wywołań interfejsu API może nastąpić równolegle, a wyniki mogą być zwracane w nieprawidłowej kolejności. Może to także spowodować równoczesne wejście wielu wątków do funkcji ConsumeResult(), co może okazać się ryzykowne, jeśli funkcja ta nie jest bezpieczna wątkowo. Wreszcie ten prosty kod nie śledzi tworzonych zadań, dlatego wyjątki nie będą zauważane. W związku z tym końcowym krokiem jest dodanie wątku „konsumenta”, który będzie śledzić zadania analizy, zgłaszać wyjątki, zabijać długo trwające zadania i gwarantować, że wyniki będą używane w odpowiedniej kolejności.

### <a name="a-producer-consumer-design"></a>Wzorzec producent — konsument

W naszym docelowym systemie „producent — konsument” mamy wątek producenta, który wygląda podobnie do naszej poprzedniej nieskończonej pętli. Jednak zamiast natychmiastowego wykorzystywania wyników analizy zaraz po ich udostępnieniu producent po prostu umieszcza zadania w kolejce, co pozwala na ich śledzenie.

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

Mamy również wątek konsumenta, który pobiera zadania z kolejki, czeka na ich ukończenie, po czym wyświetla wynik lub zgłasza wyjątek. Dzięki zastosowaniu kolejki możemy mieć pewność, że wyniki są wykorzystywane pojedynczo, we właściwej kolejności i bez ograniczenia maksymalnej szybkości ramek systemu.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
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

## <a name="implementing-the-solution"></a>Implementowanie rozwiązania

### <a name="getting-started"></a>Getting Started

Aby skonfigurować i uruchomić aplikację tak szybko, jak to możliwe, użyjesz elastycznej implementacji systemu opisanej powyżej. Aby uzyskać dostęp do kodu, przejdź [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)do.

Biblioteka zawiera klasę FrameGrabber, która implementuje omówiony powyżej system producent — konsument, pozwalający na przetwarzanie ramek wideo z kamery internetowej. Użytkownik może określić dokładną formę wywołania interfejsu API, a klasa korzysta ze zdarzeń, aby przekazać do wywołującego kodu informację o tym, kiedy nowa ramka została pozyskana lub jest dostępny nowy wynik analizy.

Dwie przykładowe aplikacje korzystające z tej biblioteki ilustrują niektóre z tych możliwości. Pierwsza z nich to prosta aplikacja konsolowa, której uproszczoną wersję przedstawiono poniżej. Pokryją one ramki z domyślnej kamery internetowej i przesyła je do usługi kroju na potrzeby wykrywania.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Druga przykładowa aplikacja jest trochę bardziej interesująca i umożliwia wybranie, który interfejs API może przyjmować ramki wideo. Po lewej stronie aplikacja przedstawia podgląd wideo na żywo, a po prawej stronie pokazuje najnowszy wynik interfejsu API nałożony na odpowiadającą mu ramkę.

W większości trybów będzie występować widoczne opóźnienie między wideo prezentowanym na żywo po lewej stronie a wizualizowaną analizą po prawej stronie. To opóźnienie wynika z czasu potrzebnego na realizację wywołania interfejsu API. Jednym wyjątkiem jest tryb „EmotionsWithClientFaceDetect” (wykrywanie emocji na twarzy po stronie klienta), który przed przesłaniem jakichkolwiek obrazów do usług Cognitive Services przeprowadza lokalny proces wykrywania twarzy na komputerze klienckim za pomocą biblioteki OpenCV. W ten sposób natychmiast dokonujemy wizualizacji wykrytej twarzy, a następnie aktualizujemy emocje po zakończeniu wywołania interfejsu API. To jest przykład podejścia „hybrydowego”, w którym pewna część prostego przetwarzania może zostać wykonana przez klienta, a interfejsy API usług Cognitive Services mogą rozszerzyć jego wyniki, przeprowadzając bardziej zaawansowaną analizę w razie potrzeby.

![HowToAnalyzeVideo (Jak analizować wideo)](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrowanie z bazą kodu

Aby zacząć pracę z tym przykładem, wykonaj następujące kroki:

1. Pobierz z [Subskrypcji](https://azure.microsoft.com/try/cognitive-services/) klucze interfejsu API dla interfejsów API wizualizacji. Odpowiednimi interfejsami API do analizy ramek wideo są:
    - [Interfejs API przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Interfejs API rozpoznawania twarzy](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Sklonuj repozytorium GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Otwórz przykład w programie Visual Studio 2015, skompiluj i uruchom przykładowe aplikacje:
    - W przypadku BasicConsoleSample klucz czołowy jest zakodowany bezpośrednio w [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W przypadku aplikacji LiveCameraSample klucze należy wprowadzić w okienku Ustawienia aplikacji. Zostaną one utrwalone pomiędzy sesjami jako dane użytkownika.
        

Gdy wszystko będzie gotowe do integracji, możesz **po prostu przywoływać bibliotekę VideoFrameAnalyzer z własnych projektów.** 

## <a name="summary"></a>Podsumowanie

W tym przewodniku pokazano, jak przeprowadzać analizę niemal w czasie rzeczywistym dla transmisji strumieniowych wideo na żywo przy użyciu interfejsów API rozpoznawania twarzy, przetwarzania obrazów i rozpoznawania emocji oraz przedstawiono, jak można wykorzystać przykładowy kod, aby rozpocząć pracę. Pracę nad aplikacją możesz rozpocząć przy użyciu bezpłatnych kluczy interfejsu API na [stronie tworzenia konta usług Microsoft Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Zachęcamy do przekazywania opinii i sugestii w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) lub, w przypadku bardziej obszernych informacji zwrotnych dotyczących interfejsów API, w naszej [witrynie UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tematy pokrewne
- [How to Identify Faces in Image](HowtoIdentifyFacesinImage.md) (Jak identyfikować twarze na obrazie)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
