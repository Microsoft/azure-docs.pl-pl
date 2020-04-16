---
title: Odwołanie do interfejsu API zamiany mowy na tekst (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak korzystać z interfejsu API REST z zamianą mowy na tekst. W tym artykule dowiesz się o opcjach autoryzacji, opcjach zapytań, sposobie struktury żądania i otrzymaniu odpowiedzi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400184"
---
# <a name="speech-to-text-rest-api"></a>Interfejs API REST zamiany mowy na tekst

Jako alternatywa dla [SDK mowy,](speech-sdk.md)usługa Mowy umożliwia konwertowanie mowy na tekst za pomocą interfejsu API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany. Interfejs API REST jest bardzo ograniczony i powinien być używany tylko w przypadkach, gdy nie można [użyć sdk mowy.](speech-sdk.md)

Przed użyciem interfejsu API REST z zamianą mowy na tekst należy zrozumieć:

* Żądania korzystające z interfejsu API REST i przesyłane bezpośrednio audio mogą zawierać tylko do 60 sekund dźwięku.
* Interfejs API REST z zamianą mowy na tekst zwraca tylko wyniki końcowe. Częściowe wyniki nie są dostarczane.

Jeśli wysyłanie dłuższego dźwięku jest wymagane dla aplikacji, należy rozważyć użycie [SDK mowy](speech-sdk.md) lub interfejsu API REST opartego na plikach, takiego jak [transkrypcja wsadowa](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Punkt końcowy interfejsu API REST ma ten format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Zamień `<REGION_IDENTIFIER>` identyfikator odpowiadający regionowi subskrypcji z tej tabeli:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr języka musi być dołączona do adresu URL, aby uniknąć otrzymania błędu HTTP 4xx. Na przykład język ustawiony na angielski amerykański przy użyciu `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`punktu końcowego zachodniego stanów USA to: .

## <a name="query-parameters"></a>Parametry zapytania

Parametry te mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane / opcjonalnie |
|-----------|-------------|---------------------|
| `language` | Identyfikuje język mówiony, który jest rozpoznawany. Zobacz [Obsługiwane języki](language-support.md#speech-to-text). | Wymagany |
| `format` | Określa format wyniku. Akceptowane wartości `simple` są `detailed`i . Proste wyniki `RecognitionStatus` `DisplayText`obejmują `Offset`, `Duration`, i . Szczegółowe odpowiedzi obejmują wiele wyników z wartościami zaufania i cztery różne reprezentacje. Ustawienie domyślne to `simple`. | Optional (Opcjonalność) |
| `profanity` | Określa sposób obsługi wulgaryzmów w wynikach rozpoznawania. Akceptowane wartości `masked`to , które zastępują wulgaryzmy gwiazdkami, `removed`, które usuwa wszystkie `raw`wulgaryzmy z wyniku, lub , które zawierają wulgaryzmy w wyniku. Ustawienie domyślne to `masked`. | Optional (Opcjonalność) |
| `cid` | Podczas tworzenia modeli niestandardowych [modułów rozpoznawania mowy](how-to-custom-speech.md) za pomocą portalu mowy niestandardowej można używać modeli niestandardowych za pomocą identyfikatora punktu **końcowego** znajdującego się na stronie **Wdrażanie.** Użyj **identyfikatora punktu końcowego** jako `cid` argumentu do parametru ciągu zapytania. | Optional (Opcjonalność) |

## <a name="request-headers"></a>Nagłówki żądań

W tej tabeli wymieniono wymagane i opcjonalne nagłówki dla żądań zamiany mowy na tekst.

|Nagłówek| Opis | Wymagane / opcjonalnie |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługi mowy. | Ten nagłówek lub `Authorization` jest wymagany. |
| `Authorization` | Token autoryzacji poprzedzony `Bearer`słowem . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Ten nagłówek lub `Ocp-Apim-Subscription-Key` jest wymagany. |
| `Content-type` | Opisuje format i kodek podanych danych audio. Akceptowane wartości `audio/wav; codecs=audio/pcm; samplerate=16000` są `audio/ogg; codecs=opus`i . | Wymagany |
| `Transfer-Encoding` | Określa, że fragmentowane dane audio są wysyłane, a nie pojedynczy plik. Tego nagłówka należy używać tylko w przypadku fragmentowania danych audio. | Optional (Opcjonalność) |
| `Expect` | Jeśli używasz transferu `Expect: 100-continue`fragmentowanego, wyślij . Usługa mowy potwierdza początkowe żądanie i oczekuje na dodatkowe dane.| Wymagane w przypadku wysyłania danych audio w posmkniętych fragmentach. |
| `Accept` | Jeśli jest to `application/json`przewidziane, musi być . Usługa mowy zapewnia wyniki w JSON. Niektóre struktury żądań zapewniają niezgodną wartość domyślną. Dobrą praktyką jest `Accept`zawsze włączanie . | Opcjonalne, ale zalecane. |

## <a name="audio-formats"></a>Formaty audio

Dźwięk jest wysyłany w `POST` treści żądania HTTP. Musi być w jednym z formatów w tej tabeli:

| Format | Kodek | Bitrate | Częstotliwość próbkowania  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16-bitowy  | 16 kHz, mono |
| Ogg    | Opus  | 16-bitowy  | 16 kHz, mono |

>[!NOTE]
>Powyższe formaty są obsługiwane za pośrednictwem interfejsu API REST i WebSocket w usłudze mowy. [Zestaw SDK mowy](speech-sdk.md) obsługuje obecnie format WAV z kodekiem PCM, a także innymi [formatami.](how-to-use-codec-compressed-audio-input-streams.md)

## <a name="sample-request"></a>Przykładowe żądanie

Poniższy przykład zawiera nazwa hosta i wymagane nagłówki. Należy pamiętać, że usługa oczekuje również danych audio, które nie są uwzględnione w tym przykładzie. Jak wspomniano wcześniej, fragmentowanie jest zalecane, jednak nie jest wymagane.

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

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwy powód |
|------------------|-------------|-----------------|
| `100` | Kontynuuj | Wstępne żądanie zostało zaakceptowane. Kontynuuj wysyłanie pozostałych danych. (Używany z transferem fragmentowym) |
| `200` | OK | Wniosek został rozpatrzony pozytywnie; treść odpowiedzi jest obiektem JSON. |
| `400` | Zła prośba | Nie podano kodu języka, nie jest obsługiwanym językiem, nieprawidłowym plikiem audio itp. |
| `401` | Brak autoryzacji | Klucz subskrypcji lub token autoryzacji jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy. |
| `403` | Forbidden | Brak klucza subskrypcji lub tokenu autoryzacji. |

## <a name="chunked-transfer"></a>Transfer w kawałkach

Transfer fragmentowany`Transfer-Encoding: chunked`( ) może pomóc zmniejszyć opóźnienie rozpoznawania. Umożliwia usługi Mowy rozpocząć przetwarzanie pliku audio, gdy jest transmitowany. Interfejs API REST nie zapewnia wyników częściowych ani tymczasowych.

W tym przykładzie kodu pokazano, jak wysyłać dźwięk we fragmentach. Tylko pierwszy fragment powinien zawierać nagłówek pliku audio. `request`jest `HttpWebRequest` obiektem połączonym z odpowiednim punktem końcowym REST. `audioFile`to ścieżka do pliku audio na dysku.

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

Wyniki są dostarczane jako JSON. Format `simple` zawiera te pola najwyższego poziomu.

| Parametr | Opis  |
|-----------|--------------|
|`RecognitionStatus`|Status, na `Success` przykład dla pomyślnego rozpoznania. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po kapitalizacji, interpunkcji, odwrotnej normalizacji tekstu (konwersja tekstu mówionego na krótsze formularze, takie jak 200 dla "dwieście" lub "Dr Smith" dla "doktor smith") i maskowanie wulgaryzmów. Obecny tylko na sukces.|
|`Offset`|Czas (w jednostkach 100 nanosekund), w którym rozpoznawana mowa rozpoczyna się w strumieniu audio.|
|`Duration`|Czas trwania (w jednostkach 100 nanosekund) rozpoznanej mowy w strumieniu audio.|

Pole `RecognitionStatus` może zawierać następujące wartości:

| Stan | Opis |
|--------|-------------|
| `Success` | Uznanie zakończyło się `DisplayText` sukcesem, a pole jest obecne. |
| `NoMatch` | W strumieniu audio wykryto mowę, ale nie zostały dopasowane żadne słowa z języka docelowego. Zazwyczaj oznacza, że język rozpoznawania jest inny niż język, który użytkownik mówi. |
| `InitialSilenceTimeout` | Początek strumienia audio zawierał tylko ciszę, a usługa przesunął limit czasu oczekiwania na mowę. |
| `BabbleTimeout` | Początek strumienia audio zawierał tylko szumy, a limit czasu usługi został przesunięty na mowę. |
| `Error` | Usługa rozpoznawania napotkała błąd wewnętrzny i nie mogła kontynuować. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli dźwięk składa się tylko z `profanity` wulgaryzmów, a parametr kwerendy jest ustawiony na `remove`, usługa nie zwraca wynik mowy.

Format `detailed` zawiera te same `simple` dane co `NBest`format, wraz z listą alternatywnych interpretacji tego samego wyniku rozpoznawania. Wyniki te są klasyfikowane od najbardziej prawdopodobne do najmniej prawdopodobne. Pierwszy wpis jest taki sam jak wynik głównego rozpoznawania.  Podczas korzystania `detailed` z `DisplayText` formatu, jest podana, jak `Display` dla każdego wyniku na `NBest` liście.

Każdy obiekt `NBest` na liście zawiera:

| Parametr | Opis |
|-----------|-------------|
| `Confidence` | Wynik zaufania wpisu od 0,0 (brak zaufania) do 1,0 (pełne zaufanie) |
| `Lexical` | Leksykalny kształt rozpoznanego tekstu: rzeczywiste wyrazy rozpoznane. |
| `ITN` | Odwrotna-tekst znormalizowane ("kanoniczny") forma rozpoznanego tekstu, z numerami telefonów, cyframi, skrótami ("doktor smith" do "dr smith") i innymi zastosowanymi przekształceniami. |
| `MaskedITN` | Formularz ITN z zastosowanym maskowaniem wulgaryzmów, jeśli jest to wymagane. |
| `Display` | Forma wyświetlania rozpoznanego tekstu z dodanymi literami i literami. Ten parametr jest `DisplayText` taki sam, jak `simple`podany, gdy format jest ustawiony na . |

## <a name="sample-responses"></a>Przykładowe odpowiedzi

Typowa odpowiedź `simple` na rozpoznanie:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typowa odpowiedź `detailed` na rozpoznanie:

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

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
