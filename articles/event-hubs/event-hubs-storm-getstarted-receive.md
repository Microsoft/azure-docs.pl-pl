---
title: 'Szybki Start: otrzymywanie zdarzeń przy użyciu Apache Storm — Azure Event Hubs'
description: 'Szybki Start: Ten artykuł zawiera informacje na temat odbierania zdarzeń z platformy Azure Event Hubs przy użyciu Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 099a3ff1ad57616de0d7929d787b82e9d55c9e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87087581"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Szybki Start: otrzymywanie zdarzeń z Event Hubs przy użyciu Apache Storm

[Apache Storm](https://storm.incubator.apache.org) to rozproszony system obliczeniowy w czasie rzeczywistym, który upraszcza niezawodne przetwarzanie strumieni niezwiązanych z danymi. W tej sekcji pokazano, jak za pomocą usługi Azure Event Hubs burzy elementu Spout do odbierania zdarzeń z Event Hubs. Za pomocą Apache Storm można podzielić zdarzenia na wiele procesów hostowanych w różnych węzłach. Integracja Event Hubs z burzą pozwala uprościć użycie zdarzeń przez niewidocznym do użycia punkt kontrolny w trakcie jego postępu przy użyciu instalacji dozorcy burzy, zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami z Event Hubs.

Aby uzyskać więcej informacji o wzorcach otrzymywania Event Hubs, zobacz [omówienie Event Hubs][Event Hubs overview].

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z przewodnikiem Szybki Start **utwórz Event Hubs przestrzeń nazw i centrum zdarzeń**. Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć przestrzeń nazw typu Event Hubs i uzyskać poświadczenia zarządzania wymagane przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Utwórz projekt i Dodaj kod

Ten samouczek używa instalacji [burzy usługi HDInsight][HDInsight Storm] , która zawiera Event Hubs elementu Spout jest już dostępny.

1. Postępuj zgodnie z procedurą usługi [HDInsight —](../hdinsight/storm/apache-storm-overview.md) wprowadzenie, aby utworzyć nowy klaster usługi HDInsight i połączyć się z nim za pośrednictwem pulpit zdalny.
2. Skopiuj `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` plik do lokalnego środowiska deweloperskiego. Zawiera zdarzenia-burza-elementu Spout.
3. Użyj poniższego polecenia, aby zainstalować pakiet w lokalnym magazynie Maven. Umożliwia to dodanie go jako odwołania w projekcie burzy w późniejszym kroku.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. W oknie przezaćmienie Utwórz nowy projekt Maven (kliknij kolejno pozycje **plik**, **Nowy**, **projekt**).
   
    ![Plik — nowy > projekt >][12]
5. Wybierz opcję **Użyj domyślnej lokalizacji obszaru roboczego**, a następnie kliknij przycisk **dalej** .
6. Wybierz pozycję **Maven-Archetype-Start** Archetype, a następnie kliknij przycisk **dalej** .
7. Wstaw identyfikator **GroupID** i **ArtifactId**, a następnie kliknij przycisk **Zakończ** .
8. W **pom.xml** Dodaj następujące zależności w `<dependency>` węźle.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. W folderze **src** Utwórz plik o nazwie **config. Properties** i skopiuj poniższą zawartość, zastępując `receive rule key` `event hub name` wartości i:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Wartość elementu **eventhub. Receiver. kredyty** określa, ile zdarzeń jest wsadowych przed ich zwolnieniem do potoku burzy. Dla uproszczenia ten przykład ustawia tę wartość na 10. W środowisku produkcyjnym zwykle powinna być ustawiona na wyższe wartości. na przykład 1024.
10. Utwórz nową klasę o nazwie **LoggerBolt** przy użyciu następującego kodu:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Ten piorun służy do rejestrowania zawartości odebranych zdarzeń. Można to łatwo rozszerzyć w celu przechowywania krotek w usłudze Storage. [Przykładowa burza w usłudze HDInsight z centrum zdarzeń] używa tego samego podejścia do przechowywania danych w usłudze Azure Storage i Power BI.
11. Utwórz klasę o nazwie **LogTopology** przy użyciu następującego kodu:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Ta klasa tworzy nowe Event Hubs elementu Spout przy użyciu właściwości w pliku konfiguracji w celu utworzenia wystąpienia. Należy pamiętać, że ten przykład tworzy tyle zadań elementy Spout jako liczbę partycji w centrum zdarzeń, aby można było korzystać z maksymalnej liczby równoległości dozwolonej przez centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Przykładowa burza usługi HDInsight z centrum zdarzeń]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
