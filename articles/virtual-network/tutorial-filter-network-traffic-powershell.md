---
title: Filtruj ruch sieciowy — Azure PowerShell | Microsoft Docs
description: W tym artykule dowiesz się, jak filtrować ruch sieciowy w podsieci z grupą zabezpieczeń sieci przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8ab22e7960e233d6ae934fb52989aa73a494b33a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791514"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu programu PowerShell

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
* Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
* Wdrażanie maszyn wirtualnych w podsieci
* Testowanie filtrów ruchu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten artykuł będzie wymagał modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń. Reguły zabezpieczeń określają źródło i lokalizację docelową. Źródła i lokalizacje docelowe mogą być grupami zabezpieczeń aplikacji.

### <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów utworzonych w tym artykule przy użyciu elementu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz grupę zabezpieczeń aplikacji za pomocą elementu [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych wymaganiach w zakresie filtrowania portów. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

Utwórz regułę zabezpieczeń przy użyciu elementu [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myWebServers* przez porty 80 i 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

W tym artykule protokół RDP (Port 3389) został uwidoczniony w Internecie dla maszyny wirtualnej *myAsgMgmtServers* . W przypadku środowisk produkcyjnych zamiast udostępniania portu 3389 do Internetu zaleca się nawiązanie połączenia z zasobami platformy Azure, którymi chcesz zarządzać za pomocą połączenia [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub sieci [prywatnej](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz konfigurację podsieci z poleceniem [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig), a następnie Zapisz konfigurację podsieci w sieci wirtualnej za pomocą [opcji Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). Poniższy przykład dodaje podsieć o nazwie *mySubnet* do sieci wirtualnej i kojarzy z nią sieciową grupę zabezpieczeń *myNsg*:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Przed utworzeniem maszyn wirtualnych Pobierz obiekt sieci wirtualnej z podsiecią za pomocą [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Utwórz publiczny adres IP dla każdej maszyny wirtualnej za pomocą [nowego AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Utwórz dwa interfejsy sieciowe za pomocą polecenie [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy, przypisuje do niego publiczny adres IP *myVmWeb* oraz ustawia go jako element członkowski grupy zabezpieczeń aplikacji *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Poniższy przykład tworzy interfejs sieciowy, przypisuje do niego publiczny adres IP *myVmMgmt* oraz ustawia go jako element członkowski grupy zabezpieczeń aplikacji *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby umożliwić weryfikację filtrowania ruchu w kolejnym kroku.

Utwórz konfigurację maszyny wirtualnej za pomocą polecenie [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig), a następnie utwórz maszynę wirtualną za pomocą [nowej AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną, która będzie służyć jako serwer internetowy. Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Utwórz maszynę wirtualną, która będzie służyć jako serwer zarządzania:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Tworzenie maszyny wirtualnej zajmuje kilka minut. Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tworzenia maszyny wirtualnej.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVmMgmt*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną *myVmMgmt* z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.

Połączenie powiedzie się, ponieważ port 3389 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgMgmtServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmMgmt*.

Użyj następującego polecenia, aby utworzyć połączenie pulpitu zdalnego z maszyną wirtualną *myVmWeb* z maszyny wirtualnej *myVmMgmt*, korzystając z następującego polecenia w programie PowerShell:

``` 
mstsc /v:myvmWeb
```

Połączenie powiedzie się, ponieważ domyślne reguły zabezpieczeń w każdej sieciowej grupie zabezpieczeń zezwalają na ruch na wszystkich portach pomiędzy wszystkimi adresami IP w sieci wirtualnej. Nie możesz utworzyć połączenia pulpitu zdalnego z maszyną wirtualną *myVmWeb* z Internetu, ponieważ reguły zabezpieczeń *myAsgWebServers* nie zezwalają na ruch przychodzący z Internetu przez port 3389.

Użyj następującego polecenia, aby zainstalować usługi Microsoft IIS na maszynie wirtualnej *myVmWeb* z poziomu programu PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu instalacji usług IIS odłącz się od maszyny wirtualnej *myVmWeb*, co spowoduje pozostanie w połączeniu pulpitu zdalnego z maszyną wirtualną *myVmMgmt*. Aby wyświetlić ekran powitalny usług IIS, Otwórz przeglądarkę internetową i przejdź do protokołu http: \/ /myVmWeb.

Odłącz się od maszyny wirtualnej *myVmMgmt*.

Na komputerze wprowadź następujące polecenie w programie PowerShell, aby pobrać publiczny adres IP serwera *myVmWeb*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aby potwierdzić, że możesz uzyskać dostęp do serwera internetowego *myVmWeb* spoza platformy Azure, otwórz przeglądarkę internetową na komputerze i przejdź do adresu `http://<public-ip-address-from-previous-step>`. Połączenie powiedzie się, ponieważ port 80 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgWebServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmWeb*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](./network-security-groups-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie tabeli tras](tutorial-create-route-table-powershell.md).