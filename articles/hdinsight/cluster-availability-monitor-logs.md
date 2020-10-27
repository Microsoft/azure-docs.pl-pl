---
title: Jak monitorować dostępność klastra za pomocą dzienników Azure Monitor w usłudze HDInsight
description: Informacje na temat używania dzienników Azure Monitor do monitorowania kondycji i dostępności klastra.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: f86b2166ea9bd2a547a29a777d6b709877036161
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542543"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Jak monitorować dostępność klastra za pomocą dzienników Azure Monitor w usłudze HDInsight

Klastry usługi HDInsight obejmują integrację dzienników Azure Monitor, która udostępnia metryki i dzienniki Queryable oraz konfigurowalne alerty. W tym artykule pokazano, jak używać Azure Monitor do monitorowania klastra.

## <a name="azure-monitor-logs-integration"></a>Integracja dzienników Azure Monitor

Dzienniki Azure Monitor umożliwiają gromadzenie i agregowanie danych wygenerowanych przez wiele zasobów, takich jak klastry usługi HDInsight, w celu zapewnienia ujednoliconego środowiska monitorowania.

Jako warunek wstępny potrzebny będzie obszar roboczy Log Analytics do przechowywania zebranych danych. Jeśli jeszcze tego nie zrobiono, możesz wykonać poniższe instrukcje: [Utwórz obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Włącz integrację dzienników Azure Monitor usługi HDInsight

Na stronie zasób klastra usługi HDInsight w portalu wybierz pozycję **Azure monitor** . Następnie wybierz pozycję **Włącz** , a następnie wybierz obszar roboczy log Analytics z listy rozwijanej.

![Pakiet Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Domyślnie program pakietu OMS jest instalowany na wszystkich węzłach klastra z wyjątkiem węzłów brzegowych. Ponieważ żaden Agent pakietu OMS nie jest zainstalowany w węzłach brzegowych klastra, domyślnie nie ma żadnych danych telemetrycznych w węzłach brzegowych obecnych w Log Analytics.

## <a name="query-metrics-and-logs-tables"></a>Zapytanie dotyczące metryk i tabel dzienników

Po włączeniu integracji dzienników Azure Monitor (może to potrwać kilka minut) przejdź do zasobów **obszaru roboczego log Analytics** i wybierz pozycję **dzienniki** .

![Log Analytics dzienników obszaru roboczego](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Rejestruje listę przykładowych zapytań, takich jak:

| Nazwa zapytania                      | Opis                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Dostępność komputerów dzisiaj    | Wykres przedstawiający liczbę komputerów, które wysyłają dzienniki, co godzinę                     |
| Wyświetl pulsy                 | Wyświetl listę wszystkich pulsów komputerów z ostatniej godziny                           |
| Ostatni puls każdego komputera | Pokaż ostatni puls Wysłany przez każdy komputer                             |
| Niedostępne komputery           | Wyświetl listę wszystkich znanych komputerów, które nie wysłały pulsu w ostatnich 5 godzinach |
| Szybkość dostępności               | Oblicz szybkość dostępności każdego połączonego komputera                |

Przykładowo Uruchom przykładowe zapytanie o **współczynnik dostępności** , wybierając pozycję **Uruchom** dla tego zapytania, jak pokazano na poniższym zrzucie ekranu. Spowoduje to wyświetlenie wartości procentowej częstotliwości dostępności każdego węzła w klastrze. Jeśli włączono wiele klastrów usługi HDInsight do wysyłania metryk do tego samego obszaru roboczego Log Analytics, zobaczysz stawkę dostępności dla wszystkich węzłów (z wyjątkiem węzłów brzegowych) w tych klastrach.

![Przykładowe zapytanie Log Analytics "szybkość dostępności" dzienników obszaru roboczego](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> Częstotliwość dostępności jest mierzona w okresie 24-godzinnym, więc klaster będzie musiał działać przez co najmniej 24 godziny, zanim zobaczysz dokładne stawki dostępności.

Możesz przypiąć tę tabelę do udostępnionego pulpitu nawigacyjnego, klikając pozycję **Przypnij** w prawym górnym rogu. Jeśli nie masz żadnych zapisywalnych udostępnionych pulpitów nawigacyjnych, możesz zobaczyć, jak utworzyć je w tym miejscu: [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Alerty Azure Monitor

Można również skonfigurować alerty Azure Monitor, które będą wyzwalane, gdy wartość metryki lub wyniki zapytania spełniają określone warunki. Na przykład utwórz alert, aby wysłać wiadomość e-mail, gdy co najmniej jeden węzeł nie wysłał pulsu w ciągu 5 godzin (tj. jest to prawdopodobnie niedostępne).

W obszarze **dzienniki** Uruchom przykładowe zapytanie **niedostępne komputery** , wybierając polecenie **Uruchom** dla tego zapytania, jak pokazano poniżej.

![Przykład dziennika obszaru roboczego "niedostępne komputery" Log Analytics](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Jeśli wszystkie węzły są dostępne, to zapytanie powinno zwrócić teraz zero wyników. Kliknij pozycję **Nowa reguła alertu** , aby rozpocząć konfigurowanie alertu dla tego zapytania.

![Log Analytics nowej regule alertu obszaru roboczego](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Alert zawiera trzy składniki: *zasób* , dla którego ma zostać utworzona reguła (log Analytics w tym przypadku obszar roboczy), *warunek* wyzwalania alertu oraz *grupy akcji* , które określają, co się stanie po wyzwoleniu alertu.
Kliknij **tytuł warunku** , jak pokazano poniżej, aby zakończyć konfigurowanie logiki sygnałów.

![Warunek reguły tworzenia alertu portalu](media/cluster-availability-monitor-logs/portal-condition-title.png)

Spowoduje to otwarcie **konfiguracji logiki sygnałów** .

Skonfiguruj sekcję **logika alertów** w następujący sposób:

*Na podstawie: liczba wyników, warunek: większe niż, próg: 0.*

Ponieważ to zapytanie zwraca tylko niedostępne węzły jako wyniki, jeśli liczba wyników jest większa niż 0, alert powinien zostać wywołany.

W sekcji **oceniane na podstawie** Określ **okres** i **częstotliwość** w zależności od tego, jak często chcesz sprawdzać dostępność niedostępnych węzłów.

Na potrzeby tego alertu należy się upewnić, że **okres = częstotliwość.** Więcej informacji na temat okresu, częstotliwości i innych parametrów alertu można znaleźć [tutaj](../azure-monitor/platform/alerts-unified-log.md#alert-logic-definition).

Po zakończeniu konfigurowania logiki sygnałów wybierz pozycję **gotowe** .

![Reguła alertu konfiguruje logikę sygnału](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Jeśli nie masz jeszcze istniejącej grupy akcji, kliknij pozycję **Utwórz nową** w sekcji **grupy akcji** .

![Reguła alertu tworzy nową grupę akcji](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Spowoduje to otwarcie **grupy akcji Dodaj** . Wybierz **nazwę grupy akcji** , **krótką nazwę** , **subskrypcję** i **grupę zasobów.** W sekcji **Akcje** wybierz **nazwę akcji** i wybierz pozycję **email/SMS/push/Voice** jako **Typ akcji.**

> [!NOTE]
> Istnieje kilka innych akcji, które mogą być wyzwalane przez alert oprócz wiadomości E-mail/SMS/wypychania/głosu, takich jak usługa Azure Functions, LogicApp, webhook, narzędzia ITSM i Automatyzacja. [Dowiedz się więcej.](../azure-monitor/platform/action-groups.md#action-specific-information)

Spowoduje to otwarcie **wiadomości e-mail/SMS/wypychania/głosu** . Wybierz **nazwę** odbiorcy, **zaznacz** pole adres **e-mail** , a następnie wpisz adres e-mail, na który ma być wysyłany alert. Wybierz pozycję **OK** w  **wiadomości e-mail/SMS/wypychanie/głos** , a następnie w obszarze **Dodaj grupę akcji** , aby zakończyć konfigurowanie grupy akcji.

![Reguła alertu tworzy grupę akcji.](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Po zamknięciu tych zamków powinna zostać wyświetlona grupa akcji wymieniona w sekcji **grupy akcji** . Na koniec Wypełnij sekcję **szczegóły alertu** , wpisując nazwę i **Opis** **reguły alertu** i wybierając **ważność** . Kliknij przycisk **Utwórz regułę alertu** , aby zakończyć.

![Portal tworzy zakończenie reguły alertu](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> Możliwość określania **ważności** jest zaawansowanym narzędziem, które może być używane podczas tworzenia wielu alertów. Na przykład można utworzyć jeden alert, aby zgłosić ostrzeżenie (ważność 1), jeśli jeden węzeł główny ulegnie awarii i inny alert, który podnosi krytyczne (ważność 0) w prawdopodobnym zdarzeniu, że oba węzły główne przechodzą.

Po spełnieniu warunku tego alertu alert zostanie uruchomiony i otrzymasz wiadomość e-mail z informacjami o alercie, takimi jak:

![Przykład wiadomości e-mail dotyczącej alertu Azure Monitor](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

Możesz również wyświetlić wszystkie alerty, które zostały wywołane, pogrupowane według ważności, przechodząc do **alertów** w **obszarze roboczym log Analytics** .

![Log Analytics alertów obszaru roboczego](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Wybranie grupowania ważności (tj. **ważność 1,** jak zostało wyróżnione powyżej) spowoduje wyświetlenie rekordów dla wszystkich alertów o ważności, które zostały wywołane jak poniżej:

![Ważność obszar roboczy Log Analytics jeden alert](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Następne kroki

* [Dostępność klastra — Apache Ambari](./hdinsight-cluster-availability.md)
* [Korzystanie z dzienników Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
