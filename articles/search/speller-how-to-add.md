---
title: Dodaj sprawdzanie pisowni
titleSuffix: Azure Cognitive Search
description: Przed wykonaniem zapytania Dołącz korekcję pisowni do potoku zapytania, aby naprawić literówki.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: da172e9a7605876711e4a4f32bf4fac698b35109
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694805"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Dodawanie sprawdzania pisowni do zapytań w Wyszukiwanie poznawcze

> [!IMPORTANT]
> Poprawianie pisowni jest w publicznej wersji zapoznawczej, dostępne wyłącznie w wersji zapoznawczej interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Podczas początkowego uruchamiania wersji zapoznawczej nie jest naliczana opłata za program sprawdzania pisowni. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Można poprawić odwołania przez poprawność poszczególnych terminów zapytania wyszukiwania przed uzyskaniem dostępu do aparatu wyszukiwania. Parametr **modułu sprawdzania pisowni** jest obsługiwany dla wszystkich typów zapytań: [prostej](query-simple-syntax.md), [pełnej](query-lucene-syntax.md)i nowej opcji [semantycznej](semantic-how-to-query-request.md) obecnie w publicznej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

+ Istniejący indeks wyszukiwania zawierający zawartość w języku angielskim

+ Klient wyszukiwania do wysyłania zapytań

  Klient wyszukiwania musi obsługiwać interfejsy API REST w wersji zapoznawczej na żądanie zapytania. Możesz użyć programu [Poster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)lub kodu, który został zmodyfikowany, aby zapewnić wywołania REST do interfejsów API w wersji zapoznawczej.

+ [Żądanie zapytania](/rest/api/searchservice/preview-api/search-documents) , które używa korekcji pisowni, ma wartość "API-Version = 2020-06 -30-Preview", "pisownia = Leksykon" i "queryLanguage = pl-US".

  QueryLanguage jest wymagany w przypadku sprawdzania pisowni, a obecnie jedyną prawidłową wartością jest "en-us".

> [!Note]
> Parametr modułu sprawdzania pisowni jest dostępny we wszystkich warstwach w tych samych regionach, które zapewniają wyszukiwanie semantyczne. Nie musisz rejestrować się w celu uzyskania dostępu do tej funkcji w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Poprawianie pisowni przy użyciu prostego wyszukiwania

W poniższym przykładzie zastosowano wbudowany indeks hoteli-przykładowe, aby zademonstrować korekcję pisowni w prostej kwerendzie tekstowej z bezpłatną formą. Bez korekcji pisowni zapytanie zwraca wyniki zerowe. Dzięki korekcie zapytanie zwraca jeden wynik dla Johnsonem zorientowanego na rodzinę.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Poprawianie pisowni z pełnymi Lucene

Poprawianie pisowni odbywa się na poszczególnych terminach zapytania, które przechodzą na analizę, dlatego można użyć parametru modułu sprawdzania pisowni z niektórymi zapytaniami.

+ Niezgodne formularze zapytań, które pomijają analizę tekstu, obejmują: symbol wieloznaczny, wyrażenie regularne, rozmyte
+ Zgodne formularze zapytań obejmują: wyszukiwanie w terenie, bliskość, zwiększenie warunków

W tym przykładzie użyto wyszukiwania pól w polu Kategoria, z pełną składnią Lucene i błędnego terminu zapytania. Dzięki dołączeniu do modułu sprawdzania pisowni literówka w "Suiite" jest korygowana, a zapytanie powiodło się.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Poprawianie pisowni z użyciem wyszukiwania semantycznego

To zapytanie, z literówkami w każdym okresie oprócz jednego, powoduje, że poprawki pisowni zwracają odpowiednie wyniki. Aby dowiedzieć się więcej, zobacz [Tworzenie kwerendy semantycznej](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Zagadnienia dotyczące języka

Parametr queryLanguage wymagany dla narzędzia sprawdzania pisowni musi być zgodny z wszystkimi [analizatorami języka](index-add-language-analyzers.md) przypisanymi do definicji pól w schemacie indeksu. 

+ queryLanguage określa, które leksykony są używane do sprawdzania pisowni i są również używane jako dane wejściowe [algorytmu klasyfikacji semantycznej](semantic-how-to-query-response.md) , jeśli używasz "querytype = semantyka".

+ Analizatory języka są używane podczas indeksowania i wykonywania zapytań, aby znaleźć pasujące dokumenty w indeksie wyszukiwania. Przykładem definicji pola korzystającej z analizatora języka jest `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Aby uzyskać najlepsze wyniki przy korzystaniu z narzędzia sprawdzania pisowni, jeśli queryLanguage jest "en-us", wówczas wszystkie analizatory języka muszą być w języku angielskim ("en. Microsoft" lub "en. Lucene").

> [!NOTE]
> Analizatory języka niezależny od (takie jak słowo kluczowe, proste, standardowe, zatrzymywanie, odstępy lub `standardasciifolding.lucene` ) nie powodują konfliktu z ustawieniami queryLanguage.

W żądaniu zapytania, ustawiana queryLanguage jest stosowana równomiernie z pisownią, odpowiedziami i napisami. Nie istnieje przesłonięcie poszczególnych części.

Gdy zawartość w indeksie wyszukiwania może być złożona w wielu językach, dane wejściowe zapytania najprawdopodobniej są w jednym. Aparat wyszukiwania nie sprawdza zgodności queryLanguage, analizatora języka i języka, w którym składa się zawartość, dlatego należy odpowiednio wprowadzić zakres zapytań, aby uniknąć tworzenia nieprawidłowych wyników.

## <a name="next-steps"></a>Następne kroki

+ [Tworzenie zapytania semantycznego](semantic-how-to-query-request.md)
+ [Tworzenie podstawowego zapytania](search-query-create.md)
+ [Użyj pełnej składni zapytań Lucene](query-Lucene-syntax.md)
+ [Użyj prostej składni zapytania](query-simple-syntax.md)
+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)