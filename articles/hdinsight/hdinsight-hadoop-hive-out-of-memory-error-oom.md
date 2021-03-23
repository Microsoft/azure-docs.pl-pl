---
title: Naprawianie błędu braku pamięci gałęzi w usłudze Azure HDInsight
description: Naprawianie błędu braku pamięci gałęzi w usłudze HDInsight. Scenariusz klient jest kwerendą obejmującą wiele dużych tabel.
keywords: błąd braku pamięci, OOM, ustawienia Hive
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: d1e8f596ee022a59baa89e7f78648c98420eb44b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868872"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Naprawianie błędu braku pamięci Apache Hive w usłudze Azure HDInsight

Dowiedz się, jak naprawić błąd Apache Hive braku pamięci (OOM) podczas przetwarzania dużych tabel przez skonfigurowanie ustawień pamięci programu Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Uruchamianie kwerendy Apache Hive w przypadku dużych tabel

Klient uruchomił zapytanie programu Hive:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Niektóre wszystkie szczegóły tego zapytania:

* T1 jest aliasem dla dużej tabeli, TABELA1, która ma wiele typów kolumn typu STRING.
* Inne tabele nie są duże, ale mają wiele kolumn.
* Wszystkie tabele są wzajemnie łączące się, w niektórych przypadkach z wieloma kolumnami w tabeli TABELA1 i innych.

Zapytanie Hive zajęło 26 minut na zakończenie działania klastra usługi HDInsight z 24 węzłami. Klient zauważył następujące komunikaty ostrzegawcze:

```output
    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product
```

Za pomocą aparatu wykonywania Apache Tez. To samo zapytanie zostało uruchomione przez 15 minut, a następnie wywołało następujący błąd:

```output
    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space
```

Ten błąd pozostaje w przypadku używania większej maszyny wirtualnej (na przykład D12).

## <a name="debug-the-out-of-memory-error"></a>Debugowanie błędu braku pamięci

Nasza pomoc techniczna i zespoły inżynieryjne znalazły jeden z problemów spowodowanych błędem braku pamięci był [znany problem opisany w JIRA Apache](https://issues.apache.org/jira/browse/HIVE-8306):

"When. Auto. Convert. Join. noconditionaltask = true Sprawdzamy noconditionaltask. size, a jeśli sumy tabel w sprzężeniu mapy są mniejsze niż noconditionaltask. rozmiar planu wygeneruje sprzężenie mapy, problem z tym, że obliczenie nie uwzględnia obciążenia wprowadzonego przez inną implementację HashTable jako wyniki, jeśli suma rozmiarów danych wejściowych jest mniejsza niż rozmiar noconditionaltask przez zapytania o małym marginesie, zostanie osiągnięty OOM".

Plik **Hive. Auto. Convert. Join. noconditionaltask** w pliku hive-site.xml został ustawiony na **wartość true**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Prawdopodobnie mapowanie jest przyczyną błędu braku pamięci sterty języka Java. Zgodnie z opisem w blogu [Ustawienia pamięci przędzy usługi Hadoop w usłudze HDInsight](/archive/blogs/shanyu/hadoop-yarn-memory-settings-in-hdinsight), gdy używany jest aparat wykonywania tez, używany obszar sterty faktycznie należy do kontenera tez. Zapoznaj się z poniższym obrazem opisującym pamięć kontenera tez.

:::image type="content" source="./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png" alt-text="Diagram pamięci kontenera tez: błąd gałęzi braku pamięci" border="false":::

Zgodnie z wpisem w blogu poniższe dwa ustawienia pamięci definiują pamięć kontenera dla sterty: **Hive. tez. Container. size** i **Hive. tez. Java.** W naszym środowisku wyjątek braku pamięci oznacza, że rozmiar kontenera jest zbyt mały. Oznacza to, że rozmiar sterty Java (Hive. tez. Java.) jest za mały. W każdym przypadku, gdy zostanie wyświetlona ilość pamięci, możesz spróbować zwiększyć **gałąź Hive. tez. Java.** W razie potrzeby może zajść konieczność zwiększenia **gałęzi Hive. tez. Container. size**. Ustawienie **Java.** monity powinno mieć rozmiar około 80% **kontenera.**

> [!NOTE]  
> Ustawienie **Hive. tez. Java.** funkcja musi być zawsze mniejsze niż **Hive. tez. Container. size**.

Ponieważ maszyna D12 ma 28 GB pamięci, zalecamy użycie rozmiaru kontenera 10 GB (10240 MB) i przypisanie 80% do środowiska Java.

```console
SET hive.tez.container.size=10240
SET hive.tez.java.opts=-Xmx8192m
```

W przypadku nowych ustawień zapytanie zostało wykonane pomyślnie w ciągu 10 minut.

## <a name="next-steps"></a>Następne kroki

Błąd OOM nie musi oznaczać, że rozmiar kontenera jest zbyt mały. Zamiast tego należy skonfigurować ustawienia pamięci, aby zwiększyć rozmiar sterty i wynosić co najmniej 80% rozmiaru pamięci kontenera. Aby zoptymalizować zapytania programu Hive, zobacz [optymalizacja Apache Hive zapytań dotyczących Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-optimize-hive-query.md).