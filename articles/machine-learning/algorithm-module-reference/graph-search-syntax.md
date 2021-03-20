---
title: Składnia zapytań wyszukiwania w usłudze Graph
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać składni zapytania wyszukiwania w projektancie Azure Machine Learning, aby wyszukiwać węzły w programie na wykresie potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420771"
---
# <a name="graph-search-query-syntax"></a>Składnia zapytań wyszukiwania w usłudze Graph

Ten artykuł zawiera informacje o składni zapytań wyszukiwania grafu w Azure Machine Learning. Funkcja wyszukiwania grafów pozwala wyszukiwać węzeł według jego nazwy i właściwości. 

 ![Animowany zrzut ekranu przedstawiający przykładowe środowisko wyszukiwania w grafie](media/search/graph-search.gif)

Program Graph Search obsługuje wyszukiwanie pełnotekstowe w postaci słów kluczowych przy użyciu nazwy węzła i komentarzy. Można również filtrować właściwości węzła, takie jak runStatus, Duration, computeTarget. Wyszukiwanie słów kluczowych jest oparte na zapytaniu Lucene. Pełne zapytanie wyszukiwania wygląda następująco:  

**[zapytanie Lucene | [zapytanie filtru]** 

Możesz użyć kwerendy lub kwerendy filtru Lucene. Aby użyć obu, użyj **|** separatora. Składnia zapytania filtru jest bardziej rygorystyczna niż zapytanie Lucene. Dlatego jeśli dane wejściowe klienta mogą być analizowane w obu tych przypadkach, zostanie zastosowane zapytanie filtru.

 

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