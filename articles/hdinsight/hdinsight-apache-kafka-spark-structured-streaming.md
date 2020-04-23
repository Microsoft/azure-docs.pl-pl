---
title: 'Samouczek: Apache Spark Streaming & Apache Kafka - Azure HDInsight'
description: Dowiedz się, jak wymieniać dane z platformą Apache Kafka za pomocą przesyłania strumieniowego platformy Apache Spark. Ten samouczek obejmuje przesyłanie strumieniowe danych z platformy Spark w usłudze HDInsight za pomocą notesu Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18
ms.date: 04/22/2020
ms.openlocfilehash: 5fa25f54faecbc7caf130ffeb0d24c3d8fef7e09
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084808"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Samouczek: używanie strumieni ze strukturą platformy Apache Spark z platformą Kafka w usłudze HDInsight

W tym samouczku przedstawiono sposób użycia [przesyłania strumieniowego platformy Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) do odczytywania i zapisywania danych przy użyciu platformy [Apache Kafka](./kafka/apache-kafka-introduction.md) w usłudze Azure HDInsight.

Spark Structured Streaming to aparat przetwarzania strumienia zbudowany na platformie Spark SQL. Aparat ten umożliwia wyrażanie obliczeń strumieniowych tak samo jak obliczeń wsadowych na danych statycznych.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastrów za pomocą szablonu usługi Azure Resource Manager
> * Użyj strumieniowania strukturalnego Spark z platformą Kafka

Po wykonaniu czynności w tym dokumencie należy pamiętać o usunięciu klastrów, aby uniknąć nadmiernych opłat.

## <a name="prerequisites"></a>Wymagania wstępne

* jq, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Znajomość zagadnień dotyczących używania [notesów Jupyter](https://jupyter.org/) za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz dokument [Ładowanie danych i uruchamianie zapytań za pomocą platformy Apache Spark w usłudze HDInsight](spark/apache-spark-load-data-run-query.md).

* Znajomość języka programowania Scala. Kod używany w tym samouczku jest napisany w języku Scala.

* Znajomość zagadnień dotyczących tworzenia tematów platformy Kafka. Aby uzyskać więcej informacji, zobacz dokument [Przewodnik Szybki start dla platformy Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Kroki przedstawione w tym dokumencie wymagają grupy zasobów platformy Azure, która zawiera zarówno platformę Spark w usłudze HDInsight, jak i platformę Kafka w klastrze usługi HDInsight. Oba klastry znajdują się w usłudze Azure Virtual Network, dzięki czemu klaster Spark może komunikować się bezpośrednio z klastrem Kafka.
>
> Dla Twojej wygody w tym dokumencie umieszczono link do szablonu, który umożliwia utworzenie wszystkich wymaganych zasobów platformy Azure.
>
> Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zobacz Planowanie sieci wirtualnej dla dokumentu [HDInsight.](hdinsight-plan-virtual-network-deployment.md)

## <a name="structured-streaming-with-apache-kafka"></a>Przesyłanie strumieniowe ze strukturą na platformie Apache Kafka

Przesyłanie strumieniowe ze strukturą platformy Spark to aparat przetwarzania strumieni oparty na aparacie SQL platformy Spark. Podczas korzystania z usługi przesyłania strumieniowego strukturalnego, można pisać zapytania przesyłania strumieniowego w taki sam sposób pisania kwerend wsadowych.

Poniższe fragmenty kodu demonstrują odczytywanie z platformy Kafka i zapisywanie do pliku. Pierwszy z nich to operacja wsadowa, a drugi to operacja przesyłania strumieniowego:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

W obu fragmentach dane są odczytywane z platformy Kafka i zapisywane do pliku. Różnice między przykładami:

| Batch | Przesyłanie strumieniowe |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Użycie funkcji przesyłania `awaitTermination(30000)`strumieniowego powoduje również zatrzymanie strumienia po 30 000 ms.

Aby używać przesyłania strumieniowego ze strukturą na platformie Kafka, projekt musi mieć zdefiniowaną zależność od pakietu `org.apache.spark : spark-sql-kafka-0-10_2.11`. Wersja tego pakietu powinna być zgodna z wersją platformy Spark w usłudze HDInsight. W przypadku platformy Spark 2.2.0 (dostępnej w programie HDInsight 3.6) informacje o zależnościach dla różnych typów projektów można znaleźć pod adresem [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

W przypadku notesu Jupyter używanego w tym samouczku następująca komórka ładuje tę zależność pakietu:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Tworzenie klastrów

Apache Kafka na HDInsight nie zapewnia dostępu do brokerów Kafka za pośrednictwem publicznego Internetu. Wszystkie elementy, które używają platformy Kafka, muszą znajdować się w tej samej sieci wirtualnej platformy Azure. W tym samouczku zarówno klaster Kafka, jak i klaster Spark znajdują się w tej samej sieci wirtualnej platformy Azure.

Na poniższym diagramie przedstawiono przepływ komunikacji między platformami Spark i Kafka:

![Diagram przedstawiający klastry Spark i Kafka w sieci wirtualnej platformy Azure](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikacja usługi Kafka jest ograniczona do sieci wirtualnej. Inne usługi w klastrze, takie jak SSH i Ambari, są dostępne przez Internet. Aby uzyskać więcej informacji o publicznych portach dostępnych z usługą HDInsight, zobacz [Ports and URIs used by HDInsight (Porty i identyfikatory URI używane przez usługę HDInsight)](hdinsight-hadoop-port-settings-for-services.md).

Aby utworzyć usługę Azure Virtual Network, a następnie utworzyć w niej klastry Kafka i Spark, wykonaj następujące kroki:

1. Kliknij poniższy przycisk, aby zalogować się do platformy Azure i otworzyć szablon w witrynie Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Szablon usługi Azure Resource **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**Manager znajduje się pod adresem .

    Ten szablon umożliwia utworzenie następujących zasobów:

   * Klaster Kafka w usłudze HDInsight 3.6.
   * Klaster Spark 2.2.0 w usłudze HDInsight 3.6.
   * Sieć wirtualna platformy Azure zawierająca klastry usługi HDInsight.

     > [!IMPORTANT]  
     > Używany w tym samouczku notes do przesyłania strumieniowego ze strukturą wymaga platformy Spark 2.2.0 w usłudze HDInsight 3.6. W przypadku użycia starszej wersji platformy Spark w usłudze HDInsight podczas korzystania z notesu wystąpią błędy.

2. Wypełnij pola w sekcji **Dostosowany szablon**, korzystając z poniższych informacji:

    | Ustawienie | Wartość |
    | --- | --- |
    | Subskrypcja | Twoja subskrypcja platformy Azure |
    | Grupa zasobów | Grupa zasobów zawierająca zasoby. |
    | Lokalizacja | Region świadczenia usługi Azure, w którym są tworzone zasoby. |
    | Nazwa klastra Spark | Nazwa klastra Spark. Pierwszych sześć znaków musi być innych niż nazwa klastra Kafka. |
    | Nazwa klastra Kafka | Nazwa klastra Kafka. Pierwszych sześć znaków musi być innych niż nazwa klastra Spark. |
    | Nazwa użytkownika logowania klastra | Nazwa użytkownika będącego administratorem klastrów. |
    | Hasło logowania klastra | Hasło użytkownika będącego administratorem klastrów. |
    | Nazwa użytkownika SSH | Użytkownik SSH tworzony na potrzeby obsługi klastrów. |
    | Hasło SSH | Hasło użytkownika SSH. |

    ![Zrzut ekranu przedstawiający dostosowany szablon](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Zapoznaj się z **Regulaminem,** a następnie wybierz **zgadzam się z warunkami określonymi powyżej.**

4. Wybierz pozycję **Kup**.

> [!NOTE]  
> Tworzenie klastrów może potrwać do 20 minut.

## <a name="use-spark-structured-streaming"></a>Użyj strumieniowania strukturalnego Spark

W tym przykładzie pokazano, jak używać usługi Spark Structured Streaming z platformą Kafka w programie HDInsight. Wykorzystuje dane na temat podróży taksówką, która jest dostarczana przez Nowy Jork.  Zestaw danych używany przez ten notebook pochodzi z [2016 Green Taxi Trip Data](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Zbieranie informacji o hoście. Użyj zwijanie i [jq](https://stedolan.github.io/jq/) polecenia poniżej, aby uzyskać Kafka ZooKeeper i broker hostów informacji. Polecenia są przeznaczone dla wiersza polecenia systemu Windows, niewielkie różnice będą potrzebne dla innych środowisk. Zastąp `KafkaCluster` nazwą klastra platformy `KafkaPassword` Kafka i hasłem logowania klastra. Ponadto, `C:\HDI\jq-win64.exe` zastąpić rzeczywistą ścieżkę do instalacji jq. Wprowadź polecenia w wierszu polecenia systemu Windows i zapisz dane wyjściowe do użycia w późniejszych krokach.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. W przeglądarce internetowej `https://CLUSTERNAME.azurehdinsight.net/jupyter`przejdź `CLUSTERNAME` do miejsca , gdzie jest nazwa klastra. Po wyświetleniu monitu wprowadź nazwę użytkownika klastra (administratora) i hasło użyte podczas tworzenia klastra.

1. Wybierz **pozycję Nowy > Spark,** aby utworzyć notes.

1. Przesyłanie strumieniowe platformy Spark ma mikrobatching, co oznacza, że dane są wykonywane jako partie i executery uruchamiane na partiach danych. Jeśli wykonawca ma limit czasu bezczynnego mniej niż czas potrzebny do przetworzenia partii, a następnie wykonawców będzie stale dodawane i usuwane. Jeśli wykonawca bezczynny limit czasu jest większy niż czas trwania partii, wykonawca nigdy nie zostanie usunięty. Dlatego **zaleca się wyłączenie alokacji dynamicznej przez ustawienie spark.dynamicAllocation.enabled false podczas uruchamiania aplikacji strumieniowych.**

    Ładowanie pakietów używanych przez notes, wprowadzając następujące informacje w komórce notesu. Uruchom polecenie za pomocą **klawiszy CTRL + ENTER**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Utwórz temat Kafka. Edytuj poniższe polecenie, zastępując `YOUR_ZOOKEEPER_HOSTS` informacjami o hoście Zookeeper wyodrębnione w pierwszym kroku. Wprowadź edytowane polecenie w notesie Jupyter, `tripdata` aby utworzyć temat.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Pobierz dane dotyczące przejazdów taksówką. Wprowadź polecenie w następnej komórce, aby załadować dane na przejazdach taksówką w Nowym Jorku. Dane są ładowane do ramki danych, a następnie dataframe jest wyświetlany jako dane wyjściowe komórki.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Ustaw informacje o hostach brokera platformy Kafka. Zamień `YOUR_KAFKA_BROKER_HOSTS` na brokera hosty informacji wyodrębnionych w kroku 1.  Wprowadź edytowane polecenie w następnej komórce Notesu Jupytera.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Wyślij dane do platformy Kafka. W poniższym poleceniu `vendorid` pole jest używane jako wartość klucza dla wiadomości platformy Kafka. Klucz jest używany przez platformę Kafka podczas partycjonowania danych. Wszystkie pola są przechowywane w wiadomości platformy Kafka jako wartość ciągu JSON. Wprowadź następujące polecenie w programie Jupyter, aby zapisać dane w programie Kafka za pomocą kwerendy wsadowej.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Deklaruj schemat. Następujące polecenie pokazuje, jak używać schematu podczas odczytywania danych JSON z platformy Kafka. Wprowadź polecenie w następnej komórce Jupytera.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Wybierz dane i uruchom strumień. Poniższe polecenie pokazuje, jak pobrać dane z platformy Kafka przy użyciu kwerendy wsadowej. A następnie zapisz wyniki do hdfs w klastrze Platformy Spark. W tym przykładzie `select` pobiera komunikat (pole wartości) z platformy Kafka i stosuje do niego schemat. Dane są następnie zapisywane w formacie HDFS (WASB lub ADL) w formacie parkietu. Wprowadź polecenie w następnej komórce Jupytera.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Możesz sprawdzić, czy pliki zostały utworzone, wprowadzając polecenie w następnej komórce Jupytera. Wyświetla listę plików `/example/batchtripdata` w katalogu.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Podczas gdy w poprzednim przykładzie użyto kwerendy wsadowej, następujące polecenie pokazuje, jak zrobić to samo przy użyciu kwerendy przesyłania strumieniowego. Wprowadź polecenie w następnej komórce Jupytera.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Uruchom następującą komórkę, aby sprawdzić, czy pliki zostały zapisane przez kwerendę przesyłania strumieniowego.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra HDInsight. I wszelkie inne zasoby skojarzone z grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W [witrynie Azure portal](https://portal.azure.com/)rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów,__ aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

> [!WARNING]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć.
>
> Usunięcie platformy Kafka w klastrze usługi HDInsight powoduje usunięcie wszystkich danych przechowywanych na platformie Kafka.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak używać apache Spark Structured Streaming. Aby napisać i odczytać dane z Apache Kafka na HDInsight. Korzystając z poniższego linku, możesz dowiedzieć się, jak używać systemu Apache Storm z platformą Kafka.

> [!div class="nextstepaction"]
> [Używanie systemu Apache Storm z platformą Apache Kafka](hdinsight-apache-storm-with-kafka.md)
