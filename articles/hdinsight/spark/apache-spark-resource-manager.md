---
title: Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zarządzać zasobami klastrów Spark w usłudze Azure HDInsight w celu uzyskania lepszej wydajności.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929793"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight

Dowiedz się, jak uzyskać dostęp do interfejsów, takich jak interfejs użytkownika [Apache Ambari](https://ambari.apache.org/) Apache Hadoop, interfejs użytkownika [przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) oraz [serwer historii platformy Spark](./apache-azure-spark-history-server.md) skojarzony z klastrem [Apache Spark](https://spark.apache.org/) i jak dostosować konfigurację klastra pod kątem optymalnej wydajności.

## <a name="open-the-spark-history-server"></a>Otwórz serwer historii platformy Spark

Serwer historii platformy Spark jest interfejsem użytkownika sieci Web do kończenia i uruchamiania aplikacji platformy Spark. Jest to rozszerzenie interfejsu użytkownika sieci Web platformy Spark. Aby uzyskać pełne informacje, zobacz [serwer historii platformy Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Otwórz interfejs użytkownika przędzy

Za pomocą interfejsu użytkownika PRZĘDZy można monitorować aplikacje, które są aktualnie uruchomione w klastrze Spark.

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Z **pulpitów nawigacyjnych klastra** wybierz pozycję **przędza**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra Spark.

    ![Uruchom interfejs użytkownika PRZĘDZy](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternatywnie można również uruchomić interfejs użytkownika PRZĘDZy z interfejsu użytkownika Ambari. W interfejsie użytkownika Ambari **Przejdź do**  >  **łącza szybkie linki** do usługi  >  **Active**  >  **Menedżer zasobów**.

## <a name="optimize-clusters-for-spark-applications"></a>Optymalizowanie klastrów dla aplikacji platformy Spark

Trzy kluczowe parametry, które mogą być używane do konfiguracji platformy Spark w zależności od wymagań aplikacji to `spark.executor.instances` , `spark.executor.cores` i `spark.executor.memory` . Program wykonujący to proces uruchomiony dla aplikacji platformy Spark. Jest on uruchamiany w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba modułów wykonujących i rozmiary wykonawców dla każdego klastra jest obliczana na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te informacje są przechowywane w `spark-defaults.conf` węzłach głównych klastra.

Trzy parametry konfiguracji można skonfigurować na poziomie klastra (dla wszystkich aplikacji uruchamianych w klastrze) lub można je określić również dla poszczególnych aplikacji.

### <a name="change-the-parameters-using-ambari-ui"></a>Zmień parametry za pomocą interfejsu Ambari

1. W interfejsie użytkownika Ambari przejdź do opcji **Spark2**  >  **config**  >  **Custom Spark2-Defaults (ustawienia domyślne**).

    ![Ustawianie parametrów przy użyciu niestandardowych Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Ustawianie parametrów przy użyciu niestandardowych Ambari")

1. Wartości domyślne są dobrym sposobem, że cztery aplikacje Spark działają współbieżnie w klastrze. Można zmienić te wartości z interfejsu użytkownika, jak pokazano na poniższym zrzucie ekranu:

    ![Ustawianie parametrów przy użyciu Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Ustawianie parametrów przy użyciu Ambari")

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany konfiguracji. W górnej części strony zostanie wyświetlony monit o ponowne uruchomienie wszystkich usług, których to dotyczy. Wybierz pozycję **Uruchom ponownie**.

    ![Ponowne uruchamianie usług](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Zmień parametry aplikacji uruchomionej w Jupyter Notebook

W przypadku aplikacji uruchamianych w Jupyter Notebook można użyć Magic, `%%configure` Aby wprowadzić zmiany w konfiguracji. W idealnym przypadku należy wprowadzić takie zmiany na początku aplikacji, zanim zaczniesz korzystać z pierwszej komórki kodu. W ten sposób zagwarantujemy, że konfiguracja zostanie zastosowana do sesji usługi Livy, gdy zostanie utworzona. Jeśli chcesz zmienić konfigurację na późniejszym etapie w aplikacji, musisz użyć `-f` parametru. Jednak dzięki temu cały postęp w aplikacji zostanie utracony.

Poniższy fragment kodu przedstawia sposób zmiany konfiguracji aplikacji działającej w Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Parametry konfiguracji muszą zostać przesłane jako ciąg JSON i muszą znajdować się w następnym wierszu po Magic, jak pokazano w przykładowej kolumnie.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Zmiana parametrów dla aplikacji przesłanej przy użyciu platformy Spark — przesyłanie

Poniższe polecenie stanowi przykład zmiany parametrów konfiguracji dla aplikacji usługi Batch przesłanej przy użyciu `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Zmiana parametrów dla aplikacji przesłanej przy użyciu narzędzia zwinięcie

Poniższe polecenie stanowi przykład zmiany parametrów konfiguracji dla aplikacji usługi Batch przesłanej przy użyciu zwinięcia.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Skopiuj plik JAR do konta magazynu klastra. Nie należy kopiować pliku JAR bezpośrednio do węzła głównego.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Zmień te parametry na serwerze Spark Thrift

Serwer Spark Thrift zapewnia dostęp JDBC/ODBC do klastra Spark i służy do obsługi zapytań Spark SQL. Narzędzia, takie jak Power BI, Tableau i tak dalej, używają protokołu ODBC do komunikacji z serwerem Spark Thrift w celu wykonywania zapytań Spark SQL jako aplikacji platformy Spark. Po utworzeniu klastra Spark dwa wystąpienia serwera Spark Thrift są uruchamiane, jeden w każdym węźle głównym. Każdy serwer Spark Thrift jest widoczny jako aplikacja Spark w interfejsie użytkownika z PRZĘDZą.

Serwer Spark Thrift korzysta z dynamicznej alokacji modułu wykonawczego platformy Spark, w związku z czym `spark.executor.instances` nie jest używany. Zamiast tego serwer Spark Thrift używa `spark.dynamicAllocation.maxExecutors` `spark.dynamicAllocation.minExecutors` programu i do określenia liczby programów wykonujących. Parametry konfiguracji `spark.executor.cores` i `spark.executor.memory` są używane do modyfikowania rozmiaru programu wykonującego. Te parametry można zmienić, jak pokazano w następujących krokach:

* Rozwiń kategorię **Advanced spark2-Thrift-sparkconf** , aby zaktualizować parametry `spark.dynamicAllocation.maxExecutors` i `spark.dynamicAllocation.minExecutors` .

    ![Konfigurowanie serwera Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurowanie serwera Spark Thrift")

* Rozwiń kategorię **Custom spark2-Thrift-sparkconf** , aby zaktualizować parametry `spark.executor.cores` , i `spark.executor.memory` .

    ![Konfigurowanie parametru serwera Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurowanie parametru serwera Spark Thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Zmiana pamięci sterownika serwera Spark Thrift

Pamięć sterownika serwera Spark Thrift jest skonfigurowana do 25% rozmiaru pamięci RAM węzła głównego, pod warunkiem, że całkowity rozmiar pamięci RAM węzła głównego jest większy niż 14 GB. Aby zmienić konfigurację pamięci sterownika, można użyć interfejsu użytkownika Ambari, jak pokazano na poniższym zrzucie ekranu:

W interfejsie użytkownika Ambari przejdź do **Spark2**  >  **configs**  >  **Advanced Spark2-ENV**. Następnie podaj wartość **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Odzyskiwanie zasobów klastra Spark

Ze względu na dynamiczną alokację Spark jedynymi zasobami, które są używane przez serwer Thrift, są zasoby dla dwóch wzorców aplikacji. Aby odzyskiwać te zasoby, należy zatrzymać usługi serwera Thrift uruchomione w klastrze.

1. Z poziomu interfejsu użytkownika Ambari w okienku po lewej stronie wybierz pozycję **Spark2**.

2. Na następnej stronie wybierz pozycję **serwery Spark2 Thrift**.

    ![Uruchom ponownie Thrift serwer1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Uruchom ponownie Thrift serwer1")

3. Powinny zostać wyświetlone dwie węzłów głównych, na których działa serwer Spark2 Thrift. Wybierz jedną z węzłów głównych.

    ![Ponowne uruchamianie Thrift Serwer2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Ponowne uruchamianie Thrift Serwer2")

4. Na następnej stronie znajduje się lista wszystkich usług uruchomionych w ramach tego węzła głównego. Z listy wybierz przycisk listy rozwijanej obok pozycji serwer Spark2 Thrift, a następnie wybierz polecenie **Zatrzymaj**.

    ![Uruchom ponownie Thrift serwer3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Uruchom ponownie Thrift serwer3")
5. Powtórz te czynności również na innych węzła głównegoach.

## <a name="restart-the-jupyter-service"></a>Uruchom ponownie usługę Jupyter

Uruchom interfejs użytkownika sieci Web Ambari, jak pokazano na początku artykułu. W okienku nawigacji po lewej stronie wybierz pozycję **Jupyter**, wybierz pozycję **Akcje usługi**, a następnie wybierz pozycję **Uruchom ponownie wszystkie**. Spowoduje to uruchomienie usługi Jupyter na wszystkich węzłów głównychach.

![Uruchom ponownie program Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Uruchom ponownie program Jupyter")

## <a name="monitor-resources"></a>Monitorowanie zasobów

Uruchom interfejs użytkownika przędzy, jak pokazano na początku artykułu. W tabeli metryk klastra w górnej części ekranu Sprawdź wartości **używanej pamięci** i kolumny **łącznie z pamięcią** . Jeśli te dwie wartości są zamknięte, może nie być wystarczającej ilości zasobów do uruchomienia następnej aplikacji. To samo dotyczy **rdzeni wirtualnych używanych** i **rdzeni wirtualnych sum** kolumn. Ponadto w widoku głównym, jeśli aplikacja została zatrzymana w stanie **zaakceptowanym** , a nie przeszedł w stan **uruchomienia** lub **niepowodzenia** , może to oznaczać, że nie jest dostępna wystarczająca ilość zasobów do uruchomienia.

![Limit zasobów](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limit zasobów")

## <a name="kill-running-applications"></a>Kasuj uruchomione aplikacje

1. W interfejsie użytkownika przędzenia w lewym panelu wybierz pozycję **uruchomiona**. Z listy uruchomionych aplikacji Ustal, która aplikacja ma zostać zabity, i wybierz **Identyfikator**.

    ![Kasuj APP1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kasuj APP1")

2. Wybierz pozycję **Kasuj aplikację** w prawym górnym rogu, a następnie wybierz przycisk **OK**.

    ![Kasuj APP2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kasuj APP2")

## <a name="see-also"></a>Zobacz także

* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Dla analityków danych

* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analiza danych telemetrycznych usługi Application Insight przy użyciu Apache Spark w usłudze HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Dla deweloperów Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)
* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
