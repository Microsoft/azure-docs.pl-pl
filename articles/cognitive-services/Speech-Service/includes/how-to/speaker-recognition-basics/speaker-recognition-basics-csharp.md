---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: cd2bb595df91597059131b1dd7274e1a7ba36066
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875495"
---
W tym przewodniku szybki start przedstawiono podstawowe wzorce projektowe dla rozpoznawanie osoby mówiącej przy użyciu zestawu Speech SDK, w tym:

* Weryfikacja niezależna od tekstu i tekstu
* Identyfikacja osoby mówiącej, aby identyfikować przykład głosu między grupą głosów
* Usuwanie profilów głosowych

Aby zapoznać się z pojęciami dotyczącymi rozpoznawania mowy, zobacz artykuł z [omówieniem](../../../speaker-recognition-overview.md) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Rozpoznawanie osoby mówiącej jest obecnie obsługiwana *tylko* w zasobach usługi Azure Speech utworzonych w `westus` regionie.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

Przed wykonaniem jakichkolwiek czynności należy zainstalować zestaw Speech SDK. W zależności od platformy należy wykonać następujące instrukcje:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">PLATFORMY UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Importowanie zależności

Aby uruchomić przykłady z tego artykułu, należy uwzględnić następujące `using` instrukcje w górnej części skryptu.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby wywołać usługę mowy przy użyciu zestawu Speech SDK, należy utworzyć [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) . W tym przykładzie utworzysz [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) przy użyciu klucza subskrypcji i regionu. Utworzysz również podstawowy kod standardowy do użycia w pozostałej części tego artykułu, który można modyfikować w celu dostosowania.

Należy pamiętać, że region jest ustawiony na `westus` , ponieważ jest to jedyny obsługiwany region usługi.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Weryfikacja zależna od tekstu

Weryfikacja osoby mówiącej to czynność potwierdzająca, że prelegent pasuje do znanego lub **zarejestrowanego** głosu. Pierwszym krokiem jest **zarejestrowanie** profilu głosowego, dzięki czemu usługa ma coś do porównania przyszłych przykładów głosowych. W tym przykładzie należy zarejestrować profil przy użyciu strategii **zależnej od tekstu** , która wymaga określonego potwierdzenia do użycia zarówno do rejestracji, jak i weryfikacji. Listę obsługiwanych fraz z przekazywaniem można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/rest/api/speakerrecognition/) .

Zacznij od utworzenia następującej funkcji w klasie w `Program` celu zarejestrowania profilu głosowego.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

W tej funkcji program `await client.CreateProfileAsync()` tworzy nowy profil głosowy. Po utworzeniu należy określić sposób wprowadzania próbek audio, korzystając `AudioConfig.FromDefaultMicrophoneInput()` z tego przykładu, aby przechwytywać dźwięk z domyślnego urządzenia wejściowego. Następnie rejestrujesz próbki audio w `while` pętli, która śledzi liczbę pozostałych próbek i wymagane do rejestracji. W każdej iteracji program `client.EnrollProfileAsync(profile, audioInput)` wyświetli monit o podyktowanie frazy do mikrofonu i dodanie przykładu do profilu głosowego.

Po zakończeniu rejestracji będziesz mieć `await SpeakerVerify(config, profile, profileMapping)` możliwość zweryfikowania względem profilu, który właśnie został utworzony. Dodaj kolejną funkcję do zdefiniowania `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

W tej funkcji przekazujesz `VoiceProfile` właśnie utworzony obiekt, aby zainicjować model do zweryfikowania. Następnie zostanie `await speakerRecognizer.RecognizeOnceAsync(model)` wyświetlony komunikat z prośbą o ponowne wypowiedzenie hasła, ale ten czas spowoduje jego zweryfikowanie względem Twojego profilu głosowego i zwrócenie wyniku podobieństwa z zakresu od 0.0 do 1.0. `result`Obiekt zwraca również `Accept` lub `Reject` , w zależności od tego, czy fraza dopasuje, czy nie.

Następnie zmodyfikuj funkcję, `Main()` Aby wywołać nowo utworzone funkcje. Ponadto należy pamiętać, że tworzysz, `Dictionary<string, string>` Aby przekazać odwołanie przez wywołania funkcji. Przyczyną tego problemu jest to, że usługa nie zezwala na przechowywanie nazwy, którą można odczytać przez człowieka `VoiceProfile` , i przechowuje tylko numer identyfikacyjny do celów związanych z ochroną prywatności. W `VerificationEnroll` funkcji należy dodać do tego słownika wpis z nowo utworzonym identyfikatorem wraz z nazwą tekstu. W scenariuszach projektowania aplikacji, w których należy wyświetlić nazwę, którą można odczytać przez człowieka, **należy zapisać to mapowanie w innym miejscu, ponieważ usługa nie będzie mogła go zapisać**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Uruchom skrypt, a zostanie wyświetlony monit o wypowiedzenie frazy *mój głos to My Passport, zweryfikuj mnie* trzy razy, aby przeprowadzić rejestrację i jeden dodatkowy czas na weryfikację. Zwrócony wynik to wynik podobieństwa, którego można użyć do utworzenia własnych progów niestandardowych do weryfikacji.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Weryfikacja niezależna od tekstu

W przeciwieństwie do weryfikacji **zależnej od tekstu** , weryfikacja **niezależna od tekstu** :

* Nie wymaga wymawiania określonych fraz.
* Nie wymaga trzech próbek audio *, ale wymaga* 20-sekund całkowitego dźwięku

Wprowadź kilka prostych zmian w `VerificationEnroll` funkcji, aby przełączyć się na weryfikację **niezależną od tekstu** . Najpierw należy zmienić typ weryfikacji na `VoiceProfileType.TextIndependentVerification` . Następnie zmień `while` pętlę na śledzenie `result.RemainingEnrollmentsSpeechLength` , która będzie w dalszym ciągu monitował o porozmawianie do momentu przechwycenia 20 sekund nagrania audio.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Ponownie uruchom program i zagłosuj wszystko w fazie weryfikacji, ponieważ nie jest wymagane wyrażenie "Pass-phrase". Ponownie wynik podobieństwa jest zwracany.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Identyfikacja osoby mówiącej służy do określenia **, kto** jest w danej grupie zarejestrowanych głosów. Ten proces jest bardzo podobny do **weryfikacji niezależnej od tekstu**, z główną różnicą, którą można zweryfikować w wielu profilach głosowych jednocześnie, zamiast weryfikować się w odniesieniu do pojedynczego profilu.

Utwórz funkcję `IdentificationEnroll` do rejestracji wielu profilów głosowych. Proces rejestracji dla każdego profilu jest taki sam jak proces rejestracji dla **weryfikacji niezależnej od tekstu**i wymaga 20 sekund audio dla każdego profilu. Ta funkcja akceptuje listę ciągów i utworzy `profileNames` Nowy profil głosowy dla każdej nazwy na liście. Funkcja zwraca listę `VoiceProfile` obiektów, które są używane w następnej funkcji do identyfikowania prelegenta.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Utwórz następującą funkcję, `SpeakerIdentification` Aby przesłać żądanie identyfikacji. Główną różnicą tej funkcji w porównaniu do żądania **weryfikacji głośników** jest użycie `SpeakerIdentificationModel.FromProfiles()` , które akceptuje listę `VoiceProfile` obiektów. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Zmień `Main()` funkcję na poniżej. Tworzysz listę ciągów `profileNames` , które są przekazywane do `IdentificationEnroll()` funkcji. Spowoduje to wyświetlenie monitu o utworzenie nowego profilu głosowego dla każdej nazwy na tej liście, aby można było dodać więcej nazw w celu utworzenia dodatkowych profilów dla znajomych lub współpracowników.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Uruchom skrypt, a zostanie wyświetlony monit o zarejestrowanie przykładów głosu dla pierwszego profilu. Po zakończeniu rejestracji zostanie wyświetlony monit o powtórzenie tego procesu dla każdej nazwy na liście `profileNames` . Po zakończeniu każdej rejestracji zostanie wyświetlony monit o poznanie **wszystkich użytkowników** , a usługa podejmie próbę zidentyfikowania tej osoby spośród zarejestrowanych profilów głosowych.

Ten przykład zwraca tylko najbliższe dopasowanie i jego wynik podobieństwa, ale można uzyskać pełną odpowiedź obejmującą pięć najważniejszych wyników podobieństwa, dodając `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` do `SpeakerIdentification` funkcji.

## <a name="changing-audio-input-type"></a>Zmienianie typu wejścia audio

W przykładach w tym artykule użyto domyślnego mikrofonu urządzenia jako dane wejściowe dla przykładów audio. Jednak w scenariuszach, w których należy używać plików audio zamiast danych wejściowych mikrofonu, po prostu zmień dowolne wystąpienie na, aby `AudioConfig.FromDefaultMicrophoneInput()` `AudioConfig.FromWavFileInput(path/to/your/file.wav)` przełączyć się na dane wejściowe. Możesz również mieć mieszane dane wejściowe, korzystając z mikrofonu do rejestracji i weryfikowania plików na przykład.

## <a name="deleting-voice-profile-enrollments"></a>Usuwanie rejestracji profilu głosowego

Aby usunąć zarejestrowany profil, użyj `DeleteProfileAsync()` funkcji w `VoiceProfileClient` obiekcie. W poniższej przykładowej funkcji pokazano, jak usunąć profil głosowy ze znanego identyfikatora profilu głosowego.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
