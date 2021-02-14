---
title: Azure Cosmos DB zasad indeksowania
description: Dowiedz się, jak skonfigurować i zmienić domyślne zasady indeksowania dla automatycznego indeksowania i zwiększenia wydajności w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: tisande
ms.openlocfilehash: 26465eb9826c60daad7b44e1c2fe6ae3c19b1ed0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378812"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Zasady indeksowania w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W usłudze Azure Cosmos DB każdy kontener ma zasady indeksowania, które określają sposób indeksowania elementów tego kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów indeksują każdą właściwość i każdy element oraz wymuszają indeksy zakresu dla wszelkich ciągów i liczb. Dzięki temu można uzyskać dobrą wydajność zapytań bez konieczności pomyślnego zaopiniowania indeksowania i zarządzania indeksem.

W niektórych sytuacjach może być potrzebne zastąpienie tego automatycznego zachowania zachowaniem lepiej dostosowanym do wymagań. Można dostosować zasady indeksowania kontenera, ustawiając jego *tryb indeksowania* i dołączając lub wykluczając *ścieżki właściwości*.

> [!NOTE]
> Metoda aktualizacji zasad indeksowania opisana w tym artykule ma zastosowanie tylko do interfejsu API SQL (Core) Azure Cosmos DB. Dowiedz się więcej na temat indeksowania w [interfejsie API Azure Cosmos DB MongoDB](mongodb-indexing.md)

## <a name="indexing-mode"></a>Tryb indeksowania

Azure Cosmos DB obsługuje dwa tryby indeksowania:

- **Spójne**: indeks jest aktualizowany synchronicznie podczas tworzenia, aktualizowania lub usuwania elementów. Oznacza to, że spójność zapytań odczytu będzie [spójna z konfiguracją dla konta](consistency-levels.md).
- **Brak**: indeksowanie jest wyłączone w kontenerze. Jest to często używane, gdy kontener jest używany jako czysty magazyn klucz-wartość bez konieczności stosowania indeksów pomocniczych. Może również służyć do poprawy wydajności operacji zbiorczych. Po zakończeniu operacji zbiorczych tryb indeksu może być ustawiony na spójny, a następnie monitorowany przy użyciu [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) do momentu ukończenia.

> [!NOTE]
> Azure Cosmos DB obsługuje również tryb indeksowania z opóźnieniem. Indeksowanie z opóźnieniem aktualizuje indeks na znacznie niższym poziomie priorytetu, gdy aparat nie wykonuje żadnej innej pracy. Może to doprowadzić do **niespójnych lub niekompletnych** wyników zapytań. Jeśli planujesz wysyłać zapytania względem kontenera Cosmos, nie wybieraj indeksowania z opóźnieniem. Nowe kontenery nie mogą wybrać indeksowania z opóźnieniem. Możesz zażądać wykluczenia, kontaktując się z [pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) (chyba że używasz konta usługi Azure Cosmos w trybie bez [serwera](serverless.md) , który nie obsługuje indeksowania z opóźnieniem).

Domyślnie zasady indeksowania są ustawione na `automatic` . Jest to osiągane przez ustawienie `automatic` właściwości w zasadach indeksowania na `true` . Ustawienie tej właściwości `true` umożliwia usłudze Azure CosmosDB Automatyczne indeksowanie dokumentów w miarę ich pisania.

## <a name="index-size"></a><a id="index-size"></a>Rozmiar indeksu

W Azure Cosmos DB łączny zużyty magazyn to kombinacja rozmiaru danych i rozmiaru indeksu. Poniżej przedstawiono niektóre funkcje rozmiaru indeksu:

* Rozmiar indeksu zależy od zasad indeksowania. Jeśli wszystkie właściwości są indeksowane, rozmiar indeksu może być większy niż rozmiar danych.
* Gdy dane są usuwane, indeksy są kompaktowe blisko siebie. Jednak w przypadku małych usunięć danych nie należy od razu obniżyć rozmiaru indeksu.
* Rozmiar indeksu można tymczasowo zwiększyć podczas dzielenia partycji fizycznych. Przestrzeń indeksu jest wydawana po zakończeniu podziału partycji.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Uwzględnianie i wykluczanie ścieżek właściwości

Niestandardowe zasady indeksowania mogą określać ścieżki właściwości, które są jawnie dołączone lub wykluczone z indeksowania. Przez optymalizację liczby indeksowanych ścieżek można znacznie ograniczyć opóźnienia i obciążenie RU operacji zapisu. Te ścieżki są zdefiniowane po [metodzie opisanej w sekcji Omówienie indeksowania](index-overview.md#from-trees-to-property-paths) z następującymi dodatkami:

- ścieżka prowadząca do wartości skalarnej (ciąg lub Number) ma koniec `/?`
- elementy z tablicy są rozłączone za pośrednictwem `/[]` notacji (zamiast `/0` `/1` itp.)
- `/*`symbol wieloznaczny może służyć do dopasowania elementów poniżej węzła

Ponowne wykonanie tego samego przykładu:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters` `employees` ścieżka jest`/headquarters/employees/?`

- `locations`" `country` ścieżka jest`/locations/[]/country/?`

- ścieżka do wszystkich elementów w obszarze `headquarters` to `/headquarters/*`

Na przykład możemy dołączyć `/headquarters/employees/?` ścieżkę. Ta ścieżka zapewni, że indeksuje Właściwość Employees, ale nie indeksuje dodatkowych zagnieżdżonych JSON w ramach tej właściwości.

## <a name="includeexclude-strategy"></a>Strategia uwzględniania/wykluczania

Każda zasada indeksowania musi zawierać ścieżkę katalogu głównego `/*` jako dołączoną lub wykluczoną ścieżkę.

- Uwzględnij ścieżkę główną, aby wykluczać ścieżki, które nie muszą być indeksowane. Jest to zalecane podejście, które pozwala Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.
- Wyklucz ścieżkę katalogu głównego do selektywnego dołączania ścieżek, które muszą być indeksowane.

- W przypadku ścieżek ze zwykłymi znakami, które zawierają: znaki alfanumeryczne i _ (podkreślenie), nie trzeba określać ciągu ścieżki wokół podwójnych cudzysłowów (na przykład "/Path/?"). W przypadku ścieżek z innymi znakami specjalnymi należy wypróbować ciąg ścieżki wokół podwójnych cudzysłowów (na przykład "/ \" Path-ABC \" /?"). Jeśli oczekujesz znaków specjalnych w ścieżce, możesz pominąć wszystkie ścieżki w celu zapewnienia bezpieczeństwa. Funkcjonalnie nie powoduje żadnych różnic w przypadku ucieczki wszystkich ścieżek, które zawierają znaki specjalne.

- Właściwość systemowa `_etag` jest domyślnie wykluczona z indeksowania, chyba że element ETag zostanie dodany do ścieżki dołączonej do indeksowania.

- Jeśli tryb indeksowania jest ustawiony na **spójne**, właściwości systemu `id` i `_ts` są indeksowane automatycznie.

W przypadku dołączania i wykluczania ścieżek mogą wystąpić następujące atrybuty:

- `kind` może być albo `range` `hash` . Obsługa indeksu wartości skrótu jest ograniczona do filtrów równości. Funkcja indeksu zakresu oferuje wszystkie funkcje indeksów skrótu, a także wydajne sortowanie, filtry zakresu i funkcje systemowe. Zawsze zalecamy użycie indeksu zakresu.

- `precision` jest liczbą zdefiniowaną na poziomie indeksu dla dołączonych ścieżek. Wartość `-1` wskazuje maksymalną precyzję. Zalecamy zawsze ustawienie tej wartości na `-1` .

- `dataType` może być albo `String` `Number` . Wskazuje typy właściwości JSON, które będą indeksowane.

Gdy nie zostanie określony, te właściwości będą miały następujące wartości domyślne:

| **Nazwa właściwości**     | **Wartość domyślna** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` i `Number` |

Zapoznaj się z [tą sekcją](how-to-manage-indexing-policy.md#indexing-policy-examples) , aby zapoznać się z przykładami zasad indeksowania obejmującymi i wykluczającymi ścieżki.

## <a name="includeexclude-precedence"></a>Pierwszeństwo uwzględniania/wykluczania

Jeśli zawarte ścieżki i wykluczone ścieżki mają konflikt, pierwszeństwo ma dokładniejszą ścieżkę.

Oto przykład:

**Ścieżka uwzględniona**: `/food/ingredients/nutrition/*`

**Wykluczona ścieżka**: `/food/ingredients/*`

W takim przypadku dołączona ścieżka ma pierwszeństwo przed wykluczoną ścieżką, ponieważ jest bardziej precyzyjna. W oparciu o te ścieżki wszelkie dane ze `food/ingredients` ścieżki lub zagnieżdżone w ramach zostałyby wykluczone z indeksu. Wyjątek mógłby zawierać dane w dołączonej ścieżce: `/food/ingredients/nutrition/*` , które byłyby indeksowane.

Poniżej przedstawiono niektóre reguły pierwszeństwa zawartych i wykluczonych ścieżek w Azure Cosmos DB:

- Dokładniejsze ścieżki są bardziej precyzyjne niż wąskie ścieżki. na przykład: `/a/b/?` jest bardziej precyzyjna niż `/a/?` .

- `/?`Jest bardziej precyzyjna niż `/*` . Na przykład `/a/?` ma dokładniejszy `/a/*` `/a/?` priorytet.

- Ścieżka `/*` musi być ścieżką dołączoną lub wykluczoną.

## <a name="spatial-indexes"></a>Indeksy przestrzenne

Podczas definiowania ścieżki przestrzennej w zasadach indeksowania należy określić, który indeks ```type``` ma zostać zastosowany do tej ścieżki. Możliwe typy indeksów przestrzennych obejmują:

* Moment

* Wielokąt

* MultiPolygon

* LineString

Domyślnie Azure Cosmos DB nie utworzy żadnych indeksów przestrzennych. Jeśli chcesz używać przestrzennych funkcji języka SQL, należy utworzyć indeks przestrzenny dla wymaganych właściwości. Zobacz [tę sekcję](sql-query-geospatial-index.md) , aby zapoznać się z przykładami zasad indeksowania służącymi do dodawania indeksów przestrzennych.

## <a name="composite-indexes"></a>Indeksy złożone

Zapytania, które mają `ORDER BY` klauzulę mającą co najmniej dwie właściwości, wymagają złożonego indeksu. Można również zdefiniować indeks złożony, aby zwiększyć wydajność wielu zapytań równości i zakresu. Domyślnie nie są zdefiniowane żadne indeksy złożone, dlatego należy [dodać indeksy złożone](how-to-manage-indexing-policy.md#composite-index) zgodnie z wymaganiami.

W przeciwieństwie do ścieżek uwzględnionych lub wykluczonych nie można utworzyć ścieżki z `/*` symbolem wieloznacznym. Każda ścieżka złożona ma niejawną `/?` na końcu ścieżki, której nie trzeba określać. Ścieżki złożone prowadzą do wartości skalarnej i jest to jedyna wartość, która jest uwzględniona w indeksie złożonym.

Podczas definiowania indeksu złożonego należy określić:

- Co najmniej dwie ścieżki właściwości. Sekwencja, w której są zdefiniowane ścieżki właściwości.

- Zamówienie (rosnąco lub malejąco).

> [!NOTE]
> Po dodaniu indeksu złożonego zapytanie będzie używać istniejących indeksów zakresu do momentu ukończenia nowego złożonego dodawania indeksu. W związku z tym, po dodaniu indeksu złożonego, nie należy od razu obserwować ulepszeń wydajności. Istnieje możliwość śledzenia postępu transformacji indeksu przy [użyciu jednego z zestawów SDK](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Zaporządkuj według zapytań dotyczących wielu właściwości:

Poniższe zagadnienia są używane podczas używania indeksów złożonych dla zapytań z `ORDER BY` klauzulą z co najmniej dwiema właściwościami:

- Jeśli złożone ścieżki indeksu nie pasują do sekwencji właściwości w `ORDER BY` klauzuli, indeks złożony nie obsługuje zapytania.

- Kolejność ścieżek złożonych indeksów (rosnąco lub malejąco) powinna również być zgodna z `order` `ORDER BY` klauzulą w klauzuli.

- Indeks złożony również obsługuje `ORDER BY` klauzulę z odwrotną kolejnością we wszystkich ścieżkach.

Rozważmy następujący przykład, w którym zdefiniowano indeks złożony w nazwach właściwości, wieku i _ts:

| **Indeks złożony**     | **Przykładowe `ORDER BY` zapytanie**      | **Obsługiwane przez indeks złożony?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Zasady indeksowania należy dostosować, aby można było obsłużyć wszystkie niezbędne `ORDER BY` zapytania.

### <a name="queries-with-filters-on-multiple-properties"></a>Zapytania z filtrami na wielu właściwościach

Jeśli zapytanie zawiera filtry dla co najmniej dwóch właściwości, warto utworzyć indeks złożony dla tych właściwości.

Rozważmy na przykład następujące zapytanie, które ma zarówno filtr równości, jak i zakres:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

To zapytanie będzie bardziej wydajne, co zajmuje mniej czasu i zużywa mniejszą liczbę jednostek RU, jeśli może korzystać ze złożonego indeksu w `(name ASC, age ASC)` .

Zapytania z wieloma filtrami zakresów można także zoptymalizować przy użyciu indeksu złożonego. Jednak każdy indywidualny indeks złożony może zoptymalizować tylko jeden filtr zakresu. Filtry zakresu obejmują `>` , `<` , `<=` , `>=` , i `!=` . Filtr zakresu powinien być zdefiniowany jako ostatni w indeksie złożonym.

Rozważmy następujące zapytanie z filtrem równości i dwoma zakresami filtrów:

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

To zapytanie będzie bardziej wydajne z indeksem złożonym w systemach `(name ASC, age ASC)` i `(name ASC, _ts ASC)` . Jednak zapytanie nie używa indeksu złożonego, `(age ASC, name ASC)` ponieważ właściwości z filtrami równości muszą być zdefiniowane jako pierwsze w indeksie złożonym. Dwa oddzielne indeksy złożone są wymagane zamiast pojedynczego indeksu złożonego, `(name ASC, age ASC, _ts ASC)` ponieważ każdy indeks złożony może zoptymalizować tylko jeden filtr zakresu.

Poniższe zagadnienia są używane podczas tworzenia indeksów złożonych dla zapytań z filtrami dla wielu właściwości

- Wyrażenia filtru mogą używać wielu indeksów złożonych.
- Właściwości w filtrze zapytania powinny być zgodne z tymi w indeksie złożonym. Jeśli właściwość znajduje się w indeksie złożonym, ale nie jest uwzględniona w zapytaniu jako filtr, zapytanie nie będzie korzystać z indeksu złożonego.
- Jeśli zapytanie ma dodatkowe właściwości w filtrze, który nie został zdefiniowany w indeksie złożonym, wówczas do obliczenia zapytania zostanie użyta kombinacja indeksów złożonych i zakresów. Będzie to wymagało mniejszej liczby jednostek RU niż w przypadku używania indeksów zakresów.
- Jeśli właściwość ma filtr zakresu ( `>` ,,, `<` `<=` `>=` lub `!=` ), wówczas ta właściwość powinna być zdefiniowana jako Ostatnia w indeksie złożonym. Jeśli zapytanie ma więcej niż jeden filtr zakresu, może ono korzystać z wielu indeksów złożonych.
- Podczas tworzenia indeksu złożonego do optymalizowania zapytań z wieloma filtrami `ORDER` indeks złożony nie będzie miał wpływu na wyniki. Ta właściwość jest opcjonalna.

Rozważmy następujące przykłady, w których zdefiniowany jest indeks złożony w polu Nazwa właściwości, wiek i sygnatura czasowa:

| **Indeks złożony**     | **Przykładowe zapytanie**      | **Obsługiwane przez indeks złożony?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>Zapytania z filtrem i KOLEJNOŚCIą według

Jeśli zapytanie filtruje dla jednej lub wielu właściwości i ma inne właściwości w klauzuli ORDER BY, może być pomocne dodanie właściwości w filtrze do `ORDER BY` klauzuli.

Na przykład dodając właściwości w filtrze do `ORDER BY` klauzuli, można ponownie napisać następujące zapytanie, aby użyć złożonego indeksu:

Zapytanie przy użyciu indeksu zakresu:

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

Zapytanie przy użyciu indeksu złożonego:

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

Te same optymalizacje zapytań można uogólnione dla wszystkich `ORDER BY` zapytań z filtrami, pamiętając, że poszczególne indeksy złożone mogą obsługiwać tylko jeden filtr zakresu.

Zapytanie przy użyciu indeksu zakresu:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

Zapytanie przy użyciu indeksu złożonego:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

Ponadto można użyć indeksów złożonych, aby zoptymalizować zapytania z funkcjami systemowymi i KOLEJNOŚCIą według:

Zapytanie przy użyciu indeksu zakresu:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

Zapytanie przy użyciu indeksu złożonego:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

Podczas tworzenia indeksów złożonych w celu optymalizacji zapytania z klauzulą Filter i należy stosować następujące zagadnienia `ORDER BY` :

* Jeśli nie zdefiniujesz indeksu złożonego w zapytaniu z filtrem dla jednej właściwości i oddzielną `ORDER BY` klauzulą używającą innej właściwości, zapytanie będzie nadal kończyć się powodzeniem. Koszt RU zapytania można jednak zmniejszyć przy użyciu indeksu złożonego, szczególnie jeśli właściwość w `ORDER BY` klauzuli ma wysoką Kardynalność.
* Jeśli zapytanie filtruje właściwości, należy je najpierw uwzględnić w `ORDER BY` klauzuli.
* Jeśli zapytanie jest filtrowane dla wielu właściwości, filtry równości muszą być pierwszymi właściwościami w `ORDER BY` klauzuli.
* Jeśli zapytanie filtruje wiele właściwości, można użyć maksymalnie jednego filtru zakresu lub funkcji systemowej dla indeksu złożonego. Właściwość użyta w filtrze zakresu lub funkcji systemowej powinna być zdefiniowana jako Ostatnia w indeksie złożonym.
* Wszystkie zagadnienia dotyczące tworzenia indeksów złożonych dla `ORDER BY` zapytań z wieloma właściwościami, a także zapytania z filtrami dla wielu właściwości nadal mają zastosowanie.


| **Indeks złożony**                      | **Przykładowe `ORDER BY` zapytanie**                                  | **Obsługiwane przez indeks złożony?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>Zapytania z filtrem i agregacją 

Jeśli zapytanie filtruje dla jednej lub wielu właściwości i ma zagregowaną funkcję systemową, warto utworzyć indeks złożony dla właściwości w funkcji filtrowania i agregacji systemu. Ta optymalizacja ma zastosowanie do funkcji [Suma](sql-query-aggregate-sum.md) i [średnia](sql-query-aggregate-avg.md) .

Poniższe uwagi dotyczą tworzenia indeksów złożonych w celu zoptymalizowania zapytania za pomocą funkcji filtrowania i agregacji systemu.

* Indeksy złożone są opcjonalne podczas wykonywania zapytań z agregacjami. Koszt RU zapytania można jednak często znacznie zmniejszyć przy użyciu indeksu złożonego.
* Jeśli zapytanie jest filtrowane dla wielu właściwości, filtry równości muszą być pierwszymi właściwościami w indeksie złożonym.
* Można mieć maksymalnie jeden filtr zakresu dla indeksu złożonego i musi on znajdować się we właściwości w funkcji agregującej system.
* Właściwość w agregowanej funkcji systemowej powinna być zdefiniowana jako Ostatnia w indeksie złożonym.
* `order`( `ASC` Lub) nie `DESC` ma znaczenia.

| **Indeks złożony**                      | **Przykładowe zapytanie**                                  | **Obsługiwane przez indeks złożony?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="index-transformationmodifying-the-indexing-policy"></a><>modyfikowania zasad indeksowania

Zasady indeksowania kontenera można aktualizować w dowolnym momencie przy [użyciu Azure Portal lub jednego z obsługiwanych zestawów SDK](how-to-manage-indexing-policy.md). Aktualizacja zasad indeksowania wyzwala transformację ze starego indeksu do nowego, który jest wykonywany w trybie online i w miejscu (dlatego w trakcie operacji nie jest używane dodatkowe miejsce do magazynowania). Stare zasady indeksowania są efektywnie przekształcane w nowe zasady bez wpływu na dostępność zapisu, dostępność odczytu lub przepływność zainicjowaną na tym kontenerze. Przekształcanie indeksów jest operacją asynchroniczną i czas potrzebny do ukończenia zależy od przepływności, liczby elementów i ich rozmiaru.

> [!IMPORTANT]
> Przekształcanie indeksów to operacja, która zużywa [jednostki żądania](request-units.md). Jednostki żądań zużywane przez transformację indeksu nie są obecnie rozliczane, jeśli używasz kontenerów [bezserwerowych](serverless.md) . Te jednostki żądania będą rozliczane, gdy bezserwerowy staną się ogólnie dostępne.

> [!NOTE]
> Istnieje możliwość śledzenia postępu transformacji indeksu przy [użyciu jednego z zestawów SDK](how-to-manage-indexing-policy.md).

Nie ma to wpływu na zapisywanie dostępności podczas przekształcania indeksów. Przekształcenie indeksu używa zainicjowanego jednostek ru, ale ma niższy priorytet niż operacje CRUD lub zapytania.

Podczas dodawania nowego indeksu nie ma wpływu na dostępność. Zapytania będą korzystać tylko z nowych indeksów po zakończeniu przekształcania indeksu. Podczas przekształcania indeksu aparat zapytań będzie kontynuował korzystanie z istniejących indeksów, więc zobaczysz podobną wydajność odczytu podczas transformacji indeksowania do zaobserwowanego przed zainicjowaniem zmiany indeksowania. Przy dodawaniu nowych indeksów nie jest również ryzykowne żadne niekompletne lub niespójne wyniki zapytania.

W przypadku usuwania indeksów i natychmiastowego uruchamiania zapytań, które odfiltrują się do usuniętych indeksów, nie istnieje gwarancja spójnych lub pełnych wyników zapytania. W przypadku usunięcia wielu indeksów i przeprowadzenia tej operacji w ramach jednej pojedynczej zmiany zasad indeksowania aparat zapytań zapewnia spójne i kompletne wyniki w całej transformacji indeksu. Jeśli jednak usuniesz indeksy przy użyciu wielu zmian zasad indeksowania, aparat zapytań nie będzie zapewniać spójnych ani kompletnych wyników do momentu zakończenia wszystkich przekształceń indeksu. Większość deweloperów nie porzuca indeksów, a następnie natychmiast próbuje uruchomić zapytania, które używają tych indeksów, w przeciwnym razie ta sytuacja jest mało prawdopodobne.

> [!NOTE]
> Jeśli to możliwe, zawsze należy próbować grupować wiele zmian indeksowania w jedną modyfikację pojedynczej zasady indeksowania

## <a name="indexing-policies-and-ttl"></a>Zasady indeksowania i czas wygaśnięcia

Korzystanie z [funkcji Time-to-Live (TTL)](time-to-live.md) wymaga indeksowania. Oznacza to, że:

- nie można aktywować czasu wygaśnięcia w kontenerze, w którym jest ustawiony tryb indeksowania `none` ,
- nie można ustawić trybu indeksowania na brak w kontenerze, w którym jest aktywowany czas wygaśnięcia.

W przypadku scenariuszy, w których nie ma potrzeby indeksowania ścieżki właściwości, ale czas wygaśnięcia jest wymagany, można użyć zasad indeksowania z trybem indeksowania ustawionym na `consistent` , brak dołączonych ścieżek i `/*` jako jedynej wykluczonej ścieżki.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Jak zarządzać zasadami indeksowania](how-to-manage-indexing-policy.md)
