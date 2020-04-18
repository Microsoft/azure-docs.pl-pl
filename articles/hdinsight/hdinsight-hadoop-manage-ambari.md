---
title: Monitorowanie usługi Azure HDInsight i zarządzanie nim przy użyciu interfejsu użytkownika sieci Web Ambari
description: Dowiedz się, jak używać interfejsu apache ambari do monitorowania klastrów HDInsight i zarządzania nimi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 2ed3d6b1088315b580ab8ddc4f12d8d61434ec53
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606556"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari upraszcza zarządzanie i monitorowanie klastra Apache Hadoop. To uproszczenie odbywa się poprzez zapewnienie łatwy w użyciu interfejsu użytkownika sieci web i interfejsu API REST. Ambari jest dołączony do klastrów HDInsight i służy do monitorowania klastra i wprowadzania zmian konfiguracji.

W tym dokumencie dowiesz się, jak używać interfejsu użytkownika sieci Web Ambari z klastrem HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Co to jest Apache Ambari?

[Apache Ambari](https://ambari.apache.org) upraszcza zarządzanie Hadoop, zapewniając łatwy w użyciu interfejs użytkownika sieci Web. Za pomocą ambari można zarządzać klastrami Hadoop i monitorować je. Deweloperzy mogą zintegrować te funkcje w swoich aplikacjach za pomocą [interfejsów API Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Łączność

Interfejs użytkownika sieci Web Ambari jest dostępny `https://CLUSTERNAME.azurehdinsight.net`w `CLUSTERNAME` klastrze HDInsight pod adresem , gdzie jest nazwa klastra.

> [!IMPORTANT]  
> Łączenie się z Ambari na HDInsight wymaga protokołu HTTPS. Po wyświetleniu monitu o uwierzytelnienie użyj nazwy konta administratora i hasła podanego podczas tworzenia klastra. Jeśli nie zostanie wyświetlony monit o poświadczenia, sprawdź ustawienia sieciowe, aby potwierdzić, że nie ma problemu z łącznością między klientem a klastrami usługi Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

Chociaż Ambari dla klastra jest dostępny bezpośrednio przez Internet, niektóre łącza z interfejsu użytkownika sieci Web Ambari (na przykład do JobTracker) nie są udostępniane w Internecie. Aby uzyskać dostęp do tych usług, należy utworzyć tunel SSH. Aby uzyskać więcej informacji, zobacz [Korzystanie z tunelowania SSH z hdinsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

> [!WARNING]  
> Nie wszystkie funkcje interfejsu użytkownika sieci Web Ambari są obsługiwane w programie HDInsight. Aby uzyskać więcej informacji, zobacz sekcję [Operacje nieobsługiowane](#unsupported-operations) w tym dokumencie.

Podczas łączenia się z interfejsem użytkownika sieci Web Ambari zostanie wyświetlony monit o uwierzytelnienie się na stronie. Użyj użytkownika administratora klastra (domyślny administrator) i hasła używanego podczas tworzenia klastra.

Po otwarciu strony zanotuj pasek u góry. Ten pasek zawiera następujące informacje i formanty:

![Apache Ambari — omówienie pulpitu nawigacyjnego](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Element |Opis |
|---|---|
|Logo Ambari|Otwiera pulpit nawigacyjny, który może służyć do monitorowania klastra.|
|Nazwa klastra # ops|Wyświetla liczbę trwających operacji Ambari. Wybranie nazwy klastra lub **# ops** wyświetla listę operacji w tle.|
|# alerty|Wyświetla ostrzeżenia lub alerty krytyczne, jeśli istnieją, dla klastra.|
|Pulpit nawigacyjny|Wyświetla pulpit nawigacyjny.|
|Usługi|Ustawienia informacji i konfiguracji usług w klastrze.|
|Hosts|Informacje i ustawienia konfiguracji węzłów w klastrze.|
|Alerty|Dziennik informacji, ostrzeżeń i alertów krytycznych.|
|Administrator|Stos/usługi oprogramowania zainstalowane w klastrze, informacje o koncie usługi i zabezpieczenia protokołu Kerberos.|
|Przycisk Administrator|Zarządzanie Ambari, ustawienia użytkownika i wyloguj się.|

## <a name="monitoring"></a>Monitorowanie

### <a name="alerts"></a>Alerty

Poniższa lista zawiera typowe stany alertów używane przez Ambari:

* **OK**
* **Ostrzeżenie**
* **Krytyczne**
* **Nieznany**

Alerty inne niż **OK** powodują **wpis # alerty** w górnej części strony, aby wyświetlić liczbę alertów. Wybranie tego wpisu powoduje wyświetlenie alertów i ich stanu.

Alerty są zorganizowane w kilka domyślnych grup, które można wyświetlić na stronie **Alerty.**

![Apache Ambari alarmuje podsumowanie strony](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Grupy można zarządzać za pomocą menu **Akcje** i wybierając polecenie **Zarządzaj grupami alertów**.

![Apache Ambari zarządza grupami alertów](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Zarządzasz metodami alertów i możesz tworzyć powiadomienia alertów z menu **Akcje,** wybierając pozycję __Zarządzaj powiadomieniami__. Wyświetlane są bieżące powiadomienia. Utwórz powiadomienia z tego miejsca. Powiadomienia mogą być wysyłane za pośrednictwem **poczty e-mail** lub **SNMP,** gdy wystąpią określone kombinacje alertów/ważności. Na przykład można wysłać wiadomość e-mail, gdy którykolwiek z alertów w grupie **Domyślne YARN** jest ustawiony na **Krytyczny**.

![Apache Ambari utworzyć powiadomienie o alertach](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Na koniec wybranie opcji __Zarządzaj ustawieniami alertów__ z menu __Akcje__ umożliwia ustawienie liczby wystąpień alertu przed wysłaniem powiadomienia. To ustawienie może służyć do zapobiegania powiadomień o błędach przejściowych.

Aby uzyskać samouczek powiadomienia alertu przy użyciu bezpłatnego [konta SendGrid,](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)zobacz [Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Klaster

Karta **Metryki** na pulpicie nawigacyjnym zawiera serię widżetów, które ułatwiają monitorowanie stanu klastra na pierwszy rzut oka. Kilka widżetów, takich jak **Użycie procesora,** dostarcza dodatkowych informacji po kliknięciu.

![Apache Ambari dashboard z metrykami](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Na karcie **Mapy cieplne** są wyświetlane dane jako kolorowe mapy cieplne, przechodząc od zielonego do czerwonego.

![Apache Ambari deski rozdzielczej z heatmaps](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Aby uzyskać więcej informacji na temat węzłów w klastrze, wybierz pozycję **Hosty**. Następnie wybierz konkretny węzeł, który Cię interesuje.

![Apache Ambari szczegóły podsumowania hosta](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Usługi

Pasek boczny **usług** na pulpicie nawigacyjnym zapewnia szybki wgląd w stan usług uruchomionych w klastrze. Różne ikony są używane do wskazywania stanu lub działań, które należy podjąć. Na przykład żółty symbol recyklingu jest wyświetlany, jeśli usługa wymaga recyklingu.

![Pasek boczny usług Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Wyświetlane usługi różnią się między typami i wersjami klastra HDInsight. Usługi wyświetlane w tym miejscu mogą być inne niż usługi wyświetlane dla klastra.

Wybranie usługi powoduje wyświetlenie bardziej szczegółowych informacji o usłudze.

![Apache Ambari informacje podsumowujące usługi](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Szybkie linki

Niektóre usługi wyświetlają łącze **Szybkie łącza** u góry strony. Tego łącza można użyć do uzyskania dostępu do interfejsów użytkownika sieci web specyficznych dla usługi, takich jak:

* **Historia zadań** — MapaReduce historia pracy.
* **Menedżer zasobów** — interfejs użytkownika Menedżera zasobów YARN.
* **NameNode** - Interfejs użytkownika rozproszonego systemu plików (HDFS) Hadoop.
* **Interfejs użytkownika sieci Web Oozie** — interfejs użytkownika Oozie.

Wybranie dowolnego z tych łączy powoduje otwarcie nowej karty w przeglądarce, która wyświetla wybraną stronę.

> [!NOTE]  
> Wybranie wpisu **Szybkie łącza** dla usługi może zwrócić błąd "nie znaleziono serwera". W przypadku wystąpienia tego błędu należy użyć tunelu SSH podczas korzystania z wpisu **Szybkie łącza** dla tej usługi. Aby uzyskać więcej informacji, zobacz [Korzystanie z tunelowania SSH z hdinsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Zarządzanie

### <a name="ambari-users-groups-and-permissions"></a>Użytkownicy Ambari, grupy i uprawnienia

Praca z użytkownikami, grupami i uprawnieniami jest obsługiwana podczas korzystania z klastra HDInsight [przyłączony do domeny.](./domain-joined/hdinsight-security-overview.md) Aby uzyskać informacje na temat używania interfejsu użytkownika zarządzania ambari w klastrze przyłączanym do domeny, zobacz [Zarządzanie klastrami HDInsight przyłączonych do domeny](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Nie należy zmieniać hasła programu Ambari watchdog (hdinsightwatchdog) w klastrze HDInsight opartym na systemie Linux. Zmiana hasła przerywa możliwość używania akcji skryptu lub wykonywania operacji skalowania za pomocą klastra.

### <a name="hosts"></a>Hosts

Strona **Hosts** zawiera listę wszystkich hostów w klastrze. Aby zarządzać hostami, wykonaj następujące kroki.

![Apache Ambari hosts strona przegląd](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Dodawanie, likwidowanie i ponowne uruchomienie hosta nie powinny być używane z klastrami HDInsight.

1. Wybierz hosta, którym chcesz zarządzać.

2. Użyj menu **Akcje,** aby wybrać akcję, którą chcesz wykonać:

    |Element |Opis |
    |---|---|
    |Uruchamianie wszystkich komponentów|Uruchom wszystkie składniki na hoście.|
    |Zatrzymaj wszystkie komponenty|Zatrzymaj wszystkie składniki na hoście.|
    |Uruchom ponownie wszystkie składniki|Zatrzymaj i uruchom wszystkie składniki na hoście.|
    |Włączanie trybu konserwacji|Pomija alerty dla hosta. Ten tryb powinien być włączony, jeśli wykonujesz akcje generujące alerty. Na przykład zatrzymywanie i uruchamianie usługi.|
    |Wyłącz tryb konserwacji|Zwraca hosta do normalnego alertów.|
    |Stop|Zatrzymuje DataNode lub NodeManagers na hoście.|
    |Uruchamianie|Uruchamia DataNode lub NodeManagers na hoście.|
    |Ponowne uruchamianie|Zatrzymuje i uruchamia DataNode lub NodeManagers na hoście.|
    |Zlikwidować|Usuwa hosta z klastra. **Nie używaj tej akcji w klastrach HDInsight.**|
    |Rekomisja|Dodaje wcześniej zlikwidowany host do klastra. **Nie używaj tej akcji w klastrach HDInsight.**|

### <a name="services"></a><a id="service"></a>Usług

Na stronie **Pulpit nawigacyjny** lub **Usługi** użyj przycisku **Akcje** u dołu listy usług, aby zatrzymać i uruchomić wszystkie usługi.

![Lista akcji usługi Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Chociaż **dodaj usługę** jest wymieniony w tym menu, nie powinny być używane do dodawania usług do klastra HDInsight. Nowe usługi powinny być dodawane przy użyciu akcji skryptu podczas inicjowania obsługi administracyjnej klastra. Aby uzyskać więcej informacji na temat używania akcji skryptów, zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

Podczas gdy przycisk **Akcje** można ponownie uruchomić wszystkie usługi, często chcesz uruchomić, zatrzymać lub ponownie uruchomić określoną usługę. Wykonaj następujące kroki, aby wykonać akcje w poszczególnych usługach:

1. Na stronie **Pulpit nawigacyjny** lub **Usługi** wybierz usługę.

2. U góry karty **Podsumowanie** użyj przycisku **Akcje usługi** i wybierz akcję do podjęcia. Ta akcja powoduje ponowne uruchomienie usługi we wszystkich węzłach.

    ![Apache Ambari indywidualne działania serwisowe](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Ponowne uruchomienie niektórych usług, gdy klaster jest uruchomiony, może generować alerty. Aby uniknąć alertów, można użyć przycisku **Akcje usługi,** aby włączyć **tryb konserwacji** dla usługi przed wykonaniem ponownego uruchomienia.

3. Po wybraniu akcji wpis **# op** u góry strony zwiększa się, aby pokazać, że występuje operacja w tle. Jeśli jest skonfigurowana do wyświetlania, zostanie wyświetlona lista operacji w tle.

   > [!NOTE]  
   > Jeśli **włączono tryb konserwacji** usługi, należy pamiętać, aby wyłączyć go za pomocą przycisku **Akcje usługi** po zakończeniu operacji.

Aby skonfigurować usługę, należy wykonać następujące czynności:

1. Na stronie **Pulpit nawigacyjny** lub **Usługi** wybierz usługę.

2. Wybierz kartę **Konfiguracje.** Zostanie wyświetlona bieżąca konfiguracja. Zostanie również wyświetlona lista poprzednich konfiguracji.

    ![Konfiguracja usługi Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Użyj wyświetlanych pól, aby zmodyfikować konfigurację, a następnie wybierz pozycję **Zapisz**. Możesz też wybrać poprzednią konfigurację, a następnie wybrać pozycję **Utwórz prąd,** aby przywrócić poprzednie ustawienia.

## <a name="ambari-views"></a>Widoki Ambari

Widoki Ambari umożliwiają deweloperom podłączenie elementów interfejsu użytkownika do interfejsu użytkownika sieci Web Ambari przy użyciu [apache Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). Usługa HDInsight udostępnia następujące widoki z typami klastra Hadoop:

* Widok gałęzi: Widok gałęzi umożliwia uruchamianie zapytań hive bezpośrednio z przeglądarki sieci Web. Można zapisywać kwerendy, wyświetlać wyniki, zapisywać wyniki w magazynie klastra lub pobierać wyniki do systemu lokalnego. Aby uzyskać więcej informacji na temat używania widoków gałęzi, zobacz [Używanie widoków gałęzi Apache z hdinsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Widok Tez: Widok Tez pozwala lepiej zrozumieć i zoptymalizować zadania. Można wyświetlić informacje o tym, jak wykonywane są zadania Tez i jakie zasoby są używane.

## <a name="unsupported-operations"></a>Operacje nieobsługiwały

Następujące operacje Ambari nie są obsługiwane w programie HDInsight:

* __Przenoszenie usługi Metrics Collector__. Podczas przeglądania informacji w usłudze Metrics Collector jedną z akcji dostępnych w menu Akcje usługi jest __moduł zbierający Move Metrics__. Ta akcja nie jest obsługiwana przez hdinsight.

## <a name="next-steps"></a>Następne kroki

* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) z HDInsight.
* [Korzystanie z systemu Apache Ambari w celu optymalizacji konfiguracji klastrów usługi HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Skalowanie klastrów usługi Azure HDInsight](./hdinsight-scaling-best-practices.md)
