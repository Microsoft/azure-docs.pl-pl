---
title: Jak korzystać z transkrypcji usługi Batch — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja usługi Batch jest idealnym rozwiązaniem, jeśli chcesz transkrypcja dużą ilość dźwięku w magazynie, na przykład obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać transkrypcje.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: 426cf78f6b87acf1d8c7551b0b0a6172a30167b1
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621099"
---
# <a name="how-to-use-batch-transcription"></a>Jak korzystać z transkrypcji partii

Transkrypcja usługi Batch to zestaw operacji interfejsu API REST, które umożliwiają transkrypcja dużej ilości danych audio w magazynie. Możesz wskazać pliki audio przy użyciu typowego identyfikatora URI lub identyfikatora URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać wyniki transkrypcji. Za pomocą interfejsu API programu v 3.0 można transkrypcja jeden lub więcej plików audio lub przetwarzać cały kontener magazynu.

Korzystając z interfejsów API REST transkrypcji wsadowej, można wywołać następujące metody:

|    Operacja transkrypcji partii                                             |    Metoda    |    Wywołanie interfejsu API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Tworzy nową transkrypcję.                                              |    POST      |    speechtotext/v 3.0/transkrypcje            |
|    Pobiera listę transkrypcji dla uwierzytelnionej subskrypcji.    |    GET       |    speechtotext/v 3.0/transkrypcje            |
|    Pobiera listę obsługiwanych ustawień regionalnych dla transkrypcji w trybie offline.              |    GET       |    speechtotext/v 3.0/transkrypcje/ustawienia regionalne    |
|    Aktualizuje modyfikowalne szczegóły transkrypcji identyfikowanej przez jego identyfikator.    |    WYSŁANA     |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Usuwa określone zadanie transkrypcji.                                 |    DELETE    |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Pobiera transkrypcję identyfikowaną przez podany identyfikator.                        |    GET       |    speechtotext/v 3.0/transkrypcje/{ID}       |
|    Pobiera pliki wynikowe transkrypcji identyfikowane przez podany identyfikator.    |    GET       |    speechtotext/v 3.0/transkrypcje/{ID}/pliki |

Możesz przejrzeć i przetestować szczegółowy interfejs API, który jest dostępny jako [dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Zadania transkrypcji partii są planowane na podstawie najlepszego nakładu pracy.
Nie można oszacować, kiedy zadanie zmieni się w stan uruchomienia, ale powinno wystąpić w ciągu kilku minut w przypadku normalnego obciążenia systemu. Raz w stanie uruchomienia transkrypcja odbywa się szybciej niż szybkość odtwarzania dźwięku w środowisku uruchomieniowym.

## <a name="prerequisites"></a>Wymagania wstępne

Podobnie jak w przypadku wszystkich funkcji usługi Speech, można utworzyć klucz subskrypcji z [Azure Portal](https://portal.azure.com) , postępując zgodnie z [przewodnikiem](overview.md#try-the-speech-service-for-free)wprowadzenie.

>[!NOTE]
> Aby można było użyć transkrypcji partii, wymagana jest Standardowa subskrypcja (S0) dla usługi rozpoznawania mowy. Bezpłatne klucze subskrypcji (F0) nie będą działały. Aby uzyskać więcej informacji, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Jeśli planujesz dostosowanie modeli, postępuj zgodnie z instrukcjami w temacie [dostosowanie akustyczne](./how-to-custom-speech-train-model.md) i [Dostosowywanie języka](./how-to-custom-speech-train-model.md). Aby można było używać utworzonych modeli w transkrypcji partii, potrzebna jest ich lokalizacja modelu. Lokalizację modelu można pobrać podczas inspekcji szczegółów modelu ( `self` Właściwości). Wdrożony niestandardowy punkt końcowy *nie jest wymagany* w przypadku usługi transkrypcji partii.

>[!NOTE]
> W ramach interfejsu API REST transkrypcja usługi Batch ma zestaw [przydziałów i limitów](speech-services-quotas-and-limits.md#batch-transcription), które zachęcamy do przeglądu. Aby skorzystać z pełnego wykorzystania możliwości transkrypcji w usłudze Batch w celu wydajnego transkrypcja dużej liczby plików audio, zalecamy wysyłanie wielu plików na żądanie lub wskazanie kontenera Blob Storage z plikami audio do transkrypcja. Usługa będzie transkrypcja pliki jednocześnie zmniejszając czas szybkością oferowaną. Używanie wielu plików w pojedynczym żądaniu jest bardzo proste i proste — Zobacz sekcję [konfiguracyjną](#configuration) . 

## <a name="batch-transcription-api"></a>Interfejs API transkrypcji usługi Batch

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Wymaga | Bity na próbkę | Częstotliwość próbkowania             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| FORMATU    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| OGG    | OPUS  | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |

W przypadku strumieni stereofonicznych audio lewy i prawy są dzielone podczas transkrypcji. Tworzony jest plik wynikowy JSON dla każdego kanału.
Aby utworzyć uporządkowaną końcową transkrypcję, użyj sygnatur czasowych wygenerowanych na wypowiedź.

### <a name="configuration"></a>Konfiguracja

Parametry konfiguracji są podane jako dane JSON.

**Jego przepisywania jeden lub więcej pojedynczych plików.** Jeśli masz więcej niż jeden plik do Transkrypcja, zalecamy wysłanie wielu plików w jednym żądaniu. W poniższym przykładzie użyto trzech plików:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Przetwarzanie całego kontenera magazynu:**

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Użyj niestandardowego modelu przeszkolonego w transkrypcji partii.** W przykładzie użyto trzech plików:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

:::row:::
   :::column span="1":::
      **Parametr**
   :::column-end:::
   :::column span="2":::
      **Opis**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Opcjonalne, wartość domyślna to `Masked` . Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie filtrowania wulgarności, `Masked` zastępowanie nieseksu gwiazdką, `Removed` Aby usunąć z wyniku wszystkie niezbyt wulgarne lub `Tags` dodać tagi "wulgarne".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Opcjonalne, wartość domyślna to `DictatedAndAutomatic` . Określa, jak obsłużyć interpunkcję w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie interpunkcji, `Dictated` w celu oznaczania znaków jawnych (mówionych), `Automatic` w celu poinformowania dekodera z interpunkcją lub `DictatedAndAutomatic` użycia podyktowanych i automatycznych interpunkcji.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Domyślnie opcjonalne `false` . Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Domyślnie opcjonalne `false` . Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Uwaga: wymaga `wordLevelTimestampsEnabled` ustawienia wartości `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Opcjonalne `0` i `1` Domyślnie uzyskanego. Tablica numerów kanałów do przetworzenia. W tym miejscu podzbiór dostępnych kanałów w pliku audio może być określony do przetworzenia (tylko na przykład `0` ).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Opcjonalne, nie usuwaj domyślnie. Czas trwania usuwania transkrypcji po zakończeniu transkrypcji. `timeToLive`Jest przydatny do masowego tworzenia transkrypcji, aby upewnić się, że zostaną ostatecznie usunięte (na przykład `PT12H` przez 12 godzin).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcjonalny adres URL z [usługą ad hoc SAS](../../storage/common/storage-sas-overview.md) do zapisywalnego kontenera na platformie Azure. Wynik jest przechowywany w tym kontenerze. Sygnatura dostępu współdzielonego z przechowywanymi zasadami zapisu **nie** jest obsługiwana. Jeśli nie zostanie określony, firma Microsoft zapisuje wyniki w kontenerze magazynu zarządzanym przez firmę Microsoft. Gdy transkrypcja zostanie usunięta przez wywołanie [usuwania transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), dane wynikowe również zostaną usunięte.
:::row-end:::

### <a name="storage"></a>Storage

Transkrypcja usługi Batch może odczytywać dźwięk z publicznego, widocznego w Internecie identyfikatora URI i może odczytywać zapisy audio i zapisu przy użyciu identyfikatora URI sygnatury dostępu współdzielonego z [usługą Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md).

## <a name="batch-transcription-result"></a>Wynik transkrypcji partii

Dla każdego wejścia audio zostanie utworzony jeden plik wynikowy transkrypcji.
Operacja [pobierania plików transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) zwraca listę plików wynikowych dla tego transkrypcji. Aby znaleźć plik transkrypcji dla określonego pliku wejściowego, należy odfiltrować wszystkie zwrócone pliki z `kind`  ==  `Transcription` i `name`  ==  `{originalInputName.suffix}.json` .

Każdy plik wynikowy transkrypcji ma następujący format:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

Wynik zawiera następujące pola:

:::row:::
   :::column span="1":::
      **Pole**
   :::column-end:::
   :::column span="2":::
      **Zawartość**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      Rzeczywiste wyrazy zostały rozpoznane.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Odwrócony tekst — znormalizowana forma rozpoznanego tekstu. Skróty ("Lekarz Kowalski" do "Dr Kowalski"), numery telefonów i inne przekształcenia są stosowane.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Formularz ITN z zastosowanym maską wulgarności.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Formularz wyświetlania rozpoznanego tekstu. Dodano znaki interpunkcyjne i wielkie litery.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separacja głośników (diarization)

Diarization to proces oddzielania głośników w części audio. Potok wsadowy obsługuje diarization i jest w stanie rozpoznawać dwa głośniki w nagraniach kanału mono. Funkcja nie jest dostępna w nagraniach stereo.

Dane wyjściowe transkrypcji z włączonym diarization zawiera `Speaker` wpis dla każdej frazy uzyskanego. Jeśli diarization nie jest używana, `Speaker` Właściwość nie jest obecna w danych wyjściowych JSON. W przypadku diarization obsługujemy dwie głosy, więc głośniki są identyfikowane jako `1` lub `2` .

Aby zażądać diarization, Dodaj opcję Ustaw `diarizationEnabled` Właściwość, `true` tak jak żądanie HTTP, poniżej.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Znaczniki czasu na poziomie słowa muszą być włączone, ponieważ parametry w powyższym żądaniu wskazują.

## <a name="best-practices"></a>Najlepsze rozwiązania

Usługa transkrypcji usługi Batch może obsłużyć dużą liczbę przesłanych transkrypcji. Można wysyłać zapytania o stan transkrypcji przy użyciu [Get transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Wywołaj regularne [usuwanie transkrypcji](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) z usługi po pobraniu wyników. Alternatywnie Ustaw `timeToLive` Właściwość, aby zapewnić ostateczne usuwanie wyników.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Zaktualizuj przykładowy kod przy użyciu informacji o subskrypcji, regionu usługi, identyfikatora URI wskazującego plik audio do transkrypcja i lokalizacji modelu, jeśli używasz modelu niestandardowego.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Przykładowy kod konfiguruje klienta i przesyła żądanie transkrypcji. Następnie sonduje informacje o stanie i drukuje szczegóły dotyczące postępu transkrypcji.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Aby uzyskać szczegółowe informacje o poprzednich wywołaniach, zobacz [dokument struktury Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Aby wyświetlić pełny przykład, przejdź do witryny [GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Ten przykład używa konfiguracji asynchronicznej do publikowania audio i otrzymywania stanu transkrypcji.
`PostTranscriptions`Metoda wysyła Szczegóły pliku audio, a `GetTranscriptions` Metoda odbiera Stany.
`PostTranscriptions` zwraca dojście i `GetTranscriptions` używa go do utworzenia dojścia w celu uzyskania stanu transkrypcji.

Ten przykładowy kod nie określa modelu niestandardowego. Usługa używa modelu linii bazowej do jego przepisywania pliku lub plików. Aby określić model, można przekazać do tej samej metody odwołanie modelu dla modelu niestandardowego.

> [!NOTE]
> W przypadku transkrypcji linii bazowej nie trzeba deklarować identyfikatora modelu linii bazowej.

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja interfejsu API zamiany mowy na tekst v3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)