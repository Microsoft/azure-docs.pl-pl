---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 9d6d6ec36d5ab7d29b3050dafda5fd711b01f1f4
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108853"
---
W tym przewodniku szybki start przedstawiono typowe wzorce projektowania służące do wykonywania syntezy zamiany tekstu na mowę przy użyciu zestawu Speech SDK. Najpierw należy wykonać podstawowe czynności konfiguracyjne i synteza, a następnie przejść do bardziej zaawansowanych przykładów tworzenia aplikacji niestandardowych, takich jak:

* Uzyskiwanie odpowiedzi jako strumieni w pamięci
* Dostosowywanie szybkości próbkowania danych wyjściowych i szybkości transmisji bitów
* Przesyłanie żądań syntezy przy użyciu SSML (język oznaczeń syntezy mowy)
* Korzystanie z głosów neuronowych

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) w języku Python w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK.

```Python
pip install azure-cognitiveservices-speech
```

Jeśli pracujesz w programie macOS i masz problemy z instalacją, może być konieczne uruchomienie tego polecenia jako pierwsze.

```Python
python3 -m pip install --upgrade pip
```

Po zainstalowaniu zestawu Speech SDK należy uwzględnić następujące instrukcje importu u góry skryptu.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

> [!NOTE]
> Bez względu na to, czy wykonujesz rozpoznawanie mowy, synteza mowy, tłumaczenie czy rozpoznawanie intencji, zawsze utworzysz konfigurację.

Istnieje kilka sposobów na zainicjowanie [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) :

* Z subskrypcją: Przekaż klucz i skojarzony region.
* Z punktem końcowym: Pass w punkcie końcowym usługi mowy. Klucz lub Token autoryzacji jest opcjonalny.
* Z hostem: Przekaż adres hosta. Klucz lub Token autoryzacji jest opcjonalny.
* Z tokenem autoryzacji: Przekaż Token autoryzacji i skojarzony region.

W tym przykładzie utworzysz [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) przy użyciu klucza subskrypcji i regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../../../overview.md#try-the-speech-service-for-free).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Wyrównać mowę do pliku

Następnie utworzysz [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) obiekt, który wykonuje konwersje zamiany tekstu na mowę i wyjście na głośniki, pliki lub inne strumienie wyjściowe. [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer)Akceptuje jako params [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) obiekt utworzony w poprzednim kroku oraz [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) obiekt, który określa sposób obsługi wyników.

Aby rozpocząć, Utwórz element, `AudioOutputConfig` Aby automatycznie zapisywać dane wyjściowe do `.wav` pliku przy użyciu `filename` parametru konstruktora.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Następnie Utwórz wystąpienie a `SpeechSynthesizer` przez przekazanie `speech_config` obiektu i `audio_config` obiektu jako parametrów. Następnie wykonanie syntezy mowy i zapis w pliku jest tak proste jak uruchamianie `speak_text_async()` z ciągiem tekstu.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Uruchom program, a plik z syntezą `.wav` jest zapisywana w określonej lokalizacji. Jest to dobry przykład typowego użycia, ale następnym zapoznaj się z tematem dostosowywania danych wyjściowych i obsługi odpowiedzi wyjściowej jako strumienia znajdującego się w pamięci na potrzeby pracy z niestandardowymi scenariuszami.

## <a name="synthesize-to-speaker-output"></a>Synteza danych wyjściowych prezentera

W niektórych przypadkach można chcieć bezpośrednio wyprowadzać dane z głosu do osoby mówiącej. Aby to zrobić, użyj przykładu w poprzedniej sekcji, ale zmień go, `AudioOutputConfig` usuwając `filename` Parametry i ustaw `use_default_speaker=True` . To wyjście do bieżącego aktywnego urządzenia wyjściowego.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Pobierz wynik jako strumień w pamięci

W przypadku wielu scenariuszy tworzenia aplikacji mowy potrzebne są wyniki danych audio jako strumień w pamięci zamiast bezpośredniego zapisywania do pliku. Pozwoli to na tworzenie zachowań niestandardowych, w tym:

* Abstrakcyjna w efekcie tablica bajtów jako strumień umożliwiający wyszukiwanie niestandardowych usług podrzędnych.
* Zintegruj wynik z innymi interfejsami API lub usługami.
* Modyfikowanie danych audio, zapisywanie niestandardowych `.wav` nagłówków itp.

Jest to proste, aby wprowadzić tę zmianę z poprzedniego przykładu. Najpierw usuń `AudioConfig` , ponieważ będziesz zarządzać zachowaniem danych wyjściowych ręcznie z tego punktu, aby zwiększyć kontrolę. Następnie Przekaż `None` `AudioConfig` w `SpeechSynthesizer` konstruktorze.

> [!NOTE]
> W `None` przypadku `AudioConfig` , gdy nie zostanie pominięty w powyższym przykładzie danych wyjściowych prezentera, nie będzie odtwarzany dźwięk domyślnie na bieżącym aktywnym urządzeniu wyjściowym.

Tym razem można zapisać wynik w [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) zmiennej. `audio_data`Właściwość zawiera `bytes` obiekt danych wyjściowych. Możesz współpracować z tym obiektem ręcznie lub użyć [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) klasy do zarządzania strumieniem znajdującym się w pamięci. W tym przykładzie użyjesz `AudioDataStream` konstruktora, aby uzyskać strumień z wyniku.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

W tym miejscu można zaimplementować dowolne zachowanie niestandardowe przy użyciu `stream` obiektu wyniku.

## <a name="customize-audio-format"></a>Dostosuj format audio

W poniższej sekcji pokazano, jak dostosować atrybuty wyjściowe audio, w tym:

* Typ pliku audio
* Szybkość próbkowania
* Bit — Głębokość

Aby zmienić format dźwięku, należy użyć `set_speech_synthesis_output_format()` funkcji dla `SpeechConfig` obiektu. Ta funkcja oczekuje `enum` typu [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) , którego można użyć do wybrania formatu danych wyjściowych. [Listę dostępnych formatów audio](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) można znaleźć w dokumentacji referencyjnej.

Istnieją różne opcje dla różnych typów plików, w zależności od wymagań. Należy pamiętać, że zgodnie z definicją, formaty nieprzetworzone, takie jak `Raw24Khz16BitMonoPcm` nie obejmują nagłówków audio. Używaj formatów nieprzetworzonych tylko wtedy, gdy wiesz, że wdrożenie podrzędne może zdekodować surową Bitstream lub jeśli planujesz ręczne tworzenie nagłówków na podstawie głębi bitowej, szybkości próbkowania, liczby kanałów itd.

W tym przykładzie należy określić format RIFF o wysokiej wierności, `Riff24Khz16BitMonoPcm` ustawiając `SpeechSynthesisOutputFormat` dla `SpeechConfig` obiektu. Podobnie jak w przypadku przykładu w poprzedniej sekcji, należy użyć [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) , aby uzyskać strumień w pamięci wyniku, a następnie zapisać go do pliku.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Ponowne uruchomienie programu zapisze dostosowany `.wav` plik do określonej ścieżki.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Użyj SSML, aby dostosować charakterystykę mowy

Język znaczników syntezy mowy (SSML) umożliwia precyzyjne dostosowanie wielkości liter, wymowy, liczby głosu i większej liczby danych wyjściowych zamiany tekstu na mowę przez przesłanie żądań ze schematu XML. W tej sekcji przedstawiono przykład zmiany głosu, ale w celu uzyskania bardziej szczegółowego przewodnika można znaleźć w [artykule How to SSML](../../../speech-synthesis-markup.md).

Aby rozpocząć korzystanie z SSML do dostosowywania, należy wprowadzić prostą zmianę, która przełącza głos.
Najpierw utwórz nowy plik XML dla konfiguracji SSML w katalogu głównym projektu, w tym przykładzie `ssml.xml` . Element główny jest zawsze `<speak>` , a Zawijanie tekstu w `<voice>` elemencie pozwala na zmianę głosu przy użyciu `name` parametru. Zapoznaj się z [pełną listą](../../../language-support.md#neural-voices) obsługiwanych głosów **neuronowych** .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Następnie musisz zmienić żądanie syntezy mowy, aby odwołać się do pliku XML. Żądanie jest w większości takie samo, ale zamiast przy użyciu `speak_text_async()` funkcji, używasz `speak_ssml_async()` . Ta funkcja oczekuje ciągu XML, więc najpierw należy odczytać konfigurację SSML jako ciąg. W tym miejscu obiekt wynik jest dokładnie taki sam jak w poprzednich przykładach.

> [!NOTE]
> Jeśli element `ssml_string` zawiera `ï»¿` na początku ciągu, należy wyłączyć format BOM lub usługa zwróci błąd. W tym celu należy ustawić `encoding` parametr w następujący sposób: `open("ssml.xml", "r", encoding="utf-8-sig")` .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> Aby zmienić głos bez używania SSML, można ustawić właściwość przy `SpeechConfig` użyciu `SpeechConfig.speech_synthesis_voice_name = "en-US-AriaNeural"`

## <a name="get-facial-pose-events"></a>Pobierz zdarzenia ułożenia twarzy

Funkcja mowy może być dobrym sposobem na przetwarzanie animacji wyrażeń twarzy.
Często [visemes](../../../how-to-speech-synthesis-viseme.md) są używane do reprezentowania kluczowych elementów w zaobserwowanej mowę, takich jak pozycja pakietów lip, szczęki i języka podczas tworzenia określonego fonem.
Możesz subskrybować zdarzenie viseme w zestawie mowy SDK.
Następnie można zastosować zdarzenia viseme w celu animowania kroju znaku jako odtwarzania dźwięku mowy.
Dowiedz się [, jak uzyskać zdarzenia viseme](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
