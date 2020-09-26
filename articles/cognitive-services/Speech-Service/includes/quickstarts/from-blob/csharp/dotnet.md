---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 05cf63f741e287112ace76a5249ae2ca8d01f34f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376613"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programmming-language-csharp)
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i otwórz go `Program.cs` .

## <a name="add-a-reference-to-newtonsoftjson"></a>Dodaj odwołanie do Newtonsoft.Jsna

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **HelloWorld** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet** , aby wyświetlić Menedżera pakietów NuGet.
1. W prawym górnym rogu Znajdź pole listy rozwijanej **Źródło pakietu** i upewnij się, że **`nuget.org`** jest zaznaczone.
1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.
1. W polu wyszukiwania wpisz *newtonsoft.json* i wybierz **Enter**.
1. W wynikach wyszukiwania wybierz [**Newtonsoft.Jsw**](https://www.nuget.org/packages/Newtonsoft.Json) pakiecie, a następnie wybierz pozycję **Zainstaluj** , aby zainstalować najnowszą stabilną wersję.
1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.
   Po zainstalowaniu pakietu w oknie **konsola Menedżera pakietów** zostanie wyświetlone potwierdzenie.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

```csharp
class Program
{
    // Replace with your subscription key
    const string SubscriptionKey = "YourSubscriptionKey";

    // Update with your service region
    const string Region = "YourServiceRegion";
    const int Port = 443;
 
    // Recordings and locale
    const string Locale = "en-US";
    const string RecordingsBlobUri = "YourFileUrl";
 
    // Name and description
    const string Name = "Simple transcription";
    const string Description = "Simple transcription description";
 
    const string SpeechToTextBasePath = "api/speechtotext/v2.0/";
 
    static async Task Main()
    {
        // Cognitive Services follows security best practices.
        // If you experience connectivity issues, see:
        // https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls
 
        await TranscribeAsync();
    }
 
    static async Task TranscribeAsync()
    {
        Console.WriteLine("Starting transcriptions client...");
    }
}
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Otoki JSON

Jako że żądania API REST w formacie JSON i zwracają wyniki w kodzie JSON, możemy z nich korzystać tylko przy użyciu ciągów, ale nie jest to zalecane.
Aby ułatwić zarządzanie żądaniami i odpowiedziami, deklarujemy kilka klas do użycia na potrzeby serializowania/deserializacji kodu JSON.

Przejdź dalej i po `TranscribeAsync` .

```csharp
public class ModelIdentity
{
    ModelIdentity(Guid id) => Id = id;

    public Guid Id { get; private set; }

    public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
}

public class Transcription
{
    [JsonConstructor]
    Transcription(
        Guid id,
        string name,
        string description,
        string locale,
        DateTime createdDateTime,
        DateTime lastActionDateTime,
        string status,
        Uri recordingsUrl,
        IReadOnlyDictionary<string, string> resultsUrls)
    {
        Id = id;
        Name = name;
        Description = description;
        CreatedDateTime = createdDateTime;
        LastActionDateTime = lastActionDateTime;
        Status = status;
        Locale = locale;
        RecordingsUrl = recordingsUrl;
        ResultsUrls = resultsUrls;
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public string Locale { get; set; }

    public Uri RecordingsUrl { get; set; }

    public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

    public Guid Id { get; set; }

    public DateTime CreatedDateTime { get; set; }

    public DateTime LastActionDateTime { get; set; }

    public string Status { get; set; }

    public string StatusMessage { get; set; }
}

public class TranscriptionDefinition
{
    TranscriptionDefinition(
        string name,
        string description,
        string locale,
        Uri recordingsUrl,
        IEnumerable<ModelIdentity> models)
    {
        Name = name;
        Description = description;
        RecordingsUrl = recordingsUrl;
        Locale = locale;
        Models = models;
        Properties = new Dictionary<string, string>
        {
            ["PunctuationMode"] = "DictatedAndAutomatic",
            ["ProfanityFilterMode"] = "Masked",
            ["AddWordLevelTimestamps"] = "True"
        };
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public Uri RecordingsUrl { get; set; }

    public string Locale { get; set; }

    public IEnumerable<ModelIdentity> Models { get; set; }

    public IDictionary<string, string> Properties { get; set; }

    public static TranscriptionDefinition Create(
        string name,
        string description,
        string locale,
        Uri recordingsUrl)
        => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
}
```

## <a name="create-and-configure-an-http-client"></a>Tworzenie i Konfigurowanie klienta http
Najpierw musimy być klientem http z prawidłowym podstawowym adresem URL i zestawem uwierzytelniania.
Wstaw ten kod w `TranscribeAsync` .

```csharp
var client = new HttpClient
{
    Timeout = TimeSpan.FromMinutes(25),
    BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
    DefaultRequestHeaders =
    {
        { "Ocp-Apim-Subscription-Key", SubscriptionKey }
    }
};
```

## <a name="generate-a-transcription-request"></a>Generuj żądanie transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten kod do `TranscribeAsync` .

```csharp
var transcriptionDefinition =
    TranscriptionDefinition.Create(
        Name,
        Description,
        Locale,
        new Uri(RecordingsBlobUri));

var res = JsonConvert.SerializeObject(transcriptionDefinition);
var sc = new StringContent(res);
sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;
```

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz wyślemy żądanie do usługi mowy i Sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi będzie po prostu wskazywać, czy usługa odebrała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, które są lokalizacją przechowywania stanu transkrypcji.

```csharp
Uri transcriptionLocation = null;
using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
{
    if (!response.IsSuccessStatusCode)
    {
        Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
        return;
    }

    transcriptionLocation = response.Headers.Location;
}
```

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza proces transkrypcji asynchronicznie, musimy wykonać sondowanie pod kątem jego stanu co do tego często. Sprawdzimy co 5 sekund.

Stan można sprawdzić, pobierając zawartość pod adresem URL, który otrzymał po wysłaniu żądania. Po otrzymaniu zawartości, deserializacjimy ją na jedną z naszych klas pomocnika, aby ułatwić korzystanie z programu.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zajmiemy się tym dalej.

```csharp
Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
Console.WriteLine("Checking status.");

var completed = false;

// Check for the status of our transcriptions periodically
while (!completed)
{
    Transcription transcription = null;
    using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
    {
        var contentType = response.Content.Headers.ContentType;
        if (response.IsSuccessStatusCode &&
            string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
        {
            transcription = await response.Content.ReadAsAsync<Transcription>();
        }
        else
        {
            Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
            continue;
        }
    }

    switch (transcription.Status)
    {
        case "Failed":
            completed = true;
            Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
            break;

        case "Succeeded":
            break;

        case "Running":
            Console.WriteLine("Transcription is still running.");
            break;

        case "NotStarted":
            Console.WriteLine("Transcription has not started.");
            break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}

Console.WriteLine("Press any key...");
Console.ReadKey();
```

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Gdy usługa pomyślnie ukończy transkrypcję, wyniki będą przechowywane w innym adresie URL, który można uzyskać z odpowiedzi na stan. Oto prośba o pobranie tych wyników do pliku tymczasowego przed rozpoczęciem odczytywania i deserializacji.
Po załadowaniu wyników można wydrukować je w konsoli programu. Dodaj następujący kod do `case "Succeeded":` etykiety.

```csharp
completed = true;
var webClient = new WebClient();
var filename = Path.GetTempFileName();
webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
var results = File.ReadAllText(filename);
Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
File.Delete(filename);
```

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji)

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using System.Net.Http;
using System.Net.Http.Formatting;

namespace BatchClient
{
    class Program
    {
        // Replace with your subscription key
        const string SubscriptionKey = "YourSubscriptionKey";

        // Update with your service region
        const string Region = "YourServiceRegion";
        const int Port = 443;

        // Recordings and locale
        const string Locale = "en-US";
        const string RecordingsBlobUri = "YourFileUrl";

        // Name and description
        const string Name = "Simple transcription";
        const string Description = "Simple transcription description";

        const string SpeechToTextBasePath = "api/speechtotext/v2.0/";

        static async Task Main()
        {
            // For non-Windows 10 users.
            ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;

            await TranscribeAsync();
        }

        static async Task TranscribeAsync()
        {
            Console.WriteLine("Starting transcriptions client...");

            // Create the client object and authenticate
            var client = new HttpClient
            {
                Timeout = TimeSpan.FromMinutes(25),
                BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
                DefaultRequestHeaders =
                {
                    { "Ocp-Apim-Subscription-Key", SubscriptionKey }
                }
            };

            var transcriptionDefinition =
                TranscriptionDefinition.Create(
                    Name,
                    Description,
                    Locale,
                    new Uri(RecordingsBlobUri));

            var res = JsonConvert.SerializeObject(transcriptionDefinition);
            var sc = new StringContent(res);
            sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;

            Uri transcriptionLocation = null;

            using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
            {
                if (!response.IsSuccessStatusCode)
                {
                    Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
                    return;
                }

                transcriptionLocation = response.Headers.Location;
            }

            Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
            Console.WriteLine("Checking status.");

            var completed = false;

            // Check for the status of our transcriptions periodically
            while (!completed)
            {
                Transcription transcription = null;

                // Get all transcriptions for the user
                using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
                {
                    var contentType = response.Content.Headers.ContentType;
                    if (response.IsSuccessStatusCode &&
                        string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
                    {
                        transcription = await response.Content.ReadAsAsync<Transcription>();
                    }
                    else
                    {
                        Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
                        continue;
                    }
                }

                // For each transcription in the list we check the status
                switch (transcription.Status)
                {
                    case "Failed":
                        completed = true;
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
                        break;

                    case "Succeeded":
                        completed = true;
                        var webClient = new WebClient();
                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
                        var results = File.ReadAllText(filename);
                        Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
                        File.Delete(filename);
                        break;

                    case "Running":
                        Console.WriteLine("Transcription is still running.");
                        break;

                    case "NotStarted":
                        Console.WriteLine("Transcription has not started.");
                        break;
                }

                await Task.Delay(TimeSpan.FromSeconds(5));
            }

            Console.WriteLine("Press any key...");
            Console.ReadKey();
        }
    }

    public class ModelIdentity
    {
        ModelIdentity(Guid id) => Id = id;

        public Guid Id { get; private set; }

        public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
    }

    public class Transcription
    {
        [JsonConstructor]
        Transcription(
            Guid id,
            string name,
            string description,
            string locale,
            DateTime createdDateTime,
            DateTime lastActionDateTime,
            string status,
            Uri recordingsUrl,
            IReadOnlyDictionary<string, string> resultsUrls)
        {
            Id = id;
            Name = name;
            Description = description;
            CreatedDateTime = createdDateTime;
            LastActionDateTime = lastActionDateTime;
            Status = status;
            Locale = locale;
            RecordingsUrl = recordingsUrl;
            ResultsUrls = resultsUrls;
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public string Locale { get; set; }

        public Uri RecordingsUrl { get; set; }

        public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

        public Guid Id { get; set; }

        public DateTime CreatedDateTime { get; set; }

        public DateTime LastActionDateTime { get; set; }

        public string Status { get; set; }

        public string StatusMessage { get; set; }
    }

    public class TranscriptionDefinition
    {
        TranscriptionDefinition(
            string name,
            string description,
            string locale,
            Uri recordingsUrl,
            IEnumerable<ModelIdentity> models)
        {
            Name = name;
            Description = description;
            RecordingsUrl = recordingsUrl;
            Locale = locale;
            Models = models;
            Properties = new Dictionary<string, string>
            {
                ["PunctuationMode"] = "DictatedAndAutomatic",
                ["ProfanityFilterMode"] = "Masked",
                ["AddWordLevelTimestamps"] = "True"
            };
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public Uri RecordingsUrl { get; set; }

        public string Locale { get; set; }

        public IEnumerable<ModelIdentity> Models { get; set; }

        public IDictionary<string, string> Properties { get; set; }

        public static TranscriptionDefinition Create(
            string name,
            string description,
            string locale,
            Uri recordingsUrl)
            => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
    }
}
```

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj**  >  **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
