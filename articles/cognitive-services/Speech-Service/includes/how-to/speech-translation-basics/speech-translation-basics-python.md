---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399584"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy postępuj zgodnie z instrukcjami w sekcji <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Pobierz zestaw <span class="docon docon-navigate-external x-hidden-focus"></span> SDK mowy</a> w artykule Zestaw SDK mowy.

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady w tym artykule, dołącz następujące `import` instrukcje w górnej części pliku kodu języka Python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Dane wrażliwe i zmienne środowiskowe

Przykładowy kod źródłowy w tym artykule zależy od zmiennych środowiskowych do przechowywania poufnych danych, takich jak klucz subskrypcji zasobów mowy i regionu. Plik kodu języka Python zawiera dwie wartości, które są przypisane `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`ze zmiennych środowiskowych maszyn hosta, a mianowicie i . Obie te zmienne znajdują się w zakresie globalnym, dzięki czemu są dostępne w ramach definicji funkcji pliku kodu. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [zmienne środowiskowe i konfiguracja aplikacji](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Tworzenie konfiguracji tłumaczenia mowy

Aby wywołać usługę mowy przy użyciu SDK [`SpeechTranslationConfig`][config]mowy, należy utworzyć plik . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, host lub token autoryzacji.

> [!TIP]
> Niezależnie od tego, czy wykonujesz rozpoznawanie mowy, syntezę mowy, tłumaczenie czy rozpoznawanie intencji, zawsze tworzysz konfigurację.

Istnieje kilka sposobów, które można [`SpeechTranslationConfig`][config]zainicjować:

* Z subskrypcją: przekaż klucz i skojarzony region.
* Z punktem końcowym: przekazać w punkcie końcowym usługi mowy. Klucz lub token autoryzacji jest opcjonalny.
* Z hostem: przekaż adres hosta. Klucz lub token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: przekaż w tokenie autoryzacji i skojarzonym regionie.

Przyjrzyjmy się, [`SpeechTranslationConfig`][config] jak jest tworzony przy użyciu klucza i regionu. Zobacz stronę [pomocy technicznej regionu,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) aby znaleźć identyfikator regionu.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Jednym z typowych zadań tłumaczenia mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na włoski. W kodzie interakcji [`SpeechTranslationConfig`][config] z wystąpieniem, `speech_recognition_language` przypisując do właściwości.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

Właściwość [`speech_recognition_language`][recognitionlang] oczekuje ciąg formatu ustawień regionalnych języka. Dowolną wartość można podać w kolumnie **Ustawienia regionalne** na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="add-translation-language"></a>Dodawanie języka tłumaczenia

Innym typowym zadaniem tłumaczenia mowy jest określenie docelowych języków tłumaczenia, co najmniej jeden jest wymagany, ale obsługiwane są wielokrotności. We wliczeniu kodu: docelowych języka tłumaczeniowego zarówno w języku francuskim, jak i niemieckim.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Przy każdym [`add_target_language`][addlang]wywołaniu określono nowy docelowy język tłumaczenia. Innymi słowy, gdy mowa jest rozpoznawana z języka źródłowego, każde tłumaczenie docelowe jest dostępne jako część wynikowej operacji tłumaczenia.

## <a name="initialize-a-translation-recognizer"></a>Inicjowanie aparatu tłumaczenia

Po utworzeniu przycisku [`SpeechTranslationConfig`][config], następnym krokiem [`TranslationRecognizer`][recognizer]jest zainicjowanie pliku . Podczas inicjowania [`TranslationRecognizer`][recognizer], musisz przekazać go `translation_config`. Obiekt konfiguracji zawiera poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`TranslationRecognizer`][recognizer] powinno wyglądać:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`][audioconfig] i `audio_config` podać parametr podczas [`TranslationRecognizer`][recognizer]inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw odwołujesz się `AudioConfig` do obiektu w następujący sposób:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`][audioconfig], zamiast wywoływania `use_default_microphone=True`z , będziesz `filename="path-to-file.wav"` dzwonić `filename` i podać parametr.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Tłumaczenie mowy

Aby przetłumaczyć mowę, zestaw SDK mowy opiera się na wejściu mikrofonu lub pliku audio. Rozpoznawanie mowy występuje przed tłumaczeniem mowy. Po zainicjowaniu wszystkich obiektów wywołaj funkcję rozpoznawania i uzyskaj wynik.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Aby uzyskać więcej informacji na temat zamiany mowy na tekst, zobacz [podstawy rozpoznawania mowy](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetyzowanie tłumaczeń

Po pomyślnym rozpoznawaniu mowy i tłumaczeniu wynik zawiera wszystkie tłumaczenia w słowniku. Klucz [`translations`][translations] słownika jest językiem tłumaczenia docelowego, a wartością jest przetłumaczony tekst. Rozpoznaną mowę można przetłumaczyć, a następnie zsyntetyzować w innym języku (mowa na mowę).

### <a name="event-based-synthesis"></a>Synteza oparta na zdarzeniach

Obiekt `TranslationRecognizer` udostępnia `Synthesizing` zdarzenie. Zdarzenie jest uruchamiane kilka razy i zapewnia mechanizm pobierania syntetyzowanego dźwięku z wyniku rozpoznawania tłumaczenia. Jeśli tłumaczysz na wiele języków, zobacz [ręczna synteza](#manual-synthesis). Określ głos syntezy, przypisując [`voice_name`][voicename] i `Synthesizing` zapewnij program obsługi zdarzeń dla zdarzenia, pobierz dźwięk. Poniższy przykład zapisuje przetłumaczony dźwięk jako plik *wav.*

> [!IMPORTANT]
> Synteza oparta na zdarzeniach działa tylko z jednym tłumaczeniem, **nie** należy dodawać wielu języków tłumaczenia docelowego. Ponadto [`voice_name`][voicename] powinien być tym samym językiem co język tłumaczenia docelowego, na przykład; `"de"` może być `"de-DE-Hedda"`mapowana na .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Ręczna synteza

Słownik [`translations`][translations] może służyć do syntezy dźwięku z tekstu tłumaczenia. Iteracja każdego tłumaczenia i syntetyzowanie tłumaczenia. Podczas tworzenia `SpeechSynthesizer` wystąpienia `SpeechConfig` obiekt musi mieć [`speech_synthesis_voice_name`][speechsynthesisvoicename] jego właściwość ustawioną na żądany głos. Poniższy przykład tłumaczy na pięć języków, a każde tłumaczenie jest następnie syntetyzowane do pliku audio w odpowiednim języku neuronowym.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Aby uzyskać więcej informacji na temat syntezy mowy, zobacz [podstawy syntezy mowy](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
