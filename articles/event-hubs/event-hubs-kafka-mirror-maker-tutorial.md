---
title: Użyj Apache Kafka narzędzia MirrorMaker — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące używania Kafka narzędzia MirrorMaker do dublowania klastra Kafka w centrach AzureEvent.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861002"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Użyj Apache Kafka narzędzia MirrorMaker z Event Hubs

W tym samouczku pokazano, jak zdublować brokera Kafka w centrum zdarzeń platformy Azure przy użyciu Kafka narzędzia MirrorMaker. Jeśli przechowujesz Apache Kafka na Kubernetes za pomocą operatora CNCF Strimzi, możesz zapoznać się z samouczkiem w [tym wpisie w blogu](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) , aby dowiedzieć się, jak skonfigurować Kafka za pomocą Strimzi i tworzenia duplikatów 2. 

   ![Kafka narzędzia MirrorMaker z Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *dozwolonych*, termin, przez który firma Microsoft już nie używa. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie klastra Kafka
> * Konfigurowanie Kafka narzędzia MirrorMaker
> * Uruchom Kafka narzędzia MirrorMaker

## <a name="introduction"></a>Wprowadzenie
W tym samouczku pokazano, jak centrum zdarzeń i Kafka narzędzia MirrorMaker mogą zintegrować istniejący potok Kafka z platformą Azure przez "dublowanie" strumienia danych wejściowych Kafka w usłudze Event Hubs, co umożliwia integrację Apache Kafka strumieni przy użyciu kilku [wzorców Federacji](event-hubs-federation-overview.md). 

Punkt końcowy usługi Azure Event Hubs Kafka umożliwia nawiązanie połączenia z usługą Azure Event Hubs przy użyciu protokołu Kafka (czyli klientów Kafka). Wprowadzając minimalne zmiany w aplikacji Kafka, możesz połączyć się z platformą Azure Event Hubs i korzystać z zalet ekosystemu platformy Azure. Event Hubs obecnie obsługuje protokół Apache Kafka w wersji 1,0 lub nowszej.

Możesz użyć Apache Kafka narzędzia MirrorMaker 1 jednokierunkowy z Apache Kafka do Event Hubs. Narzędzia MirrorMaker 2 można użyć w obu kierunkach, ale [ `MirrorCheckpointConnector` i, `MirrorHeartbeatConnector` które można skonfigurować w narzędzia MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) , muszą być skonfigurowane tak, aby wskazywały brokera Apache Kafka, a nie Event Hubs. W tym samouczku przedstawiono Konfigurowanie narzędzia MirrorMaker 1.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobieranie](https://maven.apache.org/download.cgi) i [Instalowanie](https://maven.apache.org/install.html) archiwum binarnego Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Usługa Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Skopiuj Event Hubs parametry połączenia do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz Event Hubs parametry połączenia, Sklonuj Event Hubs platformy Azure dla repozytorium Kafka i przejdź do `mirror-maker` podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurowanie klastra Kafka

Skorzystaj z [przewodnika Szybki Start](https://kafka.apache.org/quickstart) dla programu Kafka, aby skonfigurować klaster z żądanymi ustawieniami (lub użyć istniejącego klastra Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurowanie Kafka narzędzia MirrorMaker

Kafka narzędzia MirrorMaker włącza "dublowanie" strumienia. Dane źródłowe i docelowe klastry Kafka, narzędzia MirrorMaker zapewniają, że wszystkie komunikaty wysyłane do klastra źródłowego są odbierane przez klastry źródłowe i docelowe. Ten przykład pokazuje, jak zdublować źródłowy klaster Kafka z docelowym centrum zdarzeń. Ten scenariusz może służyć do wysyłania danych z istniejącego potoku Kafka do Event Hubs bez zakłócania przepływu danych. 

Aby uzyskać szczegółowe informacje na temat Kafka narzędzia MirrorMaker, zobacz [Podręcznik Kafka/narzędzia MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Aby skonfigurować Kafka narzędzia MirrorMaker, nadaj jej klasterowi Kafka jako odbiorcę i źródło danych oraz centrum zdarzeń jako jego producenta/miejsce docelowe.

#### <a name="consumer-configuration"></a>Konfiguracja konsumenta

Zaktualizuj plik konfiguracji klienta `source-kafka.config` , który informuje narzędzia MirrorMaker o właściwościach źródłowego klastra Kafka.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfiguracja producenta

Teraz Zaktualizuj plik konfiguracji producenta `mirror-eventhub.config` , który informuje narzędzia MirrorMaker o wysłaniu duplikatów (lub "dublowanych") danych do usługi Event Hubs. W celu zmiany `bootstrap.servers` i `sasl.jaas.config` wskazywania punktu końcowego Event Hubs Kafka. Usługa Event Hubs wymaga komunikacji Secure (SASL), która jest osiągana przez ustawienie ostatnich trzech właściwości w następującej konfiguracji: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Zamień na `{YOUR.EVENTHUBS.CONNECTION.STRING}` Parametry połączenia dla przestrzeni nazw Event Hubs. Aby uzyskać instrukcje dotyczące uzyskiwania parametrów połączenia, zobacz [pobieranie parametrów połączenia Event Hubs](event-hubs-get-connection-string.md). Oto Przykładowa konfiguracja: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Uruchom Kafka narzędzia MirrorMaker

Uruchom skrypt Kafka narzędzia MirrorMaker z katalogu głównego Kafka przy użyciu nowo zaktualizowanych plików konfiguracji. Upewnij się, że Skopiuj pliki konfiguracji do głównego katalogu Kafka lub zaktualizuj swoje ścieżki w poniższym poleceniu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Aby sprawdzić, czy zdarzenia docierają do centrum zdarzeń, zapoznaj się z statystykami transferu danych przychodzących w [Azure Portal](https://azure.microsoft.com/features/azure-portal/)lub Uruchom odbiorcę z centrum zdarzeń.

Po uruchomieniu usługi narzędzia MirrorMaker wszystkie zdarzenia wysyłane do źródłowego klastra Kafka są odbierane przez klaster Kafka i dublowany centrum zdarzeń. Korzystając z narzędzia MirrorMaker i punktu końcowego Kafka Event Hubs, można migrować istniejący potok Kafka do zarządzanej usługi Azure Event Hubs bez zmiany istniejącego klastra ani zakłócania ciągłego przepływu danych.

## <a name="samples"></a>Samples
Zobacz następujące przykłady w witrynie GitHub:

- [Przykładowy kod dla tego samouczka w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Usługa Azure Event Hubs Kafka narzędzia MirrorMaker uruchomiona w wystąpieniu kontenera platformy Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Event Hubs Kafka, zobacz następujące artykuły:  

- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie Kafka z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)