---
title: Omówienie podobieństwa i oceniania
titleSuffix: Azure Cognitive Search
description: Wyjaśnia koncepcje podobieństwa i oceniania oraz to, co deweloper może zrobić, aby dostosować wynik oceniania.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 5bd1a9111528146224561995feaecf54612a1c78
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535665"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Podobieństwo i ocenianie na platformie Azure Wyszukiwanie poznawcze

Ocenianie odnosi się do obliczenia wyniku wyszukiwania dla każdego elementu zwróconego w wynikach wyszukiwania dla kwerend wyszukiwania pełnotekstowego. Wynik jest wskaźnikiem istotności elementu w kontekście bieżącej operacji wyszukiwania. Im wyższy wynik, tym bardziej istotny element. W wynikach wyszukiwania elementy mają rangę uporządkowaną od wysokiej do niskiego poziomu na podstawie wyników wyszukiwania obliczonych dla każdego elementu. 

Domyślnie górne 50 są zwracane w odpowiedzi, ale można użyć parametru **$Top** , aby zwrócić mniejszą lub większą liczbę elementów (maksymalnie 1000 w ramach jednej odpowiedzi) i **$Skip** , aby uzyskać następny zestaw wyników.

Wynik wyszukiwania jest obliczany na podstawie właściwości statystycznych danych i zapytania. Usługa Azure Wyszukiwanie poznawcze wyszukuje dokumenty pasujące do wyszukiwanych terminów (niektóre lub wszystkie, w zależności od opcji [searchmode](/rest/api/searchservice/search-documents#searchmodeany--all-optional)), preferując dokumenty zawierające wiele wystąpień wyszukiwanego terminu. Wynik wyszukiwania jest nawet wyższy, jeśli termin jest rzadki w indeksie danych, ale jest często używany w dokumencie. Podstawą tego podejścia do obliczania istotności jest znana wartość *TF-IDF lub* Term częstotliwość odwracania dokumentu.

Wartości wyniku wyszukiwania można powtarzać w zestawie wyników. Gdy wiele trafień ma ten sam wynik wyszukiwania, kolejność tych samych elementów oceny nie jest zdefiniowana i nie jest stabilna. Uruchom ponownie zapytanie i możesz zobaczyć pozycje zmiany położenia, zwłaszcza jeśli używasz bezpłatnej usługi lub płatnej usługi z wieloma replikami. W przypadku dwóch elementów o identycznym wyniku nie ma gwarancji, która jest wyświetlana w pierwszej kolejności.

Jeśli chcesz przerwać powiązanie między powtarzanymi wynikami, możesz dodać klauzulę **$OrderBy** do pierwszej kolejności według wyniku, a następnie zamówić według innego pola do sortowania (na przykład `$orderby=search.score() desc,Rating desc` ). Aby uzyskać więcej informacji, zobacz [$OrderBy](./search-query-odata-orderby.md).

> [!NOTE]
> A `@search.score = 1.00` wskazuje zestaw wyników z niewskaźnikiem oceny lub bez rangi. Wynik jest jednolity dla wszystkich wyników. Wyniki nieoceniane pojawiają się, gdy formularz kwerendy jest wyszukiwaniem rozmytym, symbolami wieloznacznymi lub kwerendami regularnymi lub wyrażeniem **$Filter** . 

## <a name="scoring-profiles"></a>Profile oceniania

Można dostosować sposób, w jaki różne pola są klasyfikowane przez definiowanie niestandardowego *profilu oceniania*. Profile oceniania zapewniają większą kontrolę nad klasyfikacją elementów w wynikach wyszukiwania. Na przykład możesz chcieć poprawić elementy w oparciu o potencjalną przychody, podwyższyć poziom nowych elementów lub ewentualnie poprawić elementy, które zostały zbyt długie. 

Profil oceniania jest częścią definicji indeksu składającą się z pól ważonych, funkcji i parametrów. Aby uzyskać więcej informacji o definiowaniu tego elementu, zobacz [Profile oceniania](index-add-scoring-profiles.md).

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Statystyki oceniania i sesje programu Sticky Notes

W celu zapewnienia skalowalności usługa Azure Wyszukiwanie poznawcze dystrybuuje każdy indeks w poziomie za pomocą procesu fragmentowania, co oznacza, że [fragmenty indeksu są fizycznie oddzielone](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

Domyślnie wynik dokumentu jest obliczany na podstawie właściwości statystycznych danych *w fragmentu*. Takie podejście zwykle nie jest problemem w przypadku dużych korpus danych i zapewnia lepszą wydajność niż Obliczanie wyniku w oparciu o informacje we wszystkich fragmentówach. Wspomniane przy użyciu tej optymalizacji wydajności mogą spowodować, że dwa bardzo podobne dokumenty (lub nawet identyczne dokumenty) kończą się z różnymi wynikami istotności, jeśli zakończą się one w różnych fragmentówach.

Jeśli wolisz obliczyć wynik na podstawie właściwości statystycznych we wszystkich fragmentów, możesz to zrobić przez dodanie *scoringStatistics = Global* jako [parametru zapytania](/rest/api/searchservice/search-documents) (lub dodać *"scoringStatistics": "Global"* jako parametr treści [żądania zapytania](/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
Użycie scoringStatistics gwarantuje, że wszystkie fragmentów w tej samej replice będą mieć te same wyniki. Oznacza to, że różne repliki mogą się nieco różnić od siebie, ponieważ zawsze są aktualizowane przy użyciu najnowszych zmian wprowadzonych w indeksie. W niektórych scenariuszach można chcieć, aby użytkownicy mieli bardziej spójne wyniki w trakcie "sesji zapytań". W takich scenariuszach można podać `sessionId` jako część zapytań. `sessionId`Jest to unikatowy ciąg tworzony w celu odwoływania się do unikatowej sesji użytkownika.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
Tak długo, jak `sessionId` to samo jest używane, nastąpi próba uzyskania najlepszego nakładu pracy na tę samą replikę, co zwiększa spójność wyników widocznych dla użytkowników. 

> [!NOTE]
> Wielokrotne użycie tych samych `sessionId` wartości może zakłócać Równoważenie obciążenia żądań w replikach i niekorzystnie wpłynąć na wydajność usługi wyszukiwania. Wartość użyta jako identyfikator sesji nie może rozpoczynać się od znaku "_".

## <a name="similarity-ranking-algorithms"></a>Algorytmy klasyfikacji podobieństwa

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa różne algorytmy klasyfikacji podobieństwa: *klasyczny algorytm podobieństwa* i oficjalną implementację algorytmu *Okapi BM25* (obecnie w wersji zapoznawczej). Klasyczny algorytm podobieństwa jest algorytmem domyślnym, ale od 15 lipca nowe usługi utworzone po tej dacie korzystają z nowego algorytmu BM25. Będzie on jedynym algorytmem dostępnym w nowych usługach.

Na razie możesz określić algorytm klasyfikacji podobieństwa, którego chcesz użyć. Aby uzyskać więcej informacji, zobacz [algorytm klasyfikacji](index-ranking-similarity.md).

Poniższy segment wideo szybko przekazuje do wyjaśnień dotyczących algorytmów klasyfikacji używanych w usłudze Azure Wyszukiwanie poznawcze. Możesz obejrzeć pełny film wideo, aby uzyskać więcej informacji.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>Features — parametr (wersja zapoznawcza)

Żądania [przeszukiwania dokumentów](/rest/api/searchservice/preview-api/search-documents) mają nowy parametr [Features](/rest/api/searchservice/preview-api/search-documents#featuresmode) , który może dostarczyć dodatkowych informacji na temat istotności na poziomie pola. W `@searchScore` przypadku obliczenia wartości dla dokumentu (w zależności od tego dokumentu w kontekście tego zapytania) można uzyskać informacje dotyczące poszczególnych pól, które zostały przedstawione w `@search.features` strukturze. Struktura zawiera wszystkie pola używane w zapytaniu (określone pola za pomocą **searchFields** w zapytaniach lub wszystkie pola, które są przypisywane do **wyszukiwania** w indeksie). Dla każdego pola uzyskuje się następujące wartości:

+ Liczba unikatowych tokenów znalezionych w polu
+ Wynik podobieństwa lub miara podobieństwa zawartości pola, względem terminu zapytania
+ Częstotliwość okresu lub liczba znalezionych terminów zapytania w polu

W przypadku zapytania przeznaczonego dla pól "Description" i "title" odpowiedź obejmująca następujące elementy `@search.features` mogą wyglądać następująco:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

Te punkty danych można wykorzystać w [niestandardowych rozwiązaniach do oceniania](https://github.com/Azure-Samples/search-ranking-tutorial) lub użyć tych informacji do debugowania problemów dotyczących istotności.


## <a name="see-also"></a>Zobacz też

 [Profile oceniania](index-add-scoring-profiles.md) — informacje o interfejsie [API](/rest/api/searchservice/) [wyszukiwania dokumenty API](/rest/api/searchservice/search-documents) [Azure wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search)