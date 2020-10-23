---
title: Obsługa języka — usługa mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy obsługuje wiele języków w przypadku konwersji mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy. Ten artykuł zawiera kompleksową listę obsługi języków według funkcji usługi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 6f484277ee940c63888fff068739598dd553b515
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440897"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). 

W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania **zapisu audio + z etykietami ludzkimi** lub **powiązanego tekstu: zdania**. Aby dowiedzieć się więcej o dostosowywaniu, zobacz Wprowadzenie [do Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Regionalne  | Język                          | Dostosowania                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabski (Zjednoczone Emiraty Arabskie)     | Model językowy                                    |
| `ar-BH` | Arabski (Bahrajn), nowoczesny Standard | Model językowy                                    |
| `ar-EG` | Arabski (Egipt)                    | Model językowy                                    |
| `ar-IQ` | Arabski (Irak)                     | Model językowy                                    |
| `ar-JO` | Arabski (Jordania)                   | Model językowy                                    |
| `ar-KW` | Arabski (Kuwejt)                   | Model językowy                                    |
| `ar-LB` | Arabski (Liban)                  | Model językowy                                    |
| `ar-OM` | Arabski (Oman)                     | Model językowy                                    |
| `ar-QA` | Arabski (katar)                    | Model językowy                                    |
| `ar-SA` | Arabski (Arabia Saudyjska)             | Model językowy                                    |
| `ar-SY` | Arabski (Syria)                    | Model językowy                                    |
| `bg-BG` | Bułgarski (Bułgaria)              | Model językowy                                    |
| `ca-ES` | Kataloński                   | Model językowy                                    |
| `cs-CZ` | Czeski (Czechy)            | Model języka                                    | 
| `da-DK` | Duński (Dania)                  | Model językowy                                    |
| `de-DE` | Niemiecki (Niemcy)                  | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `el-GR` | Grecki (Grecja)                    | Model językowy                                    |
| `en-AU` | Angielski (Australia)               | Model akustyczny<br>Model językowy                  |
| `en-CA` | Angielski (Kanada)                  | Model akustyczny<br>Model językowy                  |
| `en-GB` | Angielski (Zjednoczone Królestwo)          | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `en-HK` | Angielski (Hongkong SAR)               | Model języka                                    | 
| `en-IE` | Angielski (Irlandia)                 | Model języka                                    | 
| `en-IN` | Angielski (Indie)                   | Model akustyczny<br>Model językowy                  |
| `en-NZ` | Angielski (Nowa Zelandia)             | Model akustyczny<br>Model językowy                  |
| `en-PH` | Angielski (Filipiny)             | Model języka                                    | 
| `en-SG` | Angielski (Singapur)               | Model języka                                    | 
| `en-US` | Angielski (Stany Zjednoczone)           | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `en-ZA` | Angielski (Republika Południowej Afryki)            | Model języka                                    | 
| `es-AR` | Hiszpański (Argentyna)               | Model języka                                    | 
| `es-BO` | Hiszpański (Boliwia)                 | Model języka                                    | 
| `es-CL` | Hiszpański (Chile)                   | Model języka                                    | 
| `es-CO` | Hiszpański (Kolumbia)                | Model języka                                    | 
| `es-CR` | Hiszpański (Kostaryka)              | Model języka                                    | 
| `es-CU` | Hiszpański (Kuba)                    | Model języka                                    | 
| `es-DO` | Hiszpański (Dominikana)      | Model języka                                    | 
| `es-EC` | Hiszpański (Ekwador)                 | Model języka                                    | 
| `es-ES` | hiszpański (Hiszpania)                   | Model akustyczny<br>Model językowy                  |
| `es-GT` | Hiszpański (Gwatemala)               | Model języka                                    | 
| `es-HN` | Hiszpański (Honduras)                | Model języka                                    | 
| `es-MX` | Hiszpański (Meksyk)                  | Model akustyczny<br>Model językowy                  |
| `es-NI` | Hiszpański (Nikaragua)               | Model języka                                    | 
| `es-PA` | Hiszpański (Panama)                  | Model języka                                    | 
| `es-PE` | Hiszpański (Peru)                    | Model języka                                    | 
| `es-PR` | Hiszpański (Portoryko)             | Model języka                                    | 
| `es-PY` | Hiszpański (Paragwaj)                | Model języka                                    | 
| `es-SV` | Hiszpański (Salwador)             | Model języka                                    | 
| `es-US` | Hiszpański (USA)                     | Model języka                                    | 
| `es-UY` | Hiszpański (Urugwaj)                 | Model języka                                    | 
| `es-VE` | Hiszpański (Wenezuela)               | Model języka                                    |
| `et-EE` | Estoński (Estonia)                 | Model języka                                    | 
| `fi-FI` | Fiński (Finlandia)                 | Model językowy                                    |
| `fr-CA` | francuski (Kanada)                   | Model akustyczny<br>Model językowy                  |
| `fr-FR` | Francuski (Francja)                   | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `ga-IE` | Irlandzki (Irlandia)                    | Model językowy                                    |
| `gu-IN` | Gudżarati (Indyjski)                 | Model językowy                                    |
| `hi-IN` | Hindi (Indie)                     | Model akustyczny<br>Model językowy                  |
| `hr-HR` | Chorwacki (Chorwacja)                | Model językowy                                    |
| `hu-HU` | Węgierski (Węgry)               | Model języka                                    | 
| `it-IT` | Włoski (Włochy)                   | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `ja-JP` | Japoński (Japonia)                  | Model językowy                                    |
| `ko-KR` | Koreański (Korea)                    | Model językowy                                    |
| `lt-LT` | Litewski (Litwa)            | Model językowy                                    |
| `lv-LV` | Łotewski (Łotwa)                  | Model językowy                                    |
| `mr-IN` | Marathi (Indie)                   | Model językowy                                    |
| `mt-MT` | Maltański (Malta)                    | Model językowy                                    |
| `nb-NO` | Norweski (bokmål) (Norwegia)       | Model językowy                                    |
| `nl-NL` | Niderlandzki (Holandia)               | Model językowy                                    |
| `pl-PL` | Polski (Polska)                   | Model językowy                                    |
| `pt-BR` | Portugalski (Brazylia)               | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `pt-PT` | Portugalski (Portugalia)             | Model językowy                                    |
| `ro-RO` | Rumuński (Rumunia)                | Model językowy                                    |
| `ru-RU` | Rosyjski (Rosja)                  | Model akustyczny<br>Model językowy                  |
| `sk-SK` | Słowacki (Słowacja)                 | Model językowy                                    |
| `sl-SI` | Słoweński (Słowenia)              | Model językowy                                    |
| `sv-SE` | Szwedzki (Szwecja)                  | Model językowy                                    |
| `ta-IN` | Tamilski (Indie)                     | Model językowy                                    |
| `te-IN` | Telugu (Indie)                    | Model językowy                                    |
| `th-TH` | Tajski (Tajlandia)                   | Model językowy                                    |
| `tr-TR` | Turecki (Turcja)                  | Model językowy                                    |
| `zh-CN` | Chiński (mandarynki, uproszczony)    | Model akustyczny<br>Model językowy                  |
| `zh-HK` | Chiński (kantoński, tradycyjny)  | Model językowy                                    |
| `zh-TW` | Chiński (tajwański mandarynki)      | Model językowy                                    |

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestawy Microsoft Speech SDK i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne. Możesz również uzyskać pełną listę języków i głosów obsługiwanych dla każdego regionu/punktu końcowego za pomocą [interfejsu API głosy/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

|Regionalne  | Język            | Płeć | Nazwa głosu | Obsługa stylu |
|--|--|--|--|--|
| `ar-EG` | Arabski (Egipt) | Kobiety | `ar-EG-SalmaNeural` | Ogólne |
| `ar-SA` | Arabski (Arabia Saudyjska) | Kobiety | `ar-SA-ZariyahNeural` | Ogólne |
| `bg-BG` <sup>Nowe</sup> | Bułgarski (Bulgary) | Kobiety | `bg-BG-KalinaNeural` | Ogólne |
| `ca-ES` | Kataloński | Kobiety | `ca-ES-AlbaNeural` | Ogólne |
| `cs-CZ` <sup>Nowe</sup> | Czeski (czeski) | Kobiety | `cs-CZ-VlastaNeural` | Ogólne |
| `da-DK` | Duński (Dania) | Kobiety | `da-DK-ChristelNeural` | Ogólne |
| `de-AT` <sup>Nowe</sup> | Niemiecki (Austria) | Kobiety | `de-AT-IngridNeural` | Ogólne |
| `de-CH` <sup>Nowe</sup> | Niemiecki (Szwajcaria) | Kobiety | `de-CH-LeniNeural` | Ogólne |
| `de-DE` | Niemiecki (Niemcy) | Kobiety | `de-DE-KatjaNeural` | Ogólne |
| `de-DE` <sup>Nowe</sup> | Niemiecki (Niemcy) | Mężczyźni | `de-DE-ConradNeural` | Ogólne |
| `el-GR` <sup>Nowe</sup> | Grecki (Grecja) | Kobiety | `el-GR-AthinaNeural` | Ogólne |
| `en-AU` | Angielski (Australia) | Kobiety | `en-AU-NatashaNeural` | Ogólne |
| `en-AU` <sup>Nowe</sup> | Angielski (Australia) | Mężczyźni | `en-AU-WilliamNeural` | Ogólne |
| `en-CA` | Angielski (Kanada) | Kobiety | `en-CA-ClaraNeural` | Ogólne |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety | `en-GB-LibbyNeural` | Ogólne |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety | `en-GB-MiaNeural` | Ogólne |
| `en-GB` <sup>Nowe</sup> | Angielski (Zjednoczone Królestwo) | Mężczyźni | `en-GB-RyanNeural` | Ogólne |
| `en-IE` <sup>Nowe</sup> | Angielski (Irlandia) | Kobiety | `en-IE-EmilyNeural` | Ogólne |
| `en-IN` | Angielski (Indie) | Kobiety | `en-IN-NeerjaNeural` | Ogólne |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety | `en-US-AriaNeural` | Ogólne, dostępne są wiele stylów głosowych |
| `en-US` | Angielski (Stany Zjednoczone) | Mężczyźni | `en-US-GuyNeural` | Ogólne |
| `en-US` <sup>Nowe</sup> | Angielski (Stany Zjednoczone) | Kobiety | `en-US-JennyNeural` | Ogólne, dostępne są wiele stylów głosowych |
| `es-ES` | hiszpański (Hiszpania) | Kobiety | `es-ES-ElviraNeural` | Ogólne |
| `es-ES` <sup>Nowe</sup> | hiszpański (Hiszpania) | Mężczyźni | `es-ES-AlvaroNeural` | Ogólne |
| `es-MX` | Hiszpański (Meksyk) | Kobiety | `es-MX-DaliaNeural` | Ogólne |
| `es-MX` <sup>Nowe</sup> | Hiszpański (Meksyk) | Mężczyźni | `es-MX-JorgeNeural` | Ogólne |
| `fi-FI` | Fiński (Finlandia) | Kobiety | `fi-FI-NooraNeural` | Ogólne |
| `fr-CA` | francuski (Kanada) | Kobiety | `fr-CA-SylvieNeural` | Ogólne |
| `fr-CA` <sup>Nowe</sup> | francuski (Kanada) | Mężczyźni | `fr-CA-JeanNeural` | Ogólne |
| `fr-CH` <sup>Nowe</sup> | Francuski (Szwajcaria) | Kobiety | `fr-CH-ArianeNeural` | Ogólne |
| `fr-FR` | Francuski (Francja) | Kobiety | `fr-FR-DeniseNeural` | Ogólne |
| `fr-FR` <sup>Nowe</sup> | Francuski (Francja) | Mężczyźni | `fr-FR-HenriNeural` | Ogólne |
| `he-IL` <sup>Nowe</sup> | Hebrajski (Izrael) | Kobiety | `he-IL-HilaNeural` | Ogólne |
| `hi-IN` | Hindi (Indie) | Kobiety | `hi-IN-SwaraNeural` | Ogólne |
| `hr-HR` <sup>Nowe</sup> | Chorwacki (Chorwacja) | Kobiety | `hr-HR-GabrijelaNeural` | Ogólne |
| `hu-HU` <sup>Nowe</sup> | Węgierski (Węgry) | Kobiety | `hu-HU-NoemiNeural` | Ogólne |
| `id-ID` <sup>Nowe</sup> | Indonezyjski (Indonezja) | Mężczyźni | `id-ID-ArdiNeural` | Ogólne |
| `it-IT` | Włoski (Włochy) | Kobiety | `it-IT-ElsaNeural` | Ogólne |
| `it-IT` <sup>Nowe</sup> | Włoski (Włochy) | Kobiety | `it-IT-IsabellaNeural` | Ogólne |
| `it-IT` <sup>Nowe</sup> | Włoski (Włochy) | Mężczyźni | `it-IT-DiegoNeural` | Ogólne |
| `ja-JP` | Japoński (Japonia) | Kobiety | `ja-JP-NanamiNeural` | Ogólne |
| `ja-JP` <sup>Nowe</sup> | Japoński (Japonia) | Mężczyźni | `ja-JP-KeitaNeural` | Ogólne |
| `ko-KR` | Koreański (Korea) | Kobiety | `ko-KR-SunHiNeural` | Ogólne |
| `ko-KR` <sup>Nowe</sup> | Koreański (Korea) | Mężczyźni | `ko-KR-InJoonNeural` | Ogólne |
| `ms-MY` <sup>Nowe</sup> | Malajski (Malezja) | Kobiety | `ms-MY-YasminNeural` | Ogólne |
| `nb-NO` | Norweski, Bokmål (Norwegia) | Kobiety | `nb-NO-IselinNeural` | Ogólne |
| `nl-NL` | Niderlandzki (Holandia) | Kobiety | `nl-NL-ColetteNeural` | Ogólne |
| `pl-PL` | Polski (Polska) | Kobiety | `pl-PL-ZofiaNeural` | Ogólne |
| `pt-BR` | Portugalski (Brazylia) | Kobiety | `pt-BR-FranciscaNeural` | Ogólne, dostępne są wiele stylów głosowych |
| `pt-BR` <sup>Nowe</sup> | Portugalski (Brazylia) | Mężczyźni | `pt-BR-AntonioNeural` | Ogólne |
| `pt-PT` | Portugalski (Portugalia) | Kobiety | `pt-PT-FernandaNeural` | Ogólne |
| `ro-RO` <sup>Nowe</sup> | Rumuński (Rumunia) | Kobiety | `ro-RO-AlinaNeural` | Ogólne |
| `ru-RU` | Rosyjski (Rosja) | Kobiety | `ru-RU-DariyaNeural` | Ogólne |
| `sk-SK` <sup>Nowe</sup> | Słowacki (Słowacja) | Kobiety | `sk-SK-ViktoriaNeural` | Ogólne |
| `sl-SI` <sup>Nowe</sup> | Słoweński (Słowenia) | Kobiety | `sl-SI-PetraNeural` | Ogólne |
| `sv-SE` | Szwedzki (Szwecja) | Kobiety | `sv-SE-HilleviNeural` | Ogólne |
| `ta-IN` <sup>Nowe</sup> | Tamilski (Indie) | Kobiety | `ta-IN-PallaviNeural` | Ogólne |
| `te-IN` <sup>Nowe</sup> | Telugu (Indie) | Kobiety | `te-IN-ShrutiNeural` | Ogólne |
| `th-TH` | Tajski (Tajlandia) | Kobiety | `th-TH-AcharaNeural` | Ogólne |
| `th-TH` <sup>Nowe</sup> | Tajski (Tajlandia) | Kobiety | `th-TH-PremwadeeNeural` | Ogólne |
| `tr-TR` | Turecki (Turcja) | Kobiety | `tr-TR-EmelNeural` | Ogólne |
| `vi-VN` <sup>Nowe</sup> | Wietnamski (Wietnam) | Kobiety | `vi-VN-HoaiMyNeural` | Ogólne |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Kobiety | `zh-CN-XiaoxiaoNeural` | Ogólne, dostępne są wiele stylów głosowych |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Kobiety | `zh-CN-XiaoyouNeural` | Głos dla dzieci, zoptymalizowany pod kątem narracji w wątku |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Mężczyźni | `zh-CN-YunyangNeural` | Zoptymalizowane pod kątem czytania wiadomości, dostępne są wiele stylów głosowych |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Mężczyźni | `zh-CN-YunyeNeural` | Optymalizacja pod kątem narracji w wątku |
| `zh-HK` | Kantoński (chiński tradycyjny, Hongkong SAR) | Kobiety | `zh-HK-HiuGaaiNeural` | Ogólne |
| `zh-TW` | Mandarynki (chiński tradycyjny, Tajwan) | Kobiety | `zh-TW-HsiaoYuNeural` | Ogólne |

> [!IMPORTANT]
> `en-US-JessaNeural`Głos został zmieniony na `en-US-AriaNeural` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaNeural)" w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Regionalne | Język | Płeć | Nazwa głosu |
|--|--|--|--|
| `ar-EG` | Arabski (arabski) | Kobiety | `ar-EG-Hoda` |
| `ar-SA` | Arabski (Arabia Saudyjska) | Mężczyźni | `ar-SA-Naayf` |
| `bg-BG` | Bułgarski (Bułgaria) | Mężczyźni | `bg-BG-Ivan` |
| `ca-ES` | Kataloński | Kobiety | `ca-ES-HerenaRUS` |
| `cs-CZ` | Czeski (Czechy) | Mężczyźni | `cs-CZ-Jakub` |
| `da-DK` | Duński (Dania) | Kobiety | `da-DK-HelleRUS` |
| `de-AT` | Niemiecki (Austria) | Mężczyźni | `de-AT-Michael` |
| `de-CH` | Niemiecki (Szwajcaria) | Mężczyźni | `de-CH-Karsten` |
| `de-DE` | Niemiecki (Niemcy) | Kobiety | `de-DE-HeddaRUS` |
| `de-DE` | Niemiecki (Niemcy) | Mężczyźni | `de-DE-Stefan` |
| `el-GR` | Grecki (Grecja) | Mężczyźni | `el-GR-Stefanos` |
| `en-AU` | Angielski (Australia) | Kobiety | `en-AU-Catherine` |
| `en-AU` | Angielski (Australia) | Kobiety | `en-AU-HayleyRUS` |
| `en-CA` | Angielski (Kanada) | Kobiety | `en-CA-HeatherRUS` |
| `en-CA` | Angielski (Kanada) | Kobiety | `en-CA-Linda` |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Mężczyźni | `en-GB-George` |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety | `en-GB-HazelRUS` |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety | `en-GB-Susan` |
| `en-IE` | Angielski (Irlandia) | Mężczyźni | `en-IE-Sean` |
| `en-IN` | Angielski (Indie) | Kobiety | `en-IN-Heera` |
| `en-IN` | Angielski (Indie) | Kobiety | `en-IN-PriyaRUS` |
| `en-IN` | Angielski (Indie) | Mężczyźni | `en-IN-Ravi` |
| `en-US` | Angielski (Stany Zjednoczone) | Mężczyźni | `en-US-BenjaminRUS` |
| `en-US` | Angielski (Stany Zjednoczone) | Mężczyźni | `en-US-GuyRUS` |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety | `en-US-JessaRUS` |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety | `en-US-ZiraRUS` |
| `es-ES` | hiszpański (Hiszpania) | Kobiety | `es-ES-HelenaRUS` |
| `es-ES` | hiszpański (Hiszpania) | Kobiety | `es-ES-Laura` |
| `es-ES` | hiszpański (Hiszpania) | Mężczyźni | `es-ES-Pablo` |
| `es-MX` | Hiszpański (Meksyk) | Kobiety | `es-MX-HildaRUS` |
| `es-MX` | Hiszpański (Meksyk) | Mężczyźni | `es-MX-Raul` |
| `fi-FI` | Fiński (Finlandia) | Kobiety | `fi-FI-HeidiRUS` |
| `fr-CA` | francuski (Kanada) | Kobiety | `fr-CA-Caroline` |
| `fr-CA` | francuski (Kanada) | Kobiety | `fr-CA-HarmonieRUS` |
| `fr-CH` | Francuski (Szwajcaria) | Mężczyźni | `fr-CH-Guillaume` |
| `fr-FR` | Francuski (Francja) | Kobiety | `fr-FR-HortenseRUS` |
| `fr-FR` | Francuski (Francja) | Kobiety | `fr-FR-Julie` |
| `fr-FR` | Francuski (Francja) | Mężczyźni | `fr-FR-Paul` |
| `he-IL` | Hebrajski (Izrael) | Mężczyźni | `he-IL-Asaf` |
| `hi-IN` | Hindi (Indie) | Mężczyźni | `hi-IN-Hemant` |
| `hi-IN` | Hindi (Indie) | Kobiety | `hi-IN-Kalpana` |
| `hr-HR` | Chorwacki (Chorwacja) | Mężczyźni | `hr-HR-Matej` |
| `hu-HU` | Węgierski (Węgry) | Mężczyźni | `hu-HU-Szabolcs` |
| `id-ID` | Indonezyjski (Indonezja) | Mężczyźni | `id-ID-Andika` |
| `it-IT` | Włoski (Włochy) | Mężczyźni | `it-IT-Cosimo` |
| `it-IT` | Włoski (Włochy) | Kobiety | `it-IT-LuciaRUS` |
| `ja-JP` | Japoński (Japonia) | Kobiety | `ja-JP-Ayumi` |
| `ja-JP` | Japoński (Japonia) | Kobiety | `ja-JP-HarukaRUS` |
| `ja-JP` | Japoński (Japonia) | Mężczyźni | `ja-JP-Ichiro` |
| `ko-KR` | Koreański (Korea) | Kobiety | `ko-KR-HeamiRUS` |
| `ms-MY` | Malajski (Malezja) | Mężczyźni | `ms-MY-Rizwan` |
| `nb-NO` | Norweski, Bokmål (Norwegia) | Kobiety | `nb-NO-HuldaRUS` |
| `nl-NL` | Niderlandzki (Holandia) | Kobiety | `nl-NL-HannaRUS` |
| `pl-PL` | Polski (Polska) | Kobiety | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugalski (Brazylia) | Mężczyźni | `pt-BR-Daniel` |
| `pt-BR` | Portugalski (Brazylia) | Kobiety | `pt-BR-HeloisaRUS` |
| `pt-PT` | Portugalski (Portugalia) | Kobiety | `pt-PT-HeliaRUS` |
| `ro-RO` | Rumuński (Rumunia) | Mężczyźni | `ro-RO-Andrei` |
| `ru-RU` | Rosyjski (Rosja) | Kobiety | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Rosyjski (Rosja) | Kobiety | `ru-RU-Irina` |
| `ru-RU` | Rosyjski (Rosja) | Mężczyźni | `ru-RU-Pavel` |
| `sk-SK` | Słowacki (Słowacja) | Mężczyźni | `sk-SK-Filip` |
| `sl-SI` | Słoweński (Słowenia) | Mężczyźni | `sl-SI-Lado` |
| `sv-SE` | Szwedzki (Szwecja) | Kobiety | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamilski (Indie) | Mężczyźni | `ta-IN-Valluvar` |
| `te-IN` | Telugu (Indie) | Kobiety | `te-IN-Chitra` |
| `th-TH` | Tajski (Tajlandia) | Mężczyźni | `th-TH-Pattara` |
| `tr-TR` | Turecki (Turcja) | Kobiety | `tr-TR-SedaRUS` |
| `vi-VN` | Wietnamski (Wietnam) | Mężczyźni | `vi-VN-An` |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Kobiety | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Mężczyźni | `zh-CN-Kangkang` |
| `zh-CN` | Mandarynki (chiński uproszczony, Chiny) | Kobiety | `zh-CN-Yaoyao` |
| `zh-HK` | Kantoński (chiński tradycyjny, Hongkong SAR) | Mężczyźni | `zh-HK-Danny` |
| `zh-HK` | Kantoński (chiński tradycyjny, Hongkong SAR) | Kobiety | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarynki (chiński tradycyjny, Tajwan) | Kobiety | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarynki (chiński tradycyjny, Tajwan) | Kobiety | `zh-TW-Yating` |
| `zh-TW` | Mandarynki (chiński tradycyjny, Tajwan) | Mężczyźni | `zh-TW-Zhiwei` |

> [!IMPORTANT]
> `en-US-Jessa`Głos został zmieniony na `en-US-Aria` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaRUS)" w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla,,,,,,,, `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` i `zh-CN` . Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz opcję `en-GB` .

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem Chinese-English dwujęzykowego. Wybierz pozycję "dwujęzyczne w języku chińskim English", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich ustawieniach regionalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem tego, `en-US` `zh-CN` gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

## <a name="speech-translation"></a>Tłumaczenie mowy

Interfejs API **tłumaczenia mowy** obsługuje różne języki dla tłumaczenia mowy na mowę i zamiany mowy na tekst. Język źródłowy musi być zawsze z tabeli języka zamiany mowy na tekst. Dostępne języki docelowe zależą od tego, czy celem tłumaczenia jest rozpoznawanie mowy czy tekst. Możesz przetłumaczyć przychodzącą mowę na więcej niż [60 języków](https://www.microsoft.com/translator/business/languages/). Podzbiór języków jest dostępny dla [syntezy mowy](language-support.md#text-languages).

### <a name="text-languages"></a>Języki tekstu

| Język tekstu           | Kod języka |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabski                  | `ar`          |
| Języku                  | `bn`          |
| Bośniacki (łaciński)         | `bs`          |
| Bułgarski               | `bg`          |
| Kantoński (tradycyjny) | `yue`         |
| Kataloński                 | `ca`          |
| Chiński (uproszczony)      | `zh-Hans`     |
| Chiński (tradycyjny)     | `zh-Hant`     |
| Chorwacki                | `hr`          |
| Czeski                   | `cs`          |
| Duński                  | `da`          |
| Niderlandzki                   | `nl`          |
| Angielski                 | `en`          |
| Estoński                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| Fiński                 | `fi`          |
| Francuski                  | `fr`          |
| Niemiecki                  | `de`          |
| Grecki                   | `el`          |
| Gudżarati                | `gu`          |
| Haitański          | `ht`          |
| Hebrajski                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Kowalski               | `mww`         |
| Węgierski               | `hu`          |
| Indonezyjski              | `id`          |
| Irlandzki                   | `ga`          |
| Włoski                 | `it`          |
| japoński                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Koreański                  | `ko`          |
| Łotewski                 | `lv`          |
| Litewski              | `lt`          |
| Madagaskaru                | `mg`          |
| Malajski                   | `ms`          |
| Malayalam               | `ml`          |
| Maltański                 | `mt`          |
| Maoryjski                   | `mi`          |
| Marathi                 | `mr`          |
| Norweski               | `nb`          |
| Perski                 | `fa`          |
| polski                  | `pl`          |
| portugalski (Brazylia)     | `pt-br`       |
| Portugalski (Portugalia)   | `pt-pt`       |
| Pendżabski                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Rumuński                | `ro`          |
| Rosyjski                 | `ru`          |
| Samoan                  | `sm`          |
| Serbski (cyrylica)      | `sr-Cyrl`     |
| Serbski (łaciński)         | `sr-Latn`     |
| Słowacki                  | `sk`          |
| Słoweński               | `sl`          |
| Hiszpański                 | `es`          |
| Szwedzki                 | `sv`          |
| Tahitian                | `ty`          |
| Tamilski                   | `ta`          |
| Telugu                  | `te`          |
| Tajlandzki                    | `th`          |
| Tonga                  | `to`          |
| Turecki                 | `tr`          |
| Ukraiński               | `uk`          |
| Urdu                    | `ur`          |
| Wietnamski              | `vi`          |
| Walijski                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Rozpoznawanie osoby mówiącej

Zapoznaj się z poniższą tabelą dla obsługiwanych języków dla różnych rozpoznawanie osoby mówiącej interfejsów API. Więcej informacji na temat rozpoznawanie osoby mówiącej można znaleźć w temacie [Omówienie](speaker-recognition-overview.md) .

| Regionalne | Język | Weryfikacja zależna od tekstu | Weryfikacja niezależna od tekstu | Identyfikacja niezależna od tekstu |
|----|----|----|----|----|
| en-US | Angielski (Stany Zjednoczone) | tak | tak | tak |
|zh-CN    |Chiński (mandarynki, uproszczony)|    nie dotyczy|    tak|    tak|
|de-DE    |Niemiecki (Niemcy)    |nie dotyczy    |tak    |tak|
|en-GB    |Angielski (Zjednoczone Królestwo)    |nie dotyczy    |tak    |tak|
|fr-FR    |Francuski (Francja)    |nie dotyczy    |tak    |tak|
|en-AU    |Angielski (Australia)    |nie dotyczy    |tak    |tak|
|EN-CA    |Angielski (Kanada)    |nie dotyczy|    tak|    tak|
|fr — CA    |francuski (Kanada)    |nie dotyczy    |tak|    tak|
|it-IT    |Włoski|    nie dotyczy    |tak|    tak|
|es-ES|    hiszpański (Hiszpania)    |nie dotyczy    |tak|    tak|
|es — MX    |Hiszpański (Meksyk)    |nie dotyczy|    tak|    tak|
|ja-JP|    japoński    |nie dotyczy    |tak    |tak|
|pt-BR|    Portugalski (Brazylia)|    nie dotyczy|    tak|    tak|

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
