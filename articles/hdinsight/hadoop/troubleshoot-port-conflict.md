---
title: Konflikt portów podczas uruchamiania usług w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów z konfliktami portów podczas współpracy z klastrami usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943098"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Scenariusz: konflikt portów podczas uruchamiania usług w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Uruchomienie usługi nie powiedzie się.

## <a name="cause"></a>Przyczyna

Istnieje konflikt portów.

## <a name="resolution"></a>Rozwiązanie

### <a name="method-1"></a>Metoda 1

Użyj poniższych poleceń, aby pobrać/skasować wszystkie uruchomione procesy, których dotyczy problem z portem.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Następnie uruchom usługę.

### <a name="method-2"></a>Metoda 2

Uruchom ponownie węzeł.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).