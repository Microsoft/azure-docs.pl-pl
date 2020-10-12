---
title: Apache Hive wyświetlenia z wyniku zapytania — Azure HDInsight
description: Limit czasu wyświetlania Apache Hive podczas pobierania wyników zapytania w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f00f70e674ac0b83b737d6b2a4bf9d20400736fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77672025"
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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
