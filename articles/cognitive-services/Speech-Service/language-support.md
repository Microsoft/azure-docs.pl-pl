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
ms.openlocfilehash: a864717304dacaf6cf4c77c92050827af619ed39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736683"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). 

W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania **zapisu audio + z etykietami ludzkimi** lub **powiązanego tekstu: zdania** . Aby dowiedzieć się więcej o dostosowywaniu, zobacz Wprowadzenie [do Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Język                          | Regionalne | Dostosowania                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Arabski (Bahrajn), nowoczesny Standard  |`ar-BH` | Model językowy                                   |
|Arabski (Egipt)                     |`ar-EG` | Model językowy                                   |
|Arabski (Irak)                      |`ar-IQ` | Model językowy                                   |
|Arabski (Jordania)                    |`ar-JO` | Model językowy                                   |
|Arabski (Kuwejt)                    |`ar-KW` | Model językowy                                   |
|Arabski (Liban)                   |`ar-LB` | Model językowy                                   |
|Arabski (Oman)                      |`ar-OM` | Model językowy                                   |
|Arabski (katar)                     |`ar-QA` | Model językowy                                   |
|Arabski (Arabia Saudyjska)              |`ar-SA` | Model językowy                                   |
|Arabski (Syria)                     |`ar-SY` | Model językowy                                   |
|Arabski (Zjednoczone Emiraty Arabskie)      |`ar-AE` | Model językowy                                   |
|Bułgarski (Bułgaria)               |`bg-BG` | Model językowy                                   |
|Kataloński                    |`ca-ES` | Model językowy                                   |
|Chiński (kantoński, tradycyjny)   |`zh-HK` | Model językowy                                   |
|Chiński (mandarynki, uproszczony)     |`zh-CN` | Model akustyczny<br>Model językowy                 |
|Chiński (tajwański mandarynki)       |`zh-TW` | Model językowy                                   |
|Chorwacki (Chorwacja)                 |`hr-HR` | Model językowy                                   |
|Czeski (Czechy)             |`cs-CZ` | Model języka                                   |
|Duński (Dania)                   |`da-DK` | Model językowy                                   |
|Niderlandzki (Holandia)                |`nl-NL` | Model językowy                                   |
|Angielski (Australia)                |`en-AU` | Model akustyczny<br>Model językowy                 |
|Angielski (Kanada)                   |`en-CA` | Model akustyczny<br>Model językowy                 |
|Angielski (Hongkong SAR)                |`en-HK` | Model języka                                   |
|Angielski (Indie)                    |`en-IN` | Model akustyczny<br>Model językowy                 |
|Angielski (Irlandia)                  |`en-IE` | Model języka                                   |
|Angielski (Nowa Zelandia)              |`en-NZ` | Model akustyczny<br>Model językowy                 |
|Angielski (Filipiny)              |`en-PH` | Model języka                                   |
|Angielski (Singapur)                |`en-SG` | Model języka                                   |
|Angielski (Republika Południowej Afryki)             |`en-ZA` | Model języka                                   |
|Angielski (Zjednoczone Królestwo)           |`en-GB` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Angielski (Stany Zjednoczone)            |`en-US` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Estoński (Estonia)                  |`et-EE` | Model języka                                   |
|Fiński (Finlandia)                  |`fi-FI` | Model językowy                                   |
|francuski (Kanada)                    |`fr-CA` | Model akustyczny<br>Model językowy                 |
|Francuski (Francja)                    |`fr-FR` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Niemiecki (Niemcy)                   |`de-DE` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Grecki (Grecja)                     |`el-GR` | Model językowy                                   |
|Gudżarati (Indyjski)                  |`gu-IN` | Model językowy                                   |
|Hindi (Indie)                      |`hi-IN` | Model akustyczny<br>Model językowy                 |
|Węgierski (Węgry)                |`hu-HU` | Model języka                                   |
|Irlandzki (Irlandia)                     |`ga-IE` | Model językowy                                   |
|Włoski (Włochy)                    |`it-IT` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Japoński (Japonia)                   |`ja-JP` | Model językowy                                   |
|Koreański (Korea)                     |`ko-KR` | Model językowy                                   |
|Łotewski (Łotwa)                   |`lv-LV` | Model językowy                                   |
|Litewski (Litwa)             |`lt-LT` | Model językowy                                   |
|Maltański (Malta)                     |`mt-MT` | Model językowy                                   |
|Marathi (Indie)                    |`mr-IN` | Model językowy                                   |
|Norweski (BokmÃ ¥ l) (Norwegia)       |`nb-NO` | Model językowy                                   |
|Polski (Polska)                    |`pl-PL` | Model językowy                                   |
|Portugalski (Brazylia)                |`pt-BR` | Model akustyczny<br>Model językowy<br>Fonetyczn|
|Portugalski (Portugalia)              |`pt-PT` | Model językowy                                   |
|Rumuński (Rumunia)                 |`ro-RO` | Model językowy                                   |
|Rosyjski (Rosja)                   |`ru-RU` | Model akustyczny<br>Model językowy                 |
|Słowacki (Słowacja)                  |`sk-SK` | Model językowy                                   |
|Słoweński (Słowenia)               |`sl-SI` | Model językowy                                   |
|Hiszpański (Argentyna)                |`es-AR` | Model języka                                   |
|Hiszpański (Boliwia)                  |`es-BO` | Model języka                                   |
|Hiszpański (Chile)                    |`es-CL` | Model języka                                   |
|Hiszpański (Kolumbia)                 |`es-CO` | Model języka                                   |
|Hiszpański (Kostaryka)               |`es-CR` | Model języka                                   |
|Hiszpański (Kuba)                     |`es-CU` | Model języka                                   |
|Hiszpański (Dominikana)       |`es-DO` | Model języka                                   |
|Hiszpański (Ekwador)                  |`es-EC` | Model języka                                   |
|Hiszpański (Salwador)              |`es-SV` | Model języka                                   |
|Hiszpański (Gwatemala)                |`es-GT` | Model języka                                   |
|Hiszpański (Honduras)                 |`es-HN` | Model języka                                   |
|Hiszpański (Meksyk)                   |`es-MX` | Model akustyczny<br>Model językowy                 |
|Hiszpański (Nikaragua)                |`es-NI` | Model języka                                   |
|Hiszpański (Panama)                   |`es-PA` | Model języka                                   |
|Hiszpański (Paragwaj)                 |`es-PY` | Model języka                                   |
|Hiszpański (Peru)                     |`es-PE` | Model języka                                   |
|Hiszpański (Portoryko)              |`es-PR` | Model języka                                   |
|hiszpański (Hiszpania)                    |`es-ES` | Model akustyczny<br>Model językowy                 |
|Hiszpański (Urugwaj)                  |`es-UY` | Model języka                                   |
|Hiszpański (USA)                      |`es-US` | Model języka                                   |
|Hiszpański (Wenezuela)                |`es-VE` | Model języka                                   |
|Szwedzki (Szwecja)                   |`sv-SE` | Model językowy                                   |
|Tamilski (Indie)                      |`ta-IN` | Model językowy                                   |
|Telugu (Indie)                     |`te-IN` | Model językowy                                   |
|Tajski (Tajlandia)                    |`th-TH` | Model językowy                                   |
|Turecki (Turcja)                   |`tr-TR` | Model językowy                                   |

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestawy Microsoft Speech SDK i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne. Możesz również uzyskać pełną listę języków i głosów obsługiwanych dla każdego regionu/punktu końcowego za pomocą [interfejsu API głosy/lista](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

|Język  | Regionalne           | Płeć | Nazwa głosu | Obsługa stylu |
|--|--|--|--|--|
| Arabski (Egipt) | `ar-EG` | Kobiety | `ar-EG-SalmaNeural` | Ogólne |
| Arabski (Arabia Saudyjska) | `ar-SA` | Kobiety | `ar-SA-ZariyahNeural` | Ogólne |
| Bułgarski (Bulgary) | `bg-BG` <sup>Nowe</sup> | Kobiety | `bg-BG-KalinaNeural` | Ogólne |
| Kantoński (chiński tradycyjny, Hongkong SAR) | `zh-HK` | Kobiety | `zh-HK-HiuGaaiNeural` | Ogólne |
| Kataloński | `ca-ES` | Kobiety | `ca-ES-AlbaNeural` | Ogólne |
| Chorwacki (Chorwacja) | `hr-HR` <sup>Nowe</sup> | Kobiety | `hr-HR-GabrijelaNeural` | Ogólne |
| Czeski (czeski) | `cs-CZ` <sup>Nowe</sup> | Kobiety | `cs-CZ-VlastaNeural` | Ogólne |
| Duński (Dania) | `da-DK` | Kobiety | `da-DK-ChristelNeural` | Ogólne |
| Niderlandzki (Holandia) | `nl-NL` | Kobiety | `nl-NL-ColetteNeural` | Ogólne |
| Angielski (Australia) | `en-AU` | Kobiety | `en-AU-NatashaNeural` | Ogólne |
| Angielski (Australia) | `en-AU` <sup>Nowe</sup> | Mężczyźni | `en-AU-WilliamNeural` | Ogólne |
| Angielski (Kanada) | `en-CA` | Kobiety | `en-CA-ClaraNeural` | Ogólne |
| Angielski (Indie) | `en-IN` | Kobiety | `en-IN-NeerjaNeural` | Ogólne |
| Angielski (Irlandia) | `en-IE` <sup>Nowe</sup> | Kobiety | `en-IE-EmilyNeural` | Ogólne |
| Angielski (Zjednoczone Królestwo) | `en-GB` | Kobiety | `en-GB-LibbyNeural` | Ogólne |
| Angielski (Zjednoczone Królestwo) | `en-GB` | Kobiety | `en-GB-MiaNeural` | Ogólne |
| Angielski (Zjednoczone Królestwo) | `en-GB` <sup>Nowe</sup> | Mężczyźni | `en-GB-RyanNeural` | Ogólne |
| Angielski (Stany Zjednoczone) | `en-US` | Kobiety | `en-US-AriaNeural` | Ogólne, dostępne są wiele stylów głosowych |
| Angielski (Stany Zjednoczone) | `en-US` | Mężczyźni | `en-US-GuyNeural` | Ogólne |
| Angielski (Stany Zjednoczone) | `en-US` <sup>Nowe</sup> | Kobiety | `en-US-JennyNeural` | Ogólne, dostępne są wiele stylów głosowych |
| Fiński (Finlandia) | `fi-FI` | Kobiety | `fi-FI-NooraNeural` | Ogólne |
| francuski (Kanada) | `fr-CA` | Kobiety | `fr-CA-SylvieNeural` | Ogólne |
| francuski (Kanada) | `fr-CA` <sup>Nowe</sup> | Mężczyźni | `fr-CA-JeanNeural` | Ogólne |
| Francuski (Francja) | `fr-FR` | Kobiety | `fr-FR-DeniseNeural` | Ogólne |
| Francuski (Francja) | `fr-FR` <sup>Nowe</sup> | Mężczyźni | `fr-FR-HenriNeural` | Ogólne |
| Francuski (Szwajcaria) | `fr-CH` <sup>Nowe</sup> | Kobiety | `fr-CH-ArianeNeural` | Ogólne |
| Niemiecki (Austria) | `de-AT` <sup>Nowe</sup> | Kobiety | `de-AT-IngridNeural` | Ogólne |
| Niemiecki (Niemcy) | `de-DE` | Kobiety | `de-DE-KatjaNeural` | Ogólne |
| Niemiecki (Niemcy) | `de-DE` <sup>Nowe</sup> | Mężczyźni | `de-DE-ConradNeural` | Ogólne |
| Niemiecki (Szwajcaria) | `de-CH` <sup>Nowe</sup> | Kobiety | `de-CH-LeniNeural` | Ogólne |
| Grecki (Grecja) | `el-GR` <sup>Nowe</sup> | Kobiety | `el-GR-AthinaNeural` | Ogólne |
| Hebrajski (Izrael) | `he-IL` <sup>Nowe</sup> | Kobiety | `he-IL-HilaNeural` | Ogólne |
| Hindi (Indie) | `hi-IN` | Kobiety | `hi-IN-SwaraNeural` | Ogólne |
| Węgierski (Węgry) | `hu-HU` <sup>Nowe</sup> | Kobiety | `hu-HU-NoemiNeural` | Ogólne |
| Indonezyjski (Indonezja) | `id-ID` <sup>Nowe</sup> | Mężczyźni | `id-ID-ArdiNeural` | Ogólne |
| Włoski (Włochy) | `it-IT` | Kobiety | `it-IT-ElsaNeural` | Ogólne |
| Włoski (Włochy) | `it-IT` <sup>Nowe</sup> | Kobiety | `it-IT-IsabellaNeural` | Ogólne |
| Włoski (Włochy) | `it-IT` <sup>Nowe</sup> | Mężczyźni | `it-IT-DiegoNeural` | Ogólne |
| Japoński (Japonia) | `ja-JP` | Kobiety | `ja-JP-NanamiNeural` | Ogólne |
| Japoński (Japonia) | `ja-JP` <sup>Nowe</sup> | Mężczyźni | `ja-JP-KeitaNeural` | Ogólne |
| Koreański (Korea) | `ko-KR` | Kobiety | `ko-KR-SunHiNeural` | Ogólne |
| Koreański (Korea) | `ko-KR` <sup>Nowe</sup> | Mężczyźni | `ko-KR-InJoonNeural` | Ogólne |
| Malajski (Malezja) | `ms-MY` <sup>Nowe</sup> | Kobiety | `ms-MY-YasminNeural` | Ogólne |
| Mandarynki (chiński uproszczony, Chiny) | `zh-CN` | Kobiety | `zh-CN-XiaoxiaoNeural` | Ogólne, dostępne są wiele stylów głosowych |
| Mandarynki (chiński uproszczony, Chiny) | `zh-CN` | Kobiety | `zh-CN-XiaoyouNeural` | Głos dla dzieci, zoptymalizowany pod kątem narracji w wątku |
| Mandarynki (chiński uproszczony, Chiny) | `zh-CN` | Mężczyźni | `zh-CN-YunyangNeural` | Zoptymalizowane pod kątem czytania wiadomości, dostępne są wiele stylów głosowych |
| Mandarynki (chiński uproszczony, Chiny) | `zh-CN` | Mężczyźni | `zh-CN-YunyeNeural` | Optymalizacja pod kątem narracji w wątku |
| Mandarynki (chiński tradycyjny, Tajwan) | `zh-TW` | Kobiety | `zh-TW-HsiaoYuNeural` | Ogólne |
| Norweski, BokmÃ ¥ l (Norwegia) | `nb-NO` | Kobiety | `nb-NO-IselinNeural` | Ogólne |
| Polski (Polska) | `pl-PL` | Kobiety | `pl-PL-ZofiaNeural` | Ogólne |
| Portugalski (Brazylia) | `pt-BR` | Kobiety | `pt-BR-FranciscaNeural` | Ogólne, dostępne są wiele stylów głosowych |
| Portugalski (Brazylia) | `pt-BR` <sup>Nowe</sup> | Mężczyźni | `pt-BR-AntonioNeural` | Ogólne |
| Portugalski (Portugalia) | `pt-PT` | Kobiety | `pt-PT-FernandaNeural` | Ogólne |
| Rumuński (Rumunia) | `ro-RO` <sup>Nowe</sup> | Kobiety | `ro-RO-AlinaNeural` | Ogólne |
| Rosyjski (Rosja) | `ru-RU` | Kobiety | `ru-RU-DariyaNeural` | Ogólne |
| Słowacki (Słowacja) | `sk-SK` <sup>Nowe</sup> | Kobiety | `sk-SK-ViktoriaNeural` | Ogólne |
| Słoweński (Słowenia) | `sl-SI` <sup>Nowe</sup> | Kobiety | `sl-SI-PetraNeural` | Ogólne |
| Hiszpański (Meksyk) | `es-MX` | Kobiety | `es-MX-DaliaNeural` | Ogólne |
| Hiszpański (Meksyk) | `es-MX` <sup>Nowe</sup> | Mężczyźni | `es-MX-JorgeNeural` | Ogólne |
| hiszpański (Hiszpania) | `es-ES` | Kobiety | `es-ES-ElviraNeural` | Ogólne |
| hiszpański (Hiszpania) | `es-ES` <sup>Nowe</sup> | Mężczyźni | `es-ES-AlvaroNeural` | Ogólne |
| Szwedzki (Szwecja) | `sv-SE` | Kobiety | `sv-SE-HilleviNeural` | Ogólne |
| Tamilski (Indie) | `ta-IN` <sup>Nowe</sup> | Kobiety | `ta-IN-PallaviNeural` | Ogólne |
| Telugu (Indie) | `te-IN` <sup>Nowe</sup> | Kobiety | `te-IN-ShrutiNeural` | Ogólne |
| Tajski (Tajlandia) | `th-TH` | Kobiety | `th-TH-AcharaNeural` | Ogólne |
| Tajski (Tajlandia) | `th-TH` <sup>Nowe</sup> | Kobiety | `th-TH-PremwadeeNeural` | Ogólne |
| Turecki (Turcja) | `tr-TR` | Kobiety | `tr-TR-EmelNeural` | Ogólne |
| Wietnamski (Wietnam) | `vi-VN` <sup>Nowe</sup> | Kobiety | `vi-VN-HoaiMyNeural` | Ogólne|

> [!IMPORTANT]
> `en-US-JessaNeural`Głos został zmieniony na `en-US-AriaNeural` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaNeural)" w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Język | Regionalne | Płeć | Nazwa głosu |
|--|--|--|--|
| Arabski (arabski)  |  `ar-EG`  |  Kobiety  |  `ar-EG-Hoda`|
| Arabski (Arabia Saudyjska)  |  `ar-SA`  |  Mężczyźni  |  `ar-SA-Naayf`|
| Bułgarski (Bułgaria)  |  `bg-BG`  |  Mężczyźni  |  `bg-BG-Ivan`|
| Kantoński (chiński tradycyjny, Hongkong SAR)  |  `zh-HK`  |  Mężczyźni  |  `zh-HK-Danny`|
| Kantoński (chiński tradycyjny, Hongkong SAR)  |  `zh-HK`  |  Kobiety  |  `zh-HK-TracyRUS`|
| Kataloński  |  `ca-ES`  |  Kobiety  |  `ca-ES-HerenaRUS`|
| Chorwacki (Chorwacja)  |  `hr-HR`  |  Mężczyźni  |  `hr-HR-Matej`|
| Czeski (Czechy)  |  `cs-CZ`  |  Mężczyźni  |  `cs-CZ-Jakub`|
| Duński (Dania)  |  `da-DK`  |  Kobiety  |  `da-DK-HelleRUS`|
| Niderlandzki (Holandia)  |  `nl-NL`  |  Kobiety  |  `nl-NL-HannaRUS`|
| Angielski (Australia)  |  `en-AU`  |  Kobiety  |  `en-AU-Catherine`|
| Angielski (Australia)  |  `en-AU`  |  Kobiety  |  `en-AU-HayleyRUS`|
| Angielski (Kanada)  |  `en-CA`  |  Kobiety  |  `en-CA-HeatherRUS`|
| Angielski (Kanada)  |  `en-CA`  |  Kobiety  |  `en-CA-Linda`|
| Angielski (Indie)  |  `en-IN`  |  Kobiety  |  `en-IN-Heera`|
| Angielski (Indie)  |  `en-IN`  |  Kobiety  |  `en-IN-PriyaRUS`|
| Angielski (Indie)  |  `en-IN`  |  Mężczyźni  |  `en-IN-Ravi`|
| Angielski (Irlandia)  |  `en-IE`  |  Mężczyźni  |  `en-IE-Sean`|
| Angielski (Zjednoczone Królestwo)  |  `en-GB`  |  Mężczyźni  |  `en-GB-George`|
| Angielski (Zjednoczone Królestwo)  |  `en-GB`  |  Kobiety  |  `en-GB-HazelRUS`|
| Angielski (Zjednoczone Królestwo)  |  `en-GB`  |  Kobiety  |  `en-GB-Susan`|
| Angielski (Stany Zjednoczone)  |  `en-US`  |  Mężczyźni  |  `en-US-BenjaminRUS`|
| Angielski (Stany Zjednoczone)  |  `en-US`  |  Mężczyźni  |  `en-US-GuyRUS`|
| Angielski (Stany Zjednoczone)  |  `en-US`  |  Kobiety  |  `en-US-JessaRUS`|
| Angielski (Stany Zjednoczone)  |  `en-US`  |  Kobiety  |  `en-US-ZiraRUS`|
| Fiński (Finlandia)  |  `fi-FI`  |  Kobiety  |  `fi-FI-HeidiRUS`|
| francuski (Kanada)  |  `fr-CA`  |  Kobiety  |  `fr-CA-Caroline`|
| francuski (Kanada)  |  `fr-CA`  |  Kobiety  |  `fr-CA-HarmonieRUS`|
| Francuski (Francja)  |  `fr-FR`  |  Kobiety  |  `fr-FR-HortenseRUS`|
| Francuski (Francja)  |  `fr-FR`  |  Kobiety  |  `fr-FR-Julie`|
| Francuski (Francja)  |  `fr-FR`  |  Mężczyźni  |  `fr-FR-Paul`|
| Francuski (Szwajcaria)  |  `fr-CH`  |  Mężczyźni  |  `fr-CH-Guillaume`|
| Niemiecki (Austria)  |  `de-AT`  |  Mężczyźni  |  `de-AT-Michael`|
| Niemiecki (Niemcy)  |  `de-DE`  |  Kobiety  |  `de-DE-HeddaRUS`|
| Niemiecki (Niemcy)  |  `de-DE`  |  Mężczyźni  |  `de-DE-Stefan`|
| Niemiecki (Szwajcaria)  |  `de-CH`  |  Mężczyźni  |  `de-CH-Karsten`|
| Grecki (Grecja)  |  `el-GR`  |  Mężczyźni  |  `el-GR-Stefanos`|
| Hebrajski (Izrael)  |  `he-IL`  |  Mężczyźni  |  `he-IL-Asaf`|
| Hindi (Indie)  |  `hi-IN`  |  Mężczyźni  |  `hi-IN-Hemant`|
| Hindi (Indie)  |  `hi-IN`  |  Kobiety  |  `hi-IN-Kalpana`|
| Węgierski (Węgry)  |  `hu-HU`  |  Mężczyźni  |  `hu-HU-Szabolcs`|
| Indonezyjski (Indonezja)  |  `id-ID`  |  Mężczyźni  |  `id-ID-Andika`|
| Włoski (Włochy)  |  `it-IT`  |  Mężczyźni  |  `it-IT-Cosimo`|
| Włoski (Włochy)  |  `it-IT`  |  Kobiety  |  `it-IT-LuciaRUS`|
| Japoński (Japonia)  |  `ja-JP`  |  Kobiety  |  `ja-JP-Ayumi`|
| Japoński (Japonia)  |  `ja-JP`  |  Kobiety  |  `ja-JP-HarukaRUS`|
| Japoński (Japonia)  |  `ja-JP`  |  Mężczyźni  |  `ja-JP-Ichiro`|
| Koreański (Korea)  |  `ko-KR`  |  Kobiety  |  `ko-KR-HeamiRUS`|
| Malajski (Malezja)  |  `ms-MY`  |  Mężczyźni  |  `ms-MY-Rizwan`|
| Mandarynki (chiński uproszczony, Chiny)  |  `zh-CN`  |  Kobiety  |  `zh-CN-HuihuiRUS`|
| Mandarynki (chiński uproszczony, Chiny)  |  `zh-CN`  |  Mężczyźni  |  `zh-CN-Kangkang`|
| Mandarynki (chiński uproszczony, Chiny)  |  `zh-CN`  |  Kobiety  |  `zh-CN-Yaoyao`|
| Mandarynki (chiński tradycyjny, Tajwan)  |  `zh-TW`  |  Kobiety  |  `zh-TW-HanHanRUS`|
| Mandarynki (chiński tradycyjny, Tajwan)  |  `zh-TW`  |  Kobiety  |  `zh-TW-Yating`|
| Mandarynki (chiński tradycyjny, Tajwan)  |  `zh-TW`  |  Mężczyźni  |  `zh-TW-Zhiwei`|
| Norweski, BokmÃ ¥ l (Norwegia)  |  `nb-NO`  |  Kobiety  |  `nb-NO-HuldaRUS`|
| Polski (Polska)  |  `pl-PL`  |  Kobiety  |  `pl-PL-PaulinaRUS`|
| Portugalski (Brazylia)  |  `pt-BR`  |  Mężczyźni  |  `pt-BR-Daniel`|
| Portugalski (Brazylia)  |  `pt-BR`  |  Kobiety  |  `pt-BR-HeloisaRUS`|
| Portugalski (Portugalia)  |  `pt-PT`  |  Kobiety  |  `pt-PT-HeliaRUS`|
| Rumuński (Rumunia)  |  `ro-RO`  |  Mężczyźni  |  `ro-RO-Andrei`|
| Rosyjski (Rosja)  |  `ru-RU`  |  Kobiety  |  `ru-RU-EkaterinaRUS`|
| Rosyjski (Rosja)  |  `ru-RU`  |  Kobiety  |  `ru-RU-Irina`|
| Rosyjski (Rosja)  |  `ru-RU`  |  Mężczyźni  |  `ru-RU-Pavel`|
| Słowacki (Słowacja)  |  `sk-SK`  |  Mężczyźni  |  `sk-SK-Filip`|
| Słoweński (Słowenia)  |  `sl-SI`  |  Mężczyźni  |  `sl-SI-Lado`|
| Hiszpański (Meksyk)  |  `es-MX`  |  Kobiety  |  `es-MX-HildaRUS`|
| Hiszpański (Meksyk)  |  `es-MX`  |  Mężczyźni  |  `es-MX-Raul`|
| hiszpański (Hiszpania)  |  `es-ES`  |  Kobiety  |  `es-ES-HelenaRUS`|
| hiszpański (Hiszpania)  |  `es-ES`  |  Kobiety  |  `es-ES-Laura`|
| hiszpański (Hiszpania)  |  `es-ES`  |  Mężczyźni  |  `es-ES-Pablo`|
| Szwedzki (Szwecja)  |  `sv-SE`  |  Kobiety  |  `sv-SE-HedvigRUS`|
| Tamilski (Indie)  |  `ta-IN`  |  Mężczyźni  |  `ta-IN-Valluvar`|
| Telugu (Indie)  |  `te-IN`  |  Kobiety  |  `te-IN-Chitra`|
| Tajski (Tajlandia)  |  `th-TH`  |  Mężczyźni  |  `th-TH-Pattara`|
| Turecki (Turcja)  |  `tr-TR`  |  Kobiety  |  `tr-TR-SedaRUS`|
| Wietnamski (Wietnam)  |  `vi-VN`  |  Mężczyźni  |  `vi-VN-An`  |


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

| Język | Regionalne | Weryfikacja zależna od tekstu | Weryfikacja niezależna od tekstu | Identyfikacja niezależna od tekstu |
|----|----|----|----|----|
|Angielski (Stany Zjednoczone)  |  en-US  |  tak  |  tak  |  tak |
|Chiński (mandarynki, uproszczony) | zh-CN     |     nie dotyczy |     tak |     tak|
|Angielski (Australia)     | en-AU     | nie dotyczy     | tak     | tak|
|Angielski (Kanada)     | EN-CA     | nie dotyczy |     tak |     tak|
|Angielski (Zjednoczone Królestwo)     | en-GB     | nie dotyczy     | tak     | tak|
|francuski (Kanada)     | fr — CA     | nie dotyczy     | tak |     tak|
|Francuski (Francja)     | fr-FR     | nie dotyczy     | tak     | tak|
|Niemiecki (Niemcy)     | de-DE     | nie dotyczy     | tak     | tak|
|Włoski | it-IT     |     nie dotyczy     | tak |     tak|
|japoński     | ja-JP | nie dotyczy     | tak     | tak|
|Portugalski (Brazylia) | pt-BR |     nie dotyczy |     tak |     tak|
|Hiszpański (Meksyk)     | es — MX     | nie dotyczy |     tak |     tak|
|hiszpański (Hiszpania)     | es-ES | nie dotyczy     | tak |     tak|

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
