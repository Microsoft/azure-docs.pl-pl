---
title: 'Samouczek: korzystanie z interfejsu API strumieni platformy Apache Kafka — Azure HDInsight '
description: Samouczek — informacje dotyczące korzystania z interfejsu API strumieni Apache Kafka z usługą Kafka w usłudze HDInsight. Ten interfejs API umożliwia przetwarzanie strumieni między tematami na platformie Kafka.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 5a1548cdf1d05a1f9d42f5c64b7fdc18f514518e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939203"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Samouczek: korzystanie z interfejsu API strumieni Apache Kafka w usłudze Azure HDInsight

Dowiedz się, jak utworzyć aplikację, która używa interfejsu API strumieni platformy Apache Kafka, i uruchomić ją na platformie Kafka w usłudze HDInsight.

Aplikacja przedstawiona w tym samouczku zlicza przesyłane strumieniowo wyrazy. Odczytuje ona dane tekstowe z tematu platformy Kafka, wyodrębnia poszczególne wyrazy, a następnie zapisuje liczbę wyrazów w innym temacie platformy Kafka.

Przetwarzanie strumienia platformy Kafka jest często wykonywane przy użyciu platformy Apache Spark lub systemu Apache Storm. Kafka w wersji 1.1.0 (w usłudze HDInsight 3,5 i 3,6) wprowadzono interfejs API strumieni Kafka. Ten interfejs API umożliwia przekształcanie strumieni danych między tematami wejściowymi i wyjściowymi. W niektórych przypadkach może to być alternatywą dla tworzenia rozwiązań przesyłania strumieniowego platformy Spark lub systemu Storm.

Aby uzyskać więcej informacji o strumieniach platformy Kafka, zobacz dokumentację [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Wprowadzenie do strumieni) w serwisie Apache.org.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zrozumienie kodu
> * Kompilowanie i wdrażanie aplikacji
> * Konfigurowanie tematów platformy Kafka
> * Uruchamianie kodu

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Kafka w usłudze HDInsight 3.6. Aby dowiedzieć się, jak utworzyć platformę Kafka w klastrze usługi HDInsight, zobacz dokument [Wprowadzenie do platformy Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Wykonaj kroki opisane w dokumencie [Apache Kafka Consumer and Producer API](apache-kafka-producer-consumer-api.md) (Interfejs API odbiorcy i producenta na platformie Apache Kafka). Czynności opisane w tym dokumencie bazują na przykładowej aplikacji i tematach utworzonych w tym samouczku.

* [Zestaw Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) lub równoważnej, taki jak OpenJDK.

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Zrozumienie kodu

Przykładowa aplikacja znajduje się w lokalizacji [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) w `Streaming` podkatalogu. Aplikacja składa się z dwóch plików:

* `pom.xml`: w tym pliku są definiowane zależności projektu, wersja języka Java i metody pakowania.
* `Stream.java`: ten plik implementuje logikę przesyłania strumieniowego.

### <a name="pomxml"></a>Pom.xml

Należy zrozumieć następujące ważne kwestie dotyczące pliku `pom.xml`:

* Zależności: ten projekt bazuje na interfejsie API strumieni platformy Kafka, który jest udostępniany w pakiecie `kafka-clients`. Ta zależność jest definiowana przez następujący kod XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    Wpis `${kafka.version}` jest zadeklarowany w sekcji `<properties>..</properties>` pliku `pom.xml` i jest skonfigurowany zgodnie z wersją platformy Kafka znajdującą się w klastrze usługi HDInsight.

* Wtyczki: wtyczki Maven zapewniają różne możliwości. W tym projekcie są używane następujące wtyczki:

    * `maven-compiler-plugin`: służy do ustawiania wersji 8 języka Java używanej przez projekt. Język Java 8 jest wymagany przez usługę HDInsight 3.6.
    * `maven-shade-plugin`: Służy do generowania Uber jar, który zawiera tę aplikację, oraz wszystkich zależności. Służy również do ustawiania punktu wejścia aplikacji, dzięki czemu można bezpośrednio uruchomić plik JAR bez konieczności określania klasy głównej.

### <a name="streamjava"></a>Stream.java

Plik [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) używa interfejsu API strumieni do zaimplementowania aplikacji liczącej wyrazy. Odczytuje on dane z tematu Kafka o nazwie `test` i zapisuje liczbę wyrazów w temacie o nazwie `wordcounts`.

Poniższy kod definiuje aplikację do zliczania wyrazów:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Kompilowanie i wdrażanie przykładu

Aby skompilować i wdrożyć projekt na platformie Kafka w klastrze usługi HDInsight, wykonaj następujące kroki:

1. Ustaw bieżący katalog na lokalizację `hdinsight-kafka-java-get-started-master\Streaming` katalogu, a następnie użyj następującego polecenia, aby utworzyć pakiet jar:

    ```cmd
    mvn clean package
    ```

    To polecenie tworzy pakiet w lokalizacji `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `clustername` na nazwę klastra. Użyj poniższego polecenia, aby skopiować `kafka-streaming-1.0-SNAPSHOT.jar` plik do klastra usługi HDInsight. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Tworzenie tematów platformy Apache Kafka

1. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Otwórz połączenie SSH z klastrem, wprowadzając następujące polecenie. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Zainstaluj [JQ](https://stedolan.github.io/jq/), procesor JSON w wierszu polecenia. Z otwartego połączenia SSH wprowadź następujące polecenie, aby zainstalować `jq` :

    ```bash
    sudo apt -y install jq
    ```

3. Skonfiguruj zmienną hasła. Zamień na `PASSWORD` hasło logowania klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    ```

4. Wyodrębnij poprawnie wielkość liter w nazwach klastra. Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano, w zależności od sposobu utworzenia klastra. To polecenie spowoduje pobranie rzeczywistej wielkości liter, a następnie zapisanie jej w zmiennej. Wprowadź następujące polecenie:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Jeśli ten proces jest wykonywany spoza klastra, istnieje inna procedura przechowywania nazwy klastra. W mniejszej sytuacji należy uzyskać nazwę klastra z Azure Portal. Następnie zastąp nazwę klastra `<clustername>` w następującym poleceniu i wykonaj go: `export clusterName='<clustername>'` .  

5. Aby uzyskać hosty brokera platformy Kafka i hosty usługi Apache Zookeeper, użyj poniższych poleceń. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (administratora).

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Te polecenia wymagają dostępu Ambari. Jeśli klaster znajduje się za sieciowej grupy zabezpieczeń, Uruchom te polecenia z komputera, który może uzyskać dostęp do Ambari.

6. Aby utworzyć tematy używane przez operację przesyłania strumieniowego, użyj następujących poleceń:

    > [!NOTE]  
    > Może zostać wyświetlony błąd z informacją, że temat `test` już istnieje. Nie stanowi to problemu, ponieważ ten temat mógł zostać utworzony w samouczku dotyczącym interfejsu API producenta i odbiorcy.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Tematy są używane do następujących celów:

   * `test`: w tym temacie są odbierane rekordy. Aplikacja do przesyłania strumieniowego odczytuje dane z tego tematu.
   * `wordcounts`: w tym temacie aplikacja do przesyłania strumieniowego przechowuje swoje dane wyjściowe.
   * `RekeyedIntermediateTopic`: w tym temacie zachodzi ponowne dzielenie danych, ponieważ liczba wyrazów jest aktualizowana za pomocą operatora `countByKey`.
   * `wordcount-example-Counts-changelog`: ten temat jest magazynem stanów używanym przez operację `countByKey`

    Platformę Kafka w usłudze HDInsight można również skonfigurować w taki sposób, aby automatycznie tworzyła tematy. Aby uzyskać więcej informacji, zobacz dokument [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Konfigurowanie automatycznego tworzenia tematów).

## <a name="run-the-code"></a>Uruchamianie kodu

1. Aby uruchomić aplikację do przesyłania strumieniowego jako proces w tle, użyj następującego polecenia:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Może zostać wyświetlone ostrzeżenie dotyczące mechanizmu Apache log4j. Możne je zignorować.

2. Aby wysyłać rekordy do tematu `test`, użyj następującego polecenia w celu uruchomienia aplikacji producenta:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Po zakończeniu działania producenta użyj następującego polecenia, aby wyświetlić informacje przechowywane w temacie `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    Dzięki użyciu parametrów `--property` odbiorca konsoli drukuje zarówno klucz (wyraz), jak i liczbę (wartość). Ten parametr konfiguruje również deserializatora do użycia podczas odczytu tych wartości z platformy Kafka.

    Dane wyjściowe będą podobne do następującego tekstu:

    ```output
    dwarfs  13635
    ago     13664
    snow    13636
    dwarfs  13636
    ago     13665
    a       13803
    ago     13666
    a       13804
    ago     13667
    ago     13668
    jumped  13640
    jumped  13641
    ```

    Parametr `--from-beginning` konfiguruje odbiorcę, aby rozpoczął przetwarzanie od początku rekordów przechowywanych w temacie. Liczba wystąpień zwiększa się każdorazowo po napotkaniu wyrazu, dlatego temat zawiera wiele pozycji dla każdego wyrazu ze zwiększającą się liczbą wystąpień.

4. Użyj klawiszy __Ctrl + C__, aby zakończyć działanie producenta. Podobnie użyj klawiszy __Ctrl + C__, aby zakończyć działanie aplikacji i odbiorcy.

5. Aby usunąć tematy używane przez operację przesyłania strumieniowego, użyj następujących poleceń:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto informacje o sposobie korzystania z interfejsu API strumieni platformy Apache Kafka w usłudze HDInsight. Aby dowiedzieć się więcej na temat pracy z usługą Kafka, Skorzystaj z następujących informacji.

> [!div class="nextstepaction"]
> [Analizowanie dzienników platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)