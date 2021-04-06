---
title: Migrowanie danych z platformy Apache Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu funkcji datakosteks (Spark)
description: Dowiedz się, jak migrować dane z bazy danych Apache Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu Azure Databricks i platformy Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801013"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrowanie danych z usługi Cassandra do konta interfejs API Cassandra Azure Cosmos DB przy użyciu Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w Azure Cosmos DB został doskonały wybór dla obciążeń przedsiębiorstwa działających na platformie Apache Cassandra z kilku powodów:

* **Brak nakładów związanych z zarządzaniem i monitorowaniem:** Eliminuje to obciążenie związane z zarządzaniem i monitorowaniem ustawień w systemach OS, JVM i YAML oraz ich interakcje.

* **Znaczne oszczędności kosztów:** Możesz zaoszczędzić koszty w Azure Cosmos DB, które obejmują koszt maszyn wirtualnych, przepustowości i wszelkich odpowiednich licencji. Nie trzeba zarządzać centrami danych, serwerami, magazynem SSD, siecią i kosztami energii elektrycznej.

* **Możliwość korzystania z istniejącego kodu i narzędzi:** Azure Cosmos DB zapewnia bezobsługową zgodność na poziomie protokołu z istniejącymi zestawami SDK i narzędziami Cassandra. Ta zgodność gwarantuje, że można użyć istniejącej bazy kodu z Azure Cosmos DB interfejs API Cassandra z prostymi zmianami.

Istnieje wiele sposobów migrowania obciążeń bazy danych z jednej platformy do innej. [Azure Databricks](https://azure.microsoft.com/services/databricks/) to oferta platformy jako usługi (PaaS) dla [Apache Spark](https://spark.apache.org/) , która oferuje sposób przeprowadzania migracji w trybie offline na dużą skalę. W tym artykule opisano kroki wymagane do migracji danych z natywnych obszarów kluczy i tabel Apache Cassandra do interfejs API Cassandra Azure Cosmos DB przy użyciu Azure Databricks.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainicjuj obsługę konta interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account).

* Zapoznaj się z [podstawowymi informacjami na temat nawiązywania połączenia z interfejs API Cassandra Azure Cosmos DB](cassandra-spark-generic.md).

* Przejrzyj [obsługiwane funkcje interfejs API Cassandra Azure Cosmos DB,](cassandra-support.md) aby zapewnić zgodność.

* Upewnij się, że masz już utworzone puste przestrzenie kluczy i tabele w docelowym koncie Azure Cosmos DB interfejs API Cassandra.

* [Aby sprawdzić poprawność, użyj cqlsh lub powłoki hostowanej](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Inicjowanie obsługi klastra Azure Databricks

Można postępować zgodnie z instrukcjami, aby [zainicjować obsługę klastra Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Zalecamy wybranie środowiska uruchomieniowego datacegłs w wersji 7,5, które obsługuje platformę Spark 3,0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Zrzut ekranu przedstawiający Znajdowanie wersji środowiska uruchomieniowego datakosteks.":::

## <a name="add-dependencies"></a>Dodaj zależności

Aby nawiązać połączenie z punktami końcowymi Cassandra i Azure Cosmos DB, należy Apache Spark dodać do klastra bibliotekę łączników Cassandra. W klastrze wybierz kolejno pozycje **biblioteki**  >  **Zainstaluj nowy**  >  **Maven**, a następnie Dodaj `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` we współrzędnych Maven.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Zrzut ekranu pokazujący wyszukiwanie pakietów Maven w kostkach.":::

Wybierz pozycję **Zainstaluj**, a następnie uruchom ponownie klaster po zakończeniu instalacji.

> [!NOTE]
> Przed zainstalowaniem biblioteki łącznika Cassandra upewnij się, że klaster datakostki zostanie uruchomiony ponownie.

## <a name="create-scala-notebook-for-migration"></a>Tworzenie notesu Scala na potrzeby migracji

Tworzenie notesu Scala w kostkach. Zastąp źródłową i docelową konfigurację Cassandra z odpowiednimi poświadczeniami oraz źródłowymi i docelowymi miejscami i tabelami. Następnie uruchom następujący kod:

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
> `spark.cassandra.output.batch.size.rows`Wartości i `spark.cassandra.output.concurrent.writes` liczba procesów roboczych w klastrze Spark są istotnymi konfiguracjami, które można dostosować w celu uniknięcia [ograniczenia szybkości](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). Ograniczanie szybkości ma miejsce, gdy żądania Azure Cosmos DB przekraczają zainicjowaną przepływność lub [jednostki żądań](./request-units.md) (jednostek ru). Może być konieczne dostosowanie tych ustawień, w zależności od liczby modułów wykonujących w klastrze Spark i potencjalnie rozmiaru (a tym samym kosztu RU) każdego rekordu zapisywanego w tabelach docelowych.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="rate-limiting-429-error"></a>Ograniczanie szybkości (błąd 429)

Może zostać wyświetlony tekst błędu 429 lub "szybkość żądania jest duża", nawet jeśli obniżono ustawienia wartości minimalnych. Następujące scenariusze mogą spowodować ograniczenie szybkości:

* **Przepływność przypisana do tabeli jest mniejsza niż 6 000 [jednostek żądań](./request-units.md)**. Nawet przy minimalnych ustawieniach platforma Spark może pisać z częstotliwością około 6 000 jednostek żądań lub dłużej. Jeśli zainicjowano udostępnianie tabeli w przestrzeni kluczy z udostępnioną przepływem pracy, istnieje możliwość, że ta tabela ma mniej niż 6 000 jednostek ru dostępne w czasie wykonywania.

    Upewnij się, że w tabeli, do której przeprowadzasz migrację, znajduje się co najmniej 6 000 jednostek ru dostępne po uruchomieniu migracji. W razie potrzeby Przydziel dedykowane jednostki żądań do tej tabeli.

* **Nadmierne pochylenie danych z dużą ilością danych**. Jeśli masz dużą ilość danych do migrowania do danej tabeli, ale mają znaczące pochylenie w danych (czyli wiele rekordów jest zapisywanych dla tej samej wartości klucza partycji), można nadal korzystać z ograniczenia szybkości nawet wtedy, gdy w tabeli Zainicjowano obsługę kilku [jednostek żądania](./request-units.md) . Jednostki żądań są podzielone równomiernie między partycjami fizycznymi, a duże pochylenie danych może spowodować wąskie gardła żądań do pojedynczej partycji.

    W tym scenariuszu Zmniejsz do minimalnych ustawień przepływności w usłudze Spark i wymuś spowolnienie migracji. Ten scenariusz może być bardziej typowy w przypadku migrowania tabel referencyjnych lub formantów, gdzie dostęp jest krótszy i zmniejszanie może być wysokie. Jednak jeśli w dowolnym innym typie tabeli występuje znaczne pochylenie, warto przejrzeć model danych, aby uniknąć problemów z partycją gorącą dla obciążenia podczas operacji w stanie stabilnym.

## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Szacowanie RU/s przy użyciu Azure Cosmos DB planista pojemności](estimate-ru-with-capacity-planner.md)
* [Elastyczne skalowanie w Azure Cosmos DB interfejs API Cassandra](manage-scale-cassandra.md)
