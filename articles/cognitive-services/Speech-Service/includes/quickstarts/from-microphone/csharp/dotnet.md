---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 871f992f6457a846d29a7145d53a7e382cbe10dd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400668"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu mowy platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom **program Visual Studio 2019**.
2. Załaduj swój projekt i otwórz *Program.cs*.

## <a name="source-code"></a>Kod źródłowy

Zastąp zawartość pliku *Program.cs* następującym kodem języka C#.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace Speech.Recognition
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();

            Console.WriteLine("Please press any key to continue...");
            Console.ReadLine();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config =
                SpeechConfig.FromSubscription(
                    "YourSubscriptionKey",
                    "YourServiceRegion");

            using var recognizer = new SpeechRecognizer(config);
            
            var result = await recognizer.RecognizeOnceAsync();
            switch (result.Reason)
            {
                case ResultReason.RecognizedSpeech:
                    Console.WriteLine($"We recognized: {result.Text}");
                    break;
                case ResultReason.NoMatch:
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    break;
                case ResultReason.Canceled:
                    var cancellation = CancellationDetails.FromResult(result);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                    }
                    break;
            }
        }
    }
}
```

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przejść do przebudowy aplikacji i przetestować funkcje rozpoznawania mowy za pomocą usługi Mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz pozycję **Build** > **Build Solution**.
2. **Uruchom aplikację** — na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij <kbd>klawisz F5</kbd>.
3. **Rozpocznij rozpoznawanie** - poprosi Cię o wymówienia frazy w języku angielskim. Mowa jest wysyłana do usługi Mowy, transkrybowana jako tekst i renderowana w konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
