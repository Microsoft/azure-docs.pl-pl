---
title: Skojarz publiczny adres IP z maszyną wirtualną
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skojarzyć publiczny adres IP z maszyną wirtualną za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 76f92b5da2331748fbbbfc68f1e456fd50dd71ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223027"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Skojarz publiczny adres IP z maszyną wirtualną

W tym artykule dowiesz się, jak skojarzyć publiczny adres IP z istniejącą maszyną wirtualną. Jeśli chcesz nawiązać połączenie z maszyną wirtualną za pośrednictwem Internetu, maszyna wirtualna musi mieć skojarzony publiczny adres IP. Jeśli chcesz utworzyć nową maszynę wirtualną z publicznym adresem IP, możesz to zrobić przy użyciu [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [interfejsu wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)lub [programu PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Publiczne adresy IP mają opłata nominalną. Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/ip-addresses/). Istnieje limit liczby publicznych adresów IP, których można użyć na subskrypcję. Aby uzyskać szczegółowe informacje, zobacz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Aby skojarzyć publiczny adres IP z maszyną wirtualną, możesz użyć [Azure Portal](#azure-portal), [interfejsu wiersza polecenia](#azure-cli) platformy Azure lub [programu PowerShell](#powershell) .

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do lub Wyszukaj maszynę wirtualną, do której chcesz dodać publiczny adres IP, a następnie wybierz ją.
3. W obszarze **Ustawienia** wybierz pozycję **Sieć**, a następnie wybierz interfejs sieciowy, do którego chcesz dodać publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybierz interfejs sieciowy](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z interfejsami sieciowymi podłączonymi do maszyny wirtualnej. Na poprzedniej ilustracji maszyna wirtualna ma tylko jeden interfejs sieciowy. Jeśli maszyna wirtualna ma wiele interfejsów sieciowych, wszystkie będą wyświetlane i wybierasz interfejs sieciowy, do którego chcesz skojarzyć publiczny adres IP.

4. Wybierz pozycję **konfiguracje adresów IP** , a następnie wybierz konfigurację adresu IP, jak pokazano na poniższej ilustracji:

   ![Wybierz konfigurację adresu IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z konfiguracjami protokołu IP dla interfejsu sieciowego. Na poprzedniej ilustracji interfejs sieciowy ma jedną konfigurację adresu IP. Jeśli interfejs sieciowy miał wiele konfiguracji adresów IP, wszystkie będą widoczne na liście i zostanie wybrana konfiguracja adresu IP, do której ma zostać skojarzony publiczny adres IP.

5. Wybierz pozycję **włączone**, a następnie wybierz pozycję **adres IP (*Skonfiguruj wymagane ustawienia*)**. Wybierz istniejący publiczny adres IP, który automatycznie zamyka pole **Wybierz publiczny adres IP** . Jeśli na liście nie ma żadnych dostępnych publicznych adresów IP, musisz ją utworzyć. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Wybierz pozycję **Zapisz**, jak pokazano na poniższej ilustracji, a następnie zamknij pole konfiguracji protokołu IP.

   ![Włącz publiczny adres IP](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Wyświetlane są publiczne adresy IP, które znajdują się w tym samym regionie co maszyna wirtualna. Jeśli masz wiele publicznych adresów IP utworzonych w regionie, wszystkie będą widoczne w tym miejscu. Jeśli jakieś są wyszarzone, jest to spowodowane tym, że adres jest już skojarzony z innym zasobem.

6. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP, jak pokazano na poniższej ilustracji. Pojawienie się adresu IP może potrwać kilka sekund.

   ![Wyświetl przypisany publiczny adres IP](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Przypisany adres może być dowolnym adresem w pulach używanym dla regionu. Jeśli potrzebujesz, aby adres był przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

7. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** za pomocą poleceń interfejsu wiersza polecenia, które obserwują. Wybranie przycisku **Wypróbuj** wywołuje Cloud Shell, aby można było zalogować się do konta platformy Azure za pomocą usługi.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie w bash, zaloguj się do platformy Azure za pomocą `az login` .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Za pomocą polecenia [AZ Network nic-IP-config Update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) Skojarz publiczny adres IP z konfiguracją adresów IP. Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* , który istnieje w grupie zasobów o nazwie Moja *resourceName*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj polecenia [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby je utworzyć. Na przykład następujące polecenie tworzy publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie Moja *resourceName*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej na temat wszystkich ustawień publicznego adresu IP, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych.

   - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [AZ VM nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) , aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *myVMVMNic* jest nazwą interfejsu sieciowego.

   - Jeśli nie znasz nazwy konfiguracji protokołu IP dla interfejsu sieciowego, użyj polecenia [AZ Network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) , aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji protokołu IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Zapoznaj się z publicznym adresem IP przypisanym do konfiguracji protokołu IP za pomocą polecenia [AZ VM list-IP-addresss](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) . Poniższy przykład przedstawia adresy IP przypisane do istniejącej maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *resourceName*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Przypisany adres może być dowolnym adresem w pulach używanym dla regionu. Jeśli potrzebujesz, aby adres był przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="powershell"></a>PowerShell

Zainstaluj program [PowerShell](/powershell/azure/install-az-ps)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Program PowerShell jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach programu PowerShell. Wybranie przycisku **Wypróbuj** wywołuje Cloud Shell, aby można było zalogować się do konta platformy Azure za pomocą usługi.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się do platformy Azure za pomocą polecenia `Connect-AzAccount` .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj poleceń [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) i [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) , aby uzyskać sieć wirtualną i podsieć, w której znajduje się interfejs sieciowy. Następnie użyj polecenia [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) , aby pobrać interfejs sieciowy i polecenie [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) , aby uzyskać istniejący publiczny adres IP. Następnie użyj polecenia [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) , aby skojarzyć publiczny adres IP z konfiguracją IP i poleceniem [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) w celu zapisania nowej konfiguracji adresu IP w interfejsie sieciowym.

   Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją protokołu IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* , który istnieje w podsieci o nazwie *myVMSubnet* w sieci wirtualnej o nazwie *myVMVNet*. Wszystkie zasoby znajdują się w grupie zasobów *o nazwie moja grupa zasobów.*
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj polecenia [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) , aby go utworzyć. Na przykład następujące polecenie tworzy *dynamiczny* publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie Moja *resourceName* w regionie *wschodnim* .
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej na temat wszystkich ustawień publicznego adresu IP, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych.

   - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *myVMVMNic* jest nazwą interfejsu sieciowego.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, w której znajduje się interfejs sieciowy, użyj `Get-AzNetworkInterface` polecenia, aby wyświetlić informacje. Na przykład następujące polecenie pobiera informacje o sieci wirtualnej i podsieci dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *myVMVNET* jest nazwą sieci wirtualnej, a *myVMSubnet* jest nazwą podsieci.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Jeśli nie znasz nazwy konfiguracji protokołu IP dla interfejsu sieciowego, użyj polecenia [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) , aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji protokołu IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *ipconfigmyVM* jest nazwą konfiguracji adresu IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji protokołu IP za pomocą polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Poniższy przykład przedstawia adres przypisany do publicznego adresu IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie Moja *resourceName*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Jeśli nie znasz nazwy publicznego adresu IP przypisanej do konfiguracji adresu IP, uruchom następujące polecenia, aby je pobrać:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *myVMPublicIP* jest nazwa publicznego adresu IP przypisanego do konfiguracji protokołu IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Przypisany adres może być dowolnym adresem w pulach używanym dla regionu. Jeśli potrzebujesz, aby adres był przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="allow-network-traffic-to-the-vm"></a>Zezwalaj na ruch sieciowy do maszyny wirtualnej

Aby można było połączyć się z publicznym adresem IP z Internetu, należy się upewnić, że wymagane porty są otwarte w dowolnej sieciowej grupie zabezpieczeń, która może być skojarzona z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub obie. Mimo że grupy zabezpieczeń filtrują ruch do prywatnego adresu IP interfejsu sieciowego, po odebraniu przychodzącego ruchu internetowego z publicznego adresu IP, platforma Azure tłumaczy adres publiczny na prywatny adres IP, więc jeśli sieciowa Grupa zabezpieczeń uniemożliwia przepływ ruchu, komunikacja z publicznym adresem IP kończy się niepowodzeniem. Efektywne reguły zabezpieczeń dla interfejsu sieciowego i jego podsieci można wyświetlić przy użyciu [portalu](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [interfejsu wiersza polecenia](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)lub [programu PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Następne kroki

Zezwalaj na przychodzący ruch internetowy do maszyny wirtualnej przy użyciu sieciowej grupy zabezpieczeń. Aby dowiedzieć się, jak utworzyć sieciową grupę zabezpieczeń, zobacz [Work with Network Security Groups](manage-network-security-group.md#work-with-network-security-groups). Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [grupy zabezpieczeń](./network-security-groups-overview.md).