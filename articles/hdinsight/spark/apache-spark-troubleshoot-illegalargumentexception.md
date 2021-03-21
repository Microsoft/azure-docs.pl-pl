---
title: IllegalArgumentException — Apache Spark błąd usługi Azure HDInsight
description: IllegalArgumentException dla działania Apache Spark w usłudze Azure HDInsight dla Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929393"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenariusz: IllegalArgumentException dla działania Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby wykonania działania platformy Spark w potoku Azure Data Factory wystąpił następujący wyjątek:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Przyczyna

Zadanie Spark zakończy się niepowodzeniem, jeśli plik jar aplikacji nie znajduje się w magazynie domyślnym/podstawowym klastra Spark.

Jest to znany problem dotyczący struktury "open source" platformy Spark śledzony w tej usterce: [zadanie Spark kończy się niepowodzeniem, jeśli FS. defaultFS i jar aplikacji mają inny adres URL](https://issues.apache.org/jira/browse/SPARK-22587).

Ten problem został rozwiązany w usłudze Spark 2.3.0.

## <a name="resolution"></a>Rozwiązanie

Upewnij się, że aplikacja jar jest przechowywana w domyślnym/podstawowym magazynie klastra usługi HDInsight. W przypadku Azure Data Factory upewnij się, że połączona usługa ADF znajduje się w domyślnym kontenerze usługi HDInsight, a nie na kontenerze pomocniczym.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]