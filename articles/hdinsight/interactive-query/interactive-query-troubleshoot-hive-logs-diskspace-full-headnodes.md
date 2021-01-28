---
title: 'Rozwiązywanie problemów: dzienniki Apache Hive zapełnianie miejsca na dysku — usługa Azure HDInsight'
description: W tym artykule przedstawiono kroki rozwiązywania problemów, które należy wykonać, gdy dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930954"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenariusz: dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów związanych z niewystarczającą ilością miejsca na dyskach głównych w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze Apache Hive/LLAP niepożądane dzienniki zajmują całe miejsce na dysku w węzłach głównych. Ten stan może spowodować następujące problemy:

- Dostęp SSH nie powiódł się, ponieważ w węźle głównym nie ma miejsca.
- Ambari zgłasza *błąd http: usługa 503 jest niedostępna*.
- Nie można uruchomić ponownie programu serwera hiveserver2 Interactive.

W `ambari-agent` przypadku wystąpienia problemu dzienniki będą zawierać następujące wpisy:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Przyczyna

W zaawansowanych konfiguracjach programu Hive Log4J bieżący domyślny harmonogram usuwania polega na usunięciu plików starszych niż 30 dni na podstawie daty ostatniej modyfikacji.

## <a name="resolution"></a>Rozwiązanie

1. Przejdź do podsumowania składnika Hive w portalu Ambari i **Wybierz kartę konfiguracje** .

2. Przejdź do `Advanced hive-log4j` sekcji w obszarze **Ustawienia zaawansowane**.

3. Ustaw `appender.RFA.strategy.action.condition.age` parametr na wybrany wiek. Ten przykład ustawi okres ważności na 14 dni: `appender.RFA.strategy.action.condition.age = 14D`

4. Jeśli nie widzisz żadnych powiązanych ustawień, Dołącz te ustawienia:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Ustaw `hive.root.logger` na `INFO,RFA` , jak pokazano w poniższym przykładzie. Ustawieniem domyślnym jest `DEBUG` , co sprawia, że dzienniki są duże.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Zapisz konfiguracje i ponownie uruchom wymagane składniki.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
