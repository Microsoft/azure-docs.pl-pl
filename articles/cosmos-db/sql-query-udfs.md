---
title: Funkcje zdefiniowane przez użytkownika (UDF) w Azure Cosmos DB
description: Dowiedz się więcej o funkcjach zdefiniowanych przez użytkownika w programie Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 5c7050042d59156e1203b5830d710e61bd8eb382
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341896"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funkcje zdefiniowane przez użytkownika (UDF) w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Interfejs API SQL zapewnia obsługę funkcji zdefiniowanych przez użytkownika (UDF). Za pomocą skalarnej UDF można przekazać zero lub wiele argumentów i zwrócić wynik pojedynczego argumentu. Interfejs API sprawdza każdy argument pod kątem dozwolonych wartości JSON.  

## <a name="udf-use-cases"></a>Przypadki użycia w systemie UDF

Interfejs API rozszerza składnię SQL w celu obsługi niestandardowej logiki aplikacji przy użyciu UDF. Możesz zarejestrować UDF z interfejsem API SQL i odwoływać się do nich w zapytaniach SQL. W przeciwieństwie do procedur składowanych i wyzwalaczy UDF jest tylko do odczytu.

Za pomocą UDF można zwiększyć język zapytań Azure Cosmos DB. UDF to doskonały sposób na wyrażenie złożonej logiki biznesowej w projekcji zapytania.

Jednak zalecamy uniknięcie UDF, gdy:

- Równoważna [Funkcja systemowa](sql-query-system-functions.md) już istnieje w Azure Cosmos DB. Funkcja systemowa zawsze będzie używać mniejszej liczby jednostek RU niż odpowiedni format UDF.
- UDF jest jedynym filtrem w `WHERE` klauzuli zapytania. Format UDF nie korzysta z tego indeksu, aby Ocena plików UDF wymagała załadowania dokumentów. Łączenie dodatkowych predykatów filtru, które używają indeksu, w połączeniu z UDF, w `WHERE` klauzuli zmniejszy liczbę dokumentów przetworzonych przez UDF.

Jeśli w zapytaniu należy użyć tej samej wartości UDF wielokrotnie, należy odwołać się do UDF w [podzapytaniu](sql-query-subquery.md#evaluate-once-and-reference-many-times), co pozwala na użycie wyrażenia sprzężenia w celu obliczenia wartości UDF raz, ale odwoływanie się do niego wielokrotnie.

## <a name="examples"></a>Przykłady

Poniższy przykład rejestruje UDF w kontenerze elementu w bazie danych Cosmos. W przykładzie tworzony jest format UDF o nazwie `REGEX_MATCH` . Akceptuje dwie wartości ciągu JSON `input` i `pattern` i sprawdza, czy pierwsze pasuje do wzorca określonego w drugim przy użyciu `string.match()` funkcji JavaScript.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Teraz Użyj tego formatu UDF w projekcji zapytania. Należy zakwalifikować UDF z prefiksem z rozróżnianiem wielkości liter `udf.` podczas wywoływania ich z poziomu zapytań.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Wyniki są następujące:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Można użyć formatu UDF kwalifikowana z `udf.` prefiksem wewnątrz filtra, tak jak w poniższym przykładzie:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Wyniki są następujące:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

W zasadzie UDF są prawidłowymi wyrażeniami skalarnymi, których można używać zarówno w projekcjach, jak i w filtrach.

Aby rozwijać możliwości UDF, należy zapoznać się z innym przykładem logiki warunkowej:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Poniższy przykład wykonuje funkcję UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Wyniki są następujące:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Jeśli właściwości, do których odwołują się parametry UDF, nie są dostępne w wartości JSON, parametr jest traktowany jako undefined, a wywołanie UDF jest pomijane. Podobnie, jeśli wynik UDF jest niezdefiniowany, nie jest zawarty w wyniku.

Jak przedstawiono w powyższych przykładach, UDF integruje możliwości języka JavaScript z interfejsem API SQL. UDF zapewniają bogaty programowalny interfejs do wykonywania złożonej procedury, logiki warunkowej z pomocą wbudowanych funkcji środowiska uruchomieniowego języka JavaScript. Interfejs API SQL udostępnia argumenty UDF dla każdego elementu źródłowego w bieżącym etapie przetwarzania lub ZAZNACZania klauzuli. Wynik jest bezproblemowo zawarty w ogólnym potoku wykonywania. Podsumowując, UDF to doskonałe narzędzia do wykonywania złożonej logiki biznesowej w ramach zapytań.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje systemowe](sql-query-system-functions.md)
- [Agregacje](sql-query-aggregates.md)