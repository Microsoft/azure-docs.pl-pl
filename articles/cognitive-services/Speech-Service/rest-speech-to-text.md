---
title: Dokumentacja interfejsu API zamiany mowy na tekst (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST zamiany mowy na tekst. Ten artykuł zawiera informacje o opcjach autoryzacji, opcjach zapytań, sposobach tworzenia struktury żądania i otrzymywaniu odpowiedzi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 24cc7773d281d98b6a0943f5e0a61b6f547991ee
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032827"
---
# <a name="speech-to-text-rest-api"></a>Interfejs API REST zamiany mowy na tekst

Zamiana mowy na tekst ma dwa różne interfejsy API REST. Każdy interfejs API pełni swój specjalny cel i używa różnych zestawów punktów końcowych.

Interfejsy API REST zamiany mowy na tekst są następujące:
- Funkcja [zamiany mowy na tekst "API REST v 3.0"](#speech-to-text-rest-api-v30) jest używana na potrzeby [transkrypcji](batch-transcription.md) i [Custom Speech](custom-speech-overview.md)partii. v 3.0 jest [następnikiem w wersji 2.0](./migrate-v2-to-v3.md).
- [Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](#speech-to-text-rest-api-for-short-audio) jest używany do transkrypcji online jako alternatywy dla [zestawu Speech SDK](speech-sdk.md). Żądania korzystające z tego interfejsu API mogą przesyłać do 60 sekund audio na żądanie. 

## <a name="speech-to-text-rest-api-v30"></a>Zamiana mowy na tekst interfejsu API REST v 3.0

Funkcja zamiany mowy na tekst "API REST v 3.0" jest używana na potrzeby [transkrypcji](batch-transcription.md) i [Custom Speech](custom-speech-overview.md)partii. Jeśli musisz komunikować się z transkrypcją OnLine za pośrednictwem usługi REST, użyj [interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku](#speech-to-text-rest-api-for-short-audio).

Użyj interfejsu API REST w wersji 3.0 do:
- Kopiuj modele do innych subskrypcji, jeśli chcesz, aby współpracownicy mieli dostęp do modelu skompilowanego lub w przypadkach, w których chcesz wdrożyć model w więcej niż jednym regionie
- Transkrypcja dane z kontenera (transkrypcja zbiorcza) oraz dostarczanie wielu adresów URL plików audio
- Przekazywanie danych z kont usługi Azure Storage przy użyciu identyfikatora URI sygnatury dostępu współdzielonego
- Pobierz dzienniki na punkt końcowy, jeśli zażądano dzienników dla tego punktu końcowego
- Zażądaj manifestu utworzonych modeli na potrzeby konfigurowania kontenerów lokalnych

Interfejs API REST v 3.0 obejmuje następujące funkcje:
- **Powiadomienia — elementy webhook**— wszystkie uruchomione procesy usługi obsługują teraz powiadomienia elementu webhook. Interfejs API REST w wersji 3.0 udostępnia wywołania umożliwiające zarejestrowanie elementów webhook, w których są wysyłane powiadomienia
- **Aktualizowanie modeli za punktami końcowymi** 
- **Adaptacja modelu z wieloma zestawami danych**— Dostosowywanie modelu przy użyciu wielu kombinacji zestawu danych akustycznych, językowych i wymowych
- **Przenoszenie własnego magazynu**— użyj własnych kont magazynu do przechowywania dzienników, plików transkrypcji i innych danych

Zapoznaj się z przykładami dotyczącymi używania interfejsu API REST v 3.0 z transkrypcją [partii.](batch-transcription.md)

Jeśli używasz funkcji zamiany mowy na tekst API v 2.0, zobacz jak można przeprowadzić migrację do wersji 3.0 w [tym przewodniku](./migrate-v2-to-v3.md).

W [tym miejscu](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)zapoznaj się z pełnym odwołaniem do interfejsu API REST w wersji 3.0.

## <a name="speech-to-text-rest-api-for-short-audio"></a>Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku

Jako alternatywę dla [zestawu Speech SDK](speech-sdk.md)usługa mowy umożliwia konwertowanie zamiany mowy na tekst za pomocą interfejsu API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany. Interfejs API REST dla krótkiego dźwięku jest bardzo ograniczony i powinien być używany tylko w przypadkach, gdy nie można użyć [zestawu Speech SDK](speech-sdk.md) .

Przed użyciem interfejsu API REST zamiany mowy na tekst dla krótkiego dźwięku należy wziąć pod uwagę następujące kwestie:

* Żądania, które używają interfejsu API REST do krótkiego audio i przesyłania audio bezpośrednio mogą zawierać maksymalnie 60 sekund audio.
* Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku zwraca jedynie wyniki końcowe. Nie podano częściowych wyników.

Jeśli wysyłanie dłuższego dźwięku jest wymagane dla aplikacji, należy rozważyć użycie [zestawu Speech SDK](speech-sdk.md) lub [interfejsu API REST zamiany mowy na tekst](#speech-to-text-rest-api-v30).

> [!TIP]
> Zobacz [ten artykuł](sovereign-clouds.md) , aby uzyskać Azure Government i punkty końcowe platformy Azure w Chinach.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Punkt końcowy interfejsu API REST dla krótkiego dźwięku ma następujący format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zamień na `<REGION_IDENTIFIER>` Identyfikator pasujący do regionu subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language musi być dołączony do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski w regionie zachodnie stany USA to: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Parametry zapytania

Te parametry mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `language` | Identyfikuje język mówiony, który jest rozpoznawany. Zobacz [obsługiwane języki](language-support.md#speech-to-text). | Wymagane |
| `format` | Określa format wyniku. Akceptowane wartości to `simple` i `detailed` . Proste wyniki obejmują `RecognitionStatus` , `DisplayText` , `Offset` , i `Duration` . Szczegółowe odpowiedzi obejmują cztery różne reprezentacje wyświetlanego tekstu. Ustawienie domyślne to `simple`. | Opcjonalne |
| `profanity` | Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `masked` , które zastępują braki z gwiazdkami, `removed` które usuwają z wyniku wszystkie niezbyt wulgarne dane lub `raw` , które zawierają braki w wyniku. Ustawienie domyślne to `masked`. | Opcjonalne |
| `cid` | W przypadku tworzenia niestandardowych modeli przy użyciu [portalu Custom Speech](./custom-speech-overview.md) można użyć niestandardowych modeli za pośrednictwem ich **identyfikatora punktu końcowego** na stronie **wdrożenia** . Użyj **identyfikatora punktu końcowego** jako argumentu `cid` parametru ciągu zapytania. | Opcjonalne |

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany mowy na tekst.

|Nagłówek| Opis | Wymagane/opcjonalne |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługi rozpoznawania mowy. | Ten nagłówek lub `Authorization` jest wymagany. |
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer` . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Ten nagłówek lub `Ocp-Apim-Subscription-Key` jest wymagany. |
| `Pronunciation-Assessment` | Określa parametry przedstawiające wyniki wymowy w wynikach rozpoznawania, które oceniają jakość wymowy danych wejściowych mowy, ze wskaźnikami dokładności, Fluency, kompletności itp. Ten parametr jest zakodowanym w formacie base64 JSON zawierającym wiele parametrów szczegółowych. Zobacz [Parametry oceny wymowy](#pronunciation-assessment-parameters) , aby dowiedzieć się, jak skompilować ten nagłówek. | Opcjonalne |
| `Content-type` | Opisuje format i koder-dekoder dostarczonych danych audio. Akceptowane wartości to `audio/wav; codecs=audio/pcm; samplerate=16000` i `audio/ogg; codecs=opus` . | Wymagane |
| `Transfer-Encoding` | Określa, że są wysyłane fragmenty danych audio, a nie jeden plik. Tego nagłówka należy używać tylko w przypadku fragmentowania danych audio. | Opcjonalne |
| `Expect` | W przypadku używania transferu fragmentarycznego Wyślij `Expect: 100-continue` . Usługa mowy potwierdza wstępne żądanie i czeka na dodatkowe dane.| Wymagane w przypadku wysyłania fragmentarycznych danych audio. |
| `Accept` | Jeśli jest podana, musi być `application/json` . Usługa mowy zapewnia wyniki w formacie JSON. Niektóre platformy żądań zapewniają niezgodną wartość domyślną. Dobrym sposobem jest zawsze uwzględnienie `Accept` . | Opcjonalne, ale zalecane. |

### <a name="audio-formats"></a>Formaty audio

Dźwięk jest wysyłany w treści `POST` żądania HTTP. Musi być w jednym z formatów w tej tabeli:

| Format | Wymaga | Szybkość transmisji bitów | Częstotliwość próbkowania  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 KB/s | 16 kHz, mono |
| OGG    | OPUS  | 256 kpbs | 16 kHz, mono |

>[!NOTE]
>Powyższe formaty są obsługiwane za pomocą interfejsu API REST dla krótkich plików audio i WebSocket w usłudze Speech. [Zestaw Speech SDK](speech-sdk.md) obecnie obsługuje format WAV z koderem-dekoder PCM oraz [innymi formatami](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parametry oceny wymowy

Ta tabela zawiera listę wymaganych i opcjonalnych parametrów oceny wymowy.

| Parametr | Opis | Wymagane? |
|-----------|-------------|---------------------|
| ReferenceText | Tekst, dla którego będzie oceniana wymowa. | Wymagane |
| GradingSystem | System punktu do kalibracji oceny. `FivePoint`System daje 0-5 wynik zmiennoprzecinkowy i `HundredMark` daje wynik o wartości 0-100 zmiennoprzecinkowej. Wartość domyślna: `FivePoint`. | Opcjonalne |
| Poziom szczegółowości | Stopień szczegółowości oceny. Akceptowane wartości to `Phoneme` , które pokazują wynik na poziomie pełnego tekstu, wyrazów i fonem, `Word` który pokazuje wynik na poziomie pełnego tekstu i słowa, `FullText` który pokazuje wynik tylko na poziomie pełnego tekstu. Ustawienie domyślne to `Phoneme`. | Opcjonalne |
| Wymiar | Definiuje kryteria danych wyjściowych. Akceptowane wartości to `Basic` , które wyświetlają tylko wynik dokładności, `Comprehensive` pokazują wyniki o większej liczbie wymiarów (np. wyniki Fluency oraz wynik kompletności na poziomie pełnego tekstu, typ błędu na poziomie wyrazu). Sprawdź [Parametry odpowiedzi](#response-parameters) , aby zobaczyć definicje różnych wymiarów oceny i typów błędów wyrazów. Ustawienie domyślne to `Basic`. | Opcjonalne |
| EnableMiscue | Włącza Obliczanie miscue. Po włączeniu tej możliwości wymawiane wyrazy będą porównywane z tekstem odwołania i będą oznaczone jako pomijania/wstawiania na podstawie porównania. Akceptowane wartości to `False` i `True` . Ustawienie domyślne to `False`. | Opcjonalne |
| ScenarioId | Identyfikator GUID wskazujący dostosowany system punktów. | Opcjonalne |

Poniżej znajduje się przykładowy kod JSON zawierający parametry oceny wymowy:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Poniższy przykładowy kod przedstawia sposób tworzenia parametrów oceny wymowy w `Pronunciation-Assessment` nagłówku:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Zdecydowanie zaleca się przekazywanie strumieniowe (fragmenty) podczas publikowania danych audio, co może znacząco skrócić czas oczekiwania. Aby włączyć przesyłanie strumieniowe, zobacz [przykładowy kod w różnych językach programowania](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) .

>[!NOTE]
>Funkcja oceny wymowy jest obecnie dostępna tylko w `en-US` języku.

### <a name="sample-request"></a>Przykładowe żądanie

Poniższy przykład zawiera nazwę hosta i wymagane nagłówki. Należy pamiętać, że usługa również oczekuje danych audio, które nie są uwzględnione w tym przykładzie. Jak wspomniano wcześniej, zaleca się, aby fragmenty nie były jednak wymagane.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Aby włączyć ocenę wymowy, można dodać poniższy nagłówek. Zobacz [Parametry oceny wymowy](#pronunciation-assessment-parameters) , aby dowiedzieć się, jak skompilować ten nagłówek.

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi oznacza powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwe przyczyny |
|------------------|-------------|-----------------|
| `100` | Kontynuuj | Początkowe żądanie zostało zaakceptowane. Kontynuuj wysyłanie reszty danych. (Używany z transferem fragmentarycznym) |
| `200` | OK | Żądanie zakończyło się pomyślnie. treść odpowiedzi jest obiektem JSON. |
| `400` | Złe żądanie | Nie podano kodu języka, nie jest to obsługiwanego języka, nieprawidłowy plik audio itd. |
| `401` | Brak autoryzacji | Klucz subskrypcji lub Token autoryzacji jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy. |
| `403` | Forbidden | Brak klucza subskrypcji lub tokenu autoryzacji. |

### <a name="chunked-transfer"></a>Przenoszenie fragmentaryczne

Przeniesienie fragmentaryczne ( `Transfer-Encoding: chunked` ) może pomóc w zmniejszeniu opóźnienia rozpoznawania. Dzięki temu usługa mowy może rozpocząć przetwarzanie pliku audio podczas jego przesyłania. Interfejs API REST dla krótkiego dźwięku nie zapewnia wyników częściowych ani pośrednich.

Ten przykładowy kod pokazuje, jak wysyłać audio w fragmentach. Tylko pierwszy fragment powinien zawierać nagłówek pliku dźwiękowego. `request` jest `HttpWebRequest` obiektem połączonym z odpowiednim punktem końcowym Rest. `audioFile` jest ścieżką do pliku audio na dysku.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Parametry odpowiedzi

Wyniki są podane jako dane JSON. `simple`Format zawiera te pola najwyższego poziomu.

| Parametr | Opis  |
|-----------|--------------|
|`RecognitionStatus`|Stan, na przykład `Success` w celu pomyślnego rozpoznania. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po kapitalizacji, interpunkcji, normalizacji tekstu odwrotnego (konwersja mówionego tekstu na krótsze formularze, takie jak 200 dla "200" lub "Dr. Smith" dla "lekarza Kowalski") i maskowania niewulgarności. Występuje tylko po powodzeniu.|
|`Offset`|Czas (w jednostkach 100-nanosekund), w którym rozpoznana funkcja mowy zaczyna się w strumieniu audio.|
|`Duration`|Czas trwania (w jednostkach 100-nanosekund) rozpoznanej mowy w strumieniu audio.|

`RecognitionStatus`Pole może zawierać następujące wartości:

| Stan | Opis |
|--------|-------------|
| `Success` | Rozpoznanie zakończyło się pomyślnie i `DisplayText` pole jest obecne. |
| `NoMatch` | Wykryto mowę w strumieniu audio, ale nie zostały dopasowane żadne wyrazy z języka docelowego. Zazwyczaj oznacza to, że język rozpoznawania to inny język od użytkownika. |
| `InitialSilenceTimeout` | Początek strumienia audio zawiera tylko ciszą, a usługa przekroczyła limit czasu podczas oczekiwania na mowę. |
| `BabbleTimeout` | Początek strumienia audio zawiera tylko szum, a usługa przekroczyła limit czasu podczas oczekiwania na mowę. |
| `Error` | Usługa rozpoznawania napotkała błąd wewnętrzny i nie może kontynuować. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli dźwięk składa się tylko z niewulgarności, a `profanity` parametr zapytania jest ustawiony na `remove` , usługa nie zwraca wyniku mowy.

`detailed`Format zawiera dodatkowe formy rozpoznanych wyników.
W przypadku korzystania z `detailed` formatu, `DisplayText` jest dostępny jako `Display` dla każdego wyniku na `NBest` liście.

Obiekt na `NBest` liście może obejmować:

| Parametr | Opis |
|-----------|-------------|
| `Confidence` | Wynik zaufania wpisu z 0,0 (brak pewności) do 1,0 (pełny poziom pewności) |
| `Lexical` | Leksykalna forma rozpoznanego tekstu: faktyczne wyrazy zostały rozpoznane. |
| `ITN` | Forma odwrotna — znormalizowana ("kanoniczna") tekst rozpoznanego tekstu, z numerami telefonu, cyframi, skrótami ("Lekarz Kowalski" do "Dr Kowalski") i innymi przekształceniami. |
| `MaskedITN` | Jeśli zażądano formularza ITN z zastosowanym maską wulgarności. |
| `Display` | Formularz wyświetlania rozpoznanego tekstu z dodaną interpunkcją i wielką literą. Ten parametr jest taki sam, jak w `DisplayText` przypadku ustawienia format `simple` . |
| `AccuracyScore` | Dokładność wymowy mowy. Dokładność wskazuje, jak blisko fonemów jest zgodny z wymowyeniem prezentera natywnego. Wynik dokładności wyrazu i pełnego tekstu jest agregowany z oceny dokładności poziomu fonem. |
| `FluencyScore` | Fluency danego mowy. Fluency wskazuje, jak ściśle funkcja mowy dopasowuje ciche przerwy między wyrazami. |
| `CompletenessScore` | Kompletność mowy, ustalona przez obliczenie współczynnika wymawianych wyrazów w odniesieniu do danych wejściowych tekstu. |
| `PronScore` | Ogólny wynik wskazujący jakość wymowy danego mowy. Ta wartość jest agregowana `AccuracyScore` z `FluencyScore` i `CompletenessScore` wagi. |
| `ErrorType` | Ta wartość wskazuje, czy słowo zostanie pominięte, wstawione lub nieprawidłowo wypowiadane, w porównaniu z `ReferenceText` . Możliwe wartości to `None` (brak błędu w tym wyrazie), `Omission` `Insertion` i `Mispronunciation` . |

### <a name="sample-responses"></a>Przykładowe odpowiedzi

Typowa odpowiedź na potrzeby `simple` rozpoznawania:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typowa odpowiedź na potrzeby `detailed` rozpoznawania:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Typowa odpowiedź na potrzeby rozpoznawania z oceną wymowy:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](./how-to-custom-speech-train-model.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](./how-to-custom-speech-train-model.md)
- [Zapoznaj się z transkrypcją partii](batch-transcription.md)