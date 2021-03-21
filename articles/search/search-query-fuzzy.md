---
title: Wyszukiwanie rozmyte
titleSuffix: Azure Cognitive Search
description: Zaimplementuj środowisko wyszukiwania "Czy chodziło", aby poprawić błędne wyrażenie lub literówki.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694788"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Wyszukiwanie rozmyte pozwala poprawić błędy pisowni i literówki

Usługa Azure Wyszukiwanie poznawcze obsługuje Wyszukiwanie rozmyte, typ zapytania, który umożliwia kompensowanie literówki i błędnie napisanych wyrazów w ciągu wejściowym. Jest to możliwe dzięki skanowaniu pod kątem warunków mających podobną kompozycję. Rozwijanie wyszukiwania w celu poprawienia dopasowań ma wpływ na funkcję Autokorekty literówki, gdy niezgodność jest zaledwie kilka nieprawidłowych znaków. 

## <a name="what-is-fuzzy-search"></a>Co to jest Wyszukiwanie rozmyte?

Jest to ćwiczenie rozszerzające, które tworzy dopasowanie na warunkach mających podobną kompozycję. Gdy jest określone Wyszukiwanie rozmyte, aparat tworzy wykres (oparty na [deterministyczniu skończonej usługi Automation teoretycznej](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) o podobnym znaczeniu, dla wszystkich pełnych terminów w zapytaniu. Na przykład, jeśli zapytanie zawiera trzy terminy "University of Waszyngton", wykres jest tworzony dla każdego terminu w zapytaniu `search=university~ of~ washington~` (nie ma usuwania wyrazów przerywanych w przypadku wyszukiwania rozmytego, więc "z" pobiera wykres).

Wykres składa się z maksymalnie 50 ekspansji lub permutacji każdego warunku, przechwytując jednocześnie poprawne i niepoprawne warianty w procesie. Aparat następnie zwraca najbardziej odpowiednie dopasowania w odpowiedzi. 

Na przykład termin "Uniwersytet" może mieć postać "unversty, Universty, University, Universe". Wszystkie dokumenty, które pasują do tych w grafie, są uwzględniane w wynikach. W przeciwieństwie do innych zapytań, które analizują tekst w celu obsługi różnych form tego samego wyrazu ("mysz" i "mysz"), porównania w zapytaniu rozmytym są wykonywane z wartością kroju bez żadnej analizy językowej tekstu. "Universe" i "odwrotne", które różnią się semantycznie, są zgodne, ponieważ rozbieżności składniowe są małe.

Dopasowanie powiedzie się, jeśli rozbieżności są ograniczone do dwóch lub mniejszych modyfikacji, gdzie Edycja jest wstawiana, usuwana, zastępowana lub transponowana. Algorytm korekcji ciągu określający różnicę jest metryką [odległości Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) , opisaną jako "minimalna liczba operacji (wstawienia, usunięcia, podstawienia lub transpozycji dwóch sąsiadujących znaków) wymagana do zmiany jednego wyrazu na drugi". 

Na platformie Azure Wyszukiwanie poznawcze:

+ Zapytanie rozmyte ma zastosowanie do całych warunków, ale można obsługiwać frazy i konstrukcje. Na przykład "Unviersty ~ of ~" Wshington ~ "byłoby zgodne z" University of Waszyngton ".

+ Domyślna odległość edycji wynosi 2. Wartość `~0` oznacza brak rozwinięcia (tylko dokładny termin jest uważany za dopasowanie), ale można określić `~1` jeden stopień różnic lub jedną edycję. 

+ Zapytanie rozmyte może rozwijać termin do 50 dodatkowych permutacji. Ten limit nie jest konfigurowalny, ale można skutecznie zmniejszyć liczbę rozszerzeń, zmniejszając odległość edycji do 1.

+ Odpowiedzi składają się z dokumentów zawierających odpowiednie dopasowanie (do 50).

Zbiorczo wykresy są przekazywane jako kryteria dopasowywania do tokenów w indeksie. Jak można wyobrazić, Wyszukiwanie rozmyte jest zależne od innych formularzy zapytań. Rozmiar i złożoność indeksu mogą określać, czy korzyści są wystarczające do przesunięcia opóźnienia odpowiedzi.

> [!NOTE]
> Ponieważ Wyszukiwanie rozmyte jest powolne, może być wartościowa, aby zbadać alternatywy, takie jak indeksowanie n-gramowe, wraz z postępem sekwencji krótkich znaków (dwie i trzy sekwencje znaków dla tokenów dwugramowych i trigram). W zależności od używanego języka i powierzchni zapytań, n-gram może zapewnić lepszą wydajność. Jest to, że indeksowanie n-gramowe jest bardzo intensywnie czasochłonne i generuje znacznie większe indeksy.
>
> Kolejną alternatywą, którą można wziąć pod uwagę, jeśli chcesz obsłużyć tylko najbardziej egregiouse przypadki, będzie to [Mapa synonimu](search-synonyms.md). Na przykład Mapuj "Wyszukaj" na "wyszukiwanie, serch, Sarch" lub "Pobierz" na "pobranie".

## <a name="indexing-for-fuzzy-search"></a>Indeksowanie dla wyszukiwania rozmytego

Analizatory nie są używane podczas przetwarzania zapytań w celu utworzenia grafu rozwinięcia, ale nie oznacza to, że analizatory powinny być ignorowane w scenariuszach wyszukiwania rozmytego. Wszystkie analizatory są używane podczas indeksowania do tworzenia tokenów, dla których wykonywane jest dopasowywanie, niezależnie od tego, czy zapytanie jest w postaci bezpłatnej, filtrowane wyszukiwanie, czy też wyszukiwania rozmytego z wykresem jako dane wejściowe. 

Ogólnie rzecz biorąc, podczas przypisywania analizatorów dla poszczególnych pól decyzja o dostosowaniu łańcucha analizy jest oparta na podstawowym przypadku użycia (filtr lub wyszukiwanie pełnotekstowe) zamiast wyspecjalizowanych formularzy zapytań, takich jak Wyszukiwanie rozmyte. Z tego powodu nie ma określonego zalecenia analizatora dla wyszukiwania rozmytego. 

Jednakże jeśli zapytania testowe nie wytwarzają oczekiwanych odpowiedników, można spróbować użyć analizatora indeksowania, ustawiając go na [Analizator języka](index-add-language-analyzers.md), aby zobaczyć, czy wyniki są lepsze. Niektóre języki, szczególnie te z mutacją samogłosek, mogą skorzystać z przegięcia i nietypowych wyrazów wygenerowanych przez procesory Microsoft Natural Language. W niektórych przypadkach przy użyciu odpowiedniego analizatora języka można określić, czy termin jest tokenem w taki sposób, który jest zgodny z wartością dostarczoną przez użytkownika.

## <a name="how-to-use-fuzzy-search"></a>Jak używać wyszukiwania rozmytego

Zapytania rozmyte są konstruowane przy użyciu pełnej składni zapytań Lucene, wywołując [Analizator zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Ustaw pełny parser Lucene dla zapytania ( `queryType=full` ).

1. Opcjonalnie należy ograniczyć zakres żądania do określonych pól przy użyciu tego parametru ( `searchFields=<field1,field2>` ). 

1. Dołącz operator tyldy ( `~` ) na końcu całego okresu ( `search=<string>~` ).

   Dołącz opcjonalny parametr, liczbę z zakresu od 0 do 2 (domyślnie), jeśli chcesz określić odległość do edycji ( `~1` ). Na przykład "Blue ~" lub "Blue ~ 1" zwróci "Blue", "Blues" i "Glue".

Na platformie Azure Wyszukiwanie poznawcze, poza terminem i odległością (maksymalnie 2), nie ma żadnych dodatkowych parametrów do ustawienia dla zapytania.

> [!NOTE]
> Podczas przetwarzania zapytania nierozmyte zapytania nie przechodzą na [analizę leksykalną](search-lucene-query-architecture.md#stage-2-lexical-analysis). Dane wejściowe zapytania są dodawane bezpośrednio do drzewa zapytań i rozwinięte w celu utworzenia grafu warunków. Jedyną przekształceniem jest mała wielkość liter.

## <a name="testing-fuzzy-search"></a>Testowanie wyszukiwania rozmytego

W przypadku prostych testów zalecamy [Eksplorator wyszukiwania](search-explorer.md) lub [notkę](search-get-started-rest.md) do iteracji w wyrażeniu zapytania. Oba narzędzia są interaktywne, co oznacza, że możesz szybko przejść przez wiele wariantów okresu i oszacować odpowiedzi z powrotem.

Gdy wyniki są niejednoznaczne, [wyróżnianie trafień](search-pagination-page-layout.md#hit-highlighting) może ułatwić identyfikację dopasowania w odpowiedzi. 

> [!Note]
> Użycie wyróżniania trafień w celu identyfikowania dopasowań rozmytej ma ograniczenia i działa tylko dla podstawowego wyszukiwania rozmytego. Jeśli indeks zawiera profile oceniania lub jeśli zostanie wybrana warstwa zapytania z dodatkową składnią, wyróżnianie trafień może zakończyć się niepowodzeniem, aby zidentyfikować dopasowanie. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Przykład 1: Wyszukiwanie rozmyte z dokładnym terminem

Załóżmy, że następujący ciąg istnieje w `"Description"` polu w dokumencie wyszukiwania: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Rozpocznij od rozmytego wyszukiwania w "specjalny" i Dodaj wyróżnianie trafień do pola Opis:

```console
search=special~&highlight=Description
```

W odpowiedzi, ponieważ dodano podświetlanie trafień, formatowanie jest stosowane do wyrażenia "Special" jako kryterium dopasowywania.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Spróbuj ponownie wykonać żądanie, wpisując polecenie "Special" (specjalne), pobierając kilka liter ("PE"):

```console
search=scial~&highlight=Description
```

Do tej pory odpowiedź nie zostanie zmieniona. Korzystając z wartości domyślnej o długości 2 stopni, usunięcie dwóch znaków "PE" z "specjalnych" nadal umożliwia pomyślne dopasowanie w tym okresie.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Po ponowieniu kolejnych żądań zmodyfikuj termin wyszukiwania, pobierając jeden ostatni znak z sumy trzech usunięć (od "Special" do "skalę"):

```console
search=scal~&highlight=Description
```

Zwróć uwagę, że zwracana jest taka sama odpowiedź, ale teraz zamiast dopasowywania do "specjalnych" dopasowanie rozmyte jest w "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Tym rozwiniętym przykładem jest zilustrowanie przejrzystości, którą wyróżnianie trafień może doprowadzić do niejednoznacznych wyników. We wszystkich przypadkach zwracany jest ten sam dokument. Czy korzystasz z identyfikatorów dokumentów w celu sprawdzenia dopasowania, być może pominięto zmianę z "Special" na "SQL".

## <a name="see-also"></a>Zobacz też

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Wyszukiwanie poznawcze (architektura analizy zapytań)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania w programie .NET](./search-get-started-dotnet.md)
+ [Jak wykonywać zapytania w usłudze REST](./search-get-started-powershell.md)