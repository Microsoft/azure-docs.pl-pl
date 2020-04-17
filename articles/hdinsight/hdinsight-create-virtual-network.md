---
title: Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak utworzyć sieć wirtualną platformy Azure w celu połączenia usługi HDInsight z innymi zasobami w chmurze lub zasobami w centrum danych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 0c7791d43ffbbc13ab151362c5c3026ebbdb0d34
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531020"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight

W tym artykule przedstawiono przykłady i przykłady kodu do tworzenia i konfigurowania [sieci wirtualnych platformy Azure.](../virtual-network/virtual-networks-overview.md) Do użycia z klastrami usługi Azure HDInsight. Przedstawiono szczegółowe przykłady tworzenia sieciowych grup zabezpieczeń (NSG) i konfigurowania systemu DNS.

Aby uzyskać podstawowe informacje na temat korzystania z sieci wirtualnych za pomocą usługi Azure HDInsight, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Wymagania wstępne dotyczące przykładów kodu i przykładów

Przed wykonaniem któregokolwiek z przykładów kodu w tym artykule, mieć zrozumienie sieci TCP/IP. Jeśli nie znasz sieci TCP/IP, skonsultuj się z kimś przed wprowadzeniem zmian w sieciach produkcyjnych.

Inne wymagania wstępne dla przykładów w tym artykule obejmują następujące elementy:

* Jeśli używasz programu PowerShell, musisz zainstalować [moduł AZ](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli chcesz używać interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowałeś, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Jeśli szukasz wskazówek krok po kroku na temat łączenia usługi HDInsight z siecią lokalną przy użyciu sieci wirtualnej platformy Azure, zobacz [Connect HDInsight do lokalnego dokumentu sieciowego.](connect-on-premises-network.md)

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Przykład: sieciowe grupy zabezpieczeń z funkcją HDInsight

Przykłady w tej sekcji pokazują, jak utworzyć reguły sieciowej grupy zabezpieczeń. Reguły umożliwiają usługi HDInsight komunikowania się z usługami zarządzania platformy Azure. Przed użyciem przykładów dostosuj adresy IP, aby były zgodne z adresami używanymi w regionie platformy Azure. Informacje te można znaleźć w [adresach IP zarządzania hdinsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Szablon zarządzania zasobami platformy Azure

Poniższy szablon zarządzania zasobami tworzy sieć wirtualną, która ogranicza ruch przychodzący, ale umożliwia ruch z adresów IP wymaganych przez program HDInsight. Ten szablon tworzy również klaster HDInsight w sieci wirtualnej.

* [Wdrażanie zabezpieczonej sieci wirtualnej platformy Azure i klastra usługi HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt programu PowerShell służy do tworzenia sieci wirtualnej, która ogranicza ruch przychodzący i umożliwia ruch z adresów IP dla regionu Europa Północna.

> [!IMPORTANT]  
> Zmień adresy `hdirule1` IP `hdirule2` dla i w tym przykładzie, aby dopasować region platformy Azure, którego używasz. Informacje te można znaleźć w [adresach IP zarządzania informacjami HDInsight](hdinsight-management-ip-addresses.md).

```powershell
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

W tym przykładzie pokazano, jak dodać reguły, aby zezwolić na ruch przychodzący na wymaganych adresów IP. Nie zawiera reguły ograniczającej dostęp przychodzący z innych źródeł. Poniższy kod pokazuje, jak włączyć dostęp SSH z Internetu:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższe kroki umożliwiają utworzenie sieci wirtualnej, która ogranicza ruch przychodzący, ale umożliwia ruch z adresów IP wymaganych przez program HDInsight.

1. Użyj następującego polecenia, aby utworzyć `hdisecure`nową grupę zabezpieczeń sieciowej o nazwie . Zamień `RESOURCEGROUP` na grupę zasobów zawierającą sieć wirtualną platformy Azure. Zastąp `LOCATION` lokalizacją (regionem), w którym została utworzona grupa.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Po utworzeniu grupy otrzymasz informacje o nowej grupie.

2. Poniżej przedstawiono następujące czynności, aby dodać reguły do nowej sieciowej grupy zabezpieczeń, które umożliwiają komunikację przychodzącą na porcie 443 z usługi kondycji i zarządzania usługi Azure HDInsight. Zamień `RESOURCEGROUP` na nazwę grupy zasobów, która zawiera sieć wirtualną platformy Azure.

    > [!IMPORTANT]  
    > Zmień adresy `hdirule1` IP `hdirule2` dla i w tym przykładzie, aby dopasować region platformy Azure, którego używasz. Informacje te można znaleźć w [adresach IP zarządzania hdinsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Aby pobrać unikatowy identyfikator tej sieciowej grupy zabezpieczeń, użyj następującego polecenia:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Użyj następującego polecenia, aby zastosować grupę zabezpieczeń sieci do podsieci. Zastąp `GUID` wartości i `RESOURCEGROUP` wartości z wartościami zwróconymi z poprzedniego kroku. Zamień `VNETNAME` i `SUBNETNAME` nazwę sieci wirtualnej i podsieci, którą chcesz utworzyć.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po zakończeniu tego polecenia można zainstalować usługę HDInsight w sieci wirtualnej.

Te kroki tylko otwarty dostęp do usługi kondycji i zarządzania HDInsight w chmurze platformy Azure. Każdy inny dostęp do klastra HDInsight spoza sieci wirtualnej jest zablokowany. Aby włączyć dostęp spoza sieci wirtualnej, należy dodać dodatkowe reguły sieciowej grupy zabezpieczeń.

Poniższy kod pokazuje, jak włączyć dostęp SSH z Internetu:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Przykład: konfiguracja DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Rozpoznawanie nazw między siecią wirtualną a połączoną siecią lokalną

W tym przykładzie przedstawiono następujące założenia:

* Masz sieć wirtualną platformy Azure, która jest połączona z siecią lokalną przy użyciu bramy sieci VPN.

* Niestandardowy serwer DNS w sieci wirtualnej jest uruchomiony Linux lub Unix jako system operacyjny.

* [Powiązanie](https://www.isc.org/downloads/bind/) jest instalowane na niestandardowym serwerze DNS.

Na niestandardowym serwerze DNS w sieci wirtualnej:

1. Użyj narzędzia Azure PowerShell lub narzędzia Azure CLI, aby znaleźć sufiks DNS sieci wirtualnej:

    Zamień `RESOURCEGROUP` nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Na niestandardowym serwerze DNS sieci wirtualnej użyj następującego `/etc/bind/named.conf.local` tekstu jako zawartości pliku:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` tę wartość sufiksem DNS sieci wirtualnej.

    Ta konfiguracja kieruje wszystkie żądania DNS dla sufiksu DNS sieci wirtualnej do cyklicznego programu rozpoznawania nazw platformy Azure.

1. Na niestandardowym serwerze DNS sieci wirtualnej użyj następującego `/etc/bind/named.conf.options` tekstu jako zawartości pliku:

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
    
    * Zastąp `10.0.0.0/16` wartość zakresem adresów IP sieci wirtualnej. Ten wpis umożliwia adresy żądań rozpoznawania nazw w tym zakresie.

    * Dodaj zakres adresów IP sieci lokalnej do `acl goodclients { ... }` sekcji.  wpis umożliwia żądania rozpoznawania nazw z zasobów w sieci lokalnej.
    
    * Zastąp wartość `192.168.0.1` adresem IP lokalnego serwera DNS. Ten wpis kieruje wszystkie inne żądania DNS do lokalnego serwera DNS.

1. Aby użyć konfiguracji, uruchom ponownie bind. Na przykład `sudo service bind9 restart`.

1. Dodaj usługę przesyłania dalej warunkowej do lokalnego serwera DNS. Skonfiguruj usługę przesyłania dalej warunkowej do wysyłania żądań sufiksu DNS z kroku 1 do niestandardowego serwera DNS.

    > [!NOTE]  
    > Szczegółowe informacje na temat dodawania usługi przesyłania dalej warunkowej można znaleźć w dokumentacji oprogramowania DNS.

Po wykonaniu tych kroków można połączyć się z zasobami w obu sieciach przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować program HDInsight w sieci wirtualnej.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Rozpoznawanie nazw między dwiema połączonymi sieciami wirtualnymi

W tym przykładzie przedstawiono następujące założenia:

* Masz dwie sieci wirtualne platformy Azure, które są połączone przy użyciu bramy sieci VPN lub komunikacji równorzędnej.

* Niestandardowy serwer DNS w obu sieciach używa systemu Linux lub Unix jako systemu operacyjnego.

* [Bind](https://www.isc.org/downloads/bind/) jest instalowany na niestandardowych serwerach DNS.

1. Użyj narzędzia Azure PowerShell lub narzędzia Azure CLI, aby znaleźć sufiks DNS obu sieci wirtualnych:

    Zamień `RESOURCEGROUP` nazwę grupy zasobów zawierającej sieć wirtualną, a następnie wprowadź polecenie:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Użyj następującego tekstu jako `/etc/bind/named.config.local` zawartości pliku na niestandardowym serwerze DNS. Tę zmianę należy wprowadzić na niestandardowym serwerze DNS w obu sieciach wirtualnych.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` tę wartość sufiksem DNS __innej__ sieci wirtualnej. Ten wpis kieruje żądania sufiksu DNS sieci zdalnej do niestandardowego systemu DNS w tej sieci.

3. Na niestandardowych serwerach DNS w obu sieciach wirtualnych `/etc/bind/named.conf.options` użyj następującego tekstu jako zawartości pliku:

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

   Zastąp `10.0.0.0/16` i `10.1.0.0/16` wartości zakresami adresów IP sieci wirtualnych. Ten wpis umożliwia zasobom w każdej sieci żądania serwerów DNS.

    Wszystkie żądania, które nie są dla sufiksów DNS sieci wirtualnych (na przykład microsoft.com) jest obsługiwany przez cykliczny program rozpoznawania nazw platformy Azure.

4. Aby użyć konfiguracji, uruchom ponownie bind. Na przykład `sudo service bind9 restart` na obu serwerach DNS.

Po wykonaniu tych kroków można połączyć się z zasobami w sieci wirtualnej przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować program HDInsight w sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać pełny przykład konfigurowania usługi HDInsight do łączenia się z siecią lokalną, zobacz [Łączenie usługi HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby skonfigurować klastry Apache HBase w sieciach wirtualnych platformy Azure, zobacz [Tworzenie klastrów Apache HBase w usłudze HDInsight w sieci wirtualnej platformy Azure](hbase/apache-hbase-provision-vnet.md).
* Aby skonfigurować replikację geograficzną Apache HBase, zobacz [Konfigurowanie replikacji klastra Apache HBase w sieciach wirtualnych platformy Azure](hbase/apache-hbase-replication.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika i przekierowanie IP](../virtual-network/virtual-networks-udr-overview.md).
