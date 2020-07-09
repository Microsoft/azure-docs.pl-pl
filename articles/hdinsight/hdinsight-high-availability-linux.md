---
title: Wysoka dostępność dla usługi Hadoop — Azure HDInsight
description: Dowiedz się, jak klastry usługi HDInsight zwiększają niezawodność i dostępność przy użyciu dodatkowego węzła głównego. Dowiedz się, w jaki sposób to wpływa na usługi Hadoop, takie jak Ambari i Hive, a także jak łączyć się indywidualnie z każdym węzłem głównym przy użyciu protokołu SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Wysoka dostępność usługi Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 21ecf33291924097f076aa28088eb4eac652ce67
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849665"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight

Klastry usługi HDInsight zapewniają dwa węzły główne, aby zwiększyć dostępność i niezawodność usług Apache Hadoop i uruchomionych zadań.

Usługa Hadoop zapewnia wysoką dostępność i niezawodność dzięki replikacji usług i danych między wieloma węzłami w klastrze. Jednak standardowe dystrybucje usługi Hadoop zazwyczaj mają tylko jeden węzeł główny. Awaria pojedynczego węzła głównego może spowodować, że klaster przestanie działać. Usługa HDInsight oferuje dwie węzłów głównych, aby zwiększyć dostępność i niezawodność usługi Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Dostępność i niezawodność węzłów

Węzły w klastrze usługi HDInsight są implementowane przy użyciu Virtual Machines platformy Azure. W poniższych sekcjach omówiono typy poszczególnych węzłów używane w usłudze HDInsight.

> [!NOTE]  
> Nie wszystkie typy węzłów są używane dla typu klastra. Na przykład typ klastra usługi Hadoop nie ma żadnych węzłów Nimbus. Aby uzyskać więcej informacji na temat węzłów używanych przez typy klastrów usługi HDInsight, zobacz sekcję typy klastrów w dokumencie [Tworzenie klastrów Hadoop opartych na systemie Linux w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) .

### <a name="head-nodes"></a>Węzły główne

W celu zapewnienia wysokiej dostępności usług Hadoop Usługa HDInsight udostępnia dwa węzły główne. Oba węzły główne są aktywne i działają w klastrze usługi HDInsight jednocześnie. Niektóre usługi, takie jak Apache HDFS lub Apache Hadoop, są tylko "aktywne" w jednym węźle głównym w danym momencie. Inne usługi, takie jak serwera hiveserver2 lub Hive, są aktywne w obu węzłach głównych w tym samym czasie.

Aby uzyskać nazwy hostów dla różnych typów węzłów w klastrze, należy użyć [interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Nie należy kojarzyć wartości liczbowej z czy węzłem jest podstawowy czy pomocniczy. Wartość liczbowa jest obecna tylko w celu zapewnienia unikatowej nazwy dla każdego węzła.

### <a name="nimbus-nodes"></a>Nimbus węzły

Węzły Nimbus są dostępne w klastrach Apache Storm. Węzły Nimbus zapewniają podobną funkcjonalność do usługi Hadoop JobTracker przez dystrybuowanie i monitorowanie procesów między węzłami procesów roboczych. Usługa HDInsight udostępnia dwa węzły Nimbus dla klastrów burzowych

### <a name="apache-zookeeper-nodes"></a>Węzły Apache dozorcy

Węzły [dozorcy](https://zookeeper.apache.org/) są używane do wyboru lidera głównych usług w węzłach nadrzędnych. Są one również używane do upewnienia się, że usługi, węzły danych (Worker) i bramy wiedzą, z którym węzłem głównym jest aktywna usługa główna. Domyślnie Usługa HDInsight udostępnia trzy węzły dozorcy.

### <a name="worker-nodes"></a>Węzły procesu roboczego

Węzły procesu roboczego wykonują rzeczywistą analizę danych, gdy zadanie zostanie przesłane do klastra. Jeśli węzeł procesu roboczego zakończy się niepowodzeniem, zadanie, które było wykonywane, zostanie przesłane do innego węzła procesu roboczego. Domyślnie Usługa HDInsight tworzy cztery węzły procesu roboczego. Tę liczbę można zmienić, aby odpowiadała potrzebom zarówno podczas tworzenia klastra, jak i po nim.

### <a name="edge-node"></a>węzeł krawędzi

Węzeł brzegowy nie uczestniczy aktywnie w analizie danych w klastrze. Jest on używany przez deweloperów lub analityków danych podczas pracy z usługą Hadoop. Węzeł brzegowy znajduje się w tym samym Virtual Network platformy Azure co inne węzły w klastrze i może bezpośrednio uzyskiwać dostęp do wszystkich innych węzłów. Węzła brzegowego można używać bez konieczności przechodzenia zasobów do krytycznych usług Hadoop lub zadań analizy.

Obecnie usługi ML w usłudze HDInsight są jedynym typem klastra, który domyślnie udostępnia węzeł brzegowy. W przypadku usług ML w usłudze HDInsight węzeł brzegowy jest używany do testowania kodu R lokalnie w węźle przed przesłaniem go do klastra w celu przetwarzania rozproszonego.

Aby uzyskać informacje na temat korzystania z węzła brzegowego z innymi typami klastrów, zobacz [Używanie węzłów brzegowych w](hdinsight-apps-use-edge-node.md) dokumencie usługi HDInsight.

## <a name="accessing-the-nodes"></a>Uzyskiwanie dostępu do węzłów

Dostęp do klastra za pośrednictwem Internetu jest udostępniany przez bramę publiczną. Dostęp jest ograniczony do łączenia się z węzłami głównymi i, jeśli taki istnieje, węzeł brzegowy. Dostęp do usług uruchomionych w węzłach głównych nie ma wpływ na wiele węzłów głównych. Brama publiczna kieruje żądania do węzła głównego, który hostuje żądaną usługę. Na przykład jeśli Apache Ambari jest obecnie hostowany w pomocniczym węźle głównym, Brama kieruje żądania przychodzące dla Ambari do tego węzła.

Dostęp za pośrednictwem bramy publicznej jest ograniczony do portów 443 (HTTPS), 22 i 23.

|Port |Opis |
|---|---|
|443|Służy do uzyskiwania dostępu do Ambari i innych interfejsów API sieci Web lub interfejsów REST w węźle głównym.|
|22|Służy do uzyskiwania dostępu do podstawowego węzła głównego lub węzła krawędzi przy użyciu protokołu SSH.|
|23|Służy do uzyskiwania dostępu do pomocniczego węzła głównego przy użyciu protokołu SSH. Na przykład program `ssh username@mycluster-ssh.azurehdinsight.net` nawiązuje połączenie z podstawowym węzłem głównym klastra o nazwie "Moja **klaster**".|

Aby uzyskać więcej informacji na temat korzystania z protokołu SSH, zobacz dokument [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) .

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Wewnętrzne w pełni kwalifikowane nazwy domen (FQDN)

Węzły w klastrze usługi HDInsight mają wewnętrzny adres IP i nazwę FQDN, do których można uzyskać dostęp tylko z klastra. Podczas uzyskiwania dostępu do usług w klastrze przy użyciu wewnętrznej nazwy FQDN lub adresu IP należy użyć Ambari do sprawdzenia, czy adres IP lub nazwa FQDN mają być używane podczas uzyskiwania dostępu do usługi.

Na przykład usługa Apache Oozie może być uruchomiona tylko na jednym węźle głównym i użycie `oozie` polecenia z sesji SSH wymaga adresu URL usługi. Ten adres URL można pobrać z Ambari za pomocą następującego polecenia:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

To polecenie zwraca wartość podobną do następującej, która zawiera wewnętrzny adres URL do użycia z `oozie` poleceniem:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Aby uzyskać więcej informacji na temat pracy z interfejsem API REST Ambari, zobacz [monitorowanie i zarządzanie usługą HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Uzyskiwanie dostępu do innych typów węzłów

Można połączyć się z węzłami, które nie są bezpośrednio dostępne za pośrednictwem Internetu, przy użyciu następujących metod:

|Metoda |Opis |
|---|---|
|Protokół SSH|Po nawiązaniu połączenia z węzłem głównym przy użyciu protokołu SSH można nawiązać połączenie z innymi węzłami w klastrze przy użyciu protokołu SSH z węzła głównego. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunel SSH|Jeśli musisz uzyskać dostęp do usługi sieci Web hostowanej na jednym z węzłów, które nie są uwidocznione w Internecie, musisz użyć tunelu SSH. Aby uzyskać więcej informacji, zobacz artykuł [Używanie tunelu SSH z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) .|
|Azure Virtual Network|Jeśli klaster usługi HDInsight jest częścią Virtual Network platformy Azure, wszystkie zasoby w tym samym Virtual Network mogą bezpośrednio uzyskać dostęp do wszystkich węzłów w klastrze. Aby uzyskać więcej informacji, zapoznaj się z dokumentem [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md) .|

## <a name="how-to-check-on-a-service-status"></a>Jak sprawdzić stan usługi

Aby sprawdzić stan usług uruchomionych w węzłach głównych, użyj interfejsu użytkownika sieci Web Ambari lub interfejsu API REST Ambari.

### <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

Interfejs użytkownika sieci Web Ambari jest wyświetlany pod adresem `https://CLUSTERNAME.azurehdinsight.net` . Zastąp wartość **ClusterName** nazwą klastra. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia użytkownika HTTP dla klastra. Domyślną nazwą użytkownika HTTP jest **administrator** , a hasło to hasło wprowadzone podczas tworzenia klastra.

Po nadejściu na stronie Ambari zainstalowane usługi są wyświetlane po lewej stronie.

![Zainstalowane usługi Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Istnieje szereg ikon, które mogą pojawić się obok usługi, aby wskazać stan. Wszystkie alerty związane z usługą można wyświetlić za pomocą linku **alerty** w górnej części strony.  Ambari oferuje kilka wstępnie zdefiniowanych alertów.

Następujące alerty ułatwiają monitorowanie dostępności klastra:

| Nazwa alertu                               | Opis                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stan monitora metryki                    | Ten alert wskazuje stan procesu monitorowania metryk zgodnie z opisem w skrypcie stanu monitora.                                                                                   |
| Puls agenta Ambari                   | Ten alert jest wyzwalany, jeśli serwer utracił kontakt z agentem.                                                                                                                        |
| Proces serwera dozorcy                 | Ten alert na poziomie hosta jest wyzwalany, jeśli proces serwera dozorcy nie może zostać określony jako nasłuchuje w sieci.                                                               |
| Stan serwera metadanych IOCache           | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera metadanych IOCache i odpowiadać na żądania klientów                                                            |
| Interfejs użytkownika sieci Web JournalNode                       | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web JournalNode jest nieosiągalny.                                                                                                                 |
| Serwer Spark2 Thrift                     | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera Spark2 Thrift.                                                                                                |
| Proces serwera historii                   | Ten alert na poziomie hosta jest wyzwalany, jeśli proces serwera historii nie może zostać ustanowiony i nasłuchuje w sieci.                                                                |
| Interfejs użytkownika sieci Web serwera historii                    | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera historii jest nieosiągalny.                                                                                                              |
| `ResourceManager`Interfejs użytkownika sieci Web                   | Ten alert na poziomie hosta jest wyzwalany, jeśli `ResourceManager` interfejs użytkownika sieci Web jest nieosiągalny.                                                                                                             |
| Podsumowanie kondycji węzła nodemanager               | Ten alert na poziomie usługi jest wyzwalany w przypadku wystąpienia złej kondycji NodeManagers                                                                                                                    |
| Interfejs użytkownika sieci Web osi czasu aplikacji                      | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera osi czasu aplikacji jest nieosiągalny.                                                                                                         |
| Podsumowanie kondycji węzła datanode                  | Ten alert na poziomie usługi jest wyzwalany, jeśli istnieją węzły datanodes o złej kondycji                                                                                                                       |
| Interfejs użytkownika sieci Web NameNode                          | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web NameNode jest nieosiągalny.                                                                                                                    |
| Proces kontrolera trybu failover dozorcy    | Ten alert na poziomie hosta jest wyzwalany, jeśli proces kontrolera trybu failover dozorcy nie może zostać potwierdzony w sieci.                                                   |
| Interfejs użytkownika sieci Web serwera Oozie                      | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web serwera Oozie jest nieosiągalny.                                                                                                                |
| Stan serwera Oozie                      | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera Oozie i odpowiadać na żądania klientów.                                                                      |
| Proces magazynu metadanych Hive                   | Ten alert na poziomie hosta jest wyzwalany, jeśli proces magazynu metadanych Hive nie może zostać określony jako nasłuchuje w sieci.                                                                 |
| Proces serwera hiveserver2                      | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić HiveServer i odpowiadać na żądania klientów.                                                                        |
| Stan serwera WebHCat                    | Ten alert na poziomie hosta jest wyzwalany, jeśli `templeton` stan serwera nie jest w dobrej kondycji.                                                                                                            |
| Dostępne serwery dozorcy      | Ten alert jest wyzwalany, jeśli liczba serwerów dozorcy w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki testów procesu dozorcy.     |
| Serwer Spark2 usługi Livy                       | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera Livy2.                                                                                                        |
| Serwer historii Spark2                    | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera historii Spark2.                                                                                               |
| Proces modułów zbierających metryki                | Ten alert jest wyzwalany, jeśli moduł zbierający metryk nie może zostać potwierdzony i nasłuchuje na skonfigurowanym porcie przez liczbę sekund równą wartości progowej.                                 |
| Moduł zbierający metryk — proces HBase Master | Ten alert jest wyzwalany, jeśli procesy wzorca HBase modułu zbierającego metryk nie mogą zostać potwierdzone i nasłuchuje w sieci pod kątem skonfigurowanego progu krytycznego, podanego w sekundach. |
| Dostępne monitory metryk procentowych       | Ten alert jest wyzwalany, jeśli procent procesów monitora metryk nie działa i nie nasłuchuje w sieci pod kątem skonfigurowanych progów ostrzegawczych i krytycznych.                             |
| Procent dostępnych NodeManagers           | Ten alert jest wyzwalany, jeśli liczba w dół NodeManagers w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu Nodemanager.        |
| Kondycja węzła nodemanager                       | Ten alert na poziomie hosta sprawdza Właściwość kondycji węzła dostępną w składniku Nodemanager.                                                                                              |
| Interfejs użytkownika sieci Web nodemanager                       | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web Nodemanager jest nieosiągalny.                                                                                                                 |
| Kondycja NameNode wysokiej dostępności        | Ten alert na poziomie usługi jest wyzwalany, jeśli aktywne NameNode lub wstrzymanie NameNode nie jest uruchomione.                                                                                     |
| Proces datanode                         | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić poszczególnych procesów elementu datanode do nasłuchiwania w sieci.                                                         |
| Interfejs użytkownika sieci Web datanode                          | Ten alert na poziomie hosta jest wyzwalany, jeśli interfejs użytkownika sieci Web węzła datanode jest nieosiągalny.                                                                                                                    |
| Procent dostępnych JournalNodes           | Ten alert jest wyzwalany, jeśli liczba w dół JournalNodes w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki testów procesu JournalNode.        |
| Dostępne węzły datanodes              | Ten alert jest wyzwalany, jeśli liczba węzłów danych w dół w klastrze jest większa niż skonfigurowany próg krytyczny. Agreguje wyniki kontroli procesu elementu datanode.              |
| Stan serwera Zeppelin                   | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić serwera Zeppelin i odpowiadać na żądania klientów.                                                                   |
| Interaktywny proces serwera hiveserver2          | Ten alert na poziomie hosta jest wyzwalany, jeśli nie można ustalić HiveServerInteractive i odpowiadać na żądania klientów.                                                             |
| Aplikacja LLAP                         | Ten alert jest wyzwalany, jeśli nie można ustalić aplikacji LLAP i odpowiadać na żądania.                                                                                    |

Możesz wybrać każdą usługę, aby wyświetlić więcej informacji na jej temat.

Gdy strona usługi zawiera informacje o stanie i konfiguracji każdej usługi, nie zawiera informacji na temat węzła głównego, na którym działa usługa. Aby wyświetlić te informacje, Użyj linku **hosty** w górnej części strony. Na tej stronie są wyświetlane hosty w klastrze, w tym węzły główne.

![Lista hostów Apache Ambari węzła głównego](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Wybranie linku dla jednego z węzłów głównych spowoduje wyświetlenie usług i składników uruchomionych w tym węźle.

![Stan składnika Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [monitorowanie i zarządzanie usługą HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Interfejs API REST usługi Ambari

Interfejs API REST Ambari jest dostępny za pośrednictwem Internetu. Brama publiczna usługi HDInsight obsługuje żądania routingu do węzła głównego, który aktualnie obsługuje interfejs API REST.

Aby sprawdzić stan usługi za pomocą interfejsu API REST Ambari, można użyć następującego polecenia:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Zastąp **hasło** hasłem konta użytkownika http (Administrator).
* Zamień ciąg **CLUSTERNAME** na nazwę klastra.
* Zastąp wartość **ServiceName** nazwą usługi, dla której chcesz sprawdzić stan.

Na przykład aby sprawdzić stan usługi **HDFS** w klastrze o nazwie Moje **klastry**z hasłem **hasła**, użyj następującego polecenia:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Odpowiedź jest podobna do następującej:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

Adres URL informuje nas, że usługa jest obecnie uruchomiona w węźle głównym o nazwie My **cluster. wutj3h4ic1zejluqhxzvckxq0g**.

Stan informuje nas, że usługa jest obecnie uruchomiona lub **uruchomiona**.

Jeśli nie wiesz, jakie usługi są zainstalowane w klastrze, możesz użyć następującego polecenia, aby pobrać listę:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Aby uzyskać więcej informacji na temat pracy z interfejsem API REST Ambari, zobacz [monitorowanie i zarządzanie usługą HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Składniki usługi

Usługi mogą zawierać składniki, które chcą sprawdzać stan poszczególnych elementów. Na przykład system plików HDFS zawiera składnik NameNode. Aby wyświetlić informacje na temat składnika, polecenie będzie:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Jeśli nie wiesz, jakie składniki są udostępniane przez usługę, możesz użyć następującego polecenia, aby pobrać listę:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak uzyskać dostęp do plików dziennika w węzłach głównych

### <a name="ssh"></a>Protokół SSH

W przypadku połączenia z węzłem głównym za pośrednictwem protokołu SSH pliki dzienników można znaleźć w obszarze **/var/log**. Na przykład **/var/log/Hadoop-Yarn/Yarn** zawiera dzienniki dla przędzy.

Każdy węzeł główny może mieć unikatowe wpisy dziennika, dlatego należy sprawdzić dzienniki na obu.

### <a name="sftp"></a>SFTP

Możesz również nawiązać połączenie z węzłem głównym przy użyciu protokół transferu plików SSH lub bezpiecznego protokół transferu plików (SFTP) i pobrać pliki dziennika bezpośrednio.

Podobnie jak w przypadku korzystania z klienta SSH, podczas nawiązywania połączenia z klastrem należy podać nazwę konta użytkownika SSH i adres SSH klastra. Na przykład `sftp username@mycluster-ssh.azurehdinsight.net`. Podaj hasło do konta po wyświetleniu monitu lub Podaj klucz publiczny przy użyciu `-i` parametru.

Po nawiązaniu połączenia zostanie wyświetlony `sftp>` monit. Z tego monitu można zmienić katalogi, przekazać i pobrać pliki. Na przykład następujące polecenia powodują zmianę katalogów do katalogu **/var/log/Hadoop/HDFS** , a następnie pobranie wszystkich plików w katalogu.

```bash
cd /var/log/hadoop/hdfs
get *
```

Aby uzyskać listę dostępnych poleceń, wpisz `help` w wierszu polecenia `sftp>` .

> [!NOTE]  
> Istnieją również interfejsy graficzne, które umożliwiają wizualizację systemu plików w przypadku połączenia przy użyciu protokołu SFTP. Na przykład [MobaXTerm](https://mobaxterm.mobatek.net/) umożliwia przeglądanie systemu plików przy użyciu interfejsu podobnego do Eksploratora Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Aby uzyskać dostęp do plików dziennika za pomocą Ambari, należy użyć tunelu SSH. Interfejsy sieci Web dla poszczególnych usług nie są udostępniane publicznie w Internecie. Aby uzyskać informacje na temat korzystania z tunelu SSH, zobacz dokument [Używanie tunelowania SSH](hdinsight-linux-ambari-ssh-tunnel.md) .

W interfejsie użytkownika sieci Web Ambari wybierz usługę, dla której chcesz wyświetlić dzienniki (na przykład PRZĘDZę). Następnie użyj opcji **szybkie linki** , aby wybrać węzeł główny, dla którego mają być wyświetlone dzienniki.

![Używanie szybkich linków do wyświetlania dzienników](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Jak skonfigurować rozmiar węzła

Rozmiar węzła można wybrać tylko podczas tworzenia klastra. Listę różnych rozmiarów maszyn wirtualnych dostępnych dla usługi HDInsight można znaleźć na [stronie z cennikiem usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Podczas tworzenia klastra można określić rozmiar węzłów. Poniższe informacje zawierają wskazówki dotyczące sposobu określania rozmiaru przy użyciu [Azure Portal](https://portal.azure.com/), [Azure PowerShell module AZ](/powershell/azureps-cmdlets-docs)i [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

* **Azure Portal**: podczas tworzenia klastra można ustawić rozmiar węzłów używanych przez klaster:

    ![Obraz Kreatora tworzenia klastra z wybranym rozmiarem węzła](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Interfejs wiersza polecenia platformy Azure**: w przypadku korzystania z [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) poleceń można ustawić rozmiar węzłów głowy, procesu roboczego i dozorcy przy użyciu `--headnode-size` `--workernode-size` parametrów, i `--zookeepernode-size` .

* **Azure PowerShell**: w przypadku korzystania z polecenia cmdlet [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) można ustawić rozmiar węzłów główna, proces roboczy i dozorcy przy użyciu `-HeadNodeSize` `-WorkerNodeSize` parametrów, i `-ZookeeperNodeSize` .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat elementów omówionych w tym artykule, zobacz:

* [Dokumentacja REST Ambari Apache](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Zainstaluj i skonfiguruj moduł Azure PowerShell AZ](/powershell/azure/overview)
* [Zarządzanie usługą HDInsight przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Inicjowanie obsługi klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
