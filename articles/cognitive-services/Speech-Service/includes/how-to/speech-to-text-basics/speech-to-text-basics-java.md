---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 2de836a63eed79b7d166db220218e0c11ef11e70
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399770"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy należy korzystać z następujących instrukcji:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Środowisko wykonawcze Java<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu przycisku [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable), następnym krokiem [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)jest zainicjowanie pliku . Podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable), musisz przekazać go `config`. Zapewnia to poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) powinno wyglądać:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) i `audioConfig` podać parametr podczas [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw dodaj następujące `import` instrukcje.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Następnie będzie można odwoływać się `AudioConfig` do obiektu w następujący sposób:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable), zamiast wywoływania, `fromDefaultMicrophoneInput`wywołasz `fromWavFileOutput` i `filename` przekażesz parametr.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

Klasa [aparatu rozpoznawania](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) dla SDK mowy dla języka Java udostępnia kilka metod, które można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego strzału (asynchowe) — wykonuje rozpoznawanie w trybie nieblokowym (asynchronimiacyjnym). Spowoduje to rozpoznanie pojedynczej wypowiedzi. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany.
* Ciągłe rozpoznawanie (asynchroniczne) — asynchronicznie inicjuje ciągłą operację rozpoznawania. Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać. Aby zatrzymać ciągłe rozpoznawanie asynchroniczne, zadzwoń [do przystankuContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Dowiedz się więcej o [wyborze trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie jednostrzałowe

Oto przykład asynchroniiowego rozpoznawania pojedynczego [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)strzału za pomocą:

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Musisz napisać kod do obsługi wyniku. W tym przykładzie [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable)ocenia się:

* Drukuje wynik rozpoznawania:`ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania rozpoznawania, poinformuj o tym użytkownika:`ResultReason.NoMatch`
* Jeśli wystąpi błąd, wydrukuj komunikat o błędzie:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco bardziej zaangażowane niż rozpoznawanie pojedynczych strzałów. To wymaga, aby zapisać `recognizing`się `recognized`do `canceled` , i wydarzenia, aby uzyskać wyniki uznania. Aby zatrzymać rozpoznawanie, [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)należy wywołać . Oto przykład ciągłego rozpoznawania na pliku wejściowym audio.

Zacznijmy od zdefiniowania danych wejściowych [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)i zainicjowania:

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Następnie utwórzmy zmienną do zarządzania stanem rozpoznawania mowy. Aby rozpocząć, deklarujemy `Semaphore` w zakresie klasy.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Zapiszemy się do wydarzeń wysłanych z . [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Sygnał dla zdarzeń zawierających wyniki pośredniego rozpoznawania.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący pomyślną próbę rozpoznania).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacja).
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, alternatywnie, błąd transportu lub protokołu).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Z wszystko skonfigurowane, [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)możemy zadzwonić .

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Tryb dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

Aby włączyć tryb dyktowania, [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) użyj [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)tej metody w programie .

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Typowym zadaniem rozpoznawania mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na francuski. W kodzie znajdź [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)swój , a następnie dodaj ten wiersz bezpośrednio pod nim.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)jest parametrem, który przyjmuje ciąg jako argument. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość.

## <a name="improve-recognition-accuracy"></a>Większa dokładność rozpoznawania

Istnieje kilka sposobów, aby zwiększyć dokładność rozpoznawania za pomocą SDK mowy. Przyjrzyjmy się listom fraz. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja. Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

> [!IMPORTANT]
> Funkcja Lista fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) najpierw utwórz obiekt, a [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)następnie dodaj określone wyrazy i frazy za pomocą programu .

Wszelkie zmiany, które [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) mają obowiązywać przy następnym rozpoznawaniu lub po ponownym połączeniu z usługą mowy.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Jeśli chcesz wyczyścić listę fraz: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje w celu zwiększenia dokładności rozpoznawania

Listy fraz są tylko jedną z opcji, aby poprawić dokładność rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)