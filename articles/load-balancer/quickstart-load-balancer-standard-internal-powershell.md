---
title: 'Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia — Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 12190a50579bf5b87685fc4b19ec7b2907e5ee9c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547048"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure PowerShell

Wprowadzenie do Azure Load Balancer przy użyciu Azure PowerShell do tworzenia wewnętrznego modułu równoważenia obciążenia i dwóch maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów przy użyciu elementu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* O nazwie **CreateIntLBQS-RG** .
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Tworzenie sieci wirtualnej i hosta usługi Azure bastionu

Utwórz sieć wirtualną przy użyciu [nowego AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* O nazwie **myVNet** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Podsieć o nazwie **myBackendSubnet** .
* Sieć wirtualna **10.0.0.0/16** .
* Podsieć **10.0.0.0/24** .
* Podsieć o nazwie **AzureBastionSubnet** .
* **10.0.1.0 podsieci/24** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Utwórz publiczny adres IP dla hosta usługi Azure bastionu

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć publiczny adres IP dla hosta bastionu:

* O nazwie **myPublicIPBastion**
* W grupie zasobów **CreateIntLBQS-RG** .
* W lokalizacji **wschodniego** .
* **Statyczna** metoda alokacji.
* **Standardowa** jednostka SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Tworzenie hosta usługi Azure bastionu

Użyj [New-AzBastion](/powershell/module/az.network/new-azbastion) , aby utworzyć hosta bastionu:

* O nazwie **myBastion** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W sieci wirtualnej **myVNet** .
* Skojarzone z publicznym adresem IP **myPublicIPBastion** .

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* O nazwie **myNSGRuleHTTP** .
* Opis **zezwalania na użycie protokołu HTTP** .
* Dostęp do programu **Zezwalaj** .
* Protokół **(*)** .
* Kierunek **ruchu przychodzącego** .
* Priorytet **2000** .
* Źródło **Internetu** .
* Zakres portów źródłowych **(*)** .
* Prefiks adresu docelowego **(*)** .
* **Port docelowy 80** .

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

* O nazwie **myNSG** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* Z regułami zabezpieczeń utworzonymi w poprzednich krokach przechowywanych w zmiennej.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Tworzenie usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu elementu [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Nazwa **frontonu** .
* Prywatny adres IP elementu **10.0.0.4** .

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* O nazwie **myBackEndPool** .
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
* O nazwie **myHealthProbe** .
* Protokół **TCP** .
* Monitorowanie **portu 80** .

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
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80** . 
* Korzystanie z sondy kondycji **myHealthProbe** .
* Protokół **TCP** .
* Limit czasu bezczynności wynoszący **15 minut** .
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
>[!NOTE]
>Maszyny wirtualne w puli zaplecza nie będą miały wychodzącej łączności z Internetem przy użyciu tej konfiguracji. </br> Aby uzyskać więcej informacji na temat zapewniania łączności wychodzącej, zobacz: </br> **[Połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md)**</br> Opcje zapewniania łączności: </br> **[Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](egress-only.md)** </br> **[Co to jest Virtual Network translator adresów sieciowych?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**


### <a name="create-load-balancer-resource"></a>Utwórz zasób modułu równoważenia obciążenia

Utwórz wewnętrzny moduł równoważenia obciążenia za pomocą usługi [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* O nazwie **myLoadBalancer**
* W **wschodnim regionie** .
* W grupie zasobów **CreateIntLBQS-RG** .

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Tworzenie interfejsów sieciowych

Utwórz trzy interfejsy sieciowe za pomocą [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet** .
* W podsieci **myBackendSubnet** .
* W sieciowej grupie zabezpieczeń **myNSG** .
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool** .

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
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

* O nazwie **myNicVM2** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet** .
* W podsieci **myBackendSubnet** .
* W sieciowej grupie zabezpieczeń **myNSG** .
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool** .

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
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

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

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

* O nazwie **myVM1** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Dołączone do interfejsu sieciowego **myNicVM1** .
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer** .
* W **Strefa 1** .
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
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

* O nazwie **myVM2** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Dołączone do interfejsu sieciowego **myNicVM2** .
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer** .
* W **strefa 2** .
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
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

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Przed wdrożeniem maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, należy utworzyć pomocnicze zasoby sieci wirtualnej.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Tworzenie sieci wirtualnej i hosta usługi Azure bastionu

Utwórz sieć wirtualną przy użyciu [nowego AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* O nazwie **myVNet** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Podsieć o nazwie **myBackendSubnet** .
* Sieć wirtualna **10.0.0.0/16** .
* Podsieć **10.0.0.0/24** .
* Podsieć o nazwie **AzureBastionSubnet** .
* **10.0.1.0 podsieci/24** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Utwórz publiczny adres IP dla hosta usługi Azure bastionu

Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć publiczny adres IP dla hosta bastionu:

* O nazwie **myPublicIPBastion**
* W grupie zasobów **CreateIntLBQS-RG** .
* W lokalizacji **wschodniego** .
* **Statyczna** metoda alokacji.
* **Standardowa** jednostka SKU.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Tworzenie hosta usługi Azure bastionu

Użyj [New-AzBastion](/powershell/module/az.network/new-azbastion) , aby utworzyć hosta bastionu:

* O nazwie **myBastion** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W sieci wirtualnej **myVNet** .
* Skojarzone z publicznym adresem IP **myPublicIPBastion** .

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.


### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* O nazwie **myNSGRuleHTTP** .
* Opis **zezwalania na użycie protokołu HTTP** .
* Dostęp do programu **Zezwalaj** .
* Protokół **(*)** .
* Kierunek **ruchu przychodzącego** .
* Priorytet **2000** .
* Źródło **Internetu** .
* Zakres portów źródłowych **(*)** .
* Prefiks adresu docelowego **(*)** .
* **Port docelowy 80** .

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

* O nazwie **myNSG** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* Z regułami zabezpieczeń utworzonymi w poprzednich krokach przechowywanych w zmiennej.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji opisano szczegółowo procedurę tworzenia i konfigurowania następujących składników modułu równoważenia obciążenia:

  * Pula adresów IP frontonu, która odbiera przychodzący ruch sieciowy w module równoważenia obciążenia.
  * Pula adresów IP zaplecza, w której Pula frontonu wysyła ruch sieciowy o zrównoważonym obciążeniu.
  * Sonda kondycji, która określa kondycję wystąpień maszyn wirtualnych zaplecza.
  * Reguła modułu równoważenia obciążenia, która definiuje sposób dystrybucji ruchu do maszyn wirtualnych.

### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu

Utwórz adres IP frontonu przy użyciu elementu [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Nazwa **frontonu** .
* Prywatny adres IP elementu **10.0.0.4** .

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza za pomocą [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* O nazwie **myBackEndPool** .
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
* O nazwie **myHealthProbe** .
* Protokół **TCP** .
* Monitorowanie **portu 80** .

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
* Wysyłanie ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza **myBackEndPool** przy użyciu **portu 80** . 
* Korzystanie z sondy kondycji **myHealthProbe** .
* Protokół **TCP** .
* Limit czasu bezczynności wynoszący **15 minut** .

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
* W **wschodnim regionie** .
* W grupie zasobów **CreateIntLBQS-RG** .

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Tworzenie interfejsów sieciowych

Utwórz trzy interfejsy sieciowe za pomocą [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>Maszyna wirtualna 1

* O nazwie **myNicVM1** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet** .
* W podsieci **myBackendSubnet** .
* W sieciowej grupie zabezpieczeń **myNSG** .
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool** .

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
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

* O nazwie **myNicVM2** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet** .
* W podsieci **myBackendSubnet** .
* W sieciowej grupie zabezpieczeń **myNSG** .
* Dołączono do modułu równoważenia obciążenia **myLoadBalancer** w **myBackEndPool** .

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
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

### <a name="create-availability-set-for-virtual-machines"></a>Utwórz zestaw dostępności dla maszyn wirtualnych

Użyj opcji [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) , aby utworzyć zestaw dostępności dla maszyn wirtualnych:

* O nazwie **myAvSet** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

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

* O nazwie **myVM1** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Dołączone do interfejsu sieciowego **myNicVM1** .
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer** .
* W lokalizacji **wschodniego** .
* W zestawie dostępności **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
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

* O nazwie **myVM2** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Dołączone do interfejsu sieciowego **myNicVM2** .
* Dołączono do usługi równoważenia obciążenia **myLoadBalancer** .
* W lokalizacji **wschodniego** .
* W zestawie dostępności **myAvSet** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
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

Utworzenie i skonfigurowanie trzech maszyn wirtualnych może potrwać kilka minut.

---

## <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj rozszerzenie niestandardowego skryptu przy użyciu polecenia [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest). 

Rozszerzenie powoduje uruchomienie programu PowerShell Add-WindowsFeature Web-Server w celu zainstalowania serwera WebSerwer IIS, a następnie zaktualizowanie strony Default.htm w celu wyświetlenia nazwy hosta maszyny wirtualnej:

### <a name="vm1"></a>Maszyna wirtualna 1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
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
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

### <a name="create-network-interface"></a>Utwórz interfejs sieciowy

Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* O nazwie **myNicTestVM** .
* W grupie zasobów **CreateIntLBQS-RG** .
* W polu **Wschodnie** lokalizacje.
* W sieci wirtualnej **myVNet** .
* W podsieci **myBackendSubnet** .
* W sieciowej grupie zabezpieczeń **myNSG** .

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ustaw nazwę użytkownika i hasło administratora maszyny wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = Get-Credential
```

Utwórz maszynę wirtualną przy użyciu:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* O nazwie **myTestVM** .
* W grupie zasobów **CreateIntLBQS-RG** .
* Dołączone do interfejsu sieciowego **myNicTestVM** .
* W lokalizacji **wschodniego** .

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Testowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź prywatny adres IP dla modułu równoważenia obciążenia na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby** , a następnie wybierz pozycję **myLoadBalancer** .

2. Zanotuj lub skopiuj adres obok **prywatnego adresu IP** w **omówieniu** **myLoadBalancer** .

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby** , a następnie na liście zasobów wybierz pozycję **myTestVM** , która znajduje się w grupie zasobów **CreateIntLBQS-RG** .

4. Na stronie **Przegląd** wybierz opcję **Połącz** , a następnie **bastionu** .

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM** .

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Tworzenie standardowego wewnętrznego modułu równoważenia obciążenia" border="true":::
   
Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS na maszynie wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i pozostałe zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki Start

* Utworzono standardowy lub podstawowy moduł równoważenia obciążenia wewnętrznego
* Dołączone maszyny wirtualne. 
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji.
* Przetestowano moduł równoważenia obciążenia.

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do..
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)


