---
title: Wyszukiwanie semantyczne
titleSuffix: Azure Cognitive Search
description: Dowiedz się, w jaki sposób Wyszukiwanie poznawcze używać modeli wyszukiwania semantycznego analizy głębokiej w usłudze Bing, aby wyniki wyszukiwania były bardziej intuicyjne.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203236"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Wyszukiwanie semantyczne na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Funkcje wyszukiwania semantycznego znajdują się w publicznej wersji zapoznawczej, dostępne wyłącznie za pomocą interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wyszukiwanie semantyczne to zbiór funkcji związanych z zapytaniami, które obsługują bardziej naturalne środowisko zapytań o wyższej jakości. Funkcje obejmują semantyczną klasyfikację wyników wyszukiwania, a także tytuły i odpowiedzi generowane z wyróżnieniem semantycznym. Górne wyniki 50 zwrócone przez [aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) są przeklasyfikowane, aby znaleźć najbardziej odpowiednie dopasowania.

Podstawowa technologia wykorzystuje inwestycje z Bing i Microsoft Research i jest zintegrowana z infrastrukturą Wyszukiwanie poznawcze. Aby można było z niego korzystać, musisz mieć małe modyfikacje żądania wyszukiwania, ale nie jest wymagane dodatkowe konfigurowanie ani ponowne indeksowanie.

Funkcje publicznej wersji zapoznawczej obejmują:

+ Semantyczny model klasyfikacji, który ocenia wyniki na podstawie kontekstu lub semantyki znaczenia warunków zapytania wyszukiwania
+ Napisy semantyczne, które podkreślają odpowiednie fragmenty
+ Semantyka odpowiedzi na zapytanie, sformułowane także od wyników
+ Sprawdź pisownię, która koryguje literówki przed przekazaniem terminów zapytania do aparatu wyszukiwania

## <a name="semantic-search-architecture"></a>Architektura wyszukiwania semantycznego

Składniki wyszukiwania semantycznego są nakładane na siebie w oparciu o istniejący potok wykonywania zapytań. Korekcja pisowni (niepokazywana na diagramie) usprawnia odwoływanie, poprawiając literówki w poszczególnych terminach zapytań. Po ukończeniu wszystkich operacji analizowania i analizy aparat wyszukiwania pobiera dokumenty, które pasują do zapytania i oceniają je przy użyciu [domyślnego algorytmu oceniania](index-similarity-and-scoring.md#similarity-ranking-algorithms), BM25 lub klasycznego, w zależności od tego, kiedy usługa została utworzona. Na tym etapie są również stosowane profile oceniania. 

Po otrzymaniu najwyżej 50 dopasowań [algorytm klasyfikacji semantycznej](semantic-how-to-query-response.md) ponownie ocenia dokument korpus. Wyniki mogą zawierać więcej niż 50 dopasowań, ale tylko pierwsze 50 zostanie przeklasyfikowane. W celu klasyfikowania algorytm używa zarówno uczenia maszynowego, jak i przenoszenia, aby ponownie oceniać dokumenty na podstawie tego, jak dokładnie każdy z nich pasuje do zamiaru zapytania.

Do tworzenia napisów i odpowiedzi są stosowane modele reprezentacji języka. W ramach podpisu algorytm używa modelu języka opracowanego przez usługę Bing do wyodrębniania napisów z wyróżnionymi, które najlepiej podsumowują wyniki zapytania. Jeśli zapytanie wyszukiwania jest pytaniem, a odpowiedzi są żądane, podobny model języka identyfikuje fragmenty tekstu, które najlepiej odpowiadają na pytanie wyrażone przez zapytanie wyszukiwania.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Składniki semantyczne w potoku zapytania" border="true":::

## <a name="availability-and-pricing"></a>Dostępność i Cennik

Klasyfikacja semantyczna jest dostępna poprzez [rejestrację](https://aka.ms/SemanticSearchPreviewSignup)rejestracji, w usługach wyszukiwania utworzonych w warstwie Standardowa (S1, S2, S3), które znajdują się w jednym z następujących regionów: Północne stany USA, zachodnie stany USA, zachodnie stany USA 2, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia. Istniejąca usługa wyszukiwania o temperaturze S1 lub nowszej w określonych regionach jest zgodna z podglądem (nie ma potrzeby tworzenia nowej usługi).

Korekta pisowni jest dostępna w tych samych regionach, ale nie ma ograniczeń warstwy i nie wymaga wymagania dotyczącego rejestracji. 

Od 2 marca do 1 kwietnia, korekta pisowni i Klasyfikacja semantyczna są oferowane bezpłatnie. Po 1 kwietnia opłaty za korzystanie z tej funkcji będą naliczane. Oczekiwany koszt wynosi około USD $500 miesięcznie dla zapytań 250 000. Szczegółowe informacje o kosztach można znaleźć na [stronie cennika wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/) , a następnie [oszacować koszty i zarządzać nimi](search-sku-manage-costs.md).

## <a name="next-steps"></a>Następne kroki

Nowy typ zapytania umożliwia tworzenie struktur klasyfikacji i odpowiedzi w przeszukiwaniu semantycznym. [Utwórz zapytanie semantyczne](semantic-how-to-query-request.md) , aby rozpocząć. Lub zapoznaj się z następującymi artykułami dotyczącymi pokrewnych informacji.

+ [Dodaj sprawdzanie pisowni do terminów zapytania](speller-how-to-add.md)
+ [Klasyfikacja i odpowiedzi semantyczne](semantic-how-to-query-response.md)