---
title: Bindexception — adres jest już używany w usłudze Azure HDInsight
description: Bindexception — adres jest już używany w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: c0a7bc7629b5c2a2e458ba94d62e341f578fdd25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946392"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scenariusz: powiązanaexception — adres jest już używany w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można wykonać operacji ponownego uruchamiania na serwerze regionu Apache HBase. W `region-server.log` katalogu w w `/var/log/hbase` węzłach procesu roboczego, w którym nie można uruchomić serwera regionów, może zostać wyświetlony komunikat o błędzie podobny do poniższego:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Przyczyna

Ponowne uruchamianie serwerów regionu Apache HBase podczas działania dużego obciążenia. Poniżej znajdują się informacje o tym, co się stanie w tle, gdy użytkownik inicjuje operację ponownego uruchamiania na serwerze regionu HBase z poziomu interfejsu użytkownika Apache Ambari:

1. Agent Ambari wysyła żądanie zatrzymania do serwera regionu.

1. Agent Ambari czeka 30 sekund, aż serwer regionu zostanie bezpiecznie zamknięty

1. Jeśli aplikacja nadal nawiązuje połączenie z serwerem regionu, serwer nie zostanie natychmiast zamknięty. Limit czasu 30 sekund upływa przed zamknięciem.

1. Po 30 sekundach Agent Ambari wysyła polecenie Force-Kill ( `kill -9` ) do serwera regionu.

1. Ze względu na to nieoczekiwane zamknięcie, chociaż proces serwera regionu zostanie zamknięty, port skojarzony z procesem może nie zostać wyrzucony, który ostatecznie prowadzi do `AddressBindException` .

## <a name="resolution"></a>Rozwiązanie

Przed zainicjowaniem ponownego uruchomienia Zmniejsz obciążenie serwerów regionów HBase. Ponadto dobrym pomysłem jest pierwsze opróżnianie wszystkich tabel. Aby uzyskać informacje na temat sposobu opróżniania tabel, zobacz [HDInsight HBase: jak ulepszyć czas ponownego uruchamiania klastra Apache HBase przy użyciu tabel opróżniania](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Alternatywnie spróbuj ręcznie uruchomić ponownie serwery regionów w węzłach procesu roboczego, używając następujących poleceń:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).