---
title: Planowanie sieci wirtualnej dla usługi Azure HDInsight
description: Dowiedz się, jak zaplanować wdrożenie usługi Azure Virtual Network w celu połączenia usługi HDInsight z innymi zasobami w chmurze lub zasobami w centrum danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/21/2020
ms.openlocfilehash: 18774ae4a98b795846459251174ee47671aef39c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769899"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planowanie sieci wirtualnej dla usługi Azure HDInsight

Ten artykuł zawiera podstawowe informacje na temat korzystania z [sieci wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md) (sieci wirtualnych) z usługi Azure HDInsight. Omówiono również decyzje dotyczące projektowania i implementacji, które należy podeszć przed zaimplementowanie sieci wirtualnej dla klastra HDInsight. Po zakończeniu fazy planowania można przejść do [tworzenia sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md). Aby uzyskać więcej informacji na temat adresów IP zarządzania hdinsight, które są potrzebne do prawidłowego konfigurowania sieciowych grup zabezpieczeń (NSG) i tras zdefiniowanych przez użytkownika, zobacz [Adresy IP zarządzania hdinsight](hdinsight-management-ip-addresses.md).

Za pomocą sieci wirtualnej platformy Azure umożliwia następujące scenariusze:

* Łączenie się z hdinsight bezpośrednio z sieci lokalnej.
* Łączenie usługi HDInsight z magazynami danych w sieci wirtualnej platformy Azure.
* Bezpośredni dostęp do usług Apache Hadoop, które nie są dostępne publicznie przez Internet. Na przykład apache interfejsów API platformy Kafka lub Apache HBase Java API.

> [!IMPORTANT]
> Utworzenie klastra HDInsight w sieci wirtualnej spowoduje utworzenie kilku zasobów sieciowych, takich jak karty sieciowe i moduły równoważenia obciążenia. **Nie** należy usuwać tych zasobów sieciowych, ponieważ są one potrzebne do prawidłowego działania klastra za pomocą sieci wirtualnej.
>
> Po 28 lutego 2019 r. zasoby sieciowe (takie jak karty sieciowe, lbs itp.) dla nowych klastrów HDInsight utworzone w sieci wirtualnej zostaną udostępnione w tej samej grupie zasobów klastra HDInsight. Wcześniej te zasoby były aprowiowane w grupie zasobów sieci wirtualnej. Nie ma żadnych zmian w bieżących uruchomionych klastrów i klastrów utworzonych bez sieci wirtualnej.

## <a name="planning"></a>Planowanie

Poniżej przedstawiono pytania, na które należy odpowiedzieć podczas planowania instalacji usługi HDInsight w sieci wirtualnej:

* Czy musisz zainstalować program HDInsight w istniejącej sieci wirtualnej? A może tworzysz nową sieć?

    Jeśli używasz istniejącej sieci wirtualnej, może być konieczne zmodyfikowanie konfiguracji sieci przed zainstalowaniem usługi HDInsight. Aby uzyskać więcej informacji, zobacz [dodawanie funkcji HDInsight do istniejącej sekcji sieci wirtualnej.](#existingvnet)

* Czy chcesz połączyć sieć wirtualną zawierającą program HDInsight z inną siecią wirtualną lub siecią lokalną?

    Aby łatwo pracować z zasobami w sieciach, może być konieczne utworzenie niestandardowego systemu DNS i skonfigurowanie przekazywania dalej DNS. Aby uzyskać więcej informacji, zobacz sekcję [łączenie wielu sieci.](#multinet)

* Czy chcesz ograniczyć/przekierować ruch przychodzący lub wychodzący do funkcji HDInsight?

    Usługa HDInsight musi mieć nieograniczoną komunikację z określonymi adresami IP w centrum danych platformy Azure. Istnieje również kilka portów, które muszą być dozwolone za pośrednictwem zapór do komunikacji z klientem. Aby uzyskać więcej informacji, zobacz sekcję [kontrolowanie ruchu sieciowego.](#networktraffic)

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Dodawanie funkcji HDInsight do istniejącej sieci wirtualnej

Skorzystaj z kroków w tej sekcji, aby dowiedzieć się, jak dodać nowy dostęp HDInsight do istniejącej sieci wirtualnej platformy Azure.

> [!NOTE]  
> Nie można dodać istniejącego klastra HDInsight do sieci wirtualnej.

1. Czy używasz klasycznego lub modelu wdrażania Menedżera zasobów dla sieci wirtualnej?

    Usługa HDInsight 3.4 i nowsze wymaga sieci wirtualnej Menedżera zasobów. Wcześniejsze wersje programu HDInsight wymagały klasycznej sieci wirtualnej.

    Jeśli istniejąca sieć jest klasyczną siecią wirtualną, należy utworzyć sieć wirtualną Menedżera zasobów, a następnie połączyć dwie z tych sieci. [Łączenie klasycznych sieci wirtualnych z nowymi sieciami wirtualnymi](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po dołączeniu usługa HDInsight zainstalowana w sieci Menedżera zasobów może wchodzić w interakcje z zasobami w sieci klasycznej.

2. Czy do ograniczania ruchu do sieci wirtualnej lub z sieci wirtualnej używane są sieciowe grupy zabezpieczeń, trasy zdefiniowane przez użytkownika lub urządzenia sieci wirtualnej?

    Jako usługa zarządzana usługa HDInsight wymaga nieograniczonego dostępu do kilku adresów IP w centrum danych platformy Azure. Aby zezwolić na komunikację z tymi adresami IP, zaktualizuj istniejące sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika.

    HDInsight obsługuje wiele usług, które używają różnych portów. Nie blokuj ruchu do tych portów. Aby uzyskać listę portów, które mają być zezwalane za pośrednictwem zapór urządzeń wirtualnych, zobacz sekcję Zabezpieczenia.

    Aby znaleźć istniejącą konfigurację zabezpieczeń, użyj następujących poleceń interfejsu wiersza polecenia azure powershell lub interfejsu wiersza polecenia platformy Azure:

    * Grupy zabezpieczeń sieci

        Zamień `RESOURCEGROUP` nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz dokument [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń.](../virtual-network/diagnose-network-traffic-filter-problem.md)

        > [!IMPORTANT]  
        > Reguły sieciowej grupy zabezpieczeń są stosowane w kolejności na podstawie priorytetu reguły. Pierwsza reguła, która pasuje do wzorca ruchu jest stosowana, a żadne inne nie są stosowane dla tego ruchu. Reguły zamówień od najbardziej dopuszczalnych do najmniej permisywnych. Aby uzyskać więcej informacji, zobacz dokument [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń.](../virtual-network/security-overview.md)

    * Trasy definiowane przez użytkownika

        Zamień `RESOURCEGROUP` nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz dokument [Rozwiązywanie problemów z trasami.](../virtual-network/diagnose-network-routing-problem.md)

3. Utwórz klaster HDInsight i wybierz sieć wirtualną platformy Azure podczas konfiguracji. Aby zrozumieć proces tworzenia klastra, należy wykonać kroki opisane w następujących dokumentach:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Portal)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)
    * [Tworzenie usługi HDInsight przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Tworzenie usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Dodawanie funkcji HDInsight do sieci wirtualnej jest opcjonalnym krokiem konfiguracji. Podczas konfigurowania klastra należy wybrać sieć wirtualną.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Łączenie wielu sieci

Największym wyzwaniem w konfiguracji wielosieciowej jest rozpoznawanie nazw między sieciami.

Platforma Azure zapewnia rozpoznawanie nazw dla usług platformy Azure, które są instalowane w sieci wirtualnej. Ta wbudowana rozdzielczość nazw umożliwia funkcji HDInsight łączenie się z następującymi zasobami przy użyciu w pełni kwalifikowanej nazwy domeny (FQDN):

* Każdy zasób, który jest dostępny w Internecie. Na przykład microsoft.com, windowsupdate.com.

* Każdy zasób, który znajduje się w tej samej sieci wirtualnej platformy Azure, przy użyciu __wewnętrznej nazwy DNS__ zasobu. Na przykład w przypadku używania domyślnego rozpoznawania nazw są to przykłady wewnętrznych nazw DNS przypisanych do węzłów procesu roboczego USŁUGI HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba te węzły mogą komunikować się bezpośrednio ze sobą, a inne węzły w hdinsight, przy użyciu wewnętrznych nazw DNS.

Domyślna rozdzielczość nazw __nie__ zezwala funkcji HDInsight na rozpoznawanie nazw zasobów w sieciach, które są przyłączone do sieci wirtualnej. Na przykład często dołączaj do sieci lokalnej do sieci wirtualnej. W domyślnej rozdzielczości nazw usługa HDInsight nie może uzyskać dostępu do zasobów w sieci lokalnej według nazwy. Jest odwrotnie, zasoby w sieci lokalnej nie mogą uzyskać dostępu do zasobów w sieci wirtualnej według nazwy.

> [!WARNING]  
> Przed utworzeniem klastra HDInsight należy utworzyć niestandardowy serwer DNS i skonfigurować sieć wirtualną tak, aby z niego korzystała.

Aby włączyć rozpoznawanie nazw między siecią wirtualną a zasobami w połączonych sieciach, należy wykonać następujące akcje:

1. Utwórz niestandardowy serwer DNS w sieci wirtualnej platformy Azure, w której planujesz zainstalować usługę HDInsight.

2. Skonfiguruj sieć wirtualną do używania niestandardowego serwera DNS.

3. Znajdź sufiks DNS przypisany na platformę Azure dla sieci wirtualnej. Ta wartość jest `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`podobna do . Aby uzyskać informacje na temat znajdowania sufiksu DNS, zobacz [przykład: Niestandardowy DNS](hdinsight-create-virtual-network.md#example-dns) sekcji.

4. Konfigurowanie przekazywania dalej między serwerami DNS. Konfiguracja zależy od typu sieci zdalnej.

   * Jeśli sieć zdalna jest siecią lokalną, skonfiguruj system DNS w następujący sposób:

     * __Niestandardowy dns__ (w sieci wirtualnej):

         * Przesyłanie dalej żądań sufiksu DNS sieci wirtualnej do cyklicznego programu rozpoznawania nazw platformy Azure (168.63.129.16). Platforma Azure obsługuje żądania zasobów w sieci wirtualnej

         * Przesyłanie dalej wszystkich innych żądań do lokalnego serwera DNS. Lokalny system DNS obsługuje wszystkie inne żądania rozpoznawania nazw, a nawet żądania dotyczące zasobów internetowych, takich jak Microsoft.com.

     * __Lokalny system DNS:__ Przesyłanie dalej żądań sufiksu DNS w sieci wirtualnej do niestandardowego serwera DNS. Niestandardowy serwer DNS następnie przekazuje do cyklicznego programu rozpoznawania nazw platformy Azure.

       Ta konfiguracja kieruje żądania w pełni kwalifikowanych nazw domen zawierających sufiks DNS sieci wirtualnej do niestandardowego serwera DNS. Wszystkie inne żądania (nawet w przypadku publicznych adresów internetowych) są obsługiwane przez lokalny serwer DNS.

   * Jeśli sieć zdalna jest inną siecią wirtualną platformy Azure, skonfiguruj system DNS w następujący sposób:

     * __Niestandardowy dns__ (w każdej sieci wirtualnej):

         * Żądania sufiksu DNS sieci wirtualnych są przekazywane do niestandardowych serwerów DNS. System DNS w każdej sieci wirtualnej jest odpowiedzialny za rozwiązywanie zasobów w swojej sieci.

         * Przesyłaj dalej wszystkie inne żądania do cyklicznego programu rozpoznawania nazw platformy Azure. Cykliczny program rozpoznawania nazw jest odpowiedzialny za rozwiązywanie lokalnych i internetowych zasobów.

       Serwer DNS dla każdej sieci przekazuje żądania do drugiej strony na podstawie sufiksu DNS. Inne żądania są rozpoznawane przy użyciu cyklicznego programu rozpoznawania nazw platformy Azure.

     Na przykład każdej konfiguracji zobacz [przykład: Niestandardowy DNS](hdinsight-create-virtual-network.md#example-dns) sekcji.

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

## <a name="directly-connect-to-apache-hadoop-services"></a>Bezpośrednie połączenie z usługami Apache Hadoop

Można połączyć się `https://CLUSTERNAME.azurehdinsight.net`z klastrem pod adresem . Ten adres korzysta z publicznego adresu IP, który może być niedoścignione, jeśli używano sieciowych grup zabezpieczeń w celu ograniczenia ruchu przychodzącego z Internetu. Ponadto podczas wdrażania klastra w sieci wirtualnej można uzyskać do `https://CLUSTERNAME-int.azurehdinsight.net`niego dostęp przy użyciu prywatnego punktu końcowego . Ten punkt końcowy jest rozpoznawany na prywatny adres IP wewnątrz sieci wirtualnej dla dostępu do klastra.

Aby połączyć się z apache ambari i innymi stronami internetowymi za pośrednictwem sieci wirtualnej, należy wykonać następujące czynności:

1. Aby odnajdować wewnętrzne, w pełni kwalifikowane nazwy domen (FQDN) węzłów klastra HDInsight, należy użyć jednej z następujących metod:

    Zamień `RESOURCEGROUP` nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

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

    Na liście zwróconych węzłów znajdź numer FQDN dla węzłów głównego i użyj sieci FQDN do nawiązania połączenia z ambari i innymi usługami sieci web. Na przykład `http://<headnode-fqdn>:8080` użyj dostępu do Ambari.

    > [!IMPORTANT]  
    > Niektóre usługi hostowane w węzłach głównego są aktywne tylko w jednym węźle naraz. Jeśli spróbujesz uzyskać dostęp do usługi w jednym węźle głównym i zwraca błąd 404, przełącz się do drugiego węzła głównego.

2. Aby określić węzeł i port, na których usługa jest dostępna, zobacz [Porty używane przez usługi Hadoop w dokumencie HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>Kontrolowanie ruchu sieciowego

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniki kontrolowania ruchu przychodzącego i wychodzącego do klastrów HDInsight

Ruch sieciowy w sieciach wirtualnych platformy Azure można kontrolować za pomocą następujących metod:

* **Sieciowe grupy zabezpieczeń** umożliwiają filtrowanie ruchu przychodzącego i wychodzącego do sieci. Aby uzyskać więcej informacji, zobacz dokument [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń.](../virtual-network/security-overview.md)

* **Sieciowe urządzenia wirtualne** (NVA) mogą być używane tylko z ruchem wychodzącym. Urządzenia NVA replikują funkcje urządzeń, takich jak zapory i routery. Aby uzyskać więcej informacji, zobacz dokument [Urządzenia sieciowe.](https://azure.microsoft.com/solutions/network-appliances)

Jako usługa zarządzana usługa HDInsight wymaga nieograniczonego dostępu do usług kondycji i zarządzania HDInsight zarówno dla ruchu przychodzącego, jak i wychodzącego z sieci wirtualnej. Podczas korzystania z sieciowych grup zabezpieczeń należy upewnić się, że te usługi nadal mogą komunikować się z klastrem HDInsight.

![Diagram jednostek usługi HDInsight utworzonych w niestandardowej sieci wirtualnej platformy Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>Usługa HDInsight z sieciowymi grupami zabezpieczeń

Jeśli planujesz używać **sieciowych grup zabezpieczeń** do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

1. Zidentyfikuj region platformy Azure, który ma być używany dla usługi HDInsight.

2. Zidentyfikuj tagi usługi wymagane przez usługę HDInsight dla twojego regionu. Aby uzyskać więcej informacji, zobacz [Tagi usługi sieciowej grupy zabezpieczeń (NSG) dla usługi Azure HDInsight](hdinsight-service-tags.md).

3. Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń podsieci, w której zamierzasz zainstalować usługę HDInsight.

    * __Sieciowe grupy zabezpieczeń:__ zezwalaj na ruch __przychodzący__ na porcie __443__ z adresów IP. Zapewni to, że usługi zarządzania HDInsight mogą dotrzeć do klastra spoza sieci wirtualnej.

Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [omówienie grup zabezpieczeń sieci .](../virtual-network/security-overview.md)

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Kontrolowanie ruchu wychodzącego z klastrów HDInsight

Aby uzyskać więcej informacji na temat kontrolowania ruchu wychodzącego z klastrów usługi HDInsight, zobacz [Konfigurowanie ograniczenia ruchu wychodzącego dla klastrów usługi Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Wymuszone tunelowanie do lokalnego

Tunelowanie wymuszone to zdefiniowana przez użytkownika konfiguracja routingu, w której cały ruch z podsieci jest zmuszony do określonej sieci lub lokalizacji, takiej jak sieć lokalna. Usługa HDInsight __nie__ obsługuje wymuszonego tunelowania ruchu do sieci lokalnych.

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>Wymagane adresy IP

Jeśli do kontrolowania ruchu używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika, zobacz [Adresy IP zarządzania programem HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a><a id="hdinsight-ports"></a>Wymagane porty

Jeśli planujesz przy użyciu **zapory** i uzyskać dostęp do klastra z zewnątrz na niektórych portach, może być konieczne zezwolenie na ruch na tych portach potrzebnych w danym scenariuszu. Domyślnie nie jest potrzebne specjalne białe listy portów, o ile ruch zarządzania platformy Azure wyjaśniony w poprzedniej sekcji może dotrzeć do klastra na porcie 443.

Aby uzyskać listę portów dla określonych usług, zobacz [Porty używane przez usługi Apache Hadoop w dokumencie HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

Aby uzyskać więcej informacji na temat reguł zapory dla urządzeń wirtualnych, zobacz dokument [scenariusza urządzenia wirtualnego.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="load-balancing"></a>Równoważenie obciążenia

Podczas tworzenia klastra HDInsight tworzony jest również moduł równoważenia obciążenia. Typ tego modułu równoważenia obciążenia jest na [podstawowym poziomie jednostki SKU,](../load-balancer/concepts-limitations.md#skus)który ma pewne ograniczenia. Jednym z tych ograniczeń jest to, że jeśli masz dwie sieci wirtualne w różnych regionach, nie można połączyć się z podstawowymi modułami równoważenia obciążenia. Zobacz [sieci wirtualne często zadawane pytania: ograniczenia dotyczące komunikacji równorzędnej sieci wirtualnej na sieci globalnej](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładami kodu i przykładami tworzenia sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* Aby uzyskać kompleksowy przykład konfigurowania usługi HDInsight do łączenia się z siecią lokalną, zobacz [Łączenie usługi HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika i przekierowanie IP](../virtual-network/virtual-networks-udr-overview.md).
