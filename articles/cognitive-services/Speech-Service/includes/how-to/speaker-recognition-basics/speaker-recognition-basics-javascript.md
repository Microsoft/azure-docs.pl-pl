---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 5eb204da12b3c3405d52382285b5d75363f266cf
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875498"
---
W tym przewodniku szybki start przedstawiono podstawowe wzorce projektowe dla rozpoznawanie osoby mówiącej przy użyciu zestawu Speech SDK, w tym:

* Weryfikacja niezależna od tekstu i tekstu
* Identyfikacja osoby mówiącej, aby identyfikować przykład głosu między grupą głosów
* Usuwanie profilów głosowych

Aby zapoznać się z pojęciami dotyczącymi rozpoznawania mowy, zobacz artykuł z [omówieniem](../../../speaker-recognition-overview.md) .

## <a name="skip-to-samples-on-github"></a>Przejdź do przykładów w witrynie GitHub

Jeśli chcesz pominąć prosty kod przykładowy, zobacz [przykłady przewodnika Szybki Start dla języka JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznawanie osoby mówiącej jest obecnie obsługiwana *tylko* w zasobach usługi Azure Speech utworzonych w `westus` regionie.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności konieczne będzie zainstalowanie <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">zestawu Speech SDK dla języka JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. W zależności od platformy należy wykonać następujące instrukcje:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Przeglądarka sieci Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ponadto, w zależności od środowiska docelowego, użyj jednego z następujących elementów:

# <a name="script"></a>[napisy](#tab/script)

Pobierz i Wyodrębnij <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">zestaw Speech SDK dla <span class="docon docon-navigate-external x-hidden-focus"></span> </a> pliku *microsoft.cognitiveservices.speech.sdk.bundle.js* JavaScript i umieść go w folderze dostępnym dla pliku HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Jeśli masz dostęp do przeglądarki sieci Web i używasz `<script>` znacznika; `sdk` prefiks nie jest wymagany. `sdk`Prefiks jest aliasem używanym do nazwy `require` modułu.

# <a name="import"></a>[zaimportować](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Aby uzyskać więcej informacji na temat `import` , zobacz <a href="https://javascript.info/import-export" target="_blank">eksport <span class="docon docon-navigate-external x-hidden-focus"></span> i import </a>.

# <a name="require"></a>[wymagane](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Aby uzyskać więcej informacji na temat `require` , zobacz <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">co to <span class="docon docon-navigate-external x-hidden-focus"></span> jest wymagane? </a>.

---

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy dodać następujące instrukcje w górnej części pliku. js.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Te instrukcje służą do zaimportowania wymaganych bibliotek i pobrania klucza subskrypcji i regionu usługi rozpoznawania mowy ze zmiennych środowiskowych. Określają one również ścieżki do plików audio, które będą używane w ramach następujących zadań.

## <a name="create-helper-function"></a>Utwórz funkcję pomocnika

Dodaj następującą funkcję pomocnika, aby odczytywać pliki audio w strumieniach używanych przez usługę mowy.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

W tej funkcji należy użyć metod [AudioInputStream. createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest#createpushstream-audiostreamformat-) i [AudioConfig. fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) , aby utworzyć obiekt [AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) . Ten `AudioConfig` obiekt reprezentuje strumień audio. Podczas wykonywania poniższych zadań będziesz używać kilku z tych `AudioConfig` obiektów.

## <a name="text-dependent-verification"></a>Weryfikacja zależna od tekstu

Weryfikacja osoby mówiącej to czynność potwierdzająca, że prelegent pasuje do znanego lub **zarejestrowanego** głosu. Pierwszym krokiem jest **zarejestrowanie** profilu głosowego, dzięki czemu usługa ma coś do porównania przyszłych przykładów głosowych. W tym przykładzie należy zarejestrować profil przy użyciu strategii **zależnej od tekstu** , która wymaga określonego hasła do rejestracji i weryfikacji. Zapoznaj [się z](https://docs.microsoft.com/rest/api/speakerrecognition/) listą obsługiwanych hasła.

### <a name="textdependentverification-function"></a>TextDependentVerification, funkcja

Zacznij od utworzenia `TextDependentVerification` funkcji.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) za pomocą metody [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) . Należy zauważyć, że istnieją trzy [typy](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest) `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

W takim przypadku należy przekazać `VoiceProfileType.TextDependentVerification` do `VoiceProfileClient.createProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika, które zdefiniujesz dalej `AddEnrollmentsToTextDependentProfile` i `SpeakerVerify` . Na koniec Wywołaj [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) , aby usunąć profil.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile, funkcja

Zdefiniuj następującą funkcję, aby zarejestrować profil głosowy.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

W tej funkcji należy wywołać `GetAudioConfigFromFile` funkcję zdefiniowaną wcześniej, aby utworzyć `AudioConfig` obiekty z próbek audio. Te próbki audio zawierają hasło, takie jak "My Voice to My Passport, verify Me". Następnie należy zarejestrować te próbki audio przy użyciu metody [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

### <a name="speakerverify-function"></a>SpeakerVerify, funkcja

Zdefiniuj `SpeakerVerify` w następujący sposób.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

W tej funkcji utworzysz obiekt [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest) za pomocą metody [SpeakerVerificationModel. FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest#fromprofile-voiceprofile-) , przekazując do utworzonego wcześniej obiektu [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) .

Następnie należy wywołać metodę [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) , aby sprawdzić poprawność próbki audio, która zawiera to samo hasło co zarejestrowane wcześniej próbki audio. `SpeechRecognizer.recognizeOnceAsync` zwraca obiekt [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest) , którego `score` Właściwość zawiera wynik podobieństwa z zakresu od 0,0 do 1,0. `SpeakerRecognitionResult`Obiekt zawiera również `reason` Właściwość typu [ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest). Jeśli weryfikacja zakończyła się pomyślnie, `reason` Właściwość powinna mieć wartość `RecognizedSpeaker` .

## <a name="text-independent-verification"></a>Weryfikacja niezależna od tekstu

W przeciwieństwie do weryfikacji **zależnej od tekstu** , weryfikacja **niezależna od tekstu** :

* Nie wymaga wymawiania określonego hasła, co może być wypowiadane
* Nie wymaga trzech próbek audio *, ale wymaga* 20 s całkowitego dźwięku

### <a name="textindependentverification-function"></a>TextIndependentVerification, funkcja

Zacznij od utworzenia `TextIndependentVerification` funkcji.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Podobnie jak w przypadku `TextDependentVerification` funkcji, ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) za pomocą metody [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

W takim przypadku należy przekazać `VoiceProfileType.TextIndependentVerification` do `createProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika: `AddEnrollmentsToTextIndependentProfile` , które zdefiniujesz dalej, i `SpeakerVerify` zdefiniowane już. Na koniec Wywołaj [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) , aby usunąć profil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Zdefiniuj następującą funkcję, aby zarejestrować profil głosowy.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

W tej funkcji należy wywołać `GetAudioConfigFromFile` funkcję zdefiniowaną wcześniej, aby utworzyć `AudioConfig` obiekty z próbek audio. Następnie należy zarejestrować te próbki audio przy użyciu metody [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Identyfikacja osoby mówiącej służy do określenia **, kto** jest w danej grupie zarejestrowanych głosów. Proces jest podobny do **weryfikacji niezależnej od tekstu**, z główną różnicą, że można zweryfikować wiele profilów głosowych jednocześnie, zamiast weryfikować się w przypadku pojedynczego profilu.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification, funkcja

Zacznij od utworzenia `TextIndependentIdentification` funkcji.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Podobnie jak `TextDependentVerification` `TextIndependentVerification` funkcje i, ta funkcja tworzy obiekt [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) za pomocą metody [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

W takim przypadku należy przekazać `VoiceProfileType.TextIndependentIdentification` do `VoiceProfileClient.createProfileAsync` .

Następnie należy wywołać dwie funkcje pomocnika: `AddEnrollmentsToTextIndependentProfile` , które zostały już zdefiniowane, i `SpeakerIdentify` , które zdefiniujesz dalej. Na koniec Wywołaj [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) , aby usunąć profil.

### <a name="speakeridentify-function"></a>SpeakerIdentify, funkcja

Zdefiniuj `SpeakerIdentify` funkcję w następujący sposób.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

W tej funkcji utworzysz obiekt [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest) za pomocą metody [SpeakerIdentificationModel. fromProfiles](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest#fromprofiles-voiceprofile---) , przekazując do utworzonego wcześniej obiektu [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) .

Następnie należy wywołać metodę [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) i przekazać przykład audio.
`SpeechRecognizer.recognizeOnceAsync` próbuje zidentyfikować głos dla tego przykładu audio na podstawie `VoiceProfile` obiektów użytych do utworzenia `SpeakerIdentificationModel` . Zwraca obiekt [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest) , którego `profileId` Właściwość identyfikuje dopasowanie `VoiceProfile` , jeśli istnieje, podczas gdy `score` Właściwość zawiera wynik podobieństwa z zakresu od 0.0 do 1.0.

## <a name="main-function"></a>Funkcja Main

Na koniec Zdefiniuj `main` funkcję w następujący sposób.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Ta funkcja tworzy obiekt [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest) , który jest używany do tworzenia, rejestrowania i usuwania profilów głosowych. Następnie wywołuje wcześniej zdefiniowane funkcje.
