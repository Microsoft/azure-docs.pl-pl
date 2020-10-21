---
title: Azure Cosmos DB obsługę Gremlin i zgodność z funkcjami TinkerPop
description: Poznaj język Gremlin ze struktury Apache TinkerPop. Dowiedz się, które funkcje i kroki są dostępne w Azure Cosmos DB i różnice zgodności aparatu grafu TinkerPop.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 10/13/2020
ms.author: sngun
ms.openlocfilehash: f435185d0f00d8f64425e3f2b7081e0ee9a393ce
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276223"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Azure Cosmos DB obsługę programu Graph i zgodność z funkcjami TinkerPop

Azure Cosmos DB obsługuje język przechodzenia wykresu [Apache Tinkerpop](https://tinkerpop.apache.org) , znany jako [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Język Gremlin służy do tworzenia jednostek grafu (wierzchołków i krawędzi), modyfikacji właściwości w ramach tych elementów, wykonywania zapytań i przejść oraz usuwania elementów.

Aparat Azure Cosmos DB Graph jest ściśle opisany w specyfikacji kroków przechodzenia [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ale istnieją różnice w implementacji, które są specyficzne dla Azure Cosmos DB. W tym artykule udostępniamy Krótki przewodnik Gremlin i wyliczamy funkcje Gremlin, które są obsługiwane przez interfejs API Gremlin.

## <a name="compatible-client-libraries"></a>Zgodne biblioteki klienckie

W poniższej tabeli przedstawiono popularne sterowniki Gremlin, których można użyć do usługi Azure Cosmos DB:

| Pobierz | Element źródłowy | Getting Started | Obsługiwana wersja łącznika |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Gremlin.NET w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Tworzenie grafu przy użyciu platformy .NET](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Tworzenie grafu przy użyciu środowiska Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Tworzenie grafu przy użyciu platformy Node.js](create-graph-nodejs.md) | 3.3.4 + |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python w witrynie GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Tworzenie grafu przy użyciu środowiska Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP w witrynie GitHub](https://github.com/PommeVerte/gremlin-php) | [Tworzenie grafu przy użyciu środowiska PHP](create-graph-php.md) | 3.1.0 |
| [Konsola Gremlin](https://tinkerpop.apache.org/downloads.html) | [Dokumentacja dotycząca witryny TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Tworzenie grafu przy użyciu Konsoli Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Obsługiwane obiekty grafu

TinkerPop jest standardem, który obejmuje szeroki zakres technologii grafów. Dlatego ma standardową terminologię do opisywania, jakie funkcje są udostępniane przez dostawcę grafu. Usługa Azure Cosmos DB zapewnia trwałą, zapisywalną bazę danych grafów o dużej współbieżności, którą można podzielić na partycje w wielu serwerach lub klastrach. 

W poniższej tabeli wymieniono funkcje struktury TinkerPop wdrażane przez usługę Azure Cosmos DB: 

| Kategoria | Wdrożenie usługi Azure Cosmos DB |  Uwagi | 
| --- | --- | --- |
| Funkcjonalności grafu | Zapewnia funkcjonalności Persistence i ConcurrentAccess. Zaprojektowana obsługa funkcjonalności Transactions | Metody komputera mogą być wdrażane przy użyciu łącznika Spark. |
| Funkcjonalności zmiennych | Obsługuje zmienne Boolean, Byte, Double, Float, Integer, Long, String | Obsługuje typy pierwotne, a zgodność z typami złożonymi jest osiągana za pomocą modelu danych |
| Funkcjonalności wierzchołków | Obsługuje funkcje RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Obsługuje tworzenie, modyfikowanie i usuwanie wierzchołków |
| Funkcjonalności właściwości wierzchołków | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości wierzchołków |
| Funkcjonalności krawędzi | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Obsługuje tworzenie, modyfikowanie i usuwanie krawędzi |
| Funkcjonalności właściwości krawędzi | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Obsługuje tworzenie, modyfikowanie i usuwanie właściwości krawędzi |

## <a name="gremlin-wire-format"></a>Format sieci Gremlin

Azure Cosmos DB używa formatu JSON podczas zwracania wyników z operacji Gremlin. Azure Cosmos DB obecnie obsługuje format JSON. Na przykład poniższy fragment kodu przedstawia reprezentację w formacie JSON wierzchołka *zwróconego do klienta* z Azure Cosmos DB:

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Poniżej opisano właściwości używane w formacie JSON dla wierzchołków:

| Właściwość | Opis | 
| --- | --- | --- |
| `id` | Identyfikator wierzchołka. Musi być unikatowa (w połączeniu z wartością, `_partition` Jeśli ma zastosowanie). Jeśli nie podano wartości, zostanie ona automatycznie dostarczona z identyfikatorem GUID | 
| `label` | Etykieta wierzchołka. Ta właściwość służy do opisywania typu jednostki. |
| `type` | Służy do odróżnienia wierzchołków od dokumentów bez grafów |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z wierzchołkiem. Każda właściwość może mieć wiele wartości. |
| `_partition` | Klucz partycji wierzchołka. Używany do [partycjonowania grafu](graph-partitioning.md). |
| `outE` | Ta właściwość zawiera listę krawędzi poza wierzchołkiem. Przechowywanie informacji sąsiedztwa razem z wierzchołkiem umożliwia szybkie wykonanie przejść. Krawędzie są pogrupowane w oparciu o etykiety. |

Krawędź zawiera następujące informacje, aby pomóc w nawigacji do innych części grafu.

| Właściwość | Opis |
| --- | --- |
| `id` | Identyfikator krawędzi. Musi być unikatowa (w połączeniu z wartością, `_partition` Jeśli ma zastosowanie) |
| `label` | Etykieta krawędzi. Ta właściwość jest opcjonalna i służy do opisu typu relacji. |
| `inV` | Ta właściwość zawiera listę wierzchołków dla krawędzi. Przechowywanie informacji sąsiedztwa razem z krawędzią umożliwia szybkie wykonanie przejść. Wierzchołki są pogrupowane w oparciu o etykiety. |
| `properties` | Pakiet właściwości zdefiniowanych przez użytkownika skojarzonych z krawędzią. Każda właściwość może mieć wiele wartości. |

Każda właściwość może przechowywać wiele wartości w tablicy. 

| Właściwość | Opis |
| --- | --- |
| `value` | Wartość właściwości.

## <a name="gremlin-steps"></a>Kroki w środowisku Gremlin

Teraz przyjrzyjmy się krokom w środowisku Gremlin obsługiwanym przez usługę Azure Cosmos DB. Aby uzyskać pełną dokumentację dotyczącą języka Gremlin, zobacz [odwołanie do struktury TinkerPop](https://tinkerpop.apache.org/docs/3.3.2/reference).

| krok | Opis | Dokumentacja dotycząca struktury TinkerPop 3.2 |
| --- | --- | --- |
| `addE` | Dodaje krawędź między dwoma wierzchołkami | [krok addE](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Dodaje wierzchołek do grafu | [krok addV](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Gwarantuje, że wszystkie przejścia zwracają wartość | [krok and](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Modulator kroku do przypisania zmiennej do wyniku kroku | [krok as](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | Modulator kroku używany z elementami `group` i `order` | [krok by](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Zwraca pierwsze przejście, które zwraca wynik | [krok coalesce](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Zwraca wartość stałą. Używany z krokiem `coalesce`| [krok constant](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Zwraca liczbę z przejścia | [krok count](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Zwraca wartości z usuniętymi duplikatami | [krok dedup](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Upuszcza wartości (wierzchołek/krawędź) | [krok drop](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Tworzy opis wszystkich operacji generowanych przez wykonany krok Gremlin | [executionProfile — krok](graph-execution-profile.md) |
| `fold` | Działa jak bariera, która oblicza agregację wyników| [krok fold](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Grupuje wartości w oparciu o określone etykiety| [krok group](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Służy do filtrowania właściwości, wierzchołków i krawędzi. Obsługuje warianty `hasLabel`, `hasId`, `hasNot` i `has`. | [krok step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Wstawia wartości do strumienia| [krok inject](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Służy do wykonywania filtru przy użyciu wyrażenia logicznego | [krok is](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Pozwala ograniczyć liczbę elementów podczas przechodzenia| [krok limit](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Krok local opakowuje sekcję przejścia, podobnie jak podzapytanie | [krok local](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Służy do tworzenia negacji filtru | [krok not](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Zwraca wynik określonego przejścia, jeśli wstrzymuje wynik lub zwraca wywołujący element | [krok opcjonalny](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Gwarantuje, że co najmniej jedno przejście zwróci wartość | [krok or](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Zwraca wyniki w określonej kolejności sortowania | [krok order](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Zwraca pełną ścieżkę przejścia | [krok path](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projektuje właściwości jako mapę | [krok project](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Zwraca właściwości dla określonych etykiet | [krok properties](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtruje do określonego zakresu wartości| [krok range](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Powtarza krok określoną liczbę razy. Używany do zapętlenia | [krok repeat](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Służy do próbkowania wyników z przejścia | [krok sample](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Służy do projektowania wyników z przejścia |  [krok select](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Używany do nieblokujących agregacji z przejścia | [krok store](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość do początku danego ciągu | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość do końca danego ciągu | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość do zawartości danego ciągu | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość, która nie zaczyna się od danego ciągu | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość, która nie kończy się podanym ciągiem | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Funkcja filtrowania ciągów. Ta funkcja jest używana jako predykat dla kroku, `has()` Aby dopasować właściwość, która nie zawiera danego ciągu | [Predykaty TextP](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Agreguje ścieżki z wierzchołka do drzewa | [krok tree](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Odwija iterator w ramach kroku| [krok unfold](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Scalanie wyników z wielu przejść| [krok union](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Zawiera kroki niezbędne do przejść między wierzchołkami i krawędziami `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV`, oraz `otherV` do | [kroki vertex](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Służy do filtrowania wyników z przejścia. Obsługuje operatory `eq`, `neq`, `lt`, `lte`, `gt`, `gte` i `between`  | [krok where](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Aparat zoptymalizowany pod kątem zapisu oferowany w usłudze Azure Cosmos DB obsługuje domyślnie automatyczne indeksowanie wszystkich właściwości w wierzchołkach i krawędziach. W związku z tym zapytania z filtrami, zapytania zakresu, sortowanie lub agregacje na dowolnej właściwości są przetwarzane z indeksu i skutecznie obsługiwane. Więcej informacji na temat działania indeksowania w usłudze Azure Cosmos DB znajduje się w dokumencie dotyczącym [indeksowania niezależnie od schematu](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="behavior-differences"></a>Różnice w zachowaniu

* Aparat grafu Azure Cosmos DB uruchamia przechodzenie w ***pierwszej kolejności*** , podczas gdy TinkerPop Gremlin jest głębokością. Takie zachowanie zapewnia lepszą wydajność w skalowalnym systemie, takim jak Cosmos DB.

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

* ***[Kod bajtowy języka Gremlin](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** to specyfikacja przechodzenia przez graf niezależna od języka programowania. Program Cosmos DB Graph jeszcze nie obsługuje tego programu. Użyj `GremlinClient.SubmitAsync()` i przekaż przechodzenie jako ciąg tekstowy.

* ***`property(set, 'xyz', 1)`*** ustawienie Kardynalność nie jest obecnie obsługiwane. Zamiast tego użyj polecenia cmdlet `property(list, 'xyz', 1)`. Aby dowiedzieć się więcej, zobacz [Właściwości wierzchołka z TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* Ten *** `match()` krok*** nie jest obecnie dostępny. Ten krok zapewnia możliwości deklaracyjnego wykonywania zapytań.

* ***Obiekty jako właściwości*** dla wierzchołków lub krawędzi nie są obsługiwane. Właściwości mogą być tylko typami pierwotnymi lub tablicami.

* ***Sortowanie według właściwości tablicy*** `order().by(<array property>)` nie jest obsługiwana. Sortowanie jest obsługiwane tylko według typów pierwotnych.

* ***Niepierwotne typy JSON*** nie są obsługiwane. Użyj `string` `number` typów,, lub `true` / `false` . `null` wartości nie są obsługiwane. 

* Serializator ***GraphSONv3*** nie jest obecnie obsługiwany. `GraphSONv2`W konfiguracji połączenia użyj klas serializatorów, czytników i składników zapisywania. Wyniki zwrócone przez interfejs API Azure Cosmos DB Gremlin nie mają takiego samego formatu jak format GraphSON. 

* **Wyrażenia lambda i funkcje** nie są obecnie obsługiwane. Obejmuje to `.map{<expression>}` `.by{<expression>}` funkcje, i `.filter{<expression>}` . Aby dowiedzieć się więcej, i dowiedzieć się, jak ponownie napisać je za pomocą kroków Gremlin, zobacz [uwagi na temat wyrażeń lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Transakcje*** nie są obsługiwane z powodu rozproszonego charakteru systemu.  Skonfiguruj odpowiedni model spójności na koncie Gremlin na "odczytywanie własnych zapisów" i Użyj optymistycznej współbieżności, aby rozwiązać konflikty zapisów.

## <a name="known-limitations"></a>Znane ograniczenia

* **Użycie indeksu dla zapytań Gremlin z `.V()` etapami przechodzenia do częściowej**: obecnie tylko pierwsze `.V()` wywołanie przechodzenia spowoduje użycie indeksu w celu rozpoznania dowolnych filtrów lub predykatów, do których dołączono. Kolejne wywołania nie zapoznają się z indeksem, co może spowodować wydłużenie czasu oczekiwania i kosztów zapytania.
    
    Przy założeniu domyślnego indeksowania, typowe zapytanie Gremlin odczytu, które rozpoczyna się od `.V()` kroku, będzie używać parametrów w dołączonych krokach filtrowania, takich jak `.has()` lub `.where()` w celu optymalizacji kosztu i wydajności zapytania. Na przykład:

    ```java
    g.V().has('category', 'A')
    ```

    Jednak `.V()` w przypadku dołączenia więcej niż jednego kroku do zapytania Gremlin rozpoznawanie danych dla zapytania może nie być optymalne. Wykonaj następujące zapytanie jako przykład:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    To zapytanie zwróci dwie grupy wierzchołków na podstawie ich właściwości o nazwie `category` . W takim przypadku tylko pierwsze wywołanie `g.V().has('category', 'A')` spowoduje użycie indeksu do rozpoznawania wierzchołków na podstawie wartości właściwości.

    Obejście tego zapytania polega na użyciu podprocedur przechodzenia, takich jak `.map()` i `union()` . Jest to exemplified poniżej:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Wydajność zapytań można sprawdzić za pomocą [ `executionProfile()` kroku Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Następne kroki

* Rozpocznij tworzenie aplikacji grafu [przy użyciu zestawów SDK firmy Microsoft](create-graph-dotnet.md) 
* Dowiedz się więcej na temat [obsługi grafów](graph-introduction.md) w usłudze Azure Cosmos DB
