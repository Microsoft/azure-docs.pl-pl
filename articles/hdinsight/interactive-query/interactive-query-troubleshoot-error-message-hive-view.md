---
title: Komunikat o błędzie nie jest wyświetlany w widoku Apache Hive — usługa Azure HDInsight
description: Zapytanie kończy się niepowodzeniem w widoku Apache Hive bez żadnych szczegółów w klastrze usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895216"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenariusz: komunikat o błędzie zapytania nie jest wyświetlany w widoku Apache Hive w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Komunikat o błędzie zapytania widoku Apache Hive będzie wyglądać podobnie do tego, bez dalszych informacji:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Przyczyna

Czasami komunikat o błędzie błędu zapytania może być zbyt duży, aby można go było wyświetlić na stronie głównej widoku programu Hive.

## <a name="resolution"></a>Rozwiązanie

Sprawdź kartę powiadomienia w prawym górnym rogu Hive_view, aby wyświetlić pełną ślad stosu i komunikat o błędzie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
