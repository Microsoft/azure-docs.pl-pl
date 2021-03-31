---
title: Odwołanie do składni wyrażenia OData
titleSuffix: Azure Cognitive Search
description: Formalna Specyfikacja gramatyki i składni dla wyrażeń OData w zapytaniach usługi Azure Wyszukiwanie poznawcze.
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
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88928857"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze używa [wyrażeń OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) jako parametrów w całym interfejsie API. Najczęściej wyrażenia OData są używane dla `$orderby` `$filter` parametrów i. Wyrażenia te mogą być złożone, zawierające wiele klauzul, funkcje i operatory. Jednak nawet proste wyrażenia OData, takie jak ścieżki właściwości, są używane w wielu częściach interfejsu API REST usługi Azure Wyszukiwanie poznawcze. Na przykład wyrażenia ścieżki są używane do odwoływania się do podpól złożonych pól wszędzie w interfejsie API, takich jak podczas tworzenia listy podpól w [sugestii](index-add-suggesters.md), [funkcji oceniania](index-add-scoring-profiles.md), `$select` parametru lub nawet [wyszukiwania w zapytań Lucene](query-lucene-syntax.md).

W tym artykule opisano wszystkie te formy wyrażeń OData przy użyciu formalnej gramatyki. Istnieje również [interaktywny diagram](#syntax-diagram) ułatwiający wizualne Eksplorowanie gramatyki.

## <a name="formal-grammar"></a>Formalna Gramatyka

Możemy opisać podzestaw języka OData obsługiwanego przez platformę Azure Wyszukiwanie poznawcze przy użyciu gramatyki ([rozszerzonej Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)). Reguły są wyświetlane na liście "w górę", zaczynając od najbardziej złożonych wyrażeń i dzieląc je na więcej wyrażeń pierwotnych. W górnej części są reguły gramatyki, które odpowiadają określonym parametrom interfejsu API REST usługi Azure Wyszukiwanie poznawcze:

- [`$filter`](search-query-odata-filter.md)zdefiniowane przez `filter_expression` regułę.
- [`$orderby`](search-query-odata-orderby.md)zdefiniowane przez `order_by_expression` regułę.
- [`$select`](search-query-odata-select.md)zdefiniowane przez `select_expression` regułę.
- Ścieżki pól zdefiniowane przez `field_path` regułę. Ścieżki pól są używane w całym interfejsie API. Mogą odwoływać się do pól najwyższego poziomu indeksu lub pól podrzędnych z co najmniej jednym elementem nadrzędnym [złożonego pola](search-howto-complex-data-types.md) .

Gdy EBNF jest [diagramem składni](https://en.wikipedia.org/wiki/Syntax_diagram) umożliwia przeglądania, który pozwala interaktywnie zbadać gramatykę i relacje między jej regułami.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Diagram składni

Aby wizualnie zbadać gramatykę języka OData obsługiwaną przez usługę Azure Wyszukiwanie poznawcze, wypróbuj interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Wyszukiwanie poznawcze platformy Azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Zobacz też  

- [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
- [Wyszukaj dokumenty &#40;interfejs API REST usługi Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/Search-Documents)
- [Składnia zapytań Lucene](query-lucene-syntax.md)
- [Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze](query-simple-syntax.md)