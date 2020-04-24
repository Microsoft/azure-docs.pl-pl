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
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113837"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania zapisu audio + z etykietami ludzkimi lub powiązanego tekstu: zdania. Dostosowanie wymowy jest obecnie dostępne tylko `en-US` dla `de-DE`i. Więcej informacji na temat dostosowywania [znajdziesz tutaj](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Ustawienia regionalne  | Język                          | Obsługiwane | Dostosowania                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabski (Zjednoczone Emiraty Arabskie)                      | Tak       | Nie                                                |
| `ar-BH` | Arabski (Bahrajn), nowoczesny Standard | Yes       | Model językowy                                    |
| `ar-EG` | Arabski (Egipt)                    | Yes       | Model językowy                                    |
| `ar-KW` | Arabski (Kuwejt)                   | Tak       | Nie                                                |
| `ar-QA` | Arabski (katar)                    | Tak       | Nie                                                |
| `ar-SA` | Arabski (Arabia Saudyjska)             | Tak       | Nie                                                |
| `ar-SY` | Arabski (Syria)                    | Yes       | Model językowy                                    |
| `ca-ES` | Kataloński                           | Yes       | Model językowy                                    |
| `da-DK` | Duński (Dania)                  | Yes       | Model językowy                                    |
| `de-DE` | Niemiecki (Niemcy)                  | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `en-AU` | Angielski (Australia)               | Yes       | Model akustyczny<br>Model językowy                  |
| `en-CA` | Angielski (Kanada)                  | Yes       | Model akustyczny<br>Model językowy                  |
| `en-GB` | Angielski (Zjednoczone Królestwo)          | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `en-IN` | Angielski (Indie)                   | Yes       | Model akustyczny<br>Model językowy                  |
| `en-NZ` | Angielski (Nowa Zelandia)             | Yes       | Model akustyczny<br>Model językowy                  |
| `en-US` | Angielski (Stany Zjednoczone)           | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `es-ES` | hiszpański (Hiszpania)                   | Yes       | Model akustyczny<br>Model językowy                  |
| `es-MX` | Hiszpański (Meksyk)                  | Yes       | Model akustyczny<br>Model językowy                  |
| `fi-FI` | Fiński (Finlandia)                 | Yes       | Model językowy                                    |
| `fr-CA` | francuski (Kanada)                   | Yes       | Model akustyczny<br>Model językowy                  |
| `fr-FR` | Francuski (Francja)                   | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `gu-IN` | Gudżarati (Indyjski)                 | Yes       | Model językowy                                    |
| `hi-IN` | Hindi (Indie)                     | Yes       | Model akustyczny<br>Model językowy                  |
| `it-IT` | Włoski (Włochy)                   | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `ja-JP` | Japoński (Japonia)                  | Yes       | Model językowy                                    |
| `ko-KR` | Koreański (Korea)                    | Yes       | Model językowy                                    |
| `mr-IN` | Marathi (Indie)                   | Yes       | Model językowy                                    |
| `nb-NO` | Norweski (bokmål) (Norwegia)       | Yes       | Model językowy                                    |
| `nl-NL` | Niderlandzki (Holandia)               | Yes       | Model językowy                                    |
| `pl-PL` | Polski (Polska)                   | Yes       | Model językowy                                    |
| `pt-BR` | portugalski (Brazylia)               | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `pt-PT` | Portugalski (Portugalia)             | Yes       | Model językowy                                    |
| `ru-RU` | Rosyjski (Rosja)                  | Yes       | Model akustyczny<br>Model językowy                  |
| `sv-SE` | Szwedzki (Szwecja)                  | Yes       | Model językowy                                    |
| `ta-IN` | Tamilski (Indie)                     | Yes       | Model językowy                                    |
| `te-IN` | Telugu (Indie)                    | Tak       | Nie                                                |
| `th-TH` | Tajski (Tajlandia)                   | Tak       | Nie                                                |
| `tr-TR` | Turecki (Turcja)                  | Tak       | Nie                                                |
| `zh-CN` | Chiński (mandarynki, uproszczony)    | Yes       | Model akustyczny<br>Model językowy                  |
| `zh-HK` | Chiński (kantoński, tradycyjny)  | Yes       | Model językowy                                    |
| `zh-TW` | Chiński (tajwański mandarynki)      | Yes       | Model językowy                                    |

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zestawy Microsoft Speech SDK i interfejsy API REST obsługują te głosy, z których każdy obsługuje określony język i dialekt, identyfikowane przez ustawienia regionalne.

> [!IMPORTANT]
> Ceny różnią się w zależności od standardowych, niestandardowych i neuronowychych głosów. Aby uzyskać dodatkowe informacje, odwiedź stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

### <a name="neural-voices"></a>Głosy neuronowych

Neuronowych text-to-Speech to nowy typ syntezy mowy obsługiwanej przez głębokie sieci neuronowych. W przypadku korzystania z głosu neuronowych, synteza mowy jest niemal nieczytelna w odróżnieniu od nagrań ludzkich.

Głosy neuronowych mogą służyć do współdziałania z rozszerzenie czatbotów i asystentów głosowych bardziej naturalnych i atrakcyjnych, dzięki czemu można konwertować cyfrowe teksty, takie jak książki elektroniczne, na Audiobooks i ulepszać systemy nawigacyjne w samodzielnym zakresie. Podobnie jak naturalna prosodya i wyraźny zbiór wyrazów, głosy neuronowych znacząco zmniejszają zmęczenie nasłuchiwania, gdy użytkownicy współpracują z systemami AI.

Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Ustawienia regionalne  | Język            | Płeć | Pełne Mapowanie nazw usług                                               | Krótka nazwa głosu        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Niemiecki (Niemcy)    | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Angielski (Stany Zjednoczone)        | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaNeural)"      | "pl-US-AriaNeural"      |
| `en-US` | Angielski (Stany Zjednoczone)        | Mężczyźni   | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, GuyNeural)"       | "pl-US-GuyNeural"       |
| `it-IT` | Włoski (Włochy)     | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ElsaNeural)"      | "IT-ElsaNeural"      |
| `pt-BR` | portugalski (Brazylia) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Chiński (mandarynki, uproszczony)  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> `en-US-JessaNeural` Głos został zmieniony na `en-US-AriaNeural`. Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Ustawienia regionalne | Język | Płeć | Pełne Mapowanie nazw usług | Krótka nazwa |
|--|--|--|--|--|
| <sup>jedno</sup>`ar-EG` | Arabski (Egipt) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (AR-EG, Hoda)" | "AR-EG-Hoda" |
| `ar-SA` | Arabski (Arabia Saudyjska) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bułgarski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (BG-BG, Ivan)" | "BG-BG-Ivan" |
| `ca-ES` | Kataloński | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS" |
| `cs-CZ` | Czeski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (CS-CZ, Jakub)" | "CS-CZ-Jakub" |
| `da-DK` | duński | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Niemiecki (Austria) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Niemiecki (Szwajcaria) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Niemiecki (Niemcy) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | grecki | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (El-GR, Stefanos)" | "El-GR-Stefanos" |
| `en-AU` | Angielski (Australia) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Angielski (Kanada) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Angielski (Zjednoczone Królestwo) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, Susan, Apollo)" | "pl-GB-Susan-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, HazelRUS)" | "pl-GB-HazelRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (en-GB, George, Apollo)" | "pl-GB-George-Apollo" |
| `en-IE` | Angielski (Irlandia) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IE, Janusz)" | "EN-IE-Janusz" |
| `en-IN` | Angielski (Indie) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, Heera, Apollo)" | "pl-IN-Heera-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, PriyaRUS)" | "pl-IN-PriyaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-IN, Ravi, Apollo)" | "pl-IN-Ravi-Apollo" |
| `en-US` | Angielski (Stany Zjednoczone) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, ZiraRUS)" | "pl-US-ZiraRUS" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaRUS)" | "pl-US-AriaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, BenjaminRUS)" | "pl-US-BenjaminRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, Guy24kRUS)" | "pl-US-Guy24kRUS" |
| `es-ES` | hiszpański (Hiszpania) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Hiszpański (Meksyk) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | fiński | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | francuski (Kanada) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francuski (Szwajcaria) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Francuski (Francja) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrajski (Izrael) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (IT-IL, Asaf)" | "IT-IL-Asaf" |
| `hi-IN` | Hindi (Indie) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Kalpana)" | "Witaj w Kalpana" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (Hi-IN, Hemant)" | "Witaj w Hemant" |
| `hr-HR` | Chorwacki | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (HR-HR, Matej)" | "HR-HR-Matej" |
| `hu-HU` | węgierski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonezyjski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (ID-ID, andika)" | "ID-ID-andika" |
| `it-IT` | Włoski | Mężczyźni | "Zamiana tekstu na mowę głosu na mowę serwera firmy Microsoft (Cosimo, Apollo)" | "IT-Cosimo-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (LuciaRUS)" | "IT-LuciaRUS" |
| `ja-JP` | japoński | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | koreański | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (MS-MY, Rizwan)" | "MS-MY-Rizwan" |
| `nb-NO` | Norweski | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS" |
| `nl-NL` | Niderlandzki | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS" |
| `pl-PL` | Polski | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | portugalski (Brazylia) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugalski (Portugalia) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumuński | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (RO-RO, Andrei)" | "RO-RO-Andrei" |
| `ru-RU` | Rosyjski | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (ru-RU, EkaterinaRUS)" | ru — RU — EkaterinaRUS |
| `sk-SK` | Słowacki | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (SK-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Słoweński | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (SL-SI, Lado)" | "SL-SI-Lado" |
| `sv-SE` | szwedzki | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS" |
| `ta-IN` | Tamilski (Indie) | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (ta-IN, Valluvar)" | "Ta-IN-Valluvar" |
| `te-IN` | Telugu (Indie) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (Chitra)" | "te w Chitra" |
| `th-TH` | Tajski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Turecki (Turcja) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (TR-TR, SedaRUS)" | "TR-TR-SedaRUS" |
| `vi-VN` | Wietnamski | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (VI-VN, an)" | "VI-VN-an" |
| `zh-CN` | Chiński (mandarynki, uproszczony) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, YaoYao, Apollo)" | "zh-CN-YaoYao-Apollo" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chiński (kantoński, tradycyjny) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Chiński (tajwański mandarynki) | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Kobiety | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Mężczyźni | "Microsoft Server Speech zamiana tekstu na mowę Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *AR-EG obsługuje nowoczesne standardowe arabski (MSA).*

> [!IMPORTANT]
> `en-US-Jessa` Głos został zmieniony na `en-US-Aria`. Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

> [!TIP]
> Możesz użyć pełnego mapowania nazw usług lub krótkiej nazwy głosu w żądaniach syntezy mowy.

### <a name="customization"></a>Dostosowywanie

Dostosowanie głosu jest dostępne dla `de-DE`, `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT`,,,,,,, i `zh-CN` `pt-BR` Wybierz odpowiednie ustawienia regionalne, które pasują do danych szkoleniowych, które są potrzebne do uczenia niestandardowego modelu głosu. Na przykład jeśli dane dotyczące nagrywania są wymawiane w języku angielskim z akcentem brytyjskim, `en-GB`wybierz opcję.

> [!NOTE]
> Nie obsługujemy szkolenia modelu dwujęzykowego w głosowaniu niestandardowym, z wyjątkiem języka chińskiego w języku angielskim. Wybierz pozycję "dwujęzyczne w języku chińskim English", jeśli chcesz nauczyć się nauczenie języka chińskiego w języku angielskim. Szkolenia głosowe we wszystkich ustawieniach regionalnych zaczynają się od zestawu danych 2000 + wyrażenia długości, z wyjątkiem `en-US` tego `zh-CN` , gdzie można zacząć od dowolnego rozmiaru danych szkoleniowych.

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
| duński                  | `da`          |
| Niderlandzki                   | `nl`          |
| Angielski                 | `en`          |
| Estoński                | `et`          |
| Fijian                  | `fj`          |
| Filipino                | `fil`         |
| fiński                 | `fi`          |
| Francuski                  | `fr`          |
| niemiecki                  | `de`          |
| grecki                   | `el`          |
| Gudżarati                | `gu`          |
| Haitański          | `ht`          |
| Hebrajski                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Kowalski               | `mww`         |
| węgierski               | `hu`          |
| Indonezyjski              | `id`          |
| Irlandzki                   | `ga`          |
| Włoski                 | `it`          |
| japoński                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
| koreański                  | `ko`          |
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
| Polski                  | `pl`          |
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

* [Pobierz subskrypcję wersji próbnej usługi Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
