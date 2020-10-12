---
title: Apache Kafka Przewodnik dla deweloperów Event Hubs
description: Ten artykuł zawiera linki do artykułów opisujących sposób integrowania aplikacji Kafka z usługą Azure Event Hubs.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061737"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs
Ten artykuł zawiera linki do artykułów opisujących sposób integrowania aplikacji Apache Kafka z usługą Azure Event Hubs. 

## <a name="overview"></a>Omówienie
Event Hubs udostępnia punkt końcowy Kafka, który może być używany przez istniejące aplikacje oparte na Kafka jako alternatywę dla uruchamiania własnego klastra Kafka. Event Hubs współpracuje z wieloma istniejącymi aplikacjami Kafka. Aby uzyskać więcej informacji, zobacz [Event Hubs dla Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Przewodniki Szybki start
Przewodniki Szybki Start można znaleźć w usłudze GitHub i w tym zestawie zawartości, co pozwala szybko uzyskać dostęp do Event Hubs Kafka.

### <a name="quickstarts-in-github"></a>Przewodniki Szybki Start w usłudze GitHub
Zobacz następujące przewodniki szybki start w repozytorium **Azure-Event-Hubs-for-Kafka** : 

| Język/struktura klienta | Opis | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym Event Hubs Kafka za pomocą przykładowego producenta i konsumenta pisanego w języku C# przy użyciu programu .NET Core 2,0.</p><p>Ten przykład jest oparty na [Apache Kafka klienta .NET](https://github.com/confluentinc/confluent-kafka-dotnet), zmodyfikowanym do użycia z Event Hubs for Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym Event Hubs Kafka za pomocą przykładowego producenta i konsumenta pisanego w języku Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym Event Hubs Kafka przy użyciu przykładowego producenta i konsumenta zapisaną w węźle.</p><p>Ten przykład używa biblioteki [Node-rdkafka](https://github.com/Blizzard/node-rdkafka) . </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym Event Hubs Kafka za pomocą przykładowego producenta i konsumenta pisanego w języku Python.</p><p>Ten przykład jest oparty na [Apache Kafka klienta języka Python](https://github.com/confluentinc/confluent-kafka-python), zmodyfikowanym do użycia z Event Hubs dla Kafka.</p>|
| [Przejdź](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z punktem końcowym Event Hubs Kafka za pomocą przykładowego producenta i konsumenta pisanego w podróży.</p><p>Ten przykład jest oparty na [Apache Kafka klienta golang](https://github.com/confluentinc/confluent-kafka-go), zmodyfikowanym do użycia z Event Hubs dla Kafka.</p>| 
| [Sarama Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | W tym przewodniku szybki start pokazano, jak utworzyć i nawiązać połączenie z punktem końcowym Event Hubs Kafka przy użyciu przykładowego producenta i konsumenta pisanego przy użyciu biblioteki [klienta Sarama Kafka](https://github.com/Shopify/sarama) . |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | W tym przewodniku szybki start pokazano, jak utworzyć i połączyć się z Event Hubs punktem końcowym Kafka przy użyciu interfejsu wiersza polecenia, który jest powiązany z dystrybucją Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat to odbiorca wiersza polecenia niebędący JVM i producent oparty na librdkafka, popularnym ze względu na jego szybkość i niewielki wpływ. Ten przewodnik Szybki Start zawiera przykładową konfigurację i kilka prostych przykładowych poleceń kafkacat. | 
 
### <a name="quickstarts-in-docs"></a>Przewodniki Szybki Start w witrynie DOCS
Zobacz Przewodnik Szybki Start: [przesyłanie strumieniowe danych za pomocą Event Hubs przy użyciu protokołu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) w tym zestawie zawartości, który zawiera instrukcje krok po kroku dotyczące przesyłania strumieniowego do Event Hubs. Dowiesz się, jak używać swoich producentów i konsumentów, aby komunikować się z Event Hubs tylko zmianą konfiguracji w aplikacjach. 


## <a name="tutorials"></a>Samouczki 

### <a name="tutorials-in-github"></a>Samouczki w usłudze GitHub
Zobacz następujące samouczki w witrynie GitHub:

| Samouczek | Opis | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | W tym samouczku pokazano, jak połączyć strumienie Akka z Event Hubsami z obsługą Kafka bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. Istnieją dwa osobne samouczki korzystające z języków programowania **Java** i **Scala** . | 
| [Połącz](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Ten dokument przeprowadzi Cię przez integrację Kafka z usługą Azure Event Hubs i wdrożeniem podstawowych łączników FileStreamSource i FileStreamSink. Chociaż te łączniki nie są przeznaczone do użytku w środowisku produkcyjnym, przedstawiają kompleksowy scenariusz Kafkaego połączenia, w którym platforma Azure Event Hubs jest zamaskowane jako Broker Kafka.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Ten dokument przeprowadzi Cię przez integrację Filebeat i Event Hubs za pośrednictwem danych wyjściowych Kafka Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | W tym samouczku pokazano, jak połączyć Apache Flink z Event Hubs z włączoną funkcją Kafka, bez konieczności zmiany klientów protokołu lub uruchamiania własnych klastrów. | 
| [Pozostało](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Ten dokument przeprowadzi Cię przez integrację pochodzącą i Event Hubs przy użyciu `out_kafka` wtyczki danych wyjściowych dla produktu Fluent. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | W tym samouczku przedstawiono sposób wymiany zdarzeń między konsumentami i producentami przy użyciu różnych protokołów. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Ten samouczek przeprowadzi Cię przez integrację logstash z Event Hubs z włączoną obsługą Kafka przy użyciu wtyczek logstash Kafka Input/Output. | 
| [Narzędzia MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | W tym samouczku pokazano, jak centrum zdarzeń i Kafka narzędzia MirrorMaker mogą zintegrować istniejący potok Kafka z platformą Azure przez dublowanie strumienia danych wejściowych Kafka w usłudze Event Hubs. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Ten samouczek pokazuje, jak połączyć Apache NiFi z przestrzenią nazw Event Hubs. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Przewodniki Szybki Start pokazują, jak utworzyć i nawiązać połączenie z punktem końcowym Event Hubs Kafka za pomocą przykładowego producenta i konsumenta pisanego w językach programowania języka Java. |
| [Rejestr schematu z niefluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Ten samouczek przeprowadzi Cię przez integrację rejestru schematu i Event Hubs dla Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | W tym samouczku przedstawiono sposób łączenia aplikacji platformy Spark z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów Kafka. | 

### <a name="tutorials-in-docs"></a>Samouczki w witrynie DOCS
Zobacz również samouczek: [proces Apache Kafka dla zdarzeń Event Hubs za pomocą usługi Stream Analytics](event-hubs-kafka-stream-analytics.md) w tym zestawie zawartości, który pokazuje, jak przesyłać strumieniowo dane do Event Hubs i przetwarzać je z Azure Stream Analytics.

## <a name="how-to-guides"></a>Przewodniki z instrukcjami
Zapoznaj się z następującymi przewodnikami:

| Artykuł | Opis | 
| ------- | ----------- | 
| [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md) | Pokazuje, w jaki sposób dublować brokera Kafka w centrum zdarzeń przy użyciu Kafka narzędzia MirrorMaker. |
| [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md) | Przeprowadzi Cię przez połączenie aplikacji Spark w celu Event Hubs na potrzeby przesyłania strumieniowego w czasie rzeczywistym. |
| [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md) | Pokazuje, jak połączyć Apache Flink z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. |
| [Integrowanie Apache Kafka nawiązywanie połączenia z centrum zdarzeń (wersja zapoznawcza)](event-hubs-kafka-connect-tutorial.md) | Przeprowadzi Cię przez integrację Kafka z centrum zdarzeń i wdrażanie podstawowych łączników FileStreamSource i FileStreamSink. |
| [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md) | Pokazuje, jak połączyć strumienie Akka z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. |
| [Korzystanie z programu sprężynowego rozruchu Starter dla Apache Kafka z platformą Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Pokazuje, w jaki sposób skonfigurować spinacz oparty na języku Java, który został utworzony za pomocą inicjatora rozruchu sprężynowego, aby użyć Apache Kafka z usługą Azure Event Hubs. |

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami w folderach repozytorium GitHub [Azure-Event-Hubs-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) w obszarze foldery szybkiego startu i samouczków.

Zobacz również następujące artykuły:

- [Apache Kafka Przewodnik rozwiązywania problemów dla Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Często zadawane pytania — Event Hubs dla Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka Przewodnik migracji Event Hubs](apache-kafka-migration-guide.md)



