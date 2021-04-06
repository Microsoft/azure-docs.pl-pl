---
title: Wiele adresów IP dla usługi Azure Virtual Machines — PowerShell | Microsoft Docs
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu programu PowerShell. | Menedżer zasobów
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: d86d4248b449ad3961a7798fd36a320eb6a74009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217077"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną za pomocą modelu wdrażania Azure Resource Manager przy użyciu programu PowerShell. Nie można przypisać wielu adresów IP do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Omówienie modeli wdrażania](../azure-resource-manager/management/deployment-models.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Poniższe kroki wyjaśniają, jak utworzyć przykładową maszynę wirtualną z wieloma adresami IP, zgodnie z opisem w tym scenariuszu. Zmień wartości zmiennych zgodnie z wymaganiami implementacji.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki opisane w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli program PowerShell nie został jeszcze zainstalowany i skonfigurowany, wykonaj kroki opisane w artykule [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/) .
2. Zaloguj się do swojego konta za pomocą `Connect-AzAccount` polecenia.
3. Zastąp zmienną *resourceName* i *zachodnie* nazwą i lokalizacją wybrania. Utwórz grupę zasobów. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Utwórz sieć wirtualną (VNet) i podsieć w tej samej lokalizacji co grupa zasobów:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Utwórz sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) i regułę. SIECIOWEJ grupy zabezpieczeń zabezpiecza maszynę wirtualną za pomocą reguł ruchu przychodzącego i wychodzącego. W tym przypadku jest tworzona reguła ruchu przychodzącego dla portu 3389, która umożliwia nawiązywanie przychodzących połączeń pulpitu zdalnego.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Zdefiniuj podstawową konfigurację adresu IP dla karty sieciowej. Zmień 10.0.0.4 na prawidłowy adres w utworzonej podsieci, jeśli nie użyto wcześniej zdefiniowanej wartości. Przed przypisaniem statycznego adresu IP zaleca się, aby najpierw potwierdzić, że nie jest on już używany. Wprowadź polecenie `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` . Jeśli adres jest dostępny, wyjście zwraca *wartość true*. Jeśli nie jest dostępny, wyjście zwraca *wartość false* i listę dostępnych adresów. 

    W poniższych poleceniach **Zastąp \<replace-with-your-unique-name> wartość unikatową nazwą DNS, która ma zostać użyta.** Nazwa musi być unikatowa dla wszystkich publicznych adresów IP w regionie świadczenia usługi Azure. Jest to opcjonalny parametr. Można je usunąć, jeśli chcesz tylko połączyć się z maszyną wirtualną przy użyciu publicznego adresu IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Po przypisaniu wielu konfiguracji protokołu IP do karty sieciowej należy przypisać jedną konfigurację jako *podstawową*.

    > [!NOTE]
    > Publiczne adresy IP mają opłata nominalną. Aby dowiedzieć się więcej o cenach adresów IP, zapoznaj się z informacjami na stronie [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Zdefiniuj pomocnicze konfiguracje protokołu IP dla karty sieciowej. W razie potrzeby można dodać lub usunąć konfiguracje. Każda konfiguracja adresu IP musi mieć przypisany prywatny adres IP. Każda konfiguracja może opcjonalnie mieć przypisany jeden publiczny adres IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Utwórz kartę sieciową i skojarz z nią trzy konfiguracje IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Chociaż wszystkie konfiguracje są przypisane do jednej karty sieciowej w tym artykule, można przypisać wiele konfiguracji IP do każdej karty sieciowej podłączonej do maszyny wirtualnej. Aby dowiedzieć się, jak utworzyć maszynę wirtualną z wieloma kartami sieciowymi, przeczytaj artykuł [Tworzenie maszyny wirtualnej z wieloma](../virtual-machines/windows/multiple-nics.md) kartami sieciowymi.

9. Utwórz maszynę wirtualną, wprowadzając następujące polecenia:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Aby dodać prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonaj kroki dla systemu operacyjnego w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznych adresów IP do systemu operacyjnego.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny wirtualnej

Możesz dodać prywatne i publiczne adresy IP do interfejsu sieciowego platformy Azure, wykonując poniższe kroki. W przykładach w poniższych sekcjach założono, że masz już maszynę wirtualną z trzema konfiguracjami protokołu IP opisanymi w [scenariuszu](#scenario) w tym artykule, ale nie jest to wymagane.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki opisane w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli program PowerShell nie został jeszcze zainstalowany i skonfigurowany, wykonaj kroki opisane w artykule [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/) .
2. Zmień "wartości" następujących $Variables na nazwę karty sieciowej, do której chcesz dodać adres IP, i grupę zasobów i lokalizację, w której znajduje się ta karta sieciowa:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Jeśli nie znasz nazwy karty sieciowej, którą chcesz zmienić, wprowadź następujące polecenia, a następnie zmień wartości poprzednich zmiennych:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Utwórz zmienną i ustaw ją na istniejącą kartę sieciową, wpisując następujące polecenie:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. W poniższych poleceniach Zmień *MyVNet* i *podsieć* na nazwy sieci wirtualnej i podsieć, z którą jest połączona karta sieciowa. Wprowadź polecenia, aby pobrać obiekty sieci wirtualnej i podsieci, z którymi jest połączona karta sieciowa:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, z którą jest połączona karta sieciowa, wprowadź następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   W danych wyjściowych poszukaj tekstu podobnego do następujących przykładowych danych wyjściowych:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    W tym wyjściu *MyVnet* to sieć wirtualna, a moja *podsieć* jest podsiecią, z którą jest połączona karta sieciowa.

5. Wykonaj kroki opisane w jednej z poniższych sekcji, zgodnie z wymaganiami:

   **Dodawanie prywatnego adresu IP**

   Aby dodać prywatny adres IP do karty sieciowej, należy utworzyć konfigurację adresu IP. Następujące polecenie tworzy konfigurację ze statycznym adresem IP 10.0.0.7. Podczas określania statycznego adresu IP musi to być nieużywany adres dla podsieci. Zalecane jest, aby najpierw przetestować adres, aby upewnić się, że jest on dostępny przez wprowadzenie `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` polecenia. Jeśli adres IP jest dostępny, wyjście zwraca *wartość true*. Jeśli nie jest on dostępny, wyjście zwraca *wartość false* i listę dostępnych adresów.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Utwórz tyle konfiguracji, ile potrzebujesz, przy użyciu unikatowych nazw konfiguracji i prywatnych adresów IP (dla konfiguracji ze statycznymi adresami IP).

   Dodaj prywatny adres IP do systemu operacyjnego maszyny wirtualnej, wykonując kroki dla systemu operacyjnego w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule.

   **Dodawanie publicznego adresu IP**

   Publiczny adres IP jest dodawany przez skojarzenie zasobu publicznego adresu IP z nową konfiguracją IP lub istniejącą konfiguracją IP. Wykonaj czynności opisane w jednej z poniższych sekcji, zgodnie z potrzebami.

   > [!NOTE]
   > Publiczne adresy IP mają opłata nominalną. Aby dowiedzieć się więcej o cenach adresów IP, zapoznaj się z informacjami na stronie [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Skojarz zasób publicznego adresu IP z nową konfiguracją adresu IP**

   Za każdym razem, gdy dodasz publiczny adres IP w nowej konfiguracji protokołu IP, musisz również dodać prywatny adres IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP. Możesz dodać istniejący zasób publicznego adresu IP lub utworzyć nowy. Aby utworzyć nowy, wprowadź następujące polecenie:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Aby utworzyć nową konfigurację adresu IP ze statycznym prywatnym adresem IP i skojarzonym z nim zasobem publicznego adresu IP *myPublicIp3* , wprowadź następujące polecenie:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Skojarz zasób publicznego adresu IP z istniejącą konfiguracją adresu IP**

   Zasób publicznego adresu IP może być skojarzony tylko z konfiguracją IP, która nie ma już skojarzonej. Aby określić, czy konfiguracja adresu IP ma skojarzony publiczny adres IP, wprowadź następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Zostaną wyświetlone dane wyjściowe podobne do następujących:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Ponieważ kolumna **PublicIpAddress** dla *polecenia IpConfig-3* jest pusta, nie jest obecnie skojarzony żaden zasób publicznego adresu IP. Możesz dodać istniejący zasób publicznego adresu IP do IpConfig-3 lub wprowadź następujące polecenie, aby je utworzyć:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP z istniejącą konfiguracją IP o nazwie *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Skonfiguruj kartę sieciową przy użyciu nowej konfiguracji protokołu IP, wprowadzając następujące polecenie:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Aby wyświetlić prywatne adresy IP i zasoby publicznych adresów IP przypisane do karty sieciowej, wprowadź następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Dodaj prywatny adres IP do systemu operacyjnego maszyny wirtualnej, wykonując kroki dla systemu operacyjnego w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule. Nie dodawaj publicznego adresu IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]