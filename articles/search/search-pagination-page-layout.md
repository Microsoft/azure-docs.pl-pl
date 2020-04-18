---
title: Jak pracować z wynikami wyszukiwania
titleSuffix: Azure Cognitive Search
description: Strukturyzuj i sortuj wyniki wyszukiwania, uzyskaj liczbę dokumentów i dodaj nawigację zawartości do wyników wyszukiwania w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f815003449f0600bce1cb8927b92b85b51b09a1
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641618"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search

W tym artykule wyjaśniono, jak uzyskać odpowiedź na zapytanie, która powraca z całkowitą liczbą pasujących dokumentów, na strony wyniki, posortowane wyniki i terminy wyróżnione trafienia.

Struktura odpowiedzi jest określana przez parametry w kwerendzie: [Szukaj dokumentu](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) w interfejsie API REST lub [DocumentSearchResult Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) w pliku .NET SDK.

## <a name="result-composition"></a>Skład wyników

Podczas gdy dokument wyszukiwania może składać się z dużej liczby pól, zazwyczaj tylko kilka z nich jest potrzebnych do reprezentowania każdego dokumentu w zestawie wyników. W żądaniu kwerendy `$select=<field list>` dołącz, aby określić, które pola są wyświetlane w odpowiedzi. Pole musi być przypisane jako możliwe do **pobrania** w indeksie, aby uwzględnić je w wyniku. 

Pola, które działają najlepiej obejmują te, które kontrastują i rozróżniają między dokumentami, zapewniając wystarczające informacje, aby zaprosić odpowiedź klikalności ze strony użytkownika. W witrynie e-commerce może to być nazwa produktu, opis, marka, kolor, rozmiar, cena i ocena. W przypadku wbudowanej próbki indeksu hoteli mogą to być pola w poniższym przykładzie:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Jeśli chcesz dołączyć pliki obrazów w wyniku, takie jak zdjęcie produktu lub logo, należy przechowywać je poza usługą Azure Cognitive Search, ale dołącz pole w indeksie, aby odwołać się do adresu URL obrazu w dokumencie wyszukiwania. Przykładowe indeksy, które obsługują obrazy w wynikach obejmują **demo realestate-sample-us,** prezentowane w tym [przewodniku Szybki start,](search-create-app-portal.md)oraz [aplikację demonstracyjną New York City Jobs.](https://aka.ms/azjobsdemo)

## <a name="paging-results"></a>Stronicowanie wyników

Domyślnie wyszukiwarka zwraca do pierwszych 50 dopasowań, zgodnie z wynikiem wyszukiwania, jeśli kwerenda jest wyszukiwanie pełnotekstowe lub w dowolnej kolejności dla zapytań dopasowania ścisłego.

Aby zwrócić inną liczbę pasujących `$top` `$skip` dokumentów, dodaj i parametry do żądania kwerendy. Na poniższej liście wyjaśniono logikę.

+ Dodaj, `$count=true` aby uzyskać liczbę całkowita liczba pasujących dokumentów w indeksie.

+ Zwraca pierwszy zestaw 15 pasujących dokumentów plus liczbę dopasowań:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Powrót drugiego seta, pomijając pierwsze 15, aby `$top=15&$skip=15`uzyskać następne 15: . Zrób to samo dla trzeciego zestawu 15:`$top=15&$skip=30`

Wyniki nagiezonowych zapytań nie są gwarantowane być stabilne, jeśli indeks bazowy zmienia. Stronicowanie zmienia `$skip` wartość dla każdej strony, ale każda kwerenda jest niezależna i działa w bieżącym widoku danych, ponieważ istnieje w indeksie w czasie kwerendy (innymi słowy, nie ma buforowania lub migawki wyników, takich jak te znalezione w bazie danych ogólnego przeznaczenia).
 
Oto przykład tego, jak możesz uzyskać duplikaty. Załóżmy indeks z czterema dokumentami:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Teraz załóżmy, że chcesz wyniki zwracane dwa na raz, uporządkowane według klasyfikacji. Należy wykonać tę kwerendę, aby uzyskać `$top=2&$skip=0&$orderby=rating desc`pierwszą stronę wyników: , uzyskując następujące wyniki:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
W usłudze załóżmy, że piąty dokument jest `{ "id": "5", "rating": 4 }`dodawany do indeksu między wywołaniami zapytaniami: .  Wkrótce potem należy wykonać kwerendę, aby `$top=2&$skip=2&$orderby=rating desc`pobrać drugą stronę: , i uzyskać następujące wyniki:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Należy zauważyć, że dokument 2 jest pobierany dwa razy. Dzieje się tak dlatego, że nowy dokument 5 ma większą wartość oceny, więc sortuje przed dokumentem 2 i ląduje na pierwszej stronie. Chociaż to zachowanie może być nieoczekiwane, jest typowe dla zachowania wyszukiwarki.

## <a name="ordering-results"></a>Porządkowanie wyników

W przypadku zapytań tekstowych wyniki są automatycznie klasyfikowane według wyniku wyszukiwania, obliczanego na podstawie częstotliwości terminów i bliskości w dokumencie, przy czym wyższe wyniki są kierowane do dokumentów mających więcej lub silniejsze dopasowania w wyszukiwanym terminie. 

Wyniki wyszukiwania dają ogólne poczucie trafności, odzwierciedlając siłę dopasowania w porównaniu z innymi dokumentami w tym samym zestawie wyników. Wyniki nie zawsze są spójne z jednego zapytania do następnego, więc podczas pracy z kwerendami można zauważyć niewielkie rozbieżności w sposobie zamawiania dokumentów wyszukiwania. Istnieje kilka wyjaśnień, dlaczego może to nastąpić.

| Przyczyna | Opis |
|-----------|-------------|
| Zmienność danych | Zawartość indeksu różni się w miarę dodawania, modyfikowania lub usuwania dokumentów. Częstotliwości terminów będą się zmieniać w miarę przetwarzania aktualizacji indeksu w czasie, co wpływa na wyniki wyszukiwania pasujących dokumentów. |
| Wiele replik | W przypadku usług korzystających z wielu replik zapytania są wystawiane równolegle dla każdej repliki. Statystyki indeksu używane do obliczania wyniku wyszukiwania są obliczane na podstawie repliki, a wyniki są scalane i uporządkowane w odpowiedzi na zapytanie. Repliki są głównie lustrami siebie nawzajem, ale statystyki mogą się różnić ze względu na niewielkie różnice w stanie. Na przykład jedna replika mogła usunąć dokumenty przyczyniające się do ich statystyk, które zostały scalone z innych replik. Zazwyczaj różnice w statystykach dla repliki są bardziej zauważalne w mniejszych indeksach. |
| Identyczne wyniki | Jeśli wiele dokumentów ma ten sam wynik, każdy z nich może pojawić się jako pierwszy.  |

### <a name="consistent-ordering"></a>Spójne porządkowanie

Biorąc pod uwagę flex w kolejności wyników, można zbadać inne opcje, jeśli spójność jest wymaganiem aplikacji. Najprostszym rozwiązaniem jest sortowanie według wartości pola, takiej jak ocena lub data. W przypadku scenariuszy, w których chcesz sortować według określonego pola, takiego jak klasyfikacja lub data, można jawnie zdefiniować [ `$orderby` wyrażenie](query-odata-filter-orderby-syntax.md), które można zastosować do dowolnego pola, które jest indeksowane jako **sortowalne**.

Inną opcją jest użycie [niestandardowego profilu oceniania](index-add-scoring-profiles.md). Profile punktacji dają większą kontrolę nad rankingiem elementów w wynikach wyszukiwania, z możliwością promowania dopasowań znalezionych w określonych polach. Dodatkowa logika oceniania może pomóc zastąpić drobne różnice między replikami, ponieważ wyniki wyszukiwania dla każdego dokumentu są dalej od siebie. Zalecamy [algorytm rankingu](index-ranking-similarity.md) dla tego podejścia.

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Podświetlanie trafień odnosi się do formatowania tekstu (takiego jak pogrubienie lub żółte podświetlenia) zastosowanego do pasującego terminu w wyniku, co ułatwia wykrycie dopasowania. Instrukcje wyróżniania trafienia znajdują się w [żądaniu kwerendy](https://docs.microsoft.com/rest/api/searchservice/search-documents). Wyszukiwarka zawiera pasujący termin w `highlightPreTag` `highlightPostTag`tagach i , a kod obsługuje odpowiedź (na przykład stosowanie czcionki pogrubionej).

Formatowanie jest stosowane do kwerend cały termin. W poniższym przykładzie terminy "piaszczysty", "piasek", "plaże", "plaża" znajdujące się w polu Opis są oznaczone do podświetlania. Kwerendy, które wyzwalają rozszerzenie kwerendy w silniku, takie jak wyszukiwanie rozmyte i wieloznaczne, mają ograniczoną obsługę wyróżniania trafień.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nowe zachowanie (od 15 lipca)

Usługi utworzone po 15 lipca 2020 r. zapewnią inne wrażenia z wyróżniania. Usługi utworzone przed tą datą nie zmienią się w ich zachowaniu wyróżniania. 

Z nowym zachowaniem:

* Zwracane będą tylko frazy pasujące do kwerendy pełnej frazy. Zapytanie "super bowl" zwróci podkreśla tak:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Należy pamiętać, że termin *miska żetonów* nie ma żadnych podświetlenia, ponieważ nie pasuje do pełnej frazy.
  
* Będzie można określić rozmiar fragmentu zwrócony dla podświetlenia. Rozmiar fragmentu jest określony jako liczba znaków (maksymalna wynosi 1000 znaków).

Podczas pisania kodu klienta, który implementuje naciśnięcie wyróżniania, należy pamiętać o tej zmianie. Należy zauważyć, że nie będzie to miało wpływu na ciebie, chyba że utworzysz zupełnie nową usługę wyszukiwania.

## <a name="next-steps"></a>Następne kroki

Aby szybko wygenerować stronę wyszukiwania dla klienta, należy wziąć pod uwagę następujące opcje:

+ [Generator aplikacji](search-create-app-portal.md)w portalu tworzy stronę HTML z paskiem wyszukiwania, nawigacją aspektową i obszarem wyników zawierającym obrazy.
+ [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-create-first-app.md) jest samouczek, który tworzy klienta funkcjonalnego. Przykładowy kod pokazuje nagie kwerendy, wyróżnianie trafień i sortowanie.

Kilka przykładów kodu zawiera interfejs frontonu sieci Web, który można znaleźć tutaj: [Aplikacja demonstracyjna New York City Jobs,](https://aka.ms/azjobsdemo) [przykładowy kod JavaScript z witryną demonstracyjnej na żywo](https://github.com/liamca/azure-search-javascript-samples)i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
