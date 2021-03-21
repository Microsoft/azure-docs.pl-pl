---
title: OutOfMemoryError wyjątki dla Apache Spark w usłudze Azure HDInsight
description: Różne wyjątki OutOfMemoryError dla klastra Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a15d79f2ae9c3d20a73ec557c57a5c189b18111b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946343"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError wyjątki dla Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenariusz: OutOfMemoryError wyjątek dla Apache Spark

### <a name="issue"></a>Problem

Aplikacja Apache Sparka nie powiodła się z powodu nieobsługiwanego wyjątku OutOfMemoryError. Może zostać wyświetlony komunikat o błędzie podobny do:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Przyczyna

Najbardziej prawdopodobną przyczyną tego wyjątku jest brak wystarczającej ilości pamięci sterty przydzielony do maszyn wirtualnych Java (JVMs). Te JVMs są uruchamiane jako elementy wykonawcze lub sterowniki w ramach aplikacji Apache Spark.

### <a name="resolution"></a>Rozwiązanie

1. Określ maksymalny rozmiar danych obsługiwanych przez aplikację aparatu Spark. Należy oszacować rozmiar na podstawie maksymalnego rozmiaru danych wejściowych, danych pośrednich generowanych przez transformacje danych wejściowych i danych wyjściowych, które wygenerowały dalsze Przekształcanie danych pośrednich. Jeśli wstępne oszacowanie nie jest wystarczające, Zwiększ rozmiar nieco i wykonaj iterację do momentu wystąpienia błędów pamięci.

1. Upewnij się, że klaster usługi HDInsight, który ma być używany, ma wystarczającą ilość zasobów pamięci i rdzeni, aby pomieścić aplikację aparatu Spark. Można to ustalić, wyświetlając sekcję metryki klastra w interfejsie użytkownika PRZĘDZy w klastrze, aby uzyskać wartości **używanej pamięci** a **łącznie** z **rdzeni wirtualnych** i **rdzeni wirtualnych**.

    ![Widok pamięci podstawowej przędzy](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Ustaw następujące konfiguracje platformy Spark na odpowiednie wartości. Równoważyć wymagania dotyczące aplikacji z dostępnymi zasobami w klastrze. Te wartości nie powinny przekraczać 90% dostępnej pamięci i rdzeni w postaci podanej przez PRZĘDZę i powinny również spełniać minimalne wymagania dotyczące pamięci aplikacji Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Łączna ilość pamięci używanej przez wszystkie wykonawcy =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Całkowita ilość pamięci używanej przez sterownik =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenariusz: błąd obszaru sterty Java podczas próby otwarcia serwera historii Apache Spark

### <a name="issue"></a>Problem

Podczas otwierania zdarzeń na serwerze historii platformy Spark pojawia się następujący błąd:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Przyczyna

Ten problem często jest spowodowany brakiem zasobów podczas otwierania dużych plików zdarzeń Spark. Rozmiar sterty Spark jest domyślnie ustawiony na 1 GB, ale duże pliki zdarzeń platformy Spark mogą wymagać więcej niż tego.

Jeśli chcesz sprawdzić rozmiar plików, które próbujesz załadować, możesz wykonać następujące polecenia:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Rozwiązanie

Pamięć serwera historii platformy Spark można zwiększyć, edytując `SPARK_DAEMON_MEMORY` Właściwość w konfiguracji platformy Spark i uruchamiając ponownie wszystkie usługi.

Można to zrobić z poziomu interfejsu użytkownika przeglądarki Ambari, wybierając sekcję Spark2/config/Advanced Spark2-ENV.

![Advanced spark2 — sekcja ENV](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Dodaj następującą właściwość, aby zmienić pamięć serwera historii platformy Spark z 1g na 4G: `SPARK_DAEMON_MEMORY=4g` .

![Właściwość platformy Spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Upewnij się, że wszystkie usługi, których to dotyczy, zostały uruchomione ponownie z Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenariusz: nie można uruchomić serwera usługi Livy w klastrze Apache Spark

### <a name="issue"></a>Problem

Nie można uruchomić serwera usługi Livy na Apache Spark [(Spark 2,1 w systemie Linux (HDI 3,6)]. Podjęto próbę ponownego uruchomienia w następujący stosie błędów z dzienników usługi Livy:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Przyczyna

`java.lang.OutOfMemoryError: unable to create new native thread` Najważniejsze elementy systemu operacyjnego nie mogą przypisywać więcej natywnych wątków do JVMs. Potwierdzenie, że ten wyjątek jest spowodowany przez naruszenie limitu liczby wątków dla procesu.

Po nieoczekiwanym zakończeniu działania serwera usługi Livy wszystkie połączenia z klastrami Spark są również przerywane, co oznacza, że wszystkie zadania i powiązane dane zostaną utracone. W ramach mechanizmu odzyskiwania sesji HDP 2,6 wprowadzono usługi Livy szczegóły sesji w dozorcy do odzyskania po zakończeniu działania serwera usługi Livy.

W przypadku przesyłania dużej liczby zadań za pośrednictwem usługi usługi Livy, w ramach wysokiej dostępności dla serwera usługi Livy są przechowywane te Stany sesji w ZK (w klastrach usługi HDInsight) i odzyskiwać te sesje po ponownym uruchomieniu usługi usługi Livy. Po ponownym uruchomieniu po nieoczekiwanym zakończeniu usługi Livy tworzy jeden wątek dla każdej sesji, co spowoduje zsumowanie określonej liczby sesji do odzyskiwania, co powoduje utworzenie zbyt wielu wątków.

### <a name="resolution"></a>Rozwiązanie

Usuń wszystkie wpisy, wykonując kroki opisane poniżej.

1. Pobierz adres IP węzłów dozorcy za pomocą polecenia

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Powyższe polecenie na liście wszystkie dozorcami dla mojego klastra

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Pobierz wszystkie adresy IP węzłów dozorcy za pomocą polecenia ping lub można także nawiązać połączenie z dozorcy z węzła głównego przy użyciu nazwy ZK

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Po nawiązaniu połączenia z usługą dozorcy wykonaj następujące polecenie, aby wyświetlić listę wszystkich sesji, które próbujesz ponownie uruchomić.

    1. Większość przypadków może to być lista więcej niż 8000 sesji ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Następujące polecenie służy do usuwania wszystkich odzyskiwanych sesji. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Poczekaj na zakończenie powyższego polecenia, a kursor ma zwrócić monit, a następnie uruchom ponownie usługę usługi Livy z Ambari, która powinna zakończyć się powodzeniem.

> [!NOTE]
> `DELETE` sesja usługi Livy po zakończeniu jego wykonywania. Sesje wsadowe usługi Livy nie zostaną usunięte automatycznie zaraz po zakończeniu aplikacji platformy Spark, która jest zaprojektowana. Sesja usługi Livy jest jednostką utworzoną przez żądanie POST na serwerze REST usługi Livy. `DELETE`Do usunięcia tej jednostki jest potrzebne wywołanie. W przeciwnym razie należy poczekać na zakończenie działania GC.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* [Zarządzanie pamięcią Spark — Omówienie](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach usługi HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).