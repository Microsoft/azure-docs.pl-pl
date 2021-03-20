---
title: Obsługa języka — translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services translator obsługuje używanie następujących języków w przypadku tłumaczenia tekstu na tekst przy użyciu tłumaczenia maszynowego neuronowych (NMT).
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: b114c4f3e6646f68e7a1068be8dd52778a26fb58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738144"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Obsługa języków i tekstu oraz Tłumaczenie mowy

Użyj usługi translator, aby przetłumaczyć do i z dowolnego z języków i dialektów tłumaczenia tekstu 90. Neuronowych Machine Translation (NMT) to nowy standard dla wysokiej jakości tłumaczeń maszyn opartych na formacie AI, który jest dostępny jako domyślny przy użyciu wersji v3 usługi translator, gdy jest dostępny system neuronowych.

Usługi translator można także używać w połączeniu z tłumaczeniem niestandardowym w celu kompilowania systemów tłumaczenia neuronowych, które znają terminologię używaną we własnej firmie i branży, a także z usługą Microsoft Speech Service, aby dodać tłumaczenie mowy do aplikacji.

[Dowiedz się więcej o tym, jak działa tłumaczenie maszynowe](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Tłumaczenie tekstu
Tłumaczenie tekstu jest dostępne przy użyciu operacji tłumaczenia na lub z dowolnego języka dostępnego w usłudze translator. Interfejs API oferuje również wykrywanie języka przy użyciu operacji wykrywania, przeznaczania przy użyciu operacji transliteracji i słowników dwujęzycznych, korzystając z operacji wyszukiwania słownika i przykładów słownika. Dostępne języki dla każdej z tych operacji są wymienione poniżej. 

### <a name="translate"></a>Tłumaczenie

Translator obsługuje tłumaczenie tekstu na tekst przy użyciu następujących języków. 

[Wyświetl dokumentację referencyjną operacji tłumaczenia](reference/v3-0-translate.md)

| Język | Kod języka |
|:-|:-:|
| Afrikaans | `af` |
| Albański | `sq` |
| Amharski | `am` |
| Arabski | `ar` |
| Armeński | `hy` |
| Assamski | `as` |
| Azerbejdżański | `az` |
| Języku | `bn` |
| Bośniacki (łaciński) | `bs` |
| Bułgarski | `bg` |
| Kantoński (tradycyjny) | `yue` |
| Kataloński | `ca` |
| Chiński (uproszczony) | `zh-Hans` |
| Chiński (tradycyjny) | `zh-Hant` |
| Chorwacki | `hr` |
| Czeski | `cs` |
| Dari | `prs` |
| Duński | `da` |
| Niderlandzki | `nl` |
| Angielski | `en` |
| Estoński | `et` |
| Fijian | `fj` |
| Filipino | `fil` |
| Fiński | `fi` |
| Francuski | `fr` |
| francuski (Kanada) | `fr-ca` |
| Niemiecki | `de` |
| Grecki | `el` |
| Gudżarati | `gu` |
| Haitański | `ht` |
| Hebrajski | `he` |
| Hindi | `hi` |
| Hmong Kowalski | `mww` |
| Węgierski | `hu` |
| Islandzki | `is` |
| Indonezyjski | `id` |
| Inuktitut | `iu` |
| Irlandzki | `ga` |
| Włoski | `it` |
| japoński | `ja` |
| Kannada | `kn` |
| Kazachski | `kk` |
| Khmerski | `km` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Koreański | `ko` |
| Kurdyjski (środkowe) | `ku` |
| Kurdyjski (północny) | `kmr` |
| - | `lo` |
| Łotewski | `lv` |
| Litewski | `lt` |
| Madagaskaru | `mg` |
| Malajski | `ms` |
| Malayalam | `ml` |
| Maltański | `mt` |
| Maoryjski | `mi` |
| Marathi | `mr` |
| Myanmar | `my` |
| Nepalski | `ne` |
| Norweski | `nb` |
| Odia | `or` |
| Paszto | `ps` |
| Perski | `fa` |
| polski | `pl` |
| portugalski (Brazylia) | `pt` |
| Portugalski (Portugalia) | `pt-pt` |
| Pendżabski | `pa` |
| Queretaro Otomi | `otq` |
| Rumuński | `ro` |
| Rosyjski | `ru` |
| Samoan | `sm` |
| Serbski (cyrylica) | `sr-Cyrl` |
| Serbski (łaciński) | `sr-Latn` |
| Słowacki | `sk` |
| Słoweński | `sl` |
| Hiszpański | `es` |
| Suahili | `sw` |
| Szwedzki | `sv` |
| Tahitian | `ty` |
| Tamilski | `ta` |
| Telugu | `te` |
| Tajlandzki | `th` |
| Tigrinia | `ti` |
| Tonga | `to` |
| Turecki | `tr` |
| Ukraiński | `uk` |
| Urdu | `ur` |
| Wietnamski | `vi` |
| Walijski | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> Kod języka `pt` będzie wartością domyślną `pt-br` , portugalski (Brazylia).

### <a name="detect"></a>Wykrywanie

Translator wykrywa następujące języki do tłumaczenia i przekształcenie.

[Dokumentacja dotycząca operacji wykrywania widoku](reference/v3-0-detect.md)

| Język | Kod języka |
|:-|:-:|
| Afrikaans | `af` |
| Albański | `sq` |
| Arabski | `ar` |
| Bułgarski | `bg` |
| Kataloński | `ca` |
| Chiński (uproszczony) | `zh-Hans` |
| Chiński (tradycyjny) | `zh-Hant` |
| Chorwacki | `hr` |
| Czeski | `cs` |
| Duński | `da` |
| Niderlandzki | `nl` |
| Angielski | `en` |
| Estoński | `et` |
| Fiński | `fi` |
| Francuski | `fr` |
| Niemiecki | `de` |
| Grecki | `el` |
| Gudżarati | `gu` |
| Haitański | `ht` |
| Hebrajski | `he` |
| Hindi | `hi` |
| Węgierski | `hu` |
| Islandzki | `is` |
| Indonezyjski | `id` |
| Inuktitut | `iu` |
| Irlandzki | `ga` |
| Włoski | `it` |
| japoński | `ja` |
| Klingon | `tlh-Latn` |
| Koreański | `ko` |
| Kurdyjski (środkowe) | `ku-Arab` |
| Łotewski | `lv` |
| Litewski | `lt` |
| Malajski | `ms` |
| Maltański | `mt` |
| Norweski | `nb` |
| Paszto | `ps` |
| Perski | `fa` |
| Polski | `pl` |
| Portugalski | `pt` |
| Rumuński | `ro` |
| Rosyjski | `ru` |
| Serbski (cyrylica) | `sr-Cyrl` |
| Serbski (łaciński) | `sr-Latn` |
| Słowacki | `sk` |
| Słoweński | `sl` |
| Hiszpański | `es` |
| Suahili | `sw` |
| Szwedzki | `sv` |
| Tahitian | `ty` |
| Tajlandzki | `th` |
| Turecki | `tr` |
| Ukraiński | `uk` |
| Urdu | `ur` |
| Wietnamski | `vi` |
| Walijski | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Transliteracja

Metoda transliteracji obsługuje następujące języki. W polu "do/z" <--> "wskazuje, że język można zatransliteracjć z lub do jednego z wymienionych skryptów. "-->" wskazuje, że język może być zatransliteracji tylko z jednego skryptu do drugiego.

[Wyświetl dokumentację dotyczącą operacji transliteracji](reference/v3-0-translate.md)


| Język    | Kod języka | Skrypt | Do/z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | Arabski `Arab` | <--> | Wielka `Latn` |
| Assamski | `as` | Bengalski `Beng` | <--> | Wielka `Latn` |
| Języku  | `bn` | Bengalski `Beng` | <--> | Wielka `Latn` |
|Białoruski| `be` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
|Bułgarski| `bg` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Wielka `Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony `Hans`| <--> | Chiński tradycyjny `Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Wielka `Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny `Hant`| <--> | Chiński uproszczony `Hans` |
|Grecki| `el` | Grecki `Grek`  | <--> | Wielka `Latn` |
| Gudżarati | `gu`  | Gudżarati `Gujr` | <--> | Wielka `Latn` |
| Hebrajski | `he` | Hebrajski `Hebr` | <--> | Wielka `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Wielka `Latn` |
| japoński | `ja` | Japoński `Jpan` | <--> | Wielka `Latn` |
| Kannada | `kn` | Kannada `Knda` | <--> | Wielka `Latn` |
|Kazachski| `kk` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
|Koreański| `ko` | Koreański `Kore`  | <--> | Wielka `Latn` |
|Kirgiski| `ky` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
|Macedonii| `mk` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Malayalam | `ml` | Malayalam `Mlym` | <--> | Wielka `Latn` |
| Marathi | `mr` | Devanagari `Deva` | <--> | Wielka `Latn` |
|Mongolski| `mn` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Odia | `or` | Orija `Orya` | <--> | Wielka `Latn` |
|Perski| `fa` | Arabski `Arab`  | <--> | Wielka `Latn` |
| Pendżabski | `pa` | Gurmukhi `Guru`  | <--> | Wielka `Latn`  |
|Rosyjski| `ru` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Serbski (cyrylica) | `sr-Cyrl` | Pisanych `Cyrl`  | --> | Wielka `Latn` |
| Serbski (łaciński) | `sr-Latn` | Wielka `Latn` | --> | Pisanych `Cyrl`|
|Sindhi| `sd` | Arabski `Arab`  | <--> | Wielka `Latn` |
|Syngaleski| `si` | Syngaleski `Sinh`  | <--> | Wielka `Latn` |
|Tadżycki| `tg` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Tamilski | `ta` | Tamilski `Taml` | <--> | Wielka `Latn` |
|Tatarski| `tt` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
| Telugu | `te` | Telugu `Telu` | <--> | Wielka `Latn` |
| Tajlandzki | `th` | Tajski `Thai` | --> | Wielka `Latn` |
|Ukraiński| `uk` | Pisanych `Cyrl`  | <--> | Wielka `Latn` |
|Urdu| `ur` | Arabski `Arab`  | <--> | Wielka `Latn` |

### <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki w języku angielskim lub z niego przy użyciu metod wyszukiwania i przykładów.

Wyświetl dokumentację referencyjną dla operacji [wyszukiwania słownika](reference/v3-0-dictionary-lookup.md) i [przykładów słownika](reference/v3-0-dictionary-examples.md) .

| Język    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Języku      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Kataloński      | `ca`          |
| Chiński (uproszczony)      | `zh-Hans`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Niderlandzki      | `nl`          |
| Estoński      | `et`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Kowalski      | `mww`          |
| Węgierski      | `hu`          |
| Islandzki    | `is`  |
| Indonezyjski      | `id`          |
| Włoski      | `it`          |
| japoński      | `ja`          |
| Klingon      | `tlh`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| polski      | `pl`          |
| portugalski (Brazylia)     | `pt`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Suahili      | `sw`          |
| Szwedzki      | `sv`          |
| Tamilski      | `ta`          |
| Tajlandzki      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Programowe uzyskiwanie dostępu do listy języka usługi translator

Listę obsługiwanych języków dla translatora można pobrać przy użyciu metody języka. Możesz wyświetlić listę według funkcji, kod języka, a także nazwę języka w języku angielskim lub innym obsługiwanym języku. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator w miarę udostępniania nowych języków.

[Dokumentacja dotycząca operacji wyświetlania języków](reference/v3-0-languages.md)

## <a name="customization"></a>Dostosowywanie

Następujące języki są dostępne do dostosowania do języka angielskiego lub z niego w języku angielskim przy użyciu [translatora niestandardowego](https://aka.ms/CustomTranslator).

| Język    | Kod języka |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Arabski       | `ar`          |
| Języku      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
|Kataloński|   `ca`    |
| Chiński (uproszczony)      | `zh-Hans`          |
|Chiński (tradycyjny)|   `zh-Hant`   |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Niderlandzki      | `nl`          |
| Angielski    | `en`     |
| Estoński      | `et`          |
|Fijian|    `fj`    |
|Filipino|  `fil`   |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Gudżarati| `gu`    |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|   `id`    |
| Irlandzki | `ga`  |
| Włoski      | `it`          |
| japoński      | `ja`          |
|Kannada|`kn`|
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Madagaskaru| `mg`    |
| Malajski|    `ms` |
|Maltański|   `mt`    |
| Maoryjski| `mi`  |
| Marathi| `mr`  |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| polski      | `pl`          |
| portugalski (Brazylia) | `pt` |
| Pendżabski|`pa`|
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Samoan|   `sm`    |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Suahili|  `sw`    |
| Szwedzki      | `sv`          |
|Tahitian|  `ty`    |
| Tajlandzki      | `th`          |
|Tonga|    `to`    |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu| `ur`    |
| Wietnamski      | `vi`          |
| Walijski | `cy` |

## <a name="speech-translation"></a>Tłumaczenie mowy
Tłumaczenie mowy jest dostępne za pomocą usługi Translator z Cognitive Services mowy. Zobacz [dokumentację usługi mowy](../speech-service/index.yml) , aby dowiedzieć się więcej o korzystaniu z tłumaczenia mowy i wyświetlić wszystkie [dostępne opcje języka](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Zamiana mowy na tekst
Konwertuj mowę na tekst, aby przetłumaczyć na wybrany język tekstu. Funkcja zamiany mowy na tekst umożliwia tłumaczenie mowy na tekst lub tłumaczenie mowy na mowę w przypadku użycia w połączeniu z syntezą mowy.

| Język    |
|:----------- |
|Arabski|
|Kantoński (tradycyjny)|
|Kataloński|
|Chiński (uproszczony)|
|Chiński (tradycyjny)|
|Duński|
|Niderlandzki|
|Angielski|
|Fiński|
|Francuski|
|francuski (Kanada)|
|Niemiecki|
|Gudżarati|
|Hindi|
|Włoski|
|japoński|
|Koreański|
|Marathi|
|Norweski|
|polski|
|portugalski (Brazylia)|
|Portugalski (Portugalia)|
|Rosyjski|
|Hiszpański|
|Szwedzki|
|Tamilski|
|Telugu|
|Tajlandzki|
|Turecki|

### <a name="text-to-speech"></a>Zamiana tekstu na mowę
Konwertuj tekst na mowę. Funkcja zamiany tekstu na mowę służy do dodawania dźwiękowych danych wyjściowych z wynikami tłumaczenia lub tłumaczenia zamiany mowy na mowę w przypadku użycia z funkcją zamiany mowy na tekst. 

| Język |
|:-|
| Arabski |
| Bułgarski |
| Kantoński (tradycyjny) |
| Kataloński |
| Chiński (uproszczony) |
| Chiński (tradycyjny) |
| Chorwacki |
| Czeski |
| Duński |
| Niderlandzki |
| Angielski |
| Fiński |
| Francuski |
| francuski (Kanada) |
| Niemiecki |
| Grecki |
| Hebrajski |
| Hindi |
| Węgierski |
| Indonezyjski |
| Włoski |
| japoński |
| Koreański |
| Malajski |
| Norweski |
| polski |
| portugalski (Brazylia) |
| Portugalski (Portugalia) |
| Rumuński |
| Rosyjski |
| Słowacki |
| Słoweński |
| Hiszpański |
| Szwedzki |
| Tamilski |
| Telugu |
| Tajlandzki |
| Turecki |
| Wietnamski |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>Wyświetlanie listy języków w witrynie sieci Web usługi Microsoft Translator

Aby uzyskać szybki przegląd języków, w witrynie sieci Web usługi Microsoft Translator są wyświetlane wszystkie języki obsługiwane przez usługę Translator dla tłumaczenia tekstu i usługi mowy na potrzeby tłumaczenia mowy. Ta lista nie zawiera informacji specyficznych dla deweloperów, takich jak kody języka.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
