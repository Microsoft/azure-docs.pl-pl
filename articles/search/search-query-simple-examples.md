---
title: Użyj prostej składni zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Zapoznaj się z przykładem, uruchamiając zapytania na podstawie prostej składni wyszukiwania pełnotekstowego, wyszukiwania filtru, wyszukiwania geograficznego, aspektów wyszukiwania w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401750"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Użyj składni wyszukiwania "Simple" w usłudze Azure Wyszukiwanie poznawcze

W przypadku usługi Azure Wyszukiwanie poznawcze [prosta Składnia zapytania](query-simple-syntax.md) wywołuje domyślny Analizator zapytań w celu wyszukiwania pełnotekstowego. Analizator jest szybki i obsługuje często spotykane scenariusze, w tym wyszukiwanie pełnotekstowe, filtrowanie i wyszukiwanie aspektów oraz wyszukiwanie przy użyciu prefiksów. W tym artykule użyto przykładów do zilustrowania prostego użycia składni w żądaniu [przeszukiwania dokumentów (interfejs API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Alternatywna składnia zapytania to [pełna Lucene](query-lucene-syntax.md), obsługujące bardziej złożone struktury zapytań, takie jak rozmyte i wieloznaczne wyszukiwanie. Aby uzyskać więcej informacji i przykładów, zobacz [Korzystanie z pełnej składni Lucene](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Przykłady zadań NYC

Poniższe przykłady wykorzystują [indeks wyszukiwania zadań NYC](https://azjobsdemo.azurewebsites.net/) składający się z zadań dostępnych na podstawie zestawu danych dostarczonego przez [miasto z inicjatywy New York OpenData Initiative](https://nycopendata.socrata.com/). Te dane nie powinny być uważane za bieżące ani ukończone. Indeks znajduje się w usłudze piaskownicy dostarczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Wyszukiwanie poznawcze do wypróbowania tych zapytań.

To, czego potrzebujesz, jest Poster lub równoważne narzędzie do wystawiania żądania HTTP przy POBIERAniu lub WYSYŁAniu. Jeśli nie znasz tych narzędzi, zobacz [Szybki Start: Eksplorowanie interfejsu API REST platformy Azure wyszukiwanie poznawcze](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Skonfiguruj żądanie

1. Nagłówki żądań muszą mieć następujące wartości:

   | Klucz | Wartość |
   |-----|-------|
   | Content-Type | `application/json`|
   | klucz interfejsu API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (jest to rzeczywisty klucz interfejsu API zapytania dla usługi wyszukiwania piaskownicy hostującym indeks zadań NYC) |

1. Ustaw zlecenie na **`GET`** .

1. Ustaw adres URL na **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + Kolekcja Documents na indeksie zawiera całą zawartość do przeszukiwania. Klucz API Query-Key podany w nagłówku żądania działa tylko w przypadku operacji odczytu przeznaczonych dla kolekcji dokumentów.

   + **`$count=true`** Zwraca liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania liczba będzie zawierać wszystkie dokumenty w indeksie (około 2558 w przypadku zadań NYC).

   + **`search=*`** jest nieokreślonym zapytaniem, równoważne wartości null lub pustego wyszukiwania. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwaniem, które można wykonać i zawiera wszystkie pola do pobierania w indeksie, ze wszystkimi wartościami.

1. W ramach kroku weryfikacji Wklej następujące żądanie do GET i kliknij pozycję **Wyślij**. Wyniki są zwracane w postaci pełnych dokumentów JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Jak wywołać proste analizowanie zapytań

W przypadku zapytań interaktywnych nie trzeba określać niczego: wartość domyślna jest prosta. W kodzie, jeśli wcześniej wywołano **`queryType=full`** , można zresetować wartość domyślną przy użyciu **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Przykład 1: wyszukiwanie pełnotekstowe w określonych polach

Pierwszy przykład nie jest specyficzny dla parsera, ale prowadzimy do tego, aby wprowadzić pierwszą podstawową koncepcję zapytania: zawieranie. W tym przykładzie ograniczenie wykonywania zapytania i odpowiedzi do zaledwie kilku określonych pól. Znajomość sposobu tworzenia struktury odpowiedzi w formacie JSON jest ważna, gdy narzędzie jest dostępne po opublikowaniu lub w Eksploratorze wyszukiwania. 

To zapytanie dotyczy tylko *business_title* w **`searchFields`** , określające za pośrednictwem **`select`** parametru tego samego pola w odpowiedzi.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Przykładowa odpowiedź Poster" border="false":::

Być może zauważono wynik wyszukiwania w odpowiedzi. Jednolite Punktacja **1** występuje, gdy nie ma żadnej rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie podano żadnych kryteriów. W przypadku pustego wyszukiwania wiersze są wycofywane w dowolnej kolejności. Jeśli dołączysz rzeczywiste kryteria, wyniki wyszukiwania zostaną rozdzielone na znaczące wartości.

## <a name="example-2-look-up-by-id"></a>Przykład 2: wyszukiwanie według identyfikatora

Gdy zwracasz wyniki wyszukiwania w zapytaniu, logicznym kolejnym krokiem jest udostępnienie strony szczegółów zawierającej więcej pól z dokumentu. W tym przykładzie pokazano, jak zwrócić pojedynczy dokument przy użyciu [operacji wyszukiwania](/rest/api/searchservice/lookup-document) do przekazania identyfikatora dokumentu.

Wszystkie dokumenty mają unikatowy identyfikator. Aby wypróbować składnię zapytania wyszukiwania, najpierw Zwróć listę identyfikatorów dokumentów, aby można było znaleźć jeden do użycia. W przypadku zadań NYC identyfikatory są przechowywane w `id` polu.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Następnie pobierz dokument z kolekcji na podstawie elementu `id` "9E1E3AF9-0660-4e00-AF51-9B654925A2D5", który pojawił się wcześniej w poprzedniej odpowiedzi. Poniższe zapytanie zwraca wszystkie pola do pobierania dla całego dokumentu.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Przykład 3: filtrowanie zapytań

[Składnia filtru](./search-query-odata-filter.md) jest wyrażeniem OData, które może być używane przez siebie lub z **`search`** . Filtr autonomiczny bez parametru wyszukiwania jest przydatny, gdy wyrażenie filtru jest w stanie w pełni kwalifikować dokumenty. Bez ciągu zapytania nie ma analizy leksykalnej ani językowej, brak oceniania (wszystkie wyniki są 1) i bez klasyfikacji. Zwróć uwagę, że ciąg wyszukiwania jest pusty.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Używany razem filtr jest stosowany najpierw do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtru. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrowanie odpowiedzi zapytania" border="false":::

Innym zaawansowanym sposobem łączenia filtru i wyszukiwania jest **`search.ismatch*()`** użycie wyrażenia filtru, w którym można użyć zapytania wyszukiwania w filtrze. To wyrażenie filtru używa symbolu wieloznacznego w *planie* do wybrania business_title, w tym terminu, terminarza, planowania i tak dalej.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Aby uzyskać więcej informacji na temat funkcji, zobacz [Wyszukaj. IsMatch w "Przykłady filtrowania"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Przykład 4: filtry zakresu

Filtrowanie zakresu jest obsługiwane za poorednictwem **`$filter`** wyrażeń dla dowolnego typu danych. Poniższe przykłady przeszukają pola liczbowe i ciągi. 

Typy danych są ważne w filtrach zakresów i działają najlepiej, gdy dane liczbowe znajdują się w polach liczbowych oraz dane ciągu w polach ciągów. Dane liczbowe w polach ciągów nie są odpowiednie dla zakresów, ponieważ ciągi liczbowe nie są porównywalne w usłudze Azure Wyszukiwanie poznawcze.

Następujące zapytanie jest zakresem liczbowym:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtr zakresu dla zakresów liczbowych" border="false":::

W tej kwerendzie zakres znajduje się nad polem ciągu (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtr zakresu dla zakresów tekstu" border="false":::

> [!NOTE]
> Aspektowanie zakresów wartości jest typowym wymaganiem aplikacji wyszukiwania. Aby uzyskać więcej informacji i przykładów, zobacz [How to Build a facet Filter](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Przykład 5: wyszukiwanie geograficzne

Przykładowy indeks zawiera pole geo_location ze współrzędnem szerokości geograficznej i długości geograficznej. W tym przykładzie jest stosowana [Funkcja Geo. Distance](search-query-odata-geo-spatial-functions.md#examples) , która filtruje dokumenty w obrębie obwodu punktu początkowego, do dowolnej odległości (w kilometrach), którą zapewniasz. Możesz dostosować ostatnią wartość zapytania (4), aby zmniejszyć lub powiększyć obszar powierzchni zapytania.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Aby uzyskać bardziej czytelne wyniki, wyniki wyszukiwania są przycinane w celu uwzględnienia tytułu zadania i lokalizacji służbowej. Współrzędne początkowe zostały uzyskane z losowego dokumentu w indeksie (w tym przypadku dla lokalizacji służbowej na Wyspach Staten.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Mapa wyspy Staten" border="false":::

## <a name="example-6-search-precision"></a>Przykład 6: dokładność wyszukiwania

Zapytania warunkowe to pojedyncze terminy, być może wiele z nich, które są oceniane niezależnie. Zapytania frazy są ujęte w znaki cudzysłowu i oceniane jako ciąg Verbatim. Precyzja dopasowania jest kontrolowana przez operatory i searchmode.

Przykład 1: `search=fire`  dopasowuje się do 140 wyników, gdzie wszystkie dopasowania zawierają słowo pożarowe w dokumencie.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Przykład 2: `search=fire department` zwraca 2002 wyników. Dopasowania są zwracane dla dokumentów zawierających ogień lub dział.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Przykład 3: `search="fire department"` zwraca 77 wyników. Załączanie ciągu w cudzysłowie powoduje utworzenie frazy wyszukiwania składającej się z obu warunków i dopasowań w indeksie składającym się z połączonych warunków. W tym artykule wyjaśniono, dlaczego wyszukiwanie `search=+fire +department` nie jest równoważne. Oba warunki są wymagane, ale są skanowane niezależnie. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Ponieważ zapytanie frazowe jest określone za pomocą cudzysłowów, w tym przykładzie dodaje znak ucieczki ( `\` ) w celu zachowania składni.

## <a name="example-7-booleans-with-searchmode"></a>Przykład 7: wartości logiczne z opcją searchmode

Prosta składnia obsługuje operatory logiczne w postaci znaków ( `+, -, |` ). Parametr searchmode umożliwia informowanie kompromisów między precyzją i odwołaniem, dzięki czemu można **`searchMode=any`** przystąpić do odwoływania (dopasowanie dla każdego kryterium uprawnia do dokumentu dla zestawu wyników) i **`searchMode=all`** uzyskać precyzję (wszystkie kryteria muszą być dopasowane). 

Wartość domyślna to **`searchMode=any`** , co może być mylące, jeśli tworzysz zapytanie z wieloma operatorami i uzyskujesz szersze wyniki zamiast węższych wyników. Jest to szczególnie prawdziwe, gdzie wyniki obejmują wszystkie dokumenty "niezawierające" określonego terminu.

Przy użyciu domyślnego ustawienia searchmode (any) zwracane są dokumenty 2800: te zawierające frazę "Straż pożarowa" oraz wszystkie dokumenty, które nie mają frazy "Metrotech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="dowolny tryb wyszukiwania" border="false":::

Zmiana w celu **`searchMode=all`** wymuszenia skumulowanego wpływu na kryteria i zwrócenie mniejszego zestawu wyników — 21 dokumentów — składa się z dokumentów zawierających całą frazę "Straż pożarowa", minus te zadania w adresie Metrotech Center.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Tryb wyszukiwania wszystko" border="false":::

## <a name="example-8-structuring-results"></a>Przykład 8: Tworzenie struktury wyników

Kilka parametrów kontroluje pola, które znajdują się w wynikach wyszukiwania, liczbę dokumentów zwracanych w każdej partii i kolejność sortowania. Ten przykład ilustruje kilka z poprzednich przykładów, ograniczając wyniki do określonych pól przy użyciu **`$select`** instrukcji i kryteriów wyszukiwania Verbatim, zwracając 82 dopasowań.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Dołączenie do poprzedniego przykładu można sortować według tytułu. To sortowanie działa, ponieważ civil_service_title *sortowania w* indeksie.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Wyniki stronicowania są implementowane przy użyciu **`$top`** parametru, w tym przypadku zwracają 5 najważniejszych dokumentów:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Aby uzyskać następny 5, Pomiń pierwszą partię:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

## <a name="next-steps"></a>Następne kroki

Spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniają, jak skonfigurować zapytania wyszukiwania przy użyciu zestawów SDK platformy Azure.

+ [Tworzenie zapytań względem indeksu przy użyciu zestawu .NET SDK](search-get-started-dotnet.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu SDK języka Python](search-get-started-python.md)
+ [Tworzenie zapytań względem indeksu przy użyciu zestawu JavaScript SDK](search-get-started-javascript.md)

Dodatkowe informacje na temat składni, architekturę zapytań i przykłady można znaleźć w następujących linkach:

+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](query-simple-syntax.md)
+ [Pełna składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia filtru](search-query-odata-filter.md)