---
title: Azure Cosmos DB interfejs API Cassandra dostępu do Azure Databricks
description: W tym artykule opisano sposób pracy z Azure Cosmos DB interfejs API Cassandra z Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 30bd3187973de204f27a3be3862351550d6a56f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260478"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Dostęp do Azure Cosmos DB danych interfejs API Cassandra Azure Databricks

W tym artykule szczegółowo opisano, jak workwith Azure Cosmos DB interfejs API Cassandra z platformy Spark w [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Wymagania wstępne

* [Inicjowanie obsługi administracyjnej konta interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Zapoznaj się z podstawowymi informacjami na temat łączenia się z Azure Cosmos DB interfejs API Cassandra](cassandra-spark-generic.md)

* [Inicjowanie obsługi klastra Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Zapoznaj się z przykładami kodu dotyczącymi pracy z interfejs API Cassandra](cassandra-spark-generic.md#next-steps)

* [Użyj cqlsh do walidacji, jeśli wolisz](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Interfejs API Cassandra konfiguracja wystąpienia dla łącznika Cassandra:**

  Łącznik dla interfejs API Cassandra wymaga zainicjowania szczegółów połączenia Cassandra jako części kontekstu Spark. Po uruchomieniu notesu datacegły, kontekst Spark jest już zainicjowany i nie jest zalecane jego zatrzymanie i ponowne zainicjowanie. Jednym z rozwiązań jest dodanie konfiguracji wystąpienia interfejs API Cassandra na poziomie klastra w konfiguracji klastra Spark. Jest to jednorazowe działanie na klaster. Dodaj następujący kod do konfiguracji platformy Spark jako parę wartości klucza oddzielonego spacją:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

* **Łącznik Cassandra Spark:** — aby zintegrować interfejs API Cassandra Azure Cosmos DB z platformą Spark, należy podłączyć łącznik Cassandra do klastra Azure Databricks. Aby dołączyć klaster:

  * Przejrzyj wersję środowiska uruchomieniowego datakosteks, wersję platformy Spark. Następnie Znajdź [współrzędne Maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) zgodne z łącznikiem Cassandra Spark i Dołącz je do klastra. Zapoznaj się z artykułem ["przekazywanie pakietu Maven lub pakietu platformy Spark"](https://docs.databricks.com/user-guide/libraries.html) , aby dołączyć bibliotekę łączników do klastra. Na przykład Maven koordynuje dla "Databricks Runtime wersja 4,3", "Spark 2.3.1" i "Scala 2,11" to `spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB biblioteki specyficznej dla interfejs API Cassandra:** — niestandardowa fabryka połączeń jest wymagana do skonfigurowania zasady ponawiania z łącznika Cassandra platformy Spark w celu Azure Cosmos DB interfejs API Cassandra. Dodaj `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [współrzędne Maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) , aby dołączyć bibliotekę do klastra.

## <a name="sample-notebooks"></a>Przykładowe notesy

Lista Azure Databricks [przykładowe notesy](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) są dostępne w repozytorium GitHub do pobrania. Te przykłady obejmują sposób nawiązywania połączenia z Azure Cosmos DB interfejs API Cassandra z platformy Spark i wykonywania różnych operacji CRUD na danych. Możesz również [zaimportować wszystkie notesy](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) do obszaru roboczego klastra i uruchomić go. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Uzyskiwanie dostępu do Azure Cosmos DB interfejs API Cassandra z programów Spark Scala

Programy Spark uruchamiane jako zautomatyzowane procesy na Azure Databricks są przesyłane do klastra przy użyciu usługi [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html)) i zaplanowane do uruchomienia przez zadania Azure Databricks.

Poniżej znajdują się linki ułatwiające rozpoczęcie tworzenia programów Scala platformy Spark w celu współdziałania z interfejs API Cassandra Azure Cosmos DB.
* [Jak nawiązać połączenie z Azure Cosmos DB interfejs API Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Jak uruchomić program Spark Scala jako zadanie automatyczne na Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Pełna Lista przykładów kodu do pracy z interfejs API Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do [tworzenia konta interfejs API Cassandra, bazy danych i tabeli](create-cassandra-api-account-java.md) przy użyciu aplikacji Java.
