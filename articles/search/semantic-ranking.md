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
ms.openlocfilehash: e3078c8f71f8862cacad552bb3176c08530e79bb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418848"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Klasyfikacja semantyczna na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Funkcje wyszukiwania semantycznego znajdują się w publicznej wersji zapoznawczej, dostępne wyłącznie za pomocą interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Klasyfikacja semantyczna to rozszerzenie potoku wykonywania zapytania, które zwiększa precyzję i odzyskanie przez przeklasyfikowanie górnych dopasowań pierwszego zestawu wyników. Klasyfikacja semantyczna jest wspierana przez najnowocześniejsze modele uczenia się odczytu maszynowego, przeszkolone pod kątem zapytań wyrażonych w języku naturalnym, w przeciwieństwie do językowego dopasowania słów kluczowych. W przeciwieństwie do [domyślnego algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md), ranga semantyczna używa kontekstu i znaczenia wyrazów do określenia istotności.

## <a name="how-semantic-ranking-works"></a>Jak działa klasyfikacja semantyczna

Klasyfikacja semantyczna to zarówno zasób, jak i czasochłonne. W celu ukończenia przetwarzania w oczekiwanym opóźnieniu operacji zapytania, model przyjmuje jako dane wejściowe tylko pierwsze 50 dokumentów zwróconych z domyślnego [algorytmu klasyfikacji podobieństwa](index-ranking-similarity.md). Wyniki z klasyfikacji początkowej mogą zawierać więcej niż 50 dopasowań, ale tylko pierwsze 50 zostanie ponownie sklasyfikowane. 

W przypadku klasyfikacji semantycznej model używa zarówno zrozumiałych do czytania, jak i przenoszenia uczenia, aby ponownie oceniać dokumenty w oparciu o to, jak dokładnie każdy z nich jest zgodny z intencją zapytania.

1. Dla każdego dokumentu, ranga semantyczna ocenia pola w parametrze searchFields w kolejności, konsolidując zawartość w jeden duży ciąg.

1. Ciąg zostanie następnie przycięty, aby upewnić się, że ogólna długość nie przekracza 8 000 tokenów. Jeśli masz bardzo duże dokumenty, z polem zawartości lub merged_content polem zawierającym wiele stron zawartości, wszystkie elementy po limicie tokenów zostaną zignorowane.

1. Każdy z 50 dokumentów jest teraz reprezentowany przez pojedynczy długi ciąg. Ten ciąg jest wysyłany do modelu podsumowania. Model podsumowania zawiera podpisy (i odpowiedzi), dzięki czemu można zidentyfikować fragmenty, które pojawiają się w celu podsumowania zawartości lub udzielenia odpowiedzi na pytanie. Dane wyjściowe modelu podsumowania to dalej skrócony ciąg, który ma co najwyżej 128 tokenów.

1. Mniejszy ciąg zmieni się na podpis dokumentu i reprezentuje najbardziej odpowiednie fragmenty Znalezione w większym ciągu. Zestaw 50 (lub mniej) jest następnie klasyfikowany jako istotny w kolejności. 

Koncepcje dotyczące pojęć i semantyki są ustanawiane za poorednictwem reprezentacji wektorów i klastrów terminowych. Algorytm podobieństwa słów kluczowych może dawać wagę w dowolnym okresie zapytania, model semantyczny został przeszkolony do rozpoznawania współzależności i relacji między wyrazami, które w przeciwnym razie nie są związane z powierzchnią. W związku z tym, jeśli ciąg zapytania zawiera warunki z tego samego klastra, dokument zawierający obie te elementy będzie większy niż jeden, który nie jest.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Reprezentacja wektorowa dla kontekstu" border="true":::

## <a name="next-steps"></a>Następne kroki

Klasyfikacja semantyczna jest oferowana w warstwach standardowych w określonych regionach. Aby uzyskać więcej informacji i zarejestrować się, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Nowy typ zapytania umożliwia tworzenie struktur klasyfikacji i odpowiedzi w przeszukiwaniu semantycznym. [Utwórz zapytanie semantyczne](semantic-how-to-query-request.md) , aby rozpocząć.

Zapoznaj się z dowolnym z poniższych artykułów, aby uzyskać informacje pokrewne.

+ [Dodaj sprawdzanie pisowni do terminów zapytania](speller-how-to-add.md)
+ [Zwróć odpowiedź semantyczną](semantic-answers.md)