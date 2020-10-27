---
title: IllegalArgumentException — Apache Spark błąd usługi Azure HDInsight
description: IllegalArgumentException dla działania Apache Spark w usłudze Azure HDInsight dla Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 334e8d129a7b5bb79c9e01d5ba7f347682e5bd79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545620"
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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).