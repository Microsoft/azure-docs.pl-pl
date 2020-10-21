---
title: Partycjonowanie w Azure Cosmos DB interfejs API Cassandra
description: Informacje na temat partycjonowania w Azure Cosmos DB interfejs API Cassandra
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: f12919cd35441c6c198269e2f79c705c1d304acd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278819"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Partycjonowanie w Azure Cosmos DB interfejs API Cassandra

W tym artykule opisano, jak partycjonowanie działa w Azure Cosmos DB interfejs API Cassandra. 

Interfejs API Cassandra używa partycjonowania do skalowania poszczególnych tabel w przestrzeni kluczy w celu spełnienia wymagań dotyczących wydajności aplikacji. Partycje są tworzone na podstawie wartości klucza partycji, który jest skojarzony z każdym rekordem w tabeli. Wszystkie rekordy w partycji mają tę samą wartość klucza partycji. Azure Cosmos DB w sposób przezroczysty i automatycznie zarządza umieszczaniem partycji w zasobach fizycznych, aby skutecznie spełnić wymagania dotyczące skalowalności i wydajności tabeli. W miarę wzrostu wymagań dotyczących przepływności i magazynowania aplikacji Azure Cosmos DB przenosi i równoważy dane na większej liczbie maszyn fizycznych.

W perspektywie dewelopera partycjonowanie zachowuje się w taki sam sposób w przypadku Azure Cosmos DB interfejs API Cassandra, jak w przypadku natywnego rozwiązania [Apache Cassandra](https://cassandra.apache.org/). Istnieją jednak pewne różnice w tle. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Różnice między programami Apache Cassandra i Azure Cosmos DB

W Azure Cosmos DB każda maszyna, na której są przechowywane partycje, jest sama nazywana [partycją fizyczną](partitioning-overview.md#physical-partitions). Partycja fizyczna to zbliżone maszyny wirtualnej; dedykowana jednostka obliczeniowa lub zbiór zasobów fizycznych. Każda partycja przechowywana w tej jednostce obliczeniowej jest określana jako [partycja logiczna](partitioning-overview.md#logical-partitions) w Azure Cosmos DB. Jeśli znasz już program Apache Cassandra, możesz traktować partycje logiczne w taki sam sposób, jak w Cassandra. 

W przypadku systemu Apache Cassandra zaleca się limit 100 MB danych, które mogą być przechowywane w partycji. Interfejs API Cassandra dla Azure Cosmos DB umożliwia do 20 GB na partycję logiczną oraz do 30 GB danych na partycję fizyczną. W Azure Cosmos DB, w przeciwieństwie do oprogramowania Apache Cassandra, pojemność obliczeniowa dostępna w partycji fizycznej jest wyrażana przy użyciu pojedynczej metryki o nazwie [jednostki żądania](request-units.md), która pozwala na myśleć obciążenie w przypadku żądań (odczytów lub zapisów) na sekundę, a nie rdzeni, pamięci lub operacji we/wy. Pozwala to na bardziej proste planowanie pojemności, gdy zrozumiesz koszt każdego żądania. Każda partycja fizyczna może mieć do 10000 jednostek ru obliczeń. Więcej informacji o opcjach skalowalności można znaleźć w artykule dotyczącym [elastycznej skali](manage-scale-cassandra.md) w interfejs API Cassandra. 

W Azure Cosmos DB każda partycja fizyczna składa się z zestawu replik, znanych także jako zestawy replik, z co najmniej 4 replikami na partycję. Jest to w przeciwieństwie do platformy Apache Cassandra, w której możliwe jest ustawienie współczynnika replikacji 1. Jest to jednak przyczyną niskiej dostępności, jeśli jedyny węzeł z danymi ulegnie awarii. W interfejs API Cassandra jest zawsze współczynnik replikacji wynoszący 4 (kworum z 3). Azure Cosmos DB automatycznie zarządza zestawami replik, chociaż należy je utrzymywać przy użyciu różnych narzędzi w programie Apache Cassandra. 

Rozwiązanie Apache Cassandra ma koncepcję tokenów, które są skrótami kluczy partycji. Tokeny są oparte na murmur3 64 bajtów z wartościami z zakresu od-2 ^ 63 do-2 ^ 63-1. Ten zakres jest często określany jako "pierścień tokenów" w Apache Cassandra. Pierścień tokenów jest dystrybuowany do zakresów tokenów, a te zakresy są dzielone między węzłami znajdującymi się w macierzystym klastrze Apache Cassandra. Partycjonowanie dla Azure Cosmos DB jest zaimplementowane w podobny sposób, z tą różnicą, że używa innego algorytmu wyznaczania wartości skrótu i ma większy pierścień wewnętrzny token. Jednak zewnętrznie udostępnimy ten sam zakres tokenów co Apache Cassandra, tj.-2 ^ 63 do-2 ^ 63-1.


## <a name="primary-key"></a>Klucz podstawowy

Wszystkie tabele w interfejs API Cassandra muszą mieć `primary key` zdefiniowany. Poniżej przedstawiono składnię klucza podstawowego:

```shell
column_name cql_type_definition PRIMARY KEY
```

Załóżmy, że chcemy utworzyć tabelę użytkowników, która przechowuje komunikaty dla różnych użytkowników:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

W tym projekcie definiujemy `id` pole jako klucz podstawowy. Klucz podstawowy pełni rolę identyfikatora rekordu w tabeli i jest również używany jako klucz partycji w Azure Cosmos DB. Jeśli klucz podstawowy został zdefiniowany w opisany wcześniej sposób, w każdej partycji będzie dostępny tylko jeden rekord. Spowoduje to nałożenie idealnie poziomowej i skalowalnej dystrybucji podczas zapisywania danych w bazie danych i jest idealnym rozwiązaniem dla przypadków użycia wyszukiwania klucz-wartość. Aplikacja powinna udostępnić klucz podstawowy za każdym razem, gdy odczytuje dane z tabeli, aby zmaksymalizować wydajność odczytu. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="Partition" border="false":::


## <a name="compound-primary-key"></a>Złożony klucz podstawowy

Rozwiązanie Apache Cassandra ma również koncepcję programu  `compound keys` . Związek `primary key` składa się z więcej niż jednej kolumny; pierwsza kolumna to `partition key` , a wszystkie dodatkowe kolumny są `clustering keys` . Składnia dla elementu `compound primary key` jest pokazana poniżej:

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Załóżmy, że chcemy zmienić powyższy projekt i umożliwić efektywne pobieranie komunikatów dla danego użytkownika:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

W tym projekcie definiujemy teraz `user` klucz partycji oraz `id` klucz klastrowania. Można zdefiniować dowolną liczbę kluczy klastrowania, ale każda wartość (lub kombinacja wartości) klucza klastrowania musi być unikatowa, aby można było dodać wiele rekordów do tej samej partycji, na przykład:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Gdy dane są zwracane, są sortowane według klucza klastrowania zgodnie z oczekiwaniami w programie Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="Partition":::

Dzięki danemu modelowaniu wielu rekordów można przypisywać do każdej partycji, pogrupowane według użytkownika. W ten sposób można wydać zapytanie, które jest efektywnie kierowane przez `partition key` (w tym przypadku `user` ), aby uzyskać wszystkie komunikaty dla danego użytkownika. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="Partition" border="false":::


## <a name="composite-partition-key"></a>Klucz partycji złożonej

Złożone klucze partycji działają zasadniczo tak samo jak klucze złożone, z tą różnicą, że można określić wiele kolumn jako klucz partycji złożonej. Poniżej przedstawiono składnię kluczy partycji złożonej:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Można na przykład mieć następujące wartości, w których unikatowa kombinacja `firstname` i `lastname` mogłaby utworzyć klucz partycji, i `id` jest kluczem klastrowania:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Następne kroki

* Informacje o [partycjonowaniu i skalowaniu w poziomie Azure Cosmos DB](partitioning-overview.md).
* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
