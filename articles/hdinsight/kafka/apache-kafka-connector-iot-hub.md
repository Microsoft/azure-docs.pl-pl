---
title: Używanie Apache Kafka w usłudze HDInsight z platformą Azure IoT Hub
description: Dowiedz się, jak używać Apache Kafka w usłudze HDInsight z usługą Azure IoT Hub. Kafka Connect Azure IoT Hub Project zawiera Łącznik źródła i ujścia dla Kafka. Łącznik źródłowy może odczytywać dane z IoT Hub, a łącznik ujścia zapisuje do IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 0722119b35ecebf3ed1e7a377707de02a6c127bf
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825190"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Używanie Apache Kafka w usłudze HDInsight z platformą Azure IoT Hub

Dowiedz się, w jaki sposób używać łącznika [usługi Azure IoT Hub Apache Kafka](https://github.com/Azure/toketi-kafka-connect-iothub) , aby przenosić dane między Apache Kafka w usłudze HDInsight i IoT Hub na platformie Azure. W tym dokumencie dowiesz się, jak uruchomić łącznik IoT Hub z węzła brzegowego w klastrze.

Interfejs API Kafka Connect umożliwia implementowanie łączników, które w sposób ciągły ściągają dane do Kafka, lub wypychania danych z Kafka do innego systemu. [Apache Kafka połączyć się z usługą azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) to łącznik, który pobiera dane z platformy Azure IoT Hub do Kafka. Może również wypychanie danych z Kafka do IoT Hub.

Podczas ściągania z IoT Hub używasz łącznika __źródła__ . Podczas wypychania do IoT Hub używany jest łącznik __ujścia__ . Łącznik IoT Hub zawiera łączniki źródła i ujścia.

Na poniższym diagramie przedstawiono przepływ danych między usługą Azure IoT Hub i Kafka w usłudze HDInsight podczas korzystania z łącznika.

![Obraz przedstawiający dane przepływające z IoT Hub do Kafka za pośrednictwem łącznika](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Aby uzyskać więcej informacji na temat interfejsu API Connect, zobacz [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Kafka w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Kafka w usłudze HDInsight](apache-kafka-get-started.md).

* Węzeł brzegowy w klastrze Kafka. Aby uzyskać więcej informacji, zobacz [Używanie węzłów brzegowych z](../hdinsight-apps-use-edge-node.md) dokumentem usługi HDInsight.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* IoT Hub i urządzenie platformy Azure. W tym artykule warto rozważyć użycie [symulatora Raspberry Pi online do IoT Hub platformy Azure](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md).

* [Narzędzie kompilacji Scala](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Kompilowanie łącznika

1. Pobierz Źródło dla łącznika z [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) do środowiska lokalnego.

2. W wierszu polecenia przejdź do `toketi-kafka-connect-iothub-master` katalogu. Następnie użyj następującego polecenia, aby skompilować i spakować projekt:

    ```cmd
    sbt assembly
    ```

    Kompilacja potrwa kilka minut. Polecenie tworzy plik o nazwie `kafka-connect-iothub-assembly_2.11-0.7.0.jar` w `toketi-kafka-connect-iothub-master\target\scala-2.11` katalogu dla projektu.

## <a name="install-the-connector"></a>Instalowanie łącznika

1. Przekaż plik jar do węzła brzegowego Kafka w klastrze usługi HDInsight. Edytuj poniższe polecenie, zastępując ją `CLUSTERNAME` rzeczywistą nazwą klastra. Poniżej są używane domyślne wartości konta użytkownika SSH i nazwy [węzła krawędzi](../hdinsight-apps-use-edge-node.md#access-an-edge-node) .

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po zakończeniu kopiowania pliku Połącz się z węzłem brzegowym przy użyciu protokołu SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby zainstalować łącznik w `libs` katalogu Kafka, użyj następującego polecenia:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Pozostaw aktywne połączenie SSH dla pozostałych kroków.

## <a name="configure-apache-kafka"></a>Konfigurowanie Apache Kafka

Z poziomu połączenia SSH z węzłem brzegowym wykonaj następujące kroki, aby skonfigurować Kafka do uruchamiania łącznika w trybie autonomicznym:

1. Skonfiguruj zmienną hasła. Zastąp hasło hasłem logowania do klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    ```

1. Zainstaluj narzędzie [JQ](https://stedolan.github.io/jq/) . JQ ułatwia przetwarzanie dokumentów JSON zwracanych z zapytań Ambari. Wprowadź następujące polecenie:

    ```bash
    sudo apt -y install jq
    ```

1. Pobierz adres brokerów Kafka. W klastrze może istnieć wiele brokerów, ale wystarczy tylko jeden lub dwa. Aby uzyskać adres dwóch hostów brokera, użyj następującego polecenia:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Skopiuj wartości do późniejszego użycia. Zwrócona wartość będzie podobna do następującego tekstu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Pobierz adres węzłów Apache dozorcy. W klastrze istnieje kilka węzłów dozorcy, ale należy tylko jeden lub dwa. Użyj następującego polecenia, aby przechowywać adresy w zmiennej `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Podczas uruchamiania łącznika w trybie autonomicznym, `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` plik jest używany do komunikowania się z brokerami Kafka. Aby edytować `connect-standalone.properties` plik, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Wprowadź następujące zmiany:

    |Bieżąca wartość |Nowa wartość | Komentarz |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Zastąp `localhost:9092` wartość hostami brokera z poprzedniego kroku|Konfiguruje konfigurację autonomiczną dla węzła brzegowego, aby znaleźć brokerów Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Ta zmiana umożliwia przetestowanie za pomocą producenta konsoli dołączonego do usługi Kafka. Mogą być potrzebne inne konwertery dla innych producentów i klientów. Aby uzyskać informacje na temat używania innych wartości konwertera, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Tak samo jak powyżej.|
    |Nie dotyczy|`consumer.max.poll.records=10`|Dodaj do końca pliku. Ta zmiana polega na uniknięciu limitów czasu w łączniku ujścia przez ograniczenie go do 10 rekordów jednocześnie. Aby uzyskać więcej informacji, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Aby zapisać plik, użyj __kombinacji klawiszy Ctrl + X__, __Y__, a następnie klawisz __Enter__.

1. Aby utworzyć tematy używane przez łącznik, użyj następujących poleceń:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Aby sprawdzić, czy `iotin` `iotout` Tematy i istnieją, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin`Temat służy do odbierania komunikatów z IoT Hub. `iotout`Temat służy do wysyłania komunikatów do IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Pobierz informacje o połączeniu IoT Hub

Aby pobrać informacje o usłudze IoT Hub używane przez łącznik, wykonaj następujące czynności:

1. Pobierz punkt końcowy zgodny z centrum zdarzeń i nazwę punktu końcowego zgodnego z centrum zdarzeń dla Centrum IoT Hub. Aby uzyskać te informacje, należy użyć jednej z następujących metod:

   * W __[Azure Portal](https://portal.azure.com/)__ wykonaj następujące czynności:

     1. Przejdź do IoT Hub i wybierz __punkty końcowe__.
     2. Z __wbudowanych punktów końcowych__ wybierz pozycję __zdarzenia__.
     3. Z __Właściwości__, skopiuj wartość następujących pól:

         * __Nazwa zgodna z centrum zdarzeń__
         * __Punkt końcowy zgodny z centrum zdarzeń__
         * __Partycje__

        > [!IMPORTANT]  
        > Wartość punktu końcowego z portalu może zawierać dodatkowy tekst, który nie jest wymagany w tym przykładzie. Wyodrębnij tekst pasujący do tego wzorca `sb://<randomnamespace>.servicebus.windows.net/` .

   * W __[interfejsie wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)__ wykonaj następujące polecenie:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Zamień `myhubname` na nazwę Centrum IoT. Odpowiedź jest podobna do następującego tekstu:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Pobierz __zasady dostępu współdzielonego__ i __klucz__. W tym przykładzie należy użyć klucza __usługi__ . Aby uzyskać te informacje, należy użyć jednej z następujących metod:

    * W __[Azure Portal](https://portal.azure.com/)__ wykonaj następujące czynności:

        1. Wybierz pozycję __zasady dostępu współdzielonego__, a następnie wybierz pozycję __Usługa__.
        2. Skopiuj wartość __klucza podstawowego__ .
        3. Skopiuj __Parametry połączenia — wartość klucza podstawowego__ .

    * W __[interfejsie wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)__ wykonaj następujące polecenie:

        1. Aby uzyskać wartość klucza podstawowego, użyj następującego polecenia:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Zamień `myhubname` na nazwę Centrum IoT. Odpowiedź jest kluczem podstawowym `service` dla zasad tego centrum.

        2. Aby uzyskać parametry połączenia dla `service` zasad, użyj następującego polecenia:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Zamień `myhubname` na nazwę Centrum IoT. Odpowiedzią są parametry połączenia dla `service` zasad.

## <a name="configure-the-source-connection"></a>Skonfiguruj połączenie źródłowe

Aby skonfigurować źródło do pracy z IoT Hub, wykonaj następujące czynności z połączenia SSH z węzłem brzegowym:

1. Utwórz kopię `connect-iot-source.properties` pliku w `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-Kafka-Connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Aby edytować `connect-iot-source.properties` plik i dodać informacje o usłudze IoT Hub, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. W edytorze Znajdź i Zmień następujące wpisy:

    |Bieżąca wartość |Edytuj|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `iotin`. Komunikaty odebrane z usługi IoT Hub są umieszczane w `iotin` temacie.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Zamień na `PLACEHOLDER` nazwę zgodną z centrum zdarzeń.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Zamień na `PLACEHOLDER` punkt końcowy zgodny z centrum zdarzeń.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Zamień na `PLACEHOLDER` klucz podstawowy `service` zasad.|
    |`IotHub.Partitions=PLACEHOLDER`|Zamień `PLACEHOLDER` na liczbę partycji z poprzednich kroków.|
    |`IotHub.StartTime=PLACEHOLDER`|Zamień `PLACEHOLDER` na datę UTC. Ta data jest momentu, gdy łącznik rozpocznie sprawdzanie pod kątem komunikatów. Format daty to `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Zastąp element `100` pytaniem `5`. Ta zmiana powoduje, że łącznik odczytuje wiadomości do Kafka po pięciu nowych komunikatach w usłudze IoT Hub.|

    Aby zapoznać się z przykładową konfiguracją, zobacz [Łącznik Kafka Connect Source for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Aby zapisać zmiany, użyj __klawiszy Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania źródła łącznika, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>Konfigurowanie połączenia ujścia

Aby skonfigurować połączenie ujścia do pracy z IoT Hub, wykonaj następujące czynności z połączenia SSH z węzłem brzegowym:

1. Utwórz kopię `connect-iothub-sink.properties` pliku w `/usr/hdp/current/kafka-broker/config/` katalogu. Aby pobrać plik z projektu toketi-Kafka-Connect-iothub, użyj następującego polecenia:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Aby edytować `connect-iothub-sink.properties` plik i dodać informacje o usłudze IoT Hub, użyj następującego polecenia:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. W edytorze Znajdź i Zmień następujące wpisy:

    |Bieżąca wartość |Edytuj|
    |---|---|
    |`topics=PLACEHOLDER`|Zastąp element `PLACEHOLDER` pytaniem `iotout`. Komunikaty zapisywane w `iotout` temacie są przekazywane do centrum IoT Hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Zamień na `PLACEHOLDER` Parametry połączenia dla `service` zasad.|

    Aby zapoznać się z przykładową konfiguracją, zobacz [Łącznik Kafka Connect sink for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Aby zapisać zmiany, użyj __klawiszy Ctrl + X__, __Y__, a następnie __Enter__.

Aby uzyskać więcej informacji na temat konfigurowania ujścia łącznika, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>Uruchom łącznik źródłowy

1. Aby uruchomić łącznik źródłowy, użyj następującego polecenia z połączenia SSH z węzłem krawędzi:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Po uruchomieniu łącznika Wyślij komunikaty do usługi IoT Hub z urządzeń. Ponieważ łącznik odczytuje komunikaty z Centrum IoT Hub i zapisuje je w temacie Kafka, rejestruje informacje w konsoli programu:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Podczas uruchamiania łącznika mogą pojawić się pewne ostrzeżenia. Te ostrzeżenia nie powodują problemów z odebraniem komunikatów z usługi IoT Hub.

1. Zatrzymaj łącznik po kilku minutach przy użyciu **kombinacji klawiszy CTRL + C** dwa razy. Zatrzymanie łącznika potrwa kilka minut.

## <a name="start-the-sink-connector"></a>Uruchom łącznik ujścia

Z poziomu połączenia SSH z węzłem brzegowym Użyj następującego polecenia, aby uruchomić łącznik ujścia w trybie autonomicznym:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

W trakcie działania łącznika zostanie wyświetlona informacja podobna do następującego tekstu:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Podczas uruchamiania łącznika można zauważyć kilka ostrzeżeń. Możesz je bezpiecznie zignorować.

## <a name="send-messages"></a>Wysyłanie komunikatów

Aby wysyłać komunikaty za pomocą łącznika, wykonaj następujące czynności:

1. Otwórz *drugą* sesję SSH w klastrze Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pobierz adres brokerów Kafka dla nowej sesji SSH. Zastąp hasło hasłem logowania do klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Aby wysyłać komunikaty do `iotout` tematu, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    To polecenie nie zwraca użytkownika do normalnego monitu bash. Zamiast tego wysyła dane wejściowe z klawiatury do `iotout` tematu.

1. Aby wysłać komunikat do urządzenia, wklej dokument JSON w sesji SSH dla `kafka-console-producer` .

    > [!IMPORTANT]  
    > Należy ustawić wartość `"deviceId"` wpisu na identyfikator urządzenia. W poniższym przykładzie urządzenie ma nazwę `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schemat tego dokumentu JSON został opisany bardziej szczegółowo w temacie [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Jeśli używasz symulowanego urządzenia Raspberry Pi i jest ono uruchomione, na urządzeniu jest rejestrowany następujący komunikat:

    ```output
    Receive message: Turn On
    ```

    Wyślij ponownie dokument JSON, ale Zmień wartość `"message"` wpisu. Nowa wartość jest rejestrowana przez urządzenie.

Aby uzyskać więcej informacji na temat używania łącznika ujścia, zobacz [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z interfejsu API Apache Kafka Connect w celu uruchomienia łącznika usługi IoT Kafka w usłudze HDInsight. Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Kafka:

* [Używanie Apache Spark z Apache Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Używanie Apache Storm z Apache Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
