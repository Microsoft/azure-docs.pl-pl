---
title: Apache Hive dzienniki wypełniania miejsca na dysku — usługa Azure HDInsight
description: Dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 5554a66927fc70f22ec552b938ae62038a04acb9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533023"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenariusz: dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów związanych z niewystarczającą ilością miejsca na dyskach głównych w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze Apache Hive/LLAP niepożądane dzienniki zajmują całe miejsce na dysku w węzłach głównych. Z tego powodu mogą wystąpić następujące problemy.

1. Dostęp SSH nie powiedzie się z powodu braku miejsca w węźle głównym.
2. Ambari nadaje *błąd http: usługa 503 jest niedostępna* .
3. Nie można uruchomić ponownie programu serwera hiveserver2 Interactive.

`ambari-agent`Gdy wystąpi problem, dzienniki będą wyglądać następująco.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Przyczyna

W zaawansowanych konfiguracjach Hive-Log4J bieżący domyślny harmonogram usuwania jest ustawiany dla plików starszych niż 30 dni na podstawie daty ostatniej modyfikacji.

## <a name="resolution"></a>Rozwiązanie

1. Przejdź do podsumowania składników programu Hive w portalu Ambari i kliknij `Configs` kartę.

2. Przejdź do `Advanced hive-log4j` sekcji w obszarze Ustawienia zaawansowane.

3. Ustaw `appender.RFA.strategy.action.condition.age` parametr na wybrany wiek. Przykład przez 14 dni: `appender.RFA.strategy.action.condition.age = 14D`

4. Jeśli nie są wyświetlane żadne powiązane ustawienia, należy dołączyć następujące ustawienia.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Ustaw `hive.root.logger` w `INFO,RFA` następujący sposób. Domyślnym ustawieniem jest debugowanie, co sprawia, że dzienniki stają się bardzo duże.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Zapisz konfiguracje i ponownie uruchom wymagane składniki.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).