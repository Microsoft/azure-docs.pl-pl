---
title: Apache Storm przykład topologii języka Java — Azure HDInsight
description: Dowiedz się, jak utworzyć topologię Apache Storm w języku Java, tworząc przykładową topologię zliczania wyrazów.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 881ec4aa36261958b566dc2d7c4d06475a76bad4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545501"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Tworzenie topologii Apache Storm w języku Java

Dowiedz się, jak utworzyć topologię opartą na języku Java dla Apache Storm. Tworzysz topologię burzy, która implementuje aplikację Word-Count. Do kompilowania i pakowania projektu służy program Apache Maven. Następnie dowiesz się, jak zdefiniować topologię przy użyciu platformy Apache Storm strumienia.

Po wykonaniu kroków opisanych w tym dokumencie można wdrożyć topologię w celu Apache Storm w usłudze HDInsight.

> [!NOTE]  
> Kompletna wersja przykładów topologii burzy utworzonych w tym dokumencie jest dostępna pod adresem [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Wymagania wstępne

* [Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

## <a name="test-environment"></a>Środowisko testowe

Środowisko używane na potrzeby tego artykułu było komputerem z systemem Windows 10.  Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane w Notatniku.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko robocze:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **WORDCOUNT** :

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

To polecenie tworzy katalog o nazwie `WordCount` w bieżącej lokalizacji, który zawiera podstawowy projekt Maven. Drugie polecenie zmienia bieżący katalog roboczy na `WordCount` . Trzecie polecenie tworzy nowy katalog, `resources` który będzie później używany.  `WordCount`Katalog zawiera następujące elementy:

* `pom.xml`: Zawiera ustawienia dla projektu Maven.
* `src\main\java\com\microsoft\example`: Zawiera kod aplikacji.
* `src\test\java\com\microsoft\example`: Zawiera testy dla aplikacji.  

### <a name="remove-the-generated-example-code"></a>Usuwanie wygenerowanego przykładowego kodu

Usuń wygenerowane pliki testowe i aplikacje `AppTest.java` , a następnie `App.java` Wprowadź poniższe polecenia:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Dodawanie repozytoriów Maven

Usługa HDInsight jest oparta na platformie Hortonworks Data Platform (HDP), dlatego zalecamy używanie repozytorium Hortonworks do pobierania zależności dla projektów Apache Storm.  

Otwórz `pom.xml` , wprowadzając następujące polecenie:

```cmd
notepad pom.xml
```

Następnie Dodaj następujący kod XML po `<url>https://maven.apache.org</url>` wierszu:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Dodawanie właściwości

Maven umożliwia definiowanie wartości na poziomie projektu o nazwie Properties. W programie `pom.xml` Dodaj następujący tekst po `</repositories>` wierszu:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Możesz teraz używać tej wartości w innych sekcjach `pom.xml` . Na przykład podczas określania wersji składników burzy można użyć `${storm.version}` zamiast twardej kodowania wartości.

## <a name="add-dependencies"></a>Dodaj zależności

Dodaj zależność dla składników burzy. W programie `pom.xml` Dodaj następujący tekst do `<dependencies>` sekcji:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

W czasie kompilacji Maven używa tych informacji do wyszukania `storm-core` w repozytorium Maven. Najpierw szuka repozytorium na komputerze lokalnym. Jeśli pliki nie znajdują się tam, Maven pobiera je z publicznego repozytorium Maven i zapisuje je w repozytorium lokalnym.

> [!NOTE]  
> Zwróć uwagę na `<scope>provided</scope>` wiersz w tej sekcji. To ustawienie instruuje Maven, aby wykluczać z tworzonych **przez siebie plików** jar, ponieważ są one dostarczane przez system.

## <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów kompilacji projektu. Na przykład sposób kompilowania projektu lub sposobu pakowania go do pliku JAR. W programie `pom.xml` Dodaj następujący tekst bezpośrednio nad `</project>` wierszem.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ta sekcja służy do dodawania dodatków plug-in, zasobów i innych opcji konfiguracji kompilacji. Aby uzyskać pełne odwołanie do `pom.xml` pliku, zobacz [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Dodaj wtyczki

* **Wtyczka exec Maven**

    W przypadku topologii Apache Storm wdrożonej w języku Java [wtyczka exec Maven](https://www.mojohaus.org/exec-maven-plugin/) jest przydatna, ponieważ umożliwia ona łatwe uruchamianie topologii lokalnie w środowisku deweloperskim. Dodaj następujące elementy do `<plugins>` sekcji `pom.xml` pliku, aby uwzględnić wtyczkę exec Maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Wtyczka kompilatora Apache Maven**

    Innym przydatnym dodatkiem plug-in jest [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , który służy do zmiany opcji kompilacji. Zmień wersję języka Java używaną przez Maven dla źródła i celu dla aplikacji.

  * W przypadku usługi HDInsight __3,4 lub starszej__ Ustaw źródłową i docelową wersję języka Java na __1,7__ .

  * W przypadku usługi HDInsight __3,5__ Ustaw źródłową i docelową wersję języka Java na __1,8__ .

  Dodaj następujący tekst do `<plugins>` sekcji `pom.xml` pliku, aby dołączyć wtyczkę kompilatora Apache Maven. W tym przykładzie określono 1,8, więc docelowa wersja usługi HDInsight to 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Konfigurowanie zasobów

Sekcja Resources umożliwia uwzględnienie zasobów niezwiązanych z kodem, takich jak pliki konfiguracji potrzebne przez składniki w topologii. W tym przykładzie Dodaj następujący tekst w `<resources>` sekcji `pom.xml` pliku. Następnie zapisz i zamknij plik.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Ten przykład dodaje katalog zasobów w katalogu głównym projektu ( `${basedir}` ) jako lokalizację, która zawiera zasoby, i zawiera plik o nazwie `log4j2.xml` . Ten plik służy do konfigurowania informacji, które są rejestrowane przez topologię.

## <a name="create-the-topology"></a>Tworzenie topologii

Topologia Apache Storm oparta na języku Java składa się z trzech składników, które należy utworzyć (lub jako odwołanie) jako zależność.

* **Elementy Spout** : odczytuje dane ze źródeł zewnętrznych i emituje strumienie danych do topologii.

* **Pioruny** : przetwarza strumienie emitowane przez elementy Spout lub inne pioruny i emituje jeden lub więcej strumieni.

* **Topologia** : definiuje, jak elementy Spout i pioruny są rozmieszczone i udostępnia punkt wejścia dla topologii.

### <a name="create-the-spout"></a>Tworzenie elementu Spout

Aby zmniejszyć wymagania dotyczące konfigurowania zewnętrznych źródeł danych, następujące elementu Spout po prostu emituje losowe zdania. Jest to zmodyfikowana wersja elementu Spout, która jest dostarczana z [przykładami burzy](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Mimo że Ta topologia korzysta z jednej elementu Spout, inne mogą mieć kilka źródeł danych pochodzących z różnych źródła do topologii`.`

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku.  Następnie zamknij plik.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Przykład elementu Spout, który odczytuje z zewnętrznego źródła danych, można znaleźć w jednym z następujących przykładów:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): przykład elementu Spout, który odczytuje z serwisu Twitter.
> * [Burza-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): elementu Spout, który odczytuje z Kafka.

### <a name="create-the-bolts"></a>Tworzenie piorunów

Pioruny obsługują przetwarzanie danych. Pioruny mogą wykonywać dowolne czynności, na przykład obliczenia, trwałość lub rozmowy z zewnętrznymi składnikami. Ta topologia używa dwóch piorunów:

* **SplitSentence** : dzieli zdania emitowane przez **RandomSentenceSpout** na poszczególne słowa.

* **WORDCOUNT** : zlicza liczbę przypadków wystąpienia każdego wyrazu.

#### <a name="splitsentence"></a>SplitSentence

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku.  Następnie zamknij plik.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku.  Następnie zamknij plik.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definiowanie topologii

Topologia wiąże się z elementy Spout i piorunami do grafu. Wykres definiuje sposób przepływu danych między składnikami. Zawiera również wskazówki równoległości, które są używane podczas tworzenia wystąpień składników w klastrze.

Na poniższej ilustracji przedstawiono Podstawowy diagram grafu składników dla tej topologii.

![Diagram przedstawiający układ elementy Spout i piorunów](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Aby zaimplementować topologię, wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Następnie skopiuj i wklej poniższy kod Java do nowego pliku.  Następnie zamknij plik.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurowanie rejestrowania

W przypadku korzystania z oprogramowania [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) do rejestrowania informacji. Jeśli rejestrowanie nie zostanie skonfigurowane, topologia emituje informacje diagnostyczne. Aby kontrolować to, co jest rejestrowane, Utwórz plik o nazwie `log4j2.xml` w `resources` katalogu, wprowadzając poniższe polecenie:

```cmd
notepad resources\log4j2.xml
```

Następnie skopiuj i wklej poniższy tekst XML do nowego pliku.  Następnie zamknij plik.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Ten plik XML konfiguruje nowy Rejestrator dla `com.microsoft.example` klasy, który obejmuje składniki w tej przykładowej topologii. Poziom jest ustawiany na wartość Trace dla tego rejestratora, który przechwytuje wszystkie informacje o rejestrowaniu emitowane przez składniki w tej topologii.

`<Root level="error">`Sekcja konfiguruje główny poziom rejestrowania (wszystko nie jest w `com.microsoft.example` ), aby rejestrować tylko informacje o błędach.

Aby uzyskać więcej informacji o konfigurowaniu rejestrowania dla Log4J 2, zobacz [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> W wersji 0.10.0 i wyższych użyto Log4J 2. x. Starsze wersje sieci log4j 1. x, które używały innego formatu do konfiguracji dziennika. Aby uzyskać informacje na temat starszej konfiguracji, zobacz [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>Lokalne testowanie topologii

Po zapisaniu plików użyj następującego polecenia, aby przetestować topologię lokalnie.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Po uruchomieniu topologia wyświetla informacje uruchamiania. Poniższy tekst to przykład danych wyjściowych zliczania wyrazów:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

Ten przykładowy dziennik wskazuje, że wyrazy "i" zostały wyemitowane 113 razy. Liczba nadal rośnie, o ile zostanie uruchomiona topologia. Ten wzrost wynika z faktu, że elementu Spout ciągle emituje te same zdania.

Występuje 5-sekundowy interwał między emisją wyrazów a liczbą. Składnik **WORDCOUNT** jest skonfigurowany tak, aby emituje informacje tylko po nadejściu krotki. Żądania, że krotki są dostarczane tylko co pięć sekund.

## <a name="convert-the-topology-to-flux"></a>Konwertuj topologię na strumień

[Strumień](https://storm.apache.org/releases/2.0.0/flux.html) jest nową strukturą, która jest dostępna z 0.10.0 burzy i wyższym. Strumień pozwala oddzielić konfigurację od implementacji. Składniki są nadal zdefiniowane w języku Java, ale topologia jest definiowana przy użyciu pliku YAML. Można spakować domyślną definicję topologii z projektem lub użyć pliku autonomicznego podczas przesyłania topologii. Podczas przesyłania topologii do burzy należy użyć zmiennych środowiskowych lub plików konfiguracyjnych, aby wypełnić wartości definicji topologii YAML.

Plik YAML definiuje składniki do użycia w topologii i przepływ danych między nimi. Możesz dołączyć plik YAML jako część pliku JAR. Można też użyć zewnętrznego pliku YAML.

Aby uzyskać więcej informacji o strumieniu, zobacz temat [platforma strumieniowa ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Ze względu na [usterkę ( https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) w przypadku burzy 1.0.1 może być konieczne zainstalowanie [środowiska programistycznego burzy](https://storm.apache.org/releases/current/Setting-up-development-environment.html) w celu lokalnego uruchamiania topologii strumieniowej.

1. Wcześniej `WordCountTopology.java` zdefiniowana topologia, ale nie jest wymagana ze strumieniem. Usuń plik za pomocą następującego polecenia:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Następnie skopiuj i wklej poniższy tekst do nowego pliku.  Następnie zamknij plik.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Wprowadź poniższe polecenie, aby otworzyć następujące `pom.xml` poprawki:

    ```cmd
    notepad pom.xml
    ```

   1. Dodaj następującą nową zależność w `<dependencies>` sekcji:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Dodaj do sekcji następującą wtyczkę `<plugins>` . Ta wtyczka obsługuje tworzenie pakietu (plik JAR) dla projektu i stosuje pewne przekształcenia specyficzne dla strumienia podczas tworzenia pakietu.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. W sekcji wtyczka exec Maven przejdź do `<configuration>`  >  `<mainClass>` i Zmień `${storm.topology}` na `org.apache.storm.flux.Flux` . To ustawienie umożliwia strumieniom obsługę uruchamiania topologii lokalnie w środowisku programistycznym.

   1. W `<resources>` sekcji Dodaj następujące polecenie do `<includes>` . Ten kod XML zawiera plik YAML, który definiuje topologię w ramach projektu.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Lokalne testowanie topologii strumieniowej

1. Wprowadź następujące polecenie, aby skompilować i uruchomić topologię strumienia przy użyciu Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Jeśli topologia używa usługi burza 1.0.1, to polecenie kończy się niepowodzeniem. Ten błąd jest spowodowany przez [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Zamiast tego należy [zainstalować burzę w środowisku deweloperskim](https://storm.apache.org/releases/current/Setting-up-development-environment.html) i wykonać następujące czynności:
    >
    > Jeśli masz [zainstalowaną burzę w środowisku deweloperskim](https://storm.apache.org/releases/current/Setting-up-development-environment.html), możesz użyć następujących poleceń:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local`Parametr uruchamia topologię w trybie lokalnym w środowisku deweloperskim. `-R /topology.yaml`Parametr używa `topology.yaml` zasobu pliku z pliku JAR do zdefiniowania topologii.

    Po uruchomieniu topologia wyświetla informacje uruchamiania. Następujący tekst to przykład danych wyjściowych:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Istnieje 10-sekundowe opóźnienie między partiami zarejestrowanych informacji.

2. Utwórz nowy YAML topologii z projektu.

    1. Wprowadź poniższe polecenie, aby otworzyć `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Znajdź następującą sekcję i zmień wartość `10` na `5` . Ta modyfikacja zmienia interwał między emitowaniem partii wyrazów z 10 sekund do 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Zapisz plik jako `newtopology.yaml` .

3. Aby uruchomić topologię, wprowadź następujące polecenie:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Lub, jeśli masz burzę w środowisku deweloperskim:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    To polecenie używa `newtopology.yaml` jako definicji topologii. Ponieważ parametr nie został uwzględniony `compile` , Maven używa wersji projektu skompilowanej w poprzednich krokach.

    Po rozpoczęciu topologii należy zauważyć, że czas między wyemitowanymi partiami zmienił się w celu odzwierciedlenia wartości w `newtopology.yaml` . Pozwala to zobaczyć, że można zmienić konfigurację za pomocą pliku YAML bez konieczności ponownego kompilowania topologii.

Aby uzyskać więcej informacji na temat tych i innych funkcji platformy strumieniowej, zobacz [strumień https://storm.apache.org/releases/current/flux.html) (](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Pomoc

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) jest abstrakcją wysokiego poziomu, która jest dostarczana przez burzę. Obsługuje przetwarzanie stanowe. Główną zaletą programu Trident jest gwarantuje, że każdy komunikat, który przejdzie do topologii, jest przetwarzany tylko raz. Bez korzystania z programu Trident topologia może zagwarantować, że komunikaty są przetwarzane co najmniej raz. Istnieją również inne różnice, takie jak wbudowane składniki, które mogą być używane zamiast tworzenia piorunów. Pioruny są zastępowane przez składniki mniejsze niż ogólne, takie jak filtry, projekcje i funkcje.

Aplikacje Trident można tworzyć za pomocą projektów Maven. Używasz tych samych podstawowych kroków, które przedstawiono wcześniej w tym artykule — tylko kod jest różny. Nie można również używać Trident z platformą strumień.

Aby uzyskać więcej informacji na temat programu Trident, zobacz [Omówienie interfejsu API Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak utworzyć topologię Apache Storm przy użyciu języka Java. Teraz Dowiedz się, jak:

* [Wdrażanie topologii Apache Storm w usłudze HDInsight i zarządzanie nimi](apache-storm-deploy-monitor-topology-linux.md)

* [Opracowywanie topologii przy użyciu język Python](apache-storm-develop-python-topology.md)

Więcej przykładowych topologii Apache Storm można znaleźć, odwiedzając [przykładowe topologie Apache Storm w usłudze HDInsight](apache-storm-example-topology.md).