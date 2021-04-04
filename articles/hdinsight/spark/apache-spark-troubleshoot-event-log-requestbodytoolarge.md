---
title: Błąd RequestBodyTooLarge z aplikacji Apache Spark — usługa Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge pojawia się w dzienniku dla aplikacji przesyłania strumieniowego Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929436"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "pojawia się w Apache Spark dzienniku aplikacji przesyłania strumieniowego w usłudze HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Błąd: `NativeAzureFileSystem ... RequestBodyTooLarge` pojawia się w dzienniku sterowników dla aplikacji Apache Spark Streaming.

## <a name="cause"></a>Przyczyna

Plik dziennika zdarzeń Spark prawdopodobnie zostanie osiągnięty w limicie długości pliku dla WASB.

W platformie Spark 2,3 Każda aplikacja Spark generuje jeden plik dziennika zdarzeń platformy Spark. Plik dziennika zdarzeń Spark dla aplikacji do przesyłania strumieniowego Spark nadal rośnie, gdy aplikacja jest uruchomiona. Dzisiaj plik w WASB ma limit bloków 50000, a domyślny rozmiar bloku to 4 MB. W konfiguracji domyślnej maksymalny rozmiar pliku to 195 GB. Jednak usługa Azure Storage zwiększyła maksymalny rozmiar bloku do 100 MB, co skutecznie przekroczy limit jednego pliku do 4,75 TB. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Rozwiązanie

Dostępne są trzy rozwiązania tego błędu:

* Zwiększ rozmiar bloku do 100 MB. W interfejsie użytkownika Ambari Zmodyfikuj właściwość konfiguracji systemu plików HDFS `fs.azure.write.request.size` (lub utwórz ją w `Custom core-site` sekcji). Ustaw właściwość na większą wartość, na przykład: 33554432. Zapisz zaktualizowaną konfigurację i ponownie uruchom składniki.

* Okresowo zatrzymuje i ponownie przesyła zadanie przetwarzania strumieniowego platformy Spark.

* Przechowywanie dzienników zdarzeń platformy Spark przy użyciu systemu plików HDFS. Korzystanie z systemu plików HDFS dla magazynu może spowodować utratę danych zdarzeń platformy Spark podczas skalowania klastra lub uaktualnień platformy Azure.

    1. Wprowadź zmiany do `spark.eventlog.dir` i `spark.history.fs.logDirectory` za pośrednictwem interfejsu użytkownika Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Tworzenie katalogów w systemie plików HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Uruchom ponownie wszystkie usługi, których to dotyczy, za pomocą interfejsu użytkownika Ambari.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]