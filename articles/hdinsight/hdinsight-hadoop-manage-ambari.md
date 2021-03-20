---
title: Monitorowanie usługi Azure HDInsight i zarządzanie nią za pomocą interfejsu użytkownika sieci Web Ambari
description: Dowiedz się, jak używać interfejsu użytkownika Apache Ambari do monitorowania klastrów usługi HDInsight i zarządzania nimi.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 087f284bed7ab0c9eb551c1629ab4f9196c80d76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945701"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari upraszcza zarządzanie i monitorowanie klastra Apache Hadoop. To uproszczenie jest realizowane przez zapewnienie łatwego korzystania z interfejsu użytkownika sieci Web i interfejsu API REST. Usługa Ambari jest dołączana do klastrów usługi HDInsight i służy do monitorowania klastra i wprowadzania zmian w konfiguracji.

W tym dokumencie dowiesz się, jak używać interfejsu użytkownika sieci Web Ambari z klastrem usługi HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Co to jest Apache Ambari?

Platforma [Apache Ambari](https://ambari.apache.org) upraszcza zarządzanie Hadoop, zapewniając łatwy w użyciu interfejs użytkownika sieci Web. Usługi Ambari można użyć do zarządzania klastrami Hadoop i ich monitorowania. Deweloperzy mogą zintegrować te funkcje z aplikacjami za pomocą [interfejsów API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Łączność

Interfejs użytkownika sieci Web Ambari jest dostępny w klastrze usługi HDInsight w lokalizacji `https://CLUSTERNAME.azurehdinsight.net` , gdzie `CLUSTERNAME` jest nazwą klastra.

> [!IMPORTANT]  
> Połączenie z usługą Ambari w usłudze HDInsight wymaga protokołu HTTPS. Po wyświetleniu monitu o uwierzytelnienie Użyj nazwy konta administratora i hasła podanego podczas tworzenia klastra. Jeśli nie zostanie wyświetlony monit o podanie poświadczeń, sprawdź ustawienia sieci, aby upewnić się, że nie występuje problem z łącznością między klientem i klastrami usługi Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

Gdy usługa Ambari dla klastra jest dostępna bezpośrednio przez Internet, niektóre linki z internetowego interfejsu użytkownika Ambari (na przykład do JobTracker) nie są dostępne w Internecie. Aby uzyskać dostęp do tych usług, należy utworzyć tunel SSH. Aby uzyskać więcej informacji, zobacz [Używanie tunelowania SSH z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

> [!WARNING]  
> Nie wszystkie funkcje interfejsu użytkownika sieci Web Ambari są obsługiwane w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz sekcję [nieobsługiwane operacje](#unsupported-operations) w tym dokumencie.

Podczas nawiązywania połączenia z interfejsem użytkownika sieci Web Ambari zostanie wyświetlony monit o uwierzytelnienie na stronie. Użyj użytkownika administrator klastra (administrator domyślny) i hasło użyte podczas tworzenia klastra.

Gdy zostanie otwarta strona, zanotuj pasek u góry. Ten pasek zawiera następujące informacje i kontrolki:

![Pulpit nawigacyjny Apache Ambari — Omówienie](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Element |Opis |
|---|---|
|Logo Ambari|Otwiera pulpit nawigacyjny, który może służyć do monitorowania klastra.|
|Nazwa klastra # Ops|Wyświetla liczbę trwających operacji Ambari. Wybranie nazwy klastra lub **# Ops** powoduje wyświetlenie listy operacji w tle.|
|liczba alertów|W przypadku klastra są wyświetlane ostrzeżenia lub alerty krytyczne (jeśli istnieją).|
|Pulpit nawigacyjny|Wyświetla pulpit nawigacyjny.|
|Usługi|Informacje i ustawienia konfiguracji usług w klastrze.|
|Hosts|Ustawienia informacji i konfiguracji dla węzłów w klastrze.|
|Alerty|Dziennik informacji, ostrzeżeń i alertów krytycznych.|
|Administracja|Stos/usługi oprogramowania zainstalowane w klastrze, informacje o koncie usługi i zabezpieczenia protokołu Kerberos.|
|Przycisk administratora|Ambari Management, ustawienia użytkownika i wyloguj się.|

## <a name="monitoring"></a>Monitorowanie

### <a name="alerts"></a>Alerty

Poniższa lista zawiera typowe Stany alertów używane przez Ambari:

* **OK**
* **Ostrzeżenie**
* **NAJISTOTNIEJSZ**
* **ZNANA**

Alerty inne niż **OK** powodują wyświetlenie liczby alertów w pozycji **# alerty** w górnej części strony. Wybranie tej pozycji spowoduje wyświetlenie alertów i ich stanu.

Alerty są zorganizowane w kilka domyślnych grup, które mogą być wyświetlane na stronie **alerty** .

![Podsumowanie strony alertów Apache Ambari](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Grupami można zarządzać za pomocą menu **Akcje** i wybierając pozycję **Zarządzaj grupami alertów**.

![Zarządzanie grupami alertów w usłudze Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Możesz zarządzać metodami alertów i tworzyć powiadomienia o alertach z menu **Akcje** , wybierając pozycję __Zarządzaj powiadomieniami__. Wyświetlane są wszystkie bieżące powiadomienia. W tym miejscu Utwórz powiadomienia. Powiadomienia mogą być wysyłane za pośrednictwem **poczty e-mail** lub **SNMP** , gdy wystąpią określone kombinacje alertów/ważności. Na przykład możesz wysłać wiadomość e-mail, gdy dowolne z alertów w grupie **domyślnej przędzy** ma wartość **krytyczne**.

![Powiadomienie o utworzeniu alertu dotyczącego oprogramowania Apache Ambari](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Na koniec wybranie opcji __Zarządzaj ustawieniami alertów__ z menu __Akcje__ pozwala określić, ile razy alert musi nastąpić przed wysłaniem powiadomienia. Tego ustawienia można użyć, aby uniemożliwić powiadomienia dotyczące błędów przejściowych.

Aby zapoznać się z samouczkiem powiadomienia o alercie przy użyciu bezpłatnego [konta usługi SendGrid](../sendgrid-dotnet-how-to-send-email.md), zobacz [Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Klaster

Karta **metryki** pulpitu nawigacyjnego zawiera serię elementów widget, które ułatwiają szybkie monitorowanie stanu klastra. Kilka widżetów, takich jak **użycie procesora CPU**, udostępnia dodatkowe informacje po kliknięciu.

![Pulpit nawigacyjny Apache Ambari z metrykami](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Na karcie **map cieplnych** są wyświetlane metryki jako kolorowe map cieplnych, które przechodzą z koloru zielonego na czerwony.

![Pulpit nawigacyjny Apache Ambari z map cieplnych](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Aby uzyskać więcej informacji na temat węzłów w klastrze, wybierz pozycję **hosty**. Następnie wybierz konkretny wybrany węzeł.

![Szczegóły podsumowania hosta Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Usługi

Pasek boczny **usług** na pulpicie nawigacyjnym zapewnia szybki wgląd w informacje o stanie usług uruchomionych w klastrze. Różne ikony są używane do wskazywania stanu lub akcji, które należy wykonać. Na przykład żółty symbol odtwarzania jest wyświetlany, jeśli usługa musi zostać odtworzona.


![Pasek boczny usług Apache Ambari Services](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Wyświetlone usługi różnią się w zależności od typów i wersji klastra HDInsight. Wyświetlone w tym miejscu usługi mogą być inne niż usługi wyświetlane dla danego klastra.

Wybranie usługi spowoduje wyświetlenie bardziej szczegółowych informacji na temat usługi.

![Informacje podsumowania usługi Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Szybkie linki

W niektórych usługach w górnej części strony są wyświetlane łącza **szybki** link. Tego linku można użyć w celu uzyskania dostępu do interfejsów użytkownika sieci Web specyficznych dla usługi, takich jak:

* **Historia zadania** — historia zadań MapReduce.
* Interfejs użytkownika Menedżer zasobów **Menedżer zasobów** -przędzy.
* Interfejs użytkownika NameNode **NameNode** -Hadoop rozproszony system plików (HDFS).
* **Oozie interfejs użytkownika sieci Web** — interfejs użytkownika Oozie.

Wybranie dowolnego z tych linków spowoduje otwarcie nowej karty w przeglądarce, w której zostanie wyświetlona wybrana strona.

> [!NOTE]  
> Wybranie wpisu **szybkie linki** dla usługi może spowodować zwrócenie błędu "nie można odnaleźć serwera". W przypadku wystąpienia tego błędu należy użyć tunelu SSH podczas korzystania z wpisu **szybkie linki** dla tej usługi. Aby uzyskać więcej informacji, zobacz [Korzystanie z tunelowania SSH z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Zarządzanie

### <a name="ambari-users-groups-and-permissions"></a>Ambari użytkownicy, grupy i uprawnienia

Obsługa użytkowników, grup i uprawnień jest obsługiwana. Aby uzyskać administrację lokalną, zobacz [Autoryzowanie użytkowników na potrzeby widoków Apache Ambari](./hdinsight-authorize-users-to-ambari.md). W przypadku klastrów przyłączonych do domeny Zobacz [Zarządzanie przyłączonymi do domeny klastrami usługi HDInsight](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Nie usuwaj ani nie zmieniaj hasła Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartego na systemie Linux. Zmiana hasła zrywa możliwość korzystania z akcji skryptu lub wykonywania operacji skalowania w klastrze.

### <a name="hosts"></a>Hosts

Na stronie **hosty** są wyświetlane wszystkie hosty w klastrze. Aby zarządzać hostami, wykonaj następujące kroki.

![Strona hostów Apache Ambari — Omówienie](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Dodawanie, likwidowanie i relikwidowanie hosta nie powinno być używane z klastrami usługi HDInsight.

1. Wybierz hosta, którym chcesz zarządzać.

2. Za pomocą menu **Akcje** wybierz akcję, którą chcesz wykonać:

    |Element |Opis |
    |---|---|
    |Uruchom wszystkie składniki|Uruchom wszystkie składniki na hoście.|
    |Zatrzymaj wszystkie składniki|Zatrzymaj wszystkie składniki na hoście.|
    |Uruchom ponownie wszystkie składniki|Zatrzymaj i uruchom wszystkie składniki na hoście.|
    |Włącz tryb konserwacji|Pomija alerty dla hosta. Ten tryb należy włączyć, jeśli wykonujesz akcje generujące alerty. Na przykład zatrzymywanie i uruchamianie usługi.|
    |Wyłącz tryb konserwacji|Zwraca hosta do normalnego alertu.|
    |Stop|Powoduje zatrzymanie elementu datanode lub NodeManagers na hoście.|
    |Rozpocznij|Uruchamia element datanode lub NodeManagers na hoście.|
    |Uruchom ponownie|Kończy i uruchamia węzeł datanode lub NodeManagers na hoście.|
    |Zlikwidować|Usuwa hosta z klastra. **Nie należy używać tej akcji w klastrach usługi HDInsight.**|
    |Recommission|Dodaje wcześniej zlikwidowanego hosta do klastra. **Nie należy używać tej akcji w klastrach usługi HDInsight.**|

### <a name="services"></a><a id="service"></a>Usługi

Na stronie **pulpit nawigacyjny** lub **usługi** Użyj przycisku **Akcje** znajdującego się u dołu listy usług, aby zatrzymać i uruchomić wszystkie usługi.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Lista akcji usługi Apache Ambari." border="true":::

> [!WARNING]  
> Przed zainicjowaniem obsługi klastra należy dodać nowe usługi przy użyciu akcji skryptu. Aby uzyskać więcej informacji o korzystaniu z akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Po kliknięciu przycisku **Akcje** można uruchomić ponownie wszystkie usługi, często w celu uruchomienia, zatrzymania lub ponownego uruchomienia określonej usługi. Wykonaj następujące kroki, aby wykonać akcje dotyczące poszczególnych usług:

1. Na stronie **pulpit nawigacyjny** lub **usługi** wybierz usługę.

2. W górnej części karty **Podsumowanie** Użyj przycisku **Akcje usługi** i wybierz akcję do wykonania. Ta akcja powoduje ponowne uruchomienie usługi we wszystkich węzłach.

    ![Akcje poszczególnych usług Apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Ponowne uruchamianie niektórych usług, gdy klaster jest uruchomiony, może generować alerty. Aby uniknąć alertów, można użyć przycisku **Akcje usługi** , aby włączyć **tryb konserwacji** dla usługi przed ponownym uruchomieniem.

3. Po wybraniu akcji **# op** wpis w górnej części strony zwiększa się, aby pokazać, że występuje operacja w tle. Jeśli jest skonfigurowany do wyświetlania, zostanie wyświetlona lista operacji w tle.

   > [!NOTE]  
   > Jeśli włączono **tryb konserwacji** usługi, pamiętaj, aby wyłączyć go przy użyciu przycisku **Akcje usługi** po zakończeniu operacji.

Aby skonfigurować usługę, wykonaj następujące czynności:

1. Na stronie **pulpit nawigacyjny** lub **usługi** wybierz usługę.

2. Wybierz kartę **konfiguracje** . Zostanie wyświetlona bieżąca konfiguracja. Zostanie również wyświetlona lista poprzednich konfiguracji.

    ![Konfiguracja usługi Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Użyj wyświetlanych pól, aby zmodyfikować konfigurację, a następnie wybierz pozycję **Zapisz**. Lub wybierz poprzednią konfigurację, a następnie wybierz pozycję **Ustaw jako bieżącą** , aby przywrócić poprzednie ustawienia.

## <a name="ambari-views"></a>Widoki Ambari

Widoki Ambari umożliwiają deweloperom podłączenie elementów interfejsu użytkownika do interfejsu użytkownika sieci Web Ambari przy użyciu struktury widoków Apache Ambari. Usługa HDInsight udostępnia następujące widoki typów klastrów Hadoop:

* Widok Hive: widok Hive umożliwia uruchamianie zapytań programu Hive bezpośrednio z przeglądarki sieci Web. Możesz zapisywać zapytania, wyświetlać wyniki, zapisywać wyniki w magazynie klastra lub pobierać wyniki do systemu lokalnego. Aby uzyskać więcej informacji na temat korzystania z widoków Hive, zobacz [Korzystanie z widoków Apache Hive w usłudze HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Widok tez: widok tez pozwala lepiej zrozumieć i zoptymalizować zadania. Można wyświetlić informacje na temat sposobu wykonywania zadań tez i zasobów, które są używane.

## <a name="unsupported-operations"></a>Nieobsługiwane operacje

Następujące operacje Ambari nie są obsługiwane w usłudze HDInsight:

* __Przeniesienie usługi modułu zbierającego metryki__. Podczas wyświetlania informacji dotyczących usługi modułu zbierającego metryk, jedną z akcji dostępnych w menu Akcje usługi jest __przeniesienie modułu zbierającego metryki__. Ta akcja nie jest obsługiwana w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) z usługą HDInsight.
* [Korzystanie z systemu Apache Ambari w celu optymalizacji konfiguracji klastrów usługi HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Skalowanie klastrów usługi Azure HDInsight](./hdinsight-scaling-best-practices.md)
