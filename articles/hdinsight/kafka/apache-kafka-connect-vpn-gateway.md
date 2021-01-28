---
title: Nawiązywanie połączenia z usługą Kafka przy użyciu sieci wirtualnych — usługa Azure HDInsight
description: Dowiedz się, jak bezpośrednio łączyć się z usługą Kafka w usłudze HDInsight za pomocą Virtual Network platformy Azure. Dowiedz się, jak nawiązać połączenie z usługą Kafka od klientów programistycznych przy użyciu bramy sieci VPN lub z klientów w sieci lokalnej przy użyciu urządzenia bramy sieci VPN.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: ad802b2bdf08a8e43179beece5f52d869513aff3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933049"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Nawiązywanie połączenia z platformą Apache Kafka w usłudze HDInsight przy użyciu sieci wirtualnej platformy Azure

Dowiedz się, jak bezpośrednio nawiązać połączenie z usługą Apache Kafka w usłudze HDInsight za pomocą usługi Azure Virtual Network. Ten dokument zawiera informacje dotyczące nawiązywania połączenia z usługą Kafka przy użyciu następujących konfiguracji:

* Z zasobów w sieci lokalnej. To połączenie jest nawiązywane za pomocą urządzenia sieci VPN (oprogramowania lub sprzętu) w sieci lokalnej.
* Ze środowiska programistycznego korzystającego z klienta oprogramowania sieci VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektura i planowanie

Usługa HDInsight nie zezwala na bezpośrednie łączenie się z usługą Kafka za pośrednictwem publicznego Internetu. Zamiast tego klienci Kafka (producenci i konsumenci) muszą korzystać z jednej z następujących metod połączenia:

* Uruchom klienta w tej samej sieci wirtualnej co Kafka w usłudze HDInsight. Ta konfiguracja jest używana w dokumencie [Rozpocznij od Apache Kafka w programie HDInsight](apache-kafka-get-started.md) . Klient działa bezpośrednio w węzłach klastra usługi HDInsight lub na innej maszynie wirtualnej w tej samej sieci.

* Połącz sieć prywatną, taką jak sieć lokalna, z siecią wirtualną. Ta konfiguracja umożliwia klientom w sieci lokalnej bezpośrednie współpracuję z usługą Kafka. Aby włączyć tę konfigurację, wykonaj następujące zadania:

  1. Utwórz sieć wirtualną.
  2. Utwórz bramę sieci VPN, która korzysta z konfiguracji lokacja-lokacja. Konfiguracja użyta w tym dokumencie nawiązuje połączenie z urządzeniem bramy sieci VPN w sieci lokalnej.
  3. Utwórz serwer DNS w sieci wirtualnej.
  4. Skonfiguruj przekazywanie między serwerem DNS w każdej sieci.
  5. Utwórz Kafka w klastrze usługi HDInsight w sieci wirtualnej.

     Aby uzyskać więcej informacji, zobacz sekcję [łączenie się z Apache Kafka w sieci lokalnej](#on-premises) .

* Połącz poszczególne maszyny z siecią wirtualną przy użyciu bramy sieci VPN i klienta sieci VPN. Aby włączyć tę konfigurację, wykonaj następujące zadania:

  1. Utwórz sieć wirtualną.
  2. Utwórz bramę sieci VPN, która korzysta z konfiguracji typu punkt-lokacja. Ta konfiguracja może być używana z klientami systemu Windows i MacOS.
  3. Utwórz Kafka w klastrze usługi HDInsight w sieci wirtualnej.
  4. Skonfiguruj Kafka na potrzeby reklamy adresów IP. Ta konfiguracja pozwala klientowi na łączenie się przy użyciu adresów IP brokera zamiast nazw domen.
  5. Pobierz klienta sieci VPN i użyj go w systemie deweloperskim.

     Aby uzyskać więcej informacji, zobacz sekcję [łączenie się z Apache Kafka z klientem sieci VPN](#vpnclient) .

     > [!WARNING]  
     > Ta konfiguracja jest zalecana tylko w celach programistycznych ze względu na następujące ograniczenia:
     >
     > * Każdy klient musi nawiązać połączenie przy użyciu klienta oprogramowania sieci VPN.
     > * Klient sieci VPN nie przekazuje żądań rozpoznawania nazw do sieci wirtualnej, dlatego należy użyć adresu IP do komunikacji z Kafka. Komunikacja IP wymaga dodatkowej konfiguracji w klastrze Kafka.

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zobacz [Planowanie sieci wirtualnej dla klastrów usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> Nawiązywanie połączenia z Apache Kafka z sieci lokalnej

Aby utworzyć klaster Kafka, który komunikuje się z siecią lokalną, wykonaj kroki opisane w dokumencie [łączenie usługi HDInsight z](./../connect-on-premises-network.md) lokalnym dokumentem sieciowym.

> [!IMPORTANT]  
> Podczas tworzenia klastra usługi HDInsight wybierz typ klastra __Kafka__ .

W tym kroku opisano tworzenie następującej konfiguracji:

* Azure Virtual Network
* Brama sieci VPN typu lokacja-lokacja
* Konto usługi Azure Storage (używane przez usługę HDInsight)
* Usługa Kafka w usłudze HDInsight

Aby sprawdzić, czy klient Kafka może połączyć się z klastrem ze środowiska lokalnego, wykonaj kroki opisane w sekcji [przykład: klient języka Python](#python-client) .

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> Nawiązywanie połączenia z Apache Kafka z klientem sieci VPN

Wykonaj kroki opisane w tej sekcji, aby utworzyć następującą konfigurację:

* Azure Virtual Network
* Brama sieci VPN typu punkt-lokacja
* Konto usługi Azure Storage (używane przez usługę HDInsight)
* Usługa Kafka w usłudze HDInsight

1. Wykonaj kroki opisane w sekcji [Praca z certyfikatami z podpisem własnym w przypadku dokumentów połączeń punkt-lokacja](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) . Ten dokument tworzy certyfikaty, które są zbędne dla bramy.

2. Otwórz wiersz polecenia programu PowerShell i użyj następującego kodu, aby zalogować się do subskrypcji platformy Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Użyj następującego kodu, aby utworzyć zmienne zawierające informacje o konfiguracji:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Użyj następującego kodu, aby utworzyć grupę zasobów platformy Azure i sieć wirtualną:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Ukończenie tego procesu może potrwać kilka minut.

5. Użyj poniższego kodu, aby utworzyć konto usługi Azure Storage i kontener obiektów blob:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Użyj następującego kodu, aby utworzyć klaster usługi HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Ten proces może potrwać około 15 minut.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurowanie Kafka na potrzeby reklamy adresów IP

Domyślnie Apache dozorcy zwraca nazwę domeny brokerów Kafka do klientów. Ta konfiguracja nie działa z klientem oprogramowania sieci VPN, ponieważ nie może używać rozpoznawania nazw dla jednostek w sieci wirtualnej. W przypadku tej konfiguracji wykonaj następujące kroki, aby skonfigurować Kafka do anonsowania adresów IP zamiast nazw domen:

1. Korzystając z przeglądarki internetowej, przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zamień na `CLUSTERNAME` nazwę Kafka w klastrze usługi HDInsight.

    Po wyświetleniu monitu użyj nazwy użytkownika i hasła protokołu HTTPS dla klastra. Zostanie wyświetlony interfejs użytkownika sieci Web Ambari dla klastra.

2. Aby wyświetlić informacje o Kafka, wybierz pozycję __Kafka__ z listy po lewej stronie.

    ![Lista usług z wyróżnioną pozycją Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Aby wyświetlić konfigurację Kafka __, wybierz pozycję__ konfiguracje w górnej części.

    ![Konfiguracja usług Apache Ambari Services](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Aby znaleźć konfigurację __Kafka-ENV__ , wprowadź `kafka-env` w polu __filtru__ w prawym górnym rogu.

    ![Konfiguracja Kafka dla Kafka-ENV](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Aby skonfigurować Kafka do anonsowania adresów IP, Dodaj następujący tekst na dole pola __Kafka-ENV-Template__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Aby skonfigurować interfejs, który Kafka nasłuchuje, wprowadź `listeners` w polu __filtru__ w prawym górnym rogu.

7. Aby skonfigurować Kafka do nasłuchiwania na wszystkich interfejsach sieciowych, Zmień wartość w polu __detektory__ na `PLAINTEXT://0.0.0.0:9092` .

8. Aby zapisać zmiany konfiguracji, użyj przycisku __Zapisz__ . Wprowadź wiadomość tekstową opisującą zmiany. Po zapisaniu zmian wybierz __przycisk OK__ .

    ![Konfiguracja oprogramowania Apache Ambari](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Aby zapobiec błędom podczas ponownego uruchamiania Kafka, użyj przycisku __Akcje usługi__ i wybierz pozycję __Włącz tryb konserwacji__. Wybierz przycisk OK, aby ukończyć tę operację.

    ![Akcje usługi z opcją Włącz konserwację](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Aby ponownie uruchomić Kafka, użyj przycisku __Uruchom ponownie__ , a następnie wybierz pozycję __Uruchom ponownie wszystkie uwzględnione__. Potwierdź ponowne uruchomienie, a następnie użyj przycisku __OK__ po zakończeniu operacji.

    ![Przycisk Uruchom ponownie z wyróżnionym ponownym użyciem](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Aby wyłączyć tryb konserwacji, użyj przycisku __Akcje usługi__ i wybierz polecenie Wyłącz __tryb konserwacji__. Wybierz **przycisk OK** , aby ukończyć tę operację.

### <a name="connect-to-the-vpn-gateway"></a>Nawiązywanie połączenia z bramą sieci VPN

Aby nawiązać połączenie z bramą sieci VPN, użyj sekcji __łączenie z platformą Azure__ w dokumencie [Konfigurowanie połączenia punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) .

## <a name="example-python-client"></a><a id="python-client"></a> Przykład: klient języka Python

Aby sprawdzić poprawność łączności z usługą Kafka, wykonaj następujące kroki, aby utworzyć i uruchomić producenta i klienta języka Python:

1. Użyj jednej z następujących metod, aby pobrać w pełni kwalifikowaną nazwę domeny (FQDN) i adresy IP węzłów w klastrze Kafka:

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

    Ten skrypt zakłada, że `$resourceGroupName` jest nazwą grupy zasobów platformy Azure, która zawiera sieć wirtualną.

    Zapisz zwrócone informacje do użycia w następnych krokach.

2. Aby zainstalować klienta [Kafka-Python](https://kafka-python.readthedocs.io/) , należy wykonać następujące czynności:

    ```bash
    pip install kafka-python
    ```

3. Aby wysłać dane do Kafka, użyj następującego kodu w języku Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Zamień `'kafka_broker'` wpisy na adresy zwrócone z kroku 1 w tej sekcji:

   * Jeśli używasz __klienta sieci VPN oprogramowania__, Zastąp `kafka_broker` wpisy adresem IP węzłów procesu roboczego.

   * Jeśli __włączono rozpoznawanie nazw za pomocą niestandardowego serwera DNS__, Zastąp wpisy nazwą `kafka_broker` FQDN węzłów procesu roboczego.

     > [!NOTE]
     > Ten kod wysyła ciąg `test message` do tematu `testtopic` . Domyślną konfiguracją Kafka w usłudze HDInsight jest utworzenie tematu, jeśli nie istnieje.

4. Aby pobrać komunikaty z Kafka, użyj następującego kodu w języku Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Zamień `'kafka_broker'` wpisy na adresy zwrócone z kroku 1 w tej sekcji:

    * Jeśli używasz __klienta sieci VPN oprogramowania__, Zastąp `kafka_broker` wpisy adresem IP węzłów procesu roboczego.

    * Jeśli __włączono rozpoznawanie nazw za pomocą niestandardowego serwera DNS__, Zastąp wpisy nazwą `kafka_broker` FQDN węzłów procesu roboczego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną, zobacz dokument [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) .

Aby uzyskać więcej informacji na temat tworzenia Virtual Network platformy Azure za pomocą bramy sieci VPN typu punkt-lokacja, zobacz następujące dokumenty:

* [Konfigurowanie połączenia punkt-lokacja przy użyciu Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurowanie połączenia punkt-lokacja przy użyciu Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Aby uzyskać więcej informacji na temat pracy z usługą Apache Kafka w usłudze HDInsight, zobacz następujące dokumenty:

* [Wprowadzenie do Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md)
* [Używanie dublowania z Apache Kafka w usłudze HDInsight](apache-kafka-mirroring.md)
