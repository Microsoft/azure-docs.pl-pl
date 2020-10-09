---
title: Rozwiązywanie problemów dotyczących wolnego lub niepowodzenia zadania w klastrze usługi Azure HDInsight
description: Diagnozowanie i rozwiązywanie problemów dotyczących wolnego lub nieprawidłowego zadania w klastrze usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895314"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Rozwiązywanie problemów dotyczących wolnego działania lub niepowodzenia zadania w klastrze usługi HDInsight

Jeśli przetwarzanie danych przez aplikację w klastrze usługi HDInsight przebiega wolno lub kończy się niepowodzeniem z kodem błędu, masz kilka opcji rozwiązywania problemów. Jeśli wykonywanie zadań trwa dłużej niż oczekiwano lub na ogół widzisz wolne czasy odpowiedzi, mogą wystąpić błędy z klastra, takie jak usługi, na których działa klaster. Jednak najczęstsze przyczyny tych spowolnienia są niewystarczające. Podczas tworzenia nowego klastra usługi HDInsight wybierz odpowiednie [rozmiary maszyn wirtualnych](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Aby zdiagnozować klaster wolny lub niepowodzenie, Zbierz informacje o wszystkich aspektach środowiska, takich jak skojarzone usługi platformy Azure, Konfiguracja klastra i informacje o wykonywaniu zadania. Przydatną diagnostyką jest próba odtworzenia stanu błędu w innym klastrze.

* Krok 1. zbieranie danych o problemie.
* Krok 2. Weryfikowanie środowiska klastra usługi HDInsight.
* Krok 3. Wyświetlanie kondycji klastra.
* Krok 4: przegląd stosu i wersji środowiska.
* Krok 5. Sprawdzanie plików dziennika klastra.
* Krok 6. sprawdzenie ustawień konfiguracji.
* Krok 7. odtworzenie awarii w innym klastrze.

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1. zbieranie danych dotyczących problemu

Usługa HDInsight udostępnia wiele narzędzi, których można użyć do identyfikowania i rozwiązywania problemów z klastrami. Poniższe kroki przeprowadzą Cię przez te narzędzia i zawierają sugestie dotyczące lokalizowania problemu.

### <a name="identify-the-problem"></a>Identyfikowanie problemu

Aby ułatwić zidentyfikowanie problemu, należy wziąć pod uwagę następujące kwestie:

* Co się stało z oczekiwaniami? Co się stało?
* Jak długo proces będzie działał? Jak długo powinna być uruchomiona?
* Czy moje zadania zawsze działają powoli w tym klastrze? Czy działają szybciej w innym klastrze?
* Kiedy ten problem wystąpił po raz pierwszy? Jak często wystąpił od?
* Czy coś uległo zmianie w konfiguracji klastra?

### <a name="cluster-details"></a>Szczegóły klastra

Ważne informacje o klastrze obejmują:

* Nazwa klastra.
* Region klastra — sprawdzanie [awarii regionu](https://azure.microsoft.com/status/).
* Typ i wersja klastra usługi HDInsight.
* Typ i liczba wystąpień usługi HDInsight określonych dla węzłów głównych i procesów roboczych.

Azure Portal może podać następujące informacje:

![Informacje dotyczące Azure Portal usługi HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Możesz również użyć [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Inną opcją jest użycie programu PowerShell. Aby uzyskać więcej informacji, zobacz  [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight z Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2. Weryfikowanie środowiska klastra usługi HDInsight

Każdy klaster usługi HDInsight opiera się na różnych usługach platformy Azure oraz oprogramowaniu typu open source, takim jak Apache HBase i Apache Spark. Klastry usługi HDInsight mogą również wywoływać inne usługi platformy Azure, takie jak sieci wirtualne platformy Azure.  Awaria klastra może być spowodowana przez jakąkolwiek z uruchomionych usług w klastrze lub przez usługę zewnętrzną.  Zmiana konfiguracji usługi klastrowania może także spowodować niepowodzenie klastra.

### <a name="service-details"></a>Szczegóły usługi

* Sprawdź wersje wersji biblioteki Open Source.
* Wyszukaj [awarię usługi platformy Azure](https://azure.microsoft.com/status/).  
* Sprawdź limity użycia usługi platformy Azure. 
* Sprawdź konfigurację podsieci Virtual Network platformy Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika Ambari

Usługa Apache Ambari umożliwia zarządzanie klastrem usługi HDInsight i monitorowanie go za pomocą interfejsu użytkownika sieci Web i interfejsu API REST. Usługa Ambari jest dołączana do klastrów usługi HDInsight opartych na systemie Linux. Na stronie Azure Portal HDInsight Wybierz okienko **pulpit nawigacyjny klastra** .  Wybierz okienko **pulpit nawigacyjny klastra usługi HDInsight** , aby otworzyć interfejs użytkownika Ambari, a następnie wprowadź poświadczenia logowania do klastra.  

![Pulpit nawigacyjny Apache Ambari — Omówienie](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Aby otworzyć listę widoków usług, wybierz pozycję **widoki Ambari** na stronie Azure Portal.  Ta lista jest zależna od tego, które biblioteki są zainstalowane. Na przykład można zobaczyć Menedżera kolejki PRZĘDZy, widok Hive i widok tez.  Wybierz łącze usługi, aby wyświetlić informacje o konfiguracji i usłudze.

#### <a name="check-for-azure-service-outages"></a>Sprawdzanie, czy wystąpiły awarie usługi platformy Azure

Usługa HDInsight bazuje na kilku usługach platformy Azure. Uruchamia serwery wirtualne w usłudze Azure HDInsight, przechowuje dane i skrypty w usłudze Azure Blob Storage lub Azure Data Lake Storage oraz indeksuje pliki dzienników w usłudze Azure Table Storage. Zakłócenia w tych usługach, chociaż rzadko, mogą powodować problemy w usłudze HDInsight. Jeśli w klastrze występują nieoczekiwane spowolnienia lub błędy, sprawdź [pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status/). Stan każdej usługi znajduje się na liście według regionów. Sprawdź region klastra, a także regiony dla powiązanych usług.

#### <a name="check-azure-service-usage-limits"></a>Sprawdź limity użycia usługi platformy Azure

Jeśli uruchamiasz duży klaster lub jednocześnie uruchomiłeś wiele klastrów, klaster może zakończyć się niepowodzeniem, jeśli Przekroczono limit usługi platformy Azure. Limity usługi różnią się w zależności od subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Możesz zażądać, aby firma Microsoft zwiększyła liczbę dostępnych zasobów usługi HDInsight (takich jak rdzenie maszyn wirtualnych i wystąpienia maszyn wirtualnych) z [żądaniem zwiększenia limitu przydziału Menedżer zasobów Core](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Sprawdź wersję wydania

Porównaj wersję klastra z najnowszą wersją usługi HDInsight. Każda wersja usługi HDInsight obejmuje ulepszenia, takie jak nowe aplikacje, funkcje, poprawki i poprawki błędów. Problem, który ma wpływ na klaster, mógł zostać rozwiązany w najnowszej wersji. Jeśli to możliwe, uruchom ponownie klaster przy użyciu najnowszej wersji usługi HDInsight i skojarzonych bibliotek, takich jak Apache HBase, Apache Spark i innych.

#### <a name="restart-your-cluster-services"></a>Ponowne uruchamianie usług klastra

Jeśli występują spowolnienia w klastrze, Rozważ ponowne uruchomienie usług za pomocą interfejsu użytkownika Ambari lub klasycznego interfejsu wiersza polecenia platformy Azure. W klastrze mogą występować błędy przejściowe, a ponowne uruchomienie jest najszybszym sposobem stabilizacji środowiska i możliwego zwiększenia wydajności.

## <a name="step-3-view-your-clusters-health"></a>Krok 3. Wyświetlanie kondycji klastra

Klastry usługi HDInsight składają się z różnych typów węzłów działających w wystąpieniach maszyn wirtualnych. Każdy węzeł może być monitorowany w przypadku zablokowania zasobów, problemy z łącznością sieciową oraz inne problemy, które mogą spowolnić klaster. Każdy klaster zawiera dwa węzły główne i większość typów klastrów zawiera kombinację procesów roboczych i węzłów brzegowych. 

Aby uzyskać opis różnych węzłów używanych przez każdy typ klastra, zobacz [Konfigurowanie klastrów w usłudze HDInsight z Apache Hadoop, Apache Spark, Apache Kafka i nie tylko](hdinsight-hadoop-provision-linux-clusters.md).

W poniższych sekcjach opisano, jak sprawdzić kondycję każdego węzła i całego klastra.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Utwórz migawkę kondycji klastra przy użyciu pulpitu nawigacyjnego interfejsu użytkownika Ambari

[Pulpit nawigacyjny interfejsu użytkownika Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) ( `https://<clustername>.azurehdinsight.net` ) zawiera omówienie kondycji klastra, takie jak czas przestoju, pamięć, użycie sieci i procesora, dysk systemu plików HDFS i tak dalej. Aby wyświetlić zasoby na poziomie hosta, użyj sekcji hosty w Ambari. Możesz również zatrzymać i ponownie uruchomić usługi.

### <a name="check-your-webhcat-service"></a>Sprawdź usługę WebHCat

Jeden typowy scenariusz dla zadań Apache Hive, Apache chlewnej lub Apache Sqoop kończy się niepowodzeniem z usługą [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (lub *Templeton*). WebHCat to interfejs REST dla zdalnego wykonywania zadań, na przykład Hive, świnie, informacje i MapReduce. WebHCat tłumaczy żądania przesłania zadania na Apache Hadoop aplikacji PRZĘDZy i zwraca stan uzyskany na podstawie stanu aplikacji PRZĘDZy.  W poniższych sekcjach opisano typowe kody stanu HTTP WebHCat.

#### <a name="badgateway-502-status-code"></a>BadGateway (kod stanu 502)

Ten kod jest komunikatem ogólnym z węzłów bramy i jest najbardziej typowymi kodami stanu błędu. Jedną z możliwych przyczyn jest to, że usługa WebHCat jest wyłączona w aktywnym węźle głównym. Aby sprawdzić tę możliwość, użyj następującego polecenia:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari wyświetla alert pokazujący hosty, na których nie działa usługa WebHCat. Można spróbować wykonać kopię zapasową usługi WebHCat, uruchamiając ponownie usługę na hoście.

![Serwer Apache Ambari ponowne uruchomienie serwera WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Jeśli serwer WebHCat nadal nie działa, sprawdź dziennik operacji pod kątem komunikatów o niepowodzeniu. Aby uzyskać szczegółowe informacje, sprawdź `stderr` pliki i, `stdout` do których odwołuje się węzeł.

#### <a name="webhcat-times-out"></a>WebHCat limit czasu

HDInsight An Brama przekroczy limit czasu odpowiedzi trwających dłużej niż dwie minuty `502 BadGateway` . WebHCat wysyła zapytania do usług PRZĘDZy dla stanów zadań, a w przypadku, gdy trwa dłużej niż dwie minuty, żądanie może przekroczyć limit czasu.

W takim przypadku zapoznaj się z poniższymi dziennikami w `/var/log/webhcat` katalogu:

* **webhcat. log** to dziennik Log4J, w którym serwer zapisuje dzienniki
* **webhcat-Console. log** to stdout serwera po uruchomieniu
* **webhcat-Console-Error. log** to stderr procesu serwera

> [!NOTE]  
> Każda `webhcat.log` z nich jest rzutowana codziennie, generując pliki o nazwie `webhcat.log.YYYY-MM-DD` . Wybierz odpowiedni plik dla przedziału czasu, który badasz.

W poniższych sekcjach opisano niektóre możliwe przyczyny przekroczenia limitu czasu WebHCat.

##### <a name="webhcat-level-timeout"></a>Limit czasu WebHCat poziomu

Gdy WebHCat jest w obciążeniu, z więcej niż 10 otwartymi gniazdami, trwa już nawiązywanie nowych połączeń gniazda, co może spowodować przekroczenie limitu czasu. Aby wyświetlić listę połączeń sieciowych z i z WebHCat, użyj `netstat`  na bieżącym aktywnym węzła głównego:

```bash
netstat | grep 30111
```

30111 to port WebHCat nasłuchuje. Liczba otwartych gniazd powinna być mniejsza niż 10.

Jeśli nie ma otwartych gniazd, poprzednie polecenie nie wygenerowało wyniku. Aby sprawdzić, czy Templeton działa i nasłuchuje na porcie 30111, użyj:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Limit czasu na poziomie PRZĘDZy

Templeton wywołuje PRZĘDZę do uruchamiania zadań, a komunikacja między Templeton i PRZĘDZą może spowodować przekroczenie limitu czasu.

Na poziomie PRZĘDZenia istnieją dwa typy limitów czasu:

1. Przesyłanie zadania PRZĘDZy może trwać wystarczająco długo, aby spowodować przekroczenie limitu czasu.

    Jeśli otworzysz `/var/log/webhcat/webhcat.log` plik dziennika i wyszukasz "zadanie w kolejce", zobaczysz wiele wpisów, w których czas wykonywania jest zbyt długi (>2000 MS) z wpisami pokazującymi rosnące czasy oczekiwania.

    Czas trwania zadań w kolejce nadal rośnie, ponieważ szybkość, z jaką są wysyłane nowe zadania, jest wyższa niż szybkość, z jaką są wykonywane stare zadania. Gdy pamięć PRZĘDZy jest używana do 100%, *Kolejka joblauncher* nie może już pożyczać zdolności produkcyjnych z *kolejki domyślnej*. W związku z tym nie można zaakceptować więcej nowych zadań w kolejce joblauncher. Takie zachowanie może spowodować dłuższe i dłuższe czas oczekiwania, powodując błąd przekroczenia limitu czasu, który zwykle występuje przez wiele innych.

    Na poniższej ilustracji przedstawiono kolejkę joblauncher na 714,4% nadużycia. Jest to akceptowalne, o ile nadal trwa bezpłatna pojemność w kolejce domyślnej. Jeśli jednak klaster jest w pełni wykorzystany i pojemność pamięci PRZĘDZy wynosi o 100%, nowe zadania muszą oczekiwać, co ostatecznie powoduje przekroczenie limitu czasu.

    ![Widok kolejki uruchamiania zadań usługi HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Istnieją dwa sposoby rozwiązania tego problemu: Zmniejsz szybkość przesyłania nowych zadań lub Zwiększ szybkość użycia starych zadań, skalując klaster.

2. Przetwarzanie PRZĘDZy może zająć dużo czasu, co może spowodować przekroczenie limitu czasu.

    * Wyświetl listę wszystkich zadań: jest to czasochłonne wywołanie. To wywołanie wylicza aplikacje z datasourcemanager, a dla każdej ukończonej aplikacji Pobiera stan z PRZĘDZy JobHistoryServer. W przypadku większej liczby zadań to wywołanie może przekroczyć limit czasu.

    * Wyświetl listę zadań starszych niż siedem dni: Usługa HDInsight PRZĘDZy JobHistoryServer jest skonfigurowana tak, aby zachować ukończone informacje o zadaniu przez siedem dni ( `mapreduce.jobhistory.max-age-ms` wartość). Próba wyliczenia przeczyszczonych zadań skutkuje przekroczeniem limitu czasu.

Aby zdiagnozować te problemy:

1. Określanie zakresu czasu UTC do rozwiązywania problemów
2. Wybierz odpowiednie `webhcat.log` pliki
3. Szukaj ostrzeżeń i komunikatów o BŁĘDach w tym czasie

#### <a name="other-webhcat-failures"></a>Inne błędy WebHCat

1. Kod stanu HTTP 500

    W większości przypadków, gdy WebHCat zwraca 500, komunikat o błędzie zawiera szczegóły dotyczące błędu. W przeciwnym razie poszukaj `webhcat.log` ostrzeżeń i komunikatów o błędach.

2. Błędy zadania

    Mogą wystąpić sytuacje, w których interakcje z usługą WebHCat są pomyślne, ale zadania kończą się niepowodzeniem.

    Templeton zbiera dane wyjściowe konsoli zadań w `stderr` programie `statusdir` , co jest często przydatne do rozwiązywania problemów. `stderr` zawiera identyfikator aplikacji PRZĘDZy rzeczywistego zapytania.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4. przegląd stosu i wersji środowiska

Na stronie **stos i wersja** interfejsu użytkownika Ambari dostępne są informacje na temat konfiguracji usług klastra i historii wersji usługi.  Niepoprawna wersja biblioteki usługi Hadoop może być przyczyną awarii klastra.  W interfejsie użytkownika Ambari wybierz menu **administrator** , a następnie kliknij  **stosy i wersje**.  Wybierz kartę **wersje** na stronie, aby wyświetlić informacje o wersji usługi:

![Stos i wersje oprogramowania Apache Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5. Sprawdzanie plików dziennika

Istnieje wiele typów dzienników, które są generowane na podstawie wielu usług i składników wchodzących w skład klastra usługi HDInsight. [Pliki dziennika WebHCat](#check-your-webhcat-service) są opisane wcześniej. Istnieje kilka innych przydatnych plików dziennika, które można zbadać w celu zawężenia problemów z klastrem, zgodnie z opisem w poniższych sekcjach.

* Klastry usługi HDInsight składają się z kilku węzłów, z których większość jest zadaniem do uruchamiania przesłanych zadań. Zadania są uruchamiane współbieżnie, ale w plikach dziennika mogą być wyświetlane tylko wyniki liniowe. Usługa HDInsight wykonuje nowe zadania, kończąc inne, które kończą się niepowodzeniem. Wszystkie te działania są rejestrowane w `stderr` plikach i `syslog` .

* W plikach dziennika akcji skryptu są wyświetlane błędy lub nieoczekiwane zmiany konfiguracji podczas procesu tworzenia klastra.

* Dzienniki kroków usługi Hadoop identyfikują zadania usługi Hadoop uruchomione w ramach kroku zawierającego błędy.

### <a name="check-the-script-action-logs"></a>Sprawdź dzienniki akcji skryptu

[Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md) usługi HDInsight uruchamiają skrypty ręcznie lub po ich określeniu. Na przykład akcje skryptu mogą służyć do instalowania dodatkowego oprogramowania w klastrze lub do zmiany ustawień konfiguracji z wartości domyślnych. Sprawdzanie dzienników akcji skryptu może zapewnić wgląd w błędy, które wystąpiły podczas instalacji i konfiguracji klastra.  Stan akcji skryptu można wyświetlić, wybierając przycisk **Ops** w interfejsie użytkownika Ambari lub uzyskując dostęp do dzienników z domyślnego konta magazynu.

Dzienniki akcji skryptów znajdują się w `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` katalogu.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Wyświetlanie dzienników usługi HDInsight przy użyciu szybkich linków Ambari

Interfejs użytkownika usługi HDInsight Ambari zawiera kilka sekcji **szybkie łącza** .  Aby uzyskać dostęp do linków dziennika dla określonej usługi w klastrze usługi HDInsight, Otwórz interfejs użytkownika Ambari dla klastra, a następnie wybierz link usługi z listy znajdującej się po lewej stronie. Wybierz listę rozwijaną **szybkie linki** , a następnie węzeł usługi HDInsight, który jest interesujący, a następnie wybierz link do skojarzonego z nim dziennika.

Na przykład w przypadku dzienników HDFS:

![Ambari szybkie linki do plików dziennika](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Wyświetlanie plików dzienników generowanych przez usługi Hadoop

Klaster HDInsight An generuje dzienniki zapisane w tabelach platformy Azure i usłudze Azure Blob Storage. PRZĘDZa tworzy własne dzienniki wykonania. Aby uzyskać więcej informacji, zobacz [Zarządzanie dziennikami klastra usługi HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Przejrzyj Zrzuty sterty

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w tym czasie, które są przydatne do diagnozowania problemów występujących w czasie wykonywania. Aby uzyskać więcej informacji, zobacz [Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6. sprawdzenie ustawień konfiguracji

Klastry usługi HDInsight są wstępnie skonfigurowane przy użyciu domyślnych ustawień dla powiązanych usług, takich jak Hadoop, Hive, HBase i tak dalej. W zależności od typu klastra, jego konfiguracji sprzętowej, liczby węzłów, typów wykonywanych zadań i danych, z którymi pracujesz (oraz sposobu przetwarzania danych), może być konieczne zoptymalizowanie konfiguracji.

Aby uzyskać szczegółowe instrukcje dotyczące optymalizacji konfiguracji wydajności dla większości scenariuszy, zobacz [Optymalizowanie konfiguracji klastra za pomocą platformy Apache Ambari](hdinsight-changing-configs-via-ambari.md). W przypadku korzystania z platformy Spark zobacz [optymalizacja Apache Spark zadań pod kątem wydajności](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7. odtworzenie awarii w innym klastrze

Aby ułatwić zdiagnozowanie źródła błędu klastra, należy uruchomić nowy klaster z tą samą konfiguracją, a następnie ponownie przesłać kroki zadania zakończonego niepowodzeniem. Sprawdź wyniki każdego kroku przed przetworzeniem kolejnego. Ta metoda pozwala poprawić i ponownie uruchomić pojedynczy krok zakończony niepowodzeniem. Ta metoda ma również zalety ładowania danych wejściowych tylko raz.

1. Utwórz nowy klaster testowy z taką samą konfiguracją co klaster, który uległ awarii.
2. Prześlij pierwszy etap zadania do klastra testowego.
3. Po zakończeniu przetwarzania kroku Sprawdź błędy w plikach dziennika kroków. Połącz się z węzłem głównym klastra testowego i przejrzyj tam pliki dziennika. Pliki dziennika kroków są wyświetlane tylko po uruchomieniu kroku przez jakiś czas, zakończenie lub niepowodzenie.
4. Jeśli pierwszy krok zakończył się pomyślnie, uruchom następny krok. Jeśli wystąpiły błędy, zbadaj błąd w plikach dziennika. Jeśli wystąpił błąd w kodzie, wprowadź poprawkę i ponownie uruchom ten krok.
5. Kontynuuj do momentu uruchomienia wszystkich kroków bez błędu.
6. Po zakończeniu debugowania klastra testowego należy go usunąć.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)
* [Dostęp do Apache Hadoop podpisywania aplikacji PRZĘDZy w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Znane problemy dotyczące klastra Apache Spark w usłudze HDInsight](hdinsight-apache-spark-known-issues.md)
