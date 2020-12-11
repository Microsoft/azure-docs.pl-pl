---
title: Co to jest platforma Apache Spark — Azure HDInsight
description: Ten artykuł przedstawia wprowadzenie do platformy Spark w usłudze HDInsight i różne scenariusze korzystania z klastra Spark w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 176b89c9fc80b198a8a639afdd14185ab0008aed
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029444"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Co to jest platforma Apache Spark w usłudze Azure HDInsight

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Platforma Apache Spark w usłudze Azure HDInsight to implementacja firmy Microsoft usługi Apache Spark w chmurze. Usługa HDInsight ułatwia tworzenie i konfigurowanie klastra Spark na platformie Azure. Klastry Spark w usłudze HDInsight są zgodne z [usługą Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). Pozwala to wykorzystywać klastry HDInsight Spark do przetwarzania danych przechowywanych na platformie Azure. Aby uzyskać informacje na temat wersji i składników, zobacz [Wersje i składniki usługi Apache Hadoop w usłudze Azure HDInsight](../hdinsight-component-versioning.md).

![Platforma Spark: ujednolicona struktura](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Co to jest Apache Spark?

Platforma Spark udostępnia typom pierwotnym możliwość używania klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Zadanie Spark może ładować i buforować dane w pamięci, a następnie wielokrotnie wykonywać zapytania względem tych danych. Przetwarzanie w pamięci jest znacznie szybsze niż w przypadku aplikacji opartych na operacjach dyskowych, takich jak usługa Hadoop, która udostępnia dane za pomocą rozproszonego systemu plików usługi Hadoop (HDFS). Platforma Spark obsługuje również integrację z językiem programowania Scala, co pozwala manipulować rozproszonymi zestawami danych jak kolekcjami lokalnymi. Nie ma potrzeby, aby wszystkie elementy były obejmowane strukturami operacji mapowania i redukcji.

![Tradycyjny MapReduce a Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Klastry Spark w usłudze HDInsight oferują w pełni zarządzaną usługę Spark. Poniżej przedstawiono korzyści związane z utworzeniem klastra Spark w usłudze HDInsight.

| Cecha | Opis |
| --- | --- |
| Łatwość tworzenia |Nowy klaster Spark w usłudze HDInsight można utworzyć w kilka minut przy użyciu witryny Azure Portal, programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Zobacz Rozpoczynanie [pracy z klastrem Apache Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Łatwość użycia |Klaster Spark w usłudze HDInsight zawiera notesy Jupyter i Apache Zeppelin. Można ich używać do interakcyjnego przetwarzania danych i wizualizacji. Zobacz [Korzystanie z notesów Apache Zeppelin z Apache Spark](apache-spark-zeppelin-notebook.md) i [ładowanie danych i uruchamianie zapytań w klastrze Apache Spark](apache-spark-load-data-run-query.md).|
| Interfejsy API REST |Klastry Spark w usłudze HDInsight obejmują [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), czyli serwer zadań Spark oparty na interfejsie API REST do zdalnego przesyłania i monitorowania zadań. Zobacz [Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark](apache-spark-livy-rest-interface.md).|
| Obsługa usługi Azure Storage | Klastry Spark w usłudze HDInsight mogą używać Azure Data Lake Storage Gen1/Gen2 jako magazynu podstawowego lub dodatkowego magazynu. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1, zobacz [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md). Aby uzyskać więcej informacji na temat Data Lake Storage Gen2, zobacz [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).|
| Integracja z usługami Azure |Klaster Spark w usłudze HDInsight zawiera łącznik usługi Azure Event Hubs. Możesz tworzyć aplikacje przesyłania strumieniowego przy użyciu Event Hubs. Uwzględnienie Apache Kafka, który jest już dostępny w ramach platformy Spark. |
| Obsługa narzędzia ML Server | Narzędzie ML Server jest obsługiwane w usłudze HDInsight przy użyciu typu klastra **Usługi ML**. Możesz skonfigurować klaster usług ML, aby uruchamiać rozproszone obliczenia R z szybkością zapewnianą przez klaster Spark. Aby uzyskać więcej informacji, zobacz [co to jest usługa l Services w usłudze Azure HDInsight](../r-server/r-server-overview.md). |
| Integracja ze zintegrowanymi środowiskami projektowymi innych firm | Usługa HDInsight zapewnia kilka wtyczek IDE, które są przydatne do tworzenia i przesyłania aplikacji do klastra Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz temat [używanie Azure Toolkit for INTELLIJ pomysłem](apache-spark-intellij-tool-plugin.md), [Korzystanie z narzędzi platformy Spark & do programu vscode](../hdinsight-for-vscode.md)i [Używanie Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Zapytania jednoczesne |Klastry Spark w usłudze HDInsight obsługują zapytania jednoczesne. Dzięki temu wiele zapytań od jednego użytkownika lub wiele zapytań od różnych użytkowników i aplikacji może współdzielić te same zasoby klastra. |
| Buforowanie na dyskach SSD |Istnieje możliwość buforowania danych w pamięci lub na dyskach SSD podłączonych do węzłów klastra. Buforowanie w pamięci zapewnia najlepszą wydajność zapytań, ale może być kosztowne. Buforowanie na dyskach SSD stanowi doskonałe rozwiązanie umożliwiające poprawę wydajności zapytań bez konieczności tworzenia klastra o rozmiarze obejmującym cały zestaw danych w pamięci. Zobacz [Poprawianie wydajności obciążeń Apache Spark przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight](apache-spark-improve-performance-iocache.md). |
| Integracja z narzędziami do analizy biznesowej |Klastry Spark w usłudze HDInsight zawierają łączniki dla narzędzi do analizy biznesowej danych, takich jak Power BI. |
| Wstępnie załadowane biblioteki Anaconda |Klastry Spark w usłudze HDInsight są dostarczane z wstępnie zainstalowanymi bibliotekami Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) zapewnia blisko 200 bibliotek na potrzeby uczenia maszynowego, analizy danych, wizualizacji i tak dalej. |
| Adaptacja | Usługa HDInsight umożliwia dynamiczne Zmienianie liczby węzłów klastra przy użyciu funkcji automatycznego skalowania. Zobacz [Automatyczne skalowanie klastrów usługi Azure HDInsight](../hdinsight-autoscale-clusters.md). Ponadto klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Blob Storage, [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) lub [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). |
| Umowa SLA |Oferta klastrów Spark w usłudze HDInsight obejmuje całodobową pomoc techniczną oraz umowę SLA gwarantującą 99,9% czasu działania. |

Klastry Apache Spark w usłudze HDInsight obejmują następujące składniki, które są domyślnie dostępne w klastrach.

* [Spark Core](https://spark.apache.org/docs/latest/). Obejmuje takie składniki, jak Spark Core, Spark SQL, interfejsy API przesyłania strumieniowego Spark, GraphX oraz MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notes](https://jupyter.org)
* [Notes Apache Zeppelin](http://zeppelin-project.org/)

Klastry HDInsight Spark są [sterownikiem ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) do łączności z narzędziami analizy biznesowej, takimi jak Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architektura klastra Spark

![Architektura platformy Spark w usłudze HDInsight](./media/apache-spark-overview/hdi-spark-architecture.png)

Można łatwo zrozumieć składniki platformy Spark, zrozumieć, jak działa platforma Spark w klastrach usługi HDInsight.

Aplikacje platformy Spark działają jako niezależne zestawy procesów w klastrze. Skoordynowane przez obiekt SparkContext w głównym programie (nazywanym programem sterownika).

SparkContext może łączyć się z kilkoma typami menedżerów klastrów, które zapewniają zasoby między aplikacjami. Te menedżery klastrów to Apache Mesos, Apache Hadoop YARN lub menedżer klastra Spark. W usłudze HDInsight platforma Spark korzysta z menedżera klastra YARN. Po nawiązaniu połączenia platforma Spark uzyskuje funkcje wykonawcze w węzłach procesu roboczego w klastrze, które są procesami odpowiedzialnymi za uruchamianie obliczeń i przechowywanie danych aplikacji. Następnie platforma Spark wysyła kod aplikacji (zdefiniowany przez pliki JAR lub Python przekazywane do obiektu SparkContext) do funkcji wykonawczych. Na koniec obiekt SparkContext wysyła do funkcji wykonawczych zadania do uruchomienia.

Obiekt SparkContext obsługuje funkcję main użytkownika i wykonuje różne operacje równoległe w węzłach procesu roboczego. Następnie pobiera wyniki operacji. Węzły procesu roboczego odczytują i zapisują dane, korzystając z rozproszonego systemu plików usługi Hadoop. Węzły procesu roboczego przesyłają również przekształcone dane do pamięci podręcznej w postaci odpornych rozproszonych zestawów danych (Resilient Distributed Datasets, RDDs).

SparkContext nawiązuje połączenie z serwerem głównym platformy Spark i jest odpowiedzialny za konwersję aplikacji na wykres ukierunkowany (DAG) poszczególnych zadań. Zadania, które są wykonywane w procesie wykonawcy w węzłach procesu roboczego. Każda aplikacja pobiera własne procesy wykonujące. Co się stanie w czasie trwania całej aplikacji i uruchamiania zadań w wielu wątkach.

## <a name="spark-in-hdinsight-use-cases"></a>Przypadki zastosowań platformy Spark w usłudze HDInsight

Klastry Spark w usłudze HDInsight umożliwiają realizację następujących głównych scenariuszy:

### <a name="interactive-data-analysis-and-bi"></a>Interakcyjna analiza danych i analiza biznesowa

Apache Spark w usłudze HDInsight przechowuje dane na platformie Azure Blob Storage, Azure Data Lake Gen1 lub Azure Data Lake Storage Gen2. Eksperci biznesowi i twórcy najważniejszych decyzji mogą analizować i kompilować raporty dotyczące tych danych. Korzystając z programu Microsoft Power BI, można tworzyć interaktywne raporty na podstawie analizowanych danych. Analitycy mogą rozpocząć pracę od danych o częściowej strukturze lub bez struktury w magazynie klastra, zdefiniować schemat danych za pomocą notesów, a następnie skompilować modele danych przy użyciu usługi Microsoft Power BI. Klastry Spark w usłudze HDInsight obsługują również wiele narzędzi do analizy biznesowej innych firm. Takie jak Tableau, co ułatwia analitykom danych, ekspertom biznesowym i osobom zajmującym się podejmowaniem decyzji.

* [Samouczek: wizualizowanie danych platformy Spark przy użyciu usługi Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Apache Spark zawiera [MLlib](https://spark.apache.org/mllib/). MLlib to biblioteka uczenia maszynowego oparta na platformie Spark, która może być używana z klastra Spark w usłudze HDInsight. Klaster Spark w usłudze HDInsight obejmuje również Anaconda, dystrybucję w języku Python z różnymi rodzajami pakietów na potrzeby uczenia maszynowego. Dzięki wbudowanej obsłudze notesów Jupyter i Zeppelin masz środowisko do tworzenia aplikacji do uczenia maszynowego.

* [Samouczek: przewidywanie temperatur konstrukcyjnych przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)  
* [Samouczek: przewidywanie wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Przesyłanie strumieniowe i analiza danych w czasie rzeczywistym na platformie Spark

Klastry Spark w usłudze HDInsight zapewniają szeroką obsługę tworzenia rozwiązań do analizy w czasie rzeczywistym. Platforma Spark ma już łączniki do pozyskiwania danych z wielu źródeł, takich jak Kafka, Flume, Twitter, ZeroMQ lub TCP Sockets. Platforma Spark w usłudze HDInsight umożliwia dodanie pierwszej klasy do pozyskiwania danych z usługi Azure Event Hubs. Event Hubs to najczęściej używana usługa kolejkowania na platformie Azure. Pełna pomoc techniczna dla Event Hubs sprawia, że klastry Spark w usłudze HDInsight są idealną platformą do tworzenia potoku analizy w czasie rzeczywistym.

* [Omówienie Apache Spark przesyłania strumieniowego](apache-spark-streaming-overview.md)
* [Przegląd Apache Spark strukturalnych przesyłania strumieniowego](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>Następne kroki

W tym omówieniu masz podstawową wiedzę na temat Apache Spark w usłudze Azure HDInsight.  Aby dowiedzieć się więcej o Apache Spark w usłudze HDInsight, możesz skorzystać z poniższych artykułów i utworzyć klaster usługi HDInsight Spark, a następnie uruchomić kilka przykładowych zapytań Spark:

* [Szybki Start: Tworzenie klastra Apache Spark w usłudze HDInsight i uruchamianie interakcyjnego zapytania przy użyciu Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Samouczek: ładowanie danych i uruchamianie zapytań w ramach zadania Apache Spark za pomocą Jupyter](./apache-spark-load-data-run-query.md)
* [Samouczek: wizualizowanie danych platformy Spark przy użyciu usługi Power BI](apache-spark-use-bi-tools.md)
* [Samouczek: przewidywanie temperatur konstrukcyjnych przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Optymalizowanie zadań platformy Spark pod kątem wydajności](apache-spark-perf.md)