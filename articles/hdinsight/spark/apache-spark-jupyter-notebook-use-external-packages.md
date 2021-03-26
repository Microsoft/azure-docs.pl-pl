---
title: Używanie niestandardowych pakietów Maven z Jupyter w usłudze Spark — Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów Jupyter dostępnych z klastrami usługi HDInsight Spark do używania niestandardowych pakietów Maven.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866016"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight

Dowiedz się, jak skonfigurować [Jupyter Notebook](https://jupyter.org/) w klastrze Apache Spark w usłudze HDInsight, aby korzystać z zewnętrznych, współtworzonych przez społeczność pakietów Apache **Maven** , które nie zostały uwzględnione w tym klastrze.

Możesz wyszukać w [repozytorium Maven](https://search.maven.org/) pełną listę dostępnych pakietów. Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Na przykład kompletna lista pakietów z wkładem do społeczności jest dostępna w [pakietach Spark](https://spark-packages.org/).

W tym artykule dowiesz się, jak używać pakietu [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) z Jupyter Notebook.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [ładowanie danych i uruchamianie zapytań za pomocą Apache Spark w usłudze HDInsight](./apache-spark-load-data-run-query.md).

* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. Będzie to możliwe `wasb://` w przypadku usługi Azure Storage, `abfs://` Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. W przypadku włączenia bezpiecznego transferu dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI może być `wasbs://` lub `abfss://` , odpowiednio, zobacz również [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Korzystanie z zewnętrznych pakietów z notesami Jupyter

1. Przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji `CLUSTERNAME` , gdzie jest nazwą klastra Spark.

1. Utwórz nowy notes. Wybierz pozycję **Nowy**, a następnie wybierz pozycję **Spark**.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="Utwórz nowy Jupyter Notebook platformy Spark" border="true":::

1. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Wybierz nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="Wprowadzanie nazwy notesu" border="true":::

1. Użyjemy Magic, `%%configure` Aby skonfigurować Notes do korzystania z pakietu zewnętrznego. W notesach korzystających z pakietów zewnętrznych upewnij się, że `%%configure` w pierwszej komórce kodu jest wywoływana magiczna. Dzięki temu jądro jest skonfigurowane do korzystania z pakietu przed rozpoczęciem sesji.

    >[!IMPORTANT]  
    >Jeśli zapomnisz skonfigurować jądro w pierwszej komórce, możesz użyć `%%configure` z `-f` parametrem, ale to spowoduje ponowne uruchomienie sesji i cały postęp zostanie utracony.

    | Wersja usługi HDInsight | Polecenie |
    |-------------------|---------|
    | W przypadku usługi HDInsight 3,5 i usługi HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |W przypadku usługi HDInsight 3,3 i usługi HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. W powyższym fragmencie kodu oczekuje współrzędne Maven dla pakietu zewnętrznego w repozytorium centralnym Maven. W tym fragmencie kodu `com.databricks:spark-csv_2.11:1.5.0` jest Współrzędna Maven dla pakietu **Spark-CSV** . Poniżej przedstawiono sposób konstruowania współrzędnych pakietu.

    a. Znajdź pakiet w repozytorium Maven. W tym artykule używamy [platformy Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. W repozytorium Zbierz wartości dla **identyfikatora GroupID**, **ArtifactId** i **wersji**. Upewnij się, że zebrane wartości są zgodne z klastrem. W tym przypadku korzystamy z pakietu Scala 2,11 i platformy Spark 1.5.0, ale może być konieczne wybranie różnych wersji dla odpowiedniej wersji Scala lub Spark w klastrze. Wersję Scala można sprawdzić w klastrze, uruchamiając `scala.util.Properties.versionString` je w jądrze platformy Spark Jupyter lub na stronie przesyłania na platformie Spark. Wersję platformy Spark można sprawdzić w klastrze, uruchamiając `sc.version` w notesach Jupyter.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Użyj zewnętrznych pakietów z Jupyter Notebook" border="true":::

    c. Połącz trzy wartości rozdzielone dwukropkiem (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Uruchom komórkę kodu z `%%configure` magiczną. Spowoduje to skonfigurowanie bazowej sesji usługi Livy do korzystania z dostarczonego pakietu. W kolejnych komórkach w notesie możesz teraz użyć pakietu, jak pokazano poniżej.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    W przypadku usługi HDInsight 3,4 i poniżej należy użyć poniższego fragmentu kodu.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Następnie można uruchomić fragmenty kodu, jak pokazano poniżej, aby wyświetlić dane z ramki danych utworzonej w poprzednim kroku.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Korzystanie z zewnętrznych pakietów języka Python z notesami Jupyter w klastrach Apache Spark w usłudze HDInsight Linux](apache-spark-python-package-installation.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
