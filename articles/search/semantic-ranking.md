---
title: Klasyfikacja semantyczna
titleSuffix: Azure Cognitive Search
description: Opisuje algorytm klasyfikacji semantycznej w Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604291"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Klasyfikacja semantyczna na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Funkcje wyszukiwania semantycznego znajdują się w publicznej wersji zapoznawczej, dostępne wyłącznie za pomocą interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Klasyfikacja semantyczna to rozszerzenie potoku wykonywania zapytania, które zwiększa precyzję i odzyskanie przez przeklasyfikowanie górnych dopasowań pierwszego zestawu wyników. Klasyfikacja semantyczna jest wspierana przez najnowocześniejsze modele uczenia się odczytu maszynowego, przeszkolone pod kątem zapytań wyrażonych w języku naturalnym, w przeciwieństwie do językowego dopasowania słów kluczowych. W przeciwieństwie do [domyślnego algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md), ranga semantyczna używa kontekstu i znaczenia wyrazów do określenia istotności.

## <a name="how-semantic-ranking-works"></a>Jak działa klasyfikacja semantyczna

Klasyfikacja semantyczna to zarówno zasób, jak i czasochłonne. W celu ukończenia przetwarzania w oczekiwanym opóźnieniu operacji zapytania, model przyjmuje jako dane wejściowe tylko pierwsze 50 dokumentów zwróconych z domyślnego [algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md). Wyniki z klasyfikacji początkowej mogą zawierać więcej niż 50 dopasowań, ale tylko pierwsze 50 zostanie ponownie sklasyfikowane. 

W przypadku klasyfikacji semantycznej model używa zarówno zrozumiałych do czytania, jak i przenoszenia uczenia, aby ponownie oceniać dokumenty w oparciu o to, jak dokładnie każdy z nich jest zgodny z intencją zapytania.

### <a name="preparation-passage-extraction-phase"></a>Faza przygotowania (wyodrębnianie fragmentów)

Dla każdego dokumentu w początkowych wynikach jest ćwiczenie wyodrębniania z fragmentów, które identyfikują kluczowe fragmenty. Jest to ćwiczenie downsizing, które zmniejsza zawartość do ilości, która może być przetwarzana szybko.

1. Dla każdego z 50 dokumentów wszystkie pola w parametrze searchFields są oceniane w kolejnej kolejności. Zawartość każdego pola jest konsolidowana w jeden długi ciąg. 

1. Długi ciąg jest następnie przycięty, aby upewnić się, że ogólna długość nie przekracza 8 000 tokenów. Z tego powodu zaleca się najpierw umieścić zwięzłe pola, aby zostały uwzględnione w ciągu. Jeśli masz bardzo duże dokumenty z polami tekstowymi, wszystkie elementy po ograniczeniu tokenu zostały zignorowane.

1. Każdy dokument jest teraz reprezentowany przez pojedynczy długi ciąg, który jest do 8 000 tokenów. Te ciągi są wysyłane do modelu podsumowania, co spowoduje dalsze skrócenie tego ciągu. Model podsumowania szacuje długi ciąg dla najważniejszych zdań lub fragmentów, które najlepiej podsumowują dokument lub odpowiedzą na pytanie.

1. Dane wyjściowe tej fazy są podpisem (i opcjonalnie, odpowiedzią). Podpis ma co najwyżej 128 tokenów na dokument i jest uznawany za najbardziej reprezentatywny dokument.

### <a name="scoring-and-ranking-phases"></a>Etapy oceniania i oceniania

W tej fazie wszystkie podpisy 50 są oceniane do oceny istotności.

1. Ocenianie jest określane przez ocenę każdego podpisu pod kątem koncepcyjności i zgodności semantycznej względem podanego zapytania.

   Na poniższym diagramie przedstawiono ilustracje, co oznacza "istotność semantyki". Należy wziąć pod uwagę termin "stolica", który może być używany w kontekście finansów, prawa, lokalizacji geograficznej lub gramatyki. Jeśli zapytanie zawiera warunki z tego samego obszaru wektora (na przykład "Wielka" i "inwestycja"), dokument zawierający także tokeny w tym samym klastrze będzie miał wyższy wynik niż ten, który nie jest.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Reprezentacja wektorowa dla kontekstu" border="true":::

1. Dane wyjściowe tej fazy są @search.rerankerScore przypisane do każdego dokumentu. Gdy wszystkie dokumenty są oceniane, są wyświetlane w kolejności malejącej i uwzględniane w ładunku odpowiedzi na zapytanie.

## <a name="next-steps"></a>Następne kroki

Klasyfikacja semantyczna jest oferowana w warstwach standardowych w określonych regionach. Aby uzyskać więcej informacji i zarejestrować się, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing). Nowy typ zapytania umożliwia tworzenie struktur klasyfikacji i odpowiedzi w przeszukiwaniu semantycznym. Aby rozpocząć, [Utwórz zapytanie semantyczne](semantic-how-to-query-request.md).

Zapoznaj się z dowolnym z poniższych artykułów, aby uzyskać informacje pokrewne.

+ [Omówienie wyszukiwania semantycznego](semantic-search-overview.md)
+ [Zwróć odpowiedź semantyczną](semantic-answers.md)