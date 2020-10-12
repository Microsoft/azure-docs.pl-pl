---
title: Linki do dokumentacji dotyczące wzbogacania AI
titleSuffix: Azure Cognitive Search
description: Lista artykułów, samouczków, przykładów i wpisów w blogu związanych z obciążeniami wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935351"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Zasoby dokumentacji dotyczące wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze

Wzbogacanie AI jest dodatkiem do indeksowania opartego na indeksatorach, które umożliwia znalezienie ukrytych informacji w źródłach nietekstowych i niezróżnicowanym tekście, przekształcając je w zawartość z możliwością wyszukiwania pełnotekstowego na platformie Azure Wyszukiwanie poznawcze. 

W przypadku przetwarzania wbudowanego wstępnie nauczone modele AI w Cognitive Services są określane wewnętrznie w celu przeprowadzenia analiz. Można także zintegrować modele niestandardowe przy użyciu Azure Machine Learning, Azure Functions lub innych metod.

Poniżej przedstawiono skonsolidowaną listę dokumentacji dotyczącej wzbogacania systemu AI.

## <a name="concepts"></a>Pojęcia

+ [Wzbogacanie AI](cognitive-search-concept-intro.md)
+ [Zestawy umiejętności](cognitive-search-working-with-skillsets.md)
+ [Sesje debugowania](cognitive-search-debug-session.md)
+ [Magazyny wiedzy](knowledge-store-concept-intro.md)
+ [Projekcje](knowledge-store-projection-overview.md)
+ [Przyrostowe wzbogacanie (ponowne użycie buforowanego wzbogaconego dokumentu)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Wskazówki dotyczące przewodników

+ [Szybki Start: Tworzenie zestawu umiejętności poznawczych w Azure Portal](cognitive-search-quickstart-blob.md)
+ [Samouczek: wzbogacone indeksowanie za pomocą AI](cognitive-search-tutorial-blob.md)
+ [Samouczek: diagnozowanie, naprawianie i zatwierdzanie zmian w zestawu umiejętności z sesjami debugowania](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Magazyny wiedzy

+ [Szybki Start: Tworzenie sklepu z bazami danych w Azure Portal](knowledge-store-create-portal.md)
+ [Tworzenie sklepu z bazami danych przy użyciu REST i programu Poster](knowledge-store-create-rest.md)
+ [Wyświetlanie sklepu z bazami danych za pomocą Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md)
+ [Łączenie ze sklepem wiedzy Power BI](knowledge-store-connect-power-bi.md)
+ [Przykłady rzutowania (jak kształtować i eksportować wzbogacania)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Umiejętności niestandardowe (Zaawansowane)

+ [Jak zdefiniować niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md)
+ [Przykład: Tworzenie niestandardowej umiejętności przy użyciu Azure Functions (i wyszukiwanie jednostek Bing interfejsów API)](cognitive-search-create-custom-skill-example.md)
+ [Przykład: Tworzenie niestandardowej umiejętności przy użyciu języka Python](cognitive-search-custom-skill-python.md)
+ [Przykład: Tworzenie niestandardowej umiejętności przy użyciu aparatu rozpoznawania formularzy](cognitive-search-custom-skill-form.md) 
+ [Przykład: Tworzenie niestandardowej umiejętności przy użyciu Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Wskazówki dotyczące porady

+ [Dołączanie zasobu usługi Cognitive Services](cognitive-search-attach-cognitive-services.md)
+ [Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [adnotacje odwołania w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md)
+ [Mapowanie pól na indeks](cognitive-search-output-field-mapping.md)
+ [Przetwarzanie i wyodrębnianie informacji z obrazów](cognitive-search-concept-image-scenarios.md)
+ [Skonfiguruj buforowanie na potrzeby wzbogacania przyrostowego](search-howto-incremental-index.md)
+ [Jak skompilować indeks Wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md)
+ [Wskazówki dotyczące projektowania](cognitive-search-concept-troubleshooting.md)
+ [Typowe błędy i ostrzeżenia](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Dokumentacja umiejętności

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
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. umiejętności. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Umiejętności niestandardowe
  + [Microsoft. umiejętności. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft. umiejętności. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Przestarzałe umiejętności](cognitive-search-skill-deprecated.md)
  + [Microsoft. umiejętności. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>Interfejsy API

+ [Interfejs API REST](/rest/api/searchservice/)
  + [Create zestawu umiejętności (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Tworzenie indeksatora (API-Version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zobacz też

+ [Interfejs API REST usługi Azure Cognitive Search](/rest/api/searchservice/)
+ [Indeksatory w usłudze Azure Cognitive Search](search-indexer-overview.md)
+ [Co to jest platforma Azure Wyszukiwanie poznawcze?](search-what-is-azure-search.md)