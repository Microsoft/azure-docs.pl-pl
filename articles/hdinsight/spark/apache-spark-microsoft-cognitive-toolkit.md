---
title: Microsoft Cognitive Toolkit z usługą Azure HDInsight Apache Spark
description: Dowiedz się, jak przeszkolony Microsoft Cognitive Toolkit model uczenia głębokiego może zostać zastosowany do zestawu danych przy użyciu interfejsu API platformy Spark w klastrze Azure HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: cddbc4b6a5c7a2c787c8305fdf703e34543746f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929975"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Używanie Microsoft Cognitive Toolkit modelu uczenia głębokiego z klastrem Azure HDInsight Spark

W tym artykule opisano następujące czynności.

1. Uruchamianie niestandardowego skryptu w celu zainstalowania [Microsoft Cognitive Toolkit](/cognitive-toolkit/) w klastrze Azure HDInsight Spark.

2. Przekaż [Jupyter Notebook](https://jupyter.org/) do klastra [Apache Spark](https://spark.apache.org/) , aby dowiedzieć się, jak zastosować szkolony Microsoft Cognitive Toolkit model uczenia głębokiego do plików na koncie usługi Azure Blob Storage przy użyciu [interfejsu API Spark Python (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [ładowanie danych i uruchamianie zapytań za pomocą Apache Spark w usłudze HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Jak działa ten przepływ rozwiązania?

To rozwiązanie jest podzielone między tym artykułem a Jupyter Notebook przekazywanym w ramach tego artykułu. W tym artykule wykonaj następujące czynności:

* Uruchom akcję skryptu w klastrze usługi HDInsight Spark w celu zainstalowania pakietów Microsoft Cognitive Toolkit i języka Python.
* Przekaż Jupyter Notebook, w którym jest uruchamiane rozwiązanie, do klastra usługi HDInsight Spark.

Poniższe pozostałe kroki zostały omówione w Jupyter Notebook.

* Załaduj przykładowe obrazy do rozproszonego zestawu danych w usłudze Spark lub RDD.
  * Ładowanie modułów i Definiowanie ustawień wstępnych.
  * Pobierz zestaw danych lokalnie w klastrze Spark.
  * Przekonwertuj zestaw danych na RDD.
* Ocena obrazów przy użyciu przeszkolonego modelu Cognitive Toolkit.
  * Pobierz przeszkolony model Cognitive Toolkit do klastra Spark.
  * Zdefiniuj funkcje, które mają być używane przez węzły procesu roboczego.
  * Wykreśl obrazy na węzłach procesu roboczego.
  * Oceń dokładność modelu.

## <a name="install-microsoft-cognitive-toolkit"></a>Zainstaluj Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit można zainstalować w klastrze Spark za pomocą akcji skryptu. Akcja skryptu używa niestandardowych skryptów do instalowania składników w klastrze, które nie są dostępne domyślnie. Możesz użyć niestandardowego skryptu z Azure Portal przy użyciu zestawu .NET SDK usługi HDInsight lub przy użyciu Azure PowerShell. Możesz również użyć skryptu, aby zainstalować zestaw narzędzi w ramach tworzenia klastra lub gdy klaster jest uruchomiony.

W tym artykule używamy portalu do zainstalowania zestawu narzędzi po utworzeniu klastra. Aby poznać inne sposoby uruchamiania skryptu niestandardowego, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby uzyskać instrukcje dotyczące sposobu używania Azure Portal do uruchamiania akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Upewnij się, że podano następujące dane wejściowe, aby zainstalować Microsoft Cognitive Toolkit. Użyj następujących wartości dla akcji skryptu:

|Właściwość |Wartość |
|---|---|
|Typ skryptu|-Niestandardowe|
|Nazwa| Zainstaluj MCT|
|Identyfikator URI skryptu bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Typy węzłów:|Kierownik, proces roboczy|
|Parametry|Brak|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Przekaż Jupyter Notebook do klastra Azure HDInsight Spark

Aby użyć Microsoft Cognitive Toolkit z klastrem Azure HDInsight Spark, należy załadować Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** do klastra Azure HDInsight Spark. Ten Notes jest dostępny w witrynie GitHub pod adresem [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Pobierz i rozpakuj [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. W Jupyter Notebook wybierz pozycję **Przekaż** w prawym górnym rogu, a następnie przejdź do opcji Pobierz i wybierz plik `CNTK_model_scoring_on_Spark_walkthrough.ipynb` .

    ![Przekazywanie Jupyter Notebook do klastra Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Przekazywanie Jupyter Notebook do klastra Azure HDInsight Spark")

1. Wybierz pozycję **Przekaż** ponownie.

1. Po przekazaniu notesu kliknij nazwę notesu, a następnie postępuj zgodnie z instrukcjami w notesie dotyczącym ładowania zestawu danych i wykonaj artykuł.

## <a name="see-also"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu Apache Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
