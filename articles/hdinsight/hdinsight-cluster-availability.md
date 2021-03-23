---
title: Jak monitorować dostępność klastra za pomocą platformy Apache Ambari w usłudze Azure HDInsight
description: Dowiedz się, jak używać oprogramowania Apache Ambari do monitorowania kondycji i dostępności klastra.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863534"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Jak monitorować dostępność klastra za pomocą platformy Apache Ambari w usłudze Azure HDInsight

Klastry usługi HDInsight obejmują program Apache Ambari, który udostępnia informacje o kondycji w skrócie i wstępnie zdefiniowanych alertach.

W tym artykule pokazano, jak za pomocą programu Ambari monitorować klaster i zapoznać się z przykładami dotyczącymi konfigurowania alertu Ambari, monitorowania częstotliwości dostępności węzłów oraz tworzenia alertu Azure Monitor, który jest uruchamiany, gdy puls nie został odebrany z co najmniej jednego węzła w ciągu pięciu godzin.

## <a name="dashboard"></a>Pulpit nawigacyjny

Dostęp do pulpitu nawigacyjnego Ambari można uzyskać, wybierając link **macierzysty Ambari** w sekcji **pulpity nawigacyjne** w usłudze HDInsight w temacie Omówienie Azure Portal jak pokazano poniżej. Dostęp do niego można także uzyskać, przechodząc do `https://CLUSTERNAME.azurehdinsight.net` okna przeglądarki, gdzie ClusterName jest nazwą klastra.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="Widok portalu zasobów usługi HDInsight":::

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika i hasła logowania do klastra. Wprowadź poświadczenia wybrane podczas tworzenia klastra.

Następnie nastąpi przekierowanie do pulpitu nawigacyjnego Ambari, który zawiera elementy widget, które pokazują kilku metryk, aby szybko zapoznać się z kondycją klastra usługi HDInsight. Te widżety pokazują takie metryki jak liczba aktywnych węzłów danych (węzłów procesu roboczego) i JournalNodes (węzeł dozorcy), czas działania NameNodes (węzły główne), a także metryki charakterystyczne dla określonych typów klastrów, takie jak czas pracy zasobów związanych z ResourceManager dla klastrów Spark i Hadoop.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Ekran pulpitu nawigacyjnego używania oprogramowania Apache Ambari":::

## <a name="hosts--view-individual-node-status"></a>Hosty — Wyświetlanie stanu poszczególnych węzłów

Możesz również wyświetlić informacje o stanie poszczególnych węzłów. Wybierz kartę **hosty** , aby wyświetlić listę wszystkich węzłów w klastrze i wyświetlić podstawowe informacje o każdym węźle. Zielona kontrola po lewej stronie każdej nazwy węzła wskazuje, że wszystkie składniki znajdują się w węźle. Jeśli składnik nie działa w węźle, zobaczysz czerwony trójkąt alertu zamiast zielonego sprawdzenia.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="Widok hostów usługi HDInsight Apache Ambari":::

Następnie można wybrać **nazwę** węzła, aby wyświetlić bardziej szczegółowe metryki hosta dla danego węzła. Ten widok przedstawia stan/dostępność każdego pojedynczego składnika.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Widok pojedynczego węzła w programie Apache Ambari":::

## <a name="ambari-alerts"></a>Alerty Ambari

Ambari oferuje również kilka konfigurowalnych alertów, które mogą udostępniać powiadomienia o określonych zdarzeniach. Gdy alerty są wyzwalane, są wyświetlane w lewym górnym rogu Ambari w czerwonym wskaźniku zawierającym liczbę alertów. Wybranie tego wskaźnika powoduje wyświetlenie listy bieżących alertów.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Liczba bieżących alertów Apache Ambari":::

Aby wyświetlić listę definicji alertów i ich Stanów, wybierz kartę **alerty** , jak pokazano poniżej.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Widok definicji alertów Ambari":::

Ambari oferuje wiele wstępnie zdefiniowanych alertów dotyczących dostępności, w tym:

| Nazwa alertu                        | Opis   |
|---|---|
| Podsumowanie kondycji węzła datanode           | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją węzły datanodes o złej kondycji|
| Kondycja NameNode wysokiej dostępności | Ten alert na poziomie usługi jest wyzwalany, jeśli aktywne NameNode lub wstrzymanie NameNode nie jest uruchomione.|
| Procent dostępnych JournalNodes    | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki testów procesu JournalNode. |
| Dostępne węzły datanodes       | Ten alert jest wyzwalany, jeśli liczba węzłów danych w dół w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu elementu datanode.|


Aby wyświetlić szczegóły alertu lub zmodyfikować kryteria, wybierz **nazwę** alertu. Zapoznaj się z przykładem **Podsumowanie kondycji węzła** . Można wyświetlić opis alertu, a także określone kryteria, które będą wyzwalać alert "ostrzeżenie" lub "krytyczny" oraz interwał sprawdzania dla kryteriów. Aby edytować konfigurację, wybierz przycisk **Edytuj** w prawym górnym rogu pola konfiguracji.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Konfiguracja alertu Apache Ambari":::

Tutaj można edytować opis i, co ważniejsze, interwał sprawdzania i progi alertów ostrzegawczych lub krytycznych.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Widok edycji alertów Ambari":::

W tym przykładzie można sprawić, że 2 węzły w złej kondycji wyzwalają alert krytyczny, a 1 element datanode w złej kondycji wyzwala ostrzeżenie. Wybierz pozycję **Zapisz** po zakończeniu edycji.

## <a name="email-notifications"></a>Powiadomienia e-mail

Opcjonalnie możesz również skonfigurować powiadomienia e-mail dotyczące alertów Ambari. Aby to zrobić, na karcie **alerty** kliknij przycisk **Akcje** w lewym górnym rogu, a następnie **Zarządzaj powiadomieniami.**

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Akcja zarządzania Ambariami":::

Zostanie otwarte okno dialogowe umożliwiające zarządzanie powiadomieniami o alertach. Zaznacz pole wyboru **+** u dołu okna dialogowego i wypełnij pola wymagane, aby podać Ambari z informacjami o serwerze poczty e-mail, z którego mają być wysyłane wiadomości e-mail.

> [!TIP]
> Konfigurowanie powiadomień e-mail Ambari może być dobrym sposobem na otrzymywanie alertów w jednym miejscu podczas zarządzania wieloma klastrami usługi HDInsight.

## <a name="next-steps"></a>Następne kroki

- [Dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight](./hdinsight-business-continuity.md)
- [Dostępność klastra — dzienniki usługi Azure Monitor](./cluster-availability-monitor-logs.md)
- [Korzystanie z dzienników Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Powiadomienia e-mail w usłudze Apache Ambari](apache-ambari-email.md)
