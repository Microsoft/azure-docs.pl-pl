---
title: Łączenie usługi Azure HDInsight z siecią lokalną
description: Dowiedz się, jak utworzyć klaster usługi HDInsight w usłudze Azure Virtual Network, a następnie połączyć go z siecią lokalną. Dowiedz się, jak skonfigurować rozpoznawanie nazw między usługą HDInsight a siecią lokalną przy użyciu niestandardowego serwera DNS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: cd787e1c846bfe4728577cbbce069385ce064a10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943397"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Łączenie usługi HDInsight z siecią lokalną

Dowiedz się, jak połączyć usługę HDInsight z siecią lokalną przy użyciu sieci wirtualnych platformy Azure i bramy sieci VPN. Ten dokument zawiera informacje dotyczące planowania:

* Korzystanie z usługi HDInsight w usłudze Azure Virtual Network, która łączy się z siecią lokalną.
* Konfigurowanie rozpoznawania nazw DNS między siecią wirtualną a siecią lokalną.
* Konfigurowanie sieciowych grup zabezpieczeń w celu ograniczenia dostępu do Internetu do usługi HDInsight.
* Porty udostępniane przez usługi HDInsight w sieci wirtualnej.

## <a name="overview"></a>Omówienie

Aby umożliwić usłudze HDInsight i zasobom w połączonej sieci komunikowanie się według nazwy, należy wykonać następujące czynności:

1. Utwórz Virtual Network platformy Azure.
1. Utwórz niestandardowy serwer DNS w usłudze Azure Virtual Network.
1. Skonfiguruj sieć wirtualną tak, aby korzystała z niestandardowego serwera DNS zamiast domyślnego programu rozpoznawania cyklicznego platformy Azure.
1. Skonfiguruj przekazywanie między niestandardowym serwerem DNS i lokalnym serwerem DNS.

Te konfiguracje zapewniają następujące zachowanie:

* Żądania dla w pełni kwalifikowanych nazw domen, które mają sufiks DNS __dla sieci wirtualnej__ , są przekazywane do NIESTANDARDOWEGO serwera DNS. Niestandardowy serwer DNS przekazuje te żądania do programu rozpoznawania cyklicznego Azure, który zwraca adres IP.
* Wszystkie inne żądania są przekazywane do lokalnego serwera DNS. Nawet żądania dotyczące publicznych zasobów internetowych, takich jak microsoft.com, są przekazywane do lokalnego serwera DNS w celu rozpoznawania nazw.

Na poniższym diagramie zielonymi wierszami są żądania dotyczące zasobów kończących się na sufiksie DNS sieci wirtualnej. Niebieskie linie to żądania dotyczące zasobów w sieci lokalnej lub w publicznym Internecie.

![Diagram przedstawiający sposób, w jaki żądania DNS są rozwiązywane w konfiguracji](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](/powershell/azure/).
* Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowano, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Utwórz konfigurację sieci wirtualnej

Skorzystaj z następujących dokumentów, aby dowiedzieć się, jak utworzyć Virtual Network platformy Azure, która jest połączona z siecią lokalną:

* [Korzystanie z witryny Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Korzystanie z programu Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Utwórz niestandardowy serwer DNS

> [!IMPORTANT]  
> Przed zainstalowaniem usługi HDInsight w sieci wirtualnej należy utworzyć i skonfigurować serwer DNS.

Poniższe kroki służą do tworzenia maszyny wirtualnej platformy Azure przy użyciu [Azure Portal](https://portal.azure.com) . Aby poznać inne sposoby tworzenia maszyny wirtualnej, zobacz temat [Tworzenie maszyny wirtualnej — interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md) i [Tworzenie maszyny wirtualnej — Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Aby utworzyć maszynę wirtualną z systemem Linux używającą oprogramowania [bind](https://www.isc.org/downloads/bind/) DNS, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  
1. W górnym menu wybierz pozycję **+ Utwórz zasób**.

    ![Tworzenie maszyny wirtualnej Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Wybierz pozycję **Oblicz**  >  **maszynę wirtualną** , aby przejść do strony **Tworzenie maszyny wirtualnej** .

1. Na karcie __podstawowe__ wprowadź następujące informacje:  
  
    | Pole | Wartość |
    | --- | --- |
    |Subskrypcja |Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów |Wybierz grupę zasobów zawierającą wcześniej utworzoną sieć wirtualną.|
    |Nazwa maszyny wirtualnej | Wprowadź przyjazną nazwę identyfikującą tę maszynę wirtualną. W tym przykładzie używa **DNSProxy**.|
    |Region (Region) | Wybierz ten sam region, w którym utworzono wcześniej sieć wirtualną.  Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach.  |
    |Opcje dostępności |  Wybierz żądany poziom dostępności.  Platforma Azure oferuje szeroką gamę opcji zarządzania dostępnością i odpornością aplikacji.  Zaarchitektj swoje rozwiązanie, aby używać zreplikowanych maszyn wirtualnych w Strefy dostępności lub zestawach dostępności w celu ochrony Twoich aplikacji i danych przed zdarzeniami awarii i konserwacji centrum danych. W tym przykładzie **nie jest wymagane użycie nadmiarowości infrastruktury**. |
    |Image (Obraz) | Pozostaw na **serwerze Ubuntu Server 18,04 LTS**. |
    |Typ uwierzytelniania | __Hasło__ lub __klucz publiczny SSH__: Metoda uwierzytelniania dla konta SSH. Zalecamy korzystanie z kluczy publicznych, ponieważ są one bezpieczniejsze. Ten przykład używa **hasła**.  Aby uzyskać więcej informacji, zobacz dokument [dotyczący tworzenia i używania kluczy SSH dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md) .|
    |Nazwa użytkownika |Wprowadź nazwę użytkownika administratora dla maszyny wirtualnej.  W tym przykładzie używa **sshuser**.|
    |Hasło lub klucz publiczny SSH | Dostępne pole jest określane przez wybór **typu uwierzytelniania**.  Wprowadź odpowiednią wartość.|
    |Publiczne porty wejściowe|Wybierz pozycję **Zezwalaj na wybrane porty**. Następnie wybierz pozycję **SSH (22)** z listy rozwijanej **Wybieranie portów przychodzących** .|

    ![Podstawowa konfiguracja maszyny wirtualnej](./media/connect-on-premises-network/virtual-machine-basics.png)

    Pozostaw inne wpisy z wartościami domyślnymi, a następnie wybierz kartę **Sieć** .

4. Na karcie **Sieć** wprowadź następujące informacje:

    | Pole | Wartość |
    | --- | --- |
    |Sieć wirtualna | Wybierz utworzoną wcześniej sieć wirtualną.|
    |Podsieć | Wybierz domyślną podsieć dla utworzonej wcześniej sieci wirtualnej. __Nie__ wybieraj podsieci używanej przez bramę sieci VPN.|
    |Publiczny adres IP | Użyj autowypełnianej wartości.  |

    ![Ustawienia sieci wirtualnej usługi HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Pozostaw inne wpisy z wartościami domyślnymi, a następnie wybierz pozycję **Recenzja + Utwórz**.

5. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz** , aby utworzyć maszynę wirtualną.

### <a name="review-ip-addresses"></a>Przejrzyj adresy IP

Po utworzeniu maszyny wirtualnej otrzymasz powiadomienie o **pomyślnym wdrożeniu** za pomocą przycisku **Przejdź do zasobu** .  Wybierz pozycję **Przejdź do zasobu** , aby przejść do nowej maszyny wirtualnej.  Z widoku domyślnego dla nowej maszyny wirtualnej wykonaj następujące kroki, aby zidentyfikować skojarzone adresy IP:

1. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.

2. Zwróć uwagę na wartości dla **publicznego adresu IP/etykiety nazwy DNS** i **prywatnego adresu IP** do późniejszego użycia.

   ![Publiczne i prywatne adresy IP](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalowanie i Konfigurowanie powiązania (oprogramowanie DNS)

1. Użyj protokołu SSH, aby nawiązać połączenie z __publicznym adresem IP__ maszyny wirtualnej. Zamień `sshuser` na konto użytkownika ssh określone podczas tworzenia maszyny wirtualnej. Poniższy przykład nawiązuje połączenie z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Aby zainstalować powiązanie, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować powiązanie do przesyłania dalej żądań rozpoznawania nazw do lokalnego serwera DNS, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.options` pliku:

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Zastąp wartości w `goodclients` sekcji zakresem adresów IP sieci wirtualnej i sieci lokalnej. Ta sekcja definiuje adresy, z których ten serwer DNS akceptuje żądania.
    >
    > Zastąp `192.168.0.1` wpis w `forwarders` sekcji adresem IP lokalnego serwera DNS. Ten wpis kieruje żądania DNS do lokalnego serwera DNS w celu rozwiązania problemu.

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
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Ten tekst jest __sufiksem DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest ona używana później.

5. Aby skonfigurować powiązanie do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.local` pliku:

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Należy zastąpić `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufiksem DNS pobranym wcześniej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __kombinacji klawiszy Ctrl + X__, __Y__, a następnie klawisz __Enter__.

6. Aby rozpocząć tworzenie powiązania, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby sprawdzić, czy powiązanie może rozpoznać nazwy zasobów w sieci lokalnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Zamień na w `dns.mynetwork.net` pełni kwalifikowaną nazwę domeny (FQDN) zasobu w sieci lokalnej.
    >
    > Zamień na `10.0.0.4` __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź będzie wyglądać podobnie do następującego tekstu:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do korzystania z niestandardowego serwera DNS

Aby skonfigurować sieć wirtualną do korzystania z niestandardowego serwera DNS zamiast programu rozpoznawania cyklicznego Azure, wykonaj następujące kroki w [Azure Portal](https://portal.azure.com):

1. W menu po lewej stronie przejdź do **wszystkich usług**  >  **sieci**  >  **wirtualne sieci wirtualnych**.

2. Z listy wybierz sieć wirtualną, która spowoduje otwarcie widoku domyślnego dla sieci wirtualnej.  

3. W widoku domyślnym w obszarze **Ustawienia** wybierz pozycję **serwery DNS**.  

4. Wybierz opcję __niestandardowy__, a następnie wprowadź **prywatny adres IP** niestandardowego serwera DNS.

5. Wybierz pozycję __Zapisz__.  <br />  

    ![Ustaw niestandardowy serwer DNS dla sieci](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Skonfiguruj lokalny serwer DNS

W poprzedniej sekcji skonfigurowano niestandardowy serwer DNS do przesyłania żądań do lokalnego serwera DNS. Następnie należy skonfigurować lokalny serwer DNS, aby przekazywać żądania do niestandardowego serwera DNS.

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania serwera DNS, zapoznaj się z dokumentacją oprogramowania serwera DNS. Zapoznaj się z instrukcjami dotyczącymi konfigurowania __usługi przesyłania dalej warunkowego__.

Warunkowe przesyłanie dalej przesyła tylko żądania dla określonego sufiksu DNS. W takim przypadku należy skonfigurować usługę przesyłania dalej dla sufiksu DNS sieci wirtualnej. Żądania dla tego sufiksu powinny być przekazywane do adresu IP niestandardowego serwera DNS. 

Poniżej przedstawiono przykład konfiguracji warunkowego przesyłania dalej dla oprogramowania DNS **bind** :

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Aby uzyskać informacje na temat korzystania z usługi DNS w **systemie Windows Server 2016**, zobacz dokumentację [dodatku DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) ...

Po skonfigurowaniu lokalnego serwera DNS można użyć `nslookup` programu z sieci lokalnej, aby sprawdzić, czy można rozpoznać nazwy w sieci wirtualnej. Poniższy przykład 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Ten przykład używa lokalnego serwera DNS pod adresem 196.168.0.4, aby rozpoznać nazwę niestandardowego serwera DNS. Zastąp adres IP serwerem lokalnym DNS. Zastąp `dnsproxy` adres z w pełni kwalifikowaną nazwą domeny niestandardowego serwera DNS.

## <a name="optional-control-network-traffic"></a>Opcjonalne: sterowanie ruchem sieciowym

Aby kontrolować ruch sieciowy, można użyć sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) lub tras zdefiniowanych przez użytkownika (UDR). Sieciowych grup zabezpieczeń umożliwiają filtrowanie ruchu przychodzącego i wychodzącego i Zezwalanie na ruch lub odmawianie go. UDR umożliwiają sterowanie sposobem przepływu ruchu między zasobami w sieci wirtualnej, Internecie i sieci lokalnej.

> [!WARNING]  
> Usługa HDInsight wymaga dostępu przychodzącego z określonych adresów IP w chmurze Azure i nieograniczonego dostępu wychodzącego. W przypadku kontrolowania ruchu przy użyciu sieciowych grup zabezpieczeń lub UDR należy wykonać następujące czynności:

1. Znajdź adresy IP dla lokalizacji zawierającej daną sieć wirtualną. Aby zapoznać się z listą wymaganych adresów IP według lokalizacji, zobacz [Required addresss](./hdinsight-management-ip-addresses.md).

2. Adresy IP identyfikowane w kroku 1 zezwalają na ruch przychodzący z tych adresów IP.

   * Jeśli używasz __sieciowej grupy zabezpieczeń__: Zezwalaj na ruch __przychodzący__ na porcie __443__ dla adresów IP.
   * Jeśli używasz __UDR__: Ustaw typ __następnego przeskoku__ trasy na __Internet__ dla adresów IP.

Przykład używania Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do tworzenia sieciowych grup zabezpieczeń można znaleźć w dokumencie " [Rozszerzone usługi HDInsight z usługą Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) ".

## <a name="create-the-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight

> [!WARNING]  
> Przed zainstalowaniem usługi HDInsight w sieci wirtualnej należy skonfigurować niestandardowy serwer DNS.

Aby utworzyć klaster usługi HDInsight, wykonaj kroki opisane w temacie [Tworzenie klastra usługi HDInsight przy użyciu Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu.

> [!WARNING]  
> * Podczas tworzenia klastra należy wybrać lokalizację, w której znajduje się Twoja sieć wirtualna.
> * W części __Ustawienia zaawansowane__ konfiguracji należy wybrać utworzoną wcześniej sieć wirtualną i podsieć.

## <a name="connecting-to-hdinsight"></a>Łączenie z usługą HDInsight

W większości dokumentacji usługi HDInsight przyjęto założenie, że masz dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net`. Ten adres używa bramy publicznej, która jest niedostępna, jeśli użyto sieciowych grup zabezpieczeń lub UDR do ograniczenia dostępu z Internetu.

Niektóre dokumenty odwołują `headnodehost` się również do łączenia się z klastrem z sesji SSH. Ten adres jest dostępny tylko z węzłów w klastrze i nie można go używać na klientach podłączonych za pośrednictwem sieci wirtualnej.

Aby bezpośrednio połączyć się z usługą HDInsight za pomocą sieci wirtualnej, wykonaj następujące czynności:

1. Aby odnaleźć wewnętrzne w pełni kwalifikowane nazwy domen węzłów klastra usługi HDInsight, należy użyć jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Aby określić port, na którym usługa jest dostępna, zobacz [porty używane przez Apache Hadoop Services w usłudze HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

    > [!IMPORTANT]  
    > Niektóre usługi hostowane w węzłach głównych są aktywne tylko na jednym węźle naraz. Jeśli spróbujesz uzyskać dostęp do usługi w jednym węźle głównym, a to się nie powiedzie, przełącz się do drugiego węzła głównego.
    >
    > Na przykład, Apache Ambari jest aktywny tylko w jednym węźle głównym jednocześnie. Jeśli spróbujesz uzyskać dostęp do Ambari na jednym węźle głównym i zwróci błąd 404, jest on uruchomiony w drugim węźle głównym.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../virtual-network/network-security-groups-overview.md).

* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).