---
title: Problemy z pulsem usługi Apache Ambari w usłudze Azure HDInsight
description: Przegląd różnych powodów problemów pulsu Apache Ambari w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946730"
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

* Wyłącz rejestrowanie Azure Monitor przy użyciu polecenia cmdlet [disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) programu PowerShell.
* Usuń `mdsd.warn` plik dziennika

---

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]