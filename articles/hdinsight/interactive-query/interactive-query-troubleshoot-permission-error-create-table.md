---
title: Błąd odmowy uprawnień w tabeli Apache Hive w usłudze Azure HDInsight
description: Wystąpił błąd podczas próby utworzenia tabeli Apache Hive w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288903"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenariusz: Wystąpił błąd podczas próby utworzenia tabeli Apache Hive w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby utworzenia tabeli zostanie wyświetlony następujący błąd:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Jeśli uruchomisz następujące polecenie magazynu HDFS, zobaczysz podobny komunikat o błędzie:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Przyczyna

Możliwość tworzenia tabeli w Apache Hive jest podejmowana na podstawie uprawnień zastosowanych do konta magazynu klastra. Jeśli uprawnienia konta magazynu klastra są nieprawidłowe, nie będzie można tworzyć tabel. Oznacza to, że można mieć odpowiednie zasady Rangerymi dla tworzenia tabel i nadal pojawiają się błędy "odmowa uprawnień".

## <a name="resolution"></a>Rozwiązanie

Jest to spowodowane brakiem wystarczających uprawnień do używanego kontenera magazynu. Użytkownik tworzący tabelę programu Hive musi mieć uprawnienia Odczyt, zapis i wykonywanie względem kontenera. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące autoryzacji Hive przy użyciu platformy Apache Ranger w HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]