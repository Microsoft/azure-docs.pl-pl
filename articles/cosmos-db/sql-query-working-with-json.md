---
title: Praca z formatem JSON w usłudze Azure Cosmos DB
description: Dowiedz się więcej na temat zapytań i dostępu do zagnieżdżonych właściwości JSON oraz używania znaków specjalnych w Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337833"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Praca z formatem JSON w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W podstawowym interfejsie API SQL usługi Azure Cosmos DB elementy są przechowywane jako dane JSON. System typów i wyrażenia są ograniczone do obsługi tylko typów JSON. Aby uzyskać więcej informacji, zobacz [specyfikację JSON](https://www.json.org/).

Podsumowującmy kilka ważnych aspektów pracy z danymi JSON:

- Obiekty JSON zawsze zaczynają się `{` od lewego nawiasu klamrowego i kończą z `}` prawego nawiasu klamrowego
- Właściwości JSON mogą być [zagnieżdżone](#nested-properties) wewnątrz siebie
- Wartości właściwości JSON mogą być tablicami
- W nazwach właściwości JSON jest uwzględniana wielkość liter
- Nazwa właściwości JSON może być dowolną wartością ciągu (w tym spacjami lub znakami, które nie są literami)

## <a name="nested-properties"></a>Właściwości zagnieżdżone

Możesz uzyskać dostęp do zagnieżdżonego JSON przy użyciu metody dostępu do punktów. W zapytaniach można używać zagnieżdżonych właściwości JSON w taki sam sposób, w jaki można używać innych właściwości.

Oto dokument z zagnieżdżonym elementem JSON:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

W tym przypadku `state` `country` właściwości,, i `city` są zagnieżdżone we `address` właściwości.

W poniższym przykładzie przedstawiono dwa zagnieżdżone właściwości: `f.address.state` i `f.address.city` .

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Praca z tablicami

Oprócz zagnieżdżonych właściwości kod JSON obsługuje również tablice.

Oto przykładowy dokument z tablicą:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Podczas pracy z tablicami można uzyskać dostęp do określonego elementu w tablicy, odwołując się do jego położenia:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

Jednak w większości przypadków należy użyć [podzapytania](sql-query-subquery.md) lub [samosprzężenia](sql-query-join.md) podczas pracy z tablicami.

Na przykład poniżej przedstawiono dokument przedstawiający dzienne saldo konta bankowego klienta.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Jeśli chcesz uruchomić zapytanie, które wykazało, że w pewnym momencie wszyscy klienci mają nieujemne saldo, możesz użyć [istniejącego](sql-query-subquery.md#exists-expression) z podzapytania:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Różnica między wartościami null i niezdefiniowanymi

Jeśli właściwość nie jest zdefiniowana w elemencie, jego wartość to `undefined` . Właściwość o wartości `null` musi być jawnie zdefiniowana i mieć przypisaną `null` wartość.

Rozważmy na przykład ten przykładowy element:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

W tym przykładzie właściwość `isRegistered` ma wartość, `undefined` ponieważ jest pomijana z elementu. Właściwość `creationDate` ma `null` wartość.

Azure Cosmos DB obsługuje dwie pomocne funkcje systemowe sprawdzania typu dla `null` i `undefined` Właściwości:

* [IS_NULL](sql-query-is-null.md) — sprawdza, czy wartość właściwości to `null`
* [IS_DEFINED](sql-query-is-defined.md) — sprawdza, czy wartość właściwości jest zdefiniowana

Możesz uzyskać informacje na temat [obsługiwanych operatorów](sql-query-operators.md) i ich zachowań `null` oraz `undefined` wartości.

## <a name="reserved-keywords-and-special-characters-in-json"></a>Zastrzeżone słowa kluczowe i znaki specjalne w formacie JSON

Można uzyskać dostęp do właściwości przy użyciu operatora właściwości cytowane `[]` . Na przykład elementy `SELECT c.grade` i `SELECT c["grade"]` są równoważne. Ta składnia jest przydatna do ucieczki właściwości, która zawiera spacje, znaki specjalne lub ma taką samą nazwę jak słowo kluczowe SQL lub słowo zastrzeżone.

Załóżmy na przykład, że dokument ma właściwość o nazwie `order` i Właściwość `price($)` , która zawiera znaki specjalne:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

W przypadku uruchomienia zapytania zawierającego `order` Właściwość lub `price($)` Właściwość zostanie wyświetlony komunikat o błędzie składni.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Wynik:

`
Syntax error, incorrect syntax near 'order'
`

Należy ponownie napisać te same zapytania jak poniżej:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>Wyrażenia JSON

Projekcja obsługuje również wyrażenia JSON, jak pokazano w następującym przykładzie:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

W poprzednim przykładzie `SELECT` klauzula musi utworzyć obiekt JSON, a ponieważ przykład nie zawiera klucza, klauzula używa nazwy zmiennej argumentu niejawnego `$1` . Następujące zapytanie zwraca dwie niejawne zmienne argumentów: `$1` i `$2` .

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Tworzenie aliasów

Można jawnie aliasować wartości w zapytaniach. Jeśli zapytanie ma dwie właściwości o tej samej nazwie, użyj aliasu, aby zmienić nazwę jednej lub obu właściwości, tak aby były one niejednoznaczne w przewidywanym wyniku.

### <a name="examples"></a>Przykłady

`AS`Słowo kluczowe używane do aliasowania jest opcjonalne, jak pokazano w poniższym przykładzie podczas projekcji drugiej wartości jako `NameInfo` :

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki są następujące:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Tworzenie aliasów z zarezerwowanymi słowami kluczowymi lub znakami specjalnymi

Nie można użyć aliasu, aby zaprojektować wartość jako nazwę właściwości ze spacją, znakiem specjalnym lub słowem zastrzeżonym. Jeśli chcesz zmienić projekcję wartości na przykład, jeśli ma nazwę właściwości z spacją, możesz użyć [wyrażenia JSON](#json-expressions).

Oto przykład:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)
