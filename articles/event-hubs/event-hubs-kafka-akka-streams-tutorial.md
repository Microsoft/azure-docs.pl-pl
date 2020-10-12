---
title: Korzystanie z Akka strumieni dla Apache Kafka platformy Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia strumieni Akka z centrum zdarzeń platformy Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1fbbeef37c4cbdd52d2127c5242474ac46e42d25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061703"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Używanie programu Akka Streams z usługą Event Hubs dla platformy Apache Kafka

W tym samouczku pokazano, jak połączyć strumienie Akka za pomocą obsługi Event Hubs Apache Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta strumieni Akka 
> * Uruchom odbiorcę strumieni Akka

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobieranie](https://maven.apache.org/download.cgi) i [Instalowanie](https://maven.apache.org/install.html) archiwum binarnego Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw Event Hubs jest wymagana do wysyłania lub odbierania z dowolnej usługi Event Hubs. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Skopiuj Event Hubs parametry połączenia do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz Event Hubs parametry połączenia, Sklonuj Event Hubs platformy Azure dla repozytorium Kafka i przejdź do `akka` podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Uruchom producenta strumieni Akka

Korzystając z podanego przykładowego strumienia Akka, Wysyłaj komunikaty do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="producer-applicationconf"></a>Aplikacja producenta. conf

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` w programie, `producer/src/main/resources/application.conf` Aby skierować producenta do punktu końcowego Event Hubs Kafka z prawidłowym uwierzytelnianiem.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producenta z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent zaczyna wysyłać zdarzenia do centrum zdarzeń w temacie `test` i drukuje zdarzenia do stdout.

## <a name="run-akka-streams-consumer"></a>Uruchom odbiorcę strumieni Akka

Korzystając z podanego przykładu konsumenta, odbieraj komunikaty z centrum zdarzeń.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj Event Hubs punkt końcowy Kafka

#### <a name="consumer-applicationconf"></a>Aplikacja konsumencka. conf

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` w programie, `consumer/src/main/resources/application.conf` Aby skierować odbiorcę do punktu końcowego Event Hubs Kafka z prawidłowym uwierzytelnianiem.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Uruchom klienta z wiersza polecenia

Aby uruchomić konsumenta z wiersza polecenia, wygeneruj plik JAR, a następnie uruchom go z poziomu Maven (lub wygeneruj plik JAR przy użyciu Maven, a następnie uruchom polecenie w języku Java, dodając wymagane Kafka JAR (y) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Jeśli centrum zdarzeń ma zdarzenia (na przykład jeśli producent jest również uruchomiony), konsument rozpocznie pobieranie zdarzeń z tematu `test` . 

Aby uzyskać bardziej szczegółowe informacje na temat strumieni Akka, zapoznaj się z [instrukcją Akka strumienie Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) .

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie Kafka z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)
