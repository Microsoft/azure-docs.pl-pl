---
title: Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi
description: Dowiedz się, jak wdrażać i monitorować topologie Apache Storm i zarządzać nimi za pomocą pulpitu nawigacyjnego burzy w usłudze HDInsight opartej na systemie Linux. Użyj narzędzi Hadoop dla programu Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 9b190b5847c7412344e2bb09fd4000985816219b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995594"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi

W tym dokumencie przedstawiono podstawowe informacje dotyczące zarządzania i monitorowania topologii [Apache Storm](https://storm.apache.org/) działających w ramach burzy w klastrach usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.

* Obowiązkowe Znajomość Secure Shell (SSH) i bezpiecznego kopiowania (SCP). Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Obowiązkowe Visual Studio, zestaw Azure SDK 2.5.1 lub nowszy oraz Data Lake Tools for Visual Studio. Aby uzyskać więcej informacji, zobacz [Apache Hadoop & narzędzia Data Lake Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Przesyłanie topologii przy użyciu programu Visual Studio

Narzędzia Data Lake Tools for Visual Studio umożliwiają przesyłanie do klastra burzy topologii języka C# lub hybrydowego. W poniższych krokach użyto przykładowej aplikacji. Aby uzyskać informacje na temat tworzenia topologii za pomocą narzędzi Data Lake, zobacz [Apache Storm topologii z programem Visual Studio i C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Jeśli nie zainstalowano jeszcze najnowszej wersji narzędzi Data Lake Tools for Visual Studio, zobacz [Korzystanie z narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Narzędzia Azure Data Lake i Stream Analytics były wcześniej nazywane narzędziami HDInsight Tools for Visual Studio.
    >
    > Narzędzia Azure Data Lake i Stream Analytics Tools for Visual Studio są dołączone do obciążeń **programistycznych platformy Azure** dla programu visual Studio 2019.

1. Uruchom program Visual Studio.

1. W oknie **uruchamiania** wybierz pozycję **Utwórz nowy projekt**.

1. W oknie **Tworzenie nowego projektu** wybierz pole wyszukiwania, a następnie wprowadź `Storm` . Następnie wybierz z listy wyników pozycję **przykład burzy** , a następnie wybierz pozycję **dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, a następnie przejdź do lub Utwórz **lokalizację** , w której ma zostać zapisany nowy projekt. Następnie wybierz przycisk **Utwórz**.

    ![Konfigurowanie okna nowego projektu, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. W **Eksplorator serwera** kliknij prawym przyciskiem myszy pozycję **Azure** i wybierz pozycję **Połącz z Microsoft Azure subskrypcją...** i Ukończ proces logowania.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Prześlij do burzy w usłudze HDInsight**.

    > [!NOTE]  
    > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do niej, która zawiera swoją burzę w klastrze usługi HDInsight.

1. W oknie dialogowym **przesyłanie topologii** na liście rozwijanej **klaster burzy** wybierz swoją burzę w klastrze usługi HDInsight, a następnie wybierz pozycję **Prześlij**. Możesz monitorować, czy przesyłanie zakończy się pomyślnie, wyświetlając okienko **dane wyjściowe** .

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Przesyłanie topologii przy użyciu protokołu SSH i polecenia burzy

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z poziomu sesji SSH Użyj następującego polecenia, aby uruchomić topologię przykładu **WORDCOUNT** :

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia losowo generuje zdania, a następnie zlicza wystąpienia każdego wyrazu w zdaniach.

    > [!NOTE]  
    > Podczas przesyłania topologii do klastra należy najpierw skopiować plik JAR zawierający klaster przed użyciem `storm` polecenia. Aby skopiować plik do klastra, można użyć `scp` polecenia. Na przykład wprowadź `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > Przykład *WORDCOUNT* i inne przykładowe przykłady burzy są już zawarte w klastrze w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Programistyczne przesyłanie topologii

Można programowo wdrożyć topologię przy użyciu usługi Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) zawiera przykładową aplikację Java, która demonstruje sposób wdrażania i uruchamiania topologii za pomocą usługi Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorowanie i zarządzanie topologią w programie Visual Studio

W przypadku przesyłania topologii przy użyciu programu Visual Studio zostanie wyświetlone okno **Widok topologia burza** . Wybierz topologię z listy, aby wyświetlić informacje o uruchomionej topologii.

![Topologia monitora, okno widoku topologii burzy, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Można również wyświetlić **topologie burzy** w **Eksplorator serwera**. Rozwiń węzeł **Azure**  >  **HDInsight**, kliknij prawym przyciskiem myszy burzę w klastrze usługi HDInsight, a następnie wybierz pozycję **Wyświetl topologie burzy**.

Wybierz kształt elementy Spout lub piorunów, aby wyświetlić informacje o tych składnikach. Zostanie wyświetlona etykietka narzędzia zawierająca informacje o składniku dla wybranego elementu.

### <a name="deactivate-and-reactivate-a-topology"></a>Dezaktywowanie i ponowne uaktywnianie topologii

Dezaktywacja topologii wstrzymuje ją do momentu zabicia lub ponownej aktywacji topologii. Aby wykonać te operacje, użyj przycisków **Dezaktywuj** i **Uaktywnij ponownie** w obszarze **działania** w górnej części okna **Widok topologii burza** .

### <a name="rebalance-a-topology"></a>Ponowne równoważenie topologii

Ponowne równoważenie topologii umożliwia systemowi skorygowanie równoległości topologii. Na przykład jeśli Zmieniono rozmiar klastra w celu dodania kolejnych notatek, ponowne zrównoważenie umożliwia topologii wyświetlanie nowych węzłów.

Aby ponownie zrównoważyć topologię, użyj przycisku **Zrównoważ** w obszarze **działania** okna **Widok topologii burza** .

> [!WARNING]  
> Ponowne równoważenie topologii powoduje dezaktywację topologii, ponowną dystrybucję procesów roboczych w klastrze, a następnie zwraca topologię do stanu, w którym znajdowała się przed ponownym zrównoważeniem. Jeśli topologia była aktywna, zostanie ponownie uaktywniona. Jeśli topologia została zdezaktywowana, pozostanie zdezaktywowana.

### <a name="kill-a-running-topology"></a>Kasuj uruchomioną topologię

Topologie burzy są nadal uruchomione, dopóki nie zostaną zatrzymane lub klaster zostanie usunięty. Aby zatrzymać topologię, użyj przycisku **Kill** w obszarze **Akcje** .

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorowanie topologii i zarządzanie nią przy użyciu protokołu SSH oraz polecenia burza

`storm`Narzędzie pozwala pracować z uruchamianiem topologii z poziomu wiersza polecenia. Użyj `storm -h` , aby uzyskać pełną listę poleceń.

### <a name="list-topologies"></a>Wyświetl topologie

Użyj następującego polecenia, aby wyświetlić listę wszystkich uruchomionych topologii:

```shell
storm list
```

To polecenie zwraca informacje podobne do następującego tekstu:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Dezaktywowanie i ponowne uaktywnianie topologii

Dezaktywacja topologii wstrzymuje ją do momentu zabicia lub ponownej aktywacji topologii. Użyj następujących poleceń, aby dezaktywować lub ponownie uaktywnić:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Kasuj uruchomioną topologię

Topologie burzy, po uruchomieniu, kontynuują działanie do momentu zatrzymania. Aby zatrzymać topologię, użyj następującego polecenia:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Ponowne równoważenie topologii

Ponowne równoważenie topologii umożliwia systemowi skorygowanie równoległości topologii. Na przykład jeśli Zmieniono rozmiar klastra w celu dodania kolejnych notatek, ponowne zrównoważenie umożliwia topologii wyświetlanie nowych węzłów.

> [!WARNING]  
> Ponowne równoważenie topologii powoduje dezaktywację topologii, ponowną dystrybucję procesów roboczych w klastrze, a następnie zwraca topologię do stanu, w którym znajdowała się przed ponownym zrównoważeniem. Jeśli topologia była aktywna, zostanie ponownie uaktywniona. Jeśli została zdezaktywowana, pozostanie zdezaktywowana.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorowanie topologii i zarządzanie nią przy użyciu interfejsu użytkownika burzy

Interfejs użytkownika burzy udostępnia interfejs sieci Web służący do pracy z uruchomionymi topologiami i znajduje się w klastrze usługi HDInsight. Aby wyświetlić interfejs użytkownika burzy, użyj przeglądarki sieci Web `https://CLUSTERNAME.azurehdinsight.net/stormui` , gdzie *ClusterName* jest nazwą klastra.

> [!NOTE]  
> Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź nazwę i hasło administratora klastra, które zostały użyte podczas tworzenia klastra.

### <a name="storm-ui-main-page"></a>Strona główna interfejsu użytkownika burzy

Na stronie głównej interfejsu użytkownika burzy są dostępne następujące informacje:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie klastra| Podstawowe informacje o klastrze burzowym. |
| Podsumowanie Nimbus | Lista podstawowych informacji Nimbus. |
| Podsumowanie topologii | Lista uruchomionych topologii. Aby wyświetlić więcej informacji na temat określonej topologii, wybierz jej łącze w kolumnie **Nazwa** . |
| Podsumowanie opiekuna | Informacje o Inspektorze burzy. Aby wyświetlić zasoby procesów roboczych skojarzonych z określonym nadzorem, wybierz jego łącze w kolumnie **host** lub **ID** . |
| Konfiguracja Nimbus | Nimbus konfigurację klastra. |

Strona główna interfejsu użytkownika burzy wygląda podobnie do tej strony sieci Web:

![Strona główna, interfejs użytkownika burzy, topologie Apache Storm, platforma Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Podsumowanie topologii

Wybranie linku z sekcji **Podsumowanie topologii** zawiera następujące informacje o topologii:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie topologii | Podstawowe informacje o topologii. |
| Akcje topologii| Akcje zarządzania, które można wykonać dla topologii. Dostępne akcje zostały opisane w dalszej części tej sekcji. |
| Statystyka topologii | Statystyka topologii. Aby ustawić przedział czasu dla wpisu w tej sekcji, wybierz jego łącze w kolumnie **okno** . |
| Elementy Spout *(przedział czasu)* | Elementy Spout używany przez topologię. Aby wyświetlić więcej informacji na temat określonego elementu Spout, wybierz jego łącze w kolumnie **ID** . |
| Pioruny *(ramy czasowe)* | Pioruny używane przez topologię. Aby wyświetlić więcej informacji na temat określonego pioruna, wybierz jego łącze w kolumnie **ID** . |
| Zasoby procesu roboczego | Lista zasobów procesów roboczych. Aby wyświetlić więcej informacji na temat określonego zasobu procesu roboczego, wybierz jego łącze w kolumnie **hosta** . |
| Wizualizacja topologii | Przycisk **Pokaż wizualizację** , który wyświetla wizualizację topologii. |
| Konfiguracja topologii | Konfiguracja wybranej topologii. |

Strona podsumowania topologii burzy wygląda podobnie do tej strony sieci Web:

![Strona podsumowania topologii, interfejs użytkownika burzy, Apache Storm, platforma Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

W sekcji **Akcje topologii** można wybrać następujące przyciski, aby wykonać akcję:

| Przycisk | Opis |
| --- | --- |
| Activate | Wznawia przetwarzanie zdezaktywowanej topologii. |
| Dezaktywuj | Wstrzymuje uruchomioną topologię. |
| Ponownego równoważenia | Dostosowuje równoległość topologii. Należy ponownie zrównoważyć uruchomione topologie po zmianie liczby węzłów w klastrze. Ta operacja pozwala topologii dostosować równoległość, aby skompensować dodatkową lub ograniczoną liczbę węzłów w klastrze.<br/><br/>Aby uzyskać więcej informacji, zobacz <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Omówienie równoległości topologii Apache Storm</a>.
| Killbit | Kończy topologię burzy po określonym limicie czasu. |
| Debugowanie | Rozpoczyna sesję debugowania dla działającej topologii. |
| Zatrzymaj debugowanie | Kończy sesję debugowania dla działającej topologii. |
| Zmień poziom dziennika | Modyfikuje poziom dziennika debugowania. |

##### <a name="spout-and-bolt-summary"></a>Elementu Spout i piorun — podsumowanie

Wybranie elementu Spout z sekcji **elementy Spout** lub **pioruns** wyświetla następujące informacje dotyczące wybranego elementu:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie składników | Podstawowe informacje na temat elementu Spout lub pioruna. |
| Akcje składnika | **Debuguj** i **Zatrzymaj debugowanie** przycisków. |
| Elementu Spout Statystyka lub pioruna | Statystyka elementu Spout lub pioruna. Aby ustawić przedział czasu dla wpisu w tej sekcji, wybierz jego łącze w kolumnie **okno** . |
| (Tylko dla piorunów)<br/>Statystyki wejściowe *(ramy czasowe)* | Informacje o strumieniach wejściowych używanych przez pioruna. |
| Statystyki wyjściowe *(przedział czasu)* | Informacje o strumieniach emitowanych przez elementu Spout lub piorun. |
| Profilowanie i debugowanie | Kontroluje profilowanie i debugowanie składników na tej stronie. Można ustawić wartość **stan/limit czasu (minuty)** i można wybrać przyciski dla **JStack**, **Uruchom ponownie proces roboczy** i **stertę**. |
| Wykonawcy *(ramy czasowe)* | Informacje o wystąpieniach elementu Spout lub piorunów. Aby wyświetlić dziennik informacji diagnostycznych generowanych dla tego wystąpienia, wybierz wpis **portu** dla określonego wykonawcy. Możesz również wyświetlić zasoby procesu roboczego skojarzone z określonym wykonawcą, wybierając jego łącze w kolumnie **hosta** . |
| błędy | Wszystkie informacje o błędzie dla elementu Spout lub pioruna. |

Strona Podsumowanie pioruna burzy wygląda podobnie do tej strony sieci Web:

![Strona podsumowania błyskawicy, interfejs użytkownika burzy, Apache Storm, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorowanie topologii i zarządzanie nią przy użyciu interfejsu API REST

Interfejs użytkownika burzy jest oparty na interfejsie API REST, dzięki czemu można wykonywać podobne zadania związane z zarządzaniem i monitorowaniem za pomocą interfejsu API REST. Interfejsu API REST można użyć do tworzenia niestandardowych narzędzi do zarządzania i monitorowania topologii burzy.

Aby uzyskać więcej informacji, zobacz Interfejs [API REST interfejsu użytkownika Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Poniższe informacje dotyczą korzystania z interfejsu API REST z usługą Apache Storm w usłudze HDInsight.

> [!IMPORTANT]  
> Interfejs API REST burzy nie jest publicznie dostępny za pośrednictwem Internetu. Dostęp do niego należy uzyskać przy użyciu tunelu SSH do węzła głównego klastra usługi HDInsight. Aby uzyskać informacje na temat tworzenia i używania tunelu SSH, zobacz [Używanie tunelowania SSH do uzyskiwania dostępu do usługi Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI interfejsu API REST w klastrach usługi HDInsight opartych na systemie Linux jest dostępny pod adresem URL `https://HEADNODEFQDN:8744/api/v1/` , w którym zastąpisz *HEADNODEFQDN* z węzłem głównym. Nazwa domeny węzła głównego jest generowana podczas tworzenia klastra i nie jest statyczna.

W pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra można znaleźć na kilka sposobów:

| Metoda odnajdywania nazwy FQDN | Opis |
| --- | --- |
| Sesja SSH | Użyj polecenia `headnode -f` z sesji SSH do klastra. |
| Sieć Web Ambari | Na stronie sieci Web klastra Ambari ( `https://CLUSTERNAME.azurehdinsight.net` ) wybierz pozycję **usługi** w górnej części strony, a następnie wybierz pozycję **burza**. Na karcie **Podsumowanie** wybierz pozycję **serwer interfejsu użytkownika burzy**. W górnej części strony jest wyświetlana nazwa FQDN węzła, który hostuje interfejs użytkownika burzy i interfejs API REST. |
| Interfejs API REST usługi Ambari | Użyj polecenia, `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` Aby pobrać informacje o węźle, w którym działają interfejs użytkownika burzy i interfejs API REST. Zastąp dwa wystąpienia elementu *ClusterName* nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta użytkownika (administratora). W odpowiedzi wpis "host_name" danych wyjściowych JSON zawiera nazwę FQDN węzła. |

### <a name="authentication"></a>Authentication

Żądania kierowane do interfejsu API REST muszą używać *uwierzytelniania podstawowego*, dlatego należy użyć nazwy administratora i hasła dla klastra usługi HDInsight.

> [!NOTE]  
> Ponieważ uwierzytelnianie podstawowe jest wysyłane przy użyciu czystego tekstu, należy *zawsze* używać protokołu HTTPS do zabezpieczania komunikacji z klastrem.

### <a name="return-values"></a>Wartości zwracane

Informacje zwracane z interfejsu API REST mogą być używane tylko w ramach klastra. Na przykład w pełni kwalifikowana nazwa domeny (FQDN) zwracana dla serwerów [Apache ZooKeeper](https://zookeeper.apache.org/) nie jest dostępna z Internetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [opracowywać topologie oparte na języku Java za pomocą platformy Apache Maven](apache-storm-develop-java-topology.md).

Aby uzyskać listę więcej przykładowych topologii, zobacz [przykładowe topologie Apache Storm w usłudze Azure HDInsight](apache-storm-example-topology.md).
