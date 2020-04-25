---
title: Dokumentacja interfejsu API zamiany mowy na tekst (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST zamiany mowy na tekst. Ten artykuł zawiera informacje o opcjach autoryzacji, opcjach zapytań, sposobach tworzenia struktury żądania i otrzymywaniu odpowiedzi.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: yinhew
ms.openlocfilehash: 005824b0953be741f47c027d121dbe073adca3ba
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131299"
---
# <a name="speech-to-text-rest-api"></a>Interfejs API REST zamiany mowy na tekst

Jako alternatywę dla [zestawu Speech SDK](speech-sdk.md)usługa mowy umożliwia konwertowanie zamiany mowy na tekst za pomocą interfejsu API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany. Interfejs API REST jest bardzo ograniczony i powinien być używany tylko w przypadkach, gdy nie można użyć [zestawu Speech SDK](speech-sdk.md) .

Przed użyciem interfejsu API REST zamiany mowy na tekst należy zrozumieć następujące informacje:

* Żądania korzystające z interfejsu API REST i bezpośredniego przesyłania dźwięku mogą zawierać maksymalnie 60 sekund audio.
* Interfejs API REST zamiany mowy na tekst zwraca tylko końcowe wyniki. Nie podano częściowych wyników.

Jeśli wysyłanie dłuższego dźwięku jest wymagane dla aplikacji, należy rozważyć użycie [zestawu Speech SDK](speech-sdk.md) lub interfejsu API REST opartego na plikach, takiego jak [transkrypcja partii](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Punkt końcowy interfejsu API REST ma następujący format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zamień `<REGION_IDENTIFIER>` na identyfikator pasujący do regionu subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language musi być dołączony do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski w regionie zachodnie stany USA to: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Parametry zapytania

Te parametry mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| `language` | Identyfikuje język mówiony, który jest rozpoznawany. Zobacz [obsługiwane języki](language-support.md#speech-to-text). | Wymagany |
| `format` | Określa format wyniku. Akceptowane wartości to `simple` i `detailed`. Proste wyniki obejmują `RecognitionStatus`, `DisplayText`, `Offset`, i `Duration`. Szczegółowe odpowiedzi obejmują wiele wyników z wartościami ufności i czterema różnymi reprezentacjami. Ustawienie domyślne to `simple`. | Optional (Opcjonalność) |
| `profanity` | Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `masked`, które zastępują braki z gwiazdkami `removed`, które usuwają z wyniku wszystkie niezbyt wulgarne `raw`dane lub, które zawierają braki w wyniku. Ustawienie domyślne to `masked`. | Optional (Opcjonalność) |
| `pronunciationScoreParams` | Określa parametry przedstawiające wyniki wymowy w wynikach rozpoznawania, które oceniają jakość wymowy danych wejściowych mowy, ze wskaźnikami dokładności, Fluency, kompletności itp. Ten parametr jest zakodowanym w formacie base64 JSON zawierającym wiele parametrów szczegółowych. Zobacz [Parametry oceny wymowy](#pronunciation-assessment-parameters) , aby dowiedzieć się, jak skompilować ten parametr. | Optional (Opcjonalność) |
| `cid` | W przypadku tworzenia niestandardowych modeli przy użyciu [portalu Custom Speech](how-to-custom-speech.md) można użyć niestandardowych modeli za pośrednictwem ich **identyfikatora punktu końcowego** na stronie **wdrożenia** . Użyj **identyfikatora punktu końcowego** jako argumentu parametru ciągu `cid` zapytania. | Optional (Opcjonalność) |

## <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany mowy na tekst.

|Nagłówek| Opis | Wymagane/opcjonalne |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługi rozpoznawania mowy. | Ten nagłówek lub `Authorization` jest wymagany. |
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Ten nagłówek lub `Ocp-Apim-Subscription-Key` jest wymagany. |
| `Content-type` | Opisuje format i koder-dekoder dostarczonych danych audio. Akceptowane wartości to `audio/wav; codecs=audio/pcm; samplerate=16000` i `audio/ogg; codecs=opus`. | Wymagany |
| `Transfer-Encoding` | Określa, że są wysyłane fragmenty danych audio, a nie jeden plik. Tego nagłówka należy używać tylko w przypadku fragmentowania danych audio. | Optional (Opcjonalność) |
| `Expect` | W przypadku używania transferu fragmentarycznego Wyślij `Expect: 100-continue`. Usługa mowy potwierdza wstępne żądanie i czeka na dodatkowe dane.| Wymagane w przypadku wysyłania fragmentarycznych danych audio. |
| `Accept` | Jeśli jest podana, musi być `application/json`. Usługa mowy zapewnia wyniki w formacie JSON. Niektóre platformy żądań zapewniają niezgodną wartość domyślną. Dobrym sposobem jest zawsze uwzględnienie `Accept`. | Opcjonalne, ale zalecane. |

## <a name="audio-formats"></a>Formaty audio

Dźwięk jest wysyłany w treści żądania HTTP `POST` . Musi być w jednym z formatów w tej tabeli:

| Format | Wymaga | Multimedia | Częstotliwość próbkowania  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bitowy  | 16 kHz, mono |
| OGG    | OPUS  | 16-bitowy  | 16 kHz, mono |

>[!NOTE]
>Powyższe formaty są obsługiwane za pomocą interfejsu API REST i protokołu WebSocket w usłudze Speech. [Zestaw Speech SDK](speech-sdk.md) obecnie obsługuje format WAV z koderem-dekoder PCM oraz [innymi formatami](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="pronunciation-assessment-parameters"></a>Parametry oceny wymowy

Ta tabela zawiera listę wymaganych i opcjonalnych parametrów oceny wymowy.

| Parametr | Opis | Wymagane/opcjonalne |
|-----------|-------------|---------------------|
| ReferenceText | Tekst, dla którego będzie oceniana wymowa. | Wymagany |
| GradingSystem | System punktu do kalibracji oceny. Akceptowane wartości to `FivePoint` i `HundredMark`. Ustawienie domyślne to `FivePoint`. | Optional (Opcjonalność) |
| Poziom szczegółowości | Stopień szczegółowości oceny. Akceptowane wartości to `Phoneme`, które pokazują wynik na poziomie `Word`pełnego tekstu, wyrazów i fonem, który pokazuje wynik na poziomie `FullText`pełnego tekstu i słowa, który pokazuje wynik tylko na poziomie pełnego tekstu. Ustawienie domyślne to `Phoneme`. | Optional (Opcjonalność) |
| Wymiar | Definiuje kryteria danych wyjściowych. Akceptowane wartości to `Basic`, które wyświetlają tylko wynik dokładności, `Comprehensive` pokazują wyniki o większej liczbie wymiarów (np. wyniki Fluency oraz wynik kompletności na poziomie pełnego tekstu, typ błędu na poziomie wyrazu). Sprawdź [Parametry odpowiedzi](#response-parameters) , aby zobaczyć definicje różnych wymiarów oceny i typów błędów wyrazów. Ustawienie domyślne to `Basic`. | Optional (Opcjonalność) |
| EnableMiscue | Włącza Obliczanie miscue. Po włączeniu tej możliwości wymawiane wyrazy będą porównywane z tekstem odwołania i będą oznaczone jako pomijania/wstawiania na podstawie porównania. Akceptowane wartości to `False` i `True`. Ustawienie domyślne to `False`. | Optional (Opcjonalność) |
| ScenarioId | Identyfikator GUID wskazujący dostosowany system punktów. | Optional (Opcjonalność) |

Poniżej znajduje się przykładowy kod JSON zawierający parametry oceny wymowy:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Poniższy przykładowy kod przedstawia sposób tworzenia parametrów oceny wymowy w parametrze zapytania URL:

```csharp
var pronunciationScoreParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronunciationScoreParamsBytes = Encoding.UTF8.GetBytes(pronunciationScoreParamsJson);
var pronunciationScoreParams = Convert.ToBase64String(pronunciationScoreParamsBytes);
```

## <a name="sample-request"></a>Przykładowe żądanie

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

## <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi oznacza powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| `100` | Kontynuuj | Początkowe żądanie zostało zaakceptowane. Kontynuuj wysyłanie reszty danych. (Używany z transferem fragmentarycznym) |
| `200` | OK | Żądanie zakończyło się pomyślnie. treść odpowiedzi jest obiektem JSON. |
| `400` | Złe żądanie | Nie podano kodu języka, nie jest to obsługiwanego języka, nieprawidłowy plik audio itd. |
| `401` | Brak autoryzacji | Klucz subskrypcji lub Token autoryzacji jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy. |
| `403` | Forbidden | Brak klucza subskrypcji lub tokenu autoryzacji. |

## <a name="chunked-transfer"></a>Przenoszenie fragmentaryczne

Przeniesienie fragmentaryczne (`Transfer-Encoding: chunked`) może pomóc w zmniejszeniu opóźnienia rozpoznawania. Dzięki temu usługa mowy może rozpocząć przetwarzanie pliku audio podczas jego przesyłania. Interfejs API REST nie zapewnia wyników częściowych ani pośrednich.

Ten przykładowy kod pokazuje, jak wysyłać audio w fragmentach. Tylko pierwszy fragment powinien zawierać nagłówek pliku dźwiękowego. `request`jest `HttpWebRequest` obiektem połączonym z odpowiednim punktem końcowym Rest. `audioFile`jest ścieżką do pliku audio na dysku.

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

## <a name="response-parameters"></a>Parametry odpowiedzi

Wyniki są podane jako dane JSON. `simple` Format zawiera te pola najwyższego poziomu.

| Parametr | Opis  |
|-----------|--------------|
|`RecognitionStatus`|Stan, `Success` na przykład w celu pomyślnego rozpoznania. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po kapitalizacji, interpunkcji, normalizacji tekstu odwrotnego (konwersja mówionego tekstu na krótsze formularze, takie jak 200 dla "200" lub "Dr. Smith" dla "lekarza Kowalski") i maskowania niewulgarności. Występuje tylko po powodzeniu.|
|`Offset`|Czas (w jednostkach 100-nanosekund), w którym rozpoznana funkcja mowy zaczyna się w strumieniu audio.|
|`Duration`|Czas trwania (w jednostkach 100-nanosekund) rozpoznanej mowy w strumieniu audio.|

`RecognitionStatus` Pole może zawierać następujące wartości:

| Stan | Opis |
|--------|-------------|
| `Success` | Rozpoznanie zakończyło się `DisplayText` pomyślnie i pole jest obecne. |
| `NoMatch` | Wykryto mowę w strumieniu audio, ale nie zostały dopasowane żadne wyrazy z języka docelowego. Zazwyczaj oznacza to, że język rozpoznawania to inny język od użytkownika. |
| `InitialSilenceTimeout` | Początek strumienia audio zawiera tylko ciszą, a usługa przekroczyła limit czasu podczas oczekiwania na mowę. |
| `BabbleTimeout` | Początek strumienia audio zawiera tylko szum, a usługa przekroczyła limit czasu podczas oczekiwania na mowę. |
| `Error` | Usługa rozpoznawania napotkała błąd wewnętrzny i nie może kontynuować. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli dźwięk składa się tylko z niewulgarności, `profanity` a parametr zapytania jest ustawiony `remove`na, usługa nie zwraca wyniku mowy.

`detailed` Format zawiera te same dane co `simple` format, wraz z `NBest`listą alternatywną interpretacji tego samego wyniku rozpoznawania. Te wyniki są uszeregowane od najbardziej najprawdopodobniej do najmniej najprawdopodobniej. Pierwszy wpis jest taki sam jak główny wynik rozpoznawania.  W przypadku korzystania `detailed` z formatu `DisplayText` , jest dostępny `Display` jako dla każdego wyniku na `NBest` liście.

Każdy obiekt na `NBest` liście zawiera:

| Parametr | Opis |
|-----------|-------------|
| `Confidence` | Wynik zaufania wpisu z 0,0 (brak pewności) do 1,0 (pełny poziom pewności) |
| `Lexical` | Leksykalna forma rozpoznanego tekstu: faktyczne wyrazy zostały rozpoznane. |
| `ITN` | Forma odwrotna — znormalizowana ("kanoniczna") tekst rozpoznanego tekstu, z numerami telefonu, cyframi, skrótami ("Lekarz Kowalski" do "Dr Kowalski") i innymi przekształceniami. |
| `MaskedITN` | Jeśli zażądano formularza ITN z zastosowanym maską wulgarności. |
| `Display` | Formularz wyświetlania rozpoznanego tekstu z dodaną interpunkcją i wielką literą. Ten parametr jest taki sam, `DisplayText` jak w przypadku ustawienia format `simple`. |
| `AccuracyScore` | Wynik wskazujący dokładność wymowy danego mowy. |
| `FluencyScore` | Wynik wskazujący Fluency danej mowy. |
| `CompletenessScore` | Wynik wskazujący kompletność danego mowy, obliczając stosunek wymawianych słów do całego danych wejściowych. |
| `PronScore` | Ogólny wynik wskazujący jakość wymowy danego mowy. Jest on obliczany `AccuracyScore`z `FluencyScore` , `CompletenessScore` i z wag. |
| `ErrorType` | Ta wartość wskazuje, czy słowo zostanie pominięte, wstawione lub nieprawidłowo wypowiadane, w `ReferenceText`porównaniu z. Możliwe wartości to `None` (brak błędu w tym wyrazie), `Omission` `Insertion` i `Mispronunciation`. |

## <a name="sample-responses"></a>Przykładowe odpowiedzi

Typowa odpowiedź na `simple` potrzeby rozpoznawania:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typowa odpowiedź na `detailed` potrzeby rozpoznawania:

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
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
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

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
