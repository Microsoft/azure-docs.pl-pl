---
title: Linki do dokumentacji dotyczące wzbogacania AI
titleSuffix: Azure Cognitive Search
description: Lista artykułów, samouczków, przykładów i wpisów w blogu związanych z obciążeniami wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616189"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Zasoby dokumentacji dotyczące wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze

Wzbogacanie AI to funkcja indeksowania Wyszukiwanie poznawcze platformy Azure, która umożliwia znajdowanie ukrytych informacji w źródłach nietekstowych i niezróżnicowanym tekście, przekształcając je w zawartość tekstową z możliwością wyszukiwania pełnotekstowego na platformie Azure Wyszukiwanie poznawcze.

Poniższe artykuły stanowią kompletną dokumentację dotyczącą wzbogacania AI.

## <a name="getting-started"></a>Wprowadzenie
+ [Wprowadzenie do AI na platformie Azure Wyszukiwanie poznawcze](cognitive-search-concept-intro.md)
+ [Szybki Start: Tworzenie zestawu umiejętności poznawczych w Azure Portal](cognitive-search-quickstart-blob.md)
+ [Samouczek: wzbogacone indeksowanie za pomocą AI](cognitive-search-tutorial-blob.md)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Wskazówki dotyczące porady
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak odwoływać się do adnotacji w zestawie umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Jak mapować pola na indeks](cognitive-search-output-field-mapping.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Jak skompilować indeks Wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md)
+ [Jak zdefiniować niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md)
+ [Wskazówki dotyczące rozwiązywania problemów](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Dokumentacja

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
  + [Microsoft. umiejętności. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. umiejętności. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. umiejętności. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. umiejętności. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. umiejętności. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. umiejętności. Text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. umiejętności. Text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. umiejętności. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. umiejętności. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. umiejętności. Vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. umiejętności. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. umiejętności. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. umiejętności. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Umiejętności niestandardowe
  + [Microsoft. umiejętności. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Przestarzałe umiejętności](cognitive-search-skill-deprecated.md)
  + [Microsoft. umiejętności. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [Interfejs API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create zestawu umiejętności (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Create indeksator (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz także

+ [Interfejs API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Co to jest wyszukiwanie poznawcze na platformie Azure?](search-what-is-azure-search.md)
