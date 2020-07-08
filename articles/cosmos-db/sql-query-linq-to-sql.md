---
title: Tłumaczenie LINQ to SQL w Azure Cosmos DB
description: Poznaj operatory LINQ obsługiwane i sposób mapowania zapytań LINQ do zapytań SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3f8753518e1d54ddba4fc15a5a030308d0c112a1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042496"
---
# <a name="linq-to-sql-translation"></a>Tłumaczenie składni LINQ na język SQL

Dostawca zapytań Azure Cosmos DB wykonuje Mapowanie najlepszego nakładu pracy z zapytania LINQ do Cosmos DB zapytania SQL. Jeśli chcesz uzyskać zapytanie SQL tłumaczone na LINQ, użyj `ToString()` metody dla wygenerowanego `IQueryable` obiektu. W poniższym opisie założono podstawową znajomość programu LINQ.

System typu dostawcy zapytań obsługuje tylko typy pierwotne JSON: numeric, Boolean, String i null.

Dostawca zapytań obsługuje następujące wyrażenia skalarne:

- Stałe wartości, w tym stałe wartości typów danych pierwotnych w czasie oceny zapytania.
  
- Wyrażenia indeksu właściwości/tablicy odwołujące się do właściwości obiektu lub elementu tablicy. Przykład:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Wyrażenia arytmetyczne, w tym typowe wyrażenia arytmetyczne na wartościach liczbowych i logicznych. Aby zapoznać się z pełną listą, zobacz [specyfikację SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Wyrażenia porównania ciągów, które obejmują porównanie wartości ciągu z pewną stałą wartością ciągu.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Wyrażenia tworzenia obiektów/tablic, które zwracają obiekt typu wartości złożonej lub typu anonimowego, lub tablicę takich obiektów. Można zagnieżdżać te wartości.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Obsługiwane operatory LINQ

Dostawca LINQ zawarty w zestawie SDK programu SQL .NET obsługuje następujące operatory:

- **SELECT**: projekcje przekładają się na wybór SQL, w tym Konstruowanie obiektu.
- **Gdzie**: filtry przekładają się na SQL i obsługują tłumaczenie między `&&` , `||` i `!` do operatorów SQL
- **SelectMany**: umożliwia rozwijanie tablic do klauzuli JOIN języka SQL. Użyj do łańcucha lub zagnieżdżania wyrażeń do filtrowania elementów tablicy.
- **OrderBy** i **OrderByDescending**: Przekształć do order by przy użyciu ASC lub DESC.
- Operatory **Count**, **Sum**, **Min**, **Max** i **Average** na potrzeby agregacji oraz ich asynchroniczne odpowiedniki **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** i **AverageAsync**.
- **CompareTo**: operator tłumaczony na porównania zakresu. Często używane dla ciągów, ponieważ nie są one porównywalne w programie .NET.
- **Pomiń** i **Zrób**: tłumaczy na przesunięcie SQL i limit ograniczenia wyników zapytania i dzielenia na strony.
- **Funkcje matematyczne**: obsługuje tłumaczenie z platformy .NET,,,,,,,,,,,,,,, `Abs` `Acos` `Asin` `Atan` `Ceiling` `Cos` `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan` , i `Truncate` do równoważnych funkcji wbudowanych języka SQL.
- **Funkcje ciągów**: obsługuje tłumaczenie z platformy .NET,,,,,,,,,,, `Concat` `Contains` `Count` `EndsWith` `IndexOf` `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd` i `TrimStart` do równoważnych funkcji wbudowanych języka SQL.
- **Funkcje tablicowe**: obsługuje tłumaczenie z platformy .NET `Concat` , `Contains` i `Count` do równoważnych funkcji wbudowanych języka SQL.
- **Funkcje rozszerzenia geograficznego**: obsługuje tłumaczenie z metod zastępczych `Distance` ,, `IsValid` `IsValidDetailed` i `Within` do równoważnych funkcji wbudowanych języka SQL.
- **Funkcja rozszerzenia funkcji zdefiniowanej przez użytkownika**: obsługuje translację z metody zastępczej `UserDefinedFunctionProvider.Invoke` do odpowiedniej funkcji zdefiniowanej przez użytkownika.
- **Różne**: obsługuje translację `Coalesce` operatorów i operatory warunkowe. Można przetłumaczyć `Contains` na ciąg zawiera, ARRAY_CONTAINS lub SQL w, w zależności od kontekstu.

## <a name="examples"></a>Przykłady

Poniższe przykłady ilustrują, jak niektóre standardowe operatory zapytań LINQ przekładają się na zapytania Cosmos DB.

### <a name="select-operator"></a>SELECT — operator

Składnia to `input.Select(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym.

**SELECT — operator, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**SELECT — operator, przykład 2:** 

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**SELECT — operator, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operator SelectMany

Składnia to `input.SelectMany(x => f(x))`, gdzie `f` jest wyrażeniem skalarnym, które zwraca typ kontenera.

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operator Where

Składnia to `input.Where(x => f(x))`, gdzie `f` jest wyrażenie skalarnym, które zwraca wartość logiczną.

**Operator WHERE, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Operator WHERE, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Złożone zapytania języka SQL

Możesz złożyć poprzednie operatory, aby tworzyć bardziej zaawansowane zapytania. Ponieważ Cosmos DB obsługuje zagnieżdżone kontenery, można łączyć lub zagnieżdżać kompozycję.

### <a name="concatenation"></a>Łączenie

Składnia to `input(.|.SelectMany())(.Select()|.Where())*`. Połączone zapytanie może rozpoczynać się od opcjonalnego `SelectMany` zapytania, po którym następuje `Select` wiele `Where` operatorów lub.

**Łączenie, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Łączenie, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Łączenie, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Łączenie, przykład 4:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Zagnieżdżanie

Składnia to `input.SelectMany(x=>x.Q())` gdzie `Q` jest `Select` `SelectMany` operator,, or `Where` .

Zapytanie zagnieżdżone stosuje wewnętrzne zapytanie do każdego elementu zewnętrznego kontenera. Jedną z ważnych funkcji jest to, że zapytanie wewnętrzne może odwoływać się do pól elementów w kontenerze zewnętrznym, takich jak samosprzężenie.

**Zagnieżdżanie, przykład 1:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Zagnieżdżanie, przykład 2:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Zagnieżdżanie, przykład 3:**

- **Wyrażenie lambda składni LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
