---
title: Apache Hive wyświetlenia z wyniku zapytania — Azure HDInsight
description: Limit czasu wyświetlania Apache Hive podczas pobierania wyników zapytania w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930728"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Scenariusz: limit czasu wyświetlania Apache Hive podczas pobierania wyników zapytania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas uruchamiania określonych zapytań w widoku Apache Hive można napotkać następujący błąd:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Przyczyna

Domyślna wartość limitu czasu widoku programu Hive może nie być odpowiednia dla wykonywanego zapytania. Określony przedział czasu jest zbyt krótki, aby można było pobrać wynik zapytania w widoku programu Hive.

## <a name="resolution"></a>Rozwiązanie

Zwiększ limit czasu widoku Hive programu Apache Ambari przez ustawienie następujących właściwości w `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Wartość `HIVE_VIEW_INSTANCE_NAME` jest dostępna na końcu adresu URL widoku programu Hive.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]