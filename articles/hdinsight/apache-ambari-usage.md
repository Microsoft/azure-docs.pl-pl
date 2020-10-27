---
title: Użycie oprogramowania Apache Ambari w usłudze Azure HDInsight
description: Omówienie sposobu używania oprogramowania Apache Ambari w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: e7c5ca1ce314c85162986591b2cd83907811bf72
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542390"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Użycie oprogramowania Apache Ambari w usłudze Azure HDInsight

Usługa HDInsight używa platformy Apache Ambari do wdrażania klastrów i zarządzania nimi. Agenci Ambari są uruchamiani w każdym węźle (węzła głównego, Node-Worker, dozorcy i edgenode, jeśli istnieje). Serwer Ambari działa tylko na węzła głównego (hn0 lub hn1). Tylko jedno wystąpienie serwera Ambari jest uruchamiane jednocześnie. Jest to kontrolowane przez kontroler trybu failover usługi HDInsight. Gdy jedna z węzłów głównych nie działa do ponownego uruchomienia lub konserwacji, drugi węzła głównego stanie się aktywny, a Ambari serwer na drugim węzła głównego zostanie uruchomiony.

Wszystkie konfiguracje klastra należy wykonać za pomocą [interfejsu użytkownika Ambari](./hdinsight-hadoop-manage-ambari.md), a każda zmiana lokalna zostanie nadpisywana po ponownym uruchomieniu węzła.

## <a name="failover-controller-services"></a>Usługi kontrolera trybu failover

Kontroler trybu failover usługi HDInsight jest również odpowiedzialny za aktualizowanie adresu IP hosta węzła głównego, który wskazuje bieżący aktywny węzeł główny. Wszyscy agenci Ambari są skonfigurowani do zgłaszania stanu i pulsu do hosta węzła głównego. Kontroler trybu failover jest zestawem usług uruchomionych w każdym węźle w klastrze, jeśli nie są uruchomione, węzła głównego trybu failover może nie działać poprawnie i podczas próby dostępu do serwera Ambari zostanie wyświetlony protokół HTTP 502.

Aby sprawdzić, który węzła głównego jest aktywny, jeden z nich to SSH do jednego z węzłów w klastrze, a następnie uruchom `ping headnodehost` i porównaj adres IP z dwoma węzłów głównych.

Jeśli usługi kontrolera trybu failover nie są uruchomione, węzła głównego trybu failover może nie nastąpić poprawnie, co może spowodować, że nie działa serwer Ambari. Aby sprawdzić, czy usługi kontrolera trybu failover są uruchomione, wykonaj następujące działania:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Dzienniki

Na stronie Active węzła głównego można sprawdzić dzienniki serwera Ambari w:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Na dowolnym węźle klastra można sprawdzić dzienniki agenta Ambari w:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Sekwencje uruchomienia usługi

Jest to sekwencja uruchamiania usługi podczas rozruchu:

1. HDInsight — Agent uruchamia usługi kontrolera trybu failover.
1. Usługi kontrolera trybu failover uruchamiają agenta Ambari na każdym węźle i serwerze Ambari na aktywnym węzła głównego.

## <a name="ambari-database"></a>Baza danych Ambari

Usługa HDInsight tworzy bazę danych w SQL Database na podstawie okapu, która będzie działać jako baza danych dla serwera Ambari. Domyślną [warstwą usług jest S0](../azure-sql/database/elastic-pool-scale.md).

W przypadku każdego klastra z liczbą węzłów procesu roboczego większą niż 16 podczas tworzenia klastra S2 jest warstwą usługi bazy danych.

## <a name="takeaway-points"></a>Punkty wnioskiem

Nigdy nie uruchamiaj ręcznie/Zatrzymaj usługi Ambari-Server lub Ambari-Agent, chyba że próbujesz ponownie uruchomić usługę, aby obejść problem. Aby wymusić przejście w tryb failover, można ponownie uruchomić aktywny węzła głównego.

Nigdy nie należy ręcznie modyfikować żadnych plików konfiguracji w żadnym węźle klastra, pozwól, aby interfejs użytkownika Ambari wykonał to zadanie.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).