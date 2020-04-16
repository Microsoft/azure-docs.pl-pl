---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: e39486f0daf1c865de3e53372d32a44fdcb8eb99
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399757"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy należy korzystać z następujących instrukcji:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu przycisku [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig), następnym krokiem [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)jest zainicjowanie pliku . Podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer), musisz przekazać go `speech_config`. Zapewnia to poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) powinno wyglądać:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) i `audioConfig` podać parametr podczas [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw należy dodać `using namespace` następującą `#include` instrukcję po definicjach.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Następnie będzie można odwoływać się `AudioConfig` do obiektu w następujący sposób:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig), zamiast wywoływania, `FromDefaultMicrophoneInput`wywołasz `FromWavFileOutput` i `filename` przekażesz parametr.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

Klasa [aparatu rozpoznawania](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) dla SDK mowy dla języka C++ udostępnia kilka metod, które można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego strzału (asynchowe) — wykonuje rozpoznawanie w trybie nieblokowym (asynchronimiacyjnym). Spowoduje to rozpoznanie pojedynczej wypowiedzi. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany.
* Ciągłe rozpoznawanie (asynchroniczne) — asynchronicznie inicjuje ciągłą operację rozpoznawania. Użytkownik musi połączyć się do obsługi zdarzenia, aby otrzymać wyniki rozpoznawania. Aby zatrzymać ciągłe rozpoznawanie asynchroniczne, należy wywołać . [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)

> [!NOTE]
> Dowiedz się więcej o [wyborze trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie jednostrzałowe

Oto przykład asynchroniiowego rozpoznawania pojedynczego [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)strzału za pomocą:

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Musisz napisać kod do obsługi wyniku. W tym przykładzie [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)ocenia się:

* Drukuje wynik rozpoznawania:`ResultReason::RecognizedSpeech`
* Jeśli nie ma dopasowania rozpoznawania, poinformuj o tym użytkownika:`ResultReason::NoMatch`
* Jeśli wystąpi błąd, wydrukuj komunikat o błędzie:`ResultReason::Canceled`

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

Ciągłe rozpoznawanie jest nieco bardziej zaangażowane niż rozpoznawanie pojedynczych strzałów. To wymaga, aby zapisać `Recognizing`się `Recognized`do `Canceled` , i wydarzenia, aby uzyskać wyniki uznania. Aby zatrzymać rozpoznawanie, należy wywołać [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Oto przykład ciągłego rozpoznawania na pliku wejściowym audio.

Zacznijmy od zdefiniowania danych wejściowych [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)i zainicjowania:

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Następnie utwórzmy zmienną do zarządzania stanem rozpoznawania mowy. Na początek ogłosimy `promise<void>`, ponieważ na początku uznania możemy bezpiecznie założyć, że to nie koniec.

```cpp
promise<void> recognitionEnd;
```

Zapiszemy się do wydarzeń wysłanych z . [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Sygnał dla zdarzeń zawierających wyniki pośredniego rozpoznawania.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący pomyślną próbę rozpoznania).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacja).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, alternatywnie, błąd transportu lub protokołu).

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

Z wszystko skonfigurowane, [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)możemy zadzwonić .

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Tryb dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

Aby włączyć tryb dyktowania, [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) użyj [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)tej metody w programie .

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Typowym zadaniem rozpoznawania mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na niemiecki. W kodzie znajdź [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)swój , a następnie dodaj ten wiersz bezpośrednio pod nim.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)jest parametrem, który przyjmuje ciąg jako argument. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość.

## <a name="improve-recognition-accuracy"></a>Większa dokładność rozpoznawania

Istnieje kilka sposobów, aby zwiększyć dokładność rozpoznawania za pomocą SDK mowy. Przyjrzyjmy się listom fraz. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja. Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

> [!IMPORTANT]
> Funkcja Lista fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) najpierw utwórz obiekt, a [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)następnie dodaj określone wyrazy i frazy za pomocą programu .

Wszelkie zmiany, które [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) mają obowiązywać przy następnym rozpoznawaniu lub po ponownym połączeniu z usługą mowy.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Jeśli chcesz wyczyścić listę fraz: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje w celu zwiększenia dokładności rozpoznawania

Listy fraz są tylko jedną z opcji, aby poprawić dokładność rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)