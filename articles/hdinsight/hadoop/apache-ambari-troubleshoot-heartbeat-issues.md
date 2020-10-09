---
title: Problemy z pulsem usługi Apache Ambari w usłudze Azure HDInsight
description: Przegląd różnych powodów problemów pulsu Apache Ambari w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77057077"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemy z pulsem usługi Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scenariusz: wysokie wykorzystanie procesora CPU

### <a name="issue"></a>Problem

Agent Ambari ma duże użycie procesora CPU, które powoduje utratę alertów z interfejsu użytkownika Ambari, który dla niektórych węzłów zostaje utracony. Alert utracony pulsu jest zwykle przejściowy.

### <a name="cause"></a>Przyczyna

Ze względu na różne błędy agenta Ambari, w rzadkich przypadkach, Agent Ambari — może mieć wysokie wykorzystanie procesora CPU (w pobliżu 100).

### <a name="resolution"></a>Rozwiązanie

1. Określ identyfikator procesu (PID) Ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Następnie uruchom następujące polecenie, aby pokazać użycie procesora CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Uruchom ponownie Ambari agenta, aby rozwiązać problem:

    ```bash
    service ambari-agent restart
    ```

1. Jeśli ponowne uruchomienie nie działa, należy skasować proces Ambari-Agent, a następnie uruchomić go:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenariusz: nie uruchomiono agenta Ambari

### <a name="issue"></a>Problem

Agent Ambari nie został uruchomiony, co spowoduje utratę alertów z interfejsu użytkownika Ambari, który dla niektórych węzłów zostanie utracony pulsu agenta Ambari.

### <a name="cause"></a>Przyczyna

Alerty są spowodowane przez agenta Ambari, który nie jest uruchomiony.

### <a name="resolution"></a>Rozwiązanie

1. Potwierdzenie stanu Ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Potwierdź, że usługi kontrolera trybu failover są uruchomione:

    ```bash
    ps -ef | grep failover
    ```

    Jeśli usługi kontrolera trybu failover nie są uruchomione, prawdopodobnie z powodu problemu uniemożliwienie uruchomienia kontrolera trybu failover przez agenta usługi HDInsight. Sprawdź dziennik HDInsight-Agent z `/var/log/hdinsight-agent/hdinsight-agent.out` pliku.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenariusz: Utracono puls dla Ambari

### <a name="issue"></a>Problem

Agent pulsu Ambari został utracony.

### <a name="cause"></a>Przyczyna

Dzienniki pakietu OMS powodują wysokie wykorzystanie procesora CPU.

### <a name="resolution"></a>Rozwiązanie

* Wyłącz rejestrowanie Azure Monitor przy użyciu polecenia cmdlet [disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) programu PowerShell.
* Usuń `mdsd.warn` plik dziennika

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
