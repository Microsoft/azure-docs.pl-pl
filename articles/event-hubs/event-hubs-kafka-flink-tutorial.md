---
title: Korzystanie z platformy Apache Flink dla Apache Kafka — Event Hubs Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia oprogramowania Apache Flink z centrum zdarzeń platformy Azure
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f2e6eeb74c5a334d1692357edec0fd363349c7c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061652"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Korzystanie z platformy Apache Flink z usługą Azure Event Hubs dla platformy Apache Kafka
W tym samouczku pokazano, jak połączyć Apache Flink z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Aby uzyskać więcej informacji o obsłudze Event Hubs "dla protokołu klienta Apache Kafka, zobacz [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta Flink 
> * Uruchom klienta Flink

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobieranie](https://maven.apache.org/download.cgi) i [Instalowanie](https://maven.apache.org/install.html) archiwum binarnego Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw Event Hubs jest wymagana do wysyłania lub odbierania z dowolnej usługi Event Hubs. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Skopiuj Event Hubs parametry połączenia do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz Event Hubs parametry połączenia, Sklonuj Event Hubs platformy Azure dla repozytorium Kafka i przejdź do `flink` podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Uruchom producenta Flink

Korzystając z podanego przykładu producenta Flink, Wysyłaj komunikaty do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="producerconfig"></a>producer.config

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` w programie, `producer/src/main/resources/producer.config` Aby skierować producenta do punktu końcowego Event Hubs Kafka z prawidłowym uwierzytelnianiem.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producenta z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent zacznie teraz wysyłać zdarzenia do centrum zdarzeń w temacie `test` i drukować zdarzenia do stdout.

## <a name="run-flink-consumer"></a>Uruchom klienta Flink

Korzystając z podanego przykładu konsumenta, odbieraj komunikaty z centrum zdarzeń. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="consumerconfig"></a>consumer.config

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` w programie, `consumer/src/main/resources/consumer.config` Aby skierować odbiorcę do punktu końcowego Event Hubs Kafka z prawidłowym uwierzytelnianiem.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z wiersza polecenia

Aby uruchomić konsumenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Jeśli centrum zdarzeń ma zdarzenia (na przykład jeśli producent jest również uruchomiony), konsument zacznie teraz odbierać zdarzenia z tematu `test` .

Zapoznaj się z [przewodnikiem po łączniku Kafka](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) w programie Flink, aby uzyskać bardziej szczegółowe informacje na temat łączenia Flink z Kafka.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Integrowanie Kafka z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)