---
title: Replikacja klastra HBase w sieciach wirtualnych — Azure HDInsight
description: Dowiedz się, jak skonfigurować replikację HBase z jednej wersji usługi HDInsight do innej na potrzeby równoważenia obciążenia, wysokiej dostępności, migracji bez przestojów i aktualizacji oraz odzyskiwania po awarii.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: cfcb3a5a601afadb9f3fcd71c24e18a9d7f27b9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946415"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurowanie replikacji klastra Apache HBase w usłudze Azure Virtual Networks

Dowiedz się, jak skonfigurować replikację [Apache HBase](https://hbase.apache.org/) w ramach sieci wirtualnej lub między dwiema sieciami wirtualnymi na platformie Azure.

Replikacja klastra używa metodologii wypychania źródła. Klaster HBase może być źródłem lub miejscem docelowym albo może jednocześnie spełnić obie role. Replikacja jest asynchroniczna. Celem replikacji jest spójność ostateczna. Gdy źródło otrzyma edytowanie do rodziny kolumn, gdy replikacja jest włączona, Edycja jest propagowana do wszystkich klastrów docelowych. Gdy dane są replikowane z jednego klastra do innego, śledzony jest klaster źródłowy i wszystkie klastry, które już wykorzystali dane, aby zapobiec pętli replikacji.

W tym artykule opisano Konfigurowanie replikacji źródła i miejsca docelowego. W przypadku innych topologii klastra zobacz [Przewodnik dotyczący odwołania Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Poniżej przedstawiono przypadki użycia replikacji HBase dla jednej sieci wirtualnej:

* Równoważenie obciążenia. Na przykład można uruchomić skanowanie lub zadania MapReduce w klastrze docelowym, a następnie pozyskać dane w klastrze źródłowym.
* Dodawanie wysokiej dostępności.
* Migrowanie danych z jednego klastra HBase do innego.
* Uaktualnianie klastra usługi Azure HDInsight z jednej wersji do innej.

Poniżej przedstawiono przypadki użycia replikacji HBase dla dwóch sieci wirtualnych:

* Konfigurowanie odzyskiwania po awarii.
* Równoważenie obciążenia i partycjonowanie aplikacji.
* Dodawanie wysokiej dostępności.

Klastry można replikować za pomocą skryptów [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) z usługi [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy z tym artykułem musisz mieć subskrypcję platformy Azure. Zobacz artykuł [Pobieranie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurowanie środowisk

Istnieją trzy opcje konfiguracji:

- Dwa klastry Apache HBase w jednej sieci wirtualnej platformy Azure.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w tym samym regionie.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w dwóch różnych regionach (replikacja geograficzna).

W tym artykule opisano scenariusz replikacji geograficznej.

Aby ułatwić Konfigurowanie środowisk, utworzono kilka [Azure Resource Manager szablonów](../../azure-resource-manager/management/overview.md). Jeśli wolisz skonfigurować środowiska przy użyciu innych metod, zobacz:

- [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Tworzenie klastrów Apache HBase na platformie Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Skonfiguruj dwie sieci wirtualne w dwóch różnych regionach

Aby użyć szablonu, który tworzy dwie sieci wirtualne w dwóch różnych regionach i połączenie sieci VPN między sieci wirtualnych, wybierz poniższy przycisk **Wdróż na platformie Azure** . Definicja szablonu jest przechowywana w [publicznym magazynie obiektów BLOB](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Niektóre z zakodowanych wartości w szablonie:

**Sieć wirtualna 1**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Zachodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix> — vnet1 |
| Prefiks przestrzeni adresowej | 10.1.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.1.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.1.255.0/27 |
| Nazwa bramy | vnet1gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| Adres IP bramy | vnet1gwip |

**Sieć wirtualna 2**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Wschodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix> — vnet2 |
| Prefiks przestrzeni adresowej | 10.2.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.2.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.2.255.0/27 |
| Nazwa bramy | vnet2gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| Adres IP bramy | vnet1gwip |

## <a name="setup-dns"></a>Konfigurowanie systemu DNS

W ostatniej sekcji szablon tworzy maszynę wirtualną Ubuntu w każdej z dwóch sieci wirtualnych.  W tej części należy zainstalować powiązanie na dwóch maszynach wirtualnych DNS, a następnie skonfigurować przekazywanie DNS na dwóch maszynach wirtualnych.

Aby zainstalować powiązanie, Yon musi znaleźć publiczny adres IP dwóch maszyn wirtualnych DNS.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Otwórz maszynę wirtualną DNS, wybierając pozycję **grupy zasobów > [nazwa grupy zasobów] > [vnet1DNS]**.  Nazwa grupy zasobów jest tworzona w ostatniej procedurze. Domyślne nazwy maszyn wirtualnych DNS to *vnet1DNS* i *vnet2NDS*.
3. Wybierz pozycję **Właściwości** , aby otworzyć stronę właściwości sieci wirtualnej.
4. Zapisz **publiczny adres IP**, a także Zweryfikuj **prywatny adres IP**.  Prywatny adres IP powinien być **10.1.0.4** dla vnet1DNS i **10.2.0.4** dla vnet2DNS.  
5. Zmień serwery DNS dla obu sieci wirtualnych, tak aby używały domyślnych (udostępnianych przez platformę Azure) serwerów DNS w celu zezwalania na dostęp przychodzący i wychodzący do pobierania pakietów w celu zainstalowania powiązania w poniższych krokach.

Aby zainstalować powiązanie, należy wykonać następującą procedurę:

1. Użyj protokołu SSH, aby nawiązać połączenie z __publicznym adresem IP__ maszyny wirtualnej DNS. Poniższy przykład nawiązuje połączenie z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zamień `sshuser` na konto użytkownika ssh określone podczas tworzenia maszyny wirtualnej DNS.

    > [!NOTE]  
    > Istnieje wiele sposobów uzyskania tego `ssh` Narzędzia. W systemach Linux, UNIX i macOS jest on dostarczany jako część systemu operacyjnego. W przypadku korzystania z systemu Windows należy wziąć pod uwagę jedną z następujących opcji:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu w systemie Windows 10](/windows/wsl/about)
    > * [Narzędziahttps://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSHhttps://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować powiązanie, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Skonfiguruj powiązanie do przekazywania żądań rozpoznawania nazw do lokalnego serwera DNS. Aby to zrobić, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.options` pliku:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Zastąp wartości w `goodclients` sekcji zakresem adresów IP dwóch sieci wirtualnych. Ta sekcja definiuje adresy, z których ten serwer DNS akceptuje żądania.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Aby zapisać plik, użyj __kombinacji klawiszy Ctrl + X__, __Y__, a następnie klawisz __Enter__.

4. W sesji SSH Użyj następującego polecenia:

    ```bash
    hostname -f
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

    ```output
    vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Ten tekst jest __sufiksem DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

    Należy również sprawdzić sufiks DNS z innego serwera DNS. Będzie ona potrzebna w następnym kroku.

5. Aby skonfigurować powiązanie do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.local` pliku:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Należy zastąpić `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` SUFIKS DNS innej sieci wirtualnej. A adres IP usługi przesyłania dalej to prywatny adres IP serwera DNS w innej sieci wirtualnej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __kombinacji klawiszy Ctrl + X__, __Y__, a następnie klawisz __Enter__.

6. Aby rozpocząć tworzenie powiązania, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby sprawdzić, czy powiązanie może rozpoznać nazwy zasobów w innej sieci wirtualnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Zastąp `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` wartość w pełni kwalifikowaną nazwą domeny (FQDN) maszyny wirtualnej DNS w innej sieci.
    >
    > Zamień na `10.2.0.4` __wewnętrzny adres IP__ niestandardowego serwera DNS w innej sieci wirtualnej.

    Odpowiedź będzie wyglądać podobnie do następującego tekstu:

    ```output
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Do tej pory nie można wyszukać adresu IP z innej sieci bez określonego adresu IP serwera DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do używania niestandardowego serwera DNS

Aby skonfigurować sieć wirtualną do używania niestandardowego serwera DNS zamiast programu rozpoznawania cyklicznego Azure, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz sieć wirtualną, a następnie wybierz pozycję __serwery DNS__.

2. Wybierz opcję __niestandardowy__, a następnie wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz pozycję __Zapisz__.

6. Otwórz maszynę wirtualną serwera DNS w vnet1, a następnie kliknij przycisk **Uruchom ponownie**.  Aby konfiguracja DNS zaczęła obowiązywać, należy ponownie uruchomić wszystkie maszyny wirtualne w sieci wirtualnej.
7. Powtórz kroki Skonfiguruj niestandardowy serwer DNS dla vnet2.

Aby przetestować konfigurację systemu DNS, można połączyć się z dwiema maszynami wirtualnymi DNS przy użyciu protokołu SSH, a następnie wysłać polecenie ping do serwera DNS innej sieci wirtualnej przy użyciu nazwy hosta. Jeśli to nie zadziała, użyj następującego polecenia, aby sprawdzić stan systemu DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Tworzenie klastrów Apache HBase

Utwórz klaster [Apache HBase](https://hbase.apache.org/) w każdej z dwóch sieci wirtualnych o następującej konfiguracji:

- **Nazwa grupy zasobów**: Użyj tej samej nazwy grupy zasobów, która została utworzona w sieci wirtualnej.
- **Typ klastra**: HBase
- **Wersja**: HBase 1.1.2 (HDI 3,6)
- **Lokalizacja**: Użyj tej samej lokalizacji co sieć wirtualna.  Domyślnie vnet1 jest *zachodnie stany USA*, a Vnet2 to *Wschodnie stany USA*.
- **Magazyn**: Utwórz nowe konto magazynu dla klastra.
- **Sieć wirtualna** (z ustawień zaawansowanych w portalu): wybierz pozycję vnet1, która została utworzona w ostatniej procedurze.
- **Podsieć**: Nazwa domyślna używana w szablonie to **subnet1**.

Aby upewnić się, że środowisko jest prawidłowo skonfigurowane, należy być w stanie wysłać polecenie ping do nazwy FQDN węzła głównego między tymi dwoma klastrami.

## <a name="load-test-data"></a>Ładowanie danych testowych

Podczas replikowania klastra należy określić tabele, które mają być replikowane. W tej sekcji załadujesz dane do klastra źródłowego. W następnej sekcji zostanie włączona replikacja między tymi dwoma klastrami.

Aby utworzyć tabelę **kontaktów** i wstawić dane do tabeli, postępuj zgodnie z instrukcjami w [samouczku Apache HBase: wprowadzenie do korzystania z usługi Apache HBase w usłudze HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Jeśli chcesz replikować tabele z niestandardowej przestrzeni nazw, musisz upewnić się, że odpowiednie niestandardowe przestrzenie nazw są również zdefiniowane w klastrze docelowym.
>

## <a name="enable-replication"></a>Włączanie replikacji

Poniższe kroki opisują sposób wywoływania skryptu akcji skryptu z Azure Portal. Aby uzyskać informacje o uruchamianiu akcji skryptu przy użyciu Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Aby włączyć replikację HBase z poziomu Azure Portal**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz źródłowy klaster HBase.
3. W menu klaster wybierz pozycję **Akcje skryptu**.
4. W górnej części strony wybierz pozycję **Prześlij nowy**.
5. Wybierz lub wprowadź następujące informacje:

   1. **Nazwa**: wprowadź **Włącz replikację**.
   2. **Adres URL skryptu bash**: wprowadź **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh** .
   3. **Nagłówek**: Upewnij się, że jest zaznaczone. Wyczyść inne typy węzłów.
   4. **Parametry**: następujące przykładowe parametry włączają replikację dla wszystkich istniejących tabel, a następnie kopiują wszystkie dane z klastra źródłowego do klastra docelowego:

    `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata`
    
      > [!NOTE]
      > Użyj nazwy hosta zamiast nazwy FQDN zarówno dla źródłowej, jak i docelowej.
      >
      > W tym instruktażu przyjęto założenie hn1 jako aktywnego węzła głównego. Sprawdź, czy klaster ma identyfikować aktywny węzeł główny.

6. Wybierz przycisk **Utwórz**. Wykonanie skryptu może zająć trochę czasu, szczególnie w przypadku użycia argumentu **-CopyData** .

Wymagane argumenty:

|Nazwa|Opis|
|----|-----------|
|-s,--SRC-klaster | Określa nazwę DNS źródłowego klastra HBase. Na przykład:-s hbsrccluster,--SRC-Cluster = hbsrccluster |
|-d,--DST-klaster | Określa nazwę DNS docelowego klastra HBase (Replica). Na przykład:-s dsthbcluster,--SRC-Cluster = dsthbcluster |
|-SP,--SRC-Ambari-Password | Określa hasło administratora dla Ambari w źródłowym klastrze HBase. |
|-DP,--DST-Ambari-Password | Określa hasło administratora dla usługi Ambari w docelowym klastrze HBase.|

Argumenty opcjonalne:

|Nazwa|Opis|
|----|-----------|
|-Su,--SRC-Ambari-User | Określa nazwę użytkownika administratora dla Ambari w źródłowym klastrze HBase. Wartość domyślna to **admin**. |
|-du,--DST-Ambari-User | Określa nazwę użytkownika administratora dla Ambari w docelowym klastrze HBase. Wartość domyślna to **admin**. |
|-t,--lista tabel | Określa tabele, które mają być replikowane. Na przykład:--Table-list = "Tabela1; tabela2; TABLE3". Jeśli nie określisz tabel, wszystkie istniejące tabele HBase są replikowane.|
|-m,--Machine | Określa węzeł główny, w którym jest uruchamiana akcja skryptu. Wartość powinna być wybierana na podstawie tego, który jest aktywnym węzłem głównym. Użyj tej opcji, gdy skrypt $0 jest uruchamiany jako akcja skryptu z poziomu portalu usługi HDInsight lub Azure PowerShell.|
|-CP,-CopyData | Włącza migrację istniejących danych w tabelach, w których włączono replikację. |
|-rpm,-replikacja-Phoenix-meta | Włącza replikację w tabelach systemu Phoenix. <br><br>*Tej opcji należy używać ostrożnie.* Zalecamy ponowne utworzenie tabel Phoenix w klastrach replik przed użyciem tego skryptu. |
|-h,--pomoc | Wyświetla informacje o użyciu. |

`print_usage()`Sekcja [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) zawiera szczegółowy opis parametrów.

Po pomyślnym wdrożeniu akcji skryptu można użyć protokołu SSH do nawiązania połączenia z docelowym klastrem HBase, a następnie sprawdzić, czy dane zostały zreplikowane.

### <a name="replication-scenarios"></a>Scenariusze replikacji

Na poniższej liście przedstawiono niektóre ogólne przypadki użycia i ich ustawienia parametrów:

- **Włącz replikację we wszystkich tabelach między tymi dwoma klastrami**. Ten scenariusz nie wymaga kopiowania ani migrowania istniejących danych w tabelach i nie używa tabel Phoenix. Użyj następujących parametrów:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>`

- **Włącz replikację dla określonych tabel**. Aby włączyć replikację dla Tabela1, tabela2 i TABLE3, użyj następujących parametrów:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"`

- **Włącz replikację w określonych tabelach i skopiuj istniejące dane**. Aby włączyć replikację dla Tabela1, tabela2 i TABLE3, użyj następujących parametrów:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata`

- **Włącz replikację we wszystkich tabelach i Replikuj metadane w Phoenix ze źródła do miejsca docelowego**. Replikacja metadanych w Phoenix nie jest idealnym rozwiązaniem. Należy jej używać ostrożnie. Użyj następujących parametrów:

  `-m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta`

## <a name="copy-and-migrate-data"></a>Kopiowanie i Migrowanie danych

Dostępne są dwa oddzielne skrypty akcji skryptu do kopiowania lub migrowania danych po włączeniu replikacji:

- [Skrypt dla małych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabele, które mają kilka gigabajtów o rozmiarze, a ogólna kopia powinna zakończyć się w ciągu mniej niż godziny)

- [Skrypt dla dużych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabele, które powinny trwać dłużej niż jedna godzina do skopiowania)

Aby wywołać akcję skryptu, można wykonać tę samą procedurę, która została opisana w artykule [Włączanie replikacji](#enable-replication) . Użyj następujących parametrów:

`-m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]`

`print_usage()`Sekcja [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Kopiuj określone tabele (TEST1, TEST2 i test3) dla wszystkich wierszy, które są edytowane do dzisiaj (bieżąca sygnatura czasowa)**:

  `-m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

  Oraz

  `-m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow`

- **Kopiuj określone tabele z określonym zakresem czasu**:

  `-m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"`

## <a name="disable-replication"></a>Wyłączanie replikacji

Aby wyłączyć replikację, użyj innego skryptu akcji skryptu z usługi [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Aby wywołać akcję skryptu, można wykonać tę samą procedurę, która została opisana w artykule [Włączanie replikacji](#enable-replication) . Użyj następujących parametrów:

`-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">`

`print_usage()`Sekcja [skryptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Wyłącz replikację we wszystkich tabelach**:

  `-m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all`

  lub

  `--src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>`

- **Wyłącz replikację w określonych tabelach (Tabela1, tabela2 i TABLE3)**:

  `-m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"`

> [!NOTE]
> Jeśli zamierzasz usunąć klaster docelowy, upewnij się, że został on usunięty z listy elementów równorzędnych klastra źródłowego. Można to zrobić, uruchamiając polecenie remove_peer ' 1 ' w HBase Shell w klastrze źródłowym. Niepowodzenie tego klastra źródłowego może nie działać prawidłowo.
>

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania replikacji Apache HBase w ramach sieci wirtualnej lub między dwiema sieciami wirtualnymi. Aby dowiedzieć się więcej o usłudze HDInsight i Apache HBase, zobacz następujące artykuły:

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Usługa HDInsight Apache HBase — Omówienie](./apache-hbase-overview.md)
* [Tworzenie klastrów Apache HBase na platformie Azure Virtual Network](./apache-hbase-provision-vnet.md)