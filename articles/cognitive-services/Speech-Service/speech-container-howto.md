---
title: Instalowanie i uruchamianie kontenerów platformy Docker dla interfejsów API usługi Mowa
titleSuffix: Azure Cognitive Services
description: Kontenery platformy Docker dla usługi mowa mogą wykonywać rozpoznawanie mowy, transkrypcję, generowanie i nie tylko w środowisku lokalnym.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: lokalnie, docker, kontener
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388095"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Instalowanie i uruchamianie kontenerów platformy Docker dla interfejsów API usługi Mowa 

Kontenery umożliwiają uruchamianie niektórych interfejsów API usługi Mowa we własnym środowisku. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener usługi Mowa.

Kontenery usługi Mowa umożliwiają klientom tworzenie architektury aplikacji mowy, która jest zoptymalizowana pod kątem zarówno niezawodnych funkcji chmury, jak i lokalnego charakteru urządzeń brzegowych. Dostępnych jest kilka kontenerów, które używają [tych](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) samych cen co usługi Azure Speech Services oparte na chmurze.


> [!IMPORTANT]
> Następujące kontenery mowy są teraz ogólnie dostępne:
> * Standardowa mowa na tekst
> * Niestandardowa mowa na tekst
> * Standardowy tekst na mowę
> * Neuronowy tekst na mowę
>
> Następujące kontenery mowy są w wersji zapoznawczej z bramą.
> * Niestandardowy tekst na mowę
> * Rozpoznawanie wykrywanie języka 
>
> Aby korzystać z kontenerów mowy, musisz przesłać żądanie online i zatwierdzić je. Zobacz **sekcję Żądanie zatwierdzenia do uruchomienia kontenera** poniżej, aby uzyskać więcej informacji.

| Kontener | Funkcje | Najnowsza |
|--|--|--|
| Zamiana mowy na tekst | Analizuje tonacji i transkrybuje ciągłe nagrania mowy w czasie rzeczywistym lub nagrania audio wsadowego z wynikami pośrednimi.  | 2.11.0 |
| Niestandardowa mowa na tekst | Przy użyciu modelu niestandardowego z portalu [usługi Custom Speech](https://speech.microsoft.com/customspeech)program transkrybuje ciągłe nagrania mowy lub dźwięku w czasie rzeczywistym na tekst z wynikami pośrednimi. | 2.11.0 |
| Zamiana tekstu na mowę | Konwertuje tekst na naturalnie brzmiącą mowę przy użyciu danych wejściowych w postaci zwykłego tekstu lub języka SSML (Speech Synthesis Markup Language). | 1.13.0 |
| Niestandardowa wiadomość tekstowa na mowę | Przy użyciu modelu niestandardowego z portalu [głos niestandardowy konwertuje](https://aka.ms/custom-voice-portal)tekst na naturalnie brzmiącą mowę przy użyciu danych wejściowych w postaci zwykłego tekstu lub języka SSML (Speech Synthesis Markup Language). | 1.13.0 |
| Rozpoznawanie wykrywanie języka | Wykrywanie języka mówionego w plikach dźwiękowych. | 1.0 |
| Neuronowy tekst na mowę | Konwertuje tekst na naturalnie brzmiącą mowę przy użyciu głębokiej technologii sieci neuronowej, co pozwala na bardziej naturalne syntetyzowanie mowy. | 1.5.0 |

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów usługi Mowa należy spełnić następujące wymagania wstępne:

| Wymagane | Przeznaczenie |
|--|--|
| Aparat platformy Docker | Aparat platformy Docker musi być zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platforma Docker musi być skonfigurowana tak, aby umożliwić kontenerom łączenie się z platformą Azure i wysyłanie danych dotyczących rozliczeń na platformę Azure. <br><br> **W systemie Windows** należy również skonfigurować usługę Docker do obsługi kontenerów systemu Linux.<br><br> |
| Znajomość platformy Docker | Musisz mieć podstawową wiedzę na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także wiedzę na temat podstawowych `docker` poleceń. |
| Zasób rozpoznawania mowy | Aby można było używać tych kontenerów, musisz mieć:<br><br>Zasób usługi Azure _Speech_ do uzyskania skojarzonego klucza interfejsu API i URI punktu końcowego. Obie wartości są dostępne na stronach Azure Portal **Mowy** i Klucze. Oba te kontenery są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}:** jeden z dwóch dostępnych kluczy zasobów na **stronie Klucze**<br><br>**{ENDPOINT_URI}:** punkt końcowy podany na **stronie** Przegląd |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer hosta

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa rozszerzenia Advanced Vector Extension

Host **to** komputer z uruchomionym kontenerem platformy Docker. Host musi *obsługiwać rozszerzenia* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Aby sprawdzić obsługę AVX2 na hostach z systemem Linux, możesz użyć następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Komputer hosta jest wymagany *do obsługi* avx2. Kontener nie *będzie działać poprawnie* bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

W poniższej tabeli opisano minimalną i zalecaną alokację zasobów dla każdego kontenera mowy.

| Kontener | Minimum | Zalecane |
|-----------|---------|-------------|
| Zamiana mowy na tekst | 2 rdzenie, 2 GB pamięci | 4-rdzeniowe, 4 GB pamięci |
| Niestandardowa mowa na tekst | 2 rdzenie, 2 GB pamięci | 4-rdzeniowe, 4 GB pamięci |
| Zamiana tekstu na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzenie, 3 GB pamięci |
| Niestandardowy tekst na mowę | 1 rdzeń, 2 GB pamięci | 2 rdzenie, 3 GB pamięci |
| Rozpoznawanie wykrywanie języka | 1 rdzeń, 1 GB pamięci | 1 rdzeń, 1 GB pamięci |
| Neuronowe mowę tekstu na mowę | 6-rdzeniowa pamięć 12 GB | 8-rdzeniowa pamięć 16 GB |

* Każdy rdzeń musi mieć co najmniej 2,6 GHz lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` ustawieniam i `--memory` , które są używane jako część `docker run` polecenia.

> [!NOTE]
> Minimalne i zalecane są oparte na limitach platformy Docker, a *nie* na zasobach maszyny hosta. Na przykład kontenery funkcji "speech-to-text" są częścią mapy  pamięci dużego modelu językowego i zaleca się, aby cały plik mieścił się w pamięci, co stanowi dodatkowe 4–6 GB pamięci. Ponadto pierwsze uruchomienie jednego z kontenerów może trwać dłużej, ponieważ modele są stronicowane w pamięci.

## <a name="request-approval-to-the-run-the-container"></a>Żądanie zatwierdzenia uruchomienia kontenera

Wypełnij i prześlij formularz [żądania, aby](https://aka.ms/csgate) zażądać dostępu do kontenera. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Obrazy kontenerów dla mowy są dostępne w następujących Container Registry.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana mowy na tekst | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Niestandardowa mowa na tekst](#tab/cstt)

| Kontener | Repozytorium |
|-----------|------------|
| Niestandardowa mowa na tekst | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

| Kontener | Repozytorium |
|-----------|------------|
| Zamiana tekstu na mowę | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neuronowe mowę tekstu na mowę](#tab/ntts)

| Kontener | Repozytorium |
|-----------|------------|
| Neuronowe mowę tekstu na mowę | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Niestandardowa wiadomość tekstowa na mowę](#tab/ctts)

| Kontener | Repozytorium |
|-----------|------------|
| Niestandardowa wiadomość tekstowa na mowę | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Rozpoznawanie wykrywanie języka](#tab/lid)

> [!TIP]
> Aby uzyskać najbardziej przydatne wyniki, zalecamy użycie kontenera wykrywania języka mowy z kontenerami mowy na tekst lub niestandardowymi kontenerami mowy na tekst. 

| Kontener | Repozytorium |
|-----------|------------|
| Rozpoznawanie wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Ściąganie platformy Docker dla kontenerów mowy

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Ściąganie platformy Docker dla kontenera funkcji mowy na tekst

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> Tag `latest` ściąga `en-US` lokalne. Aby uzyskać dodatkowe informacje o lokalizacjach [regionalnych, zobacz Speech-to-text locales (Funkcje mowy na tekst).](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Speech-to-text locales (Przemówienia mowy na tekst — lokalne)

Wszystkie tagi, z wyjątkiem , `latest` są w następującym formacie i zróżnicują wielkość liter:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
2.6.0-amd64-en-us
```

Aby uzyskać informacje o wszystkich obsługiwanych lokalizacjach regionalnych **kontenera** funkcji mowy na [tekst,](../containers/container-image-tags.md#speech-to-text)zobacz Tagi obrazów funkcji mowa na tekst .

# <a name="custom-speech-to-text"></a>[Niestandardowa mowa na tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Ściąganie platformy Docker dla niestandardowego kontenera funkcji mowy na tekst

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> Właściwości `locale` i dla niestandardowych kontenerów mowy są określane przez model niestandardowy pozyskany `voice` przez kontener.

# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Ściąganie na platformie Docker dla kontenera funkcji text-to-speech

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z rejestru kontenerów firmy Microsoft.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> Tag `latest` ściąga lokalne i `en-US` `ariarus` głosowe. Aby uzyskać dodatkowe informacje o lokalizacjach regionalnych, zobacz [Text-to-speech locales (Przekieruj tekst na mowę).](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Przekierowywuj tekst na mowę — lokalne

Wszystkie tagi, z wyjątkiem , mają następujący format i `latest` są zróżnicowe:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Następujący tag jest przykładem formatu:

```
1.8.0-amd64-en-us-ariarus
```

Aby uzyskać informacje o wszystkich obsługiwanych  lokalizacjach regionalnych i odpowiednich głosach kontenera z tekstem na mowę, zobacz Tagi obrazów funkcji Tekst [na mowę.](../containers/container-image-tags.md#text-to-speech)

> [!IMPORTANT]
> Podczas konstruowania żądania HTTP POST funkcji *text-to-speech* komunikat języka [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) wymaga `voice` elementu z `name` atrybutem . Wartość to odpowiednie wartości regionalnych i głosowych kontenera, znane również jako ["krótka nazwa".](language-support.md#standard-voices) Na przykład `latest` tag będzie miał nazwę głosu `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neuronowe mowę tekstu na mowę](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Ściąganie platformy Docker dla neuronowego kontenera funkcji tekstu na mowę

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z usługi Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> Tag `latest` ściąga lokalne i `en-US` `arianeural` głosowe. Aby uzyskać dodatkowe informacje o lokalizacjach [regionalnych, zobacz Neural Text-to-speech locales](#neural-text-to-speech-locales)(Neuronowe funkcje tekstowe na mowę).

#### <a name="neural-text-to-speech-locales"></a>Neuronowe lokalne funkcji tekstu na mowę

Wszystkie tagi, z wyjątkiem , mają następujący format i `latest` są zróżnicowe:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Następujący tag jest przykładem formatu:

```
1.3.0-amd64-en-us-arianeural
```

Aby uzyskać informacje na temat wszystkich obsługiwanych lokalizacji regionalnych i odpowiadających im głosów w kontenerze neuronowego rozpoznawania tekstu na mowę, zobacz Neural [Text-to-speech image tags](../containers/container-image-tags.md#neural-text-to-speech)(Neuronowe tagi obrazów typu tekst na mowę). 

> [!IMPORTANT]
> Podczas konstruowania *neuronowego żądania* HTTP POST funkcji tekstu na mowę komunikat języka [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md) `voice` wymaga elementu z `name` atrybutem . Wartość to odpowiednie wartości regionalnych i głosowych kontenera, znane również jako ["krótka nazwa".](language-support.md#neural-voices) Na przykład `latest` tag będzie miał nazwę głosu `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Niestandardowa wiadomość tekstowa na mowę](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Ściąganie platformy Docker dla niestandardowego kontenera funkcji tekstu na mowę

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z usługi Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> Właściwości `locale` i dla niestandardowych kontenerów mowy są określane przez model niestandardowy pozyskany `voice` przez kontener.

# <a name="speech-language-detection"></a>[Rozpoznawanie wykrywanie języka](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Ściąganie platformy Docker dla kontenera wykrywanie języka Speech

Użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera z usługi Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta,](#the-host-computer)użyj następującego procesu, aby pracować z kontenerem.

1. [Uruchom kontener z](#run-the-container-with-docker-run)wymaganymi ustawieniami rozliczeń. [Dostępnych](speech-container-configuration.md#example-docker-run-commands) jest więcej `docker run` przykładów polecenia.
1. [Odpytuje punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [docker run,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Aby uzyskać [szczegółowe informacje na](#gathering-required-parameters) temat sposobu uzyskania wartości i , zapoznaj się ze zbieraniem `{Endpoint_URI}` wymaganych `{API_Key}` parametrów. Dostępne [są](speech-container-configuration.md#example-docker-run-commands) również `docker run` dodatkowe przykłady polecenia.

# <a name="speech-to-text"></a>[Zamiana mowy na tekst](#tab/stt)

Aby uruchomić standardowy kontener *funkcji mowy na tekst,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener *funkcji mowy na tekst* z obrazu kontenera.
* Przydziela 4 rdzenie procesora CPU i 4 gigabajty (GB) pamięci.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

> [!NOTE]
> Kontenery obsługują skompresowane dane wejściowe audio do zestawu SPEECH SDK przy użyciu usługi GStreamer.
> Aby zainstalować platformę GStreamer w kontenerze, postępuj zgodnie z instrukcjami dla platformy GStreamer dla systemu Linux w tece [Use codec compressed audio input with the Speech SDK](how-to-use-codec-compressed-audio-input-streams.md)(Korzystanie ze skompresowanych danych wejściowych audio kodera-kodera za pomocą zestawu Speech SDK).

#### <a name="diarization-on-the-speech-to-text-output"></a>Diaryzacja danych wyjściowych funkcji mowy na tekst
Diaryzacja jest domyślnie włączona. Aby uzyskać diarację w odpowiedzi, `diarize_speech_config.set_service_property` użyj .

1. Ustaw format danych wyjściowych frazy na `Detailed` .
2. Ustaw tryb diaryzacji. Obsługiwane tryby to `Identity` i `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> Tryb "Tożsamość" zwraca wartość `"SpeakerId": "Customer"` lub `"SpeakerId": "Agent"` .
> Tryb "Anonimowy" zwraca wartość `"SpeakerId": "Speaker 1"` lub `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Analizowanie tonacji na danych wyjściowych funkcji mowy na tekst 
Począwszy od wersji 2.6.0 kontenera zamiany mowy na tekst, należy używać punktu końcowego interfejsu API TextAnalytics 3.0 zamiast punktu końcowego w wersji zapoznawczej. Na przykład
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Interfejs `v3.0` ANALIZA TEKSTU API nie jest zgodny z poprzednimi wersjami analiza tekstu `v3.0-preview.1` . Aby uzyskać najnowszą obsługę funkcji tonacji, użyj obrazu kontenera mowy na tekst `v2.6.0` i analiza tekstu `v3.0` .

Począwszy od wersji 2.2.0 kontenera mowy na tekst, możesz wywołać interfejs API analizy tonacji w wersji [3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) dla danych wyjściowych. Aby wywołać analizę tonacji, musisz mieć punkt końcowy zasobu analiza tekstu API. Na przykład: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Jeśli uzyskujesz dostęp do punktu końcowego analizy tekstu w chmurze, będziesz potrzebować klucza. Jeśli używasz aplikacji analiza tekstu lokalnym, może nie być konieczne podanie tej informacji.

Klucz i punkt końcowy są przekazywane do kontenera usługi Mowa jako argumenty, jak w poniższym przykładzie.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

To polecenie:

* Wykonuje te same kroki co powyższe polecenie.
* Przechowuje klucz i punkt końcowy analiza tekstu API do wysyłania żądań analizy tonacji. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Lista fraz w wersji 2 w danych wyjściowych funkcji mowy na tekst 

Począwszy od wersji 2.6.0 kontenera funkcji mowy na tekst, możesz uzyskać dane wyjściowe z własnymi frazami — całym zdaniem lub frazami w środku. Na przykład *wysoki człowiek w* następującym zdaniu:

* "To jest zdanie **wysokiego mężczyzny** to kolejne zdanie".

Aby skonfigurować listę fraz, musisz dodać własne frazy podczas wywołania. Na przykład:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Jeśli masz wiele fraz do dodania, wywołaj dla `.addPhrase()` każdej frazy, aby dodać ją do listy fraz. 


# <a name="custom-speech-to-text"></a>[Niestandardowa mowa na tekst](#tab/cstt)

Niestandardowy *kontener funkcji mowy na tekst* opiera się na niestandardowym modelu mowy. Model niestandardowy musi być wytrenowany [przy](how-to-custom-speech-train-model.md) użyciu portalu [usługi Custom Speech.](https://speech.microsoft.com/customspeech)

Identyfikator niestandardowego **modelu mowy jest** wymagany do uruchomienia kontenera. Można go znaleźć na **stronie Szkolenie** w portalu custom speech. W portalu mowy niestandardowej przejdź do strony **Trenowanie** i wybierz model.
<br>

![Strona trenowania mowy niestandardowej](media/custom-speech/custom-speech-model-training.png)

Uzyskaj **identyfikator modelu,** który ma być argumentem `ModelId` parametru `docker run` polecenia .
<br>

![Szczegóły niestandardowego modelu mowy](media/custom-speech/custom-speech-model-details.png)

W poniższej tabeli przedstawiono różne `docker run` parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | Zainstaluj wolumin komputera [hosta](https://docs.docker.com/storage/volumes/), którego do utrwalania modelu niestandardowego używa docker. Na przykład *C:\CustomSpeech,* gdzie *dysk C* znajduje się na maszynie hosta. |
| `{MODEL_ID}` | Identyfikator niestandardowego **modelu mowy** ze **strony Trenowanie** portalu custom speech. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiarów i rozliczeń. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Klucz interfejsu API jest wymagany. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić *kontener custom speech-to-text,* wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia niestandardowy *kontener mowy na tekst* z obrazu kontenera.
* Przydziela 4 rdzenie procesora CPU i 4 gigabajty (GB) pamięci.
* Ładuje model *custom speech-to-text* z instalacji wprowadzania woluminu, na przykład *C:\CustomSpeech*.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model na podstawie `ModelId` (jeśli nie zostanie znaleziony na instalacji woluminu).
* Jeśli model niestandardowy został wcześniej pobrany, zostanie `ModelId` on zignorowany.
* Automatycznie usuwa kontener po zakończeniu pracy. Obraz kontenera jest nadal dostępny na komputerze-hoście.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Pobieranie modelu podstawowego w niestandardowym kontenerze funkcji mowy na tekst  
Począwszy od wersji 2.6.0 kontenera custom-speech-to-text, możesz uzyskać dostępne informacje o modelu bazowym przy użyciu opcji `BaseModelLocale=<locale>` . Ta opcja daje listę dostępnych modeli podstawowych dla tych opcji regionalnych w ramach konta rozliczeniowego. Na przykład:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia niestandardowy *kontener mowy na tekst* z obrazu kontenera.
* Sprawdź i zwróć dostępne modele podstawowe docelowych wartości regionalnych.

Dane wyjściowe zawierają listę modeli podstawowych z informacjami o wartościach regionalnych, identyfikatorze modelu i dacie utworzenia. Możesz użyć identyfikatora modelu, aby pobrać i użyć określonego preferowanego modelu podstawowego. Na przykład:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Niestandardowa wymowa w niestandardowym kontenerze funkcji mowy na tekst 
Począwszy od wersji 2.5.0 kontenera custom-speech-to-text, możesz uzyskać niestandardowy wynik wymowy w danych wyjściowych. Wystarczy skonfigurować własne niestandardowe reguły wymowy w modelu niestandardowym i zainstalować model w niestandardowym kontenerze mowy na tekst.


# <a name="text-to-speech"></a>[Zamiana tekstu na mowę](#tab/tts)

Aby uruchomić standardowy kontener *funkcji tekstu na mowę,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia standardowy *kontener z tekstem na mowę* z obrazu kontenera.
* Przydziela 1 rdzeń procesora CPU i 2 gigabajty (GB) pamięci.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="neural-text-to-speech"></a>[Neuronowe mowę tekstu na mowę](#tab/ntts)

Aby uruchomić kontener *neuronowego rozpoznawania tekstu na mowę,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia *neuronowy kontener funkcji* tekstu na mowę z obrazu kontenera.
* Przydziela 6 rdzeni procesora CPU i 12 gigabajtów (GB) pamięci.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="custom-text-to-speech"></a>[Niestandardowa wiadomość tekstowa na mowę](#tab/ctts)

Niestandardowy *kontener funkcji tekstu na mowę* opiera się na niestandardowym modelu głosu. Model niestandardowy musi być wytrenowany [przy](how-to-custom-voice-create-voice.md) użyciu [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Do uruchomienia **kontenera** jest wymagany niestandardowy identyfikator modelu głosu. Można go znaleźć na stronie **Szkolenie** w niestandardowym portalu głosowym. W portalu głosu niestandardowego przejdź do strony **Trenowanie** i wybierz model.
<br>

![Niestandardowa strona trenowania głosu](media/custom-voice/custom-voice-model-training.png)

Uzyskaj identyfikator **modelu,** który ma być argumentem `ModelId` parametru polecenia docker run.
<br>

![Szczegóły niestandardowego modelu głosowego](media/custom-voice/custom-voice-model-details.png)

W poniższej tabeli przedstawiono różne `docker run` parametry i odpowiadające im opisy:

| Parametr | Opis |
|---------|---------|
| `{VOLUME_MOUNT}` | Zainstaluj wolumin komputera [hosta](https://docs.docker.com/storage/volumes/), którego do utrwalania modelu niestandardowego używa docker. Na przykład *C:\CustomSpeech,* gdzie *dysk C* znajduje się na maszynie hosta. |
| `{MODEL_ID}` | Identyfikator niestandardowego **modelu mowy** na **stronie Trenowanie** w portalu głosu niestandardowego. |
| `{ENDPOINT_URI}` | Punkt końcowy jest wymagany do pomiarów i rozliczeń. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |
| `{API_KEY}` | Klucz interfejsu API jest wymagany. Aby uzyskać więcej informacji, zobacz [zbieranie wymaganych parametrów](#gathering-required-parameters). |

Aby uruchomić *niestandardowy kontener funkcji tekstu na mowę,* wykonaj następujące `docker run` polecenie:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia niestandardowy *kontener funkcji tekstu na mowę* z obrazu kontenera.
* Przydziela 1 rdzeń procesora CPU i 2 gigabajty (GB) pamięci.
* Ładuje niestandardowy *model funkcji tekstu na* mowę z instalacji wprowadzania woluminu, na przykład *C:\CustomVoice*.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Pobiera model podany `ModelId` (jeśli nie zostanie znaleziony na instalacji woluminu).
* Jeśli model niestandardowy został wcześniej pobrany, zostanie `ModelId` on zignorowany.
* Automatycznie usuwa kontener po zakończeniu pracy. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="speech-language-detection"></a>[Rozpoznawanie wykrywanie języka](#tab/lid)

Aby uruchomić kontener *speech wykrywanie języka,* wykonaj następujące `docker run` polecenie.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie: 

* Uruchamia kontener wykrywania języka mowy z obrazu kontenera. Obecnie za uruchomienie tego obrazu nie są naliczane opłaty.
* Przydziela 1 rdzeń procesora CPU i 1 gigabajt (GB) pamięci.
* Uwidacznia port TCP 5003 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu pracy. Obraz kontenera jest nadal dostępny na komputerze-hoście.

Jeśli wysyłasz tylko żądania wykrywanie języka mowy, musisz ustawić wartość klienta usługi Mowa `phraseDetection` na `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Jeśli chcesz uruchomić ten kontener z kontenerem mowy na tekst, możesz użyć tego obrazu [platformy Docker.](https://hub.docker.com/r/antsu/on-prem-client) Po uruchomieniu obu kontenerów użyj tego polecenia Docker Run, aby wykonać polecenie `speech-to-text-with-languagedetection-client` .

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Zwiększenie liczby współbieżnych wywołań może mieć wpływ na niezawodność i opóźnienia. W przypadku wykrywania języka zalecamy maksymalnie 4 równoczesne wywołania przy użyciu 1 procesora CPU i 1 GB pamięci. W przypadku hostów z 2 procesorami CPU i 2 GB pamięci zalecamy maksymalnie 6 współbieżnych wywołań.

***

> [!IMPORTANT]
> Aby uruchomić kontener, należy określić opcje , i . W przeciwnym razie `Eula` `Billing` kontener nie zostanie `ApiKey` uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

> [!NOTE]
> Użyj unikatowego numeru portu, jeśli korzystasz z wielu kontenerów.

| Kontenery | Adres URL hosta zestawu SDK | Protokół |
|--|--|--|
| Standardowa mowa na tekst i niestandardowa mowa na tekst | `ws://localhost:5000` | WS |
| Rozpoznawanie tekstu na mowę (w tym standardowe, niestandardowe i neuronowe), wykrywanie języka mowy | `http://localhost:5000` | HTTP |

Aby uzyskać więcej informacji na temat korzystania z protokołów WSS i HTTPS, zobacz [Zabezpieczenia kontenerów](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

### <a name="speech-to-text-standard-and-custom"></a>Mowa na tekst (standardowa i niestandardowa)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Analiza tonacji

Jeśli podano poświadczenia interfejsu API analiza tekstu kontenerowi [,](#analyze-sentiment-on-the-speech-to-text-output)możesz użyć zestawu SDK usługi Mowa do wysyłania żądań rozpoznawania mowy z analizą tonacji. Odpowiedzi interfejsu API można skonfigurować do użycia *prostego lub* *szczegółowego* formatu.
> [!NOTE]
> Wersja 1.13 zestawu SDK języka Python usługi Mowa ma zidentyfikowany problem z analizą tonacji. Użyj wersji 1.12.x lub starszej, jeśli korzystasz z analizy tonacji w zestawie SDK języka Python usługi Mowa.

# <a name="simple-format"></a>[Prosty format](#tab/simple-format)

Aby skonfigurować klienta usługi Mowa do używania prostego formatu, dodaj `"Sentiment"` jako wartość dla `Simple.Extensions` . Jeśli chcesz wybrać określoną wersję analiza tekstu, zastąp `'latest'` wartość w konfiguracji `speechcontext-phraseDetection.sentimentAnalysis.modelversion` właściwości.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` Zwróci wynik tonacji w warstwie głównej odpowiedzi.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Szczegółowy format](#tab/detailed-format)

Aby skonfigurować klienta usługi Mowa do używania szczegółowego formatu, dodaj jako `"Sentiment"` wartość dla , lub obu tych `Detailed.Extensions` `Detailed.Options` formatów. Jeśli chcesz wybrać określoną wersję analiza tekstu, zastąp wartość `'latest'` w konfiguracji `speechcontext-phraseDetection.sentimentAnalysis.modelversion` właściwości.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` dostarcza wynik tonacji w warstwie głównej odpowiedzi. `Detailed.Options` Dostarcza wynik w `NBest` warstwie odpowiedzi. Mogą być używane oddzielnie lub razem.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Jeśli chcesz całkowicie wyłączyć analizę tonacji, dodaj `false` wartość do . `sentimentanalysis.enabled`

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Tekst na mowę (standardowy, neuronowy i niestandardowy)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z ujawnionych portów, upewnij się, że każdy kontener ma inny ujawniony port. Na przykład uruchom pierwszy kontener na porcie 5000, a drugi na porcie 5001.

Ten kontener i inny kontener mogą być Azure Cognitive Services uruchomione razem na hoście. Możesz również mieć wiele kontenerów tego samego kontenera, Cognitive Services uruchomiony.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania lub uruchamiania kontenera mogą wystąpić problemy. Użyj instalacji [wyjściowej i](speech-container-configuration.md#mount-settings) włącz rejestrowanie. Pozwoli to kontenerowi na generowanie plików dziennika, które są przydatne podczas rozwiązywania problemów.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery usługi Mowa wysyłają informacje rozliczeniowe na platformę Azure przy użyciu *zasobu usługi Speech* na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](speech-container-configuration.md).

## <a name="summary"></a>Podsumowanie

W tym artykule poznaliśmy pojęcia i przepływ pracy dotyczące pobierania, instalowania i uruchamiania kontenerów usługi Mowa. Podsumowanie:

* Mowa udostępnia cztery kontenery systemu Linux dla platformy Docker, hermetyzując różne możliwości:
  * *Zamiana mowy na tekst*
  * *Niestandardowa mowa na tekst*
  * *Zamiana tekstu na mowę*
  * *Niestandardowy tekst na mowę*
  * *Neuronowy tekst na mowę*
  * *Rozpoznawanie wykrywanie języka*
* Obrazy kontenerów są pobierane z rejestru kontenerów na platformie Azure.
* Obrazy kontenerów są uruchamiane na platformy Docker.
* Niezależnie od tego, czy używasz interfejsu API REST (tylko do przetwarzania tekstu na mowę), czy zestawu SDK (mowa na tekst lub tekst na mowę), określasz adres URI hosta kontenera. 
* Musisz podać informacje rozliczeniowe podczas wystąpienia kontenera.

> [!IMPORTANT]
>  Cognitive Services nie mają licencji na uruchamianie bez połączenia z platformą Azure w celu zmierzania. Klienci muszą włączyć kontenery, aby przez cały czas przekazywać informacje rozliczeniowe z usługą pomiarów. Cognitive Services kontenery nie wysyłają danych klienta (np. obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przeglądanie [konfigurowania kontenerów](speech-container-configuration.md) dla ustawień konfiguracji
* Dowiedz się, jak [używać kontenerów usługi Mowa z usługami Kubernetes i Helm](speech-container-howto-on-premises.md)
* Używanie większej [Cognitive Services kontenerów](../cognitive-services-container-support.md)
