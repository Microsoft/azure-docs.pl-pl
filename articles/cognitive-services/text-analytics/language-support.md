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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: b3a633c1eff6adf2a33b4f3bfc818eb51ff5ee85
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653099"
---
# <a name="text-analytics-api-v3-language-support"></a>Obsługa języków interfejs API analizy tekstu v3 

#### <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment-analysis)

| Język              | Kod języka | Obsługa wersji 2 | Obsługa wersji 3 | Początkowa wersja modelu v3: |              Uwagi |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chiński uproszczony    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` również zaakceptowane |
| Chiński tradycyjny   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Duński               |     `da`      |     ✓      |            |                            |                    |
| Niderlandzki                 |     `nl`      |     ✓      |            |                            |                    |
| Angielski               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Fiński               |     `fi`      |     ✓      |            |                            |                    |
| Francuski                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Niemiecki                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grecki                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Włoski               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| japoński              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreański                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polski                |     `pl`      |     ✓      |            |                            |                    |
| Portugalski (Brazylia)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` również zaakceptowane |
| Rosyjski               |     `ru`      |     ✓      |            |                            |                    |
| Hiszpański               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Szwedzki               |     `sv`      |     ✓      |            |                            |                    |
| Turecki               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Wyszukiwanie opinii (tylko wersja zapoznawcza w wersji 3.1)

| Język              | Kod języka | Począwszy od wersji modelu v3: |              Uwagi |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angielski               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Rozpoznawanie jednostek nazwanych (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 obecnie obsługuje tylko Języki w języku angielskim i hiszpańskim. Jeśli wywołasz NER v3 z innym językiem, interfejs API zwróci wyniki z v 2.1, pod warunkiem, że język jest obsługiwany w wersji 2,1.
> * Wersja 2.1 zwraca tylko pełny zestaw dostępnych jednostek w języku angielskim, chińskim tradycyjnym, francuskim, niemieckim i hiszpańskim.  Jednostki "osoba", "lokalizacja" i "organizacja" są zwracane dla innych obsługiwanych języków.

| Język               | Kod języka | obsługa techniczna v 2.1 | Obsługa wersji 3 | Począwszy od wersji modelu v3: |       Uwagi        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabski                |     `ar`      |     ✓      |            |                                 |                    |
| Czeski                 |     `cs`      |     ✓      |            |                                 |                    |
| Chiński uproszczony     |   `zh-hans`   |     ✓      |            |                                 | `zh` również zaakceptowane |
| Chiński tradycyjny   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Duński                |     `da`      |     ✓      |            |                                 |                    |
| Niderlandzki                 |     `nl`      |     ✓      |            |                                 |                    |
| Angielski                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Fiński               |     `fi`      |     ✓      |            |                                 |                    |
| Francuski                 |     `fr`      |     ✓      |            |                                 |                    |
| Niemiecki                 |     `de`      |     ✓      |            |                                 |                    |
| Hebrajski                |     `he`      |     ✓      |            |                                 |                    |
| Węgierski             |     `hu`      |     ✓      |            |                                 |                    |
| Włoski               |     `it`      |     ✓      |            |                                 |                    |
| japoński              |     `ja`      |     ✓      |            |                                 |                    |
| Koreański                |     `ko`      |     ✓      |            |                                 |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |            |                                 | `nb` również zaakceptowane |
| Polski                |     `pl`      |     ✓      |            |                                 |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓      |            |                                 | `pt` również zaakceptowane |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Rosyjski              |     `ru`      |     ✓      |            |                                 |                    |
| Hiszpański               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Szwedzki               |     `sv`      |     ✓      |            |                                 |                    |
| Turecki               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Wyodrębnianie kluczowych fraz](#tab/key-phrase-extraction)

> [!NOTE]
> Wersje modeli wyodrębnianie kluczowych fraz wcześniejszych niż 2020-07-01 mają limit znaków równy 64. Ten limit nie występuje w nowszych wersjach modeli.

| Język              | Kod języka | Obsługa wersji 2 | Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: |       Uwagi        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Niderlandzki                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Angielski               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Fiński               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francuski                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Niemiecki                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Włoski               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| japoński              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreański                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` również zaakceptowane |
| Polski                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugalski (Portugalia) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` również zaakceptowane |
| Portugalski (Brazylia)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Rosyjski               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Hiszpański               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Szwedzki               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Łączenie jednostek](#tab/entity-linking)

| Język | Kod języka | Obsługa wersji 2 | Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: | Uwagi |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Angielski  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Hiszpański  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Wykrywanie języka](#tab/language-detection)

Interfejs API analizy tekstu może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych oraz zwracać wykryte Języki o ich nazwie i kodzie. Parametry kodu języka analiza tekstu wykrywanie języka są zgodne ze standardem [BCP-47](https://tools.ietf.org/html/bcp47) z większością z nich zgodnymi z identyfikatorami [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywanie języka, aby sprawdzić, czy zwróci kod. Odpowiedź dla języków, których nie można wykryć, to `unknown` .

| Język | Kod języka | Obsługa wersji 3 | Dostępne począwszy od wersji modelu v3: |
|:-|:-:|:-:|:-:|
| Afrikaans | `af` | ✓ |  |
| Albański | `sq` | ✓ |  |
| Arabski | `ar` | ✓ |  |
| Armeński | `hy` | ✓ |  |
| Baskijski | `eu` | ✓ |  |
| Białoruski | `be` | ✓ |  |
| Bengalski | `bn` | ✓ |  |
| Bośniacki | `bs` | ✓ | 2020-09-01 |
| Bułgarski | `bg` | ✓ |  |
| Birmański | `my` | ✓ |  |
| Kataloński, walencki | `ca` | ✓ |  |
| Khmerski centralny | `km` | ✓ |  |
| Chiński | `zh` | ✓ |  |
| Chiński (uproszczony) | `zh_chs` | ✓ |  |
| Chiński (tradycyjny) | `zh_cht` | ✓ |  |
| Chorwacki | `hr` | ✓ |  |
| Czeski | `cs` | ✓ |  |
| Duński | `da` | ✓ |  |
| Dari | `prs` | ✓ | 2020-09-01 |
| Malediwski, dhivehi, malediwski | `dv` | ✓ |  |
| Holenderski, Flemish | `nl` | ✓ |  |
| Angielski | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Estoński | `et` | ✓ |  |
| Fijian | `fj` | ✓ | 2020-09-01 |
| Fiński | `fi` | ✓ |  |
| Francuski | `fr` | ✓ |  |
| Galicyjski | `gl` | ✓ |  |
| Gruziński | `ka` | ✓ |  |
| Niemiecki | `de` | ✓ |  |
| Grecki | `el` | ✓ |  |
| Gudżarati | `gu` | ✓ |  |
| Kreolski, Kreolski haitański | `ht` | ✓ |  |
| Hebrajski | `he` | ✓ |  |
| Hindi | `hi` | ✓ |  |
| Hmong Kowalski | `mww` | ✓ | 2020-09-01 |
| Węgierski | `hu` | ✓ |  |
| Islandzki | `is` | ✓ |  |
| Indonezyjski | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Irlandzki | `ga` | ✓ |  |
| Włoski | `it` | ✓ |  |
| japoński | `ja` | ✓ |  |
| Kannada | `kn` | ✓ |  |
| Kazachski | `kk` | ✓ | 2020-09-01 |
| Koreański | `ko` | ✓ |  |
| Kurdyjski | `ku` | ✓ |  |
| - | `lo` | ✓ |  |
| Wielka | `la` | ✓ |  |
| Łotewski | `lv` | ✓ |  |
| Litewski | `lt` | ✓ |  |
| Macedonii | `mk` | ✓ |  |
| Madagaskaru | `mg` | ✓ | 2020-09-01 |
| Malajski | `ms` | ✓ |  |
| Malayalam | `ml` | ✓ |  |
| Maltański | `mt` | ✓ |  |
| Maoryjski | `mi` | ✓ | 2020-09-01 |
| Marathi | `mr` | ✓ | 2020-09-01 |
| Norweski | `no` | ✓ |  |
| Norweski Nynorsk | `nn` | ✓ |  |
| Orija | `or` | ✓ |  |
| Paszto, paszto | `ps` | ✓ |  |
| Perski | `fa` | ✓ |  |
| Polski | `pl` | ✓ |  |
| Portugalski | `pt` | ✓ |  |
| Pendżabski, Panjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Rumuński, Moldavian, mołdawski | `ro` | ✓ |  |
| Rosyjski | `ru` | ✓ |  |
| Samoan | `sm` | ✓ | 2020-09-01 |
| Serbski | `sr` | ✓ |  |
| Syngaleski, Sinhalese | `si` | ✓ |  |
| Słowacki | `sk` | ✓ |  |
| Słoweński | `sl` | ✓ |  |
| Somalijski | `so` | ✓ |  |
| Hiszpański, Kastylia | `es` | ✓ |  |
| Suahili | `sw` | ✓ |  |
| Szwedzki | `sv` | ✓ |  |
| Tagalski | `tl` | ✓ |  |
| Tahitian | `ty` | ✓ | 2020-09-01 |
| Tamilski | `ta` | ✓ |  |
| Telugu | `te` | ✓ |  |
| Tajlandzki | `th` | ✓ |  |
| Tonga | `to` | ✓ | 2020-09-01 |
| Turecki | `tr` | ✓ |  |
| Ukraiński | `uk` | ✓ |  |
| Urdu | `ur` | ✓ |  |
| Uzbecki | `uz` | ✓ |  |
| Wietnamski | `vi` | ✓ |  |
| Walijski | `cy` | ✓ |  |
| Jidysz | `yi` | ✓ |  |
| Yucatec Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>Zobacz także

* [Czym jest interfejs API analizy tekstu?](overview.md)   
