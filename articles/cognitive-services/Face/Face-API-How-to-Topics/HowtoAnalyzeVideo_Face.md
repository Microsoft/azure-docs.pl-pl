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
ms.custom: devx-track-csharp
ms.openlocfilehash: 730946a0c581be4697c0f45c8bdeb1d38f0ca23d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856392"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Przykład: jak analizować filmy wideo w czasie rzeczywistym

W przewodniku pokazano, jak można prowadzić analizę niemalże w czasie rzeczywistym na ramkach pobieranych ze strumienia wideo na żywo. Podstawowe składniki takiego systemu to:

- Uzyskanie ramek ze źródła wideo
- Wybór ramek do analizy
- Przesłanie wybranych ramek do interfejsu API
- Wykorzystanie wyniku każdej analizy, który jest zwracany z wywołania interfejsu API

Te przykłady są zapisywane w języku C#, a kod można znaleźć w witrynie GitHub tutaj: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) .

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

Ten kod umożliwia uruchamianie poszczególnych analiz w oddzielnych zadaniach, które działają w tle, gdy są przechwytywane klatki. Ta metoda umożliwia uniknięcie zablokowania głównego wątku podczas oczekiwania na zakończenie wywołania interfejsu API, lecz przy utracie niektórych gwarancji dawanych przez prostą wersję. Wiele wywołań interfejsu API może nastąpić równolegle, a wyniki mogą być zwracane w nieprawidłowej kolejności. Może to także spowodować równoczesne wprowadzenie wielu wątków do funkcji ConsumeResult(), co może okazać się ryzykowne, jeśli funkcja ta nie jest bezpieczna wątkowo. Wreszcie ten prosty kod nie śledzi tworzonych zadań, dlatego wyjątki nie będą zauważane. W związku z tym końcowym krokiem jest dodanie wątku „konsumenta”, który będzie śledzić zadania analizy, zgłaszać wyjątki, zabijać długo trwające zadania i gwarantować, że wyniki będą używane w odpowiedniej kolejności.

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

Aby skonfigurować i uruchomić aplikację tak szybko, jak to możliwe, użyjesz elastycznej implementacji systemu opisanej powyżej. Aby uzyskać dostęp do kodu, przejdź do [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) .

Biblioteka zawiera klasę FrameGrabber, która implementuje omówiony powyżej system producent — konsument, pozwalający na przetwarzanie ramek wideo z kamery internetowej. Użytkownik może określić dokładną formę wywołania interfejsu API, a klasa korzysta ze zdarzeń, aby przekazać do wywołującego kodu informację o tym, kiedy nowa ramka została pozyskana lub jest dostępny nowy wynik analizy.

Dwie przykładowe aplikacje korzystające z tej biblioteki ilustrują niektóre z tych możliwości. Pierwsza z nich to prosta aplikacja konsolowa, której uproszczoną wersję przedstawiono poniżej. Pokryją one ramki z domyślnej kamery internetowej i przesyła je do usługi kroju na potrzeby wykrywania.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/analyze.cs":::

Druga przykładowa aplikacja jest trochę bardziej interesująca i umożliwia wybranie, który interfejs API może przyjmować ramki wideo. Po lewej stronie aplikacja przedstawia podgląd wideo na żywo, a po prawej stronie pokazuje najnowszy wynik interfejsu API nałożony na odpowiadającą mu ramkę.

W większości trybów będzie występować widoczne opóźnienie między wideo prezentowanym na żywo po lewej stronie a wizualizowaną analizą po prawej stronie. To opóźnienie wynika z czasu potrzebnego na realizację wywołania interfejsu API. Jednym wyjątkiem jest tryb „EmotionsWithClientFaceDetect” (wykrywanie emocji na twarzy po stronie klienta), który przed przesłaniem jakichkolwiek obrazów do usług Cognitive Services przeprowadza lokalny proces wykrywania twarzy na komputerze klienckim za pomocą biblioteki OpenCV. W ten sposób natychmiast dokonujemy wizualizacji wykrytej twarzy, a następnie aktualizujemy emocje po zakończeniu wywołania interfejsu API. To jest przykład podejścia „hybrydowego”, w którym pewna część prostego przetwarzania może zostać wykonana przez klienta, a interfejsy API usług Cognitive Services mogą rozszerzyć jego wyniki, przeprowadzając bardziej zaawansowaną analizę w razie potrzeby.

![HowToAnalyzeVideo (Jak analizować wideo)](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrowanie z bazą kodu

Aby zacząć pracę z tym przykładem, wykonaj następujące kroki:

1. Utwórz [konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/). Jeśli masz już taki, możesz przejść do następnego kroku.
2. Utwórz zasoby dla przetwarzanie obrazów i w Azure Portal, aby uzyskać klucz i punkt końcowy. Upewnij się, że podczas instalacji wybrano warstwę bezpłatna (F0).
   - [Przetwarzanie obrazów](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Do rozpoznawania](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Po wdrożeniu zasobów kliknij pozycję **Przejdź do zasobu** , aby zebrać klucz i punkt końcowy dla każdego zasobu. 
3. Klonuj repozytorium [poznawcze-przykłady-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Otwórz przykład w programie Visual Studio i skompiluj i uruchom przykładowe aplikacje:
    - W przypadku BasicConsoleSample klucz czołowy jest zakodowany bezpośrednio w [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - W przypadku aplikacji LiveCameraSample klucze należy wprowadzić w okienku Ustawienia aplikacji. Zostaną one utrwalone pomiędzy sesjami jako dane użytkownika.
        

Gdy wszystko będzie gotowe do integracji, możesz **po prostu przywoływać bibliotekę VideoFrameAnalyzer z własnych projektów.** 

## <a name="summary"></a>Podsumowanie

W tym przewodniku pokazano, jak przeprowadzać analizę niemal w czasie rzeczywistym dla transmisji strumieniowych wideo na żywo przy użyciu interfejsów API rozpoznawania twarzy, przetwarzania obrazów i rozpoznawania emocji oraz przedstawiono, jak można wykorzystać przykładowy kod, aby rozpocząć pracę.

Możesz bezpłatnie przekazać Opinie i sugestie w [repozytorium GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) lub, aby uzyskać bardziej szczegółowe informacje o interfejsie API w naszej [witrynie UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Tematy pokrewne
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
