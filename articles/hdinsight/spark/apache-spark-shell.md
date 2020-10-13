---
title: Używanie interaktywnej powłoki Spark w usłudze Azure HDInsight
description: Interaktywna powłoka Spark udostępnia proces odczytu-Execute-Print służący do uruchamiania poleceń platformy Spark po jednym naraz i wyświetlania wyników.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: 84298c9073f00f0388a9bcb7405369d7c60bcce1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081183"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Uruchamianie Apache Spark z powłoki Spark

Interaktywna powłoka [Apache Spark](https://spark.apache.org/) udostępnia środowisko REPL (Read-Execute-Print) do uruchamiania poleceń platformy Spark po jednym naraz i oglądania wyników. Ten proces jest przydatny do programowania i debugowania. Platforma Spark udostępnia jedną powłokę dla każdego z obsługiwanych języków: Scala, Python i R.

## <a name="run-an-apache-spark-shell"></a>Uruchamianie powłoki Apache Spark

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Platforma Spark udostępnia powłoki dla Scala (Spark-Shell) i Python (pyspark). W sesji SSH wprowadź *jedno* z następujących poleceń:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Jeśli zamierzasz użyć dowolnej opcjonalnej konfiguracji, upewnij się, że najpierw zapoznaj się z [Apache Spark wyjątkiem OutOfMemoryError](./apache-spark-troubleshoot-outofmemory.md).

1. Kilka podstawowych poleceń przykładowych. Wybierz odpowiedni język:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Zbadaj plik CSV. Zwróć uwagę, że język poniżej działa dla `spark-shell` i `pyspark` .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Zbadaj plik CSV i Zapisz wyniki w zmiennej:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Wyświetl wyniki:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Zakończ

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Wystąpienia SparkSession i SparkContext

Domyślnie po uruchomieniu powłoki Spark wystąpienia SparkSession i SparkContext są automatycznie tworzone dla Ciebie.

Aby uzyskać dostęp do wystąpienia SparkSession, wprowadź `spark` . Aby uzyskać dostęp do wystąpienia SparkContext, wprowadź `sc` .

## <a name="important-shell-parameters"></a>Ważne parametry powłoki

Polecenie powłoki Spark ( `spark-shell` lub `pyspark` ) obsługuje wiele parametrów wiersza polecenia. Aby wyświetlić pełną listę parametrów, Uruchom powłokę Spark za pomocą przełącznika `--help` . Niektóre z tych parametrów mogą dotyczyć tylko tych `spark-submit` , które są zawijane przez powłokę Spark.

| switch | description | przykład |
| --- | --- | --- |
| --MASTER_URL wzorca | Określa główny adres URL. W usłudze HDInsight ta wartość jest zawsze `yarn` . | `--master yarn`|
| --Jars JAR_LIST | Rozdzielana przecinkami lista Jars lokalnego do uwzględnienia w sterownikach i ścieżkach klas wykonywania. W usłudze HDInsight ta lista zawiera ścieżki do domyślnego systemu plików w usłudze Azure Storage lub Data Lake Storage. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS pakietów | Rozdzielana przecinkami lista współrzędnych Maven Jars do uwzględnienia w sterownikach i ścieżkach klas wykonywania. Przeszukuje lokalne repozytorium Maven, a następnie Maven Central, wszelkie dodatkowe repozytoria zdalne określone za pomocą `--repositories` . Format współrzędnych to *GroupID*:*artifactId*:*Version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --Lista z LISTą plików | Tylko w przypadku języka Python, rozdzielana przecinkami lista plików zip, jaja lub. PR do umieszczenia w PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [wprowadzenie do Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md) .
- Zobacz [Tworzenie klastra Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md) , aby współpracował z klastrami Spark i SparkSQL.
- Zobacz, [co to jest Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) , aby napisać aplikacje, które przetwarzają dane przesyłane strumieniowo za pomocą platformy Spark.
