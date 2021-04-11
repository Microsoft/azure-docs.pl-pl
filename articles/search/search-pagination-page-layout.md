---
title: Jak korzystać z wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: Tworzenie struktury i sortowanie wyników wyszukiwania, pobieranie liczby dokumentów i Dodawanie nawigacji zawartości do wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 92db62622c37241a76d7847931df030162de8f00
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504230"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak korzystać z wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze

W tym artykule wyjaśniono, jak sformułować odpowiedź na zapytanie w usłudze Azure Wyszukiwanie poznawcze. Struktura odpowiedzi jest określana przez parametry w pliku Query: [Search](/rest/api/searchservice/Search-Documents) w interfejsie API REST lub w [klasie SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) w zestawie .NET SDK. Parametry zapytania mogą służyć do tworzenia struktury zestawu wyników w następujący sposób:

+ Ogranicz lub Przetwarzaj liczbę dokumentów w wynikach (domyślnie 50)
+ Wybierz pola do uwzględnienia w wynikach
+ Kolejność wyników
+ Wyróżnij pasujący cały lub częściowy termin w treści wyników wyszukiwania

## <a name="result-composition"></a>Kompozycja wyniku

Chociaż dokument wyszukiwania może składać się z dużej liczby pól, zazwyczaj jest konieczne, aby reprezentować każdy dokument w zestawie wyników. W żądaniu zapytania Dołącz, `$select=<field list>` Aby określić, które pola są wyświetlane w odpowiedzi. Pole musi być przypisywane jako możliwe do **pobierania** w indeksie, który ma zostać uwzględniony w wyniku. 

Pola, które najlepiej sprawdzają się, obejmują te, które różnią się od dokumentów, i zawierają wystarczające informacje, aby zaprosić odpowiedź na kliknięcie w części użytkownika. W witrynie handlu elektronicznego może to być nazwa produktu, opis, marka, kolor, rozmiar, Cena i klasyfikacja. W przypadku przykładu hoteli-Sample-index wbudowane może być polami w następującym przykładzie:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Jeśli chcesz dołączyć pliki obrazów w wyniku, takie jak zdjęcie lub logo produktu, przechowuj je poza usługą Azure Wyszukiwanie poznawcze, ale Dołącz pole w indeksie, aby odwołać się do adresu URL obrazu w dokumencie wyszukiwania. Przykładowe indeksy obsługujące obrazy w wynikach obejmują demonstrację **realestate-Sample-US** , proponowaną w tym [przewodniku szybki start](search-create-app-portal.md)i [aplikację demonstracyjną w Nowym Jorku](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Porady dotyczące nieoczekiwanych wyników

Czasami substancja, a nie struktura wyników, nie są oczekiwane. Gdy wyniki zapytania są nieoczekiwane, możesz spróbować wykonać te modyfikacje kwerendy, aby zobaczyć, czy rezultaty rosną:

+ Zmień **`searchMode=any`** (domyślnie), aby **`searchMode=all`** wymagać dopasowania wszystkich kryteriów zamiast kryteriów. Jest to szczególnie prawdziwe, gdy operatory logiczne są uwzględnione w zapytaniu.

+ Eksperymentuj z różnymi analizatorami leksykalnymi lub analizatorami niestandardowymi, aby zobaczyć, czy zmienia wynik zapytania. Analizator domyślny spowoduje rozbicie wyrazów z łącznikiem i zmniejszenie wyrazów do formularzy głównych, co zwykle zwiększa niezawodność odpowiedzi na zapytanie. Jeśli jednak zachodzi potrzeba zachowania łączników lub jeśli ciągi zawierają znaki specjalne, może być konieczne skonfigurowanie analizatorów niestandardowych w celu upewnienia się, że indeks zawiera tokeny w odpowiednim formacie. Aby uzyskać więcej informacji, zobacz [częściowe wyszukiwanie terminów i wzorce zawierające znaki specjalne (łączniki, symbole wieloznaczne, wyrażenia regularne, wzorce)](search-query-partial-matching.md).

## <a name="paging-results"></a>Stronicowanie wyników

Domyślnie aparat wyszukiwania zwraca do pierwszych 50 dopasowań, zgodnie z ustaleniami wynik wyszukiwania, jeśli zapytanie jest wyszukiwaniem pełnotekstowym lub w dowolnej kolejności w celu dokładnego dopasowania zapytań.

Aby zwrócić inną liczbę pasujących dokumentów, Dodaj `$top` Parametry i `$skip` do żądania zapytania. Poniższa lista zawiera opis logiki.

+ Dodaj, `$count=true` Aby uzyskać licznik łącznej liczby pasujących dokumentów w indeksie.

+ Zwróć pierwszy zestaw 15 pasujących dokumentów oraz liczbę wszystkich dopasowań: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Zwróć drugi zestaw, pomijając pierwsze 15, aby uzyskać następny 15: `$top=15&$skip=15` . Wykonaj te same czynności dla trzeciego zestawu 15: `$top=15&$skip=30`

Wyniki zapytań z podziałem na strony nie są gwarantowane w przypadku zmiany podstawowego indeksu. Stronicowanie zmienia wartość `$skip` dla każdej strony, ale każde zapytanie jest niezależne i działa w bieżącym widoku danych, tak jak istnieje w indeksie w czasie zapytania (innymi słowy, nie istnieje buforowanie ani migawka wyników, na przykład te znajdujące się w bazie danych ogólnego przeznaczenia).
 
Poniżej znajduje się przykład, w jaki sposób można uzyskać duplikaty. Załóżmy, że indeks ma cztery dokumenty:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Teraz Załóżmy, że wyniki zwracane są dwa naraz, uporządkowane według klasyfikacji. Wykonanie tego zapytania spowoduje uzyskanie pierwszej strony z wynikami: `$top=2&$skip=0&$orderby=rating desc` , generując następujące wyniki:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
Załóżmy, że w usłudze zostanie dodany piąty dokument do indeksu między wywołaniami zapytań: `{ "id": "5", "rating": 4 }` .  Wkrótce należy wykonać zapytanie w celu pobrania drugiej strony: `$top=2&$skip=2&$orderby=rating desc` i uzyskać następujące wyniki:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Zauważ, że dokument 2 jest pobierany dwa razy. Wynika to z faktu, że nowy dokument 5 ma większą wartość dla klasyfikacji, więc sortuje przed dokument 2 i grunty na pierwszej stronie. Chociaż takie zachowanie może być nieoczekiwane, typowe jest zachowanie aparatu wyszukiwania.

## <a name="ordering-results"></a>Porządkowanie wyników

W przypadku kwerend wyszukiwania pełnotekstowego wyniki są automatycznie klasyfikowane według wyniku wyszukiwania, obliczone na podstawie częstotliwości i bliskości dokumentu (pochodzącego z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)), z wyższymi ocenami do dokumentów mających większe lub silniejsze dopasowania w wyszukiwanym terminie. 

Wyniki wyszukiwania dają ogólny sens istotności, odzwierciedlając siłę dopasowania względem innych dokumentów w tym samym zestawie wyników. Jednak wyniki nie zawsze są spójne z jednego zapytania do następnego, dlatego podczas pracy z zapytaniami można zauważyć niewielkie rozbieżność w zakresie uporządkowania dokumentów wyszukiwania. Istnieje kilka wyjaśnień, dlaczego taka sytuacja może wystąpić.

| Przyczyna | Opis |
|-----------|-------------|
| Lotność danych | Zawartość indeksu jest różna w miarę dodawania, modyfikowania lub usuwania dokumentów. Okresowe częstotliwości będą zmieniane, ponieważ aktualizacje indeksów są przetwarzane w czasie, wpływając na wyniki wyszukiwania pasujących dokumentów. |
| Wiele replik | W przypadku usług korzystających z wielu replik zapytania są wysyłane równolegle do każdej repliki. Statystyki indeksu używane do obliczania wyniku wyszukiwania są obliczane na podstawie repliki, a wyniki są scalane i uporządkowane w odpowiedzi na zapytanie. Repliki są w większości lustrzane duplikaty, ale statystyki mogą się różnić z powodu małych różnic w stanie. Na przykład jedna replika może mieć usunięte dokumenty wpływające na statystyki, które zostały scalone z innych replik. Zwykle różnice w statystyce dla każdej repliki są bardziej zauważalne w mniejszych indeksach. |
| Identyczne wyniki | Jeśli wiele dokumentów ma ten sam wynik, każdy z nich może być wyświetlany jako pierwszy.  |

### <a name="how-to-get-consistent-ordering"></a>Jak uzyskać spójną kolejność

Jeśli spójne porządkowanie jest wymaganiem aplikacji, można jawnie zdefiniować [ **`$orderby`** wyrażenie](query-odata-filter-orderby-syntax.md) dla pola. Tylko pola, które są indeksowane jako **`sortable`** mogą służyć do porządkowania wyników. Pola, które są często używane w **`$orderby`** polach klasyfikacji, daty i lokalizacji w przypadku określenia wartości **`orderby`** parametru, aby uwzględnić nazwy pól i wywołania [**`geo.distance()` funkcji**](query-odata-filter-orderby-syntax.md) dla wartości geoprzestrzennych.

Inne podejście, które promuje spójność, używa [niestandardowego profilu oceniania](index-add-scoring-profiles.md). Profile oceniania zapewniają większą kontrolę nad klasyfikacją elementów w wynikach wyszukiwania, dzięki czemu można poprawić dopasowania znalezione w określonych polach. Dodatkowa logika oceniania może pomóc w zastępowaniu drobnych różnic między replikami, ponieważ wyniki wyszukiwania dla każdego dokumentu są od siebie oddzielone. Zalecamy stosowanie [algorytmu klasyfikacji](index-ranking-similarity.md) dla tego podejścia.

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Podświetlanie trafień odnosi się do formatowania tekstu (takiego jak pogrubienie lub żółtych świateł) stosowanych do dopasowywania warunków w wyniku, co ułatwia dopasowanie. Instrukcje wyróżniania trafień są dostępne w [żądaniu zapytania](/rest/api/searchservice/search-documents). 

Aby włączyć podświetlanie trafień, Dodaj, `highlight=[comma-delimited list of string fields]` Aby określić, które pola będą używać wyróżniania. Wyróżnianie jest przydatne w przypadku dłuższych pól zawartości, takich jak pole opisu, gdzie dopasowanie nie jest od razu oczywiste. Tylko definicje pól, które są przypisywane jako **kryterium wyszukiwania** , kwalifikują się do wyróżniania trafień.

Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca maksymalnie pięć świateł na pole. Możesz dostosować tę liczbę, dołączając do pola średnik, po którym następuje liczba całkowita. Na przykład `highlight=Description-10` zwraca do 10 świateł na pasującej zawartości w polu Opis.

Formatowanie jest stosowane do zapytań w całym okresie. Typ formatowania jest określany przez Tagi `highlightPreTag` i `highlightPostTag` , a kod obsługuje odpowiedź (na przykład zastosowanie pogrubionej czcionki lub żółtego tła).

W poniższym przykładzie warunki "piaskowobrązowy", "piasek", "plażach", "sekwencje" Znalezione w polu opisu są oznaczone do wyróżniania. Zapytania wyzwalające rozszerzanie zapytania w aparacie, takie jak rozmyte i wieloznaczne wyszukiwanie, mają ograniczoną obsługę wyróżniania trafień.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nowe zachowanie (od 15 lipca)

Usługi utworzone po 15 lipca 2020 będą zapewniały inne środowisko wyróżniania. Usługi utworzone przed tą datą nie zmienią się w sposób ich wyróżniania. 

Z nowym zachowaniem:

+ Zostaną zwrócone tylko frazy pasujące do zapytania o pełną frazę. Fraza zapytania "pucharowa" zwróci następujące elementy:

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  Należy zauważyć, że inne wystąpienia funkcji *Super* i *Puchar* nie mają żadnego wyróżnienia, ponieważ te wystąpienia nie pasują do pełnej frazy.

Podczas pisania kodu klienta, który implementuje podświetlanie trafień, należy pamiętać o tej zmianie. Należy zauważyć, że nie będzie to miało wpływu na to, chyba że zostanie utworzona zupełnie nowa usługa wyszukiwania.

## <a name="next-steps"></a>Następne kroki

Aby szybko wygenerować stronę wyszukiwania dla klienta, należy wziąć pod uwagę następujące opcje:

+ [Generator aplikacji](search-create-app-portal.md)w portalu tworzy stronę HTML z paskiem wyszukiwania, nawigacją aspektową i obszarem wyników zawierającym obrazy.
+ [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-create-first-app.md) to samouczek, który kompiluje klienta funkcjonalnego. Przykładowy kod ilustruje zapytania z podziałem na strony, wyróżnianie trafień i sortowanie.

Kilka przykładów kodu obejmuje interfejs frontonu sieci Web, który można znaleźć tutaj: [aplikacja demonstracyjna zadań w Nowym Jorku](https://aka.ms/azjobsdemo), [przykładowy kod JavaScript z aktywną witryną demonstracyjną](https://github.com/liamca/azure-search-javascript-samples)i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
