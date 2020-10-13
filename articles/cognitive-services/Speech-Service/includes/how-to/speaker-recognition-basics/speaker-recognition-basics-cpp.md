---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: dd23bf0528a27f599058271decbf1820084c9a43
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875469"
---
W tym przewodniku szybki start przedstawiono podstawowe wzorce projektowe dla rozpoznawanie osoby mówiącej przy użyciu zestawu Speech SDK, w tym:

* Weryfikacja niezależna od tekstu i tekstu
* Identyfikacja osoby mówiącej, aby identyfikować przykład głosu między grupą głosów
* Usuwanie profilów głosowych

Aby zapoznać się z pojęciami dotyczącymi rozpoznawania mowy, zobacz artykuł z [omówieniem](../../../speaker-recognition-overview.md) .

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start dla języka C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznawanie osoby mówiącej jest obecnie obsługiwana *tylko* w zasobach usługi Azure Speech utworzonych w `westus` regionie.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od platformy należy wykonać następujące instrukcje:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">System <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Systemy <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy dodać następujące instrukcje w górnej części pliku. cpp.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) . Ta klasa zawiera informacje o subskrypcji, takie jak klucz i skojarzony region, punkt końcowy, Host lub Token autoryzacji.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Weryfikacja zależna od tekstu

Weryfikacja osoby mówiącej to czynność potwierdzająca, że prelegent pasuje do znanego lub **zarejestrowanego** głosu. Pierwszym krokiem jest **zarejestrowanie** profilu głosowego, dzięki czemu usługa ma coś do porównania przyszłych przykładów głosowych. W tym przykładzie należy zarejestrować profil przy użyciu strategii **zależnej od tekstu** , która wymaga określonego hasła do rejestracji i weryfikacji. Zapoznaj [się z](https://docs.microsoft.com/rest/api/speakerrecognition/) listą obsługiwanych hasła.

### <a name="textdependentverification-function"></a>TextDependentVerification, funkcja

Zacznij od utworzenia `TextDependentVerification` funkcji.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofile) za pomocą metody [CreateProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) . Należy zauważyć, że istnieją trzy [typy](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

W takim przypadku przekazujesz `VoiceProfileType::TextDependentVerification` do `CreateProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika, które zdefiniujesz dalej `AddEnrollmentsToTextDependentProfile` i `SpeakerVerify` . Na koniec Wywołaj [DeleteProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) , aby wyczyścić profil.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile, funkcja

Zdefiniuj następującą funkcję, aby zarejestrować profil głosowy.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

W tej funkcji rejestrujesz próbki audio w `while` pętli, która śledzi liczbę pozostałych próbek i jest wymagana do rejestracji. W każdej iteracji [EnrollProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) prosi o wypowiedzenie hasła na mikrofon i dodanie przykładu do profilu głosowego.

### <a name="speakerverify-function"></a>SpeakerVerify, funkcja

Zdefiniuj `SpeakerVerify` w następujący sposób.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

W tej funkcji utworzysz obiekt [SpeakerVerificationModel](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerverificationmodel) za pomocą metody [SpeakerVerificationModel:: FromProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) , przekazując do utworzonego wcześniej obiektu [VoiceProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofile) .

Następnie [SpeechRecognizer:: RecognizeOnceAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) wyświetli prośbę o ponowne wypowiedzenie hasła, ale ten czas spowoduje jego zweryfikowanie względem Twojego profilu głosowego i zwrócenie wyniku podobieństwa z zakresu od 0.0 do 1,0. Obiekt [SpeakerRecognitionResult](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognitionresult) zwraca również wartość `Accept` lub `Reject` , w zależności od tego, czy hasło jest zgodne.

## <a name="text-independent-verification"></a>Weryfikacja niezależna od tekstu

W przeciwieństwie do weryfikacji **zależnej od tekstu** , weryfikacja **niezależna od tekstu** :

* Nie wymaga wymawiania określonego hasła, co może być wypowiadane
* Nie wymaga trzech próbek audio *, ale wymaga* 20 s całkowitego dźwięku

### <a name="textindependentverification-function"></a>TextIndependentVerification, funkcja

Zacznij od utworzenia `TextIndependentVerification` funkcji.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Podobnie jak w przypadku `TextDependentVerification` funkcji, ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofile) za pomocą metody [CreateProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) .

W takim przypadku przekazujesz `VoiceProfileType::TextIndependentVerification` do `CreateProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika: `AddEnrollmentsToTextIndependentProfile` , które zdefiniujesz dalej, i `SpeakerVerify` zdefiniowane już. Na koniec Wywołaj [DeleteProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) , aby wyczyścić profil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Zdefiniuj następującą funkcję, aby zarejestrować profil głosowy.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

W tej funkcji rejestrujesz próbki audio w `while` pętli, która śledzi liczbę sekund pozostałego dźwięku i jest wymagana do rejestracji. W każdej iteracji [EnrollProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) prosi o rozmowę z mikrofonu i dodaje przykład do profilu głosowego.

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Identyfikacja osoby mówiącej służy do określenia **, kto** jest w danej grupie zarejestrowanych głosów. Ten proces jest bardzo podobny do **weryfikacji niezależnej od tekstu**, z główną różnicą, którą można zweryfikować w wielu profilach głosowych jednocześnie, zamiast weryfikować się w odniesieniu do pojedynczego profilu.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification, funkcja

Zacznij od utworzenia `TextIndependentIdentification` funkcji.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Podobnie jak `TextDependentVerification` `TextIndependentVerification` funkcje i, ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofile) za pomocą metody [CreateProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) .

W takim przypadku przekazujesz `VoiceProfileType::TextIndependentIdentification` do `CreateProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika: `AddEnrollmentsToTextIndependentProfile` , które zostały już zdefiniowane, i `SpeakerIdentify` , które zdefiniujesz dalej. Na koniec Wywołaj [DeleteProfileAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) , aby wyczyścić profil.

### <a name="speakeridentify-function"></a>SpeakerIdentify, funkcja

Zdefiniuj `SpeakerIdentify` funkcję w następujący sposób.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

W tej funkcji utworzysz obiekt [SpeakerIdentificationModel](https://docs.microsoft.com/cpp/cognitive-services/speech/speakeridentificationmodel) za pomocą metody [SpeakerIdentificationModel:: FromProfiles](https://docs.microsoft.com/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles) . `SpeakerIdentificationModel::FromProfiles` akceptuje listę obiektów [VoiceProfile](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofile) . W takim przypadku wystarczy przekazać `VoiceProfile` utworzony wcześniej obiekt. Jeśli jednak chcesz, możesz przekazać wiele `VoiceProfile` obiektów, każdy zarejestrowany z próbkami audio z innego głosu.

Następnie [SpeechRecognizer:: RecognizeOnceAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) prosi o ponowne wypowiedzenie. Ten czas porównuje swój głos z zarejestrowanymi profilami głosowymi i zwraca najbardziej podobny profil głosowy.

## <a name="main-function"></a>Funkcja Main

Na koniec Zdefiniuj `main` funkcję w następujący sposób.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Ta funkcja po prostu wywołuje zdefiniowane wcześniej funkcje. Najpierw tworzy obiekt [VoiceProfileClient](https://docs.microsoft.com/cpp/cognitive-services/speech/voiceprofileclient) i obiekt [SpeakerRecognizer](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer) .

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

Służy `VoiceProfileClient` do tworzenia, rejestrowania i usuwania profilów głosowych. `SpeakerRecognizer`Służy do sprawdzania poprawności próbek mowy w odniesieniu do co najmniej jednego zarejestrowanego profilu głosowego.

## <a name="changing-audio-input-type"></a>Zmienianie typu wejścia audio

W przykładach w tym artykule użyto domyślnego mikrofonu urządzenia jako dane wejściowe dla przykładów audio. Jednak w scenariuszach, w których należy używać plików audio zamiast danych wejściowych mikrofonu, po prostu zmień następujący wiersz:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

na:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Lub Zastąp dowolne użycie `audio_config` with [audio:: AudioConfig:: FromWavFileInput](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Możesz również mieć mieszane dane wejściowe, korzystając z mikrofonu do rejestracji i weryfikowania plików na przykład.
