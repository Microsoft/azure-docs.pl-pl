---
title: Obsługa języka — translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services translator obsługuje używanie następujących języków w przypadku tłumaczenia tekstu na tekst przy użyciu tłumaczenia maszynowego neuronowych (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 359079ad73fcf162fb742afe74c1c1de5896c35c
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996078"
---
# <a name="language-and-region-support-for-translator"></a>Obsługa języków i regionów w usłudze translator

Translator obsługuje tłumaczenie tekstu na tekst przy użyciu następujących języków. Neuronowych Machine Translation (NMT) to nowy standard dla wysokiej jakości tłumaczeń maszyn opartych na formacie AI, który jest dostępny jako domyślny przy użyciu wersji v3 usługi translator, gdy jest dostępny system neuronowych.

[Dowiedz się więcej o tym, jak działa tłumaczenie maszynowe](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tłumaczenie

**Translator w wersji 2**

> [!NOTE]
> Wersja V2 została zaniechana 30 kwietnia 2018. Przeprowadź migrację aplikacji do wersji v3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji 3.

* Tylko statystyczne: dla tego języka nie jest dostępny system neuronowych.
* Neuronowych dostępne: system neuronowych jest dostępny. Użyj parametru, `category=generalnn` Aby uzyskać dostęp do systemu neuronowych.
* Neuronowych domyślne: neuronowych jest domyślnym systemem tłumaczenia. Użyj parametru, `category=smt` Aby uzyskać dostęp do systemu statystycznego do użycia z centrum usługi Microsoft Translator.
* Tylko neuronowych: dostępne jest tylko tłumaczenie neuronowych.

**Translator v3** Translator V3 jest neuronowych domyślnie, a systemy statystyczne są dostępne tylko wtedy, gdy nie istnieje system neuronowych.

> [!NOTE]
> Obecnie podzestaw języków neuronowych jest dostępny w usłudze translator niestandardowym i stopniowo dodajemy dodatkowe. [Języki widoku dostępne obecnie w usłudze translator niestandardowym](#customization).

|Język|    Kod języka|    INTERFEJS API V3|
|:-----|:-----:|:-----|
|Afrikaans|    `af`|    Neuronowych|
|Arabski|    `ar`    |    Neuronowych|
|Języku|    `bn`    |    Neuronowych|
|Bośniacki (łaciński)|    `bs`    |    Neuronowych|
|Bułgarski|    `bg`    |    Neuronowych|
|Kantoński (tradycyjny)|    `yue`|    Statystyczn|
|Kataloński|    `ca`    |    Statystyczn|
|Chiński (uproszczony)|    `zh-Hans`|Neuronowych|
|Chiński (tradycyjny)|    `zh-Hant`        |Neuronowych|
|Chorwacki|    `hr`    |Neuronowych|
|Czeski|    `cs`    |    Neuronowych|
|Duński|    `da`        |Neuronowych|
|Niderlandzki|    `nl`|    Neuronowych|
|Angielski|    `en`    |    Neuronowych|
|Estoński|    `et`    |    Neuronowych|
|Fijian|    `fj`    |    Statystyczn|
|Filipino|    `fil`    |    Statystyczn|
|Fiński|    `fi`    |    Neuronowych|
|Francuski|    `fr`    |    Neuronowych|
|Niemiecki|    `de`    |    Neuronowych|
|Grecki|    `el`    |    Neuronowych|
|Gudżarati|    `gu`    |    Neuronowych|
|Haitański|    `ht`        |Statystyczn|
|Hebrajski    |`he`    |Neuronowych
|Hindi|    `hi`    |    Neuronowych|
|Hmong Kowalski|    `mww`    |    Statystyczn|
|Węgierski|    `hu`    |    Neuronowych|
|Islandzki|    `is`    |    Neuronowych|
|Indonezyjski|    `id`    |    Statystyczn|
|Irlandzki | `ga`| Neuronowych
|Włoski|    `it`    |    Neuronowych|
|Japoński|    `ja`    |    Neuronowych|
|Kannada|`kn`| Neuronowych
|Kiswahili|    `sw`    |    Statystyczn|
|Klingon|    `tlh`    |    Statystyczn|
|Klingon (plqaD)|    `tlh-Qaak`    |    Statystyczn|
|Koreański    |`ko`    |    Neuronowych|
|Łotewski|    `lv`    |    Neuronowych|
|Litewski|    `lt`    |    Neuronowych|
|Madagaskaru|    `mg`    |    Statystyczn|
|Malajski|    `ms`        |Statystyczn|
|Malayalam| `ml` | Neuronowych
|Maltański|    `mt`    |    Statystyczn|
|Maoryjski| `mi`  | Neuronowych|
|Marathi| `mr`  | Neuronowych|
|Norweski|    `nb`    |    Neuronowych|
|Perski|    `fa`    |    Neuronowych|
|Polski|    `pl`    |    Neuronowych|
|Portugalski (Brazylia)|    `pt-br`    |    Neuronowych|
|Portugalski (Portugalia)| `pt-pt` | Neuronowych
|Pendżabski|`pa`|Neuronowych
|Queretaro Otomi|    `otq`    |    Statystyczn|
|Rumuński|    `ro`    |    Neuronowych|
|Rosyjski|    `ru`    |    Neuronowych|
|Samoan|    `sm`    |    Statystyczn|
|Serbski (cyrylica)|    `sr-Cyrl`|    Statystyczn|
|Serbski (łaciński)|    `sr-Latn`        |Statystyczn|
|Słowacki|    `sk`    |    Neuronowych|
|Słoweński|    `sl`    |    Neuronowych|
|Hiszpański|    `es`    |    Neuronowych|
|Szwedzki|    `sv`    |Neuronowych|
|Tahitian|    `ty`    |Statystyczn|
|Tamilski|    `ta`    |    Neuronowych|
|Telugu|    `te`    |    Neuronowych|
|Tajlandzki|    `th`    |    Neuronowych|
|Tonga|    `to`    |    Statystyczn|
|Turecki|    `tr`        |Neuronowych|
|Ukraiński|    `uk`    |    Neuronowych|
|Urdu|    `ur`    |    Statystyczn|
|Wietnamski|    `vi`    |    Neuronowych|
|Walijski|    `cy`    |    Neuronowych|
|Yucatec Maya|    `yua`    |    Statystyczn|

> [!NOTE]
> Kod języka `pt` będzie wartością domyślną `pt-br` , portugalski (Brazylia).

## <a name="transliteration"></a>Transliteracji

Metoda transliteracji obsługuje następujące języki. W polu "do/z" <--> "wskazuje, że język można zatransliteracjć z lub do jednego z wymienionych skryptów. "-->" wskazuje, że język może być zatransliteracji tylko z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do/z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | Arabski`Arab` | <--> | Wielka`Latn` |
|Języku  | `bn` | Bengalski`Beng` | <--> | Wielka`Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Wielka`Latn` |
| Chiński (uproszczony) | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Chiński tradycyjny`Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Wielka`Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Chiński uproszczony`Hans` |
| Gudżarati | `gu`  | Gudżarati`Gujr` | --> | Wielka`Latn` |
| Hebrajski | `he` | Hebrajski`Hebr` | <--> | Wielka`Latn` |
| Hindi | `hi` | Devanagari`Deva` | <--> | Wielka`Latn` |
| Japoński | `ja` | Japoński`Jpan` | <--> | Wielka`Latn` |
| Kannada | `kn` | Kannada`Knda` | --> | Wielka`Latn` |
| Malayalam | `ml` | Malayalam`Mlym` | --> | Wielka`Latn` |
| Marathi | `mr` | Devanagari`Deva` | --> | Wielka`Latn` |
| Orija | `or` | Orija`Orya` | <--> | Wielka`Latn` |
| Pendżabski | `pa` | Gurmukhi`Guru`  | <--> | Wielka`Latn`  |
| Serbski (cyrylica) | `sr-Cyrl` | Pisanych`Cyrl`  | --> | Wielka`Latn` |
| Serbski (łaciński) | `sr-Latn` | Wielka`Latn` | --> | Pisanych`Cyrl`|
| Tamilski | `ta` | Tamilski`Taml` | --> | Wielka`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Wielka`Latn` |
| Tajlandzki | `th` | Tajski`Thai` | --> | Wielka`Latn` |

## <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki w języku angielskim lub z niego przy użyciu metod wyszukiwania i przykładów.

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
| Japoński      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski (Brazylia)     | `pt-br`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tamilski      | `ta`          |
| Tajlandzki      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |

## <a name="detect"></a>Wykrywanie

Translator wykrywa wszystkie języki dostępne do tłumaczenia i przesunięcia.


## <a name="access-the-translator-language-list-programmatically"></a>Programowe uzyskiwanie dostępu do listy języka usługi translator

Możesz pobrać listę obsługiwanych języków dla translatora w wersji 3.0 przy użyciu metody Languages. Możesz wyświetlić listę według funkcji, kod języka, a także nazwę języka w języku angielskim lub innym obsługiwanym języku. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator w miarę udostępniania nowych języków.

[Dokumentacja dotycząca operacji wyświetlania języków](reference/v3-0-languages.md)

## <a name="customization"></a>Dostosowywanie

Następujące języki są dostępne do dostosowania do języka angielskiego lub z niego w języku angielskim przy użyciu [translatora niestandardowego](https://aka.ms/CustomTranslator).

| Język    | Kod języka |
|:----------- |:-------------:|
| Arabski       | `ar`          |
| Języku      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Chiński (uproszczony)      | `zh-Hans`          |
|Chiński (tradycyjny)|    `zh-Hant`    |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| Duński      | `da`          |
| Niderlandzki      | `nl`          |
| Angielski    | `en`     |
| Estoński      | `et`          |
| Fiński      | `fi`          |
| Francuski      | `fr`          |
| Niemiecki      | `de`          |
| Grecki      | `el`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|    `id`    |
| Irlandzki | `ga`    |
| Włoski      | `it`          |
| Japoński      | `ja`          |
| Kiswahili|    `sw`    |
| Koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Madagaskaru|    `mg`    |
| Maoryjski| `mi`  |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| Portugalski (Brazylia) | `pt-br` |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Samoan|    `sm`    |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| Szwedzki      | `sv`          |
| Tajlandzki      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Wietnamski      | `vi`          |
| Walijski | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Dostęp do listy w witrynie sieci Web usługi Microsoft Translator

Aby uzyskać szybki przegląd języków, w witrynie sieci Web usługi Microsoft Translator są wyświetlane wszystkie języki obsługiwane przez translator i interfejsy API rozpoznawania mowy. Ta lista nie zawiera informacji specyficznych dla deweloperów, takich jak kody języka.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
