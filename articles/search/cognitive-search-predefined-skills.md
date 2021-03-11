---
title: Wbudowane przetwarzanie tekstu i obrazu podczas indeksowania
titleSuffix: Azure Cognitive Search
description: Wyodrębnianie danych, język naturalny, przetwarzanie obrazów umiejętności poznawcze Dodawanie semantyki i struktury do nieprzetworzonej zawartości w potoku Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bde82a0ad50c2172e70337ad202b91af0a62c530
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547819"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Wbudowane umiejętności poznawcze do przetwarzania tekstu i obrazów podczas indeksowania (Azure Wyszukiwanie poznawcze)

Ten artykuł zawiera informacje o umiejętnościach poznawczych zapewnianych przez usługę Azure Wyszukiwanie poznawcze, które można uwzględnić w zestawu umiejętności w celu wyodrębnienia zawartości i struktury. *Umiejętność* jest modułem lub operacją, która przekształca zawartość w jakiś sposób. Często jest to składnik, który wyodrębnia dane lub rozpoznaje strukturę, i w związku z tym rozszerza nasze zrozumienie danych wejściowych. Prawie zawsze, dane wyjściowe są oparte na tekście. *Zestawu umiejętności* to zbiór umiejętności, które definiują potok wzbogacania. 

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu łamania dokumentu w usłudze Azure Wyszukiwanie poznawcze. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).
>
> Funkcja [wzbogacania (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) umożliwia zapewnienie pamięci podręcznej, która pozwala Indeksatorowi wydajniejsze wykonywanie tylko umiejętności poznawczej, które są niezbędne, jeśli zmodyfikujesz zestawu umiejętności w przyszłości, oszczędzając czas i pieniądze.


## <a name="built-in-skills"></a>Wbudowane umiejętności

Kilka umiejętności jest elastycznych do użycia lub produkcji. Ogólnie rzecz biorąc, większość umiejętności opiera się na wstępnie szkolonych modelach, co oznacza, że nie można nauczyć modelu przy użyciu własnych danych szkoleniowych. W poniższej tabeli wymieniono i opisano umiejętności udostępniane przez firmę Microsoft. 

| Czy | Opis |
|-------|-------------|
|[Microsoft. umiejętności. Text. CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Szuka tekstu na podstawie niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz.|
| [Microsoft. umiejętności. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Ta umiejętność używa przedniego modelu do wykrywania ważnych fraz opartych na rozmieszczeniu, regułach językowych, sąsiedztwie z innymi postanowieniami oraz o tym, jak nietypowy termin znajduje się w danych źródłowych. |
| [Microsoft. umiejętności. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta umiejętność używa preszkolenego modelu do wykrywania używanego języka (jeden identyfikator języka na dokument). Gdy w tych samych segmentach tekstu są używane wiele języków, dane wyjściowe są identyfikatorem LCID gotowego do użycia języka.|
| [Microsoft. umiejętności. Text. MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekst z kolekcji pól w jedno pole.  |
| [Microsoft. umiejętności. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ta umiejętność używa przedniego modelu do ustanowienia jednostek dla ustalonego zestawu kategorii: ludzie, lokalizacja, organizacja, wiadomości e-mail, adresy URL, pola DateTime. |
| [Microsoft. umiejętności. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Ta umiejętność używa modelu przedniego do wyodrębnienia danych osobowych z danego tekstu. Umiejętność zapewnia również różne opcje maskowania wykrytych jednostek informacji osobistych w tekście.  |
| [Microsoft. umiejętności. Text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Ta umiejętność używa przedszkolnego modelu do oceny wartości dodatnich lub ujemnych tonacji w rekordach według podstaw rekordów. Wynik jest z przedziału od 0 do 1. Neutralne oceny występują dla przypadku wartości null, gdy nie można wykryć tonacji oraz dla tekstu, który jest uznawany za neutralny.  |
| [Microsoft. umiejętności. Text. SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na strony, aby można było wzbogacić lub rozszerzyć zawartość. |
| [Microsoft. umiejętności. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Ta umiejętność korzysta ze zintegrowanego modelu, aby przetłumaczyć tekst wejściowy na różne języki do normalizacji lub przypadków użycia. |
| [Microsoft. umiejętności. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ta umiejętność używa algorytmu wykrywania obrazu do identyfikowania zawartości obrazu i generowania opisu tekstu. |
| [Microsoft. umiejętności. Vision. OcrSkill](cognitive-search-skill-ocr.md) | Optyczne rozpoznawanie znaków. |
| [Microsoft. umiejętności. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Umożliwia filtrowanie, przypisywanie wartości domyślnej i scalanie danych na podstawie warunku.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Wyodrębnia zawartość z pliku w ramach potoku wzbogacania. |
| [Microsoft. umiejętności. util. ShaperSkill](cognitive-search-skill-shaper.md) | Mapuje dane wyjściowe do typu złożonego (wieloczęściowego typu danych, który może być używany do pełnej nazwy, adresu wielowierszowego lub kombinacji nazwiska i identyfikatora osobistego). |
| [Microsoft. umiejętności. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umożliwia rozszerzanie potoku wzbogacenia AI przez nadanie połączenia HTTP do niestandardowego interfejsu API sieci Web |
| [Microsoft. umiejętności. Custom. AmlSkill](cognitive-search-aml-skill.md) | Umożliwia rozszerzalność potoku wzbogacenia AI z modelem Azure Machine Learning |


Aby uzyskać wskazówki dotyczące tworzenia [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md), zobacz [jak zdefiniować niestandardowy interfejs](cognitive-search-custom-skill-interface.md) i [przykład: Tworzenie NIESTANDARDOWEJ umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Zobacz też

+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Niestandardowa definicja interfejsu umiejętności](cognitive-search-custom-skill-interface.md)
+ [Samouczek: wzbogacone indeksowanie za pomocą AI](cognitive-search-tutorial-blob.md)
