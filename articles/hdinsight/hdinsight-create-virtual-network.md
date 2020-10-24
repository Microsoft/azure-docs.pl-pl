---
title: Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak utworzyć Virtual Network platformy Azure w celu połączenia usługi HDInsight z innymi zasobami w chmurze lub zasobami w centrum danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 886e8cb1c5d86c1a61e65fa8cbd54c022f6c1c6b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484818"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight

W tym artykule przedstawiono przykłady i przykłady kodu służące do tworzenia i konfigurowania [sieci wirtualnych platformy Azure](../virtual-network/virtual-networks-overview.md). Do użycia z klastrami usługi Azure HDInsight. Przedstawiono szczegółowe przykłady tworzenia sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i konfigurowania usługi DNS.

Aby uzyskać ogólne informacje dotyczące korzystania z sieci wirtualnych za pomocą usługi Azure HDInsight, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Wymagania wstępne dotyczące przykładów i przykładów kodu

Przed wykonaniem dowolnego z przykładów kodu w tym artykule należy zrozumieć sieci TCP/IP. Jeśli nie znasz sieci TCP/IP, zapoznaj się z kimś przed wprowadzeniem modyfikacji do sieci produkcyjnych.

Inne wymagania wstępne dotyczące przykładów w tym artykule obejmują następujące elementy:

* Jeśli używasz programu PowerShell, musisz zainstalować [AZ module](https://docs.microsoft.com/powershell/azure/).
* Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowano, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Jeśli szukasz wskazówek krok po kroku dotyczących łączenia usługi HDInsight z siecią lokalną za pomocą Virtual Network platformy Azure, zobacz dokument [Connect HDInsight do lokalnego dokumentu sieci](connect-on-premises-network.md) .

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Przykład: sieciowe grupy zabezpieczeń z usługą HDInsight

W przykładach w tej sekcji pokazano, jak utworzyć reguły sieciowej grupy zabezpieczeń. Reguły umożliwiają usłudze HDInsight komunikowanie się z usługami zarządzania platformy Azure. Przed użyciem przykładów należy dostosować adresy IP tak, aby były zgodne z tymi, które są używane przez używany region platformy Azure. Te informacje można znaleźć w temacie [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Szablon zarządzania zasobami platformy Azure

Poniższy szablon zarządzania zasobami tworzy sieć wirtualną, która ogranicza ruch przychodzący, ale zezwala na ruch z adresów IP wymaganych przez usługi HDInsight. Ten szablon tworzy również klaster usługi HDInsight w sieci wirtualnej.

* [Wdrażanie zabezpieczonego Virtual Network platformy Azure i klastra usługi HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt programu PowerShell umożliwia utworzenie sieci wirtualnej, która ogranicza ruch przychodzący i zezwala na ruch z adresów IP w regionie Europa Północna.

> [!IMPORTANT]  
> Zmień adresy IP dla `hdirule1` i `hdirule2` w tym przykładzie w celu dopasowania do regionu platformy Azure, z którego korzystasz. Te informacje można znaleźć w obszarze [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

W tym przykładzie pokazano, jak dodać reguły zezwalające na ruch przychodzący na wymaganych adresach IP. Nie zawiera ona reguły ograniczania dostępu przychodzącego z innych źródeł. Poniższy kod ilustruje sposób włączania dostępu SSH z Internetu:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wykonaj następujące kroki, aby utworzyć sieć wirtualną ograniczającą ruch przychodzący, ale zezwala na ruch z adresów IP wymaganych przez usługi HDInsight.

1. Użyj poniższego polecenia, aby utworzyć nową sieciową grupę zabezpieczeń o nazwie `hdisecure` . Zamień na `RESOURCEGROUP` grupę zasobów zawierającą Virtual Network platformy Azure. Zamień na `LOCATION` lokalizację (region), w którym została utworzona grupa.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Po utworzeniu grupy otrzymujesz informacje o nowej grupie.

2. Aby dodać reguły do nowej sieciowej grupy zabezpieczeń, która zezwala na komunikację przychodzącą na porcie 443 z usługi Azure HDInsight Health and Management. Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej Virtual Network platformy Azure.

    > [!IMPORTANT]  
    > Zmień adresy IP dla `hdirule1` i `hdirule2` w tym przykładzie w celu dopasowania do regionu platformy Azure, z którego korzystasz. Te informacje można znaleźć w temacie [adresy IP zarządzania usługą HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Aby pobrać unikatowy identyfikator dla tej sieciowej grupy zabezpieczeń, użyj następującego polecenia:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Użyj poniższego polecenia, aby zastosować sieciową grupę zabezpieczeń do podsieci. Zastąp `GUID` `RESOURCEGROUP` wartości i wartościami zwracanymi z poprzedniego kroku. Zamień `VNETNAME` `SUBNETNAME` nazwę sieci wirtualnej i nazwę podsieci, które chcesz utworzyć.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po zakończeniu wykonywania tego polecenia można zainstalować usługi HDInsight w Virtual Network.

Te kroki otwierają tylko dostęp do usługi HDInsight Health and Management w chmurze platformy Azure. Każdy inny dostęp do klastra usługi HDInsight spoza Virtual Network jest blokowany. Aby włączyć dostęp spoza sieci wirtualnej, należy dodać dodatkowe reguły sieciowej grupy zabezpieczeń.

Poniższy kod ilustruje sposób włączania dostępu SSH z Internetu:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Przykład: Konfiguracja DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Rozpoznawanie nazw między siecią wirtualną i połączoną siecią lokalną

Ten przykład wykonuje następujące założenia:

* Masz Virtual Network platformy Azure, która jest połączona z siecią lokalną przy użyciu bramy sieci VPN.

* Niestandardowy serwer DNS w sieci wirtualnej jest uruchomiony jako system operacyjny Linux lub UNIX.

* [Powiązanie](https://www.isc.org/downloads/bind/) jest zainstalowane na niestandardowym serwerze DNS.

Na niestandardowym serwerze DNS w sieci wirtualnej:

1. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby znaleźć sufiks DNS sieci wirtualnej:

    Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Na niestandardowym serwerze DNS dla sieci wirtualnej Użyj następującego tekstu jako zawartości `/etc/bind/named.conf.local` pliku:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartość sufiksem DNS sieci wirtualnej.

    Ta konfiguracja kieruje wszystkie żądania DNS dotyczące sufiksu DNS sieci wirtualnej do programu rozpoznawania cyklicznego Azure.

1. Na niestandardowym serwerze DNS dla sieci wirtualnej Użyj następującego tekstu jako zawartości `/etc/bind/named.conf.options` pliku:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Zastąp `10.0.0.0/16` wartość zakresem adresów IP sieci wirtualnej. Ten wpis umożliwia rozpoznawanie nazw żądań adresów w tym zakresie.

    * Dodaj zakres adresów IP sieci lokalnej do `acl goodclients { ... }` sekcji.  wpis zezwala na żądania rozpoznawania nazw z zasobów w sieci lokalnej.
    
    * Zastąp wartość `192.168.0.1` adresem IP lokalnego serwera DNS. Ten wpis kieruje wszystkie pozostałe żądania DNS do lokalnego serwera DNS.

1. Aby użyć konfiguracji, uruchom ponownie powiązanie. Na przykład `sudo service bind9 restart`.

1. Dodaj usługę przesyłania dalej warunkową do lokalnego serwera DNS. Skonfiguruj usługę przesyłania dalej warunkowego do wysyłania żądań dla sufiksu DNS z kroku 1 do niestandardowego serwera DNS.

    > [!NOTE]  
    > Zapoznaj się z dokumentacją oprogramowania DNS, aby uzyskać szczegółowe informacje na temat dodawania warunkowego przesyłania dalej.

Po wykonaniu tych kroków można nawiązać połączenie z zasobami w dowolnej sieci przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować usługi HDInsight w sieci wirtualnej.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Rozpoznawanie nazw między dwiema połączonymi sieciami wirtualnymi

Ten przykład wykonuje następujące założenia:

* Istnieją dwie sieci wirtualne platformy Azure, które są połączone przy użyciu bramy sieci VPN lub komunikacji równorzędnej.

* Niestandardowy serwer DNS w obu sieciach korzysta z systemu Linux lub UNIX jako system operacyjny.

* Na niestandardowych serwerach DNS jest zainstalowane [powiązanie](https://www.isc.org/downloads/bind/) .

1. Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby znaleźć sufiks DNS obu sieci wirtualnych:

    Zamień `RESOURCEGROUP` na nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Użyj następującego tekstu jako zawartości `/etc/bind/named.config.local` pliku na niestandardowym serwerze DNS. Wprowadź tę zmianę na niestandardowym serwerze DNS w obu sieciach wirtualnych.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartość sufiksem DNS __innej__ sieci wirtualnej. Ten wpis kieruje żądania dotyczące sufiksu DNS sieci zdalnej do niestandardowego serwera DNS w tej sieci.

3. Na niestandardowych serwerach DNS w obu sieciach wirtualnych Użyj następującego tekstu jako zawartości `/etc/bind/named.conf.options` pliku:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Zastąp `10.0.0.0/16` `10.1.0.0/16` wartości i wartościami z zakresu adresów IP sieci wirtualnych. Ten wpis umożliwia zasobom w każdej sieci wykonywanie żądań serwerów DNS.

    Wszystkie żądania, które nie są sufiksami DNS sieci wirtualnych (na przykład microsoft.com) są obsługiwane przez program rozpoznawania cyklicznego Azure.

4. Aby użyć konfiguracji, uruchom ponownie powiązanie. Na przykład `sudo service bind9 restart` na obu serwerach DNS.

Po wykonaniu tych kroków można nawiązać połączenie z zasobami w sieci wirtualnej przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować usługi HDInsight w sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełny przykład konfigurowania usługi HDInsight do łączenia się z siecią lokalną, zobacz Łączenie usługi [HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby skonfigurować klastry Apache HBase w sieciach wirtualnych platformy Azure, zobacz [Tworzenie klastrów Apache HBase w usłudze HDInsight na platformie azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Aby skonfigurować replikację geograficzną Apache HBase, zobacz [Konfigurowanie replikacji klastra Apache HBase w sieciach wirtualnych platformy Azure](hbase/apache-hbase-replication.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).
