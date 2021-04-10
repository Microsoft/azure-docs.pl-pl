---
title: 'Samouczek: Apache Storm w usłudze Apache Kafka — Azure HDInsight'
description: Dowiedz się, jak utworzyć potok przesyłania strumieniowego przy użyciu systemu Apache Storm i platformy Apache Kafka w usłudze HDInsight. W tym samouczku składniki KafkaBolt i KafkaSpout są używane do przesyłania strumieniowego z platformy Kafka.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 2078ba177d68cfaa0a91b79611d92e5c68e4e245
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868421"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Samouczek: korzystanie z systemu Apache Storm z platformą Apache Kafka w usłudze HDInsight

W tym samouczku przedstawiono sposób użycia topologii systemu [Apache Storm](https://storm.apache.org/) do odczytywania i zapisywania danych przy użyciu platformy [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight. W tym samouczku pokazano także sposób utrwalania danych w magazynie zgodnym z systemem plików [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) w klastrze Storm.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * System Storm i platforma Kafka
> * Omówienie kodu
> * Tworzenie klastrów Kafka i Storm
> * Tworzenie topologii
> * Konfigurowanie topologii
> * Tworzenie tematu platformy Kafka
> * Uruchamianie topologii
> * Zatrzymywanie topologii
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość zagadnień dotyczących tworzenia tematów platformy Kafka. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Kafka w usłudze HDInsight](./kafka/apache-kafka-get-started.md).

* Znajomość zagadnień dotyczących tworzenia i wdrażania rozwiązań systemu Storm (topologii). W szczególności topologii wykorzystujących strukturę [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html). Aby uzyskać więcej informacji, zobacz dokument [Tworzenie topologii systemu Apache Storm w języku Java](./storm/apache-storm-develop-java-topology.md).

* Zestaw [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy. Usługa HDInsight w wersji 3.5 lub nowszej wymaga środowiska Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Klient SSH (potrzebne są polecenia `ssh` i `scp`) — informacje zawiera artykuł [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Po zainstalowaniu środowiska Java i zestawu JDK na deweloperskiej stacji roboczej mogą zostać ustawione następujące zmienne środowiskowe. Należy jednak sprawdzić, czy te zmienne istnieją i czy zawierają poprawne wartości dla systemu.

* `JAVA_HOME` — powinna wskazywać katalog, w którym jest zainstalowany zestaw JDK.
* `PATH` — powinna zawierać następujące ścieżki:
  
    * `JAVA_HOME` (lub równoważną ścieżkę).
    * `JAVA_HOME\bin` (lub równoważną ścieżkę).
    * Katalog, w którym zainstalowano narzędzie Maven.

> [!IMPORTANT]  
> Kroki przedstawione w tym dokumencie wymagają grupy zasobów platformy Azure, która zawiera zarówno system Storm w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Storm może komunikować się bezpośrednio z klastrem Kafka.
> 
> Dla Twojej wygody w tym dokumencie umieszczono link do szablonu, który umożliwia utworzenie wszystkich wymaganych zasobów platformy Azure. 
>
> Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zapoznaj się z dokumentem [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md) .

## <a name="storm-and-kafka"></a>System Storm i platforma Kafka

System Apache Storm udostępnia kilka składników do pracy z platformą Apache Kafka. W tym samouczku są używane następujące składniki:

* `org.apache.storm.kafka.KafkaSpout`: ten składnik odczytuje dane z platformy Kafka. Zależy on od następujących składników:

    * `org.apache.storm.kafka.SpoutConfig`: udostępnia konfigurację składnika spout.

    * `org.apache.storm.spout.SchemeAsMultiScheme` i `org.apache.storm.kafka.StringScheme`: sposób przekształcania danych z platformy Kafka w krotkę systemu Storm.

* `org.apache.storm.kafka.bolt.KafkaBolt`: ten składnik zapisuje dane do platformy Kafka. Zależy on od następujących składników:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: opisuje temat docelowy zapisu.

    * `org.apache.kafka.common.serialization.StringSerializer`: konfiguruje składnik bolt pod kątem serializowania danych jako wartości ciągu.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: mapuje strukturę danych krotki używaną w topologii systemu Storm na pola przechowywane na platformie Kafka.

Te składniki są dostępne w pakiecie `org.apache.storm : storm-kafka`. Użyj wersji pakietu zgodnej z wersją systemu Storm. W przypadku usługi HDInsight 3.6 właściwa wersja systemu Storm to 1.1.0.
Potrzebny jest także pakiet `org.apache.kafka : kafka_2.10`, który zawiera dodatkowe składniki platformy Kafka. Użyj wersji pakietu zgodnej z wersją platformy Kafka. W przypadku usługi HDInsight 3,6 wersja Kafka to 1.1.1.

Następujący kod XML to deklaracja zależności w pliku `pom.xml` projektu narzędzia [Apache Maven](https://maven.apache.org/):

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Omówienie kodu

Kod używany w tym dokumencie jest dostępny pod adresem [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) .

Ten samouczek obejmuje dwie topologie:

* Moduł zapisujący platformy Kafka: generuje losowe zdania i zapisuje je na platformie Kafka.

* Moduł odczytujący platformy Kafka: odczytuje dane z platformy Kafka, a następnie zapisuje je w magazynie plików zgodnym z systemem plików HDFS klastra Storm.

    > [!WARNING]  
    > Aby umożliwić pracę systemu Storm z magazynem zgodnym z systemem plików HDFS używanym przez usługę HDInsight, należy wykonać akcję skryptu. Skrypt instaluje kilka plików jar w ścieżce `extlib` systemu Storm. Szablon w tym samouczku automatycznie używa skryptu podczas tworzenia klastra.
    >
    > Jeśli nie używasz szablonu dostępnego w tym dokumencie do utworzenia klastra Storm, musisz ręcznie zastosować akcję skryptu dla klastra.
    >
    > Akcja skryptu znajduje się w lokalizacji [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) i jest stosowana do węzłów nadzorcy i Nimbus klastra burzy. Aby uzyskać więcej informacji dotyczących akcji skryptu, zobacz dokument [Dostosowywanie usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Topologie definiuje się przy użyciu struktury [Flux](https://storm.apache.org/releases/current/flux.html). Struktura Flux została wprowadzona w systemie Storm 0.10.x i umożliwia rozdzielenie konfiguracji topologii i kodu. Topologie wykorzystujące strukturę Flux są definiowane w pliku YAML. Plik YAML może sam być częścią topologii. Istnieje także możliwość użycia go jako pliku autonomicznego podczas przesyłania topologii. Struktura Flux obsługuje również podstawianie wartości zmiennych w czasie wykonywania, które jest używane w tym przykładzie.

Następujące parametry są ustawiane w czasie wykonywania dla tych topologii:

* `${kafka.topic}`: nazwa tematu platformy Kafka używanego przez topologię do odczytu i zapisu.

* `${kafka.broker.hosts}`: hosty, na których są uruchomione brokery platformy Kafka. Informacje o brokerze są używane przez składnik KafkaBolt podczas zapisywania do platformy Kafka.

* `${kafka.zookeeper.hosts}`: hosty w klastrze Kafka, na których jest uruchomiona usługa Zookeeper.

* `${hdfs.url}`: adres URL systemu plików dla składnika HDFSBolt. Wskazuje, czy dane są zapisywane na koncie usługi Azure Storage, czy w usłudze Azure Data Lake Storage.

* `${hdfs.write.dir}`: katalog, w którym są zapisywane dane.

Aby uzyskać więcej informacji na temat topologii strumienia, zobacz [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

### <a name="kafka-writer"></a>Moduł zapisujący platformy Kafka

W topologii modułu zapisującego platformy Kafka składnik KafkaBolt pobiera dwa parametry w postaci wartości ciągu. Parametry te wskazują, które pola krotki są wysyłane przez składnik bolt do platformy Kafka w postaci wartości __klucza__ i __komunikatu__. Klucz jest używany do partycjonowania danych na platformie Kafka. Komunikat to zapisywane dane.

W tym przykładzie składnik `com.microsoft.example.SentenceSpout` emituje krotkę zawierającą dwa pola: `key` i `message`. Składnik KafkaBolt wyodrębnia te pola i wysyła ich dane do platformy Kafka.

Pola nie muszą mieć nazw `key` i `message`. Te nazwy są używane w projekcie, aby ułatwić zrozumienie mapowania.

Następujący kod YAML stanowi definicję modułu zapisującego platformy Kafka:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Moduł odczytujący platformy Kafka

W przypadku topologii modułu odczytującego platformy Kafka składnik spout odczytuje dane z platformy Kafka w postaci wartości ciągu. Dane są następnie zapisywane w dzienniku systemu Storm przez składnik rejestrujący i w systemie plików zgodnym z system plików HDFS klastra Storm przez składnik bolt systemu plików HDFS.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Podstawianie właściwości

Projekt zawiera plik o nazwie `dev.properties` używany do przekazywania parametrów używanych przez topologie. Są w nim zdefiniowane następujące właściwości:

| Plik dev.properties | Opis |
| --- | --- |
| `kafka.zookeeper.hosts` | Hosty usługi [Apache Zookeeper](https://zookeeper.apache.org/) dla klastra Kafka. |
| `kafka.broker.hosts` | Hosty brokera platformy Kafka (węzły procesu roboczego). |
| `kafka.topic` | Temat platformy Kafka używany przez topologie. |
| `hdfs.write.dir` | Katalog, w którym topologia modułu odczytującego platformy Kafka zapisuje dane. |
| `hdfs.url` | System plików używany przez klaster Storm. W przypadku kont usługi Azure Storage użyj wartości `wasb://`. W przypadku usługi Azure Data Lake Storage Gen2 użyj wartości `abfs://`. W przypadku usługi Azure Data Lake Storage Gen1 użyj wartości `adl://`. |

## <a name="create-the-clusters"></a>Tworzenie klastrów

Platforma Apache Kafka w usłudze HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie elementy, które używają platformy Kafka, muszą znajdować się w tej samej sieci wirtualnej platformy Azure. W tym samouczku zarówno klaster Kafka, jak i klaster Storm znajdują się w tej samej sieci wirtualnej platformy Azure. 

Na poniższym diagramie przedstawiono przepływ komunikacji między systemem Storm i platformą Kafka:

:::image type="content" source="./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png" alt-text="Diagram przedstawiający klastry Storm i Kafka w sieci wirtualnej platformy Azure":::

> [!NOTE]  
> Inne usługi w klastrze, takie jak SSH i [Apache Ambari](https://ambari.apache.org/), są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Aby utworzyć usługę Azure Virtual Network, a następnie utworzyć w niej klastry Kafka i Storm, wykonaj następujące kroki:

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon Azure Resource Manager znajduje się w lokalizacji **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** . Umożliwia on utworzenie następujących zasobów:

    * Grupa zasobów platformy Azure
    * Azure Virtual Network
    * Konto usługi Azure Storage
    * Platforma Kafka w usłudze HDInsight 3.6 (trzy węzły procesu roboczego)
    * System Storm w usłudze HDInsight 3.6 (trzy węzły procesu roboczego)

   > [!WARNING]  
   > Aby zapewnić dostępność platformy Kafka w usłudze HDInsight, klaster musi zawierać co najmniej trzy węzły procesu roboczego. Ten szablon umożliwia utworzenie klastra Kafka zawierającego trzy węzły procesu roboczego.

2. Wypełnij pola w sekcji **Wdrożenie niestandardowe**, używając następujących wskazówek:

   1. Wypełnij pola w sekcji **Dostosowany szablon**, korzystając z poniższych informacji:

      | Ustawienie | Wartość |
      | --- | --- |
      | Subskrypcja | Twoja subskrypcja platformy Azure |
      | Grupa zasobów | Grupa zasobów zawierająca zasoby. |
      | Lokalizacja | Region świadczenia usługi Azure, w którym są tworzone zasoby. |
      | Nazwa klastra Kafka | Nazwa klastra Kafka. |
      | Nazwa klastra Storm | Nazwa klastra Storm. |
      | Nazwa użytkownika logowania klastra | Nazwa użytkownika będącego administratorem klastrów. |
      | Hasło logowania klastra | Hasło użytkownika będącego administratorem klastrów. |
      | Nazwa użytkownika SSH | Użytkownik SSH tworzony na potrzeby obsługi klastrów. |
      | Hasło SSH | Hasło użytkownika SSH. |
   
      :::image type="content" source="./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png" alt-text="Obraz parametrów szablonu":::

3. Przeczytaj **Warunki i postanowienia**, a następnie wybierz pozycję **Wyrażam zgodę na powyższe warunki i postanowienia**.

4. Na koniec zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie wybierz pozycję **Kup**.

> [!NOTE]  
> Tworzenie klastrów może potrwać do 20 minut.

## <a name="build-the-topology"></a>Tworzenie topologii

1. W środowisku programistycznym Pobierz projekt z [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) , Otwórz wiersz polecenia i zmień katalogi na lokalizację, w której pobrano projekt.

2. W katalogu **hdinsight-storm-java-kafka** wykonaj następujące polecenie, aby skompilować projekt i utworzyć pakiet na potrzeby wdrażania:

   ```bash
   mvn clean package
   ```

    Proces pakietu tworzy plik o nazwie `KafkaTopology-1.0-SNAPSHOT.jar` w katalogu `target`.

3. Za pomocą poniższych poleceń skopiuj pakiet do klastra Storm w usłudze HDInsight. Zastąp ciąg `sshuser` nazwą użytkownika protokołu SSH dla klastra. Zastąp ciąg `stormclustername` nazwą klastra __Storm__.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    Po wyświetleniu monitu podaj hasło, którego użyto podczas tworzenia klastrów.

## <a name="configure-the-topology"></a>Konfigurowanie topologii

1. Za pomocą jednej z następujących metod odnajdź hosty brokera platformy **Kafka** w klastrze usługi HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > W poniższym przykładzie dla powłoki Bash przyjęto, że zmienna `$CLUSTERNAME` zawiera nazwę klastra __Kafka__. Przyjęto także, że jest zainstalowane narzędzie [jq](https://stedolan.github.io/jq/) w wersji 1.5 lub nowszej. Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Zwrócona wartość będzie podobna do następującego tekstu:

    ```output
    wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > Choć mogą występować więcej niż dwa hosty brokera dla klastra, nie trzeba podawać klientom pełnej listy wszystkich hostów. Wystarczy jeden lub dwa.

2. Za pomocą jednej z następujących metod odnajdź hosty usługi Zookeeper platformy __Kafka__ w klastrze usługi HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > W poniższym przykładzie dla powłoki Bash przyjęto, że zmienna `$CLUSTERNAME` zawiera nazwę klastra __Kafka__. Przyjęto również, że jest zainstalowane narzędzie [jq](https://stedolan.github.io/jq/). Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Zwrócona wartość będzie podobna do następującego tekstu:

    ```output
    zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    ```

    > [!IMPORTANT]  
    > Choć występują więcej niż dwa węzły usługi Zookeeper, nie trzeba podawać klientom pełnej listy wszystkich hostów. Wystarczy jeden lub dwa.

    Zapisz tę wartość, ponieważ jest używana później.

3. Otwórz do edycji plik `dev.properties` w folderze głównym projektu. Dodaj informacje o hostach brokera i usługi Zookeeper klastra __Kafka__ w odpowiednich wierszach w pliku. Następujący przykład jest skonfigurowany przy użyciu przykładowych wartości z poprzednich kroków:

    ```bash
    kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
    kafka.topic: stormtopic
    ```

    > [!IMPORTANT]  
    > Wpis `hdfs.url` jest skonfigurowany dla klastra, który korzysta z konta usługi Azure Storage. Aby zastosować tę topologię z klastrem Storm używającym usługi Data Lake Storage, zmień tę wartość z `wasb` na `adl`.

4. Zapisz plik `dev.properties`, a następnie wykonaj następujące polecenie w celu przekazania go do klastra **Storm**:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Zastąp ciąg **USERNAME** nazwą użytkownika protokołu SSH dla klastra. Zastąp ciąg **BASENAME** nazwą podstawową użytą podczas tworzenia klastra.

## <a name="create-the-kafka-topic"></a>Tworzenie tematu platformy Kafka

Platforma Kafka przechowuje dane w _temacie_. Musisz utworzyć temat przed uruchomieniem topologii systemu Storm. Aby utworzyć topologię, wykonaj następujące kroki:

1. Połącz się z klastrem __Kafka__ za pośrednictwem protokołu SSH, używając następującego polecenia. Zastąp ciąg `sshuser` nazwą użytkownika protokołu SSH użytą podczas tworzenia klastra. Zastąp ciąg `kafkaclustername` nazwą klastra Kafka:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Po wyświetleniu monitu podaj hasło, którego użyto podczas tworzenia klastrów.
   
    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utwórz temat platformy Kafka za pomocą następującego polecenia. Zastąp ciąg `$KAFKAZKHOSTS` informacją o hoście usługi Zookeeper użytym podczas konfigurowania topologii:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie powoduje nawiązanie połączenia z usługą Zookeeper klastra Kafka i utworzenie nowego tematu o nazwie `stormtopic`. Ten temat jest używany przez topologie systemu Storm.

## <a name="start-the-writer"></a>Uruchamianie modułu zapisującego

1. Korzystając z następującego polecenia nawiąż połączenie z klastrem **Storm** przy użyciu protokołu SSH. Zastąp ciąg `sshuser` nazwą użytkownika protokołu SSH użytą podczas tworzenia klastra. Zastąp ciąg `stormclustername` nazwą klastra Storm:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Po wyświetleniu monitu podaj hasło, którego użyto podczas tworzenia klastrów.
   
    Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Z poziomu połączenia SSH z klastrem Storm uruchom topologię modułu zapisującego za pomocą następującego polecenia:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    W tym poleceniu są używane następujące parametry:

    * `org.apache.storm.flux.Flux`: skonfiguruj i uruchom tę topologię za pomocą struktury Flux.

    * `--remote`: prześlij topologię do węzła Nimbus. Topologia jest dystrybuowana do węzłów procesu roboczego w klastrze.

    * `-R /writer.yaml`: skonfiguruj topologię za pomocą pliku `writer.yaml`. Parametr `-R` wskazuje, że ten zasób jest dołączany do pliku jar. Znajduje się on w katalogu głównym pliku jar, więc jego ścieżka to `/writer.yaml`.

    * `--filter`: wypełnij wpisy w pliku `writer.yaml` topologii za pomocą wartości z pliku `dev.properties`. Na przykład wartość wpisu `kafka.topic` w pliku jest używana do zastąpienia wpisu `${kafka.topic}` w definicji topologii.

## <a name="start-the-reader"></a>Uruchamianie modułu czytającego

1. Z poziomu sesji SSH z klastrem Storm uruchom topologię modułu czytającego za pomocą następującego polecenia:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Zaczekaj minutę, a następnie przy użyciu następującego polecenia wyświetl pliki utworzone przez topologię modułu czytającego:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```output
    Found 173 items
      -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
      -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
      -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
      ...
    ```

3. Wyświetl zawartość pliku za pomocą następującego polecenia. Zastąp ciąg `filename.txt` nazwą pliku:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Następujący tekst to przykładowa zawartość pliku:

    > cztery wyniki i siedem lat temu
    >
    > biały śnieg i siedem Dwarfs
    >
    > Jestem z natury
    >
    > biały śnieg i siedem Dwarfs
    >
    > Jestem z natury
    >
    > cztery wyniki i siedem lat temu
    >
    > Firma Apple Day utrzymuje lekarza

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

Z poziomu sesji SSH z klastrem Storm zatrzymaj topologie systemu Storm za pomocą następujących poleceń:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób użycia topologii systemu [Apache Storm](https://storm.apache.org/) do zapisywania i odczytywania danych na platformie [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight. Pokazano również, jak zapisać dane w magazynie zgodnym z systemem plików [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) używanym przez usługę HDInsight.

> [!div class="nextstepaction"]
> [Używanie interfejsu API producentów i konsumentów platformy Apache Kafka](kafka/apache-kafka-producer-consumer-api.md)
