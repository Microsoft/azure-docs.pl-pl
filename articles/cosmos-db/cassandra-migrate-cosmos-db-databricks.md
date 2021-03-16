---
title: Migrowanie danych z platformy Apache Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu funkcji datakosteks (Spark)
description: Dowiedz się, jak migrować dane z bazy danych Apache Cassandra do Azure Cosmos DB interfejs API Cassandra przy użyciu Azure Databricks i platformy Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caf9cbb0ca017ee00c5061d94e0d37703194943d
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573380"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrowanie danych z Cassandra do konta interfejs API Cassandra Azure Cosmos DB przy użyciu Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w Azure Cosmos DB został doskonały wybór dla obciążeń przedsiębiorstwa działających na platformie Apache Cassandra z różnych powodów, takich jak: 

* **Brak nakładów związanych z zarządzaniem i monitorowaniem:** Eliminuje to obciążenie związane z zarządzaniem i monitorowaniem wyposażono ustawień w plikach OS, JVM i YAML oraz ich interakcje.

* **Znaczne oszczędności kosztów:** Koszt można zaoszczędzić za pomocą Azure Cosmos DB, który obejmuje koszt maszyn wirtualnych, przepustowości i wszelkich odpowiednich licencji. Ponadto nie trzeba zarządzać centrami danych, serwerami, magazynem SSD, siecią i kosztami energii elektrycznej. 

* **Możliwość korzystania z istniejącego kodu i narzędzi:** Azure Cosmos DB zapewnia zgodność poziomu protokołu z istniejącymi zestawami SDK i narzędziami Cassandra. Dzięki tej zgodności można używać istniejącej bazy kodu z interfejsem API Cassandra usługi Azure Cosmos DB z prostymi zmianami.

Istnieją różne sposoby migrowania obciążeń bazy danych z jednej platformy do innej. [Azure Databricks](https://azure.microsoft.com/services/databricks/) to platforma jako oferta usługi dla [Apache Spark](https://spark.apache.org/) , która oferuje sposób przeprowadzania migracji w trybie offline w dużej skali. W tym artykule opisano kroki wymagane do migracji danych z natywnych obszarów/tabel programu Apache Cassandra do Azure Cosmos DB interfejs API Cassandra przy użyciu Azure Databricks.

## <a name="prerequisites"></a>Wymagania wstępne

* [Inicjowanie obsługi administracyjnej konta interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Zapoznaj się z podstawowymi informacjami na temat łączenia się z Azure Cosmos DB interfejs API Cassandra](cassandra-spark-generic.md)

* Zapoznaj się z [obsługiwanymi funkcjami w Azure Cosmos DB interfejs API Cassandra](cassandra-support.md) , aby zapewnić zgodność.

* Upewnij się, że już utworzono puste miejsce i tabele w docelowym Azure Cosmos DB konto interfejs API Cassandra

* [Aby sprawdzić poprawność, użyj cqlsh lub powłoki hostowanej](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Inicjowanie obsługi klastra Azure Databricks

Można postępować zgodnie z instrukcjami, aby [zainicjować obsługę klastra Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Zalecamy wybranie środowiska uruchomieniowego datacegłs w wersji 7,5, które obsługuje platformę Spark 3,0:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Środowisko uruchomieniowe datakosteks":::


## <a name="add-dependencies"></a>Dodaj zależności

Aby można było Cosmos DB połączyć się z punktami końcowymi Cassandra, należy Apache Spark dodać do klastra bibliotekę łączników Cassandra. W klastrze wybierz pozycję biblioteki — > Zainstaluj polecenie New-> Maven. Dodaj `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` we współrzędnych Maven:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pakiety wyszukiwania datakostek":::

Wybierz pozycję Zainstaluj, a następnie uruchom ponownie klaster po zakończeniu instalacji. 

> [!NOTE]
> Przed zainstalowaniem biblioteki łącznika Cassandra upewnij się, że klaster datakostki zostanie uruchomiony ponownie.

## <a name="create-scala-notebook-for-migration"></a>Tworzenie notesu Scala na potrzeby migracji

Utwórz Notes Scala w kostkach danych przy użyciu następującego kodu. Zastąp źródłową i docelową konfigurację Cassandra z odpowiednimi poświadczeniami i źródłowymi i docelowymi miejscami i tabelami, a następnie uruchom polecenie:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> Wartości dla `spark.cassandra.output.batch.size.rows` i `spark.cassandra.output.concurrent.writes` , jak również liczba procesów roboczych w klastrze Spark, są ważnymi konfiguracjami do dopasowania, aby uniknąć [ograniczania szybkości](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), co się dzieje, gdy żądania Azure Cosmos DB przekraczają zainicjowaną przepływność/([jednostki żądania](./request-units.md)). Może być konieczne dostosowanie tych ustawień w zależności od liczby modułów wykonujących w klastrze Spark oraz potencjalnie rozmiaru (a tym samym kosztu RU) każdego rekordu zapisywanego w tabelach docelowych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="rate-limiting-429-error"></a>Ograniczanie szybkości (błąd 429)
Może zostać wyświetlony kod błędu 429 lub `request rate is large` tekst błędu, pomimo zmniejszenia powyższych ustawień do ich minimalnych wartości. Poniżej przedstawiono niektóre takie scenariusze:

- **Przepływność przypisana do tabeli jest mniejsza niż 6000 [jednostek żądań](./request-units.md)**. Nawet przy minimalnych ustawieniach platforma Spark będzie mogła wykonywać zapisy z częstotliwością około 6000 jednostek żądań lub dłużej. Jeśli zainicjowano udostępnianie tabeli w przestrzeni kluczy z zainicjowaną funkcją udostępnionej przepustowości, istnieje możliwość, że ta tabela ma mniej niż 6000 jednostek ru dostępne w czasie wykonywania. Upewnij się, że w tabeli, do której przeprowadzasz migrację, znajduje się co najmniej 6000 jednostek ru, podczas uruchamiania migracji, i w razie potrzeby Przydziel dedykowane jednostki żądań do tej tabeli. 
- **Nadmierne pochylenie danych z dużą ilością danych**. Jeśli masz dużą ilość danych (czyli wierszy tabeli) do migracji do danej tabeli, ale mają znaczące pochylenie w danych (tj. duża liczba rekordów dla tej samej wartości klucza partycji), możesz nadal korzystać z ograniczenia szybkości nawet wtedy, gdy w tabeli została zainicjowana duża ilość [jednostek żądań](./request-units.md) . Wynika to z faktu, że jednostki żądań są podzielone równomiernie między partycjami fizycznymi, a duże pochylenie danych może spowodować wąskie gardło żądań do pojedynczej partycji, co powoduje ograniczenie szybkości. W tym scenariuszu zaleca się ograniczenie do minimalnych ustawień przepływności w platformie Spark, aby uniknąć ograniczenia szybkości i wymusić spowolnienie działania migracji. Ten scenariusz może być bardziej typowy podczas migrowania tabel referencyjnych lub formantów, gdzie dostęp jest krótszy, ale skośność może być wysoka. Jednak jeśli w dowolnym innym typie tabeli występuje istotna pochylenie, warto również sprawdzić model danych, aby uniknąć problemów z partycją gorącą dla obciążenia podczas operacji w stanie stabilnym. 



## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Oszacowanie ru/s przy użyciu artykułów Azure Cosmos DB planisty wydajności](estimate-ru-with-capacity-planner.md)
* [Elastyczne skalowanie w Azure Cosmos DB interfejs API Cassandra](manage-scale-cassandra.md)
