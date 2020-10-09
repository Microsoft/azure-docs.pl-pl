---
title: Apache Hive dzienniki wypełniania miejsca na dysku — usługa Azure HDInsight
description: Dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78943970"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenariusz: dzienniki Apache Hive zajmują miejsce na dysku w węzłach głównych w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów związanych z niewystarczającą ilością miejsca na dyskach głównych w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

W klastrze Apache Hive/LLAP niepożądane dzienniki zajmują całe miejsce na dysku w węzłach głównych. Z tego powodu mogą wystąpić następujące problemy.

1. Dostęp SSH nie powiedzie się z powodu braku miejsca w węźle głównym.
2. Ambari nadaje *błąd http: usługa 503 jest niedostępna*.

`ambari-agent`Gdy wystąpi problem, dzienniki będą wyglądać następująco.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Przyczyna

W zaawansowanych konfiguracjach Hive-Log4J parametr *Log4J. appender. RFA. MaxBackupIndex* został pominięty. Powoduje to nieskończoną generację plików dziennika.

## <a name="resolution"></a>Rozwiązanie

1. Przejdź do podsumowania składników programu Hive w portalu Ambari i kliknij `Configs` kartę.

2. Przejdź do `Advanced hive-log4j` sekcji w obszarze Ustawienia zaawansowane.

3. Ustaw `log4j.appender.RFA` parametr jako RollingFileAppender. 

4. Ustaw `log4j.appender.RFA.MaxFileSize` i `log4j.appender.RFA.MaxBackupIndex` w następujący sposób.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
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

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
