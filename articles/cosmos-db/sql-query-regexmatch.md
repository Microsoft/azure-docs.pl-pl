---
title: RegexMatch w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemowej RegexMatch SQL w Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004ece7877358be54ba67c2f72eb3210f16fb0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098131"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zapewnia możliwości wyrażeń regularnych. Wyrażenia regularne są zwięzłą i elastyczną notacją do znajdowania wzorców tekstu. Azure Cosmos DB używa [wyrażeń regularnych zgodnych ze standardem Perl (PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Składnia
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem regularnym.

*str_expr3*  
   Jest ciągiem wybranych modyfikatorów, który ma być używany z wyrażeniem regularnym. Ta wartość ciągu jest opcjonalna. Jeśli chcesz uruchomić RegexMatch bez modyfikatorów, możesz dodać pusty ciąg lub pominąć całkowicie. 

Informacje o [składni tworzenia wyrażeń regularnych](https://perldoc.perl.org/perlre)można znaleźć w języku Perl. 

Azure Cosmos DB obsługuje następujące cztery Modyfikatory:

| Modyfikator | Opis |
| ------ | ----------- |
| `m` | Traktuj wyrażenie ciągu, aby było przeszukiwane jako wiele wierszy. Bez tej opcji "^" i "$" będą zgodne na początku lub na końcu ciągu, a nie w każdym wierszu. |
| `s` | Zezwalaj na ".", aby dopasować dowolny znak, w tym znak nowego wiersza. | 
| `i` | Ignoruj wielkość liter w przypadku dopasowania do wzorca. |
| `x` | Ignoruj wszystkie znaki odstępu. |

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne. Zwraca wartość undefined, jeśli wyrażenie ciągu do przeszukania, wyrażenie regularne lub wybrane Modyfikatory są nieprawidłowe.
  
## <a name="examples"></a>Przykłady
  
Poniższy prosty przykład RegexMatch sprawdza ciąg "abcd" dla dopasowania wyrażenia regularnego przy użyciu kilku różnych modyfikatorów.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Za pomocą RegexMatch można użyć metaznaków, aby wykonywać bardziej złożone wyszukiwania ciągów, które w przeciwnym razie nie mogą być możliwe przy użyciu funkcji systemu StartsWith, EndsWith, Contains lub StringEquals. Oto kilka dodatkowych przykładów, które można uruchomić za pomocą zestawu danych odżywiania dostępnego za pośrednictwem [środowisko testowe zapytań Azure Cosmos DB](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Jeśli musisz użyć metaznaku w wyrażeniu regularnym i nie chcesz, aby miało ono specjalne znaczenie, należy wypróbować metaznak przy użyciu `\` .

**Sprawdź elementy zawierające opis zawierający słowo "sól" dokładnie raz:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Sprawdź elementy, które mają opis zawierający liczbę z zakresu od 0 do 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Sprawdź elementy z opisem zawierającym cztery litery wyrazów zaczynających się od "S" lub "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy) , jeśli wyrażenie regularne może być podzielone na StartsWith, EndsWith, Contains lub StringEquals funkcje systemowe.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
