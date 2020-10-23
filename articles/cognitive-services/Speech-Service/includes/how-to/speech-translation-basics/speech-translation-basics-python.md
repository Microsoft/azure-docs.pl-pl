---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 987aad74401a11407e33f0b0bd51548f354bdd6d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92471000"
---
Jedną z podstawowych funkcji usługi mowy jest możliwość rozpoznawania mowy przez człowieka i przetłumaczenia jej na inne języki. W tym przewodniku szybki start dowiesz się, jak używać zestawu Speech SDK w aplikacjach i produktach do wykonywania translacji mowy o wysokiej jakości. Ten przewodnik Szybki Start obejmuje następujące tematy:

* Tłumaczenie zamiany mowy na tekst
* Tłumaczenie mowy na wiele języków docelowych
* Wykonywanie bezpośredniego tłumaczenia mowy na mowę

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) w języku Python w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od używanej platformy postępuj zgodnie z instrukcjami w sekcji <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">pobieranie zestawu <span class="docon docon-navigate-external x-hidden-focus"></span> Speech SDK</a> w artykule _Informacje o zestawie mowy SDK_ .

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy uwzględnić następujące `import` instrukcje w górnej części pliku kodu w języku Python.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Dane poufne i zmienne środowiskowe

Przykładowy kod źródłowy opisany w tym artykule zależy od zmiennych środowiskowych w celu przechowywania poufnych danych, takich jak klucz subskrypcji zasobów mowy i region. Plik kodu w języku Python zawiera dwie wartości, które są przypisane ze zmiennych środowiskowych komputerów-hostów, `SPEECH__SUBSCRIPTION__KEY` a mianowicie i `SPEECH__SERVICE__REGION` . Obie te zmienne znajdują się w zakresie globalnym, dzięki czemu są dostępne w definicji funkcji pliku kodu. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [zmienne środowiskowe i konfiguracja aplikacji](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Tworzenie konfiguracji tłumaczenia mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechTranslationConfig`][config] . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!TIP]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechTranslationConfig`][config] :

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

Przyjrzyjmy się w jaki sposób [`SpeechTranslationConfig`][config] jest tworzony przy użyciu klucza i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free).

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Zmień język źródłowy

Jednym z typowych zadań tłumaczenia mowy jest określenie języka danych wejściowych (lub źródłowych). Przyjrzyjmy się sposobom zmiany języka wejściowego na włoski. W kodzie można korzystać z [`SpeechTranslationConfig`][config] wystąpienia, przypisując do `speech_recognition_language` właściwości.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang]Właściwość oczekuje ciągu formatu ustawień regionalnych. Na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md)można podać dowolną wartość w kolumnie **Ustawienia regionalne** .

## <a name="add-translation-language"></a>Dodawanie języka tłumaczenia

Innym typowym zadaniem tłumaczenia mowy jest określenie docelowych języków tłumaczenia, ale co najmniej jeden jest wymagany, ale obsługiwane są różne. Poniższy fragment kodu ustawia francuski i niemiecki jako cele języka tłumaczenia.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

W przypadku każdego wywołania do programu [`add_target_language`][addlang] określony jest nowy docelowy język tłumaczenia. Innymi słowy, gdy rozpoznawanie mowy jest rozpoznawane w języku źródłowym, każde docelowe tłumaczenie jest dostępne jako część wyniku operacji tłumaczenia.

## <a name="initialize-a-translation-recognizer"></a>Inicjowanie aparatu rozpoznawania tłumaczenia

Po utworzeniu [`SpeechTranslationConfig`][config] , następnym krokiem jest zainicjowanie [`TranslationRecognizer`][recognizer] . Po zainicjowaniu elementu należy [`TranslationRecognizer`][recognizer] przekazać go `translation_config` . Obiekt Configuration zawiera poświadczenia wymagane przez usługę mowy do zweryfikowania Twojego żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, Oto jak [`TranslationRecognizer`][recognizer] powinien wyglądać:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Jeśli chcesz określić urządzenie wejściowe audio, należy utworzyć [`AudioConfig`][audioconfig] i podać `audio_config` parametr podczas inicjowania [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla wejściowego urządzenia audio](../../../how-to-select-audio-input-devices.md).

Najpierw należy odwołać się do `AudioConfig` obiektu w następujący sposób:

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

Jeśli chcesz podać plik audio zamiast używać mikrofonu, nadal musisz podać `audioConfig` . Jednak podczas tworzenia [`AudioConfig`][audioconfig] , zamiast wywoływania przy użyciu `use_default_microphone=True` , należy wywołać metodę `filename="path-to-file.wav"` i podać `filename` parametr.

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

Aby tłumaczyć mowę, zestaw Speech SDK polega na użyciu mikrofonu lub wejściowego pliku audio. Rozpoznawanie mowy występuje przed tłumaczeniem mowy. Po zainicjowaniu wszystkich obiektów wywołaj funkcję rozpoznawania-raz i uzyskaj wynik.

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

## <a name="synthesize-translations"></a>Synteza tłumaczeń

Po pomyślnym przeprowadzeniu rozpoznawania mowy i tłumaczeniu, wynik zawiera wszystkie tłumaczenia w słowniku. [`translations`][translations]Klucz słownika to docelowy język tłumaczenia, a wartość to przetłumaczony tekst. Rozpoznaną mowę można przetłumaczyć, a następnie wyróżnić w innym języku (zamiana mowy na mowę).

### <a name="event-based-synthesis"></a>Synteza oparta na zdarzeniach

`TranslationRecognizer`Obiekt ujawnia `Synthesizing` zdarzenie. Zdarzenie zostanie wyzwolone kilka razy i udostępnia mechanizm umożliwiający pobranie dźwięku z tłumaczenia z wyniku rozpoznawania. W przypadku tłumaczenia na wiele języków, zobacz [synteza ręczna](#manual-synthesis). Określ głos syntezy, przypisując [`voice_name`][voicename] i podając procedurę obsługi zdarzeń dla `Synthesizing` zdarzenia, Pobierz dźwięk. Poniższy przykład zapisuje przetłumaczony dźwięk jako plik *. wav* .

> [!IMPORTANT]
> Synteza oparta na zdarzeniach działa tylko w przypadku jednego tłumaczenia **, nie należy dodawać** wielu docelowych języków tłumaczenia. Ponadto [`voice_name`][voicename] powinien być to ten sam język, w którym znajduje się docelowy język tłumaczenia, na przykład `"de"` może być mapowany na `"de-DE-Hedda"` .

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

### <a name="manual-synthesis"></a>Synteza ręczna

[`translations`][translations]Słownik może służyć do syntezowania dźwięku z tekstu tłumaczenia. Wykonaj iterację każdego tłumaczenia i wytłumacz tłumaczenie. Podczas tworzenia `SpeechSynthesizer` wystąpienia `SpeechConfig` obiekt musi mieć [`speech_synthesis_voice_name`][speechsynthesisvoicename] ustawioną właściwość na żądany głos. W poniższym przykładzie przekładają się na pięć języków, a każde tłumaczenie jest następnie wydane do pliku audio w odpowiednim języku neuronowych.

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
