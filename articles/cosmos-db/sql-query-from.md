---
title: Klauzula FROM w Azure Cosmos DB
description: Dowiedz się więcej na temat składni SQL i przykład dla klauzuli FROM dla Azure Cosmos DB. W tym artykule przedstawiono również przykłady zakresu wyników i pobieranie elementów podrzędnych przy użyciu klauzuli FROM.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 967e07e55599be0a614e0b6097ae4745f6c7081f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100086"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Klauzula FROM w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Klauzula FROM ( `FROM <from_specification>` ) jest opcjonalna, chyba że źródło zostanie odfiltrowane lub zaprojektować w dalszej części zapytania. Zapytanie, takie jak `SELECT * FROM Families` Wyliczenie w całym `Families` kontenerze. Zamiast używać nazwy kontenera, można także użyć głównego identyfikatora dla kontenera.

`FROM`Klauzula wymusza następujące reguły dla każdego zapytania:

* Kontener może mieć alias, taki jak `SELECT f.id FROM Families AS f` lub po prostu `SELECT f.id FROM Families f`. Poniżej `f` znajduje się alias `Families` . JAKO opcjonalne słowo kluczowe [aliasu](sql-query-working-with-json.md#aliasing) identyfikatora.  

* Po usunięciu aliasu oryginalna nazwa źródła nie może być powiązana. Na przykład `SELECT Families.id FROM Families f` składnia jest nieprawidłowa, ponieważ identyfikator został `Families` alias i nie można go rozpoznać.  

* Wszystkie właściwości, do których istnieją odwołania, muszą być w pełni kwalifikowane, aby uniknąć niejednoznacznych powiązań w przypadku braku ścisłego przestrzegania schematu. Na przykład `SELECT id FROM Families f` składnia jest nieprawidłowa, ponieważ właściwość `id` nie jest powiązana.

## <a name="syntax"></a>Składnia
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumenty
  
- `<from_source>`  
  
  Określa źródło danych z aliasem lub bez niego. Jeśli alias nie zostanie określony, zostanie wywnioskowany `<container_expression>` przy użyciu następujących reguł:  
  
-  Jeśli wyrażenie jest container_name, wówczas container_name zostanie użyte jako alias.  
  
-  Jeśli wyrażenie ma wartość `<container_expression>` , PROPERTY_NAME, wówczas property_name zostanie użyte jako alias. Jeśli wyrażenie jest container_name, wówczas container_name zostanie użyte jako alias.  
  
- DEFINICJ `input_alias`  
  
  Określa, że `input_alias` jest zbiorem wartości zwracanych przez bazowe wyrażenie kontenera.  
 
- `input_alias` PODCZAS  
  
  Określa, że `input_alias` powinien reprezentować zestaw wartości uzyskanych przez iterację wszystkich elementów tablicy każdej tablicy zwracanej przez bazowe wyrażenie kontenera. Każda wartość zwrócona przez wyrażenie kontenera bazowego, który nie jest tablicą, jest ignorowana.  
  
- `<container_expression>`  
  
  Określa wyrażenie kontenera, które ma być używane do pobierania dokumentów.  
  
- `ROOT`  
  
  Określa, że dokument powinien być pobrany z domyślnego, połączonego kontenera.  
  
- `container_name`  
  
  Określa, że dokument powinien być pobrany z podanego kontenera. Nazwa kontenera musi być zgodna z nazwą kontenera, z którym jest aktualnie połączona.  
  
- `input_alias`  
  
  Określa, że dokument ma zostać pobrany z innego źródła zdefiniowanego przez podany alias.  
  
- `<container_expression> '.' property_name`  
  
  Określa, że dokument ma być pobierany przez uzyskanie dostępu do `property_name` właściwości.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Określa, że dokument ma być pobierany przez uzyskanie dostępu do `property_name` właściwości lub array_index elementu tablicy dla wszystkich dokumentów pobranych przez określone wyrażenie kontenera.  
  
## <a name="remarks"></a>Uwagi
  
Wszystkie aliasy dostarczone lub wywnioskowane w `<from_source>(` s) muszą być unikatowe. Składnia `<container_expression>.` PROPERTY_NAME jest taka sama jak `<container_expression>' ['"property_name"']'` . Jednakże Ostatnia składnia może być użyta, jeśli nazwa właściwości zawiera znak inny niż identyfikator.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Obsługa brakujących właściwości, brak elementów tablicy i niezdefiniowane wartości
  
Jeśli wyrażenie kontenera uzyskuje dostęp do właściwości lub elementów tablicy i ta wartość nie istnieje, ta wartość zostanie zignorowana i nie będzie można jej przetworzyć.  
  
### <a name="container-expression-context-scoping"></a>Określanie zakresu kontekstu wyrażenia kontenera  
  
Wyrażenie kontenera może być w zakresie kontenera lub w zakresie dokumentu:  
  
- Wyrażenie jest w zakresie kontenera, jeśli źródłowe Źródło wyrażenia kontenera jest elementem głównym lub `container_name` . Takie wyrażenie reprezentuje zestaw dokumentów pobranych bezpośrednio z kontenera i nie zależy od przetwarzania innych wyrażeń kontenera.  
  
- Wyrażenie jest w zakresie dokumentu, jeśli bazowe Źródło wyrażenia kontenera jest `input_alias` wprowadzane wcześniej w zapytaniu. Takie wyrażenie reprezentuje zestaw dokumentów uzyskanych przez ocenę wyrażenia kontenera w zakresie każdego dokumentu należącego do zestawu skojarzonego z kontenerem aliasów. Wynikiem zestawu będzie związek zestawów uzyskanych przez obliczenie wyrażenia kontenera dla każdego z dokumentów w zestawie bazowym.

## <a name="examples"></a>Przykłady

### <a name="get-subitems-by-using-the-from-clause"></a>Pobierz elementy podelementowe za pomocą klauzuli FROM

Klauzula FROM może zmniejszyć źródło do mniejszego podzestawu. Aby wyliczyć tylko poddrzewo dla każdego elementu, jego element główny może stać się źródłem, jak pokazano w następującym przykładzie:

```sql
    SELECT *
    FROM Families.children
```

Wyniki są następujące:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Poprzednie zapytanie używało tablicy jako źródła, ale można również użyć obiektu jako źródła. Zapytanie uwzględnia wszystkie prawidłowe, zdefiniowane wartości JSON w źródle do uwzględnienia w wyniku. Poniższy przykład wykluczy `Families` , że nie ma `address.state` wartości.

```sql
    SELECT *
    FROM Families.address.state
```

Wyniki są następujące:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Klauzula WHERE](sql-query-where.md)
