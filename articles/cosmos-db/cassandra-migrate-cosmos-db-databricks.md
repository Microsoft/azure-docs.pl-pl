---
title: Migrowanie danych z platformy Apache Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu funkcji datakosteks (Spark)
description: Dowiedz się, jak migrować dane z bazy danych Apache Cassandra do Azure Cosmos DB interfejs API Cassandra przy użyciu Azure Databricks i platformy Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932916"
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

Można postępować zgodnie z instrukcjami, aby [zainicjować obsługę klastra Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Należy jednak pamiętać, że Apache Spark 3. x nie jest obecnie obsługiwane w przypadku łącznika Apache Cassandra. Konieczne będzie udostępnienie środowiska uruchomieniowego datakostkom z obsługiwaną wersją systemu v2. x Apache Spark. Zalecamy korzystanie z wersji 6,6 środowiska uruchomieniowego datakosteks:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Środowisko uruchomieniowe datakosteks":::


## <a name="add-dependencies"></a>Dodaj zależności

Aby można było Cosmos DB połączyć się z punktami końcowymi Cassandra, należy Apache Spark dodać do klastra bibliotekę łączników Cassandra. W klastrze wybierz biblioteki — > zainstalować nowe > Maven-> wyszukiwania:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pakiety wyszukiwania datakostek":::

Wpisz `Cassandra` w polu wyszukiwania i wybierz najnowsze `spark-cassandra-connector` dostępne repozytorium Maven, a następnie wybierz pozycję Zainstaluj:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Wybrane pakiety datakostki":::

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
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
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
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes`Konfiguracje i `connections_per_executor_max` są ważne w przypadku unikania [ograniczania szybkości](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), co się dzieje, gdy żądania Cosmos DB przekraczają zainicjowaną przepływność ([jednostki żądania](https://docs.microsoft.com/azure/cosmos-db/request-units)). Może być konieczne dostosowanie tych ustawień w zależności od liczby modułów wykonujących w klastrze Spark oraz potencjalnie rozmiaru (a tym samym kosztu RU) każdego rekordu zapisywanego w tabelach docelowych.

## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Oszacowanie ru/s przy użyciu artykułów Azure Cosmos DB planisty wydajności](estimate-ru-with-capacity-planner.md)
* [Elastyczne skalowanie w Azure Cosmos DB interfejs API Cassandra](manage-scale-cassandra.md)
