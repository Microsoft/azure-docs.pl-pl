---
title: Tworzenie prostego zapytania
titleSuffix: Azure Cognitive Search
description: Zapoznaj się z przykładem, uruchamiając zapytania na podstawie prostej składni wyszukiwania pełnotekstowego, wyszukiwania filtru, wyszukiwania geograficznego, aspektów wyszukiwania w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: e2c6f627c69316b8f146d3ac82b8d29801ec3902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91740687"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Tworzenie prostego zapytania na platformie Azure Wyszukiwanie poznawcze

W przypadku usługi Azure Wyszukiwanie poznawcze [prosta Składnia zapytania](query-simple-syntax.md) wywołuje domyślny Analizator zapytań służący do wykonywania zapytań wyszukiwania pełnotekstowego w odniesieniu do indeksu. Ten parser jest szybki i obsługuje często spotykane scenariusze, w tym wyszukiwanie pełnotekstowe, filtrowanie i wyszukiwanie aspektów oraz wyszukiwanie geograficzne. 

W tym artykule użyto przykładów do zilustrowania prostej składni, wypełniając `search=` parametr operacji [wyszukiwania dokumentów](/rest/api/searchservice/search-documents) .

Alternatywna składnia zapytania to [pełna Lucene](query-lucene-syntax.md), obsługujące bardziej złożone struktury zapytań, takie jak rozmyte i wieloznaczne wyszukiwanie, które może zająć więcej czasu. Aby uzyskać więcej informacji i przykładów demonstrujących pełną składnię, zobacz [Używanie pełnej składni Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formułowanie żądań w programie Poster

Poniższe przykłady wykorzystują indeks wyszukiwania zadań NYC składający się z zadań dostępnych na podstawie zestawu danych dostarczonego przez [miasto z inicjatywy New York OpenData](https://nycopendata.socrata.com/) Initiative. Te dane nie powinny być uważane za bieżące ani ukończone. Indeks znajduje się w usłudze piaskownicy dostarczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Wyszukiwanie poznawcze do wypróbowania tych zapytań.

To, czego potrzebujesz, jest to Poster lub równoważne narzędzie do wystawiania żądań HTTP na potrzeby pobierania. Aby uzyskać więcej informacji, zobacz [Szybki Start: Eksplorowanie interfejsu API REST platformy Azure wyszukiwanie poznawcze przy użyciu programu Poster](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Ustawianie nagłówka żądania

1. W nagłówku żądania ustaw wartość w **polu Typ zawartości** na `application/json` .

2. Dodaj **klucz API-Key**i ustaw go na ten ciąg: `252044BE3886FE4A8E3BAA4F595114BB` . Jest to klucz zapytania dla usługi wyszukiwania piaskownicy hostującym indeks zadań NYC.

Po określeniu nagłówka żądania można użyć go ponownie dla wszystkich zapytań w tym artykule, zamieniając tylko ciąg **Search =** String. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

### <a name="set-the-request-url"></a>Ustaw adres URL żądania

Żądanie jest poleceniem GET z adresem URL zawierającym punkt końcowy Wyszukiwanie poznawcze platformy Azure i ciąg wyszukiwania.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

Kompozycja adresów URL ma następujące elementy:

+ **`https://azs-playground.search.windows.net/`** jest usługą wyszukiwania piaskownicy obsługiwaną przez zespół deweloperów Wyszukiwanie poznawcze platformy Azure. 
+ **`indexes/nycjobs/`** jest indeksem zadań NYC w kolekcji Indexs tej usługi. W żądaniu wymagane są zarówno nazwę usługi, jak i indeks.
+ **`docs`** jest kolekcją dokumentów zawierającą całą zawartość przeszukiwaną. Klucz API Query-Key podany w nagłówku żądania działa tylko na operacjach odczytu dla kolekcji Documents.
+ **`api-version=2020-06-30`** Ustawia wersję interfejsu API, która jest parametrem wymaganym dla każdego żądania.
+ **`search=*`** jest ciągiem zapytania, który w początkowym zapytaniu ma wartość null, zwracając pierwsze wyniki 50 (domyślnie).

## <a name="send-your-first-query"></a>Wysyłanie pierwszego zapytania

W ramach kroku weryfikacji Wklej następujące żądanie do GET i kliknij pozycję **Wyślij**. Wyniki są zwracane w postaci pełnych dokumentów JSON. Zwracane są całe dokumenty, które umożliwiają wyświetlanie wszystkich pól i wszystkich wartości.

Wklej ten adres URL do klienta REST jako krok walidacji i Wyświetl strukturę dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Ciąg zapytania, **`search=*`** , jest nieokreślonym wyszukiwaniem równym null lub pustego wyszukiwania. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwaniem, które można wykonać.

Opcjonalnie możesz dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów pasujących do kryteriów wyszukiwania. W przypadku pustego ciągu wyszukiwania jest to wszystkie dokumenty w indeksie (około 2800 w przypadku zadań NYC).

## <a name="how-to-invoke-simple-query-parsing"></a>Jak wywołać proste analizowanie zapytań

W przypadku zapytań interaktywnych nie trzeba określać niczego: wartość domyślna jest prosta. W kodzie, jeśli wcześniej wywołano **querytype = Full** dla pełnej składni zapytań, można zresetować wartość domyślną przy użyciu typu **querytype = Simple**.

## <a name="example-1-field-scoped-query"></a>Przykład 1: zapytanie o zakres pola

Pierwszy przykład nie jest specyficzny dla parsera, ale prowadzimy do tego, aby wprowadzić pierwszą podstawową koncepcję zapytania: zawieranie. To przykładowe zakresy wykonywania zapytania i odpowiedzi na kilka określonych pól. Znajomość sposobu tworzenia struktury odpowiedzi w formacie JSON jest ważna, gdy narzędzie jest dostępne po opublikowaniu lub w Eksploratorze wyszukiwania. 

W przypadku zwięzłości zapytanie dotyczy tylko pola *business_title* i określa tylko tytuły biznesowe są zwracane. Składnia jest **searchFields** , aby ograniczyć wykonywanie zapytania tylko do pola **business_title i określić, które** pola są uwzględniane w odpowiedzi.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=*
```

W tym samym zapytaniu z wieloma polami na liście rozdzielanej przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpowiedź na to zapytanie powinna wyglądać podobnie do poniższego zrzutu ekranu.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

Być może zauważono wynik wyszukiwania w odpowiedzi. Jednolite Punktacja 1 występuje, gdy nie ma żadnej rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie zastosowano żadnych kryteriów. W przypadku wyszukiwania wartości null bez kryteriów wiersze są przywracane w dowolnej kolejności. Jeśli dołączysz rzeczywiste kryteria, wyniki wyszukiwania zostaną rozdzielone na znaczące wartości.

## <a name="example-2-look-up-by-id"></a>Przykład 2: wyszukiwanie według identyfikatora

Ten przykład jest nietypowy, ale podczas oceniania zachowań wyszukiwania można chcieć sprawdzić całą zawartość określonego dokumentu, aby zrozumieć, dlaczego został on uwzględniony lub wykluczony z wyników. Aby w całości zwrócić pojedynczy dokument, użyj [operacji Lookup](/rest/api/searchservice/lookup-document) do przekazania identyfikatora dokumentu.

Wszystkie dokumenty mają unikatowy identyfikator. Aby wypróbować składnię zapytania wyszukiwania, najpierw Zwróć listę identyfikatorów dokumentów, aby można było znaleźć jeden do użycia. W przypadku zadań NYC identyfikatory są przechowywane w `id` polu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

Następny przykład to zapytanie wyszukiwania zwracające określony dokument na podstawie `id` "9E1E3AF9-0660-4e00-AF51-9B654925A2D5", który pojawił się wcześniej w poprzedniej odpowiedzi. Poniższe zapytanie zwraca cały dokument, a nie tylko wybrane pola. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Przykład 3: filtrowanie zapytań

[Składnia filtru](./search-query-odata-filter.md) jest wyrażeniem OData, które może być używane z **wyszukiwaniem** lub przez siebie. Filtr autonomiczny bez parametru wyszukiwania jest przydatny, gdy wyrażenie filtru jest w stanie w pełni kwalifikować dokumenty. Bez ciągu zapytania nie ma analizy leksykalnej ani językowej, brak oceniania (wszystkie wyniki są 1) i bez klasyfikacji. Zwróć uwagę, że ciąg wyszukiwania jest pusty.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Używany razem filtr jest stosowany najpierw do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtru. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

Jeśli chcesz wypróbować tę opcję w programie Poster przy użyciu polecenia GET, możesz wkleić ten ciąg:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Innym zaawansowanym sposobem łączenia filtru i wyszukiwania jest **`search.ismatch*()`** użycie wyrażenia filtru, w którym można użyć zapytania wyszukiwania w filtrze. To wyrażenie filtru używa symbolu wieloznacznego w *planie* do wybrania business_title, w tym terminu, terminarza, planowania i tak dalej.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Aby uzyskać więcej informacji na temat funkcji, zobacz [Wyszukaj. IsMatch w "Przykłady filtrowania"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Przykład 4: filtry zakresu

Filtrowanie zakresu jest obsługiwane za poorednictwem **`$filter`** wyrażeń dla dowolnego typu danych. Poniższe przykłady przeszukają pola liczbowe i ciągi. 

Typy danych są ważne w filtrach zakresów i działają najlepiej, gdy dane liczbowe znajdują się w polach liczbowych oraz dane ciągu w polach ciągów. Dane liczbowe w polach ciągów nie są odpowiednie dla zakresów, ponieważ ciągi liczbowe nie są porównywalne w usłudze Azure Wyszukiwanie poznawcze. 

Poniższe przykłady są w formacie POST na potrzeby czytelności (zakres liczbowy, po którym następuje tekst):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

Możesz również wypróbować je w programie Poster przy użyciu polecenia GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Aspektowanie zakresów wartości jest typowym wymaganiem aplikacji wyszukiwania. Aby uzyskać więcej informacji i przykładów dotyczących tworzenia filtrów dla struktur nawigacyjnych aspektów, zobacz ["filtrowanie oparte na zakresie" w artykule *jak zaimplementować element nawigacji aspektowej*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Przykład 5: wyszukiwanie geograficzne

Przykładowy indeks zawiera pole geo_location ze współrzędnem szerokości geograficznej i długości geograficznej. W tym przykładzie jest stosowana [Funkcja Geo. Distance](./search-query-odata-geo-spatial-functions.md#examples) , która filtruje dokumenty w obrębie obwodu punktu początkowego, do dowolnej odległości (w kilometrach), którą zapewniasz. Możesz dostosować ostatnią wartość zapytania (4), aby zmniejszyć lub powiększyć obszar powierzchni zapytania.

Poniższy przykład jest w formacie POST, aby można było uzyskać czytelność:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Aby uzyskać bardziej czytelne wyniki, wyniki wyszukiwania są przycinane w celu uwzględnienia identyfikatora zadania, stanowiska zadania i lokalizacji służbowej. Współrzędne początkowe zostały uzyskane z losowego dokumentu w indeksie (w tym przypadku dla lokalizacji służbowej na Wyspach Staten.

Możesz również wypróbować tę usługę w programie Poster przy użyciu polecenia GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Przykład 6: dokładność wyszukiwania

Zapytania warunkowe to pojedyncze terminy, być może wiele z nich, które są oceniane niezależnie. Zapytania frazy są ujęte w znaki cudzysłowu i oceniane jako ciąg Verbatim. Precyzja dopasowania jest kontrolowana przez operatory i searchmode.

Przykład 1: **`&search=fire`**  zwraca 150 wyników, gdzie wszystkie dopasowania zawierają słowo pożar w dokumencie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Przykład 2: **`&search=fire department`** zwraca 2002 wyników. Dopasowania są zwracane dla dokumentów zawierających ogień lub dział.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Przykład 3: **`&search="fire department"`** zwraca 82 wyników. Ciąg ujęty w cudzysłów to Verbatim wyszukiwanie w obu terminach i dopasowań w indeksie składającym się z połączonych warunków. W tym artykule wyjaśniono, dlaczego wyszukiwanie **`search=+fire +department`** nie jest równoważne. Oba warunki są wymagane, ale są skanowane niezależnie. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Przykład 7: wartości logiczne z opcją searchmode

Prosta składnia obsługuje operatory logiczne w postaci znaków ( `+, -, |` ). Parametr searchmode umożliwia informowanie kompromisów między precyzją i odwołaniem, dzięki czemu można `searchMode=any` przystąpić do odwoływania (dopasowanie dla każdego kryterium uprawnia do dokumentu dla zestawu wyników) i `searchMode=all` uzyskać precyzję (wszystkie kryteria muszą być dopasowane). Wartość domyślna to `searchMode=any` , co może być mylące, jeśli tworzysz zapytanie z wieloma operatorami i uzyskujesz szersze wyniki zamiast węższych wyników. Jest to szczególnie prawdziwe, gdzie wyniki obejmują wszystkie dokumenty "niezawierające" określonego terminu.

Przy użyciu domyślnego ustawienia searchmode (any) zwracane są dokumenty 2800: te zawierające termin "Straż częściowa" i wszystkie dokumenty, które nie mają terminu "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

Zmiana opcji searchmode w celu `all` wymuszenia skumulowanego wpływu na kryteria i zwrócenie mniejszego zestawu wyników — 21 dokumentów — składających się z dokumentów zawierających całą frazę "Straż pożarowa", minus te zadania w adresie Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Parametry zestawu nagłówka żądania Poster" border="false":::

## <a name="example-8-structuring-results"></a>Przykład 8: Tworzenie struktury wyników

Kilka parametrów kontroluje pola, które znajdują się w wynikach wyszukiwania, liczbę dokumentów zwracanych w każdej partii i kolejność sortowania. Ten przykład ilustruje kilka poprzednich przykładów, ograniczając wyniki do określonych pól przy użyciu instrukcji **$SELECT** i kryteriów wyszukiwania Verbatim, zwracając 82 dopasowań 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Dołączenie do poprzedniego przykładu można sortować według tytułu. To sortowanie działa, ponieważ civil_service_title *sortowania w* indeksie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Wyniki stronicowania są implementowane przy użyciu **$Top** parametru, w tym przypadku zwracają 5 najważniejszych dokumentów:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Aby uzyskać następny 5, Pomiń pierwszą partię:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Następne kroki
Spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniają, jak skonfigurować zapytania wyszukiwania dla platformy .NET i interfejsu API REST przy użyciu domyślnej prostej składni.

* [Tworzenie zapytań względem indeksu przy użyciu zestawu .NET SDK](./search-get-started-dotnet.md)
* [Tworzenie zapytań względem indeksu przy użyciu interfejsu API REST](./search-get-started-powershell.md)

Dodatkowe informacje na temat składni, architekturę zapytań i przykłady można znaleźć w następujących linkach:

+ [Przykłady zapytań składni Lucene w celu tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełne zapytanie Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Składnia filtru i OrderBy](/rest/api/searchservice/odata-expression-syntax-for-azure-search)