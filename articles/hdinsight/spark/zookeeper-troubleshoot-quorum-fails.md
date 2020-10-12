---
title: Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight
description: Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: 9038630a2623a8b20ddfcf98899ce9a89f16bdc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84673364"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Serwer Apache ZooKeeper nie może utworzyć kworum w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów związanych z Dozorcami w klastrach usługi Azure HDInsight.

## <a name="symptoms"></a>Objawy

* Menedżer zasobów przejdzie do trybu wstrzymania
* Namenodes są w trybie wstrzymania
* Zadania platformy Spark, Hive i przędzy lub zapytania Hive kończą się niepowodzeniem z powodu błędów połączenia dozorcy
* Nie można uruchomić demonów LLAP w przypadku bezpiecznych klastrów Hive lub bezpiecznych interaktywnych.

## <a name="sample-log"></a>Przykładowy dziennik

W dziennikach przędzy może zostać wyświetlony komunikat o błędzie podobny do następującego:

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Pokrewne problemy

* Usługi wysokiej dostępności, takie jak przędza, NameNode i usługi Livy, mogą się z wielu powodów.
* Potwierdź, że dzienniki są powiązane z połączeniami dozorcy
* Upewnij się, że problem występuje wielokrotnie (nie używaj tych rozwiązań w jednym przypadku)
* Zadania mogą tymczasowo kończyć się niepowodzeniem z powodu problemów z połączeniem dozorcy

## <a name="common-causes-for-zookeeper-failure"></a>Typowe przyczyny niepowodzenia dozorcy

* Duże użycie procesora CPU na serwerach dozorcy
  * W interfejsie użytkownika Ambari, jeśli widzisz niemal 100% czasu użycia procesora CPU na serwerach dozorcy, sesje dozorcy otwarte w tym czasie mogą wygasnąć i przekroczyć limit czasu
* Klienci dozorcy są raportowane częstych limitów czasu
  * W dziennikach dla Menedżer zasobów, Namenode i innych, zobaczysz częste limity czasu połączenia klienta
  * Może to spowodować utratę kworum, częste przełączanie w tryb failover oraz inne problemy

## <a name="check-for-zookeeper-status"></a>Sprawdź stan usługi dozorcy

* Znajdź serwery dozorcy z pliku/etc/hosts lub z interfejsu użytkownika Ambari
* Uruchom następujące polecenie
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (lub 2182)  
  * Port 2181 to wystąpienie Apache dozorcy
  * Port 2182 jest używany przez dozorcy usługi HDInsight (w celu zapewnienia wysokiej dostępności dla usług, które nie są natywnie HA)
  * Jeśli polecenie nie wyświetla danych wyjściowych, oznacza to, że serwery dozorcy nie są uruchomione
  * Jeśli serwery są uruchomione, wynik będzie zawierać statyczne połączenia klienckie i inne dane statystyczne

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>Obciążenie procesora CPU w górę co godzinę

* Zaloguj się do serwera dozorcy i sprawdź/etc/crontab
* Jeśli w tym momencie są uruchomione jakieś zadania godzinowe, należy losowo przekroczyć czas rozpoczęcia między różnymi serwerami dozorcy.
  
## <a name="purging-old-snapshots"></a>Przeczyszczanie starych migawek

* Dozorcami są skonfigurowane do autoprzeczyszczania starych migawek
* Domyślnie ostatnie 30 migawek są zachowywane
* Liczba zachowywanych migawek jest kontrolowana przez klucz konfiguracji `autopurge.snapRetainCount` . Tę właściwość można znaleźć w następujących plikach:
  * `/etc/zookeeper/conf/zoo.cfg` w przypadku usługi Hadoop dozorcy
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` dla usługi HDInsight dozorcy
* Ustaw `autopurge.snapRetainCount` wartość 3 i ponownie uruchom serwery dozorcy
  * Konfigurację usługi Hadoop dozorcy można zaktualizować, a usługa może zostać ponownie uruchomiona za pomocą Ambari
  * Ręczne zatrzymywanie i ponowne uruchamianie usługi HDInsight dozorcy
    * `sudo lsof -i :2182` podaje identyfikator procesu do zabicia
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Nie Przeczyść migawek ręcznie — ręczne usuwanie migawek może spowodować utratę danych

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException w dzienniku serwera dozorcy nie wymaga oczyszczania migawek

* Ten wyjątek będzie widoczny na serwerach dozorcy (/var/log/Zookeeper/Zookeeper-Zookeeper-* lub/var/log/HDInsight-Zookeeper/Zookeeper * plików)
* Ten wyjątek zwykle oznacza, że klient nie jest już aktywny i serwer nie może wysłać komunikatu
* Ten wyjątek wskazuje również, że klient dozorcy kończy przedwcześnie sesje
* Poszukaj innych objawów przedstawionych w tym dokumencie

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).
- Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.
- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
