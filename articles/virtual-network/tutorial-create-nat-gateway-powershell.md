---
title: 'Samouczek: Tworzenie bramy translatora adresów sieciowych — PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Wprowadzenie do tworzenia bramy NAT przy użyciu Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620238"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure PowerShell

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną.
> * Tworzy maszynę wirtualną.
> * Utwórz bramę NAT i skojarz ją z siecią wirtualną.
> * Połącz się z maszyną wirtualną i Sprawdź adres IP translatora adresów sieciowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell zainstalowane lokalnie lub Azure Cloud Shell

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

W tej sekcji utworzysz bramę translatora adresów sieciowych i pomocnicze zasoby.

* Aby uzyskać dostęp do Internetu, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**. 

* Utwórz globalną bramę usługi Azure NAT przy użyciu elementu [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Wynik tego polecenia spowoduje utworzenie zasobu bramy o nazwie **myNATgateway** , który korzysta z publicznego adresu IP **myPublicIP**. Limit czasu bezczynności jest ustawiony na 10 minut.  

* Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie Moja **podsieć** przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) **w funkcji New** - [AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Przestrzeń adresów IP dla sieci wirtualnej to **10.1.0.0/16**. Podsieć w sieci wirtualnej to **10.1.0.0/24**.

* Utwórz hosta usługi Azure bastionu o nazwie **myBastionHost** w celu uzyskania dostępu do maszyny wirtualnej. Użyj [New-AzBastion](/powershell/module/az.network/new-azbastion) , aby utworzyć hosta bastionu. Utwórz publiczny adres IP dla hosta bastionu za pomocą elementu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Maszyna wirtualna

W tej sekcji utworzysz maszynę wirtualną służącą do testowania bramy translatora adresów sieciowych i weryfikacji publicznego adresu IP połączenia wychodzącego.

* Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Ustaw nazwę użytkownika i hasło administratora dla maszyny wirtualnej za pomocą [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Utwórz maszynę wirtualną przy użyciu:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Poczekaj na zakończenie tworzenia maszyny wirtualnej przed przejściem do następnej sekcji.

## <a name="test-nat-gateway"></a>Testowanie bramy translatora adresów sieciowych

W tej sekcji przetestujemy bramę translatora adresów sieciowych. Najpierw odnajdziemy publiczny adres IP bramy translatora adresów sieciowych. Następnie nastąpi połączenie z testową maszyną wirtualną i zweryfikowanie połączenia wychodzącego za pomocą bramy translatora adresów sieciowych.
    
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Znajdź publiczny adres IP dla bramy translatora adresów sieciowych na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Zanotuj publiczny adres IP:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Odnajdź publiczny adres IP bramy translatora adresów sieciowych" border="true":::

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM** , która znajduje się w grupie zasobów **myResourceGroupNAT** .

4. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź **https://whatsmyip.com** na pasku adresu.

9. Sprawdź, czy adres IP wyświetlany jest zgodny z adresem bramy NAT zanotowanym w poprzednim kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer pokazujący zewnętrzny adres IP dla ruchu wychodzącego" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i bramę translatora adresów sieciowych, wykonując następujące czynności:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz:
> [!div class="nextstepaction"]
> [Omówienie Virtual Network NAT](nat-overview.md)
