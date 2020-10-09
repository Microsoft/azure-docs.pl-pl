---
title: 'Samouczek: Apache Kafka producenci & interfejsy API konsumentów — Azure HDInsight'
description: Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Apache Kafka w usłudze HDInsight. W tym samouczku dowiesz się, jak używać tych interfejsów API na platformie Kafka w usłudze HDInsight z poziomu aplikacji Java.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 260a3fbb8486a1e9eeaa87e920143615e5fae867
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83681820"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Samouczek: korzystanie z interfejsów API producentów i odbiorców platformy Apache Kafka

Informacje o sposobie korzystania z interfejsów API producentów i odbiorców platformy Apache Kafka w usłudze HDInsight.

Interfejs API producenta platformy Kafka umożliwia aplikacjom wysyłanie strumieni danych do klastra Kafka. Interfejs API odbiorcy platformy Kafka umożliwia aplikacjom odczytywanie strumieni danych z klastra.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Zrozumienie kodu
> * Kompilowanie i wdrażanie aplikacji
> * Uruchamianie aplikacji w klastrze

Aby uzyskać więcej informacji o tych interfejsach API, zobacz dokumentację platformy Apache dotyczącą [interfejsu API producenta](https://kafka.apache.org/documentation/#producerapi) i [interfejsu API odbiorcy](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Wymagania wstępne

* Apache Kafka w klastrze usługi HDInsight. Aby dowiedzieć się, jak utworzyć klaster, zobacz [Rozpoczynanie pracy z Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md).
* [Zestaw Java developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks) lub równoważnej, taki jak OpenJDK.
* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.
* Klient SSH, taki jak wyglądająco. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Zrozumienie kodu

Przykładowa aplikacja znajduje się w lokalizacji [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) w `Producer-Consumer` podkatalogu. Jeśli pakiet Enterprise Security używany jest klaster Kafka **(ESP)** , należy użyć wersji aplikacji znajdującej się w `DomainJoined-Producer-Consumer` podkatalogu.

Ta aplikacja składa się zasadniczo z czterech plików:
* `pom.xml`: w tym pliku są definiowane zależności projektu, wersja języka Java i metody pakowania.
* `Producer.java`: ten plik wysyła losowe zdania do platformy Kafka przy użyciu interfejsu API producenta.
* `Consumer.java`: ten plik korzysta z interfejsu API odbiorcy do odczytywania danych z platformy Kafka i przekazywania ich do wyjścia STDOUT.
* `AdminClientWrapper.java`: Ten plik używa interfejsu API administratora do tworzenia, opisywania i usuwania tematów Kafka.
* `Run.java`: interfejs wiersza polecenia używany do uruchamiania kodu producenta i odbiorcy.

### <a name="pomxml"></a>Pom.xml

Należy zrozumieć następujące ważne kwestie dotyczące pliku `pom.xml`:

* Zależności: ten projekt bazuje na interfejsach API producenta i odbiorcy platformy Kafka, które są udostępniane w pakiecie `kafka-clients`. Ta zależność jest definiowana przez następujący kod XML:

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

    * `maven-compiler-plugin`: służy do ustawiania wersji 8 języka Java używanej przez projekt. Jest to wersja języka Java używana przez usługę HDInsight 3.6.
    * `maven-shade-plugin`: służy do generowania pełnego pliku jar zawierającego tę aplikację, a także wszelkie zależności. Jest ona również używana do ustawiania punktu wejścia aplikacji, dzięki czemu można bezpośrednio uruchamiać plik Jar bez konieczności określania klasy głównej.

### <a name="producerjava"></a>Producer.java

Producent komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) i wysyła dane do tematu platformy Kafka. Poniższy fragment kodu pochodzi z pliku [Producer. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) z [repozytorium GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) i pokazuje, jak ustawić właściwości producenta. W przypadku klastrów z włączoną obsługą zabezpieczeń przedsiębiorstwa należy dodać dodatkową właściwość "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Odbiorca komunikuje się z hostami brokera platformy Kafka (węzłami procesu roboczego) i odczytuje rekordy w pętli. Poniższy fragment kodu z pliku [konsument. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) ustawia właściwości konsumenta. W przypadku klastrów z włączoną obsługą zabezpieczeń przedsiębiorstwa należy dodać dodatkową właściwość "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

W tym kodzie odbiorca jest skonfigurowany do odczytywania od początku tematu (właściwość `auto.offset.reset` jest ustawiona na wartość `earliest`).

### <a name="runjava"></a>Run.java

Plik [Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) zawiera interfejs wiersza polecenia, w którym jest uruchamiany kod producenta lub konsumenta. Jako parametr należy podać informacje o hoście brokera platformy Kafka. Opcjonalnie możesz dołączyć wartość identyfikatora grupy, która jest używana przez proces odbiorcy. Jeśli utworzysz wiele wystąpień odbiorców przy użyciu tego samego identyfikatora grupy, to spowoduje to załadowanie z tematu równoważenia obciążenia.

## <a name="build-and-deploy-the-example"></a>Kompilowanie i wdrażanie przykładu

### <a name="use-pre-built-jar-files"></a>Użyj wstępnie skompilowanych plików JAR

Pobierz Jars z [przykładu Rozpoczynanie pracy z platformą Azure Kafka](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Jeśli klaster jest włączony **pakiet Enterprise Security (ESP)** , użyj Kafka-Producer-Consumer-ESP. jar. Użyj poniższego polecenia, aby skopiować Jars do klastra.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Kompiluj pliki JAR z kodu

Jeśli chcesz pominąć ten krok, prekompilowane Jars można pobrać z `Prebuilt-Jars` podkatalogu. Pobierz plik Kafka-Producer-Consumer. jar. Jeśli klaster jest włączony **pakiet Enterprise Security (ESP)** , użyj Kafka-Producer-Consumer-ESP. jar. Wykonaj krok 3, aby skopiować plik jar do klastra usługi HDInsight.

1. Pobierz i Wyodrębnij przykłady z programu [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) .

2. Ustaw bieżący katalog na lokalizację `hdinsight-kafka-java-get-started\Producer-Consumer` katalogu. Jeśli pakiet Enterprise Security używasz klastra Kafka **(ESP)** z włączoną obsługą klastrów, należy ustawić lokalizację na `DomainJoined-Producer-Consumer` podkatalog. Użyj następującego polecenia, aby skompilować aplikację:

    ```cmd
    mvn clean package
    ```

    To polecenie tworzy katalog o nazwie `target`, który zawiera plik o nazwie `kafka-producer-consumer-1.0-SNAPSHOT.jar`. W przypadku klastrów ESP plik zostanie `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Wprowadź następujące polecenie, aby skopiować `kafka-producer-consumer-1.0-SNAPSHOT.jar` plik do klastra usługi HDInsight. Po wyświetleniu monitu wprowadź hasło użytkownika SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Uruchamianie przykładu

1. Zamień ciąg `sshuser` na nazwę użytkownika SSH klastra i zamień ciąg `CLUSTERNAME` na nazwę klastra. Otwórz połączenie SSH z klastrem, wprowadzając następujące polecenie. Jeśli zostanie wyświetlony monit, wprowadź hasło konta użytkownika SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby uzyskać hosty brokera Kafka, Zastąp wartości `<clustername>` i `<password>` w poniższym poleceniu i wykonaj je. Użyj tej samej wielkości liter dla `<clustername>` , jak pokazano w Azure Portal. Zamień na `<password>` hasło logowania klastra, a następnie wykonaj następujące polecenie:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > To polecenie wymaga dostępu Ambari. Jeśli klaster znajduje się za sieciowej grupy zabezpieczeń, Uruchom to polecenie z komputera, który może uzyskać dostęp do Ambari.

1. Utwórz temat Kafka, `myTest` wprowadzając następujące polecenie:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Aby uruchomić producenta i zapisać dane w temacie, użyj następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Po zakończeniu procesu producenta odczytaj rekordy z tematu za pomocą następującego polecenia:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Zostanie wyświetlona liczba odczytanych rekordów wraz z liczbą rekordów.

1. Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

### <a name="multiple-consumers"></a>Wielu odbiorców

Odbiorcy platformy Kafka używają grupy odbiorców podczas odczytywania rekordów. Korzystanie z tej samej grupy przez wielu odbiorców umożliwia równoważenie obciążenia podczas przeprowadzania odczytu z tematu. Każdy odbiorca w grupie odbiera część rekordów.

Aplikacja odbiorcy akceptuje parametr, który jest używany jako identyfikator grupy. Na przykład następujące polecenie uruchamia odbiorcę przy użyciu identyfikatora grupy `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Użyj klawiszy __Ctrl+C__, aby zakończyć działanie odbiorcy.

Aby zobaczyć, jak działa ten proces, użyj następującego polecenia:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

To polecenie używa polecenia `tmux`, aby podzielić terminal na dwie kolumny. W każdej kolumnie jest uruchamiany odbiorca z tą samą wartością identyfikatora grupy. Kiedy odbiorcy zakończą odczytywanie, można zauważyć, że każdy z nich odczytał tylko część rekordów. __Naciśnij klawisz Ctrl + C__ dwa razy, aby wyjść `tmux` .

Użycie przez klientów w tej samej grupie jest obsługiwane przez partycje tematu. W tym przykładowym kodzie utworzony wcześniej temat `test` ma osiem partycji. Jeśli zostanie uruchomionych ośmiu odbiorców, każdy z nich będzie odczytywał rekordy z jednej partycji tematu.

> [!IMPORTANT]  
> Grupa odbiorców nie może zawierać więcej wystąpień odbiorców niż partycji. W tym przykładzie jedna grupa odbiorców może zawierać maksymalnie ośmiu odbiorców, ponieważ tyle partycji znajduje się w temacie. Może też istnieć wiele grup odbiorców — każda z nich może zawierać maksymalnie ośmiu odbiorców.

Rekordy przechowywane w Kafka są przechowywane w kolejności, w jakiej są odbierane w ramach partycji. Aby dostarczać rekordy *na partycji* w określonej kolejności, utwórz grupę odbiorców, w której liczba wystąpień odbiorców jest zgodna z liczbą partycji. Aby dostarczać rekordy *w temacie* w określonej kolejności, utwórz grupę odbiorców z jednym wystąpieniem odbiorcy.

## <a name="common-issues-faced"></a>Często spotykane problemy

1. **Tworzenie tematu kończy się niepowodzeniem** Jeśli w klastrze jest włączony pakiet Enterprise Security Pack, użyj [wstępnie utworzonych plików jar dla producenta i konsumenta](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). Plik JAR. ESP można skompilować z kodu w [ `DomainJoined-Producer-Consumer` podkatalogu](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). Należy zauważyć, że właściwości producent i odbiorca wyapisz dodatkową właściwość `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` dla klastrów obsługujących ESP.

2. **Problem z problemem z klastrami z obsługą ESP** Jeśli operacje tworzenia i zużywania nie powiodą się i jest używany klaster obsługujący ESP, sprawdź, czy użytkownik `kafka` jest obecny we wszystkich zasadach Ranger. Jeśli go nie ma, Dodaj go do wszystkich zasad Rangerymi.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie zawarto informacje o sposobie korzystania z interfejsu API producenta i odbiorcy platformy Apache Kafka w usłudze HDInsight. Dowiedz się więcej o pracy z platformą Kafka, korzystając z następujących zasobów:

* [Użyj serwera proxy REST Kafka](rest-proxy.md)
* [Analizowanie dzienników platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
