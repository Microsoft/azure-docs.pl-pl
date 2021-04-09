---
title: Omówienie podobieństwa i oceniania
titleSuffix: Azure Cognitive Search
description: Wyjaśnia koncepcje podobieństwa i oceniania oraz to, co deweloper może zrobić, aby dostosować wynik oceniania.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 72243f896b2cf7dbab61a42514bee634da28d4c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676324"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Podobieństwo i ocenianie na platformie Azure Wyszukiwanie poznawcze

W tym artykule opisano dwa algorytmy klasyfikacji podobieństwa na platformie Azure Wyszukiwanie poznawcze. Wprowadzono w nim również dwie powiązane funkcje: *Profile oceniania* (kryteria dostosowywania wyniku wyszukiwania) i parametr *featuremode* (rozpakuje wynik wyszukiwania, aby wyświetlić więcej szczegółów). 

Trzeci algorytm ponownej klasyfikacji semantycznej jest obecnie w publicznej wersji zapoznawczej. Więcej informacji można znaleźć w temacie [Omówienie wyszukiwania semantycznego](semantic-search-overview.md).

## <a name="similarity-ranking-algorithms"></a>Algorytmy klasyfikacji podobieństwa

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa algorytmy klasyfikacji podobieństwa.

| Algorytm | Wynik | Dostępność |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Używane przez wszystkie usługi wyszukiwania do 15 lipca 2020. |
| BM25Similarity | @search.score | Używane przez wszystkie usługi wyszukiwania utworzone po 15 lipca. Starsze usługi, które domyślnie korzystają z opcji klasycznych, mogą [zrezygnować z BM25](index-ranking-similarity.md). |

Zarówno klasycznym, jak i BM25 to funkcje pobierania podobne do IDF, które wykorzystują częstotliwość okresów (TF) i odwrotną częstotliwość dokumentu (IDF) jako zmienne do obliczenia ocen istotności dla każdej pary dokumentów-zapytań, która jest następnie używana do klasyfikowania, a koncepcyjnie podobna do klasycznego, BM25 pobiera jego element główny w celu usprawnienia. BM25 oferuje także zaawansowane opcje dostosowywania, takie jak umożliwienie użytkownikowi decydowania, jak Ocena istotności skaluje się z terminem częstotliwości pasujących warunków.

Poniższy segment wideo szybko przekazuje do wyjaśnienia ogólnie dostępnych algorytmów klasyfikacji używanych w usłudze Azure Wyszukiwanie poznawcze. Możesz obejrzeć pełny film wideo, aby uzyskać więcej informacji.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Ocenianie istotności

Ocenianie odnosi się do obliczenia wyniku wyszukiwania dla każdego elementu zwróconego w wynikach wyszukiwania dla kwerend wyszukiwania pełnotekstowego. Wynik jest wskaźnikiem istotności elementu w kontekście bieżącego zapytania. Im wyższy wynik, tym bardziej istotny element. W wynikach wyszukiwania elementy mają rangę uporządkowaną od wysokiej do niskiego poziomu na podstawie wyników wyszukiwania obliczonych dla każdego elementu. Wynik jest zwracany w odpowiedzi jako " @search.score " w każdym dokumencie.

Domyślnie górne 50 są zwracane w odpowiedzi, ale można użyć parametru **$Top** , aby zwrócić mniejszą lub większą liczbę elementów (maksymalnie 1000 w ramach jednej odpowiedzi) i **$Skip** , aby uzyskać następny zestaw wyników.

Wynik wyszukiwania jest obliczany na podstawie właściwości statystycznych danych i zapytania. Usługa Azure Wyszukiwanie poznawcze wyszukuje dokumenty pasujące do wyszukiwanych terminów (niektóre lub wszystkie, w zależności od opcji [searchmode](/rest/api/searchservice/search-documents#query-parameters)), preferując dokumenty zawierające wiele wystąpień wyszukiwanego terminu. Wynik wyszukiwania jest nawet wyższy, jeśli termin jest rzadki w indeksie danych, ale jest często używany w dokumencie. Podstawą tego podejścia do obliczania istotności jest znana wartość *TF-IDF lub* Term częstotliwość odwracania dokumentu.

Wartości wyniku wyszukiwania można powtarzać w zestawie wyników. Gdy wiele trafień ma ten sam wynik wyszukiwania, kolejność tych samych elementów oceny nie jest zdefiniowana i nie jest stabilna. Uruchom ponownie zapytanie i możesz zobaczyć pozycje zmiany położenia, zwłaszcza jeśli używasz bezpłatnej usługi lub płatnej usługi z wieloma replikami. W przypadku dwóch elementów o identycznym wyniku nie ma gwarancji, która jest wyświetlana w pierwszej kolejności.

Jeśli chcesz przerwać powiązanie między powtarzanymi wynikami, możesz dodać klauzulę **$OrderBy** do pierwszej kolejności według wyniku, a następnie zamówić według innego pola do sortowania (na przykład `$orderby=search.score() desc,Rating desc` ). Aby uzyskać więcej informacji, zobacz [$OrderBy](search-query-odata-orderby.md).

> [!NOTE]
> A `@search.score = 1.00` wskazuje zestaw wyników z niewskaźnikiem oceny lub bez rangi. Wynik jest jednolity dla wszystkich wyników. Wyniki nieoceniane pojawiają się, gdy formularz kwerendy jest wyszukiwaniem rozmytym, symbolami wieloznacznymi lub kwerendami regularnymi lub wyrażeniem **$Filter** .

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

## <a name="scoring-profiles"></a>Profile oceniania

Można dostosować sposób, w jaki różne pola są klasyfikowane przez definiowanie *profilu oceniania*. Profile oceniania zapewniają większą kontrolę nad klasyfikacją elementów w wynikach wyszukiwania. Na przykład możesz chcieć poprawić elementy w oparciu o potencjalną przychody, podwyższyć poziom nowych elementów lub ewentualnie poprawić elementy, które zostały zbyt długie. 

Profil oceniania jest częścią definicji indeksu składającą się z pól ważonych, funkcji i parametrów. Aby uzyskać więcej informacji o definiowaniu tego elementu, zobacz [Profile oceniania](index-add-scoring-profiles.md).

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

+ [Profile oceniania](index-add-scoring-profiles.md)
+ [Dokumentacja interfejsu API REST](/rest/api/searchservice/)
+ [Interfejs API dokumentów wyszukiwania](/rest/api/searchservice/search-documents)
+ [Azure Wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search)