---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 1c58d285e77ace20531912ec150a9d44acde61b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399742"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono:

* Masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz i konto i subskrypcji - [Wypróbuj usługę mowy za darmo](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>Instalowanie i importowanie pakietu SDK mowy

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy.

```Python
pip install azure-cognitiveservices-speech
```

Jeśli korzystasz z systemu macOS i występują problemy z instalacją, może być konieczne uruchomienie tego polecenia w pierwszej kolejności.

```Python
python3 -m pip install --upgrade pip
```

Po zainstalowaniu zestawie SDK mowy zaimportuj go do projektu języka Python za pomocą tej instrukcji.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!NOTE]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Inicjowanie aparatu rozpoznawania

Po utworzeniu przycisku [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python), następnym krokiem [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)jest zainicjowanie pliku . Podczas inicjowania [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python), musisz przekazać go `speech_config`. Zapewnia to poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) powinno wyglądać:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) i `audio_config` podać parametr podczas [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audio_config`. Jednak podczas tworzenia [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python), zamiast podawania `device_name`, użyjesz `filename` parametru.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Rozpoznawanie mowy

Klasa [aparatu rozpoznawania](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) dla SDK mowy dla języka Python udostępnia kilka metod, które można użyć do rozpoznawania mowy.

* Rozpoznawanie pojedynczego strzału (synchronizacja) — wykonuje rozpoznawanie w trybie blokowania (synchroniczowego). Zwraca po rozpoznaniu pojedynczej wypowiedź. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany. Zadanie zwraca tekst rozpoznawania w wyniku.
* Rozpoznawanie pojedynczego strzału (asynchowe) — wykonuje rozpoznawanie w trybie nieblokowym (asynchronimiacyjnym). Spowoduje to rozpoznanie pojedynczej wypowiedzi. Koniec pojedynczego wypowiedź jest określana przez nasłuchiwanie wyciszenia na końcu lub do maksymalnie 15 sekund audio jest przetwarzany.
* Ciągłe rozpoznawanie (synchronizacja) — synchronicznie inicjuje ciągłe rozpoznawanie. Klient musi się `EventSignal` połączyć, aby otrzymać wyniki rozpoznawania. Aby zatrzymać rozpoznawanie, zadzwoń [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Ciągłe rozpoznawanie (asynchroniczne) — asynchronicznie inicjuje ciągłą operację rozpoznawania. Użytkownik musi połączyć się z EventSignal, aby otrzymać wyniki rozpoznawania. Aby zatrzymać ciągłe rozpoznawanie asynchroniczne, należy wywołać [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> Dowiedz się więcej o [wyborze trybu rozpoznawania mowy](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Rozpoznawanie jednostrzałowe

Oto przykład synchroniczowego rozpoznawania pojedynczego [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)strzału za pomocą:

```Python
result = speech_recognizer.recognize_once()
```

Oto przykład synchroniczowego rozpoznawania pojedynczego [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)strzału za pomocą:

```Python
result = speech_recognizer.recognize_once_async()
```

Niezależnie od tego, czy użyto metody synchroniczne lub asynchroniczne, należy napisać kod do iteracji przez wynik. W tym przykładzie [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)ocenia się:

* Drukuje wynik rozpoznawania:`speechsdk.ResultReason.RecognizedSpeech`
* Jeśli nie ma dopasowania rozpoznawania, poinformuj o tym użytkownika:`speechsdk.ResultReason.NoMatch `
* Jeśli wystąpi błąd, wydrukuj komunikat o błędzie:`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Ciągłe rozpoznawanie

Ciągłe rozpoznawanie jest nieco bardziej zaangażowane niż rozpoznawanie pojedynczych strzałów. Wymaga połączenia się z `EventSignal` wynikami rozpoznawania, a aby zatrzymać rozpoznawanie, należy wywołać [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) lub [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Oto przykład ciągłego rozpoznawania na pliku wejściowym audio.

Zacznijmy od zdefiniowania danych wejściowych [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)i zainicjowania:

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Następnie utwórzmy zmienną do zarządzania stanem rozpoznawania mowy. Na początek ustawimy to `False`na , ponieważ na początku uznania możemy bezpiecznie założyć, że to nie koniec.

```Python
done = False
```

Teraz utworzymy wywołanie zwrotne, aby zatrzymać `evt` ciągłe rozpoznawanie po odebraniu. Jest o tym kilka rzeczy, o których należy pamiętać.

* Po `evt` odebraniu `evt` wiadomość jest drukowana.
* Po `evt` odebraniu [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) jest wywoływana w celu zatrzymania rozpoznawania.
* Stan rozpoznawania zostanie `True`zmieniony na .

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

W tym przykładzie kodu pokazano, jak połączyć wywołania zwrotne ze zdarzeniami wysłanymi [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)z pliku .

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Sygnał dla zdarzeń zawierających wyniki pośredniego rozpoznawania.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Sygnał dla zdarzeń zawierających końcowe wyniki rozpoznawania (wskazujący pomyślną próbę rozpoznania).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Sygnał dla zdarzeń wskazujących początek sesji rozpoznawania (operacja).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Sygnał dla zdarzeń wskazujących koniec sesji rozpoznawania (operacja).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Sygnał dla zdarzeń zawierających anulowane wyniki rozpoznawania (wskazujący próbę rozpoznania, która została anulowana w wyniku lub bezpośrednie żądanie anulowania lub, alternatywnie, błąd transportu lub protokołu).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Z wszystko skonfigurowane, możemy zadzwonić [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Tryb dyktowania

Korzystając z ciągłego rozpoznawania, można włączyć przetwarzanie dyktowania za pomocą odpowiedniej funkcji "włącz dyktowanie". Ten tryb spowoduje, że wystąpienie konfiguracji mowy będzie interpretować opisy wyrazów struktur zdań, takich jak znaki interpunkcyjne. Na przykład wypowiedź "Czy mieszkasz w znaku zapytania miasta" będzie interpretowana jako tekst "Czy mieszkasz w mieście?".

Aby włączyć tryb dyktowania, [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) użyj [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)tej metody w programie .

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Typowym zadaniem rozpoznawania mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na niemiecki. W kodzie znajdź SpeechConfig, a następnie dodaj ten wiersz bezpośrednio pod nim.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)jest parametrem, który przyjmuje ciąg jako argument. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość.

## <a name="improve-recognition-accuracy"></a>Większa dokładność rozpoznawania

Istnieje kilka sposobów, aby zwiększyć dokładność rozpoznawania za pomocą SDK mowy. Przyjrzyjmy się listom fraz. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja. Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

> [!IMPORTANT]
> Funkcja Lista fraz jest dostępna tylko w języku angielskim.

Aby użyć listy fraz, [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) najpierw utwórz obiekt, a [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)następnie dodaj określone wyrazy i frazy za pomocą programu .

Wszelkie zmiany, które [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) mają obowiązywać przy następnym rozpoznawaniu lub po ponownym połączeniu z usługą mowy.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Jeśli chcesz wyczyścić listę fraz: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Inne opcje w celu zwiększenia dokładności rozpoznawania

Listy fraz są tylko jedną z opcji, aby poprawić dokładność rozpoznawania. Możesz również wykonać następujące czynności: 

* [Zwiększanie dokładności za pomocą mowy niestandardowej](../../../how-to-custom-speech.md)
* [Zwiększanie dokładności za pomocą modeli dzierżaw](../../../tutorial-tenant-model.md)