---
title: Obsługa języków — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Interfejs API tekstu tłumacza obsługuje następujące języki dla tłumaczenia tekstu na tekst przy użyciu tłumaczenia maszynowego neuronowego (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 0ecde5acb7dc57ed9e5802c1589d5813a9206643
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684824"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Obsługa języka i regionu interfejsu API tekstu tłumacza

Interfejs API tekstu tłumacza obsługuje następujące języki do tłumaczenia tekstu na tekst. Neural Machine Translation (NMT) to nowy standard dla wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji i jest dostępny jako domyślny przy użyciu V3 interfejsu API tekstu tłumacza, gdy dostępny jest system neuronowy.

[Dowiedz się więcej o tym, jak działa tłumaczenie maszynowe](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Tłumaczenie

**Interfejs API translatora w wersji 2**

> [!NOTE]
> Wersja V2 została wycofana 30 kwietnia 2018 roku. Zmigruj aplikacje do wersji 3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji V3.

* Tylko statystyki: Dla tego języka nie jest dostępny żaden system neuronowy.
* Neural dostępne: System neuronowy jest dostępny. Użyj parametru, `category=generalnn` aby uzyskać dostęp do systemu nerwowego.
* Domyślnie neuronowe: Neuronowe jest domyślnym systemem translacji. Użyj tego `category=smt` parametru, aby uzyskać dostęp do systemu statystycznego do użytku z Centrum Microsoft Translator.
* Tylko neuronalne: Dostępne jest tylko tłumaczenie nerwowe.

**Interfejs API tłumacza v3** Interfejs API translatora V3 jest domyślnie neuronowy, a systemy statystyczne są dostępne tylko wtedy, gdy nie istnieje żaden system neuronowy.

> [!NOTE]
> Obecnie podzbiór języków nerwowych są dostępne w translatorze niestandardowym i stopniowo dodajemy dodatkowe. [Wyświetlanie języków dostępnych obecnie w uzywce Niestandardowej .](#customization)

|Język|  Kod języka|  V3 API|
|:-----|:-----:|:-----|
|Afrikaans| `af`|   Neuronowe|
|Arabski|    `ar`    |   Neuronowe|
|Bangla|    `bn`    |   Neuronowe|
|Bośniacki (łaciński)|   `bs`    |   Neuronowe|
|Bułgarski| `bg`    |   Neuronowe|
|Kantoński (tradycyjny)|   `yue`|  Statystycznych|
|Kataloński|   `ca`    |   Statystycznych|
|Chiński (uproszczony)|    `zh-Hans`|Neuronowe|
|Chiński (tradycyjny)|   `zh-Hant`       |Neuronowe|
|Chorwacki|  `hr`    |Neuronowe|
|Czeski| `cs`    |   Neuronowe|
|duński|    `da`        |Neuronowe|
|Niderlandzki| `nl`|   Neuronowe|
|Angielski|   `en`    |   Neuronowe|
|Estoński|  `et`    |   Neuronowe|
|Fijian|    `fj`    |   Statystycznych|
|Filipino|  `fil`   |   Statystycznych|
|fiński|   `fi`    |   Neuronowe|
|Francuski|    `fr`    |   Neuronowe|
|niemiecki|    `de`    |   Neuronowe|
|grecki| `el`    |   Neuronowe|
|Gudżarati|  `gu`    |   Neuronowe|
|Haitański|    `ht`        |Statystycznych|
|Hebrajski |`he`   |Neuronowe
|Hindi| `hi`    |   Neuronowe|
|Hmong Daw| `mww`   |   Statystycznych|
|węgierski| `hu`    |   Neuronowe|
|Islandzki| `is`    |   Neuronowe|
|Indonezyjski|    `id`    |   Statystycznych|
|Irlandzki | `ga`| Neuronowe
|Włoski|   `it`    |   Neuronowe|
|japoński|  `ja`    |   Neuronowe|
|Kannada|`kn`| Neuronowe
|Kiswahili| `sw`    |   Statystycznych|
|Klingoński|   `tlh`   |   Statystycznych|
|Klingon (plqaD)|   `tlh-Qaak`  |   Statystycznych|
|koreański |`ko`   |   Neuronowe|
|Łotewski|   `lv`    |   Neuronowe|
|Litewski|    `lt`    |   Neuronowe|
|Madagaskaru|  `mg`    |   Statystycznych|
|Malajski| `ms`        |Statystycznych|
|Malayalam| `ml` | Neuronowe
|Maltański|   `mt`    |   Statystycznych|
|Maoryjski| `mi`  | Neuronowe|
|Marathi| `mr`  | Neuronowe|
|Norweski| `nb`    |   Neuronowe|
|Perski|   `fa`    |   Neuronowe|
|Polski|    `pl`    |   Neuronowe|
|portugalski (Brazylia)|   `pt-br` |   Neuronowe|
|Portugalski (Portugalia)| `pt-pt` | Neuronowe
|Pendżabski|`pa`|Neuronowe
|Queretaro Otomi|   `otq`   |   Statystycznych|
|Rumuński|  `ro`    |   Neuronowe|
|Rosyjski|   `ru`    |   Neuronowe|
|Samoański|    `sm`    |   Statystycznych|
|Serbski (cyrylica)|    `sr-Cyrl`|  Statystycznych|
|Serbski (łaciński)|   `sr-Latn`       |Statystycznych|
|Słowacki|    `sk`    |   Neuronowe|
|Słoweński| `sl`    |   Neuronowe|
|Hiszpański|   `es`    |   Neuronowe|
|szwedzki|   `sv`    |Neuronowe|
|Tahitian|  `ty`    |Statystycznych|
|Tamilski| `ta`    |   Neuronowe|
|Telugu|    `te`    |   Neuronowe|
|Tajski|  `th`    |   Neuronowe|
|Tonga|    `to`    |   Statystycznych|
|Turecki|   `tr`        |Neuronowe|
|Ukraiński| `uk`    |   Neuronowe|
|Urdu|  `ur`    |   Statystycznych|
|Wietnamski|    `vi`    |   Neuronowe|
|Walijski| `cy`    |   Neuronowe|
|Yucatec Maya|  `yua`   |   Statystycznych|

> [!NOTE]
> Domyślnie `pt` kod `pt-br`języka na język portugalski (Brazylia).

## <a name="transliteration"></a>Transliteracja

Metoda Transliterate obsługuje następujące języki. W "Do/Od", "<-->" wskazuje, że język może być transliteracji z lub do jednego ze skryptów wymienionych. "-->" wskazuje, że język może być transliterowany tylko z jednego skryptu do drugiego.

| Język    | Kod języka | Skrypt | Do/z | Skrypt|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabski | `ar` | Arabski`Arab` | <--> | Łacińskiej`Latn` |
|Bangla  | `bn` | Bengalski`Beng` | <--> | Łacińskiej`Latn` |
| Chiński uproszczony | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Łacińskiej`Latn` |
| Chiński uproszczony | `zh-Hans` | Chiński uproszczony`Hans`| <--> | Chiński tradycyjny`Hant`|
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Łacińskiej`Latn` |
| Chiński (tradycyjny) | `zh-Hant` | Chiński tradycyjny`Hant`| <--> | Chiński uproszczony`Hans` |
| Gudżarati | `gu`  | Gudżarati`Gujr` | --> | Łacińskiej`Latn` |
| Hebrajski | `he` | Hebrajski`Hebr` | <--> | Łacińskiej`Latn` |
| Hindi | `hi` | Dewanagari`Deva` | <--> | Łacińskiej`Latn` |
| japoński | `ja` | Japoński`Jpan` | <--> | Łacińskiej`Latn` |
| Kannada | `kn` | Kannada`Knda` | --> | Łacińskiej`Latn` |
| Malayalam | `ml` | Malayalam`Mlym` | --> | Łacińskiej`Latn` |
| Marathi | `mr` | Dewanagari`Deva` | --> | Łacińskiej`Latn` |
| Orija | `or` | Orija`Orya` | <--> | Łacińskiej`Latn` |
| Pendżabski | `pa` | Gurmukhi`Guru`  | <--> | Łacińskiej`Latn`  |
| Serbski (cyrylica) | `sr-Cyrl` | Cyrylica`Cyrl`  | --> | Łacińskiej`Latn` |
| Serbski (łaciński) | `sr-Latn` | Łacińskiej`Latn` | --> | Cyrylica`Cyrl`|
| Tamilski | `ta` | Tamilski`Taml` | --> | Łacińskiej`Latn` |
| Telugu | `te` | Telugu`Telu` | --> | Łacińskiej`Latn` |
| Tajski | `th` | Tajski`Thai` | --> | Łacińskiej`Latn` |

## <a name="dictionary"></a>Słownik

Słownik obsługuje następujące języki do lub z języka angielskiego przy użyciu metod odnośnik i przykładów.

| Język    | Kod języka |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabski       | `ar`          |
| Bangla      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Kataloński      | `ca`          |
| Chiński (uproszczony)      | `zh-Hans`          |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| duński      | `da`          |
| Niderlandzki      | `nl`          |
| Estoński      | `et`          |
| fiński      | `fi`          |
| Francuski      | `fr`          |
| niemiecki      | `de`          |
| grecki      | `el`          |
| Haitański      | `ht`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| węgierski      | `hu`          |
| Islandzki    | `is`  |
| Indonezyjski      | `id`          |
| Włoski      | `it`          |
| japoński      | `ja`          |
| Kiswahili      | `sw`          |
| Klingoński      | `tlh`          |
| koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Malajski      | `ms`          |
| Maltański      | `mt`          |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| portugalski (Brazylia)     | `pt-br`          |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| szwedzki      | `sv`          |
| Tamilski      | `ta`          |
| Tajski      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Urdu      | `ur`          |
| Wietnamski      | `vi`          |
| Walijski      | `cy`          |

## <a name="detect"></a>Wykrywanie

Translator Text API wykrywa wszystkie języki dostępne do tłumaczenia i transliteracji.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Programowo uzyskiwanie dostępu do listy języków interfejsu API tekstu tłumacza

Można pobrać listę obsługiwanych języków dla interfejsu API tekstu tłumacza w wersji 3.0 przy użyciu metody Languages. Listę można wyświetlić według funkcji, kodu języka, a także nazwy języka w języku angielskim lub innym obsługiwanym języku. Ta lista jest automatycznie aktualizowana przez usługę Microsoft Translator w miarę udostępniania nowych języków.

[Wyświetlanie dokumentacji referencyjnej operacji Języka](reference/v3-0-languages.md)

## <a name="customization"></a>Dostosowywanie

Następujące języki są dostępne do dostosowywania do lub z języka angielskiego za pomocą [translatora niestandardowego](https://aka.ms/CustomTranslator).

| Język    | Kod języka |
|:----------- |:-------------:|
| Arabski       | `ar`          |
| Bangla      | `bn`          |
| Bośniacki (łaciński)      | `bs`          |
| Bułgarski      | `bg`          |
| Chiński (uproszczony)      | `zh-Hans`          |
|Chiński (tradycyjny)|   `zh-Hant`   |
| Chorwacki      | `hr`          |
| Czeski      | `cs`          |
| duński      | `da`          |
| Niderlandzki      | `nl`          |
| Angielski    | `en`     |
| Estoński      | `et`          |
| fiński      | `fi`          |
| Francuski      | `fr`          |
| niemiecki      | `de`          |
| grecki      | `el`          |
| Hebrajski      | `he`          |
| Hindi      | `hi`          |
| węgierski      | `hu`          |
| Islandzki | `is` |
| Indonezyjski|   `id`    |
| Irlandzki | `ga`  |
| Włoski      | `it`          |
| japoński      | `ja`          |
| Kiswahili|    `sw`    |
| koreański      | `ko`          |
| Łotewski      | `lv`          |
| Litewski      | `lt`          |
| Madagaskaru| `mg`    |
| Maoryjski| `mi`  |
| Norweski      | `nb`          |
| Perski      | `fa`          |
| Polski      | `pl`          |
| portugalski (Brazylia) | `pt-br` |
| Rumuński      | `ro`          |
| Rosyjski      | `ru`          |
| Samoański|   `sm`    |
| Serbski (łaciński)      | `sr-Latn`          |
| Słowacki     | `sk`          |
| Słoweński      | `sl`          |
| Hiszpański      | `es`          |
| szwedzki      | `sv`          |
| Tajski      | `th`          |
| Turecki      | `tr`          |
| Ukraiński      | `uk`          |
| Wietnamski      | `vi`          |
| Walijski | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Uzyskiwanie dostępu do listy w witrynie microsoft translator w sieci Web

Aby uzyskać szybkie spojrzenie na języki, w witrynie microsoft translatora przedstawiono wszystkie języki obsługiwane przez interfejsy API tekstu i mowy tłumacza. Ta lista nie zawiera informacji specyficznych dla dewelopera, takich jak kody języków.

[Zobacz listę języków](https://www.microsoft.com/translator/languages.aspx)
