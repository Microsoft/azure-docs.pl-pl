---
title: Co to jest platforma Apache Spark — Azure HDInsight
description: Ten artykuł przedstawia wprowadzenie do platformy Spark w usłudze HDInsight i różne scenariusze korzystania z klastra Spark w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 04/17/2020
ms.openlocfilehash: 6926fb8aa22f57ee6068866f732258703b6d78e9
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641765"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Co to jest platforma Apache Spark w usłudze Azure HDInsight

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Platforma Apache Spark w usłudze Azure HDInsight to implementacja firmy Microsoft usługi Apache Spark w chmurze. Usługa HDInsight ułatwia tworzenie i konfigurowanie klastra Spark na platformie Azure. Klastry Spark w usłudze HDInsight są zgodne z usługą Azure Storage i usługą Azure Data Lake Storage. Pozwala to wykorzystywać klastry HDInsight Spark do przetwarzania danych przechowywanych na platformie Azure. Aby uzyskać informacje na temat wersji i składników, zobacz [Wersje i składniki usługi Apache Hadoop w usłudze Azure HDInsight](../hdinsight-component-versioning.md).

![Platforma Spark: ujednolicona struktura](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Co to jest Apache Spark?

Platforma Spark udostępnia typom pierwotnym możliwość używania klastrów obliczeniowych korzystających z funkcji przetwarzania w pamięci. Zadanie platformy Spark może ładować dane do pamięci, buforować je i wielokrotnie przesyłać do nich zapytania. Przetwarzanie w pamięci jest znacznie szybsze niż w przypadku aplikacji opartych na operacjach dyskowych, takich jak usługa Hadoop, która udostępnia dane za pomocą rozproszonego systemu plików usługi Hadoop (HDFS). Platforma Spark obsługuje również integrację z językiem programowania Scala, co pozwala manipulować rozproszonymi zestawami danych jak kolekcjami lokalnymi. Nie ma potrzeby, aby wszystkie elementy były obejmowane strukturami operacji mapowania i redukcji.

![Tradycyjna mapawreduce vs. Iskra](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Klastry Spark w usłudze HDInsight oferują w pełni zarządzaną usługę Spark. Poniżej przedstawiono korzyści związane z utworzeniem klastra Spark w usłudze HDInsight.

| Funkcja | Opis |
| --- | --- |
| Łatwość tworzenia |Nowy klaster Spark w usłudze HDInsight można utworzyć w kilka minut przy użyciu witryny Azure Portal, programu Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Zobacz [Wprowadzenie do klastra Apache Spark w umiań HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Łatwość obsługi |Klaster Spark w usłudze HDInsight zawiera notesy Jupyter i Apache Zeppelin. Można ich używać do interakcyjnego przetwarzania danych i wizualizacji. Zobacz [Używanie notesów Apache Zeppelin z danymi Apache Spark](apache-spark-zeppelin-notebook.md) i Load oraz [uruchamianie zapytań w klastrze Apache Spark](apache-spark-load-data-run-query.md).|
| Interfejsy API REST |Klastry Spark w usłudze HDInsight obejmują [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), czyli serwer zadań Spark oparty na interfejsie API REST do zdalnego przesyłania i monitorowania zadań. Zobacz [Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark](apache-spark-livy-rest-interface.md).|
| Obsługa usługi Azure Data Lake Storage | Klastry Spark w usłudze HDInsight mogą korzystać z usługi Azure Data Lake Storage zarówno w formie magazynu podstawowego, jak i dodatkowego. Aby uzyskać więcej informacji o usłudze Data Lake Storage, zobacz temat [Przegląd usługi Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integracja z usługami Azure |Klaster Spark w usłudze HDInsight zawiera łącznik usługi Azure Event Hubs. Aplikacje do przesyłania strumieniowego można tworzyć za pomocą centrów zdarzeń. W tym Apache Kafka, który jest już dostępny jako część Spark. |
| Obsługa narzędzia ML Server | Narzędzie ML Server jest obsługiwane w usłudze HDInsight przy użyciu typu klastra **Usługi ML**. Możesz skonfigurować klaster usług ML, aby uruchamiać rozproszone obliczenia R z szybkością zapewnianą przez klaster Spark. Aby uzyskać więcej informacji, zobacz [Co to jest usługi ML w usłudze Azure HDInsight](../r-server/r-server-overview.md). |
| Integracja ze zintegrowanymi środowiskami projektowymi innych firm | Usługa HDInsight zapewnia kilka wtyczek IDE, które są przydatne do tworzenia i przesyłania aplikacji do klastra Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [Używanie zestawu narzędzi platformy Azure dla intellij idea](apache-spark-intellij-tool-plugin.md), Użyj narzędzi spark & [hive dla vscode](../hdinsight-for-vscode.md)i [użyj zestawu narzędzi Azure dla programu Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Zapytania jednoczesne |Klastry Spark w usłudze HDInsight obsługują zapytania jednoczesne. Dzięki temu wiele zapytań od jednego użytkownika lub wiele zapytań od różnych użytkowników i aplikacji może współdzielić te same zasoby klastra. |
| Buforowanie na dyskach SSD |Istnieje możliwość buforowania danych w pamięci lub na dyskach SSD podłączonych do węzłów klastra. Buforowanie w pamięci zapewnia najlepszą wydajność zapytań, ale może być kosztowne. Buforowanie na dyskach SSD stanowi doskonałe rozwiązanie umożliwiające poprawę wydajności zapytań bez konieczności tworzenia klastra o rozmiarze obejmującym cały zestaw danych w pamięci. Zobacz [Poprawa wydajności obciążeń platformy Apache Spark przy użyciu pamięci podręcznej we/wy usługi Azure HDInsight.](apache-spark-improve-performance-iocache.md) |
| Integracja z narzędziami do analizy biznesowej |Klastry Spark w usłudze HDInsight zawierają łączniki dla narzędzi do analizy biznesowej danych, takich jak Power BI. |
| Wstępnie załadowane biblioteki Anaconda |Klastry Spark w usłudze HDInsight są dostarczane z wstępnie zainstalowanymi bibliotekami Anaconda. [Anaconda](https://docs.continuum.io/anaconda/) udostępnia blisko 200 bibliotek do uczenia maszynowego, analizy danych, wizualizacji i tak dalej. |
| Adaptacji | Funkcja Skalowanie automatyczne umożliwia dynamiczną zmianę liczby węzłów klastra. Zobacz [Automatyczne skalowanie klastrów usługi Azure HDInsight](../hdinsight-autoscale-clusters.md). Ponadto klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub Data Lake Storage. |
| Umowa SLA |Oferta klastrów Spark w usłudze HDInsight obejmuje całodobową pomoc techniczną oraz umowę SLA gwarantującą 99,9% czasu działania. |

Klastry Apache Spark w usłudze HDInsight obejmują następujące składniki, które są domyślnie dostępne w klastrach.

* [Spark Core](https://spark.apache.org/docs/latest/). Obejmuje takie składniki, jak Spark Core, Spark SQL, interfejsy API przesyłania strumieniowego Spark, GraphX oraz MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Notes Jupyter](https://jupyter.org)
* [Notes Apache Zeppelin](http://zeppelin-project.org/)

Usługa HDInsight Spark gromadzi [sterownik ODBC](https://go.microsoft.com/fwlink/?LinkId=616229) w celu łączności z narzędziami analizy biznesowej, takimi jak Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architektura klastra Spark

![Architektura platformy Spark w usłudze HDInsight](./media/apache-spark-overview/hdi-spark-architecture.png)

Łatwo jest zrozumieć składniki platformy Spark, aby zrozumieć, jak działa platforma Spark w klastrach HDInsight.

Aplikacje platformy Spark są uruchamiane jako niezależne zestawy procesów w klastrze. Koordynowane przez SparkContext obiektu w głównym programie (o nazwie program sterownika).

SparkContext można połączyć się z kilku typów menedżerów klastra, które zapewniają zasoby między aplikacjami. Menedżerowie klastrów to Apache Mesos, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)lub Spark cluster manager. W usłudze HDInsight platforma Spark korzysta z menedżera klastra YARN. Po nawiązaniu połączenia platforma Spark uzyskuje funkcje wykonawcze w węzłach procesu roboczego w klastrze, które są procesami odpowiedzialnymi za uruchamianie obliczeń i przechowywanie danych aplikacji. Następnie platforma Spark wysyła kod aplikacji (zdefiniowany przez pliki JAR lub Python przekazywane do obiektu SparkContext) do funkcji wykonawczych. Na koniec obiekt SparkContext wysyła do funkcji wykonawczych zadania do uruchomienia.

Obiekt SparkContext obsługuje funkcję main użytkownika i wykonuje różne operacje równoległe w węzłach procesu roboczego. Następnie pobiera wyniki operacji. Węzły procesu roboczego odczytują i zapisują dane, korzystając z rozproszonego systemu plików usługi Hadoop. Węzły procesu roboczego przesyłają również przekształcone dane do pamięci podręcznej w postaci odpornych rozproszonych zestawów danych (Resilient Distributed Datasets, RDDs).

SparkContext łączy się z wzorcem platformy Spark i jest odpowiedzialny za konwersję aplikacji na wykres skierowany (DAG) poszczególnych zadań. Zadania, które są wykonywane w ramach procesu executor w węzłach procesu roboczego. Każda aplikacja pobiera własne procesy executor. Które pozostają na czas trwania całej aplikacji i uruchamiają zadania w wielu wątkach.

## <a name="spark-in-hdinsight-use-cases"></a>Przypadki zastosowań platformy Spark w usłudze HDInsight

Klastry Spark w usłudze HDInsight umożliwiają realizację następujących głównych scenariuszy:

### <a name="interactive-data-analysis-and-bi"></a>Interakcyjna analiza danych i analiza biznesowa

Klaster Apache Spark w usłudze HDInsight przechowuje dane w usłudze Azure Storage lub Azure Data Lake Storage. Eksperci biznesowi i kluczowi decydenci mogą analizować i tworzyć raporty nad tymi danymi. Za pomocą usługi Microsoft Power BI można tworzyć interaktywne raporty na podstawie analizowanych danych. Analitycy mogą rozpocząć pracę od danych o częściowej strukturze lub bez struktury w magazynie klastra, zdefiniować schemat danych za pomocą notesów, a następnie skompilować modele danych przy użyciu usługi Microsoft Power BI. Klastry platformy Spark w usłudze HDInsight obsługują również szereg narzędzi analizy biznesowej innych firm. Takie jak Tableau, ułatwiając analitykom danych, ekspertom biznesowym i kluczowym decydentom.

* [Samouczek: wizualizowanie danych platformy Spark przy użyciu usługi Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Apache Spark pochodzi z [MLlib](https://spark.apache.org/mllib/). MLlib to biblioteka uczenia maszynowego zbudowana na platformie Spark, której można używać z klastra platformy Spark w programie HDInsight. Klaster platformy Spark w programie HDInsight zawiera również Anakondę, dystrybucję języka Python z różnymi rodzajami pakietów do uczenia maszynowego. Dzięki wbudowanej obsłudze notebooków Jupyter i Zeppelin masz środowisko do tworzenia aplikacji uczenia maszynowego.

* [Samouczek: Przewidywanie temperatur budynków przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)  
* [Samouczek: Przewidywanie wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Przesyłanie strumieniowe i analiza danych w czasie rzeczywistym na platformie Spark

Klastry Spark w usłudze HDInsight zapewniają szeroką obsługę tworzenia rozwiązań do analizy w czasie rzeczywistym. Spark ma już łączniki do pozyskiwania danych z wielu źródeł, takich jak Kafka, Flume, Twitter, ZeroMQ lub TCP gniazd. Platforma Spark w usłudze HDInsight dodaje pierwszorzędną obsługę pozyskiwania danych z usługi Azure Event Hubs. Event Hubs to najczęściej używana usługa kolejkowania na platformie Azure. Pełna obsługa centrów zdarzeń sprawia, że klastry platformy Spark w usłudze HDInsight są idealną platformą do tworzenia potoku analizy w czasie rzeczywistym.

* [Omówienie przesyłania strumieniowego w ramach apache Spark](apache-spark-streaming-overview.md)
* [Omówienie strumieniowania strukturalnego Apache Spark](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Od czego zacząć?

Aby dowiedzieć się więcej na temat platformy Apache Spark w usłudze HDInsight, możesz zapoznać się z następującymi artykułami:

* [Szybki start: tworzenie klastra Platformy Spark Apache w programie HDInsight i uruchamianie interaktywnych zapytań przy użyciu programu Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Samouczek: Uruchom zadanie Apache Spark przy użyciu Jupyter](./apache-spark-load-data-run-query.md)
* [Samouczek: Analizowanie danych przy użyciu narzędzi analizy biznesowej](./apache-spark-use-bi-tools.md)
* [Samouczek: Uczenie maszynowe przy użyciu platformy Spark apache](./apache-spark-ipython-notebook-machine-learning.md)
* [Samouczek: Tworzenie aplikacji Scala Maven za pomocą IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Następne kroki

W tym omówieniu uzyskasz podstawową wiedzę na temat platformy Apache Spark w usłudze Azure HDInsight. Dowiedz się, jak utworzyć klaster platformy SPARK usługi HDInsight i uruchomić niektóre kwerendy programu Spark SQL:

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
