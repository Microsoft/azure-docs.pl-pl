---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: e39486f0daf1c865de3e53372d32a44fdcb8eb99
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399757"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od platformy należy wykonać następujące instrukcje:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">System<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Systemy<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig). Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig):

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się w jaki sposób [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) jest tworzony przy użyciu klucza i regionu. Aby znaleźć identyfikator regionu, zobacz stronę [Obsługa regionów](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) .

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig), następnym krokiem jest zainicjowanie [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer). Po zainicjowaniu [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)elementu należy przekazać go `speech_config`. Zapewnia to poświadczenia wymagane przez usługę mowy do zweryfikowania Twojego żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, Oto jak [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) powinien wyglądać:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Jeśli chcesz określić urządzenie wejściowe audio, należy utworzyć [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) i podać `audioConfig` parametr podczas inicjowania. [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).

Najpierw Dodaj następującą `using namespace` instrukcję po `#include` definicjach.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Następnie będzie można odwołać się do `AudioConfig` obiektu w następujący sposób:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audioConfig`. Jednak podczas [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)tworzenia, zamiast `FromDefaultMicrophoneInput`wywoływania, należy wywołać `FromWavFileOutput` i przekazać `filename` parametr.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

[Klasa aparatu rozpoznawania](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) dla zestawu Speech SDK dla języka C++ udostępnia kilka metod, których można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego zrzutu (Async) — wykonuje rozpoznawanie w trybie nieblokującym (asynchronicznym). Spowoduje to rozpoznanie pojedynczego wypowiedź. Koniec pojedynczej wypowiedź jest określany przez nasłuchiwanie na końcu lub do czasu przetworzenia maksymalnie 15 sekund.
* Stałe rozpoznawanie (asynchroniczne) — asynchronicznie Inicjuje operację ciągłego rozpoznawania. Użytkownik musi połączyć się ze zdarzeniem obsługi, aby otrzymać wyniki rozpoznawania. Aby zatrzymać asynchroniczne rozpoznawanie ciągłe, wywołaj [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync).

> [!NOTE]
> Dowiedz się więcej na temat [wybierania trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie pojedynczego zrzutu

Oto przykład asynchronicznego rozpoznawania pojedynczego zrzutu przy użyciu [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Musisz napisać kod, aby obsłużyć wynik. Ten przykład szacuje [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason):

* Drukuje wynik rozpoznawania:`ResultReason::RecognizedSpeech`
* Jeśli nie ma dopasowania do rozpoznawania, należy poinformować użytkownika:`ResultReason::NoMatch`
* Jeśli wystąpi błąd, Wydrukuj komunikat o błędzie:`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco większe niż w przypadku rozpoznawania pojedynczego zrzutu. Wymaga to subskrybowania zdarzeń `Recognizing`, `Recognized`i `Canceled` w celu uzyskania wyników rozpoznawania. Aby zatrzymać rozpoznawanie, należy wywołać [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Oto przykład sposobu ciągłego rozpoznawania w pliku wejściowym audio.

Zacznijmy od definiowania danych wejściowych i inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Następnie utwórz zmienną służącą do zarządzania stanem rozpoznawania mowy. Aby rozpocząć, zadeklarujemy `promise<void>`, ponieważ na początku rozpoznawania firma Microsoft może bezpiecznie założyć, że nie została zakończona.

```cpp
promise<void> recognitionEnd;
```

Zasubskrybujemy zdarzenia wysyłane z usługi [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Sygnał dla zdarzeń zawierających pośrednie wyniki rozpoznawania.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący na pomyślną próbę rozpoznania).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacji).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, Alternatywnie, błąd transportu lub protokołu).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Wszystko jest skonfigurowane, możemy wywoływać [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Tryb dyktowania

W przypadku korzystania z ciągłego rozpoznawania można włączyć przetwarzanie dyktowania przy użyciu odpowiedniej funkcji "Włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy interpretuje opisy wyrazów struktur zdań, takich jak interpunkcja. Na przykład "wypowiedź" czy "czy" na żywo "jest interpretowany jako tekst" czy jesteś w mieście? ".

Aby włączyć tryb dyktowania, użyj [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) metody w. [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Zmień język źródłowy

Typowym zadaniem rozpoznawania mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na niemiecki. Znajdź swój [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)kod, a następnie Dodaj ten wiersz bezpośrednio poniżej.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)jest parametrem, który przyjmuje ciąg jako argument. Możesz podać dowolną wartość na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Popraw dokładność rozpoznawania

Istnieje kilka sposobów na poprawienie dokładności rozpoznawania przy użyciu zestawu Speech SDK. Przyjrzyjmy się listom fraz. Listy fraz są używane do identyfikowania znanych fraz w danych audio, takich jak nazwa osoby lub określonej lokalizacji. Pojedyncze słowa lub kompletne wyrażenia można dodać do listy fraz. Podczas rozpoznawania jest używany wpis na liście frazy, jeśli dokładne dopasowanie dla całej frazy jest zawarte w dźwięku. Jeśli nie znaleziono dokładnego dopasowania do frazy, rozpoznawanie nie jest wspierane.

> [!IMPORTANT]
> Funkcja listy fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, najpierw Utwórz [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) obiekt, a następnie Dodaj określone słowa i frazy za [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)pomocą.

Wszelkie zmiany [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) zaczną obowiązywać przy następnym rozpoznaniu lub po ponownym połączeniu z usługą mowy.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Jeśli musisz wyczyścić listę fraz: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje poprawiające dokładność rozpoznawania

Listy fraz są tylko jedną opcją w celu zwiększenia dokładności rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)