---
title: Apache Kafka — przewodnik dla centrów zdarzeń
description: Ten artykuł zawiera łącza do artykułów, które opisują sposób integracji aplikacji platformy Kafka z usługi Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: b4d9dc51451a06b2c87ddce78547655b51c6fabd
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729624"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka — przewodnik dla deweloperów usługi Azure Event Hubs
Ten artykuł zawiera łącza do artykułów, które opisują, jak zintegrować aplikacje Apache Kafka z usługi Azure Event Hubs. 

## <a name="overview"></a>Omówienie
Usługa Event Hubs udostępnia punkt końcowy platformy Kafka, który może być używany przez istniejące aplikacje oparte na platformie Kafka jako alternatywa dla uruchamiania własnego klastra platformy Kafka. Usługa Event Hubs obsługuje protokół Apache Kafka 1.0 lub nowszych i współpracuje z istniejącymi aplikacjami platformy Kafka, w tym z programem MirrorMaker. Aby uzyskać więcej informacji, zobacz [Centra zdarzeń dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Przewodniki Szybki Start
Szybkie starty można znaleźć w usłudze GitHub i w tym zestawie zawartości, który pomaga szybko rozpocząć w centrach zdarzeń dla platformy Kafka.

### <a name="quickstarts-in-github"></a>Szybki start w usłudze GitHub
Zobacz następujące przewodniki Szybki start w **repozytorium azure-event-hubs-for-kafka:** 

| Język/struktura klienta | Opis | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Ten przewodnik Szybki start pokaże, jak utworzyć punkt końcowy usługi Event Hubs Platformy Przy użyciu przykładowego producenta i konsumenta napisanego w języku C# przy użyciu programu .NET Core 2.0.</p><p>Ten przykład jest oparty na [klienta Apache Kafka .NET firmy Confluent,](https://github.com/confluentinc/confluent-kafka-dotnet)zmodyfikowany do użytku z centrami zdarzeń dla platformy Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Ten przewodnik Szybki start pokaże, jak utworzyć punkt końcowy usługi Event Hubs Platformy Kafka i połączyć się z nią przy użyciu przykładowego producenta i konsumenta napisanego w języku Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Ten przewodnik Szybki start pokaże, jak utworzyć i połączyć się z punktem końcowym usługi Event Hubs Platformy przy użyciu przykładowego producenta i konsumenta napisanego w node.</p><p>W tym przykładzie użyto biblioteki [node-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Ten przewodnik Szybki start pokaże, jak utworzyć i połączyć się z punktem końcowym platformy Kafka usług eventów przy użyciu przykładowego producenta i konsumenta napisanego w pythonie.</p><p>Ten przykład jest oparty na [kliencie Apache Kafka Języka Python firmy Confluent,](https://github.com/confluentinc/confluent-kafka-python)zmodyfikowanym do użytku z centrami zdarzeń dla platformy Kafka.</p>|
| [Przejdź](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Ten przewodnik Szybki start pokaże, jak utworzyć punkt końcowy usługi Event Hubs Platformy Kafka i połączyć się z nią przy użyciu przykładowego producenta i konsumenta napisanego w programie Go.</p><p>Ten przykład jest oparty na [kliencie Apache Kafka Golang firmy Confluent,](https://github.com/confluentinc/confluent-kafka-go)zmodyfikowanym do użytku z centrami zdarzeń dla platformy Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Ten przewodnik Szybki start pokaże, jak utworzyć punkt końcowy usługi Event Hubs Platformy przy użyciu przykładowego producenta i konsumenta napisanego w programie Go przy użyciu biblioteki [klienta Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Ten przewodnik Szybki start pokaże, jak utworzyć punkt końcowy usługi Platformy Kafka i połączyć się z nią przy użyciu interfejsu wiersza polecenia dołączonego do dystrybucji platformy Apache Kafka.| 
| [Kafkacat ( Kafkacat )](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat jest konsumentem i producentem linii poleceń innych niż JVM w oparciu o librdkafka, popularny ze względu na swoją szybkość i niewielkie rozmiary. Ten przewodnik Szybki start zawiera przykładową konfigurację i kilka prostych przykładowych poleceń kafkacat. | 
 
### <a name="quickstarts-in-docs"></a>Szybki start w docs
Zobacz przewodnik Szybki start: [przesyłanie strumieniowe danych z centrami zdarzeń przy użyciu protokołu Platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) w tym zestawie zawartości, który zawiera instrukcje krok po kroku dotyczące przesyłania strumieniowego do centrów zdarzeń. Dowiesz się, jak używać producentów i konsumentów do rozmów z centrum zdarzeń tylko ze zmianą konfiguracji w aplikacjach. 


## <a name="tutorials"></a>Samouczki 

### <a name="tutorials-in-github"></a>Samouczki w usłudze GitHub
Zobacz następujące samouczki na GitHub:

| Samouczek | Opis | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | W tym samouczku pokazano, jak połączyć strumienie Akka z centrami zdarzeń z obsługą platformy Kafka bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Istnieją dwa oddzielne samouczki przy użyciu języków programowania **Java** i **Scala.** | 
| [Połącz](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | W tym dokumencie zostanie wyświetlona integracja platformy Kafka Connect z usługą Azure Event Hubs i wdrożenie podstawowych łączników FileStreamSource i FileStreamSink. Chociaż te łączniki nie są przeznaczone do użytku w środowisku produkcyjnym, pokazują one end-to-end Kafka Connect Scenario, w którym usługa Azure Event Hubs podszywa się pod brokera platformy Kafka.| 
| [Filebeat (Filebeat)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Ten dokument poprowadzi Cię przez integrację Filebeat i Event Hubs za pośrednictwem wyjścia Kafka Filebeat.This document will walk you through integrating Filebeat and Event Hubs via Filebeat's Kafka output. | 
| [Flink (flink)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | W tym samouczku pokazano, jak połączyć apache Flink z centrami zdarzeń z obsługą platformy Kafka bez zmiany klientów protokołu lub uruchamiania własnych klastrów. | 
| [Biegle](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Ten dokument poprowadzi Cię przez integrację Fluentd `out_kafka` i Event Hubs za pomocą wtyczki wyjściowej dla Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | W tym samouczku pokazano, jak wymieniać zdarzenia między konsumentami i producentami przy użyciu różnych protokołów. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | W tym samouczku przejdziesz przez integrację programu Logstash z centrami zdarzeń obsługującymi technologię Kafka przy użyciu wtyczek wejścia/wyjścia logstash Kafka. | 
| [MirrorMaker (Producent lustrzanych)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | W tym samouczku pokazano, jak centrum zdarzeń i program Kafka MirrorMaker można zintegrować istniejący potok platformy Kafka do platformy Azure przez dublowanie strumienia wejściowego platformy Kafka w usłudze Usługi Centrum zdarzeń. |
| [NiFi (NiFi)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | W tym samouczku pokazano, jak połączyć Apache NiFi z obszarem nazw centrum zdarzeń. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Przewodnika Szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym platformy Kafka centrum zdarzeń przy użyciu przykładowego producenta i konsumenta napisanego w językach programowania Go i Java. |
| [Rejestr schematów Confluenta](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | W tym samouczku przejdziesz przez integrowanie rejestru schematów i centrów zdarzeń dla platformy Kafka. | 
| [platforma Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | W tym samouczku pokazano, jak połączyć aplikację Platformy Spark z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów platformy Kafka. | 

### <a name="tutorials-in-docs"></a>Samouczki w docs
Zobacz też samouczek: [Proces Apache Kafka dla zdarzeń usługi Event Hubs przy użyciu analizy strumienia](event-hubs-kafka-stream-analytics.md) w tym zestawie zawartości, który pokazuje, jak przesyłać strumieniowo dane do centrów zdarzeń i przetwarzać go za pomocą usługi Azure Stream Analytics.

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
Zapoznaj się z poniższymi poradnikami w naszej dokumentacji:

| Artykuł | Opis | 
| ------- | ----------- | 
| [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md) | Pokazuje, jak dublować brokera platformy Kafka w centrum zdarzeń przy użyciu programu Kafka MirrorMaker. |
| [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md) | Przeprowadzi Cię przez połączenie aplikacji Spark z centrum zdarzeń w celu przesyłania strumieniowego w czasie rzeczywistym. |
| [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md) | Pokazuje, jak połączyć apache Flink z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. |
| [Integracja aplikacji Apache Kafka Connect z centrum zdarzeń (wersja zapoznawcza)](event-hubs-kafka-connect-tutorial.md) | Przeprowadzi Cię przez integrację platformy Kafka Connect z centrum zdarzeń i wdrażanie podstawowych łączników FileStreamSource i FileStreamSink. |
| [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md) | Pokazuje, jak połączyć strumienie Akka z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. |
| [Korzystanie z spring boot starter dla platformy Apache Kafka z centrum zdarzeń platformy Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Pokazuje, jak skonfigurować oparty na języku Java spinacz Spring Cloud Stream utworzony za pomocą spring boot initializer do używania apache kafka z usługi Azure Event Hubs. |

## <a name="next-steps"></a>Następne kroki
Przejrzyj przykłady w folderach usługi [Azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) usługi Repozytorium usługi GitHub w obszarze Szybki start i samouczki.

Zobacz także następujące artykuły:

- [Apache Kafka przewodnik rozwiązywania problemów dla Centrum zdarzeń](apache-kafka-troubleshooting-guide.md)
- [Często zadawane pytania - Event Hubs for Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka przewodnik migracji dla Centrów zdarzeń](apache-kafka-migration-guide.md)



