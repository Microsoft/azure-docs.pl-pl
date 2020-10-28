---
title: Planowanie sieci wirtualnej dla usługi Azure HDInsight
description: Dowiedz się, jak zaplanować wdrożenie Virtual Network platformy Azure, aby połączyć usługę HDInsight z innymi zasobami w chmurze lub zasobami w centrum danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 05/04/2020
ms.openlocfilehash: 60bb16b0a4fd2e710d0d5fddc6cf82916f2f66af
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741424"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planowanie sieci wirtualnej dla usługi Azure HDInsight

Ten artykuł zawiera ogólne informacje dotyczące korzystania z [usługi Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (sieci wirtualnych) w usłudze HDInsight. Omówiono w nim również decyzje dotyczące projektowania i wdrażania, które należy wykonać, aby można było zaimplementować sieć wirtualną dla klastra usługi HDInsight. Po zakończeniu fazy planowania można [utworzyć sieci wirtualne dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md). Aby uzyskać więcej informacji na temat adresów IP zarządzania usługą HDInsight, które są konieczne do poprawnego skonfigurowania sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i tras zdefiniowanych przez użytkownika, zobacz [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).

Korzystanie z Virtual Network platformy Azure umożliwia wykonywanie następujących scenariuszy:

* Łączenie z usługą HDInsight bezpośrednio z sieci lokalnej.
* Łączenie usługi HDInsight z magazynami danych w sieci wirtualnej platformy Azure.
* Bezpośredni dostęp do usług Apache Hadoop, które nie są dostępne publicznie za pośrednictwem Internetu. Na przykład Apache Kafka interfejsy API lub interfejs API platformy Apache HBase Java.

> [!IMPORTANT]
> Utworzenie klastra usługi HDInsight w sieci wirtualnej spowoduje utworzenie kilku zasobów sieciowych, takich jak karty sieciowe i moduły równoważenia obciążenia. **Nie** usuwaj tych zasobów sieciowych, ponieważ są one potrzebne do poprawnego działania klastra z siecią wirtualną.
>
> Po 28 lutego 2019, zasobów sieciowych (takich jak karty sieciowe, funty itp.) dla nowych klastrów usługi HDInsight utworzonych w sieci wirtualnej zostanie zainicjowany w tej samej grupie zasobów klastra usługi HDInsight. Wcześniej te zasoby zostały zainicjowane w grupie zasobów sieci wirtualnej. Nie wprowadzono zmian w aktualnie uruchomionych klastrach i tych klastrach utworzonych bez sieci wirtualnej.

## <a name="planning"></a>Planowanie

Poniżej przedstawiono pytania, na które należy odpowiedzieć podczas planowania instalacji usługi HDInsight w sieci wirtualnej:

* Czy musisz zainstalować HDInsight w istniejącej sieci wirtualnej? Lub tworzysz nową sieć?

    Jeśli używasz istniejącej sieci wirtualnej, przed zainstalowaniem usługi HDInsight może zajść potrzeba zmodyfikowania konfiguracji sieci. Aby uzyskać więcej informacji, zobacz sekcję [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](#existingvnet) .

* Czy chcesz połączyć sieć wirtualną zawierającą usługi HDInsight z inną siecią wirtualną lub siecią lokalną?

    Aby łatwo współpracować z zasobami między sieciami, może być konieczne utworzenie niestandardowego DNS i skonfigurowanie przekazywania DNS. Aby uzyskać więcej informacji, zobacz sekcję [łączenie wielu sieci](#multinet) .

* Czy chcesz ograniczyć/przekierować ruch przychodzący lub wychodzący do usługi HDInsight?

    Usługa HDInsight musi mieć nieograniczoną komunikację z określonymi adresami IP w centrum danych platformy Azure. Istnieje również kilka portów, które muszą być dozwolone przez zapory do komunikacji z klientem. Aby uzyskać więcej informacji, zobacz [sterowanie ruchem sieciowym](./control-network-traffic.md).

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Dodawanie usługi HDInsight do istniejącej sieci wirtualnej

Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, jak dodać nową usługę HDInsight do istniejącej Virtual Network platformy Azure.

> [!NOTE]  
> Nie można dodać istniejącego klastra usługi HDInsight do sieci wirtualnej.

1. Czy korzystasz z klasycznego modelu wdrażania lub Menedżer zasobów dla sieci wirtualnej?

    Usługa HDInsight 3,4 i nowsze wymagają Menedżer zasobów sieci wirtualnej. Wcześniejsze wersje usługi HDInsight wymagały klasycznej sieci wirtualnej.

    Jeśli istniejąca sieć jest klasyczną siecią wirtualną, należy utworzyć Menedżer zasobów sieci wirtualnej, a następnie połączyć te dwa. [Łączenie klasycznej sieci wirtualnych z nowym sieci wirtualnych](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po dołączeniu Usługa HDInsight zainstalowana w sieci Menedżer zasobów może korzystać z zasobów w sieci klasycznej.

2. Czy używasz sieciowych grup zabezpieczeń, tras zdefiniowanych przez użytkownika lub urządzeń Virtual Network do ograniczania ruchu do sieci wirtualnej czy z niej?

    Usługa HDInsight wymaga nieograniczonego dostępu do kilku adresów IP w centrum danych platformy Azure. Aby umożliwić komunikację z tymi adresami IP, należy zaktualizować wszystkie istniejące sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika.

    Usługa HDInsight obsługuje wiele usług, które korzystają z różnych portów. Nie blokuj ruchu do tych portów. Aby uzyskać listę portów dozwolonych przez zapory urządzeń wirtualnych, zobacz sekcję zabezpieczenia.

    Aby znaleźć istniejącą konfigurację zabezpieczeń, użyj następujących Azure PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure:

    * Grupy zabezpieczeń sieci

        Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz dokument [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) .

        > [!IMPORTANT]  
        > Reguły sieciowej grupy zabezpieczeń są stosowane w kolejności na podstawie priorytetu reguły. Stosowana jest pierwsza reguła zgodna ze wzorcem ruchu, a żadne inne nie są stosowane do tego ruchu. Kolejność reguł od najmniejszej do najmniej ograniczającej. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu grup zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md) .

    * Trasy zdefiniowane przez użytkownika

        Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozwiązywania problemów z trasami](../virtual-network/diagnose-network-routing-problem.md) .

3. Utwórz klaster usługi HDInsight i wybierz Virtual Network platformy Azure podczas konfiguracji. Aby zrozumieć proces tworzenia klastra, wykonaj kroki opisane w następujących dokumentach:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Portal)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)
    * [Tworzenie usługi HDInsight przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Tworzenie usługi HDInsight przy użyciu szablonu Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Dodawanie usługi HDInsight do sieci wirtualnej to opcjonalny krok konfiguracji. Pamiętaj, aby podczas konfigurowania klastra wybrać sieć wirtualną.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Łączenie wielu sieci

Największym wyzwaniem z konfiguracją wielosieciową jest rozpoznawanie nazw między sieciami.

Platforma Azure udostępnia rozpoznawanie nazw dla usług platformy Azure, które są zainstalowane w sieci wirtualnej. To wbudowane rozwiązanie do rozpoznawania nazw umożliwia usłudze HDInsight łączenie się z następującymi zasobami przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN):

* Wszystkie zasoby, które są dostępne w Internecie. Na przykład microsoft.com, windowsupdate.com.

* Wszystkie zasoby znajdujące się w tym samym Virtual Network platformy Azure przy użyciu __wewnętrznej nazwy DNS__ zasobu. Na przykład podczas korzystania z domyślnego rozpoznawania nazw, poniżej przedstawiono przykłady wewnętrznych nazw DNS przypisanych do węzłów procesu roboczego usługi HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba węzły mogą komunikować się bezpośrednio ze sobą oraz z innymi węzłami w usłudze HDInsight przy użyciu wewnętrznych nazw DNS.

Domyślne rozpoznawanie nazw __nie zezwala usłudze HDInsight na rozpoznawanie__ nazw zasobów w sieciach, które są przyłączone do sieci wirtualnej. Na przykład często można przyłączyć sieć lokalną do sieci wirtualnej. Tylko w przypadku domyślnego rozpoznawania nazw Usługa HDInsight nie może uzyskać dostępu do zasobów w sieci lokalnej według nazwy. Przeciwieństwem jest również wartość true, zasoby w sieci lokalnej nie mogą uzyskać dostępu do zasobów w sieci wirtualnej według nazwy.

> [!WARNING]  
> Należy utworzyć niestandardowy serwer DNS i skonfigurować sieć wirtualną tak, aby korzystała z niej przed utworzeniem klastra usługi HDInsight.

Aby włączyć rozpoznawanie nazw między siecią wirtualną i zasobami w połączonych sieciach, należy wykonać następujące czynności:

1. Utwórz niestandardowy serwer DNS w Virtual Network platformy Azure, w którym planujesz zainstalować usługę HDInsight.

2. Skonfiguruj sieć wirtualną tak, aby korzystała z niestandardowego serwera DNS.

3. Znajdź sufiks DNS przypisany przez platformę Azure dla sieci wirtualnej. Ta wartość jest podobna do `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . Informacje dotyczące znajdowania sufiksu DNS znajdują się w sekcji [przykład: Custom DNS](hdinsight-create-virtual-network.md#example-dns) .

4. Skonfiguruj przekazywanie między serwerami DNS. Konfiguracja zależy od typu sieci zdalnej.

   * Jeśli sieć zdalna jest siecią lokalną, skonfiguruj system DNS w następujący sposób:

     * __Niestandardowy serwer DNS__ (w sieci wirtualnej):

         * Przekazuj żądania dotyczące sufiksu DNS sieci wirtualnej do programu rozpoznawania cyklicznego Azure (168.63.129.16). Platforma Azure obsługuje żądania dotyczące zasobów w sieci wirtualnej

         * Przekazuj wszystkie inne żądania do lokalnego serwera DNS. Lokalna usługa DNS obsługuje wszystkie inne żądania rozpoznawania nazw, nawet żądania dotyczące zasobów internetowych, takich jak Microsoft.com.

     * __Lokalna usługa DNS__ : przekazuj żądania dla sufiksu DNS sieci wirtualnej do NIESTANDARDOWEGO serwera DNS. Niestandardowy serwer DNS przekazuje następnie do programu rozpoznawania cyklicznego Azure.

       Ta konfiguracja kieruje żądania dla w pełni kwalifikowanych nazw domen, które zawierają sufiks DNS sieci wirtualnej do niestandardowego serwera DNS. Wszystkie inne żądania (nawet dla publicznych adresów internetowych) są obsługiwane przez lokalny serwer DNS.

   * Jeśli sieć zdalna jest kolejną Virtual Network platformy Azure, skonfiguruj system DNS w następujący sposób:

     * __Niestandardowy serwer DNS__ (w każdej sieci wirtualnej):

         * Żądania dotyczące sufiksu DNS sieci wirtualnych są przekazywane do niestandardowych serwerów DNS. System DNS w każdej sieci wirtualnej jest odpowiedzialny za rozwiązywanie zasobów w sieci.

         * Przekazuj wszystkie inne żądania do programu rozpoznawania cyklicznego Azure. Program rozpoznawania cyklicznego jest odpowiedzialny za rozwiązywanie zasobów lokalnych i internetowych.

       Serwer DNS dla każdej sieci przekazuje żądania do drugiego, na podstawie sufiksu DNS. Inne żądania są rozwiązywane przy użyciu programu rozpoznawania cyklicznego Azure.

     Przykład każdej konfiguracji można znaleźć w sekcji [przykład: Custom DNS](hdinsight-create-virtual-network.md#example-dns) .

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Połącz się bezpośrednio z usługami Apache Hadoop Services

Możesz połączyć się z klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net` . Ten adres używa publicznego adresu IP, który może nie być osiągalny, jeśli używasz sieciowych grup zabezpieczeń, aby ograniczyć ruch przychodzący z Internetu. Ponadto podczas wdrażania klastra w sieci wirtualnej możesz uzyskać do niego dostęp przy użyciu prywatnego punktu końcowego `https://CLUSTERNAME-int.azurehdinsight.net` . Ten punkt końcowy jest rozpoznawany jako prywatny adres IP w sieci wirtualnej na potrzeby dostępu do klastra.

Aby nawiązać połączenie z usługą Apache Ambari i innymi stronami sieci Web za pomocą sieci wirtualnej, wykonaj następujące czynności:

1. Aby odnaleźć wewnętrzne w pełni kwalifikowane nazwy domen (FQDN) węzłów klastra usługi HDInsight, użyj jednej z następujących metod:

    Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na liście zwracanych węzłów Znajdź nazwę FQDN węzłów głównych i Użyj nazw FQDN, aby nawiązać połączenie z usługą Ambari i innymi usługami sieci Web. Na przykład, użyj, `http://<headnode-fqdn>:8080` Aby uzyskać dostęp do Ambari.

    > [!IMPORTANT]  
    > Niektóre usługi hostowane w węzłach głównych są aktywne tylko na jednym węźle naraz. Jeśli spróbujesz uzyskać dostęp do usługi w jednym węźle głównym i zwróci błąd 404, przełącz się do drugiego węzła głównego.

2. Aby określić węzeł i port, na którym usługa jest dostępna, zobacz [porty używane przez usługi Hadoop w dokumencie HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

## <a name="load-balancing"></a>Równoważenie obciążenia

Podczas tworzenia klastra usługi HDInsight jest również tworzony moduł równoważenia obciążenia. Typ tego modułu równoważenia obciążenia jest na [poziomie podstawowej jednostki SKU](../load-balancer/skus.md), który ma pewne ograniczenia. Jedno z tych ograniczeń polega na tym, że w przypadku dwóch sieci wirtualnych w różnych regionach nie można nawiązać połączenia z podstawowymi usługami równoważenia obciążenia. Zobacz [często zadawane pytania dotyczące usługi Virtual Networks: ograniczenia dotyczące globalnej komunikacji równorzędnej sieci](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)wirtualnej, aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładami kodu i przykłady tworzenia sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* Aby zapoznać się z kompleksowym przykładem konfigurowania usługi HDInsight w celu nawiązania połączenia z siecią lokalną, zobacz [łączenie usługi HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../virtual-network/network-security-groups-overview.md).
* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).
* Aby uzyskać więcej informacji na temat kontrolowania ruchu, zobacz [Kontrola ruchu sieciowego](./control-network-traffic.md).
