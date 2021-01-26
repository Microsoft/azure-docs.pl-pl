---
title: Dokumentacja interfejsu API zamiany tekstu na mowę (REST) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST zamiany tekstu na mowę. Ten artykuł zawiera informacje o opcjach autoryzacji, opcjach zapytań, sposobach tworzenia struktury żądania i otrzymywaniu odpowiedzi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 53be2f6795593bf44cafde149242b13ed4fdfe50
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790481"
---
# <a name="text-to-speech-rest-api"></a>Interfejs API REST zamiany tekstu na mowę

Usługa mowy umożliwia [Konwertowanie tekstu na mowę](#convert-text-to-speech) i [Pobieranie listy obsługiwanych głosów](#get-a-list-of-voices) dla regionu przy użyciu zestawu interfejsów API REST. Każdy dostępny punkt końcowy jest skojarzony z regionem. Wymagany jest klucz subskrypcji dla punktu końcowego/regionu, który ma być używany.

Interfejs API REST zamiany tekstu na mowę obsługuje neuronowych i standardowe głosy zamiany tekstu na mowę, z których każdy obsługuje określony język i dialekt identyfikowany przez ustawienia regionalne.

* Aby uzyskać pełną listę głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).
* Aby uzyskać informacje o dostępności regionalnej, zobacz [regiony](regions.md#text-to-speech).

> [!IMPORTANT]
> Koszty różnią się w zależności od standardowych, niestandardowych i neuronowych głosów. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Przed użyciem tego interfejsu API należy zrozumieć następujące informacje:

* Interfejs API REST zamiany tekstu na mowę wymaga nagłówka autoryzacji. Oznacza to, że należy zakończyć wymianę tokenów, aby uzyskać dostęp do usługi. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie).

> [!TIP]
> Zobacz [ten artykuł](sovereign-clouds.md) , aby uzyskać Azure Government i punkty końcowe platformy Azure w Chinach.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Pobieranie listy głosów

`voices/list`Punkt końcowy umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego.

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
| South Central US | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Południowe Zjednoczone Królestwo | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zachodnie stany USA 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Głosy w wersji zapoznawczej](language-support.md#neural-voices-in-preview) są dostępne tylko w tych 3 regionach: Wschodnie stany USA, Europa Zachodnia i Azja Południowo-Wschodnia.

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane/opcjonalne |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usługi rozpoznawania mowy. | Ten nagłówek lub `Authorization` jest wymagany. |
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer` . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Ten nagłówek lub `Ocp-Apim-Subscription-Key` jest wymagany. |



### <a name="request-body"></a>Treść żądania

Treść `GET` żądania do tego punktu końcowego nie jest wymagana.

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie wymaga tylko nagłówka autoryzacji.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Przykładowa odpowiedź

Ta odpowiedź została obcięta w celu zilustrowania struktury odpowiedzi.

> [!NOTE]
> Dostępność głosu jest różna w zależności od regionu/punktu końcowego.

```json
[
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
]
```

### <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi oznacza powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwe przyczyny |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie zakończyło się pomyślnie. |
| 400 | Nieprawidłowe żądanie | Brak wymaganego parametru, jest on pusty lub ma wartość null. Lub wartość przeniesiona do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowy problem to nagłówek, który jest zbyt długi. |
| 401 | Brak autoryzacji | Żądanie nie ma autoryzacji. Upewnij się, że klucz subskrypcji lub token jest prawidłowy i znajduje się w prawidłowym regionie. |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub częstotliwość żądań dozwolonych dla Twojej subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub po stronie serwera. Może także wskazywać nieprawidłowe nagłówki. |


## <a name="convert-text-to-speech"></a>Zamiana tekstu na mowę

`v1`Punkt końcowy umożliwia konwertowanie tekstu na mowę przy użyciu [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Te regiony są obsługiwane dla zamiany tekstu na mowę przy użyciu interfejsu API REST. Upewnij się, że wybrano punkt końcowy zgodny z Twoim regionem subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera listę wymaganych i opcjonalnych nagłówków dla żądań zamiany tekstu na mowę.

| Nagłówek | Opis | Wymagane/opcjonalne |
|--------|-------------|---------------------|
| `Authorization` | Token autoryzacji poprzedzony słowem `Bearer` . Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Wymagane |
| `Content-Type` | Określa typ zawartości dla podanego tekstu. Zaakceptowana wartość: `application/ssml+xml` . | Wymagane |
| `X-Microsoft-OutputFormat` | Określa format danych wyjściowych audio. Aby uzyskać pełną listę zaakceptowanych wartości, zobacz [wyjście audio](#audio-outputs). | Wymagane |
| `User-Agent` | Nazwa aplikacji. Podana wartość musi być krótsza niż 255 znaków. | Wymagane |

### <a name="audio-outputs"></a>Wyjście audio

Jest to lista obsługiwanych formatów audio, które są wysyłane w każdym żądaniu jako `X-Microsoft-OutputFormat` nagłówek. Każda z nich obejmuje szybkość transmisji bitów i typ kodowania. Usługa mowy obsługuje 24 kHz, 16 kHz i 8 kHz danych wyjściowych audio.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Jeśli wybrany format dźwięku i danych wyjściowych ma różne szybkości transmisji bitów, w razie potrzeby dźwięk zostanie ponownie próbkowany. OGG-24khz-16bit-mono-Opus można zdekodować przy użyciu [kodera-dekoder Opus](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Treść żądania

Treść każdego `POST` żądania jest wysyłana jako [Język oznaczeń syntezy mowy (SSML)](speech-synthesis-markup.md). SSML umożliwia wybranie głosu i języka przez funkcję syntezy mowy zwracaną przez usługę zamiany tekstu na mowę. Aby uzyskać pełną listę obsługiwanych głosów, zobacz temat [Obsługa języków](language-support.md#text-to-speech).

> [!NOTE]
> W przypadku użycia niestandardowego głosu treść żądania może być wysyłana jako zwykły tekst (ASCII lub UTF-8).

### <a name="sample-request"></a>Przykładowe żądanie

To żądanie HTTP używa SSML do określenia głosu i języka. Jeśli długość treści jest długa, a wyjściowy dźwięk przekroczy 10 minut — zostanie obcięty do 10 minut. Innymi słowy, Długość dźwięku nie może przekroczyć 10 minut.

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

Kod stanu HTTP dla każdej odpowiedzi oznacza powodzenie lub typowe błędy.

| Kod stanu HTTP | Opis | Możliwe przyczyny |
|------------------|-------------|-----------------|
| 200 | OK | Żądanie zakończyło się pomyślnie. treść odpowiedzi to plik audio. |
| 400 | Nieprawidłowe żądanie | Brak wymaganego parametru, jest on pusty lub ma wartość null. Lub wartość przeniesiona do wymaganego lub opcjonalnego parametru jest nieprawidłowa. Typowy problem to nagłówek, który jest zbyt długi. |
| 401 | Brak autoryzacji | Żądanie nie ma autoryzacji. Upewnij się, że klucz subskrypcji lub token jest prawidłowy i znajduje się w prawidłowym regionie. |
| 415 | Nieobsługiwany typ nośnika | Możliwe, że `Content-Type` podano nieprawidłowy. `Content-Type` powinien być ustawiony na `application/ssml+xml` . |
| 429 | Zbyt wiele żądań | Przekroczono limit przydziału lub częstotliwość żądań dozwolonych dla Twojej subskrypcji. |
| 502 | Zła brama    | Problem z siecią lub po stronie serwera. Może także wskazywać nieprawidłowe nagłówki. |

Jeśli stan HTTP to `200 OK` , treść odpowiedzi zawiera plik audio w żądanym formacie. Ten plik może być odtwarzany w trakcie jego przesyłania, zapisywania w buforze lub zapisywania w pliku.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Synteza asynchroniczna dla długich postaci audio](./long-audio-api.md)
- [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)