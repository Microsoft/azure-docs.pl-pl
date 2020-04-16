---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d9bc744292922fd127e983392199fa21554d3c62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400561"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `Program.cs`projekt i otwórz .
3. Pobierz <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> i dodaj go do swojego projektu.
    - Zapisz plik *whatstheweatherlike.wav* obok `Program.cs` pliku.
    - W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt wybierz pozycję **Dodaj > istniejący element**.
    - Wybierz plik *whatstheweatherlike.wav,* a następnie wybierz przycisk **Dodaj.**
    - Kliknij prawym przyciskiem myszy nowo dodany plik, wybierz polecenie **Właściwości**.
    - Zmień **katalog kopiowania na katalog wyjściowy,** aby **zawsze kopiować**.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu. Należy pamiętać, że utworzono metodę `RecognizeSpeechAsync()`asynchronizową o nazwie .

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem `SpeechRecognizer` obiektu należy utworzyć konfigurację, która używa klucza subskrypcji i regionu subskrypcji. Wstaw ten `RecognizeSpeechAsync()` kod w metodzie.

> [!NOTE]
> W tym przykładzie `FromSubscription()` użyto `SpeechConfig`metody do utworzenia pliku . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> SDK mowy domyślnie rozpoznaje przy użyciu en-us dla języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Tworzenie konfiguracji audio

Teraz musisz utworzyć obiekt, `AudioConfig` który wskazuje plik audio. Ten obiekt jest tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `RecognizeSpeechAsync()` kod w metodzie, tuż poniżej konfiguracji mowy.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Inicjowanie funkcji SpeechRecognizer

Teraz utwórzmy obiekt `SpeechRecognizer` przy `SpeechConfig` użyciu `AudioConfig` obiektów i obiektów utworzonych wcześniej. Ten obiekt jest również tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `RecognizeSpeechAsync()` kod w metodzie, wewnątrz ```AudioConfig``` using instrukcji, która zawija obiekt.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Rozpoznawanie frazy

Z `SpeechRecognizer` obiektu, masz zamiar wywołać `RecognizeOnceAsync()` metodę. Ta metoda pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy.

Wewnątrz using instrukcji, dodaj ten kod:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy wynik rozpoznawania jest zwracany przez usługę mowy, należy coś z nim zrobić. Będziemy to proste i wydrukować wynik do konsoli.

Wewnątrz using instrukcji, `RecognizeOnceAsync()`poniżej, dodaj ten kod:

```csharp
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
```

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
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
}
```

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

1. Skompiluj kod: Na pasku menu *programu Visual Studio*wybierz pozycję **Build** > **Build Solution**.
2. Uruchom aplikację: na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij **klawisz F5**.
3. Rozpocznij rozpoznawanie: Plik audio jest wysyłany do usługi Mowy, przepisywał jako tekst i renderował w konsoli.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
