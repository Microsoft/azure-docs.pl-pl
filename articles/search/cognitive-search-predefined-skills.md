---
title: Wbudowane przetwarzanie tekstu i obrazu podczas indeksowania
titleSuffix: Azure Cognitive Search
description: Wyodrębnianie danych, język naturalny, przetwarzanie obrazu umiejętności poznawczych dodać semantyki i struktury do zawartości nieprzetworzonej w potoku usługi Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b1cf1750924ab8ea8afff6ac788683565433866b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618025"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Wbudowane umiejętności poznawcze do przetwarzania tekstu i obrazu podczas indeksowania (Azure Cognitive Search)

W tym artykule dowiesz się o umiejętności poznawczych dostarczonych z usługi Azure Cognitive Search, które można uwzględnić w zestaw umiejętności, aby wyodrębnić zawartość i strukturę. *Umiejętność poznawcza* to moduł lub operacja, która w jakiś sposób przekształca zawartość. Często jest to składnik, który wyodrębnia dane lub wywnioskuje strukturę, a tym samym zwiększa nasze zrozumienie danych wejściowych. Prawie zawsze dane wyjściowe są oparte na tekście. Zestaw *umiejętności* to zbiór umiejętności definiujących potok wzbogacania. 

> [!NOTE]
> W miarę rozszerzania zakresu poprzez zwiększanie częstotliwości przetwarzania, dodawanie większej liczby dokumentów lub dodawanie kolejnych algorytmów sztucznej inteligencji należy [dołączyć rozliczany zasób usług Cognitive Services.](cognitive-search-attach-cognitive-services.md) Opłaty naliczane podczas wywoływania interfejsów API w usługach Cognitive Services i wyodrębniania obrazu w ramach etapu pękania dokumentów w usłudze Azure Cognitive Search. Nie ma żadnych opłat za wyodrębnianie tekstu z dokumentów.
>
> Wykonanie wbudowanych umiejętności jest naliczane według istniejącej [ceny płatności zgodnie z rzeczywistymi oczekiwaniami.](https://azure.microsoft.com/pricing/details/cognitive-services/) Ceny wyodrębniania obrazów są opisane na [stronie cennika usługi Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).
>
> Funkcja [wzbogacenia przyrostowego (podglądu)](cognitive-search-incremental-indexing-conceptual.md) umożliwia zapewnienie pamięci podręcznej, która umożliwia indeksatorowi zwiększenie wydajności w uruchamianiu tylko umiejętności poznawczych, które są niezbędne, jeśli zmodyfikujesz swój zestaw umiejętności w przyszłości, oszczędzając czas i pieniądze.


## <a name="built-in-skills"></a>Wbudowane umiejętności

Kilka umiejętności są elastyczne w tym, co zużywają lub produkują. Ogólnie rzecz biorąc, większość umiejętności są oparte na wstępnie przeszkolonych modeli, co oznacza, że nie można trenować modelu przy użyciu własnych danych szkoleniowych. W poniższej tabeli wylicza się i opisano umiejętności dostarczone przez firmę Microsoft. 

| Umiejętności | Opis |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Wyszukuje tekst z niestandardowej, zdefiniowanej przez użytkownika listy słów i fraz.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ta umiejętność używa wstępnie przeszkolonego modelu do wykrywania ważnych fraz na podstawie umieszczania terminów, reguł językowych, bliskości z innymi terminami i jak nietypowe jest to określenie w danych źródłowych. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ta umiejętność używa wstępnie przeszkolonego modelu do wykrywania języka, który jest używany (jeden identyfikator języka na dokument). Gdy w tych samych segmentach tekstu jest używanych wiele języków, dane wyjściowe są identyfikatorem LCID używanego języka.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje tekst z kolekcji pól w jedno pole.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ta umiejętność używa wstępnie przeszkolonego modelu do ustanawiania encji dla stałego zestawu kategorii: osób, lokalizacji, organizacji, wiadomości e-mail, adresów URL, pól datetime. |
| [Microsoft.skills.text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Ta umiejętność używa wstępnie przeszkolonego modelu do wyodrębniania informacji umożliwiających identyfikację użytkownika z danego tekstu. Umiejętność daje również różne opcje maskowania wykrytych jednostek pii w tekście.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Ta umiejętność używa wstępnie przeszkolonego modelu do oceniania pozytywnych lub negatywnych nastrojów w ujęciu rekord według rekordu. Wynik wynosi od 0 do 1. Wyniki neutralne występują zarówno dla przypadku null, gdy nie można wykryć tonacji, jak i dla tekstu, który jest uważany za neutralny.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Dzieli tekst na strony, dzięki czemu można stopniowo wzbogacać lub powiększać zawartość. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Ta umiejętność używa wstępnie przeszkolonego modelu do tłumaczenia tekstu wejściowego na różne języki w celu normalizacji lub lokalizacji przypadków użycia. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ta umiejętność używa algorytmu wykrywania obrazu do identyfikowania zawartości obrazu i generowania opisu tekstowego. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optyczne rozpoznawanie znaków. |
| [Umiejętności firmy Microsoft.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Umożliwia filtrowanie, przypisywanie wartości domyślnej i scalanie danych na podstawie warunku.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Wyodrębnia zawartość z pliku w potoku wzbogacania. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Dane wyjściowe mapują typ złożony (wieloczęściowy typ danych, który może być używany dla pełnej nazwy, adresu wielowierszowego lub kombinacji nazwiska i identyfikatora osobistego). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umożliwia rozszerzalność potoku wzbogacania AI przez wywołanie HTTP do niestandardowego interfejsu API sieci Web |


Aby uzyskać wskazówki dotyczące tworzenia [umiejętności niestandardowych,](cognitive-search-custom-skill-web-api.md)zobacz [Jak zdefiniować interfejs niestandardowy](cognitive-search-custom-skill-interface.md) i [Przykład: Tworzenie umiejętności niestandardowych do wzbogacania SI](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Zobacz też

+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Definicja interfejsu umiejętności niestandardowych](cognitive-search-custom-skill-interface.md)
+ [Samouczek: Wzbogacone indeksowanie za pomocą ai](cognitive-search-tutorial-blob.md)
