---
title: 'Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia — Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia modułu równoważenia obciążenia przy użyciu Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 35dc088909522494d6c1cf4c94f9342c95fda59a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698492"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure PowerShell

Rozpocznij pracę z Azure Load Balancer przy użyciu Azure PowerShell, aby utworzyć publiczny moduł równoważenia obciążenia i trzy maszyny wirtualne.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów przy użyciu elementu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* O nazwie **CreatePubLBQS-RG**.
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="create-a-public-ip-address-in-the-standard-sku"></a>Utwórz publiczny adres IP w standardowej jednostce SKU

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myPublicIP**.
* W **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'


$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Aby utworzyć strefowy publiczny adres IP w strefie 1, użyj następującego polecenia:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'


$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -Zone 1
```

## <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu elementu [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Nazwa **frontonu**.
* Dołączono do **myPublicIP** publicznego adresu IP.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* O nazwie **myBackEndPool**.
* W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą elementu [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą elementu [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.
* Włącz Resetowanie protokołu TCP.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'


## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```

### <a name="create-load-balancer-resource"></a>Utwórz zasób modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą usługi [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* O nazwie **myLoadBalancer**
* W **wschodnim regionie**.
* W grupie zasobów **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-standard-sku"></a>Konfigurowanie sieci wirtualnej w standardowej jednostce SKU

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu [nowego AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* O nazwie **myVNet**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Podsieć o nazwie **myBackendSubnet**.
* Sieć wirtualna **10.0.0.0/16**.
* Podsieć **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* O nazwie **myNSGRuleHTTP**.
* Opis **zezwalania na użycie protokołu HTTP**.
* Dostęp do programu **Zezwalaj**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Priorytet **2000**.
* Źródło **Internetu**.
* Zakres portów źródłowych **(*)**.
* Prefiks adresu docelowego **(*)**.
* **Port docelowy 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* O nazwie **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* Z regułami zabezpieczeń utworzonymi w poprzednich krokach przechowywanych w zmiennej.

```azurepowershell
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>Tworzenie interfejsów sieciowych

Utwórz trzy interfejsy sieciowe za pomocą [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>MW 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>MASZYNA WIRTUALNA 3

* O nazwie **myNicVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Utwórz maszyny wirtualne za pomocą:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W **Strefa 1**.
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W **strefa 2**.
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* O nazwie **myVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM3**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W **strefa 3**.
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>Utwórz konfigurację reguły ruchu wychodzącego
Reguły ruchu wychodzącego modułu równoważenia obciążenia Skonfiguruj translatora adresów sieciowych dla ruchu wychodzącego dla maszyn wirtualnych w puli zaplecza. 

Aby uzyskać więcej informacji na temat połączeń wychodzących, zobacz [połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address"></a>Tworzenie wychodzącego publicznego adresu IP

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myPublicIPOutbound**.
* W **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Aby utworzyć strefowy publiczny adres IP w strefie 1, użyj następującego polecenia:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>Utwórz konfigurację wychodzącego adresu IP frontonu

Utwórz nową konfigurację adresu IP frontonu przy użyciu elementu [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig):

* O nazwie **myFrontEndOutbound**.
* Skojarzone z publicznym adresem IP **myPublicIPOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'
$lbn = 'myLoadBalancer'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>Utwórz pulę wychodzącą

Utwórz nową pulę wychodzącą przy użyciu elementu [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig). 

Zastosuj pulę i adres IP frontonu do modułu równoważenia obciążenia przy użyciu [opcji Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* O nazwie **myBackEndPoolOutbound**.

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>Utwórz regułę wychodzącą i Zastosuj do modułu równoważenia obciążenia

Utwórz nową regułę ruchu wychodzącego dla puli zaplecza wychodzącego z [dodatkiem Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig). 

Zastosuj regułę do modułu równoważenia obciążenia przy użyciu [opcji Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* O nazwie **myOutboundRule**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.
* Skojarzone z **myFrontEndOutbound** frontonu.
* **Wszystkie** protokoły.
* Limit czasu bezczynności równy **15**.
* **10000** portów wychodzących.
* Skojarzona z pulą zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>Dodawanie maszyn wirtualnych do puli wychodzącej

Dodaj interfejsy sieciowe maszyny wirtualnej do puli wychodzącej modułu równoważenia obciążenia z [dodatkiem Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig):


#### <a name="vm1"></a>Maszyna wirtualna 1
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM1** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>Maszyna wirtualna 2
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM2** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* W puli adresów zaplecza **myBackEndPoolOutbound**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Skojarzona z interfejsem sieciowym **myNicVM3** i **ipconfig1**.
* Skojarzone z modułem równoważenia obciążenia **myLoadBalancer**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreatePubLBQS-rg'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

## <a name="create-a-public-ip-address-in-the-basic-sku"></a>Tworzenie publicznego adresu IP w podstawowej jednostce SKU

Aby uzyskać dostęp do aplikacji internetowej za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. 

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby:

* Utwórz strefę Standard nadmiarowy publiczny adres IP o nazwie **myPublicIP**.
* W **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu elementu [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Nazwa **frontonu**.
* Dołączono do **myPublicIP** publicznego adresu IP.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* O nazwie **myBackEndPool**.
* W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji sprawdza wszystkie wystąpienia maszyn wirtualnych, aby upewnić się, że mogą wysyłać ruch sieciowy. 

Maszyna wirtualna z niepowodzeniem sprawdzaniem sondy jest usuwana z modułu równoważenia obciążenia. Gdy błąd zostanie rozwiązany, maszyna wirtualna zostanie ponownie dodana do modułu równoważenia obciążenia.

Utwórz sondę kondycji za pomocą elementu [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Monitoruje kondycję maszyn wirtualnych.
* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Monitorowanie **portu 80**.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje:

* Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
* Pula adresów IP zaplecza do odbierania ruchu sieciowego.
* Wymagany port źródłowy i docelowy. 

Utwórz regułę modułu równoważenia obciążenia za pomocą elementu [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* O nazwie **myHTTPRule**
* Nasłuchiwanie na **porcie 80** w elemencie **webfrontonu** puli frontonów.
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80**. 
* Korzystanie z sondy kondycji **myHealthProbe**.
* Protokół **TCP**.
* Limit czasu bezczynności wynoszący **15 minut**.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Utwórz zasób modułu równoważenia obciążenia

Utwórz publiczny moduł równoważenia obciążenia za pomocą usługi [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* O nazwie **myLoadBalancer**
* W **wschodnim regionie**.
* W grupie zasobów **CreatePubLBQS-RG**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network-in-the-basic-sku"></a>Konfigurowanie sieci wirtualnej w podstawowej jednostce SKU

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu [nowego AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* O nazwie **myVNet**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Podsieć o nazwie **myBackendSubnet**.
* Sieć wirtualna **10.0.0.0/16**.
* Podsieć **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* O nazwie **myNSGRuleHTTP**.
* Opis **zezwalania na użycie protokołu HTTP**.
* Dostęp do programu **Zezwalaj**.
* Protokół **(*)**.
* Kierunek **ruchu przychodzącego**.
* Priorytet **2000**.
* Źródło **Internetu**.
* Zakres portów źródłowych **(*)**.
* Prefiks adresu docelowego **(*)**.
* **Port docelowy 80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* O nazwie **myNSG**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* Z regułami zabezpieczeń utworzonymi w poprzednich krokach przechowywanych w zmiennej.

```azurepowershell
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Tworzenie interfejsów sieciowych

Utwórz trzy interfejsy sieciowe za pomocą [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>MW 2

* O nazwie **myNicVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>MASZYNA WIRTUALNA 3

* O nazwie **myNicVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet**.
* W podsieci **myBackendSubnet**.
* W sieciowej grupie zabezpieczeń **myNSG**.
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreatePubLBQS-rg'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Utwórz zestaw dostępności dla maszyn wirtualnych

Użyj opcji [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) , aby utworzyć zestaw dostępności dla maszyn wirtualnych:

* O nazwie **myAvSet**.
* W grupie zasobów **CreatePubLBQS-RG**.
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreatePubLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Utwórz maszyny wirtualne za pomocą:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>Maszyna wirtualna 1

* O nazwie **myVM1**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM1**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W lokalizacji **wschodniego** .
* W zestawie dostępności **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>Maszyna wirtualna 2

* O nazwie **myVM2**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM2**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W lokalizacji **wschodniego** .
* W zestawie dostępności **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* O nazwie **myVM3**.
* W grupie zasobów **CreatePubLBQS-RG**.
* Dołączone do interfejsu sieciowego **myNicVM3**.
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer**.
* W lokalizacji **wschodniego** .
* W zestawie dostępności **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreatePubLBQS-rg'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Utworzenie i skonfigurowanie trzech maszyn wirtualnych może potrwać kilka minut.

---

## <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj rozszerzenie niestandardowego skryptu przy użyciu polecenia [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension). 

Rozszerzenie powoduje uruchomienie programu PowerShell Add-WindowsFeature Web-Server w celu zainstalowania serwera WebSerwer IIS, a następnie zaktualizowanie strony Default.htm w celu wyświetlenia nazwy hosta maszyny wirtualnej:

### <a name="vm1"></a>Maszyna wirtualna 1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>Maszyna wirtualna 2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm3"></a>VM3

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreatePubLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM3'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Użyj [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) , aby uzyskać publiczny adres IP modułu równoważenia obciążenia:

```azurepowershell-interactive
  ## Variables for command. ##
  $rg = 'CreatePubLBQS-rg'
  $ipn = 'myPublicIP'
    
  Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS na maszynie wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i pozostałe zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreatePubLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki Start

* Utworzono moduł równoważenia obciążenia w warstwie Standardowa lub podstawowa
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do..
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)