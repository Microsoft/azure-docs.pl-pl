---
title: Tworzenie bramy translatora adresów sieciowych — Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: W tym przewodniku szybki start pokazano, jak utworzyć bramę NAT przy użyciu Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 516f858c8f95ec3f7cfd8ffc1592358716986519
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223299"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure PowerShell

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ten samouczek można wykonać przy użyciu Azure Cloud Shell lub uruchomić polecenia lokalnie.  Jeśli nie korzystasz z Azure Cloud Shell, [Zaloguj się teraz](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=latest). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroupNAT** w lokalizacji **eastus2** :

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

Opcje publicznego adresu IP dla bramy translatora adresów sieciowych są następujące:

* **Publiczne adresy IP**
* **Prefiksy publicznych adresów IP**

Obie te funkcje mogą być używane z bramą translatora adresów sieciowych.

Aby przedstawić ten scenariusz, dodamy publiczny adres IP i publiczny prefiks adresu IP.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać dostęp do Internetu, wymagany jest co najmniej jeden publiczny adres IP dla bramy translatora adresów sieciowych. Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIP** w **myResourceGroupNAT**. Wynik tego polecenia będzie przechowywany w zmiennej **$publicIP** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

Użyj [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix?view=latest) , aby utworzyć zasób publicznego PREFIKSU adresu IP o nazwie **myPublicIPprefix** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIPPrefix** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

Utwórz globalną bramę usługi Azure NAT przy użyciu elementu [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Wynik tego polecenia spowoduje utworzenie zasobu bramy o nazwie **myNATgateway** , który używa publicznego adresu IP **myPublicIP** i publicznego prefiksu adresu IP **myPublicIPprefix**. Limit czasu bezczynności jest ustawiony na 10 minut.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$natGateway** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

W tym momencie Brama translatora adresów sieciowych jest funkcjonalna i nie ma potrzeby konfigurowania podsieci sieci wirtualnej.

## <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

Utwórz sieć wirtualną i skojarz ją z bramą.

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie Moja **podsieć** przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) **w funkcji New** - [AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresów IP dla sieci wirtualnej to **192.168.0.0/16**. Podsieć w sieci wirtualnej to **192.168.0.0/24**.  Wyniki poleceń będą przechowywane w zmiennych o nazwie **$Subnet** i **$VNET** do późniejszego użycia.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Cały ruch wychodzący do miejsc docelowych w Internecie używa teraz usługi translatora adresów sieciowych.  Nie trzeba konfigurować UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Tworzenie maszyny wirtualnej do korzystania z usługi translatora adresów sieciowych

Teraz utworzymy maszynę wirtualną do korzystania z usługi translatora adresów sieciowych.  Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, który umożliwia dostęp do maszyny wirtualnej.  Usługa translatora adresów sieciowych obsługuje kierunek przepływu i zastępuje domyślne miejsce docelowe w podsieci. Publiczny adres IP maszyny wirtualnej nie będzie używany dla połączeń wychodzących.

### <a name="create-public-ip-for-source-vm"></a>Utwórz publiczny adres IP dla źródłowej maszyny wirtualnej

Tworzymy publiczny adres IP, który będzie używany do uzyskiwania dostępu do maszyny wirtualnej.  Użyj [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) , aby utworzyć zasób publicznego adresu IP o nazwie **myPublicIPVM** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$publicIpVM** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Tworzenie sieciowej grupy zabezpieczeń i Uwidacznianie punktu końcowego SSH dla maszyny wirtualnej

Standardowe publiczne adresy IP są "zabezpieczone domyślnie", dlatego musimy utworzyć sieciowej grupy zabezpieczeń, aby zezwolić na dostęp przychodzący do protokołu SSH. Użyj [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) , aby utworzyć zasób sieciowej grupy zabezpieczeń o nazwie **myNSG**. Użyj [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) , aby utworzyć regułę sieciowej grupy zabezpieczeń dla dostępu SSH o nazwie **SSH** w **myResourceGroupNAT**.  Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$NSG** do późniejszego użycia.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Utwórz kartę sieciową dla maszyny wirtualnej

Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) o nazwie **myNic**. To polecenie kojarzy publiczny adres IP z sieciową grupą zabezpieczeń. Wynik tego polecenia będzie przechowywany w zmiennej o nazwie **$nic** do późniejszego użycia.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Tworzenie maszyny wirtualnej

#### <a name="create-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Do wykonania kroków tego przewodnika Szybki start konieczne jest posiadanie pary kluczy SSH. Jeśli masz już parę kluczy SSH, możesz pominąć ten krok.

Użyj protokołu ssh-keygen do utworzenia pary kluczy SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Aby uzyskać bardziej szczegółowe informacje na temat tworzenia par kluczy SSH, łącznie z użyciem programu PuTTy, zobacz [Jak używać kluczy SSH w systemie Windows](../virtual-machines/linux/ssh-from-windows.md).

W przypadku utworzenia pary kluczy SSH przy użyciu Cloud Shell para kluczy jest przechowywana w obrazie kontenera. To [konto magazynu jest tworzone automatycznie](../cloud-shell/persisting-shell-storage.md). Nie usuwaj konta magazynu ani udziału plików w programie, aż po pobraniu kluczy.

#### <a name="create-vm-configuration"></a>Utwórz konfigurację maszyny wirtualnej

Aby utworzyć maszynę wirtualną w programie PowerShell, należy utworzyć konfigurację, która ma ustawienia dla obrazu do użycia, rozmiaru i opcji uwierzytelniania. Konfiguracja służy do tworzenia maszyny wirtualnej.

Zdefiniuj poświadczenia protokołu SSH, informacje o systemie operacyjnym i rozmiar maszyny wirtualnej. W tym przykładzie klucz SSH jest przechowywany w ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Połącz definicje konfiguracji, aby utworzyć maszynę wirtualną o nazwie **myVM** z opcją [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) w **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Zaczekaj na przygotowanie maszyny wirtualnej do wdrożenia, a następnie kontynuuj pozostałe kroki.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajdywanie adresu IP maszyny wirtualnej

Najpierw musimy odnaleźć adres IP utworzonej maszyny wirtualnej. Aby uzyskać publiczny adres IP maszyny wirtualnej, użyj polecenie [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było używać go do uzyskiwania dostępu do maszyny wirtualnej.

### <a name="sign-in-to-vm"></a>Logowanie do maszyny wirtualnej

Poświadczenia SSH powinny być przechowywane w Cloud Shell z poprzedniej operacji.  Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną.

```bash
ssh azureuser@<ip-address-destination>
```

Teraz można przystąpić do korzystania z usługi translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę translatora adresów sieciowych oraz maszynę wirtualną w celu jej użycia. 

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów źródeł adresów sieciowych polega na dodaniu dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu adresów IP.


- Dowiedz się więcej o [usłudze Azure Virtual Network translator adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]