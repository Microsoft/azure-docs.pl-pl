---
title: Klasyfikacja semantyczna
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak działa algorytm klasyfikacji semantycznej na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: c3a0a8bd5805757b92e3f5b046335c8883b4ba72
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888927"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Klasyfikacja semantyczna na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Funkcje wyszukiwania semantycznego znajdują się w publicznej wersji zapoznawczej, dostępne za pomocą interfejsu API REST i portalu. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Te funkcje są rozliczane. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Klasyfikacja semantyczna to rozszerzenie potoku wykonywania zapytania, które zwiększa precyzję przez przeklasyfikowanie górnych dopasowań pierwszego zestawu wyników. Klasyfikacja semantyczna jest oparta na dużych sieciach opartych na transformatorach, przeszkolonych do przechwytywania znaczenia semantyki terminów zapytania, a w przeciwieństwie do lingwistycznego dopasowania słów kluczowych. W przeciwieństwie do [domyślnego algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md), ranga semantyczna używa kontekstu i znaczenia wyrazów do określenia istotności.

Klasyfikacja semantyczna to zarówno zasób, jak i czasochłonne. Aby zakończyć przetwarzanie w oczekiwanym opóźnieniu operacji zapytania, dane wejściowe rangi semantycznej są konsolidowane i zmniejszane, aby można było szybko wykonać podstawowe czynności podsumowujące i zmiany klasyfikacji.

## <a name="preparation-for-semantic-ranking"></a>Przygotowanie do klasyfikacji semantycznej

Przed rozpoczęciem oceny przydatności zawartość musi być zredukowana do liczby wejść, które mogą być efektywnie obsługiwane przez rangę semantyczną. Zmniejszenie zawartości obejmuje następującą sekwencję kroków.

1. Obniżka zawartości zaczyna się od pierwszego zestawu wyników zwracanego przez domyślny [algorytm klasyfikacji podobieństwa](index-ranking-similarity.md) używany do wyszukiwania słów kluczowych. Wyniki wyszukiwania mogą zawierać do 1 000 dopasowań, ale Klasyfikacja semantyczna będzie przetwarzać tylko pierwsze 50. 

   Po otrzymaniu zapytania początkowe wyniki mogą być znacznie mniejsze niż 50, w zależności od liczby znalezionych dopasowań. Bez względu na liczbę dokumentów początkowy zestaw wyników jest dokument korpus na potrzeby klasyfikacji semantycznej.

1. W dokumencie korpus zawartość każdego pola w "searchFields" jest wyodrębniana i łączona do długiego ciągu.

1. Wszystkie ciągi, które są nadmiernie długie, są przycinane w celu zapewnienia, że ogólna długość spełnia wymagania wejściowe kroku podsumowania. W tym ćwiczeniu przycinania jest ważne, aby pomieścić zwięzłe pola jako pierwsze w "searchFields", aby upewnić się, że są one uwzględnione w ciągu. Jeśli masz bardzo duże dokumenty z polami tekstowymi, wszystkie elementy po osiągnięciu maksymalnego limitu są ignorowane.

Każdy dokument jest teraz reprezentowany przez pojedynczy długi ciąg.

> [!NOTE]
> Parametrami wejściowymi do modeli są tokeny, nie znaki ani wyrazy. Tokenizacji jest określany w części przez przypisanie analizatora w polach z możliwością wyszukiwania. Aby uzyskać szczegółowe informacje na temat sposobu tworzenia tokenów ciągów, można sprawdzić dane wyjściowe tokenu analizatora przy użyciu [interfejsu API REST analizatora testowego](/rest/api/searchservice/test-analyzer).

## <a name="summarization"></a>Podsumowanie

Po zmniejszeniu liczby ciągów można teraz przekazać zredukowane dane wejściowe za pomocą informacji o czytaniu maszyn i modeli reprezentacji języka, aby określić, które zdania i frazy najlepiej podsumowują dokument w odniesieniu do zapytania.

Dane wejściowe podsumowania to długie ciągi, które są uzyskiwane dla każdego dokumentu w fazie przygotowania. Z danego danych wejściowych model podsumowania znajduje fragment, który najlepiej reprezentuje odpowiedni dokument. Ten fragment stanowi również [podpis semantyczny](semantic-how-to-query-request.md) dokumentu. Każdy podpis jest dostępny w postaci zwykłego tekstu z wyróżnionymi wyróżnieniami i zawiera mniej niż 200 słów na dokument.

[Odpowiedź semantyczna](semantic-answers.md) zostanie również zwrócona, jeśli określono parametr "odpowiedzi", jeśli zapytanie zostało pożądane jako pytanie i jeśli zostanie znaleziony w długim ciągu, który może dostarczyć odpowiedzi na pytanie.

## <a name="scoring-and-ranking"></a>Ocenianie i Klasyfikacja

1. Podpisy są oceniane pod kątem zgodności z koncepcją i semantyką względem podanego zapytania.

   Na poniższym diagramie przedstawiono ilustracje, co oznacza "istotność semantyki". Należy wziąć pod uwagę termin "stolica", który może być używany w kontekście finansów, prawa, lokalizacji geograficznej lub gramatyki. Jeśli zapytanie zawiera warunki z tego samego obszaru wektora (na przykład "Wielka" i "inwestycja"), dokument zawierający także tokeny w tym samym klastrze będzie miał wyższy wynik niż ten, który nie jest.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Reprezentacja wektorowa dla kontekstu" border="true":::

1. @search.rerankerScoreJest przypisany do każdego dokumentu w oparciu o istotną semantykę podpisu.

1. Po dokonaniu oceny wszystkich dokumentów są one wyświetlane w kolejności malejącej według wyniku i uwzględnione w ładunku odpowiedzi na zapytanie. Ładunek zawiera odpowiedzi, zwykły tekst i wyróżnione napisy oraz wszystkie pola, które zostały oznaczone jako możliwe do pobierania lub określone w klauzuli select.

## <a name="next-steps"></a>Następne kroki

Klasyfikacja semantyczna jest oferowana w warstwach standardowych w określonych regionach. Aby uzyskać więcej informacji na temat dostępności i rejestrowania, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing). Nowy typ zapytania włącza struktury klasyfikacji i odpowiedzi w przeszukiwaniu semantycznym. Aby rozpocząć, [Utwórz zapytanie semantyczne](semantic-how-to-query-request.md).

Alternatywnie zapoznaj się z następującymi artykułami dotyczącymi klasyfikacji domyślnej. Klasyfikacja semantyczna zależy od rangi podobieństwa do zwrócenia początkowych wyników. Wiedza o wykonywaniu i klasyfikowaniu zapytań zapewnia szeroką wiedzę na temat działania całego procesu.

+ [Wyszukiwanie pełnotekstowe w usłudze Azure Wyszukiwanie poznawcze](search-lucene-query-architecture.md)
+ [Podobieństwo i ocenianie na platformie Azure Wyszukiwanie poznawcze](index-similarity-and-scoring.md)
+ [Analizatory do przetwarzania tekstu na platformie Azure Wyszukiwanie poznawcze](search-analyzers.md)