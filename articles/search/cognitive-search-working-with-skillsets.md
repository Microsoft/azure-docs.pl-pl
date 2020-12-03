---
title: Koncepcje i przepływy pracy zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Umiejętności to miejsce, w którym tworzysz potok wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze. Poznaj ważne pojęcia i szczegółowe informacje na temat kompozycji zestawu umiejętności.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558117"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Zestawu umiejętności koncepcje w usłudze Azure Wyszukiwanie poznawcze

Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują bardziej szczegółowego wglądu w koncepcje i składanie zestawu umiejętności oraz założono znajomość procesu wzbogacania AI. Jeśli dopiero zaczynasz pracę, Rozpocznij od [wzbogacania AI na platformie Azure wyszukiwanie poznawcze](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Wprowadzenie do umiejętności

Zestawu umiejętności to zasób wielokrotnego użytku w usłudze Azure Wyszukiwanie poznawcze, który jest dołączony do indeksatora i Określa kolekcję umiejętności używanych do analizowania, przekształcania i wzbogacania zawartości tekstu lub obrazu podczas indeksowania. Umiejętności mają dane wejściowe i wyjściowe, a często dane wyjściowe jednej z nich są dostępne w łańcuchu lub sekwencji procesów.

Zestawu umiejętności ma trzy główne właściwości:

+ `skills`, nieuporządkowana kolekcja umiejętności, dla których platforma Określa sekwencję wykonywania na podstawie danych wejściowych wymaganych dla każdej umiejętności.
+ `cognitiveServices`klucz zasobu Cognitive Services, który wykonuje przetwarzanie obrazów i tekstu dla umiejętności, które zawierają wbudowane umiejętności.
+ `knowledgeStore`(opcjonalnie) konto usługi Azure Storage, na którym będą rzutowane dokumenty wzbogacone. Wzbogacone dokumenty są również używane przez indeksy wyszukiwania.

Umiejętności są tworzone w formacie JSON. Poniższy przykład jest nieco uproszczoną wersją tego [zestawu umiejętności przeglądów hotelowych](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), używany do zilustrowania koncepcji w tym artykule. 

Poniżej przedstawiono dwie pierwsze umiejętności:

+ Umiejętność #1 to [umiejętność tekstu podzielona](cognitive-search-skill-textsplit.md) , która akceptuje zawartość pola "reviews_text" jako dane wejściowe i dzieli tę zawartość na "strony" 5000 znaków jako dane wyjściowe.
+ Kwalifikacja #2 to [umiejętność wykrywania tonacji](cognitive-search-skill-sentiment.md) akceptuje "strony" jako dane wejściowe i tworzy nowe pole o nazwie "tonacji" jako dane wyjściowe zawierające wyniki analizy tonacji.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> Można tworzyć złożone umiejętności z użyciem pętli i rozgałęziania przy użyciu [umiejętności warunkowej](cognitive-search-skill-conditional.md) do tworzenia wyrażeń. Składnia jest oparta na notacji ścieżki [wskaźnika JSON](https://tools.ietf.org/html/rfc6901) z kilkoma modyfikacjami w celu identyfikowania węzłów w drzewie wzbogacania. `"/"`Przechodzi poziom niżej w drzewie i `"*"` działa jako operator for-each w kontekście. Wiele przykładów w tym artykule ilustrują składnię. 

### <a name="enrichment-tree"></a>Drzewo wzbogacania

W trakcie wykonywania [kroków potoku wzbogacania](cognitive-search-concept-intro.md#enrichment-steps)przetwarzanie zawartości następuje w fazie *łamania dokumentu* , w której tekst i obrazy są wyodrębniane ze źródła. Zawartość obrazu można następnie skierować do umiejętności, które określają przetwarzanie obrazu, podczas gdy zawartość tekstowa jest umieszczana w kolejce do przetwarzania tekstu. W przypadku dokumentów źródłowych zawierających duże ilości tekstu można ustawić *tryb analizy* na indeksatorze, aby podzielić tekst na mniejsze fragmenty w celu zoptymalizowania przetwarzania. 

![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Magazyn wiedzy w diagramie potoku")

Gdy dokument znajduje się w potoku wzbogacenia, jest reprezentowany jako drzewo zawartości i powiązane wzbogacania. To drzewo jest tworzone jako dane wyjściowe pęknięcia dokumentu.  Format drzewa wzbogacania umożliwia potokowi wzbogacanie dołączenie metadanych do nawet pierwotnych typów danych, ale nie jest prawidłowym obiektem JSON, ale można go umieścić w prawidłowym formacie JSON. W poniższej tabeli przedstawiono stan dokumentu wprowadzanego do potoku wzbogacania:

|Tryb Source\Parsing danych|Domyślne|JSON, wiersze JSON & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Nie dotyczy |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Nie dotyczy|

 W miarę wykonywania umiejętności Dodaj nowe węzły do drzewa wzbogacania. Te nowe węzły mogą być następnie używane jako dane wejściowe dla umiejętności podrzędnych, projekcja w sklepie wiedzy lub mapowanie do pól indeksu. Wzbogacania nie są modyfikowalne: po utworzeniu węzły nie mogą być edytowane. Ponieważ umiejętności jest bardziej skomplikowany, to drzewo wzbogacania, ale nie wszystkie węzły w drzewie wzbogacania muszą wprowadzić je do indeksu lub sklepu wiedzy. 

Można wybiórczo utrwalać tylko podzbiór wzbogaceń do indeksu lub sklepu z bazami danych.

### <a name="context"></a>Kontekst

Każda umiejętność wymaga kontekstu. Kontekst określa:

+ Liczba wykonań kwalifikacji na podstawie wybranych węzłów. W przypadku wartości kontekstu typu Collection dodanie `/*` na końcu spowoduje, że umiejętność zostanie wywołana raz dla każdego wystąpienia w kolekcji. 

+ Gdzie w drzewie wzbogacania są dodawane dane wyjściowe umiejętności. Dane wyjściowe są zawsze dodawane do drzewa jako elementy podrzędne węzła kontekstu. 

+ Kształt danych wejściowych. W przypadku kolekcji wielopoziomowych ustawienie kontekstu dla kolekcji nadrzędnej wpłynie na kształt danych wejściowych dla umiejętności. Na przykład jeśli masz drzewo wzbogacania z listą krajów/regionów, z których każda została ulepszona z listą Stanów zawierających listę kodów POCZTOWYch.

|Kontekst|Dane wejściowe|Kształt danych wejściowych|Wywołanie umiejętności|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Lista wszystkich kodów POCZTOWYch w kraju/regionie |Raz na kraj/region |
|`/document/countries/*/states/*` |"/Document/countries/*/States/*/ZipCodes/*" " |Lista kodów POCZTOWYch w stanie | Raz na kombinację kraju/regionu i stanu|

## <a name="generate-enriched-data"></a>Generuj wzbogacone dane 

Korzystając z [przeglądów hotelu zestawu umiejętności](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) jako punktu odniesienia, będziemy wyglądać następująco:

+ Jak drzewo wzbogacania rozwija się z wykonywaniem każdej umiejętności
+ Jak kontekst i dane wejściowe działają, aby określić, ile razy jest wykonywane umiejętność
+ Jak kształt danych wejściowych jest oparty na kontekście

"Dokument" w ramach procesu wzbogacania reprezentuje pojedynczy wiersz (przegląd w hotelu) w pliku źródłowym hotel_reviews.csv.

### <a name="skill-1-split-skill"></a>Kwalifikacja #1: podzielona umiejętność

Gdy zawartość źródłowa składa się z dużych fragmentów tekstu, warto podzielić ją na mniejsze składniki, aby uzyskać większą dokładność języka, tonacji i wykrywania fraz kluczy. Dostępne są dwa ziarna: strony i zdania. Strona składa się z około 5000 znaków.

Umiejętność podziału tekstu jest zwykle w zestawu umiejętności.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

W kontekście umiejętności `"/document/reviews_text"` , podzielona umiejętność będzie wykonywana raz dla `reviews_text` . Dane wyjściowe kwalifikacji to lista, w której `reviews_text` znajduje się w segmentach znaków 5000. Dane wyjściowe z podzielonej umiejętności są nazywane `pages` i dodawane do drzewa wzbogacania. `targetName`Funkcja umożliwia zmianę nazwy danych wyjściowych przed ich dodaniem do drzewa wzbogacania.

Drzewo wzbogacania ma teraz nowy węzeł umieszczony w kontekście umiejętności. Ten węzeł jest dostępny dla dowolnego mapowania pól umiejętności, projekcji lub danych wyjściowych. Koncepcyjnie, drzewo wygląda następująco:

![drzewo wzbogacania po rozpoczęciu dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Drzewo wzbogacania po rozpoczęciu dokumentu i przed wykonaniem kwalifikacji")

Węzeł główny dla wszystkich wzbogacań to `"/document"` . Podczas pracy z indeksatorami obiektów BLOB `"/document"` węzeł będzie miał węzły podrzędne `"/document/content"` i `"/document/normalized_images"` . Podczas pracy z danymi CSV, podobnie jak w tym przykładzie, nazwy kolumn są mapowane na węzły poniżej `"/document"` . 

Aby uzyskać dostęp do dowolnych wzbogacań dodanych do węzła przez umiejętność, wymagana jest pełna ścieżka do wzbogacania. Na przykład jeśli chcesz użyć tekstu z ```pages``` węzła jako dane wejściowe do innej umiejętności, musisz określić go jako ```"/document/reviews_text/pages/*"``` .
 
 ![drzewo wzbogacania po #1 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Drzewo wzbogacania po wykonaniu #1 kwalifikacji")

### <a name="skill-2-language-detection"></a>Umiejętność #2 wykrywania języka

Dokumenty recenzowania w hotelu obejmują Opinie klientów wyrażone w wielu językach. Umiejętność wykrywania języka określa, który język jest używany. Następnie wynik jest przesyłany do wyodrębniania fraz kluczowych i wykrywania tonacji, biorąc pod uwagę podczas wykrywania tonacji i fraz.

Chociaż umiejętność wykrywania języka to trzecia (umiejętności #3) umiejętność zdefiniowana w zestawu umiejętności, jest to kolejna umiejętność wykonania. Ponieważ nie jest on blokowany przez wymaganie żadnych danych wejściowych, zostanie wykonany równolegle z poprzednią umiejętnością. Podobnie jak w przypadku podzielonej umiejętności, która je poprzedza, umiejętność wykrywania języka jest również wywoływana jednokrotnie dla każdego dokumentu. Drzewo wzbogacania ma teraz nowy węzeł dla języka.

 ![drzewo wzbogacania po #2 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Drzewo wzbogacania po wykonaniu #2 kwalifikacji")
 
 ### <a name="skill-3-key-phrases-skill"></a>Kwalifikacja #3: umiejętność kluczowych fraz 

W `/document/reviews_text/pages/*` przypadku kontekstu kluczowych fraz kwalifikacji zostanie wywołana jednokrotnie dla każdego elementu w `pages` kolekcji. Dane wyjściowe z umiejętności będą węzłem ze skojarzonym elementem strony. 

 Teraz powinno być możliwe przeszukanie pozostałej części umiejętności w zestawu umiejętności i wizualizowanie sposobu, w jaki drzewo wzbogacania będzie stale rosnąć przy wykonywaniu każdej umiejętności. Niektóre umiejętności, takie jak umiejętność scalania i umiejętność kształtowania, również tworzą nowe węzły, ale używają tylko danych z istniejących węzłów i nie tworzą nowych wzbogaceń netto.

![drzewo wzbogacania po wszystkich umiejętnościach](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Drzewo wzbogacania po wszystkich umiejętnościach")

Kolory łączników w powyższym drzewie wskazują, że wzbogacania zostały utworzone przez różne umiejętności, a węzły muszą być rozkierowane osobno i nie będą częścią obiektu zwracanego podczas wybierania węzła nadrzędnego.

## <a name="save-enrichments"></a>Zapisz wzbogacenia

Na platformie Azure Wyszukiwanie poznawcze indeksator zapisuje dane wyjściowe, które tworzy. Jednym z danych wyjściowych jest zawsze [indeks z możliwością wyszukiwania](search-what-is-an-index.md). Określenie indeksu jest wymagane i po dołączeniu zestawu umiejętności dane pozyskane przez indeks obejmują istotę wzbogacania. Zwykle dane wyjściowe określonych umiejętności, takie jak kluczowe frazy lub wyniki tonacji, są wprowadzane do indeksu w polu utworzonym do tego celu.

Opcjonalnie indeksator może również wysyłać dane wyjściowe do [magazynu wiedzy](knowledge-store-concept-intro.md) do użycia w innych narzędziach lub procesach. Magazyn wiedzy jest definiowany jako część zestawu umiejętności. Definicja tego elementu określa, czy wzbogacone dokumenty są rzutowane jako tabele, czy obiekty (pliki i obiektów BLOB). Projekcje tabelaryczne są dobrze dopasowane do interaktywnej analizy w narzędziach takich jak Power BI, natomiast pliki i obiekty blob są zwykle używane w nauce danych lub podobnych procesach. W tej sekcji dowiesz się, jak kompozycja zestawu umiejętności może kształtować tabele lub obiekty, które chcesz projektować.

### <a name="projections"></a>Projekcje

W przypadku zawartości, która jest przeznaczona dla magazynu wiedzy, należy rozważyć sposób, w jaki zawartość jest strukturalna. *Projekcja* to proces wybierania węzłów z drzewa wzbogacania i tworzenia fizycznego wyrażenia ich w sklepie z bazami danych. Projekcje to niestandardowe kształty dokumentu (zawartość i wzbogacanie), które mogą być wyprowadzane jako projekcje tabeli lub obiektu. Aby dowiedzieć się więcej o pracy z projekcjami, zobacz [Praca z projekcjami](knowledge-store-projection-overview.md).

![Opcje mapowania pól](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opcje mapowania pól dla potoku wzbogacania")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`Element jest używany tylko w danych wejściowych i projekcjach o umiejętnościach. Służy do konstruowania obiektów zagnieżdżonych na wiele poziomów. Może być konieczne utworzenie nowego obiektu w celu przekazania go jako dane wejściowe do umiejętności lub projektu w sklepie merytorycznym. Ponieważ węzły wzbogacania nie mogą być prawidłowym obiektem JSON w drzewie wzbogacania i odwoływania się do węzła w drzewie tylko zwraca ten stan węzła podczas jego tworzenia, przy użyciu wzbogacania jako dane wejściowe lub projekcje wymagane jest utworzenie dobrze sformułowanego obiektu JSON. `sourceContext`Umożliwia konstruowanie obiektu hierarchicznego typu anonimowego, który będzie wymagał wielu umiejętności, jeśli używany jest tylko kontekst. 

Użycie `sourceContext` jest pokazane w poniższych przykładach. Spójrz na dane wyjściowe umiejętności, które wygenerowały wzbogacanie, aby określić, czy jest to prawidłowy obiekt JSON, a nie typ pierwotny.

### <a name="slicing-projections"></a>Projekcje wycinków

Podczas definiowania grupy projekcji tabeli pojedynczy węzeł drzewa wzbogacania można podzielić na wiele powiązanych tabel. Jeśli dodasz tabelę ze ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji tabeli, utworzony węzeł podrzędny nie będzie elementem podrzędnym istniejącej projekcji tabeli, ale zamiast tego będzie rzutowany do nowej, powiązanej tabeli. Ta technika tworzenia wycinków pozwala zdefiniować pojedynczy węzeł w umiejętności kształtu, który może być źródłem dla wszystkich projekcji tabeli. 

### <a name="shaping-projections"></a>Kształtowanie projekcji

Istnieją dwa sposoby definiowania projekcji:

+ Użyj umiejętności kształtu tekstu, aby utworzyć nowy węzeł, który jest węzłem głównym dla wszystkich wzbogaceń. Następnie w projekcjach można odwoływać się tylko do danych wyjściowych umiejętności kształtu.

+ Użyj wbudowanego kształtu rzutowania w samej definicji projekcji.

Podejście kształtujące jest bardziej pełne niż kształtowanie wbudowane, ale zapewnia, że wszystkie mutacje drzewa wzbogacania są zawarte w umiejętnościach i że dane wyjściowe są obiektem, którego można użyć ponownie. W przeciwieństwie do wbudowanego kształtu można utworzyć kształt, który jest wymagany, ale jest obiektem anonimowym i jest dostępny tylko dla projekcji, dla której jest zdefiniowany. Podejścia można używać razem lub oddzielnie. Zestawu umiejętności utworzone dla Ciebie w przepływie pracy portalu zawiera oba te elementy. Używa ona umiejętności kształtu dla projekcji tabeli, ale używa również kształtu wbudowanego do projekcji tabeli wyrażeń kluczowych.

Aby zwiększyć przykład, można wybrać opcję usunięcia kształtu wbudowanego i użyć umiejętności kształtu do utworzenia nowego węzła dla kluczowych fraz. Aby utworzyć kształt podzielony na trzy tabele, mianowicie,, `hotelReviewsDocument` `hotelReviewsPages` , i `hotelReviewsKeyPhrases` , dwie opcje są opisane w poniższych sekcjach.

#### <a name="shaper-skill-and-projection"></a>Umiejętność i projekcja kształtu

> [!Note]
> Niektóre kolumny z tabeli dokumentów zostały usunięte z tego przykładu dla zwięzłości.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

W przypadku `tableprojection` węzła zdefiniowanego w `outputs` powyższej sekcji możemy teraz używać funkcji dzielenia na fragmenty projektu części `tableprojection` węzła w różnych tabelach:

> [!Note]
> Jest to tylko fragment kodu projekcji w ramach konfiguracji magazynu wiedzy.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Projekcje w tekście

Podejście kształtowania wbudowanego nie wymaga umiejętności kształtu, ponieważ wszystkie kształty potrzebne do projekcji są tworzone w czasie, gdy są potrzebne. Aby zaprojektować te same dane co w poprzednim przykładzie, opcja rzutowania wbudowanego będzie wyglądać następująco:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Jednym ze sposobów podejścia jest to, jak wartości `"Keyphrases"` są rzutowane przy użyciu `"sourceContext"` . `"Keyphrases"`Węzeł, który zawiera kolekcję ciągów, jest samym elementem podrzędnym tekstu strony. Jednak ponieważ projekcje wymagają obiektu JSON, a strona jest pierwotna (ciąg), `"sourceContext"` jest używana do zawijania frazy klucza do obiektu z nazwaną właściwością. Ta technika umożliwia niezależne projekcję elementów podstawowych.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie pierwszej zestawu umiejętności z umiejętnościami poznawczymi.

> [!div class="nextstepaction"]
> [Utwórz swój pierwszy zestawu umiejętności](cognitive-search-defining-skillset.md).
