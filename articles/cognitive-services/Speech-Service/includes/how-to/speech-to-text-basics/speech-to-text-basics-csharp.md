---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 982c3c6011936c184c55dd92a76d4aec023baaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399733"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy należy korzystać z następujących instrukcji:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">Rdzeń .NET<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Jedność<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">Uwp<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu przycisku [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet), następnym krokiem [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)jest zainicjowanie pliku . Podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet), musisz przekazać go `speechConfig`. Zapewnia to poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) powinno wyglądać:

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) i `audioConfig` podać parametr podczas [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw dodaj następującą `using` instrukcję.

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

Następnie będzie można odwoływać się `AudioConfig` do obiektu w następujący sposób:

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet), zamiast wywoływania, `FromDefaultMicrophoneInput`wywołasz `FromWavFileOutput` i `filename` przekażesz parametr.


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

Klasa [aparatu rozpoznawania](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) dla SDK mowy dla języka C# udostępnia kilka metod, które można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego strzału (asynchowe) — wykonuje rozpoznawanie w trybie nieblokowym (asynchronimiacyjnym). Spowoduje to rozpoznanie pojedynczej wypowiedzi. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany.
* Ciągłe rozpoznawanie (asynchroniczne) — asynchronicznie inicjuje ciągłą operację rozpoznawania. Użytkownik rejestruje się do zdarzeń i obsługuje różne stany aplikacji. Aby zatrzymać ciągłe rozpoznawanie asynchroniczne, należy wywołać . [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)

> [!NOTE]
> Dowiedz się więcej o [wyborze trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie jednostrzałowe

Oto przykład asynchroniiowego rozpoznawania pojedynczego [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet)strzału za pomocą:

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

Musisz napisać kod do obsługi wyniku. W tym przykładzie [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet)ocenia się:

* Drukuje wynik rozpoznawania:`ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania rozpoznawania, poinformuj o tym użytkownika:`ResultReason.NoMatch`
* Jeśli wystąpi błąd, wydrukuj komunikat o błędzie:`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
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

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco bardziej zaangażowane niż rozpoznawanie pojedynczych strzałów. To wymaga, aby zapisać `Recognizing`się `Recognized`do `Canceled` , i wydarzenia, aby uzyskać wyniki uznania. Aby zatrzymać rozpoznawanie, [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)należy wywołać . Oto przykład ciągłego rozpoznawania na pliku wejściowym audio.

Zacznijmy od zdefiniowania danych wejściowych [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)i zainicjowania:

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
Następnie utwórzmy zmienną do zarządzania stanem rozpoznawania mowy. Aby rozpocząć, deklarujemy `TaskCompletionSource<int>` po poprzednich deklaracji.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Zapiszemy się do wydarzeń wysłanych z . [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet): Sygnał dla zdarzeń zawierających wyniki pośredniego rozpoznawania.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący pomyślną próbę rozpoznania).
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacja).
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, alternatywnie, błąd transportu lub protokołu).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Z wszystko skonfigurowane, [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)możemy zadzwonić .

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Tryb dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

Aby włączyć tryb dyktowania, [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet) użyj [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)tej metody w programie .

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Typowym zadaniem rozpoznawania mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na włoski. W kodzie znajdź [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)swój , a następnie dodaj ten wiersz bezpośrednio pod nim.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

Właściwość [`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) oczekuje ciąg formatu ustawień regionalnych języka. Dowolną wartość można podać w kolumnie **Ustawienia regionalne** na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Większa dokładność rozpoznawania

Istnieje kilka sposobów, aby zwiększyć dokładność rozpoznawania za pomocą SDK mowy. Przyjrzyjmy się listom fraz. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja. Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

> [!IMPORTANT]
> Funkcja Lista fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) najpierw utwórz obiekt, a [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet)następnie dodaj określone wyrazy i frazy za pomocą programu .

Wszelkie zmiany, które [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) mają obowiązywać przy następnym rozpoznawaniu lub po ponownym połączeniu z usługą mowy.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Jeśli chcesz wyczyścić listę fraz: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje w celu zwiększenia dokładności rozpoznawania

Listy fraz są tylko jedną z opcji, aby poprawić dokładność rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)