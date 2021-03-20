---
title: Wyszukiwanie OData. Informacje o funkcji oceny
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna dotycząca korzystania z funkcji Search. Score w zapytaniach usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88923980"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Funkcja OData na platformie Azure wyszukiwanie poznawcze

Po wysłaniu zapytania do Wyszukiwanie poznawcze platformy Azure bez parametru [ **$OrderBy**](search-query-odata-orderby.md), przywracane wyniki zostaną posortowane w kolejności malejącej według oceny istotności. Nawet w przypadku używania **$OrderBy** ocena znaczenia będzie używana w celu podzielenia powiązań domyślnie. Jednak czasami warto użyć oceny istotności jako początkowego kryterium sortowania i innych kryteriów jako powiązania. `search.score`Funkcja ta umożliwia wykonywanie tych czynności.

## <a name="syntax"></a>Składnia

Składnia dla `search.score` w **$OrderBy** ma wartość `search.score()` . Funkcja nie `search.score` przyjmuje żadnych parametrów. Może być używany z `asc` `desc` specyfikatorem kolejności sortowania lub, podobnie jak każda inna klauzula w parametrze **$OrderBy** . Może pojawić się w dowolnym miejscu na liście kryteriów sortowania.

## <a name="example"></a>Przykład

Sortuj Hotele w kolejności malejącej według `search.score` i `rating` , a następnie w kolejności rosnącej według odległości od podanych współrzędnych, tak aby między dwiema hoteli z identycznymi klasyfikacjami była wyświetlana pierwsza z nich:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Następne kroki  

- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Przeszukaj dokumenty &#40;usłudze Azure Wyszukiwanie poznawcze EST API&#41;](/rest/api/searchservice/Search-Documents)