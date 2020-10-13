---
title: Tworzenie konfiguracji wielosid SAP o wysokiej dostępności na platformie Azure
description: Dowiedz się, jak utworzyć konfigurację usługi SAP NetWeaver o wysokiej dostępności na maszynach wirtualnych z systemem Windows.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6945be2d9a5d7cd56556c7d89998b21efe26d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314083"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Tworzenie konfiguracji protokołu SAP NetWeaver o wysokiej dostępności

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

We wrześniu 2016 firma Microsoft udostępniła funkcję, w której można zarządzać wieloma wirtualnymi adresami IP przy użyciu wewnętrznego modułu równoważenia obciążenia platformy Azure. Ta funkcja już istnieje w zewnętrznym module równoważenia obciążenia platformy Azure.

Jeśli masz wdrożenie SAP, możesz użyć wewnętrznego modułu równoważenia obciążenia, aby utworzyć konfigurację klastra systemu Windows dla oprogramowania SAP ASCS/SCS, zgodnie z opisem w [przewodniku dotyczącym wysokiej dostępności SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide].

Ten artykuł koncentruje się na sposobie przenoszenia z jednej instalacji ASCS/SCS do konfiguracji protokołu SAP o wiele identyfikatorów SID, instalując dodatkowe wystąpienia klastrowane ASCS/SCS w istniejącym klastrze usługi Windows Server Failover Clustering (WSFC). Po zakończeniu tego procesu zostanie skonfigurowany klaster z obsługą protokołu SAP o wiele identyfikatorów SID.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Klaster usługi WSFC został już skonfigurowany, który jest używany dla jednego wystąpienia SAP ASCS/SCS, jak opisano w [przewodniku dla NETWEAVER SAP na maszynach wirtualnych z systemem Windows][sap-ha-guide] i jak pokazano na tym diagramie.

![Wystąpienie środowiska SAP ASCS/SCS o wysokiej dostępności][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Architektura docelowa

Celem jest zainstalowanie wielu wystąpień klastrowanych platformy SAP ABAP ASCS lub SAP Java SCS w tym samym klastrze usługi WSFC, jak pokazano poniżej:

![Wiele wystąpień klastra SAP ASCS/SCS na platformie Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Istnieje limit liczby prywatnych adresów IP frontonu dla każdego wewnętrznego modułu równoważenia obciążenia platformy Azure.
>
>Maksymalna liczba wystąpień SAP ASCS/SCS w jednym klastrze usługi WSFC jest równa maksymalnej liczbie prywatnych adresów IP frontonu dla każdego wewnętrznego modułu równoważenia obciążenia platformy Azure.
>

Aby uzyskać więcej informacji na temat limitów równoważenia obciążenia, zobacz "prywatny adres IP frontonu na moduł równoważenia obciążenia" w obszarze [limity sieciowe: Azure Resource Manager][networking-limits-azure-resource-manager].

Pełna krajobraz z dwoma systemami SAP wysokiej dostępności będzie wyglądać następująco:

![Konfiguracja wielościeżkowego protokołu SAP o wysokiej dostępności przy użyciu dwóch identyfikatorów SID systemu SAP][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Instalator musi spełniać następujące warunki:
> - Wystąpienia SAP ASCS/SCS muszą współdzielić ten sam klaster usługi WSFC.
> - Każdy identyfikator SID systemu DBMS musi mieć własny dedykowany klaster usługi WSFC.
> - Serwery aplikacji SAP należące do jednego identyfikatora SID systemu SAP muszą mieć własne dedykowane maszyny wirtualne.


## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury
Aby przygotować infrastrukturę, można zainstalować dodatkowe wystąpienie SAP ASCS/SCS z następującymi parametrami:

| Nazwa parametru | Wartość |
| --- | --- |
| IDENTYFIKATOR SID SAP ASCS/SCS |PR1-lb-ASCS |
| Wewnętrzny moduł równoważenia obciążenia SAP DBMS | PR5 |
| Nazwa hosta wirtualnego SAP | PR5 — SAP-CL |
| Adres IP hosta wirtualnego SAP ASCS/SCS (dodatkowy adres IP modułu równoważenia obciążenia platformy Azure) | 10.0.0.50 |
| Numer wystąpienia SAP ASCS/SCS | 50 |
| Port sondy ILB dla dodatkowego wystąpienia SAP ASCS/SCS | 62350 |

> [!NOTE]
> W przypadku wystąpień klastra SAP ASCS/SCS każdy adres IP wymaga unikatowego portu sondowania. Na przykład jeśli jeden adres IP w wewnętrznym module równoważenia obciążenia platformy Azure używa portu sondy 62300, żaden inny adres IP w tym module równoważenia obciążenia nie może używać portu sondy 62300.
>
>W naszym celu, ponieważ port sondy 62300 jest już zarezerwowany, używany jest port sondy 62350.

Można zainstalować dodatkowe wystąpienia oprogramowania SAP ASCS/SCS w istniejącym klastrze usługi WSFC z dwoma węzłami:

| Rola maszyny wirtualnej | Nazwa hosta maszyny wirtualnej | Statyczny adres IP |
| --- | --- | --- |
| pierwszy węzeł klastra dla wystąpienia ASCS/SCS |PR1-ASCS-0 |10.0.0.10 |
| drugi węzeł klastra dla wystąpienia ASCS/SCS |PR1-ASCS-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Utwórz nazwę wirtualnego hosta dla klastrowanego wystąpienia SAP ASCS/SCS na serwerze DNS

Można utworzyć wpis DNS dla nazwy hosta wirtualnego wystąpienia ASCS/SCS, korzystając z następujących parametrów:

| Nowa nazwa hosta wirtualnego SAP ASCS/SCS | Skojarzony adres IP |
| --- | --- |
|PR5 — SAP-CL |10.0.0.50 |

Nowa nazwa hosta i adres IP są wyświetlane w Menedżerze DNS, jak pokazano na poniższym zrzucie ekranu:

![Lista Menedżera DNS wyróżniania zdefiniowanego wpisu DNS dla nowej nazwy wirtualnej klastra SAP ASCS/SCS i adresu TCP/IP][sap-ha-guide-figure-6004]

Procedura tworzenia wpisu DNS jest również szczegółowo opisana w [przewodniku głównym dla programu SAP NetWeaver o wysokiej dostępności na maszynach wirtualnych z systemem Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nowy adres IP przypisany do nazwy hosta wirtualnego dodatkowego wystąpienia ASCS/SCS musi być taki sam jak nowy adres IP przypisany do usługi SAP Azure load module.
>
>W naszym scenariuszu adres IP to 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Dodawanie adresu IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure za pomocą programu PowerShell

Aby utworzyć więcej niż jedno wystąpienie SAP ASCS/SCS w tym samym klastrze usługi WSFC, użyj programu PowerShell, aby dodać adres IP do istniejącego wewnętrznego modułu równoważenia obciążenia platformy Azure. Każdy adres IP musi mieć własne reguły równoważenia obciążenia, port sondy, pulę adresów IP frontonu i pulę zaplecza.

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. Zaktualizuj zmienne programu PowerShell dla danego środowiska. Skrypt utworzy wszystkie zbędne reguły równoważenia obciążenia dla wszystkich portów SAP ASCS/SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po uruchomieniu skryptu wyniki są wyświetlane w Azure Portal, jak pokazano na poniższym zrzucie ekranu:

![Nowa pula adresów IP frontonu w Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Dodawanie dysków do maszyn klastra i Konfigurowanie dysku udziału klastra oprogramowanie SIOS

Należy dodać nowy dysk udziału klastra dla każdego dodatkowego wystąpienia oprogramowania SAP ASCS/SCS. W przypadku systemu Windows Server 2012 R2 dysk udziału klastra usługi WSFC jest obecnie używany do rozwiązania oprogramowania oprogramowanie SIOS DataKeeper.

Wykonaj następujące czynności:
1. Dodaj dodatkowy dysk lub dyski o takim samym rozmiarze (co trzeba umieścić) w każdym z węzłów klastra, a następnie sformatuj je.
2. Skonfiguruj replikację magazynu za pomocą oprogramowanie SIOS DataKeeper.

W tej procedurze przyjęto założenie, że już zainstalowano oprogramowanie SIOS DataKeeper na maszynach klastra usługi WSFC. Jeśli zainstalowano go, musisz teraz skonfigurować replikację między maszynami. Ten proces jest szczegółowo opisany w [przewodniku głównym dotyczącym wysokiej dostępności oprogramowania SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide-8.12.3.3].  

![Funkcja dublowania synchronicznego dla nowego dysku udziału SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Wdrażanie maszyn wirtualnych dla serwerów aplikacji SAP i klastra DBMS

Aby ukończyć Przygotowanie infrastruktury dla drugiego systemu SAP, wykonaj następujące czynności:

1. Wdróż dedykowane maszyny wirtualne dla serwerów aplikacji SAP i umieść je w własnej dedykowanej grupie dostępności.
2. Wdróż dedykowane maszyny wirtualne dla klastra DBMS i umieść je w własnej dedykowanej grupie dostępności.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalowanie drugiego systemu SAP SID2 NetWeaver

Pełny proces instalowania drugiego systemu SAP SID2 został opisany w [przewodniku głównym dla maszyn wirtualnych z systemem Windows o wysokiej dostępności][sap-ha-guide-9].

Procedura wysokiego poziomu jest następująca:

1. [Zainstaluj pierwszy węzeł SAP][sap-ha-guide-9.1.2].  
 W tym kroku instalujesz oprogramowanie SAP z wystąpieniem ASCS o wysokiej dostępności w **istniejącym węźle klastra usługi WSFC 1**.

2. [Zmodyfikuj profil SAP wystąpienia ASCS/SCS][sap-ha-guide-9.1.3].

3. [Skonfiguruj port sondy][sap-ha-guide-9.1.4].  
 W tym kroku jest konfigurowany port sondy SAP-SID2-IP dla klastra SAP przy użyciu programu PowerShell. Tę konfigurację należy wykonać na jednym z węzłów klastra SAP ASCS/SCS.

4. [Zainstaluj wystąpienie bazy danych] [SAP-ha-Guide-9,2].  
 W tym kroku jest instalowany system DBMS w dedykowanym klastrze usługi WSFC.

5. [Zainstaluj drugi węzeł klastra] [SAP-ha-Guide-9,3].  
 W tym kroku instalujesz oprogramowanie SAP z wystąpieniem ASCS o wysokiej dostępności w istniejącym węźle klastra usługi WSFC 2.

6. Otwórz porty zapory systemu Windows dla wystąpienia SAP ASCS/SCS i ProbePort.  
 Na obu węzłach klastra, które są używane dla wystąpień SAP ASCS/SCS, otwierane są wszystkie porty zapory systemu Windows, które są używane przez SAP ASCS/SCS. Te porty są wymienione w [przewodniku dotyczącego oprogramowania SAP NetWeaver o wysokiej dostępności na maszynach wirtualnych z systemem Windows][sap-ha-guide-8.8].  
 Otwórz również port sondy wewnętrznego modułu równoważenia obciążenia platformy Azure, który jest 62350 w naszym scenariuszu.

7. [Zmień typ uruchomienia wystąpienia usługi SAP wykres wywołujących systemu Windows][sap-ha-guide-9.4].

8. [Zainstaluj podstawowy serwer aplikacji SAP][sap-ha-guide-9.5] na nowej dedykowanej maszynie wirtualnej.

9. [Zainstaluj dodatkowy serwer aplikacji SAP][sap-ha-guide-9.6] na nowej dedykowanej maszynie wirtualnej.

10. [Przetestuj tryb failover wystąpienia SAP ASCS/SCS i oprogramowanie SIOS replikację][sap-ha-guide-10].

## <a name="next-steps"></a>Następne kroki

- [Limity sieci: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Wiele adresów VIP dla Azure Load Balancer][load-balancer-multivip-overview]
- [Przewodnik dotyczący wysokiej dostępności oprogramowania SAP NetWeaver na maszynach wirtualnych z systemem Windows][sap-ha-guide]
