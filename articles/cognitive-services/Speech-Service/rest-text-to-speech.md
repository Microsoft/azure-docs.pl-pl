---
title: Dokumentacja interfejsu API funkcji tekstu na mowę (REST) — usługa rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST funkcji tekstu na mowę. W tym artykule dowiesz się więcej na temat opcji autoryzacji, opcji zapytań, sposobu struktury żądania i odbierania odpowiedzi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 0065b6f4a7039e2883bca6acd5cf659be7b71069
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717679"
---
# <a name="text-to-speech-rest-api"></a>Interfejs API REST zamiany tekstu na mowę

Usługa rozpoznawania mowy umożliwia [konwertowanie](#convert-text-to-speech) tekstu [](#get-a-list-of-voices) na mowę syntetyzowana i uzyskiwanie listy obsługiwanych głosów dla regionu przy użyciu zestawu interfejsów API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Wymagany jest klucz subskrypcji dla punktu końcowego/regionu, który ma być potrzebny.

Interfejs API REST funkcji "tekst na mowę" obsługuje neuronowe i standardowe głosy funkcji tekstu na mowę, z których każdy obsługuje określony język i dialekt identyfikowany przez ustawienia lokalne.

* Aby uzyskać pełną listę głosów, zobacz [obsługa języka](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regiony](regions.md#text-to-speech).

> [!IMPORTANT]
> Koszty różnią się w przypadku standardowych, niestandardowych i neuronowych głosów. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Przed rozpoczęciem korzystania z tego interfejsu API należy zrozumieć:

* Interfejs API REST funkcji tekstu na mowę wymaga nagłówka autoryzacji. Oznacza to, że należy ukończyć wymianę tokenu, aby uzyskać dostęp do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

> [!TIP]
> Zapoznaj [się z tym artykułem,](sovereign-clouds.md) aby Azure Government punkty końcowe platformy Azure w Chinach.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Uzyskiwanie listy głosów

Punkt końcowy umożliwia uzyskiwanie pełnej listy głosów `voices/list` dla określonego regionu/punktu końcowego.

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

| Region (Region) | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazylia Południowa | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada Środkowa | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azja Wschodnia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Wschodnie stany USA 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Francja Środkowa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indie Środkowe | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japonia Wschodnia | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea Środkowa | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Północno-środkowe stany USA | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa Północna | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Północna Republika Południowej Afryki | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| South Central US | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnio-środkowe stany USA | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Głosy w wersji zapoznawczej](language-support.md#neural-voices-in-preview) są dostępne tylko w tych 3 regionach: Wschodnie stany USA, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="request-headers"></a>Nagłówki żądań

W tej tabeli wymieniono wymagane i opcjonalne nagłówki dla żądań tekstowych na mowę.

| Nagłówek | Opis | Wymagane/opcjonalne |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługi Mowa. | Ten nagłówek lub `Authorization` jest wymagany. |
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer` . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Ten nagłówek lub `Ocp-Apim-Subscription-Key` jest wymagany. |



### <a name="request-body"></a>Treść żądania

Treść nie jest wymagana dla `GET` żądań do tego punktu końcowego.

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie wymaga tylko nagłówka autoryzacji.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Przykładowa odpowiedź

Ta odpowiedź została obcięta, aby zilustrować strukturę odpowiedzi.

> [!NOTE]
> Dostępność głosu zależy od regionu/punktu końcowego.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwe przyczyny |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie powiodło się. |
| 400 | Nieprawidłowe żądanie | Brakuje wymaganego parametru, jest on pusty lub ma wartość null. Lub wartość przekazana do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Powszechnym problemem jest zbyt długi nagłówek. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowane. Upewnij się, że klucz subskrypcji lub token jest prawidłowy i znajduje się w poprawnym regionie. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub odsetek żądań dozwolonych dla Twojej subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub serwerem. Może również wskazywać nieprawidłowe nagłówki. |


## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

Punkt końcowy umożliwia konwertowanie tekstu na mowę `v1` przy użyciu języka speech synthesis [markup Language (SSML).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Te regiony są obsługiwane w przypadku używania funkcji tekstu na mowę przy użyciu interfejsu API REST. Upewnij się, że wybierasz punkt końcowy, który odpowiada Twojeowi regionowi subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Nagłówki żądań

W tej tabeli wymieniono wymagane i opcjonalne nagłówki dla żądań tekstowych na mowę.

| Nagłówek | Opis | Wymagane/Opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer` . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagane |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Zaakceptowana wartość: `application/ssml+xml` . | Wymagane |
| `X-Microsoft-OutputFormat` | Określa format danych wyjściowych audio. Aby uzyskać pełną listę akceptowanych wartości, zobacz [audio outputs (Dane wyjściowe audio).](#audio-outputs) | Wymagane |
| `User-Agent` | Nazwa aplikacji. Wartość musi być mniejsza niż 255 znaków. | Wymagane |

### <a name="audio-outputs"></a>Dane wyjściowe audio

Jest to lista obsługiwanych formatów audio, które są wysyłane w każdym żądaniu jako `X-Microsoft-OutputFormat` nagłówek. Każdy z nich zawiera typ kodowania i transmisji bitów. Usługa rozpoznawania mowy obsługuje wyjścia audio 24 kHz, 16 kHz i 8 kHz.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> Jeśli wybrany format głosu i danych wyjściowych ma różne stawki bitów, dźwięk jest ponownie próbkowany zgodnie z potrzebami.
> Ogg-24khz-16bit-mono-opus może być zdekodowany za pomocą [koder-dekodera opus](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Treść żądania

Treść każdego żądania `POST` jest wysyłana jako język [znaczników syntezy mowy (SSML).](speech-synthesis-markup.md) SSML umożliwia wybranie głosu i języka zsyntetyzowanych mowy zwracanych przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz [obsługa języków](language-support.md#text-to-speech).

> [!NOTE]
> W przypadku korzystania z głosu niestandardowego treść żądania może być wysyłana w postaci zwykłego tekstu (ASCII lub UTF-8).

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa protokołu SSML do określenia głosu i języka. Jeśli długość treści jest długa, a wynikowy dźwięk przekracza 10 minut — jest obcinany do 10 minut. Innymi słowy, długość dźwięku nie może przekraczać 10 minut.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwe przyczyny |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie powiodło się. treść odpowiedzi jest plikiem dźwiękowym. |
| 400 | Nieprawidłowe żądanie | Brakuje wymaganego parametru, jest on pusty lub ma wartość null. Lub wartość przekazana do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Powszechnym problemem jest zbyt długi nagłówek. |
| 401 | Brak autoryzacji | Żądanie nie jest autoryzowane. Upewnij się, że klucz subskrypcji lub token jest prawidłowy i znajduje się w poprawnym regionie. |
| 415 | Nieobsługiwany typ nośnika | Możliwe, że podano `Content-Type` błąd. `Content-Type` powinna być ustawiona na `application/ssml+xml` wartość . |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub odsetek żądań dozwolonych dla twojej subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub serwerem. Może również wskazywać nieprawidłowe nagłówki. |

Jeśli stan HTTP to , treść odpowiedzi `200 OK` zawiera plik dźwiękowy w żądanym formacie. Ten plik może być odtwarzany, ponieważ jest przesyłany, zapisywany w buforze lub zapisywany w pliku.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Synteza asynchroniczna dla dźwięku o długiej postaci](./long-audio-api.md)
- [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
