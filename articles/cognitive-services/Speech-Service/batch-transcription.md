---
title: Co to jest transkrypcja partii — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja usługi Batch jest idealnym rozwiązaniem, jeśli chcesz transkrypcja dużą ilość dźwięku w magazynie, na przykład obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać transkrypcje.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208622"
---
# <a name="what-is-batch-transcription"></a>Co to jest transkrypcja partii?

Transkrypcja usługi Batch to zestaw operacji interfejsu API REST, które umożliwiają transkrypcja dużej ilości danych audio w magazynie. Możesz wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać wyniki transkrypcji.

Asynchroniczne transkrypcja zamiany mowy na tekst to tylko jedna z funkcji. Korzystając z interfejsów API REST transkrypcji wsadowej, można wywołać następujące metody:



|    Operacja transkrypcji partii                                             |    Metoda    |    Wywołanie interfejsu API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Tworzy nową transkrypcję.                                              |    POST      |    Interfejs API/speechtotext/v 2.0/transkrypcje            |
|    Pobiera listę transkrypcji dla uwierzytelnionej subskrypcji.    |    GET       |    Interfejs API/speechtotext/v 2.0/transkrypcje            |
|    Pobiera listę obsługiwanych ustawień regionalnych dla transkrypcji w trybie offline.              |    GET       |    interfejsy API/speechtotext/v 2.0/transkrypcje/ustawienia regionalne    |
|    Aktualizuje modyfikowalne szczegóły transkrypcji identyfikowanej przez jego identyfikator.    |    WYSŁANA     |    Interfejs API/speechtotext/v 2.0/transkrypcje/{ID}       |
|    Usuwa określone zadanie transkrypcji.                                 |    DELETE    |    Interfejs API/speechtotext/v 2.0/transkrypcje/{ID}       |
|    Pobiera transkrypcję identyfikowaną przez podany identyfikator.                        |    GET       |    Interfejs API/speechtotext/v 2.0/transkrypcje/{ID}       |




Możesz przejrzeć i przetestować szczegółowy interfejs API, który jest dostępny jako [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), pod nagłówkiem `Custom Speech transcriptions`.

Zadania transkrypcji partii są planowane na podstawie najlepszego nakładu pracy. Obecnie nie ma oszacowania, kiedy zadanie zmienia się w stan uruchomienia. W przypadku normalnego ładowania systemu powinno to nastąpić w ciągu kilku minut. Po uruchomieniu rzeczywista transkrypcja jest przetwarzana szybciej niż w czasie rzeczywistym dźwięku.

Obok łatwego w użyciu interfejsu API nie musisz wdrażać niestandardowych punktów końcowych i nie masz żadnych wymagań współbieżności do obserwowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Podobnie jak w przypadku wszystkich funkcji usługi Speech, można utworzyć klucz subskrypcji z [Azure Portal](https://portal.azure.com) , postępując zgodnie z [przewodnikiem](get-started.md)wprowadzenie.

>[!NOTE]
> Aby można było użyć transkrypcji partii, wymagana jest Standardowa subskrypcja (S0) dla usługi rozpoznawania mowy. Bezpłatne klucze subskrypcji (F0) nie działają. Aby uzyskać więcej informacji, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosowanie modeli akustycznych lub modelowych, postępuj zgodnie z instrukcjami w temacie [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [modeli języków dostosowania projektu](how-to-customize-language-model.md). Aby można było używać utworzonych modeli w transkrypcji partii, potrzebne są ich identyfikatory modeli. Identyfikator modelu można pobrać podczas inspekcji szczegółów modelu. Wdrożony niestandardowy punkt końcowy nie jest wymagany w przypadku usługi transkrypcji partii.

## <a name="the-batch-transcription-api"></a>Interfejs API transkrypcji usługi Batch

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Wymaga | Multimedia | Częstotliwość próbkowania                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| FORMATU    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| OGG    | OPUS  | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |

W przypadku strumieni stereofonicznych audio lewy i prawy są dzielone podczas transkrypcji. Dla każdego kanału tworzony jest plik wynikowy JSON. Sygnatury czasowe wygenerowane na wypowiedź umożliwiają deweloperowi utworzenie uporządkowanej końcowej transkrypcji.

### <a name="configuration"></a>Konfiguracja

Parametry konfiguracji są podane jako dane JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

:::row:::
   :::column span="1":::
      **Konstruktora**
   :::column-end:::
   :::column span="2":::
      **Opis**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie filtrowania wulgarności, `Masked` zastępowanie nieseksu gwiazdką, `Removed` aby usunąć z wyniku wszystkie niezbyt wulgarne lub `Tags` dodać tagi "wulgarne". Ustawienie domyślne to `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Określa, jak obsłużyć interpunkcję w wynikach rozpoznawania. Akceptowane wartości to `None` wyłączenie interpunkcji, `Dictated` w celu oznaczania znaków jawnych ( `Automatic` mówionych), w celu poinformowania `DictatedAndAutomatic` dekodera z interpunkcją lub użycia podyktowanych i automatycznych interpunkcji. Ustawienie domyślne to `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych. Akceptowane wartości to `true` włączenie znaczników czasu poziomu słowa i `false` (wartość domyślna), aby je wyłączyć.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Określa, czy analiza tonacji powinna być stosowana do wypowiedź. Akceptowane wartości to `true` Enable i ( `false` wartość domyślna), aby je wyłączyć. Aby uzyskać szczegółowe informacje, zobacz [Analiza tonacji](#sentiment-analysis) .
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Akceptowane wartości `true` włączają diarization i `false` (wartość domyślna), aby je wyłączyć. Wymagane `AddWordLevelTimestamps` jest również ustawienie wartości true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcjonalny adres URL z [sygnaturą dostępu współdzielonego usługi](../../storage/common/storage-sas-overview.md) do zapisywalnego kontenera na platformie Azure. Wynik jest przechowywany w tym kontenerze.
:::row-end:::

### <a name="storage"></a>Magazyn

Transkrypcja usługi Batch obsługuje [usługę Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) do odczytu i zapisywania transkrypcji w magazynie.

## <a name="the-batch-transcription-result"></a>Wynik transkrypcji partii

W przypadku wejścia audio mono jest tworzony jeden plik wynikowy transkrypcji. W przypadku dźwięku wejściowego stereo, tworzone są dwa pliki wyników transkrypcji. Każda z nich ma następującą strukturę:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Wynik zawiera następujące formularze:

:::row:::
   :::column span="1":::
      **Formularz**
   :::column-end:::
   :::column span="2":::
      **Zawartość**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      Rzeczywiste wyrazy zostały rozpoznane.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Odwrócony tekst — znormalizowana forma rozpoznanego tekstu. Skróty ("Lekarz Kowalski" do "Dr Kowalski"), numery telefonów i inne przekształcenia są stosowane.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Formularz ITN z zastosowanym maską wulgarności.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Formularz wyświetlania rozpoznanego tekstu. Dodano znaki interpunkcyjne i wielkie litery.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Separacja głośników (Diarization)

Diarization to proces oddzielania głośników w części audio. Nasz potok wsadowy obsługuje diarization i jest w stanie rozpoznawać dwa głośniki w nagraniach kanału mono. Funkcja nie jest dostępna w nagraniach stereo.

Wszystkie dane wyjściowe transkrypcji zawierają `SpeakerId`. Jeśli diarization nie jest używany, zostanie wyświetlony `"SpeakerId": null` w danych wyjściowych JSON. W przypadku diarization obsługujemy dwie głosy, więc głośniki są identyfikowane `"1"` jako `"2"`lub.

Aby zażądać diarization, wystarczy dodać odpowiedni parametr w żądaniu HTTP, jak pokazano poniżej.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Znaczniki czasu na poziomie słowa również muszą mieć wartość "włączone", ponieważ parametry w powyższym żądaniu wskazują.

## <a name="sentiment-analysis"></a>Analiza tonacji

Funkcja tonacji szacuje tonacji wyrażoną w dźwięku. Tonacji jest wyrażona wartością z zakresu od 0 do 1 dla `Negative`, `Neutral`i `Positive` tonacji. Na przykład analiza tonacji może być używana w scenariuszach centrum połączeń:

- Uzyskaj wgląd w szczegółowe informacje na temat zadowolenia klientów
- Uzyskaj wgląd w wydajność agentów (zespół przejmowania wywołań)
- Znajdowanie dokładnego punktu w czasie, gdy wywołanie zajęło negatywny kierunek
- Co się stało, gdy ujemne wywołanie jest wynikiem pozytywnym
- Określ, co Ci klienci i co nie lubię o produkcie lub usłudze

Tonacji jest oceniane na segment audio w oparciu o postać leksykalną. Cały tekst w tym segmencie audio służy do obliczania tonacji. Nie jest obliczany zagregowany tonacji dla całego transkrypcji. Analiza tonacji jest obecnie dostępna tylko w języku angielskim.

> [!NOTE]
> Zalecamy używanie interfejs API analizy tekstu Microsoft. Oferuje bardziej zaawansowane funkcje wykraczające poza analizę tonacji, taką jak wyodrębnianie kluczowych fraz, automatyczne wykrywanie języka i inne. Informacje i przykłady można znaleźć w [dokumentacji analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

Przykład danych wyjściowych JSON wygląda następująco:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Najlepsze rozwiązania

Usługa transkrypcji może obsłużyć dużą liczbę przesłanych transkrypcji. Można wysyłać zapytania o stan transkrypcji za pomocą `GET` [metody transkrypcji](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Należy zachować informacje zwracane do rozsądnego rozmiaru przez określenie `take` parametru (kilka setek). [Usuwanie transkrypcji](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regularnie z usługi po pobraniu wyników. Gwarantuje to szybkie odpowiedzi od wywołań zarządzania transkrypcji.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w `samples/batch` [repozytorium przykładowym GitHub](https://aka.ms/csspeech/samples) w podkatalogu.

Musisz dostosować przykładowy kod przy użyciu informacji o subskrypcji, regionu usługi, identyfikatora URI sygnatury dostępu współdzielonego, wskazującego plik audio do Transkrypcja, oraz identyfikatorów modeli w przypadku, gdy chcesz użyć niestandardowego modelu akustycznego lub języka.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Przykładowy kod konfiguruje klienta i przesyła żądanie transkrypcji. Następnie sonduje informacje o stanie i drukuje szczegóły dotyczące postępu transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Aby uzyskać szczegółowe informacje o poprzednich wywołaniach, zobacz [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index). Aby wyświetlić pełny przykład, przejdź do witryny [GitHub](https://aka.ms/csspeech/samples) w `samples/batch` podkatalogu.

Zanotuj konfigurację asynchroniczną dotyczącą ogłaszania audio i uzyskiwania stanu transkrypcji. Tworzony klient jest klientem HTTP platformy .NET. Istnieje `PostTranscriptions` metoda wysyłania szczegółów pliku audio i `GetTranscriptions` metody uzyskiwania wyników. `PostTranscriptions`zwraca dojście i `GetTranscriptions` używa go do utworzenia dojścia w celu uzyskania stanu transkrypcji.

Bieżący przykładowy kod nie określa modelu niestandardowego. Usługa używa modeli bazowych do jego przepisywania pliku lub plików. Aby określić modele, można przekazać tę samą metodę co identyfikatory modelu dla akustycznego i modelu języka.

> [!NOTE]
> W przypadku transkrypcji bazowej nie trzeba deklarować identyfikatora dla modeli bazowych. Jeśli określisz tylko identyfikator modelu języka (bez identyfikatora modelu akustycznego), zostanie automatycznie wybrany zgodny model akustyczny. Jeśli określisz tylko identyfikator modelu akustycznego, zostanie automatycznie wybrany pasujący model języka.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w `samples/batch` katalogu w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
