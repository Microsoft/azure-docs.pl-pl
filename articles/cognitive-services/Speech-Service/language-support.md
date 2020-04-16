---
title: Obsługa języków — usługa mowy
titleSuffix: Azure Cognitive Services
description: Usługa Mowa obsługuje wiele języków do konwersji zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany zamiany tekstu na mowę, a także do tłumaczenia mowy. Ten artykuł zawiera pełną listę obsługi języka według funkcji usługi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 2a876349af2bd53a46737dda82961afe442bae05
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401083"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcji usługi mowy. W poniższych tabelach podsumowano obsługę języka dla funkcji [Zamiana mowy na tekst,](#speech-to-text) [Zamiana tekstu na mowę](#text-to-speech)i Usługi [tłumaczenia mowy.](#speech-translation)

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno microsoft speech SDK i INTERFEJSU API REST obsługują następujące języki (ustawienia regionalne). Aby zwiększyć dokładność, dostosowywanie jest oferowane dla podzbioru języków poprzez przesyłanie transkrypcji audio + human-labeled lub tekst pokrewny: zdania. Dostosowanie wymowy jest obecnie dostępne `en-US` `de-DE`tylko dla i . Dowiedz się więcej o dostosowywaniu [tutaj](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Ustawienia regionalne  | Język                          | Obsługiwane | Dostosowania                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabski (ZEA)                      | Tak       | Nie                                                |
| `ar-BH` | Arabski (Bahrajn), nowoczesny standard | Tak       | Model językowy                                    |
| `ar-EG` | Arabski (Egipt)                    | Tak       | Model językowy                                    |
| `ar-KW` | Arabski (Kuwejt)                   | Tak       | Nie                                                |
| `ar-QA` | Arabski (Katar)                    | Tak       | Nie                                                |
| `ar-SA` | Arabski (Arabia Saudyjska)             | Tak       | Nie                                                |
| `ar-SY` | Arabski (Syria)                    | Tak       | Model językowy                                    |
| `ca-ES` | Kataloński                           | Tak       | Model językowy                                    |
| `da-DK` | Duński (Dania)                  | Tak       | Model językowy                                    |
| `de-DE` | Niemiecki (Niemcy)                  | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `en-AU` | Angielski (Australia)               | Tak       | Model akustyczny<br>Model językowy                  |
| `en-CA` | Angielski (Kanada)                  | Tak       | Model akustyczny<br>Model językowy                  |
| `en-GB` | Angielski (Zjednoczone Królestwo)          | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `en-IN` | Angielski (Indie)                   | Tak       | Model akustyczny<br>Model językowy                  |
| `en-NZ` | Angielski (Nowa Zelandia)             | Tak       | Model akustyczny<br>Model językowy                  |
| `en-US` | Angielski (Stany Zjednoczone)           | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `es-ES` | hiszpański (Hiszpania)                   | Tak       | Model akustyczny<br>Model językowy                  |
| `es-MX` | Hiszpański (Meksyk)                  | Tak       | Model akustyczny<br>Model językowy                  |
| `fi-FI` | Fiński (Finlandia)                 | Tak       | Model językowy                                    |
| `fr-CA` | francuski (Kanada)                   | Tak       | Model akustyczny<br>Model językowy                  |
| `fr-FR` | Francuski (Francja)                   | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `gu-IN` | Gudżarati (indyjski)                 | Tak       | Model językowy                                    |
| `hi-IN` | Hindi (Indie)                     | Tak       | Model akustyczny<br>Model językowy                  |
| `it-IT` | Włoski (Włochy)                   | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `ja-JP` | Japoński (Japonia)                  | Tak       | Model językowy                                    |
| `ko-KR` | Koreański (Korea)                    | Tak       | Model językowy                                    |
| `mr-IN` | Marathi (Indie)                   | Tak       | Model językowy                                    |
| `nb-NO` | Norweski (Bokmål) (Norwegia)       | Tak       | Model językowy                                    |
| `nl-NL` | Niderlandzki (Holandia)               | Tak       | Model językowy                                    |
| `pl-PL` | Polski (Polska)                   | Tak       | Model językowy                                    |
| `pt-BR` | portugalski (Brazylia)               | Tak       | Model akustyczny<br>Model językowy<br>Wymowa |
| `pt-PT` | Portugalski (Portugalia)             | Tak       | Model językowy                                    |
| `ru-RU` | Rosyjski (Rosja)                  | Tak       | Model akustyczny<br>Model językowy                  |
| `sv-SE` | Szwedzki (Szwecja)                  | Tak       | Model językowy                                    |
| `ta-IN` | Tamil (Indie)                     | Tak       | Model językowy                                    |
| `te-IN` | Telugu (Indie)                    | Tak       | Nie                                                |
| `th-TH` | Tajski (Tajlandia)                   | Tak       | Nie                                                |
| `tr-TR` | Turecki (Turcja)                  | Tak       | Nie                                                |
| `zh-CN` | Chiński (mandaryński, uproszczony)    | Tak       | Model akustyczny<br>Model językowy                  |
| `zh-HK` | Chiński (kantoński, tradycyjny)  | Tak       | Model językowy                                    |
| `zh-TW` | Chiński (tajwański mandaryński)      | Tak       | Model językowy                                    |

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zarówno zestaw Microsoft Speech SDK, jak i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowany przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w przypadku głosów standardowych, niestandardowych i neuronowych. Więcej informacji można znaleźć na stronie [Cennik.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

### <a name="neural-voices"></a>Głosy neuronowe

Neuronowy tekst na mowę to nowy rodzaj syntezy mowy zasilany przez głębokie sieci neuronowe. Podczas korzystania z głosu nerwowego, syntetyzowane mowy jest prawie nie do odróżnienia od ludzkich nagrań.

Głosy neuronowe mogą być używane do interakcji z chatbotami i asystentami głosowymi bardziej naturalnymi i wciągającymi, przekształcania tekstów cyfrowych, takich jak e-booki, w audiobooki i ulepszania systemów nawigacji samochodowej. Dzięki naturalnej prozodyi i wyraźnej artykulacji słów, głosy nerwowe znacznie zmniejszają zmęczenie słuchowe, gdy użytkownicy wchodzą w interakcje z systemami Sztucznej Inteligencji.

Aby uzyskać więcej informacji o dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Ustawienia regionalne  | Język            | Płeć | Mapowanie pełnej nazwy usługi                                               | Krótka nazwa głosu        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Niemiecki (Niemcy)    | Kobiety | "Microsoft Server Speech Speech to Speech Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Angielski (Stany Zjednoczone)        | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Angielski (Stany Zjednoczone)        | Mężczyźni   | "Microsoft Server Speech Speech to Speech Voice (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Włoski (Włochy)     | Kobiety | "Microsoft Server Speech Speech to Speech Voice (it-IT, ElsaNeural)"      | "it-IT-ElsaNeural"      |
| `pt-BR` | portugalski (Brazylia) | Kobiety | "Microsoft Server Speech Text to Speech Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chiński (mandaryński, uproszczony)  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> Głos `en-US-JessaNeural` został zmieniony `en-US-AriaNeural`na . Jeśli używałeś "Jessa" przed, przekonwertuj się na "Aria".

Aby dowiedzieć się, jak skonfigurować i dostosować głosy nerwowe, zobacz [Język znaczników syntezy mowy](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> W żądaniach syntezy mowy można użyć mapowania pełnej nazwy usługi lub krótkiej nazwy głosowej.

### <a name="standard-voices"></a>Standardowe głosy

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i lokalizacjach, które umożliwiają konwersję tekstu na mowę syntetyzowaną. Aby uzyskać więcej informacji o dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Ustawienia regionalne | Język | Płeć | Mapowanie pełnej nazwy usługi | Krótka nazwa |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arabski (Egipt) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arabski (Arabia Saudyjska) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bułgarski | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)" | "bg-BG-Ivan" |
| `ca-ES` | Kataloński | Kobiety | "Microsoft Server Speech Speech to Speech Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Czeski | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | duński | Kobiety | "Microsoft Server Speech Speech to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Niemiecki (Austria) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Niemiecki (Szwajcaria) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Niemiecki (Niemcy) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | grecki | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Angielski (Australia) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Angielski (Kanada) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Angielski (Wielka Brytania) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Angielski (Irlandia) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Angielski (Indie) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | hiszpański (Hiszpania) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Hiszpański (Meksyk) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | fiński | Kobiety | "Microsoft Server Speech Speech to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | francuski (Kanada) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francuski (Szwajcaria) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Francuski (Francja) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrajski (Izrael) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (he-IL, Asaf)" | "he-IL-Asaf" |
| `hi-IN` | Hindi (Indie) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (hi-IN, Kalpana, Apollo)" | "hi-IN-Kalpana-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (hi-IN, Kalpana)" | "hi-IN-Kalpana" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (hi-IN, Hemant)" | "hi-IN-Hemant" |
| `hr-HR` | Chorwacki | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | węgierski | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonezyjski | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (id-ID, Andika)" | "id-ID-Andika" |
| `it-IT` | Włoski | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (it-IT, LuciaRUS)" | "it-LuciaRUS" |
| `ja-JP` | Japoński | Kobiety | "Microsoft Server Speech Speech to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreański | Kobiety | "Microsoft Server Speech Speech to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajski | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)" | "ms-MY-Rizwan" |
| `nb-NO` | Norweski | Kobiety | "Microsoft Server Speech Speech to Speech Voice (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Niderlandzki | Kobiety | "Microsoft Server Speech Speech to Speech Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Polski | Kobiety | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | portugalski (Brazylia) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugalski (Portugalia) | Kobiety | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumuński | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (ro-RO, Andrei)" | "ro-RO-Andrei" |
| `ru-RU` | Rosyjski | Kobiety | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Słowacki | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Słoweński | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | szwedzki | Kobiety | "Microsoft Server Speech Speech to Speech Voice (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (Indie) | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (Indie) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Tajski | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Turecki (Turcja) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Wietnamski | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Chiński (mandaryński, uproszczony) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chiński (kantoński, tradycyjny) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Speech to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chiński (tajwański mandaryński) | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech Speech to Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG obsługuje modern standard arabski (MSA).*

> [!IMPORTANT]
> Głos `en-US-Jessa` został zmieniony `en-US-Aria`na . Jeśli używałeś "Jessa" przed, przekonwertuj się na "Aria".

> [!TIP]
> W żądaniach syntezy mowy można użyć mapowania pełnej nazwy usługi lub krótkiej nazwy głosowej.

### <a name="customization"></a>Dostosowywanie

Dostosowywanie głosu jest `de-DE` `en-GB`dostępne `en-IN` `en-US`dla `es-MX` `fr-FR`, `it-IT` `pt-BR`, `zh-CN`, , , , , i . Wybierz odpowiednie ustawienia regionalne, które są zgodne z danymi treningowymi, które trzeba trenować niestandardowy model głosu. Jeśli na przykład dane nagrywania są używane w języku `en-GB`angielskim z brytyjskim akcentem, wybierz opcję .

> [!NOTE]
> Nie obsługujemy dwedualnej szkolenia modelu w języku Niestandardowym, z wyjątkiem chińsko-angielskiego dwościplialnego. Wybierz "chińsko-angielski dwujęzyczny", jeśli chcesz trenować chiński głos, który może mówić po angielsku, jak również. Szkolenie głosowe we wszystkich lokalizacjach rozpoczyna się od zestawu danych 2000+ `en-US` `zh-CN` wypowiedzi, z wyjątkiem i gdzie można rozpocząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

Interfejs API **tłumaczenia mowy** obsługuje różne języki do tłumaczenia mowy na mowę i zamiany mowy na tekst. Język źródłowy musi zawsze pochodzić z tabeli języka zamiany mowy na tekst. Dostępne języki docelowe zależą od tego, czy celem tłumaczenia jest mowa czy tekst. Możesz przetłumaczyć przychodzące mowę na ponad [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór języków jest dostępny do [syntezy mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Języki tekstowe

| Język tekstowy           | Kod języka |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabski                  | `ar`          |
| Bangla                  | `bn`          |
| Bośniacki (łaciński)         | `bs`          |
| Bułgarski               | `bg`          |
| Kantoński (tradycyjny) | `yue`         |
| Kataloński                 | `ca`          |
| Chiński (uproszczony)      | `zh-Hans`     |
| Chiński (tradycyjny)     | `zh-Hant`     |
| Chorwacki                | `hr`          |
| Czeski                   | `cs`          |
| duński                  | `da`          |
| Niderlandzki                   | `nl`          |
| Polski                 | `en`          |
| Estoński                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| fiński                 | `fi`          |
| Francuski                  | `fr`          |
| Niemiecki                  | `de`          |
| grecki                   | `el`          |
| Haitański          | `ht`          |
| Hebrajski                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| węgierski               | `hu`          |
| Indonezyjski              | `id`          |
| Irlandzki                   | `ga`          |
| Włoski                 | `it`          |
| Japoński                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingoński                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| Koreański                  | `ko`          |
| Łotewski                 | `lv`          |
| Litewski              | `lt`          |
| Madagaskaru                | `mg`          |
| Malajski                   | `ms`          |
| Malayalam               | `ml`          |
| Maltański                 | `mt`          |
| Norweski               | `nb`          |
| Perski                 | `fa`          |
| Polski                  | `pl`          |
| portugalski (Brazylia)     | `pt-br`       |
| Portugalski (Portugalia)   | `pt-pt`       |
| Pendżabski                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumuński                | `ro`          |
| Rosyjski                 | `ru`          |
| Samoański                  | `sm`          |
| Serbski (cyrylica)      | `sr-Cyrl`     |
| Serbski (łaciński)         | `sr-Latn`     |
| Słowacki                  | `sk`          |
| Słoweński               | `sl`          |
| Hiszpański                 | `es`          |
| szwedzki                 | `sv`          |
| Tahitian                | `ty`          |
| Tamilski                   | `ta`          |
| Telugu                  | `te`          |
| Tajski                    | `th`          |
| Tonga                  | `to`          |
| Turecki                 | `tr`          |
| Ukraiński               | `uk`          |
| Urdu                    | `ur`          |
| Wietnamski              | `vi`          |
| Walijski                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję próbną usługi mowy](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
