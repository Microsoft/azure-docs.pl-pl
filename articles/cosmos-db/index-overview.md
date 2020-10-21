---
title: Indeksowanie w usłudze Azure Cosmos DB
description: Dowiedz się, jak indeksowanie działa w Azure Cosmos DB, różne rodzaje indeksów, takie jak zakres, przestrzenny, obsługiwane indeksy złożone.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 35d81d0076c4b29a8b1399331aa0254178720931
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277817"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indeksowanie w usłudze Azure Cosmos DB — omówienie

Azure Cosmos DB to baza danych niezależny od schematu, która pozwala na iterację aplikacji bez konieczności rozwiązywania problemów z zarządzaniem schematami i indeksami. Domyślnie Azure Cosmos DB automatycznie indeksuje każdą właściwość dla wszystkich elementów w [kontenerze](account-databases-containers-items.md#azure-cosmos-containers) bez konieczności definiowania schematu lub konfigurowania indeksów pomocniczych.

Celem tego artykułu jest wyjaśnienie sposobu indeksowania danych przez usługę Azure Cosmos DB i wykorzystania tych indeksów do podwyższenia wydajności zapytań. Zaleca się przechodzenie przez tę sekcję przed rozpoczęciem dostosowywania [zasad indeksowania](index-policy.md).

## <a name="from-items-to-trees"></a>Z elementów do drzew

Za każdym razem, gdy element jest przechowywany w kontenerze, jego zawartość jest rzutowana jako dokument JSON, a następnie konwertowana na reprezentację drzewa. Oznacza to, że każda Właściwość tego elementu jest reprezentowana jako węzeł w drzewie. Węzeł pseudo root jest tworzony jako element nadrzędny dla wszystkich właściwości pierwszego poziomu elementu. Węzły liścia zawierają rzeczywiste wartości skalarne przenoszone przez element.

Rozważmy na przykład ten element:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Powinna być reprezentowana przez następujące drzewo:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="Poprzedni element reprezentowany jako drzewo" border="false":::

Zwróć uwagę, jak tablice są kodowane w drzewie: każdy wpis w tablicy pobiera pośredni węzeł z etykietą z indeksem tego wpisu w tablicy (0, 1 itd.).

## <a name="from-trees-to-property-paths"></a>Z drzew do ścieżek właściwości

Powód, dla którego Azure Cosmos DB przekształcania elementów na drzewa, jest, ponieważ umożliwia odwoływanie się do właściwości przez ich ścieżki w ramach tych drzew. Aby uzyskać ścieżkę do właściwości, można przejść przez drzewo z węzła głównego do tej właściwości i połączyć etykiety każdego przechodzącego węzła.

Poniżej znajdują się ścieżki każdej właściwości z przykładowego elementu opisanego powyżej:

- /Locations/0/Country: "Niemcy"
- /Locations/0/City: "Berlin"
- /Locations/1/Country: "Francja"
- /Locations/1/City: "Paryż"
- /Headquarters/Country: "Belgia"
- /Headquarters/Employees: 250
- /exports/0/City: "Moskwa"
- /exports/1/City: "Ateny"

Po zapisaniu elementu Azure Cosmos DB efektywnie indeksuje ścieżkę każdej właściwości i odpowiadającą jej wartość.

## <a name="index-kinds"></a>Rodzaje indeksów

Azure Cosmos DB obecnie obsługuje trzy rodzaje indeksów.

### <a name="range-index"></a>Indeks zakresu

Indeks **zakresu** jest oparty na uporządkowanej strukturze podobnej do drzewa. Rodzaj indeksu zakresu jest używany dla:

- Zapytania o równość:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Dopasowanie równości w elemencie tablicy
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Zapytania zakresu:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (działa dla `>` , `<` ,,, `>=` `<=` `!=` )

- Sprawdzanie obecności właściwości:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Funkcje systemowe ciągów:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` wybiera

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` wybiera

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Indeksów zakresu można używać w przypadku wartości skalarnych (String lub Number).

### <a name="spatial-index"></a>Indeks przestrzenny

Indeksy **przestrzenne** umożliwiają wydajne zapytania dotyczące obiektów geoprzestrzennych, takich jak punkty, linie, wielokąty i MultiPolygon. Te zapytania używają ST_DISTANCE, ST_WITHIN ST_INTERSECTS słów kluczowych. Poniżej przedstawiono kilka przykładów, które używają rodzaju indeksu przestrzennego:

- Zapytania dotyczące odległości geograficznej:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoprzestrzenne w ramach zapytań:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Zapytania z przecięciem geograficznym:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Indeksów przestrzennych można używać w poprawnie sformatowanych obiektach [GEOJSON](geospatial.md) . Punkty, LineStrings, wielokąty i wielowielokąty są obecnie obsługiwane.

### <a name="composite-indexes"></a>Indeksy złożone

Indeksy **złożone** zwiększają wydajność podczas wykonywania operacji na wielu polach. Typ indeksu złożonego jest używany dla:

- `ORDER BY` zapytania dotyczące wielu właściwości:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem i `ORDER BY` . Te zapytania mogą korzystać z indeksu złożonego, jeśli do klauzuli zostanie dodana Właściwość Filter `ORDER BY` .

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Wykonuje zapytania z filtrem dla dwóch lub więcej właściwości, w których co najmniej jedna właściwość jest filtrem równości

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Tak długo, jak jeden predykat filtru używa jednego z rodzajów indeksu, aparat zapytań obliczy to przed skanowaniem reszty. Na przykład jeśli masz zapytanie SQL, takie jak `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Powyższe zapytanie najpierw filtruje wpisy, w których firstName = "Andrew" przy użyciu indeksu. Następnie przekazuje wszystkie wpisy firstName = "Andrew" przy użyciu kolejnego potoku, aby obliczyć predykat zawierający filtr.

* Można przyspieszyć zapytania i uniknąć pełnego skanowania kontenera podczas korzystania z funkcji, które nie używają indeksu (np. CONTAINS) przez dodanie dodatkowych predykatów filtru, które używają indeksu. Kolejność klauzul filtru nie jest ważna. Aparat zapytań będzie określać, które predykaty są bardziej wybiórcze i odpowiednio uruchomić zapytanie.

## <a name="querying-with-indexes"></a>Wykonywanie zapytań przy użyciu indeksów

Ścieżki wyodrębnione podczas indeksowania danych ułatwiają wyszukiwanie w indeksie podczas przetwarzania zapytania. Dopasowując `WHERE` klauzulę zapytania z listą ścieżek indeksowanych, można łatwo zidentyfikować elementy, które pasują do predykatu zapytania.

Rozważmy na przykład następujące zapytanie: `SELECT location FROM location IN company.locations WHERE location.country = 'France'` . Predykat zapytania (filtrowanie dla elementów, gdzie każda lokalizacja ma wartość "Francja", ponieważ jego kraj/region) byłoby zgodny z ścieżką wyróżnioną w kolorze czerwonym poniżej:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Poprzedni element reprezentowany jako drzewo" border="false":::

> [!NOTE]
> `ORDER BY`Klauzula, która porządkuje według pojedynczej właściwości, *zawsze* wymaga indeksu zakresu i zakończy się niepowodzeniem, jeśli ścieżka, do której się odwołuje, nie ma takiej wartości. Podobnie, `ORDER BY` zapytanie, które porządkuje wiele właściwości, *zawsze* wymaga indeksu złożonego.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat indeksowania w następujących artykułach:

- [Zasady indeksowania](index-policy.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
