---
title: 'Monitorowanie: Apache Ambari & Azure Monitor dzienników — Azure HDInsight'
description: Informacje na temat używania dzienników Ambari i Azure Monitor do monitorowania kondycji i dostępności klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060182"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Jak monitorować dostępność klastra za pomocą programu Apache Ambari i dzienników Azure Monitor

Klastry usługi HDInsight obejmują zarówno platformę Apache Ambari, która udostępnia informacje o kondycji w skrócie i wstępnie zdefiniowane alerty, jak również integrację dzienników Azure Monitor, która udostępnia metryki i dzienniki queryable, a także konfigurowalne alerty.

W tym dokumencie przedstawiono sposób użycia tych narzędzi do monitorowania klastra i przedstawiono kilka przykładów dotyczących konfigurowania alertu Ambari, monitorowania częstotliwości dostępności węzłów oraz tworzenia alertu Azure Monitor, który jest uruchamiany, gdy puls nie został odebrany z co najmniej jednego węzła w ciągu pięciu godzin.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Pulpit nawigacyjny

Dostęp do pulpitu nawigacyjnego Ambari można uzyskać, wybierając link **macierzysty Ambari** w sekcji **pulpity nawigacyjne** w usłudze HDInsight w temacie Omówienie Azure Portal jak pokazano poniżej. Dostęp do niego można także uzyskać, przechodząc do `https://CLUSTERNAME.azurehdinsight.net` okna przeglądarki, gdzie ClusterName jest nazwą klastra.

![Widok portalu zasobów usługi HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika i hasła logowania do klastra. Wprowadź poświadczenia wybrane podczas tworzenia klastra.

Następnie nastąpi przekierowanie do pulpitu nawigacyjnego Ambari, który zawiera elementy widget, które pokazują kilku metryk, aby szybko zapoznać się z kondycją klastra usługi HDInsight. Te widżety pokazują takie metryki jak liczba aktywnych węzłów danych (węzłów procesu roboczego) i JournalNodes (węzeł dozorcy), czas działania NameNodes (węzły główne), a także metryki charakterystyczne dla określonych typów klastrów, takie jak czas pracy zasobów związanych z ResourceManager dla klastrów Spark i Hadoop.

![Ekran pulpitu nawigacyjnego używania oprogramowania Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosty — Wyświetlanie stanu poszczególnych węzłów

Możesz również wyświetlić informacje o stanie poszczególnych węzłów. Wybierz kartę **hosty** , aby wyświetlić listę wszystkich węzłów w klastrze i wyświetlić podstawowe informacje o każdym węźle. Zielona kontrola po lewej stronie każdej nazwy węzła wskazuje, że wszystkie składniki znajdują się w węźle. Jeśli składnik nie działa w węźle, zobaczysz czerwony trójkąt alertu zamiast zielonego sprawdzenia.

![Widok hostów usługi HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Następnie można wybrać **nazwę** węzła, aby wyświetlić bardziej szczegółowe metryki hosta dla danego węzła. Ten widok przedstawia stan/dostępność każdego pojedynczego składnika.

![Widok pojedynczego węzła w programie Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Alerty Ambari

Ambari oferuje również kilka konfigurowalnych alertów, które mogą udostępniać powiadomienia o określonych zdarzeniach. Gdy alerty są wyzwalane, są wyświetlane w lewym górnym rogu Ambari w czerwonym wskaźniku zawierającym liczbę alertów. Wybranie tego wskaźnika powoduje wyświetlenie listy bieżących alertów.

![Liczba bieżących alertów Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Aby wyświetlić listę definicji alertów i ich Stanów, wybierz kartę **alerty** , jak pokazano poniżej.

![Widok definicji alertów Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari oferuje wiele wstępnie zdefiniowanych alertów dotyczących dostępności, w tym:

| Nazwa alertu                        | Opis   |
|---|---|
| Podsumowanie kondycji węzła datanode           | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją węzły datanodes o złej kondycji|
| Kondycja NameNode wysokiej dostępności | Ten alert na poziomie usługi jest wyzwalany, jeśli aktywne NameNode lub wstrzymanie NameNode nie jest uruchomione.|
| Procent dostępnych JournalNodes    | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki testów procesu JournalNode. |
| Dostępne węzły datanodes       | Ten alert jest wyzwalany, jeśli liczba węzłów danych w dół w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu elementu datanode.|

Pełna lista alertów Ambari, które ułatwiają monitorowanie dostępności klastra, można znaleźć [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Aby wyświetlić szczegóły alertu lub zmodyfikować kryteria, wybierz **nazwę** alertu. Zapoznaj się z przykładem **Podsumowanie kondycji węzła** . Można wyświetlić opis alertu, a także określone kryteria, które będą wyzwalać alert "ostrzeżenie" lub "krytyczny" oraz interwał sprawdzania dla kryteriów. Aby edytować konfigurację, wybierz przycisk **Edytuj** w prawym górnym rogu pola konfiguracji.

![Konfiguracja alertu Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Tutaj można edytować opis i, co ważniejsze, interwał sprawdzania i progi alertów ostrzegawczych lub krytycznych.

![Widok edycji alertów Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

W tym przykładzie można sprawić, że 2 węzły w złej kondycji wyzwalają alert krytyczny, a 1 element datanode w złej kondycji wyzwala ostrzeżenie. Wybierz pozycję **Zapisz** po zakończeniu edycji.

### <a name="email-notifications"></a>Powiadomienia e-mail

Opcjonalnie możesz również skonfigurować powiadomienia e-mail dotyczące alertów Ambari. Aby to zrobić, na karcie **alerty** kliknij przycisk **Akcje** w lewym górnym rogu, a następnie **Zarządzaj powiadomieniami.**

![Akcja zarządzania Ambariami](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Zostanie otwarte okno dialogowe umożliwiające zarządzanie powiadomieniami o alertach. Zaznacz pole **+** wyboru u dołu okna dialogowego i wypełnij pola wymagane, aby podać Ambari z informacjami o serwerze poczty e-mail, z którego mają być wysyłane wiadomości e-mail.

> [!TIP]
> Konfigurowanie powiadomień e-mail Ambari może być dobrym sposobem na otrzymywanie alertów w jednym miejscu podczas zarządzania wieloma klastrami usługi HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integracja dzienników Azure Monitor

Dzienniki Azure Monitor umożliwiają gromadzenie i agregowanie danych wygenerowanych przez wiele zasobów, takich jak klastry usługi HDInsight, w celu zapewnienia ujednoliconego środowiska monitorowania.

Jako warunek wstępny potrzebny będzie obszar roboczy Log Analytics do przechowywania zebranych danych. Jeśli jeszcze tego nie zrobiono, możesz wykonać poniższe instrukcje: [Utwórz obszar roboczy log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Włącz integrację dzienników Azure Monitor usługi HDInsight

Na stronie zasób klastra usługi HDInsight w portalu wybierz pozycję **Azure monitor**. Następnie wybierz pozycję **Włącz** , a następnie wybierz obszar roboczy log Analytics z listy rozwijanej.

![Pakiet Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Zapytanie dotyczące metryk i tabel dzienników

Po włączeniu integracji dzienników Azure Monitor (może to potrwać kilka minut) przejdź do zasobów **obszaru roboczego log Analytics** i wybierz pozycję **dzienniki**.

![Log Analytics dzienników obszaru roboczego](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Rejestruje listę przykładowych zapytań, takich jak:

| Nazwa zapytania                      | Opis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostępność komputerów dzisiaj    | Wykres przedstawiający liczbę komputerów, które wysyłają dzienniki, co godzinę                     |
| Wyświetl pulsy                 | Wyświetl listę wszystkich pulsów komputerów z ostatniej godziny                           |
| Ostatni puls każdego komputera | Pokaż ostatni puls Wysłany przez każdy komputer                             |
| Niedostępne komputery           | Wyświetl listę wszystkich znanych komputerów, które nie wysłały pulsu w ostatnich 5 godzinach |
| Szybkość dostępności               | Oblicz szybkość dostępności każdego połączonego komputera                |

Przykładowo Uruchom przykładowe zapytanie o **współczynnik dostępności** , wybierając pozycję **Uruchom** dla tego zapytania, jak pokazano na poniższym zrzucie ekranu. Spowoduje to wyświetlenie wartości procentowej częstotliwości dostępności każdego węzła w klastrze. Jeśli włączono wiele klastrów usługi HDInsight do wysyłania metryk do tego samego obszaru roboczego Log Analytics, zostanie wyświetlona szybkość dostępności dla wszystkich węzłów w tych klastrach.

![Przykładowe zapytanie Log Analytics "szybkość dostępności" dzienników obszaru roboczego](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> Częstotliwość dostępności jest mierzona w okresie 24-godzinnym, więc klaster będzie musiał działać przez co najmniej 24 godziny, zanim zobaczysz dokładne stawki dostępności.

Możesz przypiąć tę tabelę do udostępnionego pulpitu nawigacyjnego, klikając pozycję **Przypnij** w prawym górnym rogu. Jeśli nie masz żadnych zapisywalnych udostępnionych pulpitów nawigacyjnych, możesz zobaczyć, jak utworzyć je w tym miejscu: [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Alerty Azure Monitor

Można również skonfigurować alerty Azure Monitor, które będą wyzwalane, gdy wartość metryki lub wyniki zapytania spełniają określone warunki. Na przykład utwórz alert, aby wysłać wiadomość e-mail, gdy co najmniej jeden węzeł nie wysłał pulsu w ciągu 5 godzin (tj. jest to prawdopodobnie niedostępne).

W obszarze **dzienniki**Uruchom przykładowe zapytanie **niedostępne komputery** , wybierając polecenie **Uruchom** dla tego zapytania, jak pokazano poniżej.

![Przykład dziennika obszaru roboczego "niedostępne komputery" Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Jeśli wszystkie węzły są dostępne, to zapytanie powinno zwrócić teraz zero wyników. Kliknij pozycję **Nowa reguła alertu** , aby rozpocząć konfigurowanie alertu dla tego zapytania.

![Log Analytics nowej regule alertu obszaru roboczego](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Alert zawiera trzy składniki: *zasób* , dla którego ma zostać utworzona reguła (log Analytics w tym przypadku obszar roboczy), *warunek* wyzwalania alertu oraz *grupy akcji* , które określają, co się stanie po wyzwoleniu alertu.
Kliknij **tytuł warunku**, jak pokazano poniżej, aby zakończyć konfigurowanie logiki sygnałów.

![Warunek reguły tworzenia alertu portalu](media/hdinsight-cluster-availability/portal-condition-title.png)

Spowoduje to otwarcie **konfiguracji logiki sygnałów**.

Skonfiguruj sekcję **logika alertów** w następujący sposób:

*Na podstawie: liczba wyników, warunek: większe niż, próg: 0.*

Ponieważ to zapytanie zwraca tylko niedostępne węzły jako wyniki, jeśli liczba wyników jest większa niż 0, alert powinien zostać wywołany.

W sekcji **oceniane na podstawie** Określ **okres** i **częstotliwość** w zależności od tego, jak często chcesz sprawdzać dostępność niedostępnych węzłów.

Na potrzeby tego alertu należy się upewnić, że **okres = częstotliwość.** Więcej informacji na temat okresu, częstotliwości i innych parametrów alertu można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Po zakończeniu konfigurowania logiki sygnałów wybierz pozycję **gotowe** .

![Reguła alertu konfiguruje logikę sygnału](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Jeśli nie masz jeszcze istniejącej grupy akcji, kliknij pozycję **Utwórz nową** w sekcji **grupy akcji** .

![Reguła alertu tworzy nową grupę akcji](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Spowoduje to otwarcie **grupy akcji Dodaj**. Wybierz **nazwę grupy akcji**, **krótką nazwę**, **subskrypcję**i **grupę zasobów.** W sekcji **Akcje** wybierz **nazwę akcji** i wybierz pozycję **email/SMS/push/Voice** jako **Typ akcji.**

> [!NOTE]
> Istnieje kilka innych akcji, które mogą być wyzwalane przez alert oprócz wiadomości E-mail/SMS/wypychania/głosu, takich jak usługa Azure Functions, LogicApp, webhook, narzędzia ITSM i Automatyzacja. [Dowiedz się więcej.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Spowoduje to otwarcie **wiadomości e-mail/SMS/wypychania/głosu**. Wybierz **nazwę** odbiorcy, **zaznacz** pole adres **e-mail** , a następnie wpisz adres e-mail, na który ma być wysyłany alert. Wybierz pozycję **OK** w **wiadomości e-mail/SMS/wypychanie/głos**, a następnie w obszarze **Dodaj grupę akcji** , aby zakończyć konfigurowanie grupy akcji.

![Reguła alertu tworzy grupę akcji.](media/hdinsight-cluster-availability/portal-add-action-group.png)

Po zamknięciu tych zamków powinna zostać wyświetlona grupa akcji wymieniona w sekcji **grupy akcji** . Na koniec Wypełnij sekcję **szczegóły alertu** , wpisując nazwę i **Opis** **reguły alertu** i wybierając **ważność**. Kliknij przycisk **Utwórz regułę alertu** , aby zakończyć.

![Portal tworzy zakończenie reguły alertu](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> Możliwość określania **ważności** jest zaawansowanym narzędziem, które może być używane podczas tworzenia wielu alertów. Na przykład można utworzyć jeden alert, aby zgłosić ostrzeżenie (ważność 1), jeśli jeden węzeł główny ulegnie awarii i inny alert, który podnosi krytyczne (ważność 0) w prawdopodobnym zdarzeniu, że oba węzły główne przechodzą.

Po spełnieniu warunku tego alertu alert zostanie uruchomiony i otrzymasz wiadomość e-mail z informacjami o alercie, takimi jak:

![Przykład wiadomości e-mail dotyczącej alertu Azure Monitor](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Możesz również wyświetlić wszystkie alerty, które zostały wywołane, pogrupowane według ważności, przechodząc do **alertów** w **obszarze roboczym log Analytics**.

![Log Analytics alertów obszaru roboczego](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Wybranie grupowania ważności (tj. **ważność 1,** jak zostało wyróżnione powyżej) spowoduje wyświetlenie rekordów dla wszystkich alertów o ważności, które zostały wywołane jak poniżej:

![Ważność obszar roboczy Log Analytics jeden alert](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Następne kroki

- [Dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight](hdinsight-high-availability-linux.md)
