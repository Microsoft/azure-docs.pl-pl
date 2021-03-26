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
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562039"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Klasyfikacja semantyczna na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Funkcje wyszukiwania semantycznego znajdują się w publicznej wersji zapoznawczej, dostępne za pomocą interfejsu API REST i portalu. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Te funkcje są rozliczane. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Klasyfikacja semantyczna to rozszerzenie potoku wykonywania zapytania, które zwiększa precyzję przez przeklasyfikowanie górnych dopasowań pierwszego zestawu wyników. Klasyfikacja semantyczna jest oparta na dużych sieciach opartych na transformatorach, przeszkolonych do przechwytywania znaczenia semantyki terminów zapytania, a w przeciwieństwie do lingwistycznego dopasowania słów kluczowych. W przeciwieństwie do [domyślnego algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md), ranga semantyczna używa kontekstu i znaczenia wyrazów do określenia istotności.

Klasyfikacja semantyczna to zarówno zasób, jak i czasochłonne. Aby zakończyć przetwarzanie w oczekiwanym opóźnieniu operacji zapytania, dane wejściowe rangi semantycznej są konsolidowane i zmniejszane, aby można było szybko wykonać podstawowe czynności podsumowujące i zmiany klasyfikacji.

## <a name="pre-processing"></a>Przetwarzanie wstępne

Przed rozpoczęciem oceny przydatności zawartość musi być zredukowana do możliwej do zarządzania ilością danych wejściowych, które mogą być efektywnie obsługiwane przez rangę semantyczną.

1. Najpierw obniżka zawartości zaczyna się od początkowego zestawu wyników zwróconego przez domyślny [algorytm klasyfikacji podobieństwa](index-ranking-similarity.md) używany do wyszukiwania słów kluczowych. W przypadku dowolnego zapytania wyniki mogą być kilku dokumentów, maksymalnie do limitu 1 000. Ponieważ przetwarzanie dużej liczby dopasowań zajmie zbyt dużo czasu, tylko górny postęp 50 do klasyfikacji semantycznej.

   Bez względu na liczbę dokumentów, niezależnie od tego, czy jeden lub 50, początkowy zestaw wyników ustanawia pierwszą iterację dokumentu korpus na potrzeby klasyfikacji semantycznej.

1. Następnie w korpus, zawartość każdego pola w "searchFields" są wyodrębniane i łączone w długi ciąg.

1. Po konsolidacji ciągów wszystkie ciągi, które są zbyt długie, są przycinane w celu zapewnienia, że ogólna długość spełnia wymagania wejściowe kroku podsumowania.

   W tym ćwiczeniu przycinania jest ważne, aby pomieścić zwięzłe pola jako pierwsze w "searchFields", aby upewnić się, że są one uwzględnione w ciągu. Jeśli masz bardzo duże dokumenty z polami tekstowymi, wszystkie elementy po osiągnięciu maksymalnego limitu są ignorowane.

Każdy dokument jest teraz reprezentowany przez pojedynczy długi ciąg.

> [!NOTE]
> Ciąg składa się z tokenów, nie znaków ani wyrazów. Tokenizacji jest określany w części przez przypisanie analizatora w polach z możliwością wyszukiwania. Jeśli używasz wyspecjalizowanego analizatora, takiego jak nGram lub EdgeNGram, możesz chcieć wykluczyć to pole z searchFields. Aby uzyskać szczegółowe informacje na temat sposobu tworzenia tokenów ciągów, można sprawdzić dane wyjściowe tokenu analizatora przy użyciu [interfejsu API REST analizatora testowego](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Wyodrębnianie

Po zmniejszeniu liczby ciągów można teraz przekazać zredukowane dane wejściowe za pomocą informacji o czytaniu maszyn i modeli reprezentacji języka, aby określić, które zdania i frazy najlepiej podsumowują dokument w odniesieniu do zapytania. Ta faza wyodrębnia zawartość z ciągu, który przejdzie dalej do rangi semantycznej.

Dane wejściowe podsumowania są długimi ciągami uzyskanymi dla każdego dokumentu w fazie przygotowania. W przypadku każdego ciągu model podsumowania znajduje fragment, który jest najbardziej reprezentatywny. Ten fragment stanowi również [podpis semantyczny](semantic-how-to-query-request.md) dokumentu. Każdy podpis jest dostępny w wersji zwykłego tekstu i wyróżnieniu i jest często krótszy niż 200 słów na dokument.

[Odpowiedź semantyczna](semantic-answers.md) zostanie również zwrócona, jeśli określono parametr "odpowiedzi", jeśli zapytanie zostało pożądane jako pytanie i jeśli zostanie znaleziony w długim ciągu, który może dostarczyć odpowiedzi na pytanie.

## <a name="semantic-ranking"></a>Klasyfikacja semantyczna

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