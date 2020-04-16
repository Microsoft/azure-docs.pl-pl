---
title: Odwołanie do interfejsu API zamiany tekstu na mowę (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak korzystać z interfejsu API REST między tekstem na mowę. W tym artykule dowiesz się o opcjach autoryzacji, opcjach zapytań, sposobie struktury żądania i otrzymaniu odpowiedzi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 77bba9433052c00df671caf73198ff75356b1c9a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400166"
---
# <a name="text-to-speech-rest-api"></a>Interfejs API REST zamiany tekstu na mowę

Usługa Mowy umożliwia [konwertowanie tekstu na mowę syntetyzowaną](#convert-text-to-speech) i [uzyskanie listy obsługiwanych głosów](#get-a-list-of-voices) dla regionu przy użyciu zestawu interfejsów API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Wymagany jest klucz subskrypcji dla punktu końcowego/regionu, którego zamierzasz użyć.

Interfejs API REST zamiany tekstu na mowę obsługuje neuronowe i standardowe głosy zamiany tekstu na mowę, z których każdy obsługuje określony język i dialekt, identyfikowany przez ustawienia regionalne.

* Aby uzyskać pełną listę głosów, zobacz [obsługa języka](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regiony](regions.md#text-to-speech).

> [!IMPORTANT]
> Koszty różnią się w przypadku głosów standardowych, niestandardowych i neuronowych. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Przed użyciem tego interfejsu API należy zrozumieć:

* Interfejs API REST między tekstem na mowę wymaga nagłówka autoryzacji. Oznacza to, że należy wykonać wymianę tokenu, aby uzyskać dostęp do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Wyświetlanie listy głosów

Punkt `voices/list` końcowy umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego.

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

| Region | Endpoint |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Środkowe stany USA | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Wschodnie stany USA | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowo-środkowe stany USA | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azja Południowo-Wschodnia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Zachodnia | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Nagłówki żądań

W tej tabeli wymieniono wymagane i opcjonalne nagłówki dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane / opcjonalnie |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzony `Bearer`słowem . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagany |

### <a name="request-body"></a>Treść żądania

Treść nie jest wymagana `GET` dla żądań do tego punktu końcowego.

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie wymaga tylko nagłówka autoryzacji.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Przykładowa odpowiedź

Odpowiedź ta została obcięty, aby zilustrować strukturę odpowiedzi.

> [!NOTE]
> Dostępność głosu zależy od regionu/punktu końcowego.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwy powód |
|------------------|-------------|-----------------|
| 200 | OK | Wniosek został rozpatrzony pozytywnie. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest brak, pusty lub null. Lub wartość przekazana do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Częstym problemem jest nagłówek, który jest zbyt długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowane. Sprawdź, czy klucz subskrypcji lub token jest prawidłowy i w odpowiednim regionie. |
| 429 | Zbyt wiele żądań | Przekroczono przydział lub stawkę żądań dozwolonych dla subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub po stronie serwera. Może również wskazywać nieprawidłowe nagłówki. |


## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

Punkt `v1` końcowy umożliwia konwertowanie tekstu na mowę przy użyciu [języka SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Te regiony są obsługiwane dla zamiany tekstu na mowę przy użyciu interfejsu API REST. Upewnij się, że wybrano punkt końcowy, który pasuje do regionu subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Nagłówki żądań

W tej tabeli wymieniono wymagane i opcjonalne nagłówki dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane / opcjonalnie |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzony `Bearer`słowem . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagany |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Zaakceptowana wartość: `application/ssml+xml`. | Wymagany |
| `X-Microsoft-OutputFormat` | Określa format wyjścia audio. Aby uzyskać pełną listę zaakceptowanych wartości, zobacz [wyjścia audio](#audio-outputs). | Wymagany |
| `User-Agent` | Nazwa aplikacji. Podana wartość musi być mniejsza niż 255 znaków. | Wymagany |

### <a name="audio-outputs"></a>Wyjścia audio

Jest to lista obsługiwanych formatów audio, które są `X-Microsoft-OutputFormat` wysyłane w każdym żądaniu jako nagłówek. Każdy zawiera bitrate i typ kodowania. Usługa Mowy obsługuje wyjścia audio 24 kHz, 16 kHz i 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Jeśli wybrany format głosu i wyjścia ma różne szybkości transmisji bitów, w razie potrzeby dźwięk zostanie ponownie przespróbowany. Jednak głosy 24 kHz `audio-16khz-16kbps-mono-siren` nie `riff-16khz-16kbps-mono-siren` obsługują formatów wyjściowych.

### <a name="request-body"></a>Treść żądania

Treść każdego `POST` żądania jest wysyłana jako [język znaczników syntezy mowy (SSML).](speech-synthesis-markup.md) SSML umożliwia wybranie głosu i języka syntetyzowanej mowy zwracanej przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [obsługa języka](language-support.md#text-to-speech).

> [!NOTE]
> W przypadku używania niestandardowego głosu treść żądania może być wysyłana jako zwykły tekst (ASCII lub UTF-8).

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa SSML do określenia głosu i języka. Jeśli długość korpusu jest długa, a wynikowy dźwięk przekracza 10 minut - jest obcinany do 10 minut. Innymi słowy długość dźwięku nie może przekraczać 10 minut.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Zapoznaj się z naszymi przewodnikami Szybki start, aby zapoznać się z przykładami specyficznymi dla języka:

* [.NET Rdzeń, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwy powód |
|------------------|-------------|-----------------|
| 200 | OK | Wniosek został rozpatrzony pozytywnie; treść odpowiedzi jest plikiem audio. |
| 400 | Nieprawidłowe żądanie | Wymagany parametr jest brak, pusty lub null. Lub wartość przekazana do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Częstym problemem jest nagłówek, który jest zbyt długi. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowane. Sprawdź, czy klucz subskrypcji lub token jest prawidłowy i w odpowiednim regionie. |
| 413 | Jednostka żądania jest za duża | Wejście SSML jest dłuższe niż 1024 znaków. |
| 415 | Nieobsługiwał typ nośnika | Możliwe, że źle `Content-Type` został dostarczony. `Content-Type`należy ustawić `application/ssml+xml`na . |
| 429 | Zbyt wiele żądań | Przekroczono przydział lub stawkę żądań dozwolonych dla subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub po stronie serwera. Może również wskazywać nieprawidłowe nagłówki. |

Jeśli stan HTTP `200 OK`jest , treść odpowiedzi zawiera plik audio w żądanym formacie. Ten plik można odtwarzać podczas przesyłania, zapisywania w buforze lub zapisywania go w pliku.

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services)
- [Asynchronika synteza dla dźwięku o długiej formie](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
