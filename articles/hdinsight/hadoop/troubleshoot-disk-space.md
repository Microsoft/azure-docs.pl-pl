---
title: Zarządzanie miejscem na dysku w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania dotyczące zarządzania problemami z miejscem na dysku podczas korzystania z klastrów usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944818"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Zarządzanie miejscem na dysku w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="hive-log-configurations"></a>Konfiguracje dzienników Hive

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Przejdź do   >  **konfiguracji** Hive Advanced  >    >  **Advanced Hive-Log4J**. Zapoznaj się z następującymi ustawieniami:

    * `hive.root.logger=DEBUG,RFA`. Jest to wartość domyślna, modyfikując [poziom dziennika](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) w `INFO` celu drukowania mniejszych wpisów dzienników.

    * `log4jhive.log.maxfilesize=1024MB`. Jest to wartość domyślna, a w razie potrzeby zmodyfikuj ją.

    * `log4jhive.log.maxbackupindex=10`. Jest to wartość domyślna, a w razie potrzeby zmodyfikuj ją. Jeśli parametr został pominięty, wygenerowane pliki dziennika będą nieograniczone.

## <a name="yarn-log-configurations"></a>Konfiguracje dzienników przędzy

Przejrzyj następujące konfiguracje:

* Apache Ambari

    1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

    1. Przejdź do konfiguracji programu **Hive**—  >    >  **Zaawansowane**  >  **Menedżer zasobów**. Upewnij się, że jest zaznaczone pole wyboru **Włącz agregację dzienników** . Jeśli ta funkcja jest wyłączona, węzły nazw przechowują dzienniki lokalnie i nie agregują ich w magazynie zdalnym po ukończeniu lub zakończeniu działania aplikacji.

* Upewnij się, że rozmiar klastra jest odpowiedni dla obciążenia. Obciążenie mogło być niedawno zmienione lub zmieniono rozmiar klastra. [Skaluj](../hdinsight-scaling-best-practices.md) klaster w górę w celu dopasowania go do większego obciążenia.

* `/mnt/resource` mogą być wypełnione oddzielone pliki (tak jak w przypadku ponownego uruchomienia Menedżera zasobów). W razie potrzeby ręczne czyszczenie `/mnt/resource/hadoop/yarn/log` i `/mnt/resource/hadoop/yarn/local` .

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).