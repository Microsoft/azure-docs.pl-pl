---
title: Tworzenie MapReduce języka Java dla Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać platformy Apache Maven do tworzenia aplikacji MapReduce opartej na języku Java, a następnie uruchamiać ją z usługą Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 84d9253b865ddac6d97395af3d8632e29cc2ea24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323812"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight

Dowiedz się, w jaki sposób używać platformy Apache Maven do tworzenia aplikacji MapReduce opartej na języku Java, a następnie uruchamiać ją z Apache Hadoop w usłudze Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* [Java developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

## <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

Środowisko używane na potrzeby tego artykułu było komputerem z systemem Windows 10. Polecenia zostały wykonane w wierszu polecenia, a różne pliki były edytowane w Notatniku. Zmodyfikuj odpowiednio dla danego środowiska.

W wierszu polecenia wprowadź poniższe polecenia, aby utworzyć środowisko robocze:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

1. Wprowadź następujące polecenie, aby utworzyć projekt Maven o nazwie **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    To polecenie tworzy katalog o nazwie określonej przez `artifactID` parametr (**wordcountjava** w tym przykładzie). Ten katalog zawiera następujące elementy:

    * `pom.xml` — [Model obiektów projektu (pliku POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) , który zawiera informacje i szczegóły konfiguracji używane do kompilowania projektu.
    * src\main\java\org\apache\hadoop\examples: zawiera kod aplikacji.
    * src\test\java\org\apache\hadoop\examples: zawiera testy dla aplikacji.

1. Usuń wygenerowany przykładowy kod. Usuń wygenerowane pliki testowe i aplikacje `AppTest.java` , a następnie `App.java` Wprowadź poniższe polecenia:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualizowanie modelu obiektów projektu

Pełne odwołanie do pliku pom.xml można znaleźć w temacie https://maven.apache.org/pom.html . Otwórz `pom.xml` , wprowadzając następujące polecenie:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Dodaj zależności

W programie `pom.xml` Dodaj następujący tekst do `<dependencies>` sekcji:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Definiuje wymagane biblioteki (wymienione w &lt; artifactId \> ) z określoną wersją (wymienioną w ramach &lt; wersji \> ). W czasie kompilacji te zależności są pobierane z domyślnego repozytorium Maven. Aby wyświetlić więcej, możesz użyć [wyszukiwania w repozytorium Maven](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) .

`<scope>provided</scope>`Informuje o tym, że te zależności nie powinny być pakowane z aplikacją, ponieważ są one dostarczane przez klaster usługi HDInsight w czasie wykonywania.

> [!IMPORTANT]
> Używana wersja powinna być zgodna z wersją usługi Hadoop znajdującą się w klastrze. Aby uzyskać więcej informacji na temat wersji, zobacz dokument dotyczący [wersji składnika usługi HDInsight](../hdinsight-component-versioning.md) .

### <a name="build-configuration"></a>Konfiguracja kompilacji

Wtyczki Maven umożliwiają dostosowanie etapów kompilacji projektu. Ta sekcja służy do dodawania dodatków plug-in, zasobów i innych opcji konfiguracji kompilacji.

Dodaj następujący kod do `pom.xml` pliku, a następnie Zapisz i zamknij plik. Ten tekst musi znajdować się wewnątrz `<project>...</project>` tagów w pliku, na przykład między `</dependencies>` i `</project>` .

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

Ta sekcja służy do konfigurowania wtyczki kompilatora Apache Maven i wtyczki programu Apache Maven. Wtyczka kompilatora służy do kompilowania topologii. Wtyczka do cieniowania służy do zapobiegania duplikowaniu licencji w pakiecie JAR utworzonym przez Maven. Ta wtyczka służy do zapobiegania błędowi "zduplikowane pliki licencji" w czasie wykonywania w klastrze usługi HDInsight. Użycie Maven-odcieni-plugin z `ApacheLicenseResourceTransformer` implementacją uniemożliwia błąd.

Maven-odcieni-Wtyczka tworzy również plik JAR Uber, który zawiera wszystkie zależności wymagane przez aplikację.

Zapisz plik `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Tworzenie aplikacji MapReduce

1. Wprowadź poniższe polecenie, aby utworzyć i otworzyć nowy plik `WordCount.java` . Wybierz opcję **tak** w wierszu polecenia, aby utworzyć nowy plik.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Następnie skopiuj i wklej poniższy kod Java do nowego pliku. Następnie zamknij plik.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    Zwróć uwagę, że nazwa pakietu to `org.apache.hadoop.examples` i nazwa klasy `WordCount` . Te nazwy są używane podczas przesyłania zadania MapReduce.

## <a name="build-and-package-the-application"></a>Kompilowanie i pakowanie aplikacji

W `wordcountjava` katalogu Użyj następującego polecenia, aby skompilować plik JAR zawierający aplikację:

```cmd
mvn clean package
```

To polecenie czyści wszystkie poprzednie artefakty kompilacji, pobiera wszystkie zależności, które nie zostały jeszcze zainstalowane, a następnie kompiluje i pakuje aplikację.

Po zakończeniu wykonywania polecenia `wordcountjava/target` katalog zawiera plik o nazwie `wordcountjava-1.0-SNAPSHOT.jar` .

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar`Plik to uberjar, który zawiera nie tylko zadanie WORDCOUNT, ale również zależności wymagane przez zadanie w czasie wykonywania.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Przekaż zadania JAR i Run (SSH)

Poniższe kroki służą `scp` do kopiowania jar do podstawowego węzła głównego platformy Apache HBase w klastrze usługi HDInsight. `ssh`Polecenie jest następnie używane do nawiązania połączenia z klastrem i uruchomienia przykładu bezpośrednio w węźle głównym.

1. Przekaż plik jar do klastra. Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Nawiąż połączenie z klastrem. Zastąp `CLUSTERNAME` ciąg nazwą klastra usługi HDInsight, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH Użyj następującego polecenia, aby uruchomić aplikację MapReduce:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    To polecenie uruchamia aplikację WordCount MapReduce. Plik wejściowy jest `/example/data/gutenberg/davinci.txt` i katalog wyjściowy `/example/data/wordcountout` . Plik wejściowy i dane wyjściowe są przechowywane w domyślnym magazynie klastra.

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić wyniki:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Powinna zostać wyświetlona lista słów i liczby, z wartościami podobnymi do następującego tekstu:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób tworzenia zadania MapReduce języka Java. Zapoznaj się z następującymi dokumentami, aby poznać inne sposoby pracy z usługą HDInsight.

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
* [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/)
