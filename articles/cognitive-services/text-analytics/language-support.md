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
ms.date: 09/12/2020
ms.author: aahi
ms.openlocfilehash: 185a6a26350f7803d109e26d8985510380b8cc16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056595"
---
# <a name="text-analytics-api-v3-language-support"></a>Obsługa języków interfejs API analizy tekstu v3 

> [!IMPORTANT]
> Wersja 3. x interfejs API analizy tekstu jest obecnie niedostępna w następujących regionach: Indie Środkowe, Zjednoczone Emiraty Arabskie, Chiny Północne 2, Chiny Wschodnie.


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
| Włoski               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| japoński              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreański                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norweski (bokmål)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Polski                |     `pl`      |     ✓      |            |                            |                    |
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

Interfejs API analizy tekstu może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych.  Wykrywanie języka zwraca "skrypt" języka. Na przykład dla frazy "mam Dog", będzie ona zwracała się  `en` zamiast  `en-US` . Jedynym szczególnym przypadkiem jest chiński, w którym funkcja wykrywania języka zwróci wartość `zh_CHS` lub `zh_CHT` Jeśli może ustalić skrypt, w którym znajduje się tekst. W sytuacjach, gdy nie można zidentyfikować określonego skryptu dla dokumentu w języku chińskim, zostanie on zwrócony po prostu `zh` .

Nie publikujemy dokładnej listy języków dla tej funkcji, ale może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych. 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywanie języka, aby sprawdzić, czy zwróci kod. Odpowiedź dla języków, których nie można wykryć, to `unknown` .

---

## <a name="see-also"></a>Zobacz też

* [Czym jest interfejs API analizy tekstu?](overview.md)   
