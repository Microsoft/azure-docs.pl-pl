---
title: NoClassDefFoundError — Apache Spark z danymi Apache Kafka w usłudze Azure HDInsight
description: Apache Spark zadanie przesyłania strumieniowego, które odczytuje dane z klastra Apache Kafka, kończy się niepowodzeniem z NoClassDefFoundError w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946376"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark zadanie przesyłania strumieniowego, które odczytuje Apache Kafka danych kończy się niepowodzeniem z NoClassDefFoundError w usłudze HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Klaster Apache Spark uruchamia zadanie przesyłania strumieniowego Spark, które odczytuje dane z klastra Apache Kafka. Zadanie przesyłania strumieniowego Spark kończy się niepowodzeniem, jeśli kompresja strumienia Kafka jest włączona. W takim przypadku aplikacja przędzy strumienia Spark application_1525986016285_0193 nie powiodła się z powodu błędu:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany określeniem wersji `spark-streaming-kafka` pliku JAR, który jest inny niż wersja klastra Kafka, który jest uruchomiony.

Na przykład jeśli używasz Kafka klastra w wersji 0.10.1, następujące polecenie spowoduje wystąpienie błędu:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Rozwiązanie

Użyj polecenia Spark-Submit z `–packages` opcją i upewnij się, że wersja pliku JAR-Streaming-Kafka jest taka sama jak wersja klastra Kafka, który jest uruchomiony.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]