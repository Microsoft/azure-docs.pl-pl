---
title: Konfigurowanie odbiorników grup dostępności i modułu równoważenia obciążenia (PowerShell)
description: Skonfiguruj odbiorniki grup dostępności w modelu Azure Resource Manager przy użyciu wewnętrznego modułu równoważenia obciążenia z co najmniej jednym adresem IP.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 9337d1c2767923e6dc7c6b267e0c180b460a116e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359425"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Skonfiguruj co najmniej jeden odbiornik grupy dostępności — Menedżer zasobów

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym dokumencie przedstawiono sposób użycia programu PowerShell w celu wykonania jednego z następujących zadań:
- Tworzenie modułu równoważenia obciążenia
- Dodaj adresy IP do istniejącego modułu równoważenia obciążenia dla SQL Server grup dostępności.

Odbiornik grupy dostępności to nazwa sieci wirtualnej, z którą łączą się klienci w celu uzyskania dostępu do bazy danych. W usłudze Azure Virtual Machines moduł równoważenia obciążenia przechowuje adres IP odbiornika. Moduł równoważenia obciążenia kieruje ruch do wystąpienia SQL Server, które nasłuchuje na porcie sondy. Zwykle Grupa dostępności używa wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia platformy Azure może obsługiwać jeden lub wiele adresów IP. Każdy adres IP używa określonego portu sondowania. 

Możliwość przypisania wielu adresów IP do wewnętrznego modułu równoważenia obciążenia jest nowa na platformie Azure i jest dostępna tylko w modelu Menedżer zasobów. Aby wykonać to zadanie, należy mieć SQL Server grupę dostępności wdrożoną na platformie Azure Virtual Machines w modelu Menedżer zasobów. Obie SQL Server maszyny wirtualne muszą należeć do tego samego zestawu dostępności. Możesz użyć [szablonu Microsoft](./availability-group-quickstart-template-configure.md) , aby automatycznie utworzyć grupę dostępności w Azure Resource Manager. Ten szablon automatycznie tworzy grupę dostępności wraz z wewnętrznym modułem równoważenia obciążenia. Jeśli wolisz, możesz [ręcznie skonfigurować grupę dostępności zawsze włączona](availability-group-manually-configure-tutorial.md).

Aby wykonać kroki opisane w tym artykule, Twoje grupy dostępności muszą być już skonfigurowane.  

Tematy pokrewne obejmują:

* [Konfigurowanie Zawsze włączone grupy dostępności na maszynie wirtualnej platformy Azure (graficzny interfejs użytkownika)](availability-group-manually-configure-tutorial.md)   
* [Konfigurowanie połączenia sieci wirtualnych przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Weryfikuj wersję programu PowerShell

Przykłady w tym artykule są testowane przy użyciu modułu Azure PowerShell w wersji 5.4.1.

Sprawdź, czy moduł programu PowerShell to 5.4.1 lub nowszy.

Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurowanie zapory systemu Windows

Skonfiguruj zaporę systemu Windows tak, aby zezwalała na dostęp SQL Server. Reguły zapory zezwalają na połączenia TCP z portami używanymi przez wystąpienie SQL Server i sondy odbiornika. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie zapory systemu Windows pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1). Utwórz regułę ruchu przychodzącego dla portu SQL Server i dla portu sondowania.

W przypadku ograniczania dostępu za pomocą grupy zabezpieczeń sieci platformy Azure upewnij się, że reguły zezwalania zawierają SQL Server adresy IP maszyny wirtualnej, a moduł równoważenia obciążenia — adresy IP dla odbiornika AG i podstawowy adres IP klastra, jeśli ma zastosowanie.

## <a name="determine-the-load-balancer-sku-required"></a>Określ wymaganą jednostkę SKU modułu równoważenia obciążenia

[Moduł równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-overview.md) jest dostępny w dwóch jednostkach SKU: Basic & Standard. Zalecany jest standardowy moduł równoważenia obciążenia. Jeśli maszyny wirtualne znajdują się w zestawie dostępności, dozwolony jest podstawowy moduł równoważenia obciążenia. Jeśli maszyny wirtualne znajdują się w strefie dostępności, wymagany jest standardowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia w warstwie Standardowa wymaga, aby wszystkie adresy IP maszyn wirtualnych używały standardowych adresów IP.

Bieżący [szablon firmy Microsoft](./availability-group-quickstart-template-configure.md) dla grupy dostępności używa podstawowego modułu równoważenia obciążenia z podstawowymi adresami IP.

   > [!NOTE]
   > Jeśli używasz standardowego modułu równoważenia obciążenia i usługi Azure Storage dla monitora chmury, musisz skonfigurować [punkt końcowy usługi](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) . 
   > 

W przykładach w tym artykule określono moduł równoważenia obciążenia w warstwie Standardowa. W przykładach skrypt zawiera `-sku Standard` .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Aby utworzyć podstawowy moduł równoważenia obciążenia, Usuń `-sku Standard` z wiersza, który tworzy moduł równoważenia obciążenia. Na przykład:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Przykładowy skrypt: Tworzenie wewnętrznego modułu równoważenia obciążenia za pomocą programu PowerShell

> [!NOTE]
> Jeśli grupa dostępności została utworzona przy użyciu [szablonu Microsoft](./availability-group-quickstart-template-configure.md), wewnętrzna usługa równoważenia obciążenia została już utworzona.

Poniższy skrypt programu PowerShell tworzy wewnętrzny moduł równoważenia obciążenia, konfiguruje reguły równoważenia obciążenia i ustawia adres IP dla modułu równoważenia obciążenia. Aby uruchomić skrypt, Otwórz Windows PowerShell ISE, a następnie wklej skrypt w okienku Skrypt. Użyj polecenia `Connect-AzAccount` , aby zalogować się do programu PowerShell. Jeśli masz wiele subskrypcji platformy Azure, użyj, `Select-AzSubscription` Aby ustawić subskrypcję. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Przykładowy skrypt: Dodawanie adresu IP do istniejącego modułu równoważenia obciążenia za pomocą programu PowerShell

Aby użyć więcej niż jednej grupy dostępności, należy dodać dodatkowy adres IP do modułu równoważenia obciążenia. Każdy adres IP wymaga własnej reguły równoważenia obciążenia, portu sondowania i portu frontonu.

Port frontonu to port używany przez aplikacje do łączenia się z wystąpieniem SQL Server. Adresy IP dla różnych grup dostępności mogą korzystać z tego samego portu frontonu.

> [!NOTE]
> W przypadku grup dostępności SQL Server każdy adres IP wymaga określonego portu sondowania. Na przykład jeśli jeden adres IP w module równoważenia obciążenia używa portu sondy 59999, żadne inne adresy IP w tym module równoważenia obciążenia nie mogą korzystać z portu sondy 59999.

* Aby uzyskać informacje na temat limitów modułu równoważenia obciążenia, zobacz **prywatny adres IP frontonu dla modułu równoważenia obciążenia** w obszarze [limity sieciowe — Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Aby uzyskać informacje na temat limitów grup dostępności, zobacz [ograniczenia (grupy dostępności)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG).

Poniższy skrypt dodaje nowy adres IP do istniejącego modułu równoważenia obciążenia. ILB używa portu odbiornika dla portu frontonu równoważenia obciążenia. Port ten może być portem, na którym nasłuchuje SQL Server. Dla domyślnych wystąpień SQL Server Port to 1433. Reguła równoważenia obciążenia dla grupy dostępności wymaga swobodnego adresu IP (bezpośredni zwrot serwera), dlatego port zaplecza jest taki sam jak port frontonu. Aktualizowanie zmiennych dla środowiska. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurowanie odbiornika

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Ustaw port odbiornika w SQL Server Management Studio

1. Uruchom SQL Server Management Studio i Połącz się z repliką podstawową.

1. Przejdź do funkcji AlwaysOn grup dostępności dla grup dostępności **o wysokiej dostępności**  >    >  . 

1. Powinna zostać wyświetlona nazwa odbiornika utworzona w Menedżer klastra trybu failover. Kliknij prawym przyciskiem myszy nazwę odbiornika i wybierz pozycję **Właściwości**.

1. W polu **port** Określ numer portu dla odbiornika grupy dostępności przy użyciu użytej wcześniej $EndpointPort (1433 była domyślna), a następnie wybierz **przycisk OK**.

## <a name="test-the-connection-to-the-listener"></a>Testowanie połączenia z odbiornikiem

Aby przetestować połączenie:

1. Użyj Remote Desktop Protocol (RDP), aby połączyć się z SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest to replika. Może to być inne SQL Server w klastrze.

1. Przetestuj połączenie przy użyciu narzędzia **sqlcmd** . Na przykład poniższy skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pomocą odbiornika z uwierzytelnianiem systemu Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Jeśli odbiornik używa portu innego niż domyślny port (1433), określ port w parametrach połączenia. Na przykład następujące polecenie sqlcmd nawiązuje połączenie z odbiornikiem w porcie 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Połączenie SQLCMD jest automatycznie nawiązywane z niezależnym wystąpieniem SQL Server hostuje replikę podstawową. 

> [!NOTE]
> Upewnij się, że określony port jest otwarty na zaporze obu serwerów SQL. Oba serwery wymagają reguły ruchu przychodzącego dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub Edytowanie reguły zapory](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia

Zwróć uwagę na następujące wytyczne dotyczące odbiornika grupy dostępności na platformie Azure przy użyciu wewnętrznego modułu równoważenia obciążenia:

* W przypadku wewnętrznego modułu równoważenia obciążenia dostęp do odbiornika można uzyskać tylko w ramach tej samej sieci wirtualnej.

* W przypadku ograniczania dostępu za pomocą grupy zabezpieczeń sieci platformy Azure upewnij się, że reguły zezwalania obejmują:
  - Zaplecze SQL Server adresy IP maszyny wirtualnej
  - Przestawne adresy IP modułu równoważenia obciążenia dla odbiornika AG
  - Podstawowy adres IP klastra, jeśli ma zastosowanie.

* Utwórz punkt końcowy usługi przy użyciu standardowego modułu równoważenia obciążenia z usługą Azure Storage dla monitora chmury. Aby uzyskać więcej informacji, zobacz [udzielanie dostępu z sieci wirtualnej](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Więcej informacji

Aby uzyskać więcej informacji, zobacz [Ręczne konfigurowanie zawsze włączonych grup dostępności na maszynie wirtualnej platformy Azure](availability-group-manually-configure-tutorial.md).

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell

Użyj następujących poleceń cmdlet programu PowerShell, aby utworzyć wewnętrzny moduł równoważenia obciążenia dla Virtual Machines platformy Azure.

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) tworzy moduł równoważenia obciążenia. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) tworzy konfigurację adresu IP frontonu dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) tworzy konfigurację reguły dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) tworzy konfigurację puli adresów zaplecza dla modułu równoważenia obciążenia. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) tworzy konfigurację sondy dla modułu równoważenia obciążenia.
* Wartość [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) powoduje usunięcie modułu równoważenia obciążenia z grupy zasobów platformy Azure.