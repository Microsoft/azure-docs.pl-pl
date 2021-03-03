---
title: Struktura odpowiedzi semantycznej
titleSuffix: Azure Cognitive Search
description: Opisuje algorytm klasyfikacji semantycznej w Wyszukiwanie poznawcze i sposób struktury "odpowiedzi semantycznych" i "podpisy semantyczne" z zestawu wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680403"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Klasyfikacja i odpowiedzi semantyczne na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Algorytm klasyfikacji semantycznej i odpowiedzi semantyczne są w publicznej wersji zapoznawczej, dostępne wyłącznie w wersji zapoznawczej interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Klasyfikacja semantyczna poprawia dokładność wyników wyszukiwania przez przeklasyfikowanie najważniejszych dopasowań przy użyciu semantycznego modelu klasyfikacji dla zapytań w języku naturalnym, w przeciwieństwie do słów kluczowych.

W tym artykule opisano algorytm klasyfikacji semantycznej i sposób, w jaki jest ona w kształcie semantyki. Odpowiedź zawiera podpisy, zarówno w postaci zwykłego tekstu, jak i z wyróżnieniami, i odpowiedzi (opcjonalnie).

+ Podpisy semantyczne to fragmenty tekstu powiązane z zapytaniem wyodrębnionym z wyników wyszukiwania. Mogą one pomóc w podsumowywaniu wyniku, gdy poszczególne pola zawartości są za duże dla strony wyników. Zawiera napisy semantyki funkcji, dzięki czemu użytkownicy mogą szybko skim wyniki zapytania, aby znaleźć najbardziej odpowiednie dokumenty w taki sposób, aby poprawić środowisko użytkownika.

+ Odpowiedzi semantyczne używają modeli uczenia maszynowego z usługi Bing do formułowania odpowiedzi na zapytania, które wyglądają jak pytania. Odpowiedzi są wybierane z listy fragmentów, które są najbardziej odpowiednie do zapytania, jako wyodrębnione z najważniejszych dokumentów w zestawie wyników zapytania. Odpowiedzi są zwracane jako niezależny obiekt najwyższego poziomu w ładunku odpowiedzi na zapytania, który można wybrać do renderowania na stronach wyszukiwania po stronie wyników wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapytania sformułowane przy użyciu semantycznego typu zapytania. Aby uzyskać więcej informacji, zobacz [Tworzenie kwerendy semantycznej](semantic-how-to-query-request.md).

## <a name="understanding-a-semantic-response"></a>Zrozumienie odpowiedzi semantycznej

Odpowiedź semantyczna zawiera nowe właściwości wyników, podpisów i odpowiedzi. Odpowiedź semantyczna jest tworzona na podstawie standardowej odpowiedzi, przy użyciu pierwszych 50 wyników zwróconych przez [aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md), które są następnie ponownie klasyfikowane przy użyciu rangi semantycznej. Jeśli określono więcej niż 50, dodatkowe wyniki są zwracane, ale nie będą one semantycznie klasyfikowane.

Podobnie jak w przypadku wszystkich zapytań, odpowiedź składa się ze wszystkich pól oznaczonych jako możliwy do pobierania lub tylko tych pól wymienionych w instrukcji SELECT. Zawiera również pole "odpowiedź" i "podpisy".

+ Dla każdego wyniku semantyki domyślnie istnieje jedna "odpowiedź" zwracana jako odrębne pole, które można wybrać do renderowania na stronie wyszukiwania. Można określić maksymalnie pięć. Proces odpowiedzi jest zautomatyzowany: odczytywanie wszystkich dokumentów w początkowych wynikach, wykonywanie podsumowania wyodrębniania, a następnie odczytywanie czytelności maszyn, a wreszcie promowanie bezpośredniej odpowiedzi na pytanie użytkownika w polu odpowiedź.

+ "Caption" to podsumowanie zawartości dokumentu, które są zwracane w postaci zwykłego tekstu lub z wyróżnieniami. Podpisy są dołączane automatycznie i nie można ich pominąć. Najważniejsze informacje są stosowane przy użyciu informacji o czytaniu maszyn, aby identyfikować, które ciągi należy wyróżnić. Najważniejsze elementy przedstawiają uwagę na najbardziej odpowiednie fragmenty, dzięki czemu możesz szybko przeskanować stronę wyników, aby znaleźć odpowiedni dokument.

Uporządkowane w sposób semantyczny zestaw wyników porządkuje wyniki według @search.rerankerScore wartości. Jeśli dodasz klauzulę OrderBy, zostanie zwrócony błąd HTTP 400, ponieważ ta klauzula nie jest obsługiwana w zapytaniu semantycznym.

## <a name="example"></a>Przykład

@search.rerankerScoreIstnieje obok standardu @search.score i służy do zmiany rangi wyników.

Uwzględniając następujące zapytanie:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Można oczekiwać, że zobaczysz następujący wynik, reprezentatywny dla odpowiedzi semantycznej. Wyniki te pokazują tylko trzy pierwsze odpowiedzi, zgodnie z ich klasyfikacją " @search.rerankerScore ". Zwróć uwagę na to, jak Oceanside jest teraz w pierwszej kolejności. W ramach domyślnej klasyfikacji " @search.score " ten wynik zostałby zwrócony w drugim miejscu, po zakończeniu końców.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Następne kroki

+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)
+ [Algorytm podobieństwa](index-ranking-similarity.md)
+ [Tworzenie zapytania semantycznego](semantic-how-to-query-request.md)
+ [Tworzenie podstawowego zapytania](search-query-create.md)