---
title: SAP ASCS/SCS — wiele identyfikatorów SID o wysokiej dostępności za pomocą usługi WSFC i udostępnionego dysku platformy Azure | Microsoft Docs
description: Wysoka dostępność dla wystąpienia SAP ASCS/SCS z użyciem usługi WSFC i udostępnionego dysku platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8116f3e00d13c0bd1e5f075a7fbe3264f337079
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970405"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Rozwiązanie SAP ASCS/SCS o wysokiej dostępności z użyciem usługi Windows Server Failover Clustering i Azure Shared Disk

> ![System operacyjny Windows][Logo_Windows] Windows
>

Ten artykuł koncentruje się na sposobie przenoszenia z jednej instalacji ASCS/SCS do konfiguracji protokołu SAP o wiele identyfikatorów SID, instalując dodatkowe wystąpienia klastrowane ASCS/SCS w istniejącym klastrze usługi Windows Server Failover Clustering (WSFC) z dyskiem udostępnionym platformy Azure. Po zakończeniu tego procesu skonfigurowano klaster SAP z obsługą wiele identyfikatorów SID.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

Obecnie można używać dysków SSD w warstwie Premium platformy Azure jako dysku udostępnionego platformy Azure dla wystąpienia SAP ASCS/SCS. Stosowane są następujące ograniczenia:

-  [Usługa Azure Ultra Disk](../../disks-types.md#ultra-disk) nie jest obsługiwana jako dysk udostępniony platformy Azure dla obciążeń SAP. Obecnie nie jest możliwe umieszczenie maszyn wirtualnych platformy Azure przy użyciu usługi Azure Ultra Disk w zestawie dostępności
-  [Dysk udostępniony platformy Azure](../../windows/disks-shared.md) z dyskami SSD w warstwie Premium jest obsługiwany tylko w przypadku maszyn wirtualnych w zestawie dostępności. Nie jest on obsługiwany w Strefy dostępności wdrożenia. 
-  Wartość dysku udostępnionego na platformie Azure [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) określa, ile węzłów klastra może używać dysku udostępnionego. Zwykle w przypadku wystąpienia SAP ASCS/SCS można skonfigurować dwa węzły w klastrze trybu failover systemu Windows, w związku z czym wartość dla parametru `maxShares` musi być równa 2.
-  Wszystkie maszyny wirtualne klastra SAP ASCS/SCS muszą zostać wdrożone w tej samej [grupie umieszczania usługi Azure zbliżeniowe](../../windows/proximity-placement-groups.md).   
   Mimo że można wdrożyć maszyny wirtualne klastra systemu Windows w zestawie dostępności przy użyciu udostępnionego dysku platformy Azure bez PPG, usługa PPG zapewni bliską fizyczną bliskość udostępnionych dysków platformy Azure i maszyn wirtualnych klastra, a tym samym osiągnięcie mniejszych opóźnień między maszynami wirtualnymi i warstwą magazynowania.    

Aby uzyskać więcej informacji na temat ograniczeń dotyczących dysku udostępnionego platformy Azure, przejrzyj dokładnie sekcję [ograniczenia](../../linux/disks-shared.md#limitations) w dokumentacji dysku udostępnionego platformy Azure.  

> [!IMPORTANT]
> Podczas wdrażania klastra trybu failover systemu Windows z systemem SAP ASCS/SCS przy użyciu udostępnionego dysku platformy Azure należy pamiętać, że wdrożenie będzie działać z jednym udostępnionym dyskiem w jednym klastrze magazynu. Będzie to miało wpływ na wystąpienie oprogramowania SAP ASCS/SCS w przypadku problemów z klastrem magazynu, w którym wdrożono dysk udostępniony platformy Azure.  

> [!IMPORTANT]
> Instalator musi spełniać następujące warunki:
> * Każdy identyfikator SID systemu zarządzania bazami danych (DBMS) musi mieć własny dedykowany klaster usługi WSFC.  
> * Serwery aplikacji SAP należące do jednego identyfikatora SID systemu SAP muszą mieć własne dedykowane maszyny wirtualne.  
> * Mieszany serwer replikacji z kolejki 1 i kolejki serwer replikacji 2 w tym samym klastrze nie jest obsługiwany.  


## <a name="supported-os-versions"></a>Obsługiwane wersje systemu operacyjnego

Obsługiwane są zarówno systemy Windows Server 2016, jak i Windows Server 2019 (Użyj najnowszych obrazów centrum danych).

Zdecydowanie zalecamy korzystanie z **systemu Windows Server 2019 Datacenter**w taki sposób, jak:
- Usługa klastra pracy awaryjnej systemu Windows 2019 jest uwzględniana przez platformę Azure
- Dodano integrację i świadomość konserwacji hosta platformy Azure oraz udoskonalone środowisko monitorowania dla zdarzeń planu platformy Azure.
- Możliwe jest użycie rozproszonej nazwy sieciowej (jest to opcja domyślna). W związku z tym nie ma potrzeby posiadania dedykowanego adresu IP dla nazwy sieci klastra. Ponadto nie ma potrzeby konfigurowania tego adresu IP w wewnętrznej Load Balancer platformy Azure. 

## <a name="architecture"></a>Architektura

Zarówno serwer replikacji z kolejki 1 (ERS1), jak i kolejki serwer replikacji 2 (ERS2) są obsługiwane w konfiguracji z obsługą jednego identyfikatora SID.  Kombinacja ERS1 i ERS2 nie jest obsługiwana w tym samym klastrze. 

1. Pierwszy przykład przedstawia dwa identyfikatory SID SAP, zarówno z architekturą ERS1, gdzie:

   - Program SAP SID1 został wdrożony na dysku udostępnionym z ERS1. Wystąpienie wykres WYWOŁUJĄCYCH jest zainstalowane na hoście lokalnym i na dysku lokalnym.
     System SAP SID1 ma własny adres IP (wirtualny) (SID1 (A) SCS IP1) skonfigurowany w ramach wewnętrznego modułu równoważenia obciążenia platformy Azure.

   - Program SAP SID2 został wdrożony na dysku udostępnionym z ERS1. Wystąpienie wykres WYWOŁUJĄCYCH jest zainstalowane na hoście lokalnym i na dysku lokalnym.
     System SAP SID2 ma własny adres IP (wirtualny) (SID2 (A) SCS IP2), który jest konfigurowany również w wewnętrznej usłudze równoważenia obciążenia platformy Azure.

![Wystąpienie programu SAP ASCS/SCS o wysokiej dostępności — dwa wystąpienia z konfiguracją ERS1][sap-ha-guide-figure-6007]

2. Drugi przykład przedstawia dwa identyfikatory SID SAP, zarówno z architekturą ERS2, gdzie: 

   - Rozwiązanie SAP SID1 z ERS2, jest również klastrowane i wdrożone na dysku lokalnym.  
     System SAP SID1 ma własny adres IP (wirtualny) (SID1 (A) SCS IP1) skonfigurowany w ramach wewnętrznego modułu równoważenia obciążenia platformy Azure.
     Program SAP ERS2 używany przez system SAP SID1 ma własny (wirtualny) adres IP (SID1 ERS2 IP2) skonfigurowany w ramach wewnętrznego modułu równoważenia obciążenia platformy Azure.

   - Rozwiązanie SAP SID2 z ERS2, jest również klastrowane i wdrożone na dysku lokalnym.  
     System SAP SID2 ma własny adres IP (wirtualny) (SID2 (A) SCS IP3) skonfigurowany w ramach wewnętrznego modułu równoważenia obciążenia platformy Azure.
     Program SAP ERS2 używany przez system SAP SID2 ma własny (wirtualny) adres IP (SID2 ERS2 IP4) skonfigurowany w ramach wewnętrznego modułu równoważenia obciążenia platformy Azure.

   Oto łącznie cztery wirtualne adresy IP:  
   - SID1 (A) SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A) SCS IP3
   - SID2 ERS2 IP4

![Wystąpienie programu SAP ASCS/SCS o wysokiej dostępności — dwa wystąpienia z konfiguracją ERS1 i ERS2][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Przygotowanie infrastruktury

Oprócz **istniejącego** wystąpienia programu SAP **PR1** ASCS/SCS zostanie zainstalowany nowy **PR2**identyfikatora SID SAP.  

### <a name="host-names-and-ip-addresses"></a>Nazwy hostów i adresy IP

| Rola nazwy hosta | Nazwa hosta | Statyczny adres IP | Zestaw dostępności | Grupa położenia zbliżeniowe |
| --- | --- | --- |---| ---|
| pierwszy węzeł klastra ASCS/SCS klaster |PR1-ASCS-10 |10.0.0.4 |PR1-ASCS-avset |PR1PPG |
| klaster ASCS/SCS drugiego węzła klastra |PR1-ASCS-11 |10.0.0.5 |PR1-ASCS-avset |PR1PPG |
| Nazwa sieci klastra | pr1clust |10.0.0.42 (**tylko** w przypadku klastra win 2016) | nie dotyczy | nie dotyczy |
| **SID1** Nazwa sieci klastra ASCS | pr1-ascscl |10.0.0.43 | nie dotyczy | nie dotyczy |
| **SID1** Nazwa sieci klastra wykres WYWOŁUJĄCYCH (**tylko** dla ERS2) | pr1-erscl |10.0.0.44 | nie dotyczy | nie dotyczy |
| **SID2** Nazwa sieci klastra ASCS | pr2-ascscl |10.0.0.45 | nie dotyczy | nie dotyczy |
| **SID2** Nazwa sieci klastra wykres WYWOŁUJĄCYCH (**tylko** dla ERS2) | pr1-erscl |10.0.0.46 | nie dotyczy | nie dotyczy |

### <a name="create-azure-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia platformy Azure

SAP ASCS, SAP SCS i New SAP ERS2, użyj wirtualnej nazwy hosta i wirtualnych adresów IP. W przypadku korzystania z wirtualnego adresu IP na platformie Azure wymagany jest [moduł równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md) . Zdecydowanie zalecamy użycie usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

Należy dodać konfigurację do istniejącego modułu równoważenia obciążenia dla drugiego wystąpienia ASCS SID protokołu SAP/SCS/wykres WYWOŁUJĄCYCH, **PR2**. Konfiguracja pierwszego **PR1a** identyfikatora SID SAP powinna być już na miejscu.  

**Z SCS PR2 [numer wystąpienia 02]**
- Konfiguracja frontonu
    - Statyczny adres IP ASCS/SCS **10.0.0.45**
- Konfiguracja zaplecza  
    Już w miejscu — maszyny wirtualne zostały już dodane do puli zaplecza, podczas gdy Konfigurowanie dla usługi SAP SID **PR1**
- Port sondy
    - Port 620**Nr** [**62002**] pozostaw opcję domyślną dla protokołu (TCP), interwał (5), próg złej kondycji (2)
- Reguły równoważenia obciążenia
    - W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję Porty HA
    - W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
        - 32**Nr** TCP [**3202**]
        - 36**Nr** TCP [**3602**]
        - 39**Nr** TCP [**3902**]
        - 81**Nr** TCP [**8102**]
        - 5**Nr**13 TCP [**50213**]
        - 5**Nr**14 TCP [**50214**]
        - 5**Nr**16 TCP [**50216**]
        - Skojarz z adresem IP frontonu **PR2** ASCS, sondą kondycji i istniejącą pulą zaplecza.  

    - Upewnij się, że limit czasu bezczynności (w minutach) jest ustawiony na wartość maksymalną 30 i że jest włączony swobodny adres IP (bezpośredni zwrot serwera).

**ERS2 PR2 [numer wystąpienia 12]** 

W przypadku, gdy w kolejce jest również klastrowane serwer replikacji 2 (ERS2), ERS2 wirtualny adres IP musi być również skonfigurowany na platformie Azure ILB oprócz powyżej protokołu SAP ASCS/SCS IP. Ta sekcja ma zastosowanie **tylko w przypadku**, gdy używana jest architektura serwera replikacji 2.  
- Nowa konfiguracja frontonu
    - Statyczny adres IP ERS2 SAP **10.0.0.46**

- Konfiguracja zaplecza  
  Maszyny wirtualne zostały już dodane do puli zaplecza ILB.  

- Nowy port sondy
    - Port 621**Nr**  [**62112**] pozostaw opcję domyślną dla protokołu (TCP), interwał (5), próg złej kondycji (2)

- Nowe reguły równoważenia obciążenia
    - W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję Porty HA
    - W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
        - 32**Nr** TCP [**3212**]
        - 33**Nr** TCP [**3312**]
        - 5**Nr**13 TCP [**51212**]
        - 5**Nr**14 TCP [**51212**]
        - 5**Nr**16 TCP [**51212**]
        - Skojarz z adresem IP frontonu **PR2** ERS2, sondą kondycji i istniejącą pulą zaplecza.  

    - Upewnij się, że limit czasu bezczynności (w minutach) jest ustawiony na wartość Max, np. 30, i że jest włączony swobodny adres IP (bezpośredni zwrot serwera).


### <a name="create-and-attach-second-azure-shared-disk"></a>Tworzenie i dołączanie drugiego dysku udostępnionego platformy Azure

Uruchom to polecenie na jednym z węzłów klastra. Konieczne będzie dostosowanie wartości dla grupy zasobów, regionu platformy Azure, SAPSID i tak dalej.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formatowanie udostępnionego dysku przy użyciu programu PowerShell
1. Pobierz numer dysku. Uruchom polecenia programu PowerShell na jednym z węzłów klastra:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Sformatuj dysk. W tym przykładzie jest to dysk numer 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Sprawdź, czy dysk jest teraz widoczny jako dysk klastrowy.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Zarejestruj dysk w klastrze.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Utwórz nazwę wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS

1. Utwórz wpis DNS dla nazwy hosta wirtualnego dla nowego wystąpienia SAP ASCS/SCS w Menedżerze DNS systemu Windows.  
   Adres IP przypisany do nazwy hosta wirtualnego w systemie DNS musi być taki sam jak adres IP przypisany w Azure Load Balancer.  

   ![_Define wpis DNS dla nazwy wirtualnej i adresu IP klastra SAP ASCS/SCS][sap-ha-guide-figure-6009]
 
   _Zdefiniuj wpis DNS dla nazwy wirtualnej i adresu IP klastra SAP ASCS/SCS_

2. W przypadku korzystania z programu SAP w kolejce serwera replikacji 2, który jest również wystąpieniem klastrowanym, należy zarezerwować również w systemie DNS nazwę hosta wirtualnego dla ERS2. 
   Adres IP przypisany do nazwy hosta wirtualnego ERS2 w systemie DNS musi być taki sam jak adres IP przypisany w Azure Load Balancer.  

   ![_Define wpis DNS dla nazwy wirtualnej i adresu IP klastra SAP ERS2][sap-ha-guide-figure-6010]
 
   _Zdefiniuj wpis DNS dla nazwy wirtualnej i adresu IP klastra SAP ERS2_

3. Aby zdefiniować adres IP, który jest przypisany do nazwy hosta wirtualnego, wybierz pozycję **domena Menedżera DNS**  >  **Domain**.

   ![Nowa nazwa wirtualna i adres IP dla konfiguracji klastra SAP ASCS/SCS i ERS2][sap-ha-guide-figure-6011]

   _Nowa nazwa wirtualna i adres TCP/IP dla konfiguracji klastra SAP ASCS/SCS i ERS2_

## <a name="sap-installation"></a>Instalacja programu SAP 

### <a name="install-the-sap-first-cluster-node"></a>Instalowanie pierwszego węzła klastra SAP

Postępuj zgodnie z procedurą instalacji opisaną w oprogramowaniu SAP. Upewnij się, że opcja Rozpocznij instalację "pierwszy węzeł klastra" i wybierz opcję "dysk udostępniony klastra" jako opcji konfiguracji.  
Wybierz nowo utworzony dysk udostępniony.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Modyfikowanie profilu SAP wystąpienia ASCS/SCS

Jeśli używasz kolejki serwera replikacji 1, Dodaj parametr profilu SAP `enque/encni/set_so_keepalive` zgodnie z poniższym opisem. Parametr profil uniemożliwia zamknięcie połączeń między procesami roboczymi SAP a serwerem z kolejki, gdy są one w stanie bezczynności przez zbyt długi czas. Parametr SAP nie jest wymagany dla ERS2. 

1. Dodaj ten parametr profilu do profilu wystąpienia SAP ASCS/SCS, jeśli jest używany ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   W przypadku programów ERS1 i ERS2 upewnij się, że `keepalive` parametry systemu operacyjnego zostały ustawione zgodnie z opisem w temacie SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Aby zastosować zmiany parametru profilu SAP, należy ponownie uruchomić wystąpienie SAP ASCS/SCS.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Skonfiguruj port sondy w zasobie klastra

Korzystając z funkcji sondowania wewnętrznego modułu równoważenia obciążenia, można sprawić, aby cała Konfiguracja klastra działała z Azure Load Balancer. Wewnętrzny moduł równoważenia obciążenia platformy Azure zwykle dystrybuuje przychodzące obciążenie równo między uczestniczącymi maszynami wirtualnymi.

Nie będzie to jednak działało w niektórych konfiguracjach klastra, ponieważ aktywne jest tylko jedno wystąpienie. Drugie wystąpienie jest pasywne i nie może akceptować żadnego obciążenia. Funkcja sondowania pomaga, gdy moduł równoważenia obciążenia wewnętrznego platformy Azure wykryje, które wystąpienie jest aktywne i ma tylko aktywne wystąpienie.  

> [!IMPORTANT]
> W tej przykładowej konfiguracji **ProbePort** jest ustawiony na 620**Nr**. W przypadku wystąpienia SAP ASCS o numerze **02** to 620**02**.
> Należy dostosować konfigurację tak, aby odpowiadała numerom wystąpień SAP i identyfikatorowi SID SAP.

Aby dodać port sondy, Uruchom ten moduł programu PowerShell na jednej z maszyn wirtualnych klastra:

- W przypadku wystąpienia SAP ASC/SCS z wystąpieniem o numerze **02** 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Jeśli używasz ERS2, z wystąpieniem o numerze **12**, które jest klastrowane. Nie ma potrzeby konfigurowania portu sondowania dla ERS1, ponieważ nie jest on klastrowany.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 Kod dla funkcji `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` będzie wyglądać następująco:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Kontynuuj instalację oprogramowania SAP

1. Zainstaluj wystąpienie bazy danych, postępując zgodnie z procesem opisanym w przewodniku instalacji SAP.  
2. Zainstaluj oprogramowanie SAP w drugim węźle klastra, wykonując kroki opisane w przewodniku instalacji SAP.  
3. Zainstaluj wystąpienie serwera podstawowego aplikacji SAP (PAS) na maszynie wirtualnej, która została wyoznaczona jako hostowanie oprogramowania.   
   Postępuj zgodnie z procesem opisanym w przewodniku instalacji SAP. Nie ma żadnych zależności na platformie Azure.
4. Zainstaluj dodatkowe serwery aplikacji SAP na maszynach wirtualnych, które są przeznaczone do hostowania wystąpień serwera aplikacji SAP.  
   Postępuj zgodnie z procesem opisanym w przewodniku instalacji SAP. Nie ma żadnych zależności na platformie Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testowanie trybu failover wystąpienia SAP ASCS/SCS
W przypadku testów w trybie failover założono, że SAP ASCS jest aktywny w węźle A.  

1. Sprawdź, czy system SAP może pomyślnie przełączyć się w tryb failover z węzła A do węzła B. W tym przykładzie test jest wykonywany dla SAPSID **PR2**.  
   Upewnij się, że każdy z SAPSID można pomyślnie przenieść do innego węzła klastra.   
   Wybierz jedną z tych opcji, aby zainicjować tryb failover \<SID\> grupy klastra SAP z węzła klastra a do węzła klastra B:
    - Menedżer klastra trybu failover  
    - Klaster trybu failover programu PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Uruchom ponownie węzeł klastra A w systemie operacyjnym gościa systemu Windows. Spowoduje to zainicjowanie automatycznej pracy awaryjnej \<SID\> grupy klastra SAP z węzła A do węzła B.  
3. Uruchom ponownie węzeł klastra A z Azure Portal. Spowoduje to zainicjowanie automatycznej pracy awaryjnej \<SID\> grupy klastra SAP z węzła A do węzła B.  
4. Uruchom ponownie węzeł klastra A za pomocą Azure PowerShell. Spowoduje to zainicjowanie automatycznej pracy awaryjnej \<SID\> grupy klastra SAP z węzła A do węzła B.

## <a name="next-steps"></a>Następne kroki

* [Przygotowanie infrastruktury platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]
* [Instalowanie oprogramowania SAP NetWeaver HA na klastrze trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md