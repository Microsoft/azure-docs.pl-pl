---
title: Konfigurowanie odbiornika zewnętrznego dla grup dostępności
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia odbiornika zawsze włączonych grup dostępności na platformie Azure, który jest dostępny zewnętrznie przy użyciu publicznego wirtualnego adresu IP skojarzonej usługi w chmurze.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75978182"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Konfigurowanie zewnętrznego odbiornika dla grup dostępności na maszynach wirtualnych platformy Azure SQL Server
> [!div class="op_single_selector"]
> * [Odbiornik wewnętrzny](../classic/ps-sql-int-listener.md)
> * [Odbiornik zewnętrzny](../classic/ps-sql-ext-listener.md)
> 
> 

W tym temacie opisano sposób konfigurowania odbiornika dla zawsze włączonych grup dostępności, które są dostępne zewnętrznie w Internecie. Jest to możliwe dzięki skojarzeniu **publicznego wirtualnego adresu IP (VIP)** usługi w chmurze z odbiornikiem.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania służące do tworzenia zasobów i pracy z nimi: [Menedżer zasobów i klasyczne](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano korzystanie z klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Grupa dostępności może zawierać repliki tylko lokalnie, tylko platformę Azure lub obejmować konfiguracje hybrydowe i platformy Azure. Repliki platformy Azure mogą znajdować się w tym samym regionie lub w wielu regionach, w których jest używana wiele sieci wirtualnych (sieci wirtualnych). W poniższych krokach przyjęto, że [skonfigurowano już grupę dostępności](../classic/portal-sql-alwayson-availability-groups.md) , ale nie skonfigurowano odbiornika.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Wskazówki i ograniczenia dotyczące zewnętrznych odbiorników
Podczas wdrażania przy użyciu publicznego adresu VIP usługi w chmurze należy zwrócić uwagę na następujące wskazówki dotyczące odbiornika grupy dostępności na platformie Azure:

* Odbiornik grupy dostępności jest obsługiwany w systemie Windows Server 2008 R2, Windows Server 2012 i Windows Server 2012 R2.
* Aplikacja kliencka musi znajdować się w innej usłudze w chmurze niż ta, która zawiera maszyny wirtualne grupy dostępności. Platforma Azure nie obsługuje bezpośredniego powrotu serwera z klientem i serwerem w tej samej usłudze w chmurze.
* Domyślnie kroki opisane w tym artykule pokazują, jak skonfigurować jeden odbiornik do używania wirtualnego adresu IP (VIP) usługi w chmurze. Istnieje jednak możliwość zarezerwowania i utworzenia wielu adresów VIP dla usługi w chmurze. Dzięki temu można wykonać kroki opisane w tym artykule, aby utworzyć wiele detektorów, które są skojarzone z innym adresem VIP. Aby uzyskać informacje na temat sposobu tworzenia wielu adresów VIP, zobacz [wiele VIP dla każdej usługi w chmurze](../../../load-balancer/load-balancer-multivip.md).
* Jeśli tworzysz odbiornik dla środowiska hybrydowego, Sieć lokalna musi mieć łączność z publiczną Internetem oprócz sieci VPN typu lokacja-lokacja z siecią wirtualną platformy Azure. W przypadku w podsieci platformy Azure odbiornik grupy dostępności jest dostępny tylko przez publiczny adres IP odpowiedniej usługi w chmurze.
* Nie jest obsługiwane tworzenie zewnętrznego odbiornika w tej samej usłudze w chmurze, w którym znajduje się również wewnętrzny odbiornik przy użyciu wewnętrznego Load Balancer (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Określanie dostępności odbiornika
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Ten artykuł koncentruje się na tworzeniu odbiornika korzystającego z **zewnętrznego równoważenia obciążenia**. Jeśli chcesz, aby odbiornik był prywatny dla sieci wirtualnej, zapoznaj się z wersją tego artykułu, która zawiera instrukcje dotyczące konfigurowania [odbiornika za pomocą ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Tworzenie punktów końcowych maszyn wirtualnych ze zrównoważonym obciążeniem przy użyciu bezpośredniego powrotu serwera
Zewnętrzne Równoważenie obciążenia używa wirtualnego wirtualnego adresu IP usługi w chmurze, która hostuje maszyny wirtualne. W takim przypadku nie trzeba tworzyć ani konfigurować modułu równoważenia obciążenia.

Należy utworzyć punkt końcowy o zrównoważonym obciążeniu dla każdej maszyny wirtualnej obsługującej replikę platformy Azure. W przypadku replik w wielu regionach każda replika dla tego regionu musi znajdować się w tej samej usłudze w chmurze w tej samej sieci wirtualnej. Tworzenie replik grup dostępności obejmujących wiele regionów platformy Azure wymaga skonfigurowania wielu sieci wirtualnych. Aby uzyskać więcej informacji na temat konfigurowania łączności między sieciami wirtualnymi, zobacz [Konfigurowanie łączności wirtualnej z siecią wirtualną](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. W Azure Portal przejdź do każdej maszyny wirtualnej obsługującej replikę i Wyświetl szczegóły.
2. Kliknij kartę **punkty końcowe** dla każdej z maszyn wirtualnych.
3. Sprawdź, czy **Nazwa** i **port publiczny** punktu końcowego odbiornika, którego chcesz użyć, nie jest już używany. W poniższym przykładzie nazwą jest "". ", a portem jest" 1433 ".
4. Na lokalnym kliencie Pobierz i zainstaluj [najnowszy moduł programu PowerShell](https://azure.microsoft.com/downloads/).
5. Uruchom **Azure PowerShell**. Zostanie otwarta nowa sesja programu PowerShell z załadowanymi modułami administracyjnymi platformy Azure.
6. Uruchom **Get-AzurePublishSettingsFile**. To polecenie cmdlet kieruje użytkownika do przeglądarki w celu pobrania pliku ustawień publikowania do katalogu lokalnego. Może zostać wyświetlony monit o podanie poświadczeń logowania do subskrypcji platformy Azure.
7. Uruchom polecenie **Import-AzurePublishSettingsFile** ze ścieżką pobranego pliku ustawień publikowania:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Po zaimportowaniu pliku ustawień publikowania możesz zarządzać subskrypcją platformy Azure w sesji programu PowerShell.
    
1. Skopiuj poniższy skrypt programu PowerShell do edytora tekstu i ustaw wartości zmiennych, aby odpowiadały Twojemu środowisku (wartości domyślne zostały podane dla niektórych parametrów). Należy pamiętać, że jeśli grupa dostępności obejmuje regiony platformy Azure, należy uruchomić skrypt jeden raz w każdym centrum danych dla usługi w chmurze i węzłów, które znajdują się w tym centrum danych.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Po ustawieniu zmiennych skopiuj skrypt z edytora tekstów do sesji Azure PowerShell, aby go uruchomić. Jeśli monit nadal zawiera >>, wpisz wprowadź ponownie, aby upewnić się, że skrypt zacznie działać.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Upewnij się, że w razie potrzeby jest zainstalowana KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Otwórz porty zapory w węzłach Grupa dostępności
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

Utwórz odbiornik grupy dostępności w dwóch krokach. Najpierw utwórz zasób klastra punktu dostępu klienta i skonfiguruj zależności. Następnie skonfiguruj zasoby klastra przy użyciu programu PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Tworzenie punktu dostępu klienta i Konfigurowanie zależności klastra
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Konfigurowanie zasobów klastra w programie PowerShell
1. W przypadku zewnętrznego równoważenia obciążenia należy uzyskać publiczny wirtualny adres IP usługi w chmurze zawierającej repliki. Zaloguj się do witryny Azure Portal. Przejdź do usługi w chmurze zawierającej maszynę wirtualną grupy dostępności. Otwórz widok **pulpitu nawigacyjnego** .
2. Zwróć uwagę na adres wyświetlany w obszarze **publiczny wirtualny adres IP (VIP)**. Jeśli rozwiązanie obejmuje sieci wirtualnych, Powtórz ten krok dla każdej usługi w chmurze zawierającej maszynę wirtualną, która obsługuje replikę.
3. Na jednej z maszyn wirtualnych Skopiuj poniższy skrypt programu PowerShell do edytora tekstu i Ustaw zmienne na zanotowane wcześniej wartości.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Po ustawieniu zmiennych Otwórz okno programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie skopiuj skrypt z edytora tekstów i wklej go do sesji Azure PowerShell, aby go uruchomić. Jeśli monit nadal zawiera >>, wpisz wprowadź ponownie, aby upewnić się, że skrypt zacznie działać.
5. Powtórz tę czynność dla każdej maszyny wirtualnej. Ten skrypt konfiguruje zasób adresu IP przy użyciu adresu IP usługi w chmurze i ustawia inne parametry, takie jak port sondy. Gdy zasób adresu IP jest przełączany w tryb online, może odpowiedzieć na sondowanie na porcie sondy z punktu końcowego ze zrównoważonym obciążeniem utworzonego wcześniej w tym samouczku.

## <a name="bring-the-listener-online"></a>Przełączenie odbiornika w tryb online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementy uzupełniające
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testowanie odbiornika grupy dostępności (w ramach tej samej sieci wirtualnej)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testowanie odbiornika grupy dostępności (za pośrednictwem Internetu)
Aby uzyskać dostęp do odbiornika spoza sieci wirtualnej, należy użyć zewnętrznego/publicznego równoważenia obciążenia (opisanego w tym temacie), a nie ILB, który jest dostępny tylko w ramach tej samej sieci wirtualnej. W parametrach połączenia należy określić nazwę usługi w chmurze. Na przykład jeśli masz usługę w chmurze o nazwie *mycloudservice*, instrukcja sqlcmd będzie następująca:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

W przeciwieństwie do poprzedniego przykładu należy użyć uwierzytelniania SQL, ponieważ obiekt wywołujący nie może używać uwierzytelniania systemu Windows przez Internet. Aby uzyskać więcej informacji, zobacz [zawsze włączone grupy dostępności na maszynie wirtualnej platformy Azure: scenariusze łączności klienta](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). W przypadku korzystania z uwierzytelniania SQL upewnij się, że utworzono tę samą nazwę logowania w obu replikach. Aby uzyskać więcej informacji na temat rozwiązywania problemów z logowaniem przy użyciu grup dostępności, zobacz [Jak mapować nazwy logowania lub używać zawartego użytkownika bazy danych SQL Database do łączenia się z innymi replikami i mapować do baz danych dostępności](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Jeśli zawsze włączone repliki znajdują się w różnych podsieciach, klienci muszą określić **MultiSubnetFailover = true** w parametrach połączenia. Powoduje to próby połączenia równoległego z replikami w różnych podsieciach. Należy zauważyć, że w tym scenariuszu jest wdrażana Grupa dostępności obejmująca wiele regionów.

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

