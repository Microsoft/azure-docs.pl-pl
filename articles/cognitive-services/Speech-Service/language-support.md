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
ms.openlocfilehash: 437dc18dc16e879e95ff4ec7c1a9ab7ec3f17bef
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266002"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Obsługa języka i głosu dla usługi mowy

Obsługa języka zależy od funkcjonalności usługi mowy. W poniższych tabelach przedstawiono obsługę języka dla ofert [zamiany mowy na tekst](#speech-to-text), zamiany [tekstu na mowę](#text-to-speech)i usługi [tłumaczenia mowy](#speech-translation) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zarówno zestaw Microsoft Speech SDK, jak i interfejs API REST obsługują następujące języki (ustawienia regionalne). 

W celu poprawienia dokładności, dostosowanie jest oferowane dla podzestawu języków za pomocą przekazywania **zapisu audio + z etykietami ludzkimi** lub **powiązanego tekstu: zdania**. Aby dowiedzieć się więcej o dostosowywaniu, zobacz Wprowadzenie [do Custom Speech](how-to-custom-speech.md).

Aby uzyskać więcej informacji o tym, jak można ulepszyć wymowę, zobacz [ulepszanie modelu dla Custom Speech](how-to-custom-speech-improve-accuracy.md#add-new-words-with-pronunciation).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Regionalne  | Język                          | Obsługiwane | Dostosowania                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arabski (Zjednoczone Emiraty Arabskie)                      | Yes       | Nie                                                |
| `ar-BH` | Arabski (Bahrajn), nowoczesny Standard | Yes       | Model językowy                                    |
| `ar-EG` | Arabski (Egipt)                    | Yes       | Model językowy                                    |
| `ar-IL` | Arabski (Izrael)                   | Yes       | Nie                                                |
| `ar-KW` | Arabski (Kuwejt)                   | Yes       | Nie                                                |
| `ar-PS` | Arabski (palestyński)                | Yes       | Nie                                                |
| `ar-QA` | Arabski (katar)                    | Yes       | Nie                                                |
| `ar-SA` | Arabski (Arabia Saudyjska)             | Yes       | Nie                                                |
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
| `pt-BR` | Portugalski (Brazylia)               | Yes       | Model akustyczny<br>Model językowy<br>Fonetyczn |
| `pt-PT` | Portugalski (Portugalia)             | Yes       | Model językowy                                    |
| `ru-RU` | Rosyjski (Rosja)                  | Yes       | Model akustyczny<br>Model językowy                  |
| `sv-SE` | Szwedzki (Szwecja)                  | Yes       | Model językowy                                    |
| `ta-IN` | Tamilski (Indie)                     | Yes       | Model językowy                                    |
| `te-IN` | Telugu (Indie)                    | Yes       | Nie                                                |
| `th-TH` | Tajski (Tajlandia)                   | Yes       | Nie                                                |
| `tr-TR` | Turecki (Turcja)                  | Yes       | Model językowy                                    |
| `zh-CN` | Chiński (mandarynki, uproszczony)    | Yes       | Model akustyczny<br>Model językowy                  |
| `zh-HK` | Chiński (kantoński, tradycyjny)  | Yes       | Model językowy                                    |
| `zh-TW` | Chiński (tajwański mandarynki)      | Yes       | Model językowy                                    |

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
| `de-DE` | Niemiecki (Niemcy)                | `Female` | "de-DE-KatjaNeural"      | Ogólne |
| `en-AU` | Angielski (Australia)             | `Female` | "en-AU-NatashaNeural"    | Ogólne |
| `en-CA` | Angielski (Kanada)                | `Female` | "en-CA-ClaraNeural"      | Ogólne |
| `en-GB` | Angielski (Zjednoczone Królestwo)                    | `Female` | "pl-GB-LibbyNeural"      | Ogólne |
|         |                                 | `Female` | "pl-GB-MiaNeural"        | Ogólne |
| `en-US` | Angielski (Stany Zjednoczone)                    | `Female` | "pl-US-AriaNeural"       | Ogólne, dostępne są wiele stylów głosowych |
|         |                                 | `Male`   | "pl-US-GuyNeural"        | Ogólne |
| `es-ES` | hiszpański (Hiszpania)                 | `Female` | "es-ES-ElviraNeural"     | Ogólne |
| `es-MX` | Hiszpański (Meksyk)                | `Female` | "es-MX-DaliaNeural"      | Ogólne |
| `fr-CA` | francuski (Kanada)                 | `Female` | "fr-CA-SylvieNeural"     | Ogólne |
| `fr-FR` | Francuski (Francja)                 | `Female` | "fr-FR-DeniseNeural"     | Ogólne |
| `it-IT` | Włoski (Włochy)                 | `Female` | "IT-ElsaNeural"       | Ogólne |
| `ja-JP` | Japoński                        | `Female` | "ja-JP-NanamiNeural"     | Ogólne |
| `ko-KR` | Koreański                          | `Female` | "ko-KR-SunHiNeural"      | Ogólne |
| `nb-NO` | Norweski                       | `Female` | "NB-NO-IselinNeural"     | Ogólne |
| `pt-BR` | Portugalski (Brazylia)             | `Female` | "pt-BR-FranciscaNeural"  | Ogólne |
| `tr-TR` | Turecki                         | `Female` | "TR-TR-EmelNeural"       | Ogólne |
| `zh-CN` | Chiński (mandarynki, uproszczony)  | `Female` | "zh-CN-XiaoxiaoNeural"   | Ogólne, dostępne są wiele stylów głosowych |
|         |                                 | `Female` | "zh-CN-XiaoyouNeural"    | Głos dla dzieci, zoptymalizowany pod kątem narracji w wątku |
|         |                                 | `Male`   | "zh-CN-YunyangNeural"    | Zoptymalizowane pod kątem czytania wiadomości, dostępne są wiele stylów głosowych |
|         |                                 | `Male`   | "zh-CN-YunyeNeural"      | Optymalizacja pod kątem narracji w wątku |

> [!IMPORTANT]
> `en-US-JessaNeural`Głos został zmieniony na `en-US-AriaNeural` . Jeśli wcześniej używasz "Jessa", przekonwertuj wartość na "Aria".

Aby dowiedzieć się, jak można konfigurować i dostosowywać neuronowych głosów, zobacz Language [syntezowania znaczników](speech-synthesis-markup.md#adjust-speaking-styles).

> [!TIP]
> Można nadal używać pełnego mapowania nazw usług, takiego jak "Microsoft Server Speech zamiana tekstu na mowę Voice (EN-US, AriaNeural)" w żądaniach syntezy mowy.

### <a name="standard-voices"></a>Głosy standardowe

Ponad 75 standardowych głosów jest dostępnych w ponad 45 językach i ustawieniach regionalnych, które umożliwiają konwertowanie tekstu na mowę. Aby uzyskać więcej informacji na temat dostępności regionalnej, zobacz [regiony](regions.md#standard-and-neural-voices).

| Regionalne | Język | Płeć | Nazwa głosu |
|--|--|--|--|
| <sup>jedno</sup>`ar-EG` | Arabski (Egipt) | `Female` | "AR-EG-Hoda" |
| `ar-SA` | Arabski (Arabia Saudyjska) | `Male` | "ar-SA-Naayf" |
| `bg-BG` | Bułgarski | `Male` |  "BG-BG-Ivan" |
| `ca-ES` | Kataloński | `Female` |  "CA-ES-HerenaRUS" |
| `cs-CZ` | Czeski | `Male` | "CS-CZ-Jakub" |
| `da-DK` | Duński | `Female` |  "da-DK-HelleRUS" |
| `de-AT` | Niemiecki (Austria) | `Male` | "de-AT-Michael" |
| `de-CH` | Niemiecki (Szwajcaria) | `Male` |  "de-CH-Karsten" |
| `de-DE` | Niemiecki (Niemcy) | `Female` |  "de-DE-Hedda" |
|  |  | `Female` | "de-DE-HeddaRUS" |
|  |  | `Male` |  "de-DE-Stefan-Apollo" |
| `el-GR` | Grecki | `Male` | "El-GR-Stefanos" |
| `en-AU` | Angielski (Australia) | `Female` |  "en-AU-Catherine" |
|  |  | `Female` |  "en-AU-HayleyRUS" |
| `en-CA` | Angielski (Kanada) | `Female` |  "en-CA-Linda" |
|  |  | `Female` |  "en-CA-HeatherRUS" |
| `en-GB` | Angielski (Zjednoczone Królestwo) | `Female` |  "pl-GB-Susan-Apollo" |
|  |  | `Female` |  "pl-GB-HazelRUS" |
|  |  | `Male` |  "pl-GB-George-Apollo" |
| `en-IE` | Angielski (Irlandia) | `Male` | "EN-IE-Janusz" |
| `en-IN` | Angielski (Indie) | `Female` | "pl-IN-Heera-Apollo" |
|  |  | `Female` |  "pl-IN-PriyaRUS" |
|  |  | `Male` |  "pl-IN-Ravi-Apollo" |
| `en-US` | Angielski (Stany Zjednoczone) | `Female` |  "pl-US-ZiraRUS" |
|  |  | `Female` | "pl-US-AriaRUS" |
|  |  | `Male` | "pl-US-BenjaminRUS" |
|  |  | `Male` |  "pl-US-Guy24kRUS" |
| `es-ES` | hiszpański (Hiszpania) | `Female` |  "es-ES-Laura-Apollo" |
|  |  | `Female` | "es-ES-HelenaRUS" |
|  |  | `Male` | "es-ES-Pablo-Apollo" |
| `es-MX` | Hiszpański (Meksyk) | `Female` |  "es-MX-HildaRUS" |
|  |  | `Male` | "es-MX-Raul-Apollo" |
| `fi-FI` | Fiński | `Female` | "fi-FI-HeidiRUS" |
| `fr-CA` | francuski (Kanada) | `Female` | "fr-CA-Caroline" |
|  |  | `Female` | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francuski (Szwajcaria) | `Male` | "fr-CH-Guillaume" |
| `fr-FR` | Francuski (Francja) | `Female` |  "fr-FR-Julie-Apollo" |
|  |  | `Female` |"fr-FR-HortenseRUS" |
|  |  | `Male` |  "fr-FR-Paul-Apollo" |
| `he-IL` | Hebrajski (Izrael) | `Male` |  "IT-IL-Asaf" |
| `hi-IN` | Hindi (Indie) | `Female` | "Hi-IN-Kalpana-Apollo" |
|  |  | `Female` |  "Witaj w Kalpana" |
|  |  | `Male` |  "Witaj w Hemant" |
| `hr-HR` | Chorwacki | `Male` | "HR-HR-Matej" |
| `hu-HU` | Węgierski | `Male` |  "hu-HU-Szabolcs" |
| `id-ID` | Indonezyjski | `Male` | "ID-ID-andika" |
| `it-IT` | Włoski | `Male` |  "IT-Cosimo-Apollo" |
|  |  | `Female` |  "IT-LuciaRUS" |
| `ja-JP` | Japoński | `Female` |  "ja-JP-Ayumi-Apollo" |
|  |  | `Male` | "ja-JP-Ichiro-Apollo" |
|  |  | `Female` |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreański | `Female` | "ko-KR-HeamiRUS" |
| `ms-MY` | Malajski | `Male` |  "MS-MY-Rizwan" |
| `nb-NO` | Norweski | `Female` |  "NB-NO-HuldaRUS" |
| `nl-NL` | Niderlandzki | `Female` |  "NL-NL-HannaRUS" |
| `pl-PL` | Polski | `Female` |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugalski (Brazylia) | `Female` | "pt-BR-HeloisaRUS" |
|  |  | `Male` |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugalski (Portugalia) | `Female` | "pt-PT-HeliaRUS" |
| `ro-RO` | Rumuński | `Male` | "RO-RO-Andrei" |
| `ru-RU` | Rosyjski | `Female` |  "ru-RU-Irina-Apollo" |
|  |  | `Male` | "ru-RU-Pavel-Apollo" |
|  |  | `Female` |  ru — RU — EkaterinaRUS |
| `sk-SK` | Słowacki | `Male` | "sk-SK-Filip" |
| `sl-SI` | Słoweński | `Male` |  "SL-SI-Lado" |
| `sv-SE` | Szwedzki | `Female` | "SV-SE-HedvigRUS" |
| `ta-IN` | Tamilski (Indie) | `Male` |  "Ta-IN-Valluvar" |
| `te-IN` | Telugu (Indie) | `Female` |  "te w Chitra" |
| `th-TH` | Tajlandzki | `Male` |  "th-TH-Pattara" |
| `tr-TR` | Turecki (Turcja) | `Female` | "TR-TR-SedaRUS" |
| `vi-VN` | Wietnamski | `Male` |  "VI-VN-an" |
| `zh-CN` | Chiński (mandarynki, uproszczony) | `Female` |  "zh-CN-HuihuiRUS" |
|  |  | `Female` | "zh-CN-YaoYao-Apollo" |
|  |  | `Male` | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Chiński (kantoński, tradycyjny) | `Female` |  "zh-HK-Tracy-Apollo" |
|  |  | `Female` | "zh-HK-TracyRUS" |
|  |  | `Male` |  "zh-HK-Danny-Apollo" |
| `zh-TW` | Chiński (tajwański mandarynki) | `Female` |  "zh-TW-Yating-Apollo" |
|  |  | `Female` | "zh-TW-HanHanRUS" |
|  |  | `Male` |  "zh-TW-Zhiwei-Apollo" |

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
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
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
| Polski                  | `pl`          |
| Portugalski (Brazylia)     | `pt-br`       |
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

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję wersji próbnej usługi Speech Service](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
