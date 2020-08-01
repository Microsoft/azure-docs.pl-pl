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
ms.openlocfilehash: 8e3f33e8f453ffd6eedaeb75a041af7ec11b4e39
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87457808"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). 

W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania **zapisu audio + z etykietami ludzkimi** lub **powiązanego tekstu: zdania**. Aby dowiedzieć się więcej o dostosowywaniu, zobacz Wprowadzenie [do Custom Speech](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Regionalne  | Język                          | Dostosowania                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arabski (Zjednoczone Emiraty Arabskie)                      | Nie                                                |
| `ar-BH` | Arabski (Bahrajn), nowoczesny Standard | Model językowy                                    |
| `ar-EG` | Arabski (Egipt)                    | Model językowy                                    |
| `ar-IL` | Arabski (Izrael)                   | Nie                                                |
| `ar-JO` | Arabski (Jordania)                   | Nie                                                |
| `ar-KW` | Arabski (Kuwejt)                   | Nie                                                |
| `ar-LB` | Arabski (Liban)                  | Nie                                                |
| `ar-PS` | Arabski (palestyński)                | Nie                                                |
| `ar-QA` | Arabski (katar)                    | Nie                                                |
| `ar-SA` | Arabski (Arabia Saudyjska)             | Nie                                                |
| `ar-SY` | Arabski (Syria)                    | Model językowy                                    |
| `ca-ES` | Kataloński                           | Model językowy                                    |
| `cs-CZ` | Czeski (Czechy)            | Model języka                                    | 
| `da-DK` | Duński (Dania)                  | Model językowy                                    |
| `de-DE` | Niemiecki (Niemcy)                  | Model akustyczny<br>Model językowy<br>Fonetyczn |
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
| `fi-FI` | Fiński (Finlandia)                 | Model językowy                                    |
| `fr-CA` | francuski (Kanada)                   | Model akustyczny<br>Model językowy                  |
| `fr-FR` | Francuski (Francja)                   | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `gu-IN` | Gudżarati (Indyjski)                 | Model językowy                                    |
| `hi-IN` | Hindi (Indie)                     | Model akustyczny<br>Model językowy                  |
| `hu-HU` | Węgierski (Węgry)               | Model języka                                    | 
| `it-IT` | Włoski (Włochy)                   | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `ja-JP` | Japoński (Japonia)                  | Model językowy                                    |
| `ko-KR` | Koreański (Korea)                    | Model językowy                                    |
| `mr-IN` | Marathi (Indie)                   | Model językowy                                    |
| `nb-NO` | Norweski (bokmål) (Norwegia)       | Model językowy                                    |
| `nl-NL` | Niderlandzki (Holandia)               | Model językowy                                    |
| `pl-PL` | Polski (Polska)                   | Model językowy                                    |
| `pt-BR` | Portugalski (Brazylia)               | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `pt-PT` | Portugalski (Portugalia)             | Model językowy                                    |
| `ru-RU` | Rosyjski (Rosja)                  | Model akustyczny<br>Model językowy                  |
| `sv-SE` | Szwedzki (Szwecja)                  | Model językowy                                    |
| `ta-IN` | Tamilski (Indie)                     | Model językowy                                    |
| `te-IN` | Telugu (Indie)                    | Model językowy                                    |
| `th-TH` | Tajski (Tajlandia)                   | Nie                                                |
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
| `ar-EG` | Arabski (Egipt)                  | Kobiety | `ar-EG-SalmaNeural`      | Ogólne |
| `ar-SA` | Arabski (Arabia Saudyjska)           | Kobiety | `ar-SA-ZariyahNeura`     | Ogólne |
| `ca-ES` | Kataloński                 | Kobiety | `ca-ES-AlbaNeural`       | Ogólne |
| `da-DK` | Duński (Dania)                | Kobiety | `da-DK-ChristelNeural`   | Ogólne |
| `de-DE` | Niemiecki (Niemcy)                | Kobiety | `de-DE-KatjaNeural`      | Ogólne |
| `en-AU` | Angielski (Australia)             | Kobiety | `en-AU-NatashaNeural`    | Ogólne |
| `en-CA` | Angielski (Kanada)                | Kobiety | `en-CA-ClaraNeural`      | Ogólne |
| `en-GB` | Angielski (Zjednoczone Królestwo)                    | Kobiety | `en-GB-LibbyNeural`      | Ogólne |
|         |                                 | Kobiety | `en-GB-MiaNeural`        | Ogólne |
| `en-IN` | Angielski (Indie)                 | Kobiety | `en-IN-NeerjaNeural`     | Ogólne |
| `en-US` | Angielski (Stany Zjednoczone)                    | Kobiety | `en-US-AriaNeural`       | Ogólne, dostępne są wiele stylów głosowych |
|         |                                 | Mężczyźni   | `en-US-GuyNeural`        | Ogólne |
| `es-ES` | hiszpański (Hiszpania)                 | Kobiety | `es-ES-ElviraNeural`     | Ogólne |
| `es-MX` | Hiszpański (Meksyk)                | Kobiety | `es-MX-DaliaNeural`      | Ogólne |
| `fi-FI` | Fiński (Finlandia)               | Kobiety | `fi-FI-NooraNeural`      | Ogólne |
| `fr-CA` | francuski (Kanada)                 | Kobiety | `fr-CA-SylvieNeural`     | Ogólne |
| `fr-FR` | Francuski (Francja)                 | Kobiety | `fr-FR-DeniseNeural`     | Ogólne |
| `hi-IN` | Hindi (Indie)                   | Kobiety | `hi-IN-SwaraNeural`      | Ogólne |
| `it-IT` | Włoski (Włochy)                 | Kobiety | `it-IT-ElsaNeural`       | Ogólne |
| `ja-JP` | Japoński                        | Kobiety | `ja-JP-NanamiNeural`     | Ogólne |
| `ko-KR` | Koreański                          | Kobiety | `ko-KR-SunHiNeural`      | Ogólne |
| `nb-NO` | Norweski                       | Kobiety | `nb-NO-IselinNeural`     | Ogólne |
| `nl-NL` | Holenderski (Netherland)              | Kobiety | `nl-NL-ColetteNeural`    | Ogólne |
| `pl-PL` | Polski (Polska)                 | Kobiety | `pl-PL-ZofiaNeural`      | Ogólne |
| `pt-BR` | Portugalski (Brazylia)             | Kobiety | `pt-BR-FranciscaNeural`  | Ogólne, dostępne są wiele stylów głosowych |
| `tr-TR` | Turecki                         | Kobiety | `tr-TR-EmelNeural`       | Ogólne |
| `pt-PT` | Portugalski (Portugalia)           | Kobiety | `pt-PT-FernandaNeural`   | Ogólne |
| `ru-RU` | Rosyjski (Rosja)                | Kobiety | `ru-RU-DariyaNeural`     | Ogólne |
| `sv-SE` | Szwedzki (Szwecja)                | Kobiety | `sv-SE-HilleviNeural`    | Ogólne |
| `th-TH` | Tajski (Tajlandia)                 | Kobiety | `th-TH-AcharaNeural`     | Ogólne |
| `zh-CN` | Chiński (mandarynki, uproszczony)  | Kobiety | `zh-CN-XiaoxiaoNeural`   | Ogólne, dostępne są wiele stylów głosowych |
|         |                                 | Kobiety | `zh-CN-XiaoyouNeural`    | Głos dla dzieci, zoptymalizowany pod kątem narracji w wątku |
|         |                                 | Mężczyźni   | `zh-CN-YunyangNeural`    | Zoptymalizowane pod kątem czytania wiadomości, dostępne są wiele stylów głosowych |
|         |                                 | Mężczyźni   | `zh-CN-YunyeNeural`      | Optymalizacja pod kątem narracji w wątku |
| `zh-HK` | Chiński (kantoński, tranditional)   | Kobiety | `zh-HK-HiuGaaiNeural`| Ogólne |
| `zh-TW` | Chiński (tajwański mandarynki)   | Kobiety | `zh-TW-HsiaoYuNeural`    | Ogólne |

> [!IMPORTANT]
> `en-US-JessaNeural`Głos został zmieniony na `en-US-AriaNeural` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaNeural)" w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Regionalne | Język | Płeć | Nazwa głosu |
|--|--|--|--|
| <sup>jedno</sup>`ar-EG` | Arabski (Egipt) | Kobiety | "AR-EG-Hoda" |
| `ar-SA` | Arabski (Arabia Saudyjska) | Mężczyźni | "ar-SA-Naayf" |
| `bg-BG` | Bułgarski | Mężczyźni |  "BG-BG-Ivan" |
| `ca-ES` | Kataloński | Kobiety |  "CA-ES-HerenaRUS" |
| `cs-CZ` | Czeski | Mężczyźni | "CS-CZ-Jakub" |
| `da-DK` | Duński | Kobiety |  "da-DK-HelleRUS" |
| `de-AT` | Niemiecki (Austria) | Mężczyźni | "de-AT-Michael" |
| `de-CH` | Niemiecki (Szwajcaria) | Mężczyźni |  "de-CH-Karsten" |
| `de-DE` | Niemiecki (Niemcy) | Kobiety |  "de-DE-Hedda" |
|  |  | Kobiety | "de-DE-HeddaRUS" |
|  |  | Mężczyźni |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grecki | Mężczyźni | "El-GR-Stefanos" |
| `en-AU` | Angielski (Australia) | Kobiety |  "en-AU-Catherine" |
|  |  | Kobiety |  "en-AU-HayleyRUS" |
| `en-CA` | Angielski (Kanada) | Kobiety |  "en-CA-Linda" |
|  |  | Kobiety |  "en-CA-HeatherRUS" |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety |  "pl-GB-Susan-Apollo" |
|  |  | Kobiety |  "pl-GB-HazelRUS" |
|  |  | Mężczyźni |  "pl-GB-George-Apollo" |
| `en-IE` | Angielski (Irlandia) | Mężczyźni | "EN-IE-Janusz" |
| `en-IN` | Angielski (Indie) | Kobiety | "pl-IN-Heera-Apollo" |
|  |  | Kobiety |  "pl-IN-PriyaRUS" |
|  |  | Mężczyźni |  "pl-IN-Ravi-Apollo" |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety |  "pl-US-ZiraRUS" |
|  |  | Kobiety | "pl-US-AriaRUS" |
|  |  | Mężczyźni | "pl-US-BenjaminRUS" |
|  |  | Mężczyźni |  "pl-US-Guy24kRUS" |
| `es-ES` | hiszpański (Hiszpania) | Kobiety |  "es-ES-Laura-Apollo" |
|  |  | Kobiety | "es-ES-HelenaRUS" |
|  |  | Mężczyźni | "es-ES-Pablo-Apollo" |
| `es-MX` | Hiszpański (Meksyk) | Kobiety |  "es-MX-HildaRUS" |
|  |  | Mężczyźni | "es-MX-Raul-Apollo" |
| `fi-FI` | Fiński | Kobiety | "fi-FI-HeidiRUS" |
| `fr-CA` | francuski (Kanada) | Kobiety | "fr-CA-Caroline" |
|  |  | Kobiety | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francuski (Szwajcaria) | Mężczyźni | "fr-CH-Guillaume" |
| `fr-FR` | Francuski (Francja) | Kobiety |  "fr-FR-Julie-Apollo" |
|  |  | Kobiety |"fr-FR-HortenseRUS" |
|  |  | Mężczyźni |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrajski (Izrael) | Mężczyźni |  "IT-IL-Asaf" |
| `hi-IN` | Hindi (Indie) | Kobiety | "Hi-IN-Kalpana-Apollo" |
|  |  | Kobiety |  "Witaj w Kalpana" |
|  |  | Mężczyźni |  "Witaj w Hemant" |
| `hr-HR` | Chorwacki | Mężczyźni | "HR-HR-Matej" |
| `hu-HU` | Węgierski | Mężczyźni |  "hu-HU-Szabolcs" |
| `id-ID` | Indonezyjski | Mężczyźni | "ID-ID-andika" |
| `it-IT` | Włoski | Mężczyźni |  "IT-Cosimo-Apollo" |
|  |  | Kobiety |  "IT-LuciaRUS" |
| `ja-JP` | Japoński | Kobiety |  "ja-JP-Ayumi-Apollo" |
|  |  | Mężczyźni | "ja-JP-Ichiro-Apollo" |
|  |  | Kobiety |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreański | Kobiety | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajski | Mężczyźni |  "MS-MY-Rizwan" |
| `nb-NO` | Norweski | Kobiety |  "NB-NO-HuldaRUS" |
| `nl-NL` | Niderlandzki | Kobiety |  "NL-NL-HannaRUS" |
| `pl-PL` | Polski | Kobiety |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugalski (Brazylia) | Kobiety | "pt-BR-HeloisaRUS" |
|  |  | Mężczyźni |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugalski (Portugalia) | Kobiety | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumuński | Mężczyźni | "RO-RO-Andrei" |
| `ru-RU` | Rosyjski | Kobiety |  "ru-RU-Irina-Apollo" |
|  |  | Mężczyźni | "ru-RU-Pavel-Apollo" |
|  |  | Kobiety |  ru — RU — EkaterinaRUS |
| `sk-SK` | Słowacki | Mężczyźni | "sk-SK-Filip" |
| `sl-SI` | Słoweński | Mężczyźni |  "SL-SI-Lado" |
| `sv-SE` | Szwedzki | Kobiety | "SV-SE-HedvigRUS" |
| `ta-IN` | Tamilski (Indie) | Mężczyźni |  "Ta-IN-Valluvar" |
| `te-IN` | Telugu (Indie) | Kobiety |  "te w Chitra" |
| `th-TH` | Tajlandzki | Mężczyźni |  "th-TH-Pattara" |
| `tr-TR` | Turecki (Turcja) | Kobiety | "TR-TR-SedaRUS" |
| `vi-VN` | Wietnamski | Mężczyźni |  "VI-VN-an" |
| `zh-CN` | Chiński (mandarynki, uproszczony) | Kobiety |  "zh-CN-HuihuiRUS" |
|  |  | Kobiety | "zh-CN-YaoYao-Apollo" |
|  |  | Mężczyźni | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chiński (kantoński, tradycyjny) | Kobiety |  "zh-HK-Tracy-Apollo" |
|  |  | Kobiety | "zh-HK-TracyRUS" |
|  |  | Mężczyźni |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chiński (tajwański mandarynki) | Kobiety |  "zh-TW-Yating-Apollo" |
|  |  | Kobiety | "zh-TW-HanHanRUS" |
|  |  | Mężczyźni |  "zh-TW-Zhiwei-Apollo" |

**1** *AR-EG obsługuje nowoczesne standardowe arabski (MSA).*

> [!IMPORTANT]
> `en-US-Jessa`Głos został zmieniony na `en-US-Aria` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaRUS)" w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla,,,,,,,, `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` i `zh-CN` . Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, wybierz opcję `en-GB` .

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz pozycję "dwujęzyczne w języku chińskim English", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich ustawieniach regionalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem tego, `en-US` `zh-CN` gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

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
| Japoński                | `ja`          |
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
| pl-PL | Angielski (Stany Zjednoczone) | tak | tak | tak |
|zh-CN  |Chiński (mandarynki, uproszczony)|    nie dotyczy|    tak|    tak|
|de-DE  |Niemiecki (Niemcy)   |nie dotyczy    |tak    |tak|
|en-GB  |Angielski (Zjednoczone Królestwo)   |nie dotyczy    |tak    |tak|
|fr-FR  |Francuski (Francja)    |nie dotyczy    |tak    |tak|
|en-AU  |Angielski (Australia)    |nie dotyczy    |tak    |tak|
|EN-CA  |Angielski (Kanada)   |nie dotyczy|   tak|    tak|
|fr — CA  |francuski (Kanada)    |nie dotyczy    |tak|   tak|
|it-IT  |Włoski|   nie dotyczy |tak|   tak|
|es-ES| hiszpański (Hiszpania) |nie dotyczy    |tak|   tak|
|es — MX  |Hiszpański (Meksyk)   |nie dotyczy|   tak|    tak|
|ja-JP| Japoński    |nie dotyczy    |tak    |tak|
|pt-BR| Portugalski (Brazylia)|    nie dotyczy|    tak|    tak|

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
