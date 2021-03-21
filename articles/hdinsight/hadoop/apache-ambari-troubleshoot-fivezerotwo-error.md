---
title: Błąd interfejsu 502 użytkownika Apache Ambari w usłudze Azure HDInsight
description: Błąd interfejsu 502 użytkownika Apache Ambari w przypadku próby uzyskania dostępu do klastra usługi Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946787"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenariusz: błąd interfejsu 502 użytkownika Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W przypadku próby uzyskania dostępu do interfejsu użytkownika Apache Ambari dla klastra usługi HDInsight zostanie wyświetlony komunikat podobny do: "502 — serwer sieci Web odebrał nieprawidłową odpowiedź, działając jako brama lub serwer proxy".

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc, kod stanu HTTP 502 oznacza, że serwer Ambari nie działa prawidłowo na aktywnym węzła głównego. Istnieje kilka możliwych przyczyn głównych.

## <a name="resolution"></a>Rozwiązanie

W większości przypadków, aby wyeliminować problem, można ponownie uruchomić usługę Active węzła głównego. Możesz też wybrać większy rozmiar maszyny wirtualnej dla węzła głównego.

### <a name="ambari-server-failed-to-start"></a>Nie można uruchomić serwera Ambari

Możesz sprawdzić dzienniki serwera Ambari, aby dowiedzieć się, dlaczego nie można uruchomić serwera Ambari. Jednym z typowych przyczyn jest błąd sprawdzania spójności bazy danych. Można to znaleźć w tym pliku dziennika: `/var/log/ambari-server/ambari-server-check-database.log` .

W przypadku wprowadzenia jakichkolwiek modyfikacji w węźle klastra należy je cofnąć. Zawsze używaj interfejsu użytkownika Ambari, aby modyfikować wszystkie konfiguracje związane z usługą Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Serwer Ambari z 100% wykorzystania procesora CPU

W rzadkich sytuacjach proces Ambari-Server został ciągle zbliżony do 100% użycia procesora CPU. Jako środek zaradczy można przeprowadzić protokół SSH do aktywnego węzła głównego i skasować proces serwera Ambari i uruchomić go ponownie.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Serwer Ambari zabity przez OOM — killer

W niektórych scenariuszach węzła głównego zabrakło pamięci, a system Linux OOM-killer zaczyna wybierać procesy do zabicia. Możesz sprawdzić tę sytuację, wyszukując identyfikator procesu AmbariServer, który nie powinien zostać znaleziony. Następnie zapoznaj się z tematem `/var/log/syslog` i poszukaj podobnej zawartości:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Następnie Zidentyfikuj, które procesy zajmują się pamiątkami, a następnie spróbuj zwiększyć główną przyczynę.

### <a name="other-issues-with-ambari-server"></a>Inne problemy z serwerem Ambari

Rzadko serwer Ambari nie może obsłużyć żądania przychodzącego, możesz znaleźć więcej informacji, przeglądając dzienniki Ambari-serwer w poszukiwaniu dowolnego błędu. W takim przypadku jest to błąd podobny do tego:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]