---
title: Składnia zapytań wyszukiwania w usłudze Graph
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać składni zapytania wyszukiwania w projektancie Azure Machine Learning, aby wyszukiwać węzły na wykresie potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259232"
---
# <a name="graph-search-query-syntax"></a>Składnia zapytań wyszukiwania w usłudze Graph

Ten artykuł zawiera informacje na temat funkcji wyszukiwania grafów w programie Azure Machine Learning. 

Funkcja wyszukiwania grafów umożliwia szybkie nawigowanie po węźle podczas debugowania lub kompilowania potoku. Możesz wpisać słowo kluczowe lub zapytanie w polu wejściowym na pasku narzędzi lub na karcie wyszukiwanie w lewym panelu, aby wyzwolić wyszukiwanie. Wszystkie dopasowane wyniki zostaną wyróżnione kolorem żółtym na kanwie, a jeśli wybierzesz wynik w lewym panelu, węzeł na kanwie zostanie wyróżniony kolorem czerwonym.

![Zrzut ekranu przedstawiający przykładowe środowisko wyszukiwania w grafie](media/search/graph-search-0322.png)

Program Graph Search obsługuje wyszukiwanie pełnotekstowe w postaci słów kluczowych przy użyciu nazwy węzła i komentarzy. Można również filtrować właściwości węzła, takie jak runStatus, Duration, computeTarget. Wyszukiwanie słów kluczowych jest oparte na zapytaniu Lucene. Pełne zapytanie wyszukiwania wygląda następująco:  

**[[zapytanie Lucene] | [zapytanie filtru]]** 

Możesz użyć kwerendy lub kwerendy filtru Lucene. Aby użyć obu, użyj **|** separatora. Składnia zapytania filtru jest bardziej rygorystyczna niż zapytanie Lucene. Dlatego jeśli dane wejściowe klienta mogą być analizowane w obu tych przypadkach, zostanie zastosowane zapytanie filtru.

Na przykład `data OR model | compute in {cpucluster}` , jest to wyszukiwanie węzłów, których nazwa lub komentarz zawiera `data` lub `model` , a obliczenia to cpucluster.
 

## <a name="lucene-query"></a>Zapytanie Lucene

Wyszukiwanie grafów używa prostego zapytania "Lucene" jako składni wyszukiwania pełnotekstowego w węzłach "name" i "Comment". Obsługiwane są następujące operatory Lucene:

 
- I/LUB
- Dopasowanie symboli wieloznacznych z **?** **\*** Operatory i.

### <a name="examples"></a>Przykłady

- Wyszukiwanie proste: `JSON Data`

- I/LUB: `JSON AND Validation`

- Wiele i/lub: `(JSON AND Validation) OR (TSV AND Training)`

 
- Dopasowanie symboli wieloznacznych: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Nie można uruchomić zapytania Lucene ze znakiem "*".

##  <a name="filter-query"></a>Zapytanie filtru

 
Zapytania filtru używają następującego wzorca:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Można użyć następujących właściwości węzła jako kluczy:

- runStatus
- obliczanie
- czas trwania
- było
- publish
- tags

I użyj następujących operatorów:

- Większe lub równe: `>=`
- Mniejsze lub równe: `<=`
- Mniejszą `>`
- Wcześniejsz `<`
- Większy `==`
- Występują `=`
- NotEqual `!=`
- Podczas `in`

 
 

### <a name="example"></a>Przykład

- czas trwania > 100;
- stan w {nie powiodło się, NotStarted}
- obliczenia w {GPU-Cluster}; runStatus w {zakończony}

## <a name="technical-notes"></a>Uwagi techniczne

- Relacja między wieloma filtrami to "i"
- Jeśli `>=,  >,  <, or  <=` jest wybrana, wartości zostaną automatycznie przekonwertowane na typ liczbowy. W przeciwnym razie typy ciągów są używane do porównania.
- W przypadku wszystkich wartości typu String wielkość liter nie jest uwzględniana w porównaniu.
- Operator "in" oczekuje kolekcji jako wartości, składnia kolekcji jest `{name1, name2, name3}`
- Spacja zostanie zignorowana między słowami kluczowymi