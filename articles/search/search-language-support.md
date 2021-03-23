---
title: Indeksowanie wielu języków dla zapytań wyszukiwania innych niż angielskie
titleSuffix: Azure Cognitive Search
description: Utwórz indeks obsługujący zawartość w wielu językach, a następnie utwórz zapytania należące do zakresu tej zawartości.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801608"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Jak utworzyć indeks dla wielu języków w usłudze Azure Wyszukiwanie poznawcze

Kluczowym wymaganiem w aplikacji wyszukiwania wielojęzycznego jest możliwość wyszukiwania i pobierania wyników w języku użytkownika. Na platformie Azure Wyszukiwanie poznawcze jednym ze sposobów spełnienia wymagań językowych aplikacji wielojęzycznej jest utworzenie dedykowanych pól do przechowywania ciągów w określonym języku, a następnie ograniczenie wyszukiwania pełnotekstowego do tylko tych pól w czasie zapytania.

+ W obszarze definicje pól Ustaw Analizator języka, który wywołuje reguły językowe języka docelowego. Aby wyświetlić pełną listę obsługiwanych analizatorów, zobacz [Dodawanie analizatorów języka](index-add-language-analyzers.md).

+ W żądaniu zapytania ustaw parametry w celu określenia zakresu wyszukiwania pełnotekstowego do określonych pól, a następnie Przytnij wyniki pól, które nie zapewniają zawartości zgodnej z funkcją wyszukiwania, którą chcesz dostarczyć.

Sukces tej techniki zawiasuje integralność zawartości pola. Usługa Azure Wyszukiwanie poznawcze nie tłumaczy ciągów ani nie przeprowadza wykrywania języka w ramach wykonywania zapytania. Należy upewnić się, że pola zawierają ciągi, których oczekujesz.

## <a name="define-fields-for-content-in-different-languages"></a>Definiowanie pól dla zawartości w różnych językach

W Wyszukiwanie poznawcze na platformie Azure zapytania mają jeden indeks docelowy. Deweloperzy, którzy chcą podawać ciągi charakterystyczne dla języka w jednym środowisku wyszukiwania, zwykle definiują dedykowane pola do przechowywania wartości: jedno pole dla ciągów w języku angielskim, jeden dla języka francuskiego itd.

Właściwość "Analizator" w definicji pola służy do ustawiania [analizatora języka](index-add-language-analyzers.md). Zostanie ona użyta do indeksowania i wykonywania zapytań.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Kompilowanie i ładowanie indeksu

Krok pośredni (i prawdopodobnie oczywisty) jest konieczny do [kompilowania i wypełniania indeksu](search-get-started-dotnet.md) przed wyrażeniem zapytania. Ten krok jest tutaj omawiany w celu zapewnienia kompletności. Jednym ze sposobów określania dostępności indeksu jest sprawdzenie listy indeksów w [portalu](https://portal.azure.com).

> [!TIP]
> Wykrywanie języka i tłumaczenie tekstu są obsługiwane podczas pozyskiwania danych przy użyciu [wzbogacania AI](cognitive-search-concept-intro.md) i [umiejętności](cognitive-search-working-with-skillsets.md). Jeśli masz źródło danych platformy Azure z zawartością w języku mieszanym, możesz wypróbować funkcje wykrywania i tłumaczenia języka za pomocą [Kreatora importu danych](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Ogranicz wyniki zapytania i przycinania

Parametry zapytania służą do ograniczania wyszukiwania do określonych pól, a następnie przycinania wyników wszelkich pól, które nie są przydatne dla Twojego scenariusza. 

| Parametry | Przeznaczenie |
|-----------|--------------|
| **searchFields** | Ogranicza wyszukiwanie pełnotekstowe do listy nazwanych pól. |
| **$select** | Przycina odpowiedź w celu uwzględnienia tylko pól, które określisz. Domyślnie zwracane są wszystkie pola do pobierania. Parametr **$SELECT** umożliwia wybranie, które z nich mają być zwracane. |

Uwzględniając cel ograniczenia wyszukiwania do pól zawierających ciągi francuski, użyj **searchFields** , aby docelowa kwerenda w polach zawierających ciągi w tym języku.

Określanie analizatora w żądaniu zapytania nie jest konieczne. Analizator języka w definicji pola będzie zawsze używany podczas przetwarzania zapytania. W przypadku zapytań, które określają wiele pól wywołujących różne analizatory języka, terminy lub frazy będą przetwarzane niezależnie od przypisanych analizatorów dla każdego pola.

Domyślnie wyszukiwanie zwraca wszystkie pola, które są oznaczone jako możliwy do pobierania. W związku z tym możesz chcieć wykluczyć pola, które nie są zgodne ze specyficznymi dla języka interfejsem wyszukiwania, które chcesz podać. W szczególności w przypadku ograniczenia wyszukiwania do pola z ciągami francuskim prawdopodobnie warto wykluczyć z wyników pola z ciągami w języku angielskim. Za pomocą **$SELECT** parametru zapytania daje kontrolę nad tym, które pola są zwracane do aplikacji wywołującej.

#### <a name="example-in-rest"></a>Przykład w REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Przykład w języku C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Zwiększ pola specyficzne dla języka

Czasami język agenta wystawiającego zapytanie nie jest znany, w takim przypadku zapytanie można wystawić dla wszystkich pól jednocześnie. Preferencja IA dla wyników w określonym języku można zdefiniować za pomocą [profilów oceniania](index-add-scoring-profiles.md). W poniższym przykładzie wyniki Znalezione w opisie w języku angielskim będą oceniane w sposób wyższy względem dopasowań w innych językach:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Następnie w żądaniu Search zostanie uwzględniony profil oceniania:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Następne kroki

+ [Analizatory języków](index-add-language-analyzers.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents)
+ [Przegląd wzbogacania AI](cognitive-search-concept-intro.md)
+ [Umiejętności — Omówienie](cognitive-search-working-with-skillsets.md)