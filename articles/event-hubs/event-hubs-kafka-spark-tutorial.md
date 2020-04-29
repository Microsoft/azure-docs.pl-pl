---
title: Łączenie z aplikacją platformy Apache Spark w usłudze Azure Event Hubs | Microsoft Docs
description: W tym artykule opisano, jak korzystać z platformy Apache Spark z usługą Azure Event Hubs dla platformy Kafka.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 1b292f9b894dac7df6b9634ce1bad627c8d24fe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632759"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Łączenie aplikacji Apache Spark z platformą Azure Event Hubs
Ten samouczek przeprowadzi Cię przez połączenie aplikacji Spark w celu Event Hubs na potrzeby przesyłania strumieniowego w czasie rzeczywistym. Ta integracja umożliwia przesyłanie strumieniowe bez konieczności zmiany klientów protokołu czy uruchamiania własnych klastrów platformy Kafka lub Zookeeper. Ten samouczek wymaga oprogramowania Apache Spark w wersji 2.4+ i Apache Kafka w wersji 2.0+.

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchamianie platformy Spark
> * Odczyt z usługi Event Hubs dla platformy Kafka
> * Zapis do usługi Event Hubs dla platformy Kafka

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka upewnij się, że masz następujące elementy:
-   Subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
-   [Apache Spark w wersji 2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka w wersji 2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Adapter Spark-Kafka został zaktualizowany do obsługi platformy Kafka w wersji 2.0 i platformy Spark od wersji 2.4. W poprzednich wersjach platformy Spark adapter obsługiwał platformę Kafka w wersji 0.10 i nowszych, ale bazował na interfejsach API platformy Kafka w wersji 0.10. Ponieważ usługa Event Hubs dla platformy Kafka nie obsługuje platformy Kafka w wersji 0.10, adaptery Spark-Kafka w wersjach platformy Spark wcześniejszych niż 2.4 nie są obsługiwane przez usługę Event Hubs dla ekosystemów platformy Kafka.


## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Pobierz parametry połączenia usługi Event Hubs i w pełni kwalifikowaną nazwę domeny (FQDN) w celu późniejszego użycia. Aby uzyskać instrukcje, zobacz [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Pobieranie parametrów połączenia usługi Event Hubs). 

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego
Sklonuj repozytorium usługi Azure Event Hubs i przejdź do podfolderu `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Odczyt z usługi Event Hubs dla platformy Kafka
Po dokonaniu kilku zmian w konfiguracji możesz rozpocząć odczyt z usługi Event Hubs dla platformy Kafka. Po zaktualizowaniu zmiennych **BOOTSTRAP_SERVERS** i **EH_SASL** za pomocą szczegółowych informacji dotyczących przestrzeni nazw możesz rozpocząć przesyłanie strumieniowe z usługi Event Hubs tak samo jak z platformy Kafka. Kompletny kod przykładowy znajduje się w pliku sparkConsumer.scala w witrynie GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "false")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Zapis do usługi Event Hubs dla platformy Kafka
Zapis do usługi Event Hubs może również wyglądać tak samo jak zapis do platformy Kafka. Nie zapomnij zaktualizować konfiguracji, wstawiając dla zmiennych **BOOTSTRAP_SERVERS** i **EH_SASL** informacje z przestrzeni nazw usługi Event Hubs.  Kompletny kod przykładowy znajduje się w pliku sparkProducer.scala w witrynie GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs i Event Hubs dla Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie Kafka z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)

