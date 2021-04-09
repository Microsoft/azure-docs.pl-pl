---
title: Obsługa języka — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: f6a109c10491ad2eabb12069157e9e6f394bc1f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101736614"
---
# <a name="text-analytics-api-v3-language-support"></a>Obsługa języków interfejs API analizy tekstu v3 

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment-analysis)

| Język              | Kod języka | Obsługa wersji 3 | Początkowa wersja modelu v3: |              Uwagi |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chiński uproszczony    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` również zaakceptowane |
| Chiński tradycyjny   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Angielski               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francuski                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Niemiecki                |     `de`      |     ✓      |         2019-10-01         |                    |
| Włoski               |     `it`      |     ✓      |         2019-10-01         |                    |
| japoński              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreański                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` również zaakceptowane |
| Hiszpański               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turecki               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Wyszukiwanie opinii (tylko wersja zapoznawcza w wersji 3.1)

| Język              | Kod języka | Począwszy od wersji modelu v3: |              Uwagi |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angielski               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Rozpoznawanie jednostek nazwanych (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Tylko jednostki "Person", "Location" i "Organization" są zwracane dla języków oznaczonych gwiazdką *.

| Język               | Kod języka | Obsługa wersji 3 | Począwszy od wersji modelu v3: |       Uwagi        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabski                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chiński uproszczony     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` również zaakceptowane |
| Chiński tradycyjny   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Czeski                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Duński                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Niderlandzki                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Angielski                |     `en`      |     ✓      |               2019-10-01        |                    |
| Fiński               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francuski                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Niemiecki                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebrajski                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Węgierski             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Włoski               |     `it`      |     ✓       |               2021-01-15        |                    |
| japoński              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreański                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norweski (bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` również zaakceptowane |
| Polski                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` również zaakceptowane |
| Rosyjski              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Hiszpański               |     `es`      |     ✓       |               2020-04-01        |                    |
| Szwedzki               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turecki               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/key-phrase-extraction)

| Język              | Kod języka |  Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: |       Uwagi        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Duński                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Niderlandzki                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Angielski               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Fiński               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Francuski                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Niemiecki                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Włoski               |     `it`      |     ✓      |                2019-10-01                 |                    |
| japoński              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Koreański                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` również zaakceptowane |
| Polski                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugalski (Portugalia) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` również zaakceptowane |
| Rosyjski               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Hiszpański               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Szwedzki               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Łączenie jednostek](#tab/entity-linking)

| Język | Kod języka |  Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: | Uwagi |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Angielski  |     `en`      |     ✓      |                2019-10-01                 |       |
| Hiszpański  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Identyfikowalne dane osobowe](#tab/pii)

| Język               | Kod języka | Obsługa wersji 3 | Począwszy od wersji modelu v3: |       Uwagi        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chiński uproszczony     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` również zaakceptowane |
| Angielski                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francuski                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Niemiecki                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Włoski               |     `it`      |     ✓       |               2021-01-15        |                    |
| japoński              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreański                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` również zaakceptowane |
| Hiszpański               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language-detection)

Interfejs API analizy tekstu może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych oraz zwracać wykryte Języki o ich nazwie i kodzie. Parametry kodu języka analiza tekstu wykrywanie języka są zgodne ze standardem [BCP-47](https://tools.ietf.org/html/bcp47) z większością z nich zgodnymi z identyfikatorami [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywanie języka, aby sprawdzić, czy zwróci kod. Odpowiedź dla języków, których nie można wykryć, to `unknown` .

| Język | Kod języka | Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albański|`sq`|✓|    |
|Amharski|`am`|✓|2021-01-05|
|Arabski|`ar`|✓|    |
|Armeński|`hy`|✓|    |
|Assamski|`as`|✓|2021-01-05|
|Azerbejdżański|`az`|✓|2021-01-05|
|Baskijski|`eu`|✓|    |
|Białoruski|`be`|✓|    |
|Bengalski|`bn`|✓|    |
|Bośniacki|`bs`|✓|2020-09-01|
|Bułgarski|`bg`|✓|    |
|Birmański|`my`|✓|    |
|Kataloński|`ca`|✓|    |
|Khmerski centralny|`km`|✓|    |
|Chiński|`zh`|✓|    |
|Chiński (uproszczony)|`zh_chs`|✓|    |
|Chiński (tradycyjny)|`zh_cht`|✓|    |
|Korsykański|`co`|✓|2021-01-05|
|Chorwacki|`hr`|✓|    |
|Czeski|`cs`|✓|    |
|Duński|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Niderlandzki|`nl`|✓|    |
|Angielski|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estoński|`et`|✓|    |
|Fijian|`fj`|✓|2020-09-01|
|Fiński|`fi`|✓|    |
|Francuski|`fr`|✓|    |
|Galicyjski|`gl`|✓|    |
|Gruziński|`ka`|✓|    |
|Niemiecki|`de`|✓|    |
|Grecki|`el`|✓|    |
|Gudżarati|`gu`|✓|    |
|Kreolski|`ht`|✓|    |
|Haus|`ha`|✓|2021-01-05|
|Hebrajski|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Kowalski|`mww`|✓|2020-09-01|
|Węgierski|`hu`|✓|    |
|Islandzki|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonezyjski|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandzki|`ga`|✓|    |
|Włoski|`it`|✓|    |
|japoński|`ja`|✓|    |
|Jawajski|`jv`|✓|2021-01-05|
|Kannada|`kn`|✓|    |
|Kazachski|`kk`|✓|2020-09-01|
|Kinjarwanda|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Koreański|`ko`|✓|    |
|Kurdyjski|`ku`|✓|    |
|-|`lo`|✓|    |
|Wielka|`la`|✓|    |
|Łotewski|`lv`|✓|    |
|Litewski|`lt`|✓|    |
|Luksemburski|`lb`|✓|2021-01-05|
|Macedonii|`mk`|✓|    |
|Madagaskaru|`mg`|✓|2020-09-01|
|Malajski|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltański|`mt`|✓|    |
|Maoryjski|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Mongolski|`mn`|✓|2021-01-05|
|Nepalski|`ne`|✓|2021-01-05|
|Norweski|`no`|✓|    |
|Norweski Nynorsk|`nn`|✓|    |
|Orija|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Perski|`fa`|✓|    |
|Polski|`pl`|✓|    |
|Portugalski|`pt`|✓|    |
|Pendżabski|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Rumuński|`ro`|✓|    |
|Rosyjski|`ru`|✓|    |
|Samoan|`sm`|✓|2020-09-01|
|Serbski|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Syngaleski|`si`|✓|    |
|Słowacki|`sk`|✓|    |
|Słoweński|`sl`|✓|    |
|Somalijski|`so`|✓|    |
|Hiszpański|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Suahili|`sw`|✓|    |
|Szwedzki|`sv`|✓|    |
|Tagalski|`tl`|✓|    |
|Tahitian|`ty`|✓|2020-09-01|
|Tadżycki|`tg`|✓|2021-01-05|
|Tamilski|`ta`|✓|    |
|Tatarski|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Tajlandzki|`th`|✓|    |
|Tybetański|`bo`|✓|2021-01-05|
|Tigrinia|`ti`|✓|2021-01-05|
|Tonga|`to`|✓|2020-09-01|
|Turecki|`tr`|✓|2021-01-05|
|Turkmeński|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Joruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Zobacz też

* [Czym jest interfejs API analizy tekstu?](overview.md)   
