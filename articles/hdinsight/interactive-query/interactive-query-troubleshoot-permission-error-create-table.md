---
title: Błąd odmowy uprawnień w tabeli Apache Hive w usłudze Azure HDInsight
description: Wystąpił błąd podczas próby utworzenia tabeli Apache Hive w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: a05d0d3361c412e68589728850c33fb47f8b36e7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532921"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scenariusz: Wystąpił błąd podczas próby utworzenia tabeli Apache Hive w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Podczas próby utworzenia tabeli zostanie wyświetlony następujący błąd:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Jeśli uruchomisz następujące polecenie magazynu HDFS, zobaczysz podobny komunikat o błędzie:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Przyczyna

Możliwość tworzenia tabeli w Apache Hive jest podejmowana na podstawie uprawnień zastosowanych do konta magazynu klastra. Jeśli uprawnienia konta magazynu klastra są nieprawidłowe, nie będzie można tworzyć tabel. Oznacza to, że można mieć odpowiednie zasady Rangerymi dla tworzenia tabel i nadal pojawiają się błędy "odmowa uprawnień".

## <a name="resolution"></a>Rozwiązanie

Jest to spowodowane brakiem wystarczających uprawnień do używanego kontenera magazynu. Użytkownik tworzący tabelę programu Hive musi mieć uprawnienia Odczyt, zapis i wykonywanie względem kontenera. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące autoryzacji Hive przy użyciu platformy Apache Ranger w HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).