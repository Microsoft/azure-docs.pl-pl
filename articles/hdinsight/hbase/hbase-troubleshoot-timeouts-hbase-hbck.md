---
title: Przekraczanie limitów czasu polecenia „hbase hbck” w usłudze Azure HDInsight
description: Problem z limitem czasu podczas usuwania przypisań regionów przy użyciu polecenia "HBase hbck"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936702"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenariusz: przekroczenie limitu czasu za pomocą polecenia "HBase hbck" w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Napotkaj limity czasu przy użyciu `hbase hbck` polecenia przy rozwiązywaniu przypisań regionów.

## <a name="cause"></a>Przyczyna

Potencjalną przyczyną problemów z przekroczeniem limitu czasu, gdy użycie `hbck` polecenia może być w stanie "w przejściu" przez długi czas. Te regiony można zobaczyć jako offline w interfejsie użytkownika HBase Master. Ze względu na to, że duża liczba regionów próbuje przejść, HBase Master może przekroczyć limit czasu i nie można przywrócić tych regionów z powrotem do trybu online.

## <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do klastra HBase usługi HDInsight przy użyciu protokołu SSH.

1. Uruchom `hbase zkcli` polecenie, aby nawiązać połączenie z powłoką Apache ZooKeeper.

1. Uruchom `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` polecenie lub.

1. Wyjdź z `hbase zkcli` powłoki przy użyciu `exit` polecenia.

1. W interfejsie użytkownika Apache Ambari Uruchom ponownie usługę Active HBase Master.

1. Uruchom polecenie `hbase hbck -fixAssignments`.

1. Aby upewnić się, że żaden region nie jest zablokowany HBase Master, należy go monitorować w sekcji "region w przejściu".

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).