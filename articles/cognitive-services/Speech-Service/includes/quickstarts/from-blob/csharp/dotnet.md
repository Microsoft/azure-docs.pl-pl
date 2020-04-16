---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 8c63c979300af4c180751b3824def0cb974ee186
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400937"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programmming-language-csharp)
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `Program.cs`projekt i otwórz .

## <a name="add-a-reference-to-newtonsoftjson"></a>Dodaj odwołanie do pliku Newtonsoft.Json

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **helloworld,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet,** aby wyświetlić Menedżera pakietów NuGet.
1. W prawym górnym rogu znajdź pole rozwijane **Źródło pakietu** i **`nuget.org`** upewnij się, że jest zaznaczone.
1. W lewym górnym rogu wybierz pozycję **Przeglądaj**.
1. W polu wyszukiwania wpisz *newtonsoft.json* i **wybierz**enter .
1. Z wyników wyszukiwania wybierz pakiet [**Newtonsoft.Json,**](https://www.nuget.org/packages/Newtonsoft.Json) a następnie wybierz pozycję **Zainstaluj,** aby zainstalować najnowszą stabilną wersję.
1. Zaakceptuj wszystkie umowy i licencje, aby rozpocząć instalację.
   Po zainstalowaniu pakietu w oknie **Konsoli Menedżera pakietów** pojawi się potwierdzenie.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

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

## <a name="json-wrappers"></a>Opakowania JSON

Jako rest interfejsu API przyjmować żądania w formacie JSON, a także zwraca wyniki w JSON możemy wchodzić z nimi przy użyciu tylko ciągów, ale to nie jest zalecane.
Aby ułatwić zarządzanie żądaniami i odpowiedziami, zadeklarujemy kilka klas do użycia do serializacji / deserializacji JSON.

Śmiało i umieścić swoje `TranscribeAsync`deklaracje po .

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

## <a name="create-and-configure-an-http-client"></a>Tworzenie i konfigurowanie klienta http
Pierwszą rzeczą, której potrzebujemy, jest klient http, który ma poprawny podstawowy adres URL i zestaw uwierzytelniania.
Wstaw ten `TranscribeAsync`kod w pliku .

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

## <a name="generate-a-transcription-request"></a>Generowanie żądania transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten `TranscribeAsync`kod do pliku .

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
Teraz publikujemy żądanie w usłudze mowy i sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi po prostu wskaże, czy usługa otrzymała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, która jest lokalizacją, w której będzie przechowywać stan transkrypcji.

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
Ponieważ usługa przetwarza transkrypcji asynchronicznie, musimy sondować jego status tak często. Sprawdzamy co 5 sekund.

Możemy sprawdzić stan, pobierając zawartość pod adresem URL, który otrzymaliśmy po opublikowaniu żądania. Kiedy przywracamy zawartość, możemy deserialize go do jednej z naszych klasy pomocnika, aby ułatwić interakcję z.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zrobimy to dalej.

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
Po pomyślnym zakończeniu transkrypcji usługi wyniki będą przechowywane w innym adresie URL, który możemy uzyskać z odpowiedzi na stan. W tym miejscu zwracamy się z prośbą o pobranie tych wyników do pliku tymczasowego przed ich odczytaniem i deserializacją.
Po załadowaniu wyników możemy wydrukować je na konsoli. Dodaj następujący kod `case "Succeeded":` do etykiety.

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
W tym momencie kod powinien wyglądać następująco: (Dodaliśmy kilka komentarzy do tej wersji)

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

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz pozycję **Build** > **Build Solution**.
2. **Uruchom aplikację** — na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij **klawisz F5**.
3. **Rozpocznij rozpoznawanie** - poprosi Cię o wymówienia frazy w języku angielskim. Mowa jest wysyłana do usługi Mowy, transkrybowana jako tekst i renderowana w konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
