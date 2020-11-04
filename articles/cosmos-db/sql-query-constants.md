---
title: Stałe SQL w Azure Cosmos DB
description: Dowiedz się, w jaki sposób stałe zapytań SQL w Azure Cosmos DB są używane do reprezentowania określonej wartości danych
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339210"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB stałe zapytania SQL  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Stała, znana również jako literał lub wartość skalarna, jest symbolem, który reprezentuje konkretną wartość danych. Format stałej zależy od typu danych wartości, która reprezentuje.  
  
 **Obsługiwane typy danych skalarnych:**  
  
|**Typ**|**Kolejność wartości**|  
|-|-|  
|**Niezdefiniowane**|Pojedyncza wartość: **undefined**|  
|**Null**|Pojedyncza wartość: **null**|  
|**Wartość logiczna**|Wartości: **false** , **true**.|  
|**Liczba**|Liczba zmiennoprzecinkowa podwójnej precyzji, IEEE 754 Standard.|  
|**Ciąg**|Sekwencja zero lub więcej znaków Unicode. Ciągi muszą być ujęte w pojedyncze lub podwójne cudzysłowy.|  
|**Macierzy**|Sekwencja zero lub więcej elementów. Każdy element może być wartością dowolnego typu danych skalarnych, z wyjątkiem **undefined**.|  
|**Stream**|Zestaw nieuporządkowany zero lub więcej par nazwa/wartość. Nazwa jest ciągiem Unicode, wartość może być dowolnego typu danych skalarnych, z wyjątkiem **undefined**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Składnia
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Argumentu
  
* `<undefined_constant>; Undefined`  
  
  Reprezentuje niezdefiniowaną wartość typu undefined.  
  
* `<null_constant>; null`  
  
  Reprezentuje wartość **null** **typu null**.  
  
* `<boolean_constant>`  
  
  Reprezentuje stałą typu Boolean.  
  
* `false`  
  
  Reprezentuje **fałszywą** wartość typu Boolean.  
  
* `true`  
  
  Reprezentuje wartość **true** typu Boolean.  
  
* `<number_constant>`  
  
  Reprezentuje stałą.  
  
* `decimal_literal`  
  
  Literały dziesiętne to liczby reprezentowane przy użyciu notacji dziesiętnej lub notacji wykładniczej.  
  
* `hexadecimal_literal`  
  
  Literały szesnastkowe to liczby reprezentowane przy użyciu prefiksu "0x", po którym następuje co najmniej jedna cyfra szesnastkowa.  
  
* `<string_constant>`  
  
  Reprezentuje stałą typu String.  
  
* `string _literal`  
  
  Literały ciągu są ciągami Unicode reprezentowanymi przez sekwencję zero lub więcej znaków Unicode lub sekwencji unikowych. Literały ciągu są ujęte w apostrofy (apostrof: ') lub podwójne cudzysłowy (cudzysłów: ").  
  
  Dozwolone są następujące sekwencje ucieczki:  
  
|**Sekwencja ucieczki**|**Opis**|**znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U + 0027|  
|\\"|cudzysłów (")|U + 0022|  
|\\\ |odwrócony ukośnik ( \\ )|U + 005C|  
|\\/|Solid (/)|U + 002F|  
|\b|Backspace|U + 0008|  
|\f|kanał informacyjny formularza|U + 000C|  
|\n|kanał informacyjny wiersza|U + 000A|  
|\r|powrót karetki|U + 000D|  
|\t| tabulator|U + 0009|  
|\uXXXX|Znak Unicode zdefiniowany przez 4 cyfry szesnastkowe.|U + XXXX|  

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
