---
title: Konfigurowanie odbiornika ILB dla grup dostępności (klasyczny)
description: Ten samouczek używa zasobów utworzonych przy użyciu klasycznego modelu wdrażania i tworzy odbiornik grupy dostępności zawsze włączone dla maszyny wirtualnej SQL Server na platformie Azure korzystającej z wewnętrznego modułu równoważenia obciążenia.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978166"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurowanie odbiornika ILB dla grup dostępności na maszynach wirtualnych platformy Azure SQL Server
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Azure Resource Manager i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano sposób korzystania z klasycznego modelu wdrażania. Zalecamy, aby większość nowych wdrożeń korzystała z modelu Menedżer zasobów.

Aby skonfigurować odbiornik dla grupy dostępności zawsze włączone w modelu Menedżer zasobów, zobacz sekcję [Konfigurowanie modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na platformie Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Twoja grupa dostępności może zawierać repliki tylko lokalnie lub tylko platformę Azure, które obejmują konfiguracje hybrydowe i platformy Azure. Repliki platformy Azure mogą znajdować się w tym samym regionie lub w wielu regionach, w których jest używanych wiele sieci wirtualnych. W procedurach przedstawionych w tym artykule przyjęto założenie, że już [skonfigurowano grupę dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale jeszcze nie skonfigurowano odbiornika.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Wskazówki i ograniczenia dotyczące wewnętrznych odbiorników
Korzystanie z wewnętrznego modułu równoważenia obciążenia (ILB) z odbiornikiem grupy dostępności na platformie Azure podlega następującym wytycznym:

* Odbiornik grupy dostępności jest obsługiwany w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Dla każdej usługi w chmurze jest obsługiwany tylko jeden odbiornik wewnętrznej grupy dostępności, ponieważ odbiornik jest skonfigurowany pod kątem ILB, a dla każdej usługi w chmurze jest tylko jeden ILB. Istnieje jednak możliwość utworzenia wielu zewnętrznych odbiorników. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odbiornika zewnętrznego dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Określanie dostępności odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł koncentruje się na tworzeniu odbiornika korzystającego z ILB. Jeśli potrzebujesz odbiornika publicznego lub zewnętrznego, zapoznaj się z wersją tego artykułu, która opisuje Konfigurowanie [odbiornika zewnętrznego](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie punktów końcowych maszyn wirtualnych ze zrównoważonym obciążeniem przy użyciu bezpośredniego powrotu serwera
Najpierw utwórz ILB, uruchamiając skrypt w dalszej części tej sekcji.

Utwórz punkt końcowy o zrównoważonym obciążeniu dla każdej maszyny wirtualnej, która hostuje replikę platformy Azure. W przypadku replik w wielu regionach każda replika tego regionu musi znajdować się w tej samej usłudze w chmurze w tej samej sieci wirtualnej platformy Azure. Tworzenie replik grup dostępności obejmujących wiele regionów platformy Azure wymaga skonfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania łączności między sieciami wirtualnymi, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W Azure Portal przejdź do każdej maszyny wirtualnej, która hostuje replikę, aby wyświetlić szczegóły.

2. Kliknij kartę **punkty końcowe** dla każdej maszyny wirtualnej.

3. Sprawdź, czy **Nazwa** i **port publiczny** punktu końcowego odbiornika, którego chcesz użyć, nie są już używane. W przykładzie w tej sekcji *nazwa to, a port to* *1433*.

4. Na lokalnym kliencie Pobierz i zainstaluj najnowszy [moduł programu PowerShell](https://azure.microsoft.com/downloads/).

5. Rozpocznij Azure PowerShell.  
    Zostanie otwarta nowa sesja programu PowerShell z załadowanymi modułami administracyjnymi platformy Azure.

6. Uruchom polecenie `Get-AzurePublishSettingsFile`. To polecenie cmdlet kieruje użytkownika do przeglądarki w celu pobrania pliku ustawień publikowania do katalogu lokalnego. Może zostać wyświetlony monit o podanie poświadczeń logowania dla subskrypcji platformy Azure.

7. Uruchom następujące `Import-AzurePublishSettingsFile` polecenie ze ścieżką pobranego pliku ustawień publikowania:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Po zaimportowaniu pliku ustawień publikowania możesz zarządzać subskrypcją platformy Azure w sesji programu PowerShell.

8. W przypadku *ILB*Przypisz statyczny adres IP. Przejrzyj bieżącą konfigurację sieci wirtualnej, uruchamiając następujące polecenie:

        (Get-AzureVNetConfig).XMLConfiguration
9. Zanotuj nazwę *podsieci* podsieci zawierającej maszyny wirtualne, które obsługują repliki. Ta nazwa jest używana w parametrze $SubnetName skryptu.

10. Zanotuj nazwę *VirtualNetworkSite* i początkowy *AddressPrefix* dla podsieci zawierającej maszyny wirtualne, które obsługują repliki. Wyszukaj dostępny adres IP, przekazując obie wartości do `Test-AzureStaticVNetIP` polecenia i sprawdzając *AvailableAddresses*. Na przykład jeśli sieć wirtualna ma nazwę *MyVNet* i ma zakres adresów podsieci, który zaczyna się od *172.16.0.128*, następujące polecenie wyświetla listę dostępnych adresów:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Wybierz jeden z dostępnych adresów i użyj go w parametrze $ILBStaticIP skryptu w następnym kroku.

12. Skopiuj poniższy skrypt programu PowerShell do edytora tekstów i ustaw wartości zmiennej na odpowiednie dla danego środowiska. Określono wartości domyślne dla niektórych parametrów.  

    Istniejące wdrożenia korzystające z grup koligacji nie mogą dodawać ILB. Aby uzyskać więcej informacji o wymaganiach ILB, zobacz temat [Omówienie wewnętrznego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-internal-overview.md).

    Ponadto, jeśli grupa dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt jeden raz w każdym centrum danych dla usługi w chmurze i węzłów, które znajdują się w tym centrum danych.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Po ustawieniu zmiennych skopiuj skrypt z edytora tekstu do sesji programu PowerShell, aby go uruchomić. Jeśli monit nadal pojawia **>>** się, naciśnij klawisz ENTER, aby upewnić się, że skrypt zacznie działać.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Upewnij się, że w razie potrzeby jest zainstalowana KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwórz porty zapory w węzłach Grupa dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktu dostępu klienta i skonfiguruj zależności. Następnie skonfiguruj zasoby klastra w programie PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Tworzenie punktu dostępu klienta i Konfigurowanie zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. W przypadku ILB należy użyć adresu IP ILB, który został utworzony wcześniej. Aby uzyskać ten adres IP w programie PowerShell, użyj następującego skryptu:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Na jednej z maszyn wirtualnych Skopiuj skrypt programu PowerShell dla danego systemu operacyjnego do edytora tekstów, a następnie ustaw zmienne na zanotowane wcześniej wartości.

    W przypadku systemu Windows Server 2012 lub nowszego Użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    W przypadku systemu Windows Server 2008 R2 Użyj następującego skryptu:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Po ustawieniu zmiennych Otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, wklej skrypt z edytora tekstów do sesji programu PowerShell, aby go uruchomić. Jeśli monit nadal pojawia **>>** się, naciśnij klawisz ENTER, aby upewnić się, że skrypt zacznie działać.

4. Powtórz powyższe kroki dla każdej maszyny wirtualnej.  
    Ten skrypt konfiguruje zasób adresu IP przy użyciu adresu IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Gdy zasób adresu IP jest przełączany w tryb online, może odpowiedzieć na sondowanie na porcie sondy od utworzonego wcześniej punktu końcowego ze zrównoważonym obciążeniem.

## <a name="bring-the-listener-online"></a>Przełączenie odbiornika w tryb online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy uzupełniające
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
