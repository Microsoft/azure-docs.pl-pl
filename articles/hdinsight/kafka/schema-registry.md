---
title: Apache Kafka z rejestrem schematu z usługą Azure HDInsight
description: Wdróż klaster Kafka zarządzany przez usługę HDInsight z węzłem brzegowym wewnątrz Virtual Network a następnie zainstaluj rejestr schematu programu w węźle brzegowym.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500326"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka z rejestrem schematu z usługą Azure HDInsight

Rejestr schematu Kafka udostępnia serializatory, które mogą przyłączyć się do Apache Kafka klientów, którzy obsługują magazyn schematu komunikatów i pobieranie komunikatów Kafka, które są wysyłane w formacie Avro. Jest on używany jako projekt OSS przez, ale jest teraz objęty [licencją społecznościową](https://www.confluent.io/blog/license-changes-confluent-platform/). Ponadto rejestr schematu służy do poniższych celów:

* Przechowuj i pobieraj schematy dla producentów i konsumentów.
* Wymuś zgodność/Full z poprzednimi/do przodu w tematach.
* Zmniejsz rozmiar ładunku wysyłanego do Kafka.

W klastrze Kafka zarządzanym przez usługi HDInsight rejestr schematu jest zazwyczaj wdrażany w węźle brzegowym, aby umożliwić rozdzielenie obliczeń z węzłów głównych.

Poniżej znajduje się reprezentatywna architektura, w jaki sposób jest wdrażany rejestr schematu w klastrze usługi HDInsight. Rejestr schematu natywnie uwidacznia interfejs API REST na potrzeby operacji na nim.  Producenci i konsumenci mogą współdziałać z rejestrem schematu z poziomu sieci wirtualnej lub przy użyciu [serwera proxy REST Kafka](rest-proxy.md).

![Rejestr schematu usługi HDInsight Kafka](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Wdrażanie usługi HDInsight Managed Kafka z rejestrem schematu z programem Fluent

W tej sekcji wdrażamy klaster Kafka zarządzany przez usługę HDInsight z węzłem brzegowym wewnątrz sieci wirtualnej, a następnie instalujesz rejestr schematu programu w węźle brzegowym.  

1. Wybierz przycisk **Wdróż na platformie Azure** poniżej, aby zalogować się do platformy Azure i otworzyć szablon Menedżer zasobów.

    [![Wdrażanie na platformie Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. W szablonie wdrożenia niestandardowego Wypełnij pola zgodnie z poniższym opisem:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Region (Region)|Z listy rozwijanej wybierz region, w którym tworzony jest klaster.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. Lub pozostaw jako-is, aby użyć nazwy domyślnej.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło.|
    |Nazwa użytkownika SSH|Podaj nazwę użytkownika, wartość domyślna to **sshuser**.|
    |Hasło ssh|Podaj hasło.|

    Pozostaw pozostałe pola jako-is. Wybierz pozycję **Recenzja + Utwórz** , aby kontynuować.

1. Przejrzyj szczegóły wdrożenia, a następnie wybierz pozycję **Utwórz** , aby zainicjować wdrożenie. Wdrożenie może potrwać 45 minut.

## <a name="configure-the-confluent-schema-registry"></a>Konfigurowanie rejestru schematu ze schematem

W tej sekcji skonfigurujemy rejestr schematu Kafka, który został zainstalowany w węźle brzegowym. Rejestr schematu programu jest zlokalizowany w programie  `/etc/schema-registry/schema-registry.properties` , a mechanizmy uruchamiania i zatrzymywania plików wykonywalnych usługi znajdują się w  `/usr/bin/` folderze.

Rejestr schematu musi wiedzieć, że usługa dozorcy może współistnieć z klastrem usługi HDInsight Kafka. Postępuj zgodnie z poniższymi instrukcjami, aby uzyskać szczegółowe informacje dotyczące kworum dozorcy.

1. Połącz się z węzłem brzegowym klastra przy użyciu [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Skonfiguruj zmienną hasła. Zastąp hasło hasłem logowania do klastra, a następnie wprowadź polecenie:

    ```bash
    export password='PASSWORD'
    ```

1. Wyodrębnij poprawną nazwę klastra z wielkością liter. Uruchom następujące polecenie:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Wyodrębnij hosty Kafka dozorcy. Uruchom następujące polecenie:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Zanotuj tę wartość, ponieważ zostanie użyta później.

1. Wyodrębnij hosty brokera Kafka. Uruchom następujące polecenie:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Otwórz plik właściwości rejestru schematu w trybie edycji. Uruchom następujące polecenie:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Zaktualizuj wartość dla `kafkastore.connection.url` z określonym wcześniej ciągiem dozorcy.
    1. Zaktualizuj wartość parametru `debug` do `true` .

    Plik właściwości będzie teraz wyglądać podobnie do:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Aby zapisać plik, użyj **kombinacji klawiszy Ctrl + X**, **Y**, a następnie klawisz **Enter**.

1. Uruchom rejestr schematu i wskaż go, aby użyć zaktualizowanego pliku właściwości rejestru schematu. Wykonaj następujące polecenia:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. W rejestrze schematu uruchomionym w jednej sesji SSH Uruchom inne okno protokołu SSH.  Zarejestruj nową wersję schematu pod podmiotem "Kafka-Key" i zanotuj dane wyjściowe:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Zarejestruj nową wersję schematu w temacie "Kafka-Value" i zanotuj dane wyjściowe:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Utwórz listę wszystkich podmiotów i sprawdź dane wyjściowe:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Warto wypróbować inne [polecenia Zaawansowane wymienione tutaj](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Wysyłanie i używanie danych Avro z Kafka przy użyciu rejestru schematu

W tej sekcji będziemy odczytywać dane z standardowego wejścia i zapisywać je w temacie Kafka w formacie. Następnie odczytajemy dane z tematów Kafka przy użyciu klienta z programem formatującego Avro w celu przekształcenia tych danych w czytelny format.

1. Utwórz temat Kafka `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Użyj **producenta konsoli Avro Kafka** , aby utworzyć schemat, przypisać schemat do tematu i rozpocząć wysyłanie danych do tematu w formacie Avro. Upewnij się, że temat Kafka w poprzednim kroku został pomyślnie utworzony i że **$KAFKABROKERS** ma w nim wartość.

    Wysyłany schemat jest kluczem: value para

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Użyj poniższego polecenia, aby uruchomić **producenta konsoli programu Kafka Avro**:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Gdy producent jest gotowy do akceptowania komunikatów, należy rozpocząć wysyłanie komunikatów we wstępnie zdefiniowanym formacie schematu Avro. Użyj klawisza Tab, aby utworzyć odstępy między kluczem i wartością.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Spróbuj wprowadzić losowe dane niebędące schematami do producenta konsoli, aby zobaczyć, w jaki sposób producent ma teraz dostęp do wszystkich danych, które nie są zgodne ze wstępnie zdefiniowanym schematem Avro.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Na innym ekranie Uruchom klienta konsoli programu Kafka Avro

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Należy zacząć wyświetlać poniższe dane wyjściowe:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z interfejsów API Apache Kafka producenta i konsumenta](apache-kafka-producer-consumer-api.md)