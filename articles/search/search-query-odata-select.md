---
title: Odwołanie do wyboru protokołu OData
titleSuffix: Azure Cognitive Search
description: Składnia i Dokumentacja języka dla jawnego wyboru pól, które mają zostać zwrócone w wynikach wyszukiwania zapytań Wyszukiwanie poznawcze platformy Azure.
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919662"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Składnia $select OData na platformie Azure Wyszukiwanie poznawcze

 Można użyć [parametru **$SELECT** OData](query-odata-filter-orderby-syntax.md) , aby wybrać pola do uwzględnienia w wynikach wyszukiwania z usługi Azure wyszukiwanie poznawcze. W tym artykule opisano szczegółowo składnię **$SELECT** . Aby uzyskać ogólne informacje na temat używania **$SELECT** podczas prezentowania wyników wyszukiwania, zobacz jak korzystać [z wyników wyszukiwania w usłudze Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

**$SELECT** parametr określa, które pola dla każdego dokumentu są zwracane w zestawie wyników zapytania. Następujący EBNF ([formularz rozszerzony Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę dla parametru **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla usługi Azure wyszukiwanie poznawcze](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

**$SELECT** parametr znajduje się w dwóch formach:

1. Pojedyncza gwiazdka ( `*` ), wskazująca, że wszystkie pola do pobierania powinny być zwracane.
1. Rozdzielana przecinkami lista ścieżek pól, które identyfikują pola, które powinny być zwracane.

W przypadku korzystania z drugiego formularza można określić tylko na liście pola do pobierania.

Jeśli lista zawiera pole złożone bez określania jego pól podrzędnych, wszystkie możliwe do pobierania pola podrzędne zostaną uwzględnione w zestawie wyników zapytania. Załóżmy na przykład, że indeks zawiera `Address` pola `Street` , `City` i `Country` pola podrzędne, które są do pobierania. W przypadku określenia `Address` w **$SELECT**wyniki zapytania będą zawierać wszystkie trzy pola podrzędne.

## <a name="examples"></a>Przykłady

Uwzględnij `HotelId` `HotelName` pola, i `Rating` najwyższego poziomu w wynikach, a także `City` pola podrzędne `Address` :

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

Przykładowy wynik może wyglądać następująco:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Uwzględnij `HotelName` pole najwyższego poziomu w wynikach, a także wszystkie pola podrzędne i `Address` `Type` i `BaseRate` podrzędne pola dla każdego obiektu w `Rooms` kolekcji:

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

Przykładowy wynik może wyglądać następująco:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki  

- [Jak korzystać z wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze](search-pagination-page-layout.md)
- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)