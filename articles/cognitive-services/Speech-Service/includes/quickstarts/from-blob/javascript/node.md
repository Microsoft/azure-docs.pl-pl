---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 4d3db2c10c3fd989ac9737e242e3d6eda517a55f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376995"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-javascript)
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="create-a-new-js-file"></a>Utwórz nowy plik JS

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w ulubionym edytorze.

Wywołaj index.js pliku.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Otoki JSON

Jako że żądania interfejsu API REST są wykonywane w formacie JSON i zwracają wyniki JSON.
Aby żądania i odpowiedzi były łatwiejsze do zrozumienia, deklarujemy kilka klas do użycia na potrzeby serializowania/deserializacji kodu JSON.


```JavaScript
class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}
```

## <a name="create-an-initial-transcription-request"></a>Utwórz początkowe żądanie transkrypcji.
Następnie wygenerujemy żądanie transkrypcji.

```JavaScript
const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}
```

## <a name="send-the-transcription-request"></a>Wyślij żądanie transkrypcji.
Teraz wyślemy żądanie do usługi mowy i Sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi będzie po prostu wskazywać, czy usługa odebrała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, które są lokalizacją przechowywania stanu transkrypcji.

Następnie wywołamy metodę, `CheckTranscriptionStatus` Aby sprawdzić stan i ostatecznie wydrukować wyniki. Zostanie wdrożony `CheckTranscriptionStatus` Następny.

```JavaScript
const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="check-the-requests-status"></a>Sprawdź stan żądań
Ponieważ usługa przetwarza proces transkrypcji asynchronicznie, musimy wykonać sondowanie pod kątem jego stanu co do tego często. Sprawdzimy co 5 sekund.

Stan można sprawdzić, pobierając zawartość pod adresem URL, który otrzymał po wysłaniu żądania. Po otrzymaniu zawartości, deserializacjimy ją na jedną z naszych klas pomocnika, aby ułatwić korzystanie z programu.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zajmiemy się tym dalej. 

`CheckTranscriptionStatus` Pobiera adres URL stanu z żądania transkrypcji i sonduje go co 5 sekund do momentu, aż wskazuje powodzenie lub błąd. Następnie wywołuje, `PrintResults` Aby wydrukować wyniki transkrypcji. Zostanie wdrożony `PrintResults` Następny.
```csharp
function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}
```

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Gdy usługa pomyślnie ukończy transkrypcję, wyniki będą przechowywane w innym adresie URL, który można uzyskać z odpowiedzi na stan. Oto prośba o pobranie tych wyników do pliku tymczasowego przed rozpoczęciem odczytywania i deserializacji.
Po załadowaniu wyników można wydrukować je w konsoli programu. 

```JavaScript
function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}
```

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco:

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";

class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}

const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}

function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}

function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}

const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="run-your-app"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

**Uruchom** węzeł uruchamiania aplikacji index.js.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
