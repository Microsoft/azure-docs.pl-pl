---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401020"
---
Odbiornik grupy dostępności jest adresem IP i nazwą sieci, na której nasłuchuje SQL Server grupy dostępności. Aby utworzyć odbiornik grupy dostępności, wykonaj następujące czynności:

1. <a name="getnet"></a>Pobierz nazwę zasobu sieci klastra.

    a. Użyj protokołu RDP, aby nawiązać połączenie z maszyną wirtualną platformy Azure, która obsługuje replikę podstawową. 

    b. Otwórz Menedżer klastra trybu failover.

    c. Wybierz węzeł **sieci** i zanotuj nazwę sieci klastra. Użyj tej nazwy w `$ClusterNetworkName` zmiennej w skrypcie programu PowerShell. Na poniższej ilustracji nazwa sieci klastra to **Network 1**:

   ![Nazwa sieci klastra](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Dodaj punkt dostępu klienta.  
    Punkt dostępu klienta to nazwa sieci używana przez aplikacje do łączenia się z bazami danych w grupie dostępności. Utwórz punkt dostępu klienta w Menedżer klastra trybu failover.

    a. Rozwiń węzeł Nazwa klastra, a następnie kliknij pozycję **role**.

    b. W okienku **role** kliknij prawym przyciskiem myszy nazwę grupy dostępności, a następnie wybierz pozycję **Dodaj zasób**  >  **Klient punkt dostępu**.

   ![Zrzut ekranu pokazujący opcję menu punkt dostępu klienta.](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. W polu **Nazwa** Utwórz nazwę dla tego nowego odbiornika. 
   Nazwa nowego odbiornika to nazwa sieci używana przez aplikacje do łączenia się z bazami danych w grupie dostępności SQL Server.

    d. Aby zakończyć tworzenie odbiornika, kliknij przycisk **dalej** dwa razy, a następnie kliknij przycisk **Zakończ**. W tym momencie nie należy przenosić odbiornika ani zasobu do trybu online.

1. Przełącz rolę klastra grupy dostępności w tryb offline. W **Menedżer klastra trybu failover** w obszarze **role** kliknij prawym przyciskiem myszy rolę, a następnie wybierz pozycję **Zatrzymaj rolę**.

1. <a name="congroup"></a>Skonfiguruj zasób IP dla grupy dostępności.

    a. Kliknij kartę **zasoby** , a następnie rozwiń utworzony punkt dostępu klienta.  
    Punkt dostępu klienta jest w trybie offline.

   ![Punkt dostępu klienta](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Kliknij prawym przyciskiem myszy zasób IP, a następnie kliknij polecenie Właściwości. Zanotuj nazwę adresu IP i użyj go w `$IPResourceName` zmiennej w skrypcie programu PowerShell.

    c. W obszarze **adres IP** kliknij pozycję **statyczny adres IP**. Ustaw adres IP jako ten sam adres, który był używany podczas ustawiania adresu usługi równoważenia obciążenia na Azure Portal.

   ![Zrzut ekranu pokazujący miejsce ustawienia adresu IP.](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Włącz zasób grupy dostępności SQL Server zależny od punktu dostępu klienta.

    a. W Menedżer klastra trybu failover kliknij pozycję **role**, a następnie kliknij swoją grupę dostępności.

    b. Na karcie **zasoby** w obszarze **inne zasoby** kliknij prawym przyciskiem myszy grupę zasobów dostępność, a następnie kliknij polecenie **Właściwości**. 

    c. Na karcie zależności Dodaj nazwę zasobu punktu dostępu klienta (odbiornika).

   ![Zrzut ekranu pokazujący, gdzie dodać nazwę na karcie zależności.](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Kliknij przycisk **OK**.

1. <a name="listname"></a>Udostępnij zasób punktu dostępu klienta zależnie od adresu IP.

    a. W Menedżer klastra trybu failover kliknij pozycję **role**, a następnie kliknij swoją grupę dostępności. 

    b. Na karcie **zasoby** kliknij prawym przyciskiem myszy zasób punktu dostępu klienta w obszarze **Nazwa serwera**, a następnie kliknij polecenie **Właściwości**. 

   ![Zrzut ekranu pokazujący opcję menu właściwości dla nazwy serwera.](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Kliknij kartę **zależności** . Sprawdź, czy adres IP jest zależnością. Jeśli tak nie jest, ustaw zależność od adresu IP. Jeśli na liście znajduje się wiele zasobów, sprawdź, czy adresy IP mają zależności lub, nie i. Kliknij przycisk **OK**. 

   ![Zasób IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Można sprawdzić, czy zależności są prawidłowo skonfigurowane. W Menedżer klastra trybu failover przejdź do ról, kliknij prawym przyciskiem myszy grupę dostępności, kliknij polecenie **więcej akcji**, a następnie kliknij pozycję  **Pokaż raport zależności**. Po poprawnym skonfigurowaniu zależności Grupa dostępności jest zależna od nazwy sieci, a nazwa sieci zależy od adresu IP. 


1. <a name="setparam"></a>Ustaw parametry klastra w programie PowerShell.

   a. Skopiuj poniższy skrypt programu PowerShell do jednego z wystąpień SQL Server. Aktualizowanie zmiennych dla środowiska.

   - `$ListenerILBIP` jest adresem IP utworzonym w module równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.
    
   - `$ListenerProbePort` to port skonfigurowany w module równoważenia obciążenia platformy Azure dla odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell na jednym z węzłów klastra.  

   > [!NOTE]
   > Jeśli wystąpienia SQL Server znajdują się w osobnych regionach, należy dwukrotnie uruchomić skrypt programu PowerShell. Po raz pierwszy Użyj `$ListenerILBIP` i `$ListenerProbePort` z pierwszego regionu. Drugi raz, użyj `$ListenerILBIP` i `$ListenerProbePort` z drugiego regionu. Nazwa sieci klastra i nazwa zasobu adresu IP klastra są również różne dla każdego regionu.

1. Przełącz rolę klastra grupy dostępności do trybu online. W **Menedżer klastra trybu failover** w obszarze **role** kliknij prawym przyciskiem myszy rolę, a następnie wybierz pozycję **Uruchom rolę**.

W razie potrzeby powtórz powyższe kroki, aby ustawić parametry klastra dla adresu IP klastra usługi WSFC.

1. Pobierz nazwę adresu IP klastra usługi WSFC. W **Menedżer klastra trybu failover** w obszarze **zasoby podstawowe klastra** Zlokalizuj **nazwę serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **adres IP**, a następnie wybierz polecenie **Właściwości**.

1. Skopiuj **nazwę** adresu IP. Może to być `Cluster IP Address` . 

1. <a name="setwsfcparam"></a>Ustaw parametry klastra w programie PowerShell.
  
   a. Skopiuj poniższy skrypt programu PowerShell do jednego z wystąpień SQL Server. Aktualizowanie zmiennych dla środowiska.

   - `$ClusterCoreIP` jest adresem IP utworzonym w module równoważenia obciążenia platformy Azure dla zasobu podstawowego klastra usługi WSFC. Różni się od adresu IP odbiornika grupy dostępności.

   - `$ClusterProbePort` to port skonfigurowany w module równoważenia obciążenia platformy Azure dla sondy kondycji usługi WSFC. Różni się od sondy odbiornika grupy dostępności.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ustaw parametry klastra, uruchamiając skrypt programu PowerShell na jednym z węzłów klastra.  

>[!WARNING]
>Port sondy kondycji odbiornika grupy dostępności musi być inny niż port sondy kondycji podstawowego adresu IP klastra. W tych przykładach portem odbiornika jest 59999, a port sondy kondycji podstawowego adresu IP klastra to 58888. Oba porty wymagają reguły zapory Zezwalaj na ruch przychodzący.
