---
title: Analizowanie dzienników usługi Application Insights przy użyciu platformy Spark — Azure HDInsight
description: Dowiedz się, jak wyeksportować dzienniki usługi Application Insights do usługi BLOB Storage, a następnie przeanalizować dzienniki przy użyciu platformy Spark w usłudze HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: fbca4b23b6fc414727ea1b5a4012d2847d6b8d78
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940464"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analizowanie Application Insights dzienników telemetrii za pomocą Apache Spark w usłudze HDInsight

Dowiedz się, w jaki sposób używać [Apache Spark](https://spark.apache.org/) w usłudze HDInsight do analizowania danych telemetrycznych usługi Application Insight.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) to Usługa analityczna, która monitoruje aplikacje sieci Web. Dane telemetryczne wygenerowane przez Application Insights mogą zostać wyeksportowane do usługi Azure Storage. Gdy dane są przechowywane w usłudze Azure Storage, można je przeanalizować przy użyciu usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja, która jest skonfigurowana do korzystania z Application Insights.

* Znajomość tworzenia klastra usługi HDInsight opartej na systemie Linux. Aby uzyskać więcej informacji, zobacz [tworzenie Apache Spark w usłudze HDInsight](apache-spark-jupyter-spark-sql.md).

* Przeglądarka sieci Web.

Podczas opracowywania i testowania tego dokumentu użyto następujących zasobów:

* Application Insights dane telemetryczne zostały wygenerowane przy użyciu [ aplikacji internetowejNode.js skonfigurowanej do używania Application Insights](../../azure-monitor/app/nodejs.md).

* Usługa Spark oparta na systemie Linux w klastrze usługi HDInsight w wersji 3,5 została użyta do analizy danych.

## <a name="architecture-and-planning"></a>Architektura i planowanie

Na poniższym diagramie przedstawiono architekturę usług tego przykładu:

![Dane przepływające z Application Insights do magazynu obiektów blob, a następnie platformy Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights można skonfigurować tak, aby w sposób ciągły eksportować informacje telemetryczne do obiektów BLOB. Usługa HDInsight może następnie odczytywać dane przechowywane w obiektach Blob. Istnieją jednak pewne wymagania, które należy wykonać:

* **Lokalizacja**: Jeśli konto magazynu i Usługa HDInsight znajdują się w różnych lokalizacjach, może to zwiększyć opóźnienia. Zwiększa również koszty, ponieważ opłaty za ruch wychodzący są stosowane do danych przenoszonych między regionami.

    > [!WARNING]  
    > Użycie konta magazynu w innej lokalizacji niż Usługa HDInsight nie jest obsługiwane.

* **Typ obiektu BLOB**: Usługa HDInsight obsługuje tylko blokowe obiekty blob. Application Insights domyślnie używać blokowych obiektów blob, dlatego powinna być domyślnie współpracująca z usługą HDInsight.

Aby uzyskać informacje na temat dodawania magazynu do istniejącego klastra, zapoznaj się z dokumentem [Dodawanie dodatkowych kont magazynu](../hdinsight-hadoop-add-storage.md) .

### <a name="data-schema"></a>Schemat danych

Application Insights zapewnia informacje o [modelu eksportu danych](../../azure-monitor/app/export-data-model.md) dla danych telemetrycznych eksportowanych do obiektów BLOB. Kroki opisane w tym dokumencie używają platformy Spark SQL do pracy z danymi. Platforma Spark SQL może automatycznie generować schemat dla struktury danych JSON zarejestrowanej przez Application Insights.

## <a name="export-telemetry-data"></a>Eksportowanie danych telemetrycznych

Wykonaj kroki opisane w sekcji [Konfigurowanie eksportu ciągłego](../../azure-monitor/app/export-telemetry.md) , aby skonfigurować Application Insights do eksportowania informacji telemetrycznych do obiektu BLOB usługi Azure Storage.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurowanie usługi HDInsight do uzyskiwania dostępu do danych

Jeśli tworzysz klaster usługi HDInsight, Dodaj konto magazynu podczas tworzenia klastra.

Aby dodać konto usługi Azure Storage do istniejącego klastra, użyj informacji zawartych w dokumencie [Dodawanie dodatkowych kont magazynu](../hdinsight-hadoop-add-storage.md) .

## <a name="analyze-the-data-pyspark"></a>Analizowanie danych: PySpark

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji, gdzie ClusterName jest nazwą klastra.

2. W prawym górnym rogu strony Jupyter, wybierz pozycję **Nowy**, a następnie **PySpark**. Zostanie otwarta nowa karta przeglądarki zawierająca Jupyter Notebook oparty na języku Python.

3. W pierwszym polu (zwanym **komórką**) na stronie Wprowadź następujący tekst:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Ten kod konfiguruje platformę Spark w celu rekursywnego uzyskiwania dostępu do struktury katalogów dla danych wejściowych. Dane telemetryczne Application Insights są rejestrowane w strukturze katalogów podobnej do `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Aby uruchomić kod, użyj **klawiszy SHIFT + ENTER** . Po lewej stronie komórki " \* " pojawia się między nawiasami, aby wskazać, że kod w tej komórce jest wykonywany. Po zakończeniu zostanie \* wyświetlony tekst "" zmiany w liczbie i dane wyjściowe podobne do następującego tekstu poniżej komórki:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Zostanie utworzona nowa komórka poniżej pierwszej. Wprowadź następujący tekst w nowej komórce. Zastąp `CONTAINER` wartość i nazwą `STORAGEACCOUNT` konta usługi Azure Storage i nazwą kontenera obiektów blob, która zawiera Application Insights dane.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Aby wykonać tę komórkę, użyj **klawiszy SHIFT + ENTER** . Zobaczysz wynik podobny do następującego tekstu:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Zwrócona ścieżka wasbs jest lokalizacją danych telemetrycznych Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby używał zwróconej ścieżki wasbs, a następnie użyj **klawiszy SHIFT + ENTER** , aby ponownie uruchomić komórkę. Tym razem wyniki powinny zawierać katalogi zawierające dane telemetryczne.

   > [!NOTE]  
   > W pozostałych krokach w tej sekcji `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` katalog został użyty. Struktura katalogów może się różnić.

6. W następnej komórce wprowadź następujący kod: Zastąp `WASB_PATH` ścieżką z poprzedniego kroku.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON eksportowanych przez proces ciągłego eksportowania. Użyj **klawiszy SHIFT + ENTER** , aby uruchomić tę komórkę.
7. W następnej komórce wprowadź i uruchom następujące polecenie, aby wyświetlić schemat utworzony przez platformę Spark dla plików JSON:

   ```python
   jsonData.printSchema()
   ```

    Schemat dla każdego typu telemetrii jest różny. Poniższy przykład to schemat generowany dla żądań sieci Web (dane przechowywane w `Requests` podkatalogu):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Użyj następujących elementów do zarejestrowania ramki danych jako tabeli tymczasowej i uruchomienia zapytania dotyczącego:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    To zapytanie zwraca informacje o mieście dla 20 najważniejszych rekordów, w których Context. Location. City nie ma wartości null.

   > [!NOTE]  
   > Struktura kontekstu jest obecna we wszystkich danych telemetrycznych zarejestrowanych przez Application Insights. W dziennikach nie można wypełnić elementu miasto. Użyj schematu, aby zidentyfikować inne elementy, które można badać, które mogą zawierać dane dla dzienników.

    To zapytanie zwraca informacje podobne do następującego tekstu:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>Analizowanie danych: Scala

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji, gdzie ClusterName jest nazwą klastra.

2. W prawym górnym rogu strony Jupyter, wybierz pozycję **Nowy**, a następnie **Scala**. Zostanie wyświetlona nowa karta przeglądarki zawierająca Jupyter Notebook oparty na Scala.

3. W pierwszym polu (zwanym **komórką**) na stronie Wprowadź następujący tekst:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Ten kod konfiguruje platformę Spark w celu rekursywnego uzyskiwania dostępu do struktury katalogów dla danych wejściowych. Dane telemetryczne Application Insights są rejestrowane w strukturze katalogów podobnej do `/{telemetry type}/YYYY-MM-DD/{##}/` .

4. Aby uruchomić kod, użyj **klawiszy SHIFT + ENTER** . Po lewej stronie komórki " \* " pojawia się między nawiasami, aby wskazać, że kod w tej komórce jest wykonywany. Po zakończeniu zostanie \* wyświetlony tekst "" zmiany w liczbie i dane wyjściowe podobne do następującego tekstu poniżej komórki:

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. Zostanie utworzona nowa komórka poniżej pierwszej. Wprowadź następujący tekst w nowej komórce. Zastąp `CONTAINER` wartość i nazwą `STORAGEACCOUNT` konta usługi Azure Storage i nazwą kontenera obiektów blob, która zawiera Application Insights dzienniki.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Aby wykonać tę komórkę, użyj **klawiszy SHIFT + ENTER** . Zobaczysz wynik podobny do następującego tekstu:

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    Zwrócona ścieżka wasbs jest lokalizacją danych telemetrycznych Application Insights. Zmień `hdfs dfs -ls` wiersz w komórce, aby używał zwróconej ścieżki wasbs, a następnie użyj **klawiszy SHIFT + ENTER** , aby ponownie uruchomić komórkę. Tym razem wyniki powinny zawierać katalogi zawierające dane telemetryczne.

   > [!NOTE]  
   > W pozostałych krokach w tej sekcji `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` katalog został użyty. Ten katalog może nie istnieć, chyba że dane telemetryczne są przeznaczone dla aplikacji sieci Web.

6. W następnej komórce wprowadź następujący kod: Zastąp `WASB\_PATH` ścieżką z poprzedniego kroku.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Ten kod tworzy ramkę danych z plików JSON eksportowanych przez proces ciągłego eksportowania. Użyj **klawiszy SHIFT + ENTER** , aby uruchomić tę komórkę.

7. W następnej komórce wprowadź i uruchom następujące polecenie, aby wyświetlić schemat utworzony przez platformę Spark dla plików JSON:

   ```scala
   jsonData.printSchema
   ```

    Schemat dla każdego typu telemetrii jest różny. Poniższy przykład to schemat generowany dla żądań sieci Web (dane przechowywane w `Requests` podkatalogu):

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. Użyj następujących elementów do zarejestrowania ramki danych jako tabeli tymczasowej i uruchomienia zapytania dotyczącego:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    To zapytanie zwraca informacje o mieście dla 20 najważniejszych rekordów, w których Context. Location. City nie ma wartości null.

   > [!NOTE]  
   > Struktura kontekstu jest obecna we wszystkich danych telemetrycznych zarejestrowanych przez Application Insights. W dziennikach nie można wypełnić elementu miasto. Użyj schematu, aby zidentyfikować inne elementy, które można badać, które mogą zawierać dane dla dzienników.

    To zapytanie zwraca informacje podobne do następującego tekstu:

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów użycia Apache Spark do pracy z danymi i usługami na platformie Azure, zobacz następujące dokumenty:

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

Aby uzyskać informacje na temat tworzenia i uruchamiania aplikacji platformy Spark, zobacz następujące dokumenty:

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu usługi Livy](apache-spark-livy-rest-interface.md)
