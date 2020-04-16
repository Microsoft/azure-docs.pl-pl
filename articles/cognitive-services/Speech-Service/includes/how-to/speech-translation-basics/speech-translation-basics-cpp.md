---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399604"
---
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [wypróbuj bezpłatną usługę Mowy.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Zanim będzie można coś zrobić, musisz zainstalować pakiet SDK mowy. W zależności od platformy postępuj zgodnie z instrukcjami w sekcji <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Pobierz zestaw <span class="docon docon-navigate-external x-hidden-focus"></span> SDK mowy</a> w artykule Zestaw SDK mowy.

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady w tym artykule, należy uwzględnić następujące `#include` i `using` instrukcje w górnej części pliku kodu Języka C++.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Dane wrażliwe i zmienne środowiskowe

Przykładowy kod źródłowy w tym artykule zależy od zmiennych środowiskowych do przechowywania poufnych danych, takich jak klucz subskrypcji zasobów mowy i regionu. Plik kodu języka C++ zawiera dwie wartości ciągu przypisane ze zmiennych środowiska maszyn hosta, a mianowicie `SPEECH__SUBSCRIPTION__KEY` i `SPEECH__SERVICE__REGION`. Oba te pola znajdują się w zakresie klasy, dzięki czemu są dostępne w obrębie treści metody klasy. Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zobacz [zmienne środowiskowe i konfiguracja aplikacji](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Zmienianie języka źródłowego

Jednym z typowych zadań tłumaczenia mowy jest określenie języka wejściowego (lub źródłowego). Przyjrzyjmy się, jak można zmienić język wprowadzania na włoski. W kodzie interakcji [`SpeechTranslationConfig`][config] z wystąpieniem, wywołując `SetSpeechRecognitionLanguage` metodę.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

Właściwość [`SpeechRecognitionLanguage`][recognitionlang] oczekuje ciąg formatu ustawień regionalnych języka. Dowolną wartość można podać w kolumnie **Ustawienia regionalne** na liście obsługiwanych [ustawień regionalnych/języków](../../../language-support.md).

## <a name="add-translation-language"></a>Dodawanie języka tłumaczenia

Innym typowym zadaniem tłumaczenia mowy jest określenie docelowych języków tłumaczenia, co najmniej jeden jest wymagany, ale obsługiwane są wielokrotności. We wliczeniu kodu: docelowych języka tłumaczeniowego zarówno w języku francuskim, jak i niemieckim.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Przy każdym [`AddTargetLanguage`][addlang]wywołaniu określono nowy docelowy język tłumaczenia. Innymi słowy, gdy mowa jest rozpoznawana z języka źródłowego, każde tłumaczenie docelowe jest dostępne jako część wynikowej operacji tłumaczenia.

## <a name="initialize-a-translation-recognizer"></a>Inicjowanie aparatu tłumaczenia

Po utworzeniu przycisku [`SpeechTranslationConfig`][config], następnym krokiem [`TranslationRecognizer`][recognizer]jest zainicjowanie pliku . Podczas inicjowania [`TranslationRecognizer`][recognizer], musisz przekazać go `translationConfig`. Obiekt konfiguracji zawiera poświadczenia, które usługa mowy wymaga, aby sprawdzić poprawność żądania.

Jeśli rozpoznajesz mowę przy użyciu domyślnego mikrofonu urządzenia, oto jak [`TranslationRecognizer`][recognizer] powinno wyglądać:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Jeśli chcesz określić urządzenie wejściowe audio, musisz utworzyć [`AudioConfig`][audioconfig] i `audioConfig` podać parametr podczas [`TranslationRecognizer`][recognizer]inicjowania pliku .

> [!TIP]
> [Dowiedz się, jak uzyskać identyfikator urządzenia dla urządzenia wejściowego audio](../../../how-to-select-audio-input-devices.md).

Najpierw odwołujesz się `AudioConfig` do obiektu w następujący sposób:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Jeśli chcesz podać plik audio zamiast mikrofonu, nadal musisz podać plik `audioConfig`. Jednak podczas tworzenia [`AudioConfig`][audioconfig], zamiast wywoływania, `FromDefaultMicrophoneInput`wywołasz `FromWavFileInput` i `filename` przekażesz parametr.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Tłumaczenie mowy

Aby przetłumaczyć mowę, zestaw SDK mowy opiera się na wejściu mikrofonu lub pliku audio. Rozpoznawanie mowy występuje przed tłumaczeniem mowy. Po zainicjowaniu wszystkich obiektów wywołaj funkcję rozpoznawania i uzyskaj wynik.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Aby uzyskać więcej informacji na temat zamiany mowy na tekst, zobacz [podstawy rozpoznawania mowy](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetyzowanie tłumaczeń

Po pomyślnym rozpoznawaniu mowy i tłumaczeniu wynik zawiera wszystkie tłumaczenia w słowniku. Klucz [`Translations`][translations] słownika jest językiem tłumaczenia docelowego, a wartością jest przetłumaczony tekst. Rozpoznaną mowę można przetłumaczyć, a następnie zsyntetyzować w innym języku (mowa na mowę).

### <a name="event-based-synthesis"></a>Synteza oparta na zdarzeniach

Obiekt `TranslationRecognizer` udostępnia `Synthesizing` zdarzenie. Zdarzenie jest uruchamiane kilka razy i zapewnia mechanizm pobierania syntetyzowanego dźwięku z wyniku rozpoznawania tłumaczenia. Jeśli tłumaczysz na wiele języków, zobacz [ręczna synteza](#manual-synthesis). Określ głos syntezy, przypisując [`SetVoiceName`][voicename] i `Synthesizing` zapewnij program obsługi zdarzeń dla zdarzenia, pobierz dźwięk. Poniższy przykład zapisuje przetłumaczony dźwięk jako plik *wav.*

> [!IMPORTANT]
> Synteza oparta na zdarzeniach działa tylko z jednym tłumaczeniem, **nie** należy dodawać wielu języków tłumaczenia docelowego. Ponadto [`SetVoiceName`][voicename] powinien być tym samym językiem co język tłumaczenia docelowego, na przykład; `"de"` może być `"de-DE-Hedda"`mapowana na .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Ręczna synteza

Słownik [`Translations`][translations] może służyć do syntezy dźwięku z tekstu tłumaczenia. Iteracja każdego tłumaczenia i syntetyzowanie tłumaczenia. Podczas tworzenia `SpeechSynthesizer` wystąpienia `SpeechConfig` obiekt musi mieć [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] jego właściwość ustawioną na żądany głos. Poniższy przykład tłumaczy na pięć języków, a każde tłumaczenie jest następnie syntetyzowane do pliku audio w odpowiednim języku neuronowym.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Aby uzyskać więcej informacji na temat syntezy mowy, zobacz [podstawy syntezy mowy](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
