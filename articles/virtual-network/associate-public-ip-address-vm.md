---
title: Kojarzenie publicznego adresu IP z maszyną wirtualną
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skojarzyć publiczny adres IP z maszyną wirtualną przy użyciu interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767713"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Kojarzenie publicznego adresu IP z maszyną wirtualną

Z tego artykułu dowiesz się, jak skojarzyć publiczny adres IP z istniejącą maszyną wirtualną. Jeśli chcesz nawiązać połączenie z maszyną wirtualną z Internetu, maszyna wirtualna musi mieć skojarzony z nią publiczny adres IP. Jeśli chcesz utworzyć nową maszynę wirtualną z publicznym adresem IP, możesz to zrobić przy użyciu interfejsu [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), interfejsu wiersza polecenia [(CLI)](virtual-network-deploy-static-pip-arm-cli.md)platformy Azure lub programu [PowerShell.](virtual-network-deploy-static-pip-arm-ps.md) Za publiczne adresy IP pobierana jest nominalna opłata. Aby uzyskać szczegółowe informacje, zobacz [cennik](https://azure.microsoft.com/pricing/details/ip-addresses/). Istnieje ograniczenie liczby publicznych adresów IP, których można użyć na subskrypcję. Aby uzyskać szczegółowe informacje, zobacz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Aby skojarzyć publiczny adres IP [z maszyną](#azure-portal)wirtualną, możesz użyć interfejsu Azure Portal , interfejsu wiersza polecenia [platformy](#azure-cli) Azure lub programu [PowerShell.](#powershell)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do adresu lub wyszukaj maszynę wirtualną, do której chcesz dodać publiczny adres IP, a następnie wybierz ją.
3. W **obszarze** Ustawienia wybierz **pozycję Sieć,** a następnie wybierz interfejs sieciowy, do którego chcesz dodać publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybieranie interfejsu sieciowego](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z interfejsami sieciowym dołączonymi do maszyny wirtualnej. Na poprzedniej ilustracji maszyna wirtualna ma tylko jeden interfejs sieciowy. Jeśli maszyna wirtualna ma wiele interfejsów sieciowych, zostaną one wyświetlone, a ty wybierzesz interfejs sieciowy, z którym chcesz skojarzyć publiczny adres IP.

4. Wybierz **pozycję Konfiguracje** adresów IP, a następnie wybierz konfigurację adresu IP, jak pokazano na poniższej ilustracji:

   ![Wybieranie konfiguracji adresu IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z konfiguracjami adresów IP dla interfejsu sieciowego. Na poprzedniej ilustracji interfejs sieciowy ma jedną konfigurację adresu IP. Jeśli interfejs sieciowy ma wiele konfiguracji adresów IP, wszystkie one będą wyświetlane na liście i należy wybrać konfigurację adresu IP, z którą chcesz skojarzyć publiczny adres IP.

5. Wybierz **pozycję Włączone,** a następnie wybierz **pozycję Adres IP ( Skonfiguruj wymagane *ustawienia*).** Wybierz istniejący publiczny adres IP, co spowoduje automatyczne zamknięcie **pola Wybierz publiczny adres IP.** Jeśli nie masz dostępnych publicznych adresów IP na liście, musisz je utworzyć. Aby dowiedzieć się, jak to zrobić, [zobacz Tworzenie publicznego adresu IP.](virtual-network-public-ip-address.md#create-a-public-ip-address) Wybierz **pozycję** Zapisz , jak pokazano na ilustracji poniżej, a następnie zamknij pole konfiguracji adresu IP.

   ![Włączanie publicznego adresu IP](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Wyświetlane publiczne adresy IP to te, które istnieją w tym samym regionie co maszyna wirtualna. Jeśli masz wiele publicznych adresów IP utworzonych w regionie, wszystkie zostaną wyświetlone tutaj. Jeśli jakiekolwiek z nich są wyszarzone, jest to spowodowane tym, że adres jest już skojarzony z innym zasobem.

6. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP, jak pokazano na ilustracji poniżej. Może mi potrwać kilka sekund, aż adres IP zostanie wyświetlony.

   ![Wyświetlanie przypisanego publicznego adresu IP](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure Zakresy adresów IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli chcesz przypisać adres z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

7. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj w** poleceniach interfejsu wiersza polecenia, które należy wykonać. Wybranie **opcji Wypróbuj** wywołuje Cloud Shell, za pomocą których możesz zalogować się do konta platformy Azure.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie w powłoce Bash, zaloguj się do platformy Azure za pomocą polecenia `az login` .
2. Publiczny adres IP jest skojarzony z konfiguracją adresu IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj [polecenia az network nic-ip-config update,](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) aby skojarzyć publiczny adres IP z konfiguracją adresu IP. Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic,* który istnieje w grupie zasobów o nazwie *myResourceGroup.*
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj [polecenia az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby go utworzyć. Na przykład następujące polecenie tworzy publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów *o nazwie myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej na temat wszystkich ustawień publicznych adresów IP, zobacz [Tworzenie publicznego adresu IP.](virtual-network-public-ip-address.md#create-a-public-ip-address) Adres jest przypisywany z puli publicznych adresów IP używanych dla każdego regionu świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj [polecenia az vm nic list,](/cli/azure/vm/nic#az_vm_nic_list) aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *nazwa interfejsu sieciowego to myVMVMNic.*

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla interfejsu sieciowego, pobierz je za pomocą polecenia [az network nic ip-config list.](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) Na przykład następujące polecenie wyświetla listę nazw konfiguracji adresów IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP za pomocą polecenia [az vm list-ip-addresses.](/cli/azure/vm#az_vm_list_ip_addresses) W poniższym przykładzie pokazano adresy IP przypisane do istniejącej maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup.*

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli musisz przypisać adres z określonej puli w regionie, użyj prefiksu [publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="powershell"></a>PowerShell

Zainstaluj [program PowerShell](/powershell/azure/install-az-ps)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Program PowerShell jest wstępnie zainstalowany i skonfigurowany do używania z Twoim kontem. Wybierz przycisk **Wypróbuj w** poleceniach programu PowerShell, które są poniżej. Wybranie **opcji Wypróbuj** wywołuje Cloud Shell, za pomocą których możesz zalogować się do konta platformy Azure.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się do platformy Azure za pomocą polecenia `Connect-AzAccount` .
2. Publiczny adres IP jest skojarzony z konfiguracją adresu IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj poleceń [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) i [Get-AzVirtualNetworkSubnetConfig,](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) aby pobrać sieć wirtualną i podsieć, w których znajduje się interfejs sieciowy. Następnie użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby uzyskać interfejs sieciowy, i polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) w celu uzyskania istniejącego publicznego adresu IP. Następnie użyj polecenia [Set-AzNetworkInterfaceIpConfig,](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) aby skojarzyć publiczny adres IP z konfiguracją adresu IP, i polecenia [Set-AzNetworkInterface,](/powershell/module/Az.Network/Set-AzNetworkInterface) aby zapisać nową konfigurację adresu IP w interfejsie sieciowym.

   Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją ip o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic,* który istnieje w podsieci o nazwie *myVMSubnet* w sieci wirtualnej o nazwie *myVMVNet.* Wszystkie zasoby znajdują się w grupie zasobów o *nazwie myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj polecenia [New-AzPublicIpAddress,](/powershell/module/Az.Network/New-AzPublicIpAddress) aby go utworzyć. Na przykład następujące polecenie  tworzy dynamiczny publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup* w *regionie eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej na temat wszystkich ustawień publicznego adresu IP, zobacz [Tworzenie publicznego adresu IP.](virtual-network-public-ip-address.md#create-a-public-ip-address) Adres jest przypisywany z puli publicznych adresów IP używanych dla każdego regionu świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure Zakresy adresów IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [Get-AzVM,](/powershell/module/Az.Compute/Get-AzVM) aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych nazwa interfejsu sieciowego *to myVMVMNic.*
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, w których znajduje się interfejs sieciowy, użyj polecenia , `Get-AzNetworkInterface` aby wyświetlić informacje. Na przykład następujące polecenie pobiera informacje o sieci wirtualnej i podsieci dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych nazwa sieci wirtualnej *to myVMVNET,* a *myVMSubnet* to nazwa podsieci.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla interfejsu sieciowego, pobierz je za pomocą polecenia [Get-AzNetworkInterface.](/powershell/module/Az.Network/Get-AzNetworkInterface) Na przykład następujące polecenie wyświetla listę nazw konfiguracji adresów IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych nazwa konfiguracji adresu IP to *ipconfigmyVM.*
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP za pomocą polecenia [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) W poniższym przykładzie pokazano adres przypisany do publicznego adresu IP o nazwie *myVMPublicIP* w grupie zasobów *o nazwie myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Jeśli nie znasz nazwy publicznego adresu IP przypisanego do konfiguracji adresu IP, uruchom następujące polecenia, aby go uzyskać:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych *myVMPublicIP* jest nazwą publicznego adresu IP przypisanego do konfiguracji adresu IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie świadczenia usługi Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [Microsoft Azure zakresów adresów IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli musisz przypisać adres z określonej puli w regionie, użyj prefiksu [publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="allow-network-traffic-to-the-vm"></a>Zezwalaj na ruch sieciowy do maszyny wirtualnej

Zanim będzie można nawiązać połączenie z publicznym adresem IP z Internetu, upewnij się, że masz otwarte niezbędne porty w dowolnej sieciowej grupie zabezpieczeń, która mogła zostać skojarzona z interfejsem sieciowym, podsieci, do których należy interfejs sieciowy, lub w obu tych grupach. Mimo że grupy zabezpieczeń filtrują ruch do prywatnego adresu IP interfejsu sieciowego, gdy przychodzący ruch internetowy dociera do publicznego adresu IP, platforma Azure tłumaczy adres publiczny na prywatny adres IP, więc jeśli sieciowa grupa zabezpieczeń uniemożliwia przepływ ruchu, komunikacja z publicznym adresem IP kończy się niepowodzeniem. Obowiązujące reguły zabezpieczeń dla interfejsu sieciowego i jego podsieci można wyświetlić przy użyciu [portalu,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)interfejsu [wiersza polecenia](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)lub programu [PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Następne kroki

Zezwalaj na przychodzący ruch internetowy do maszyny wirtualnej przy użyciu sieciowej grupy zabezpieczeń. Aby dowiedzieć się, jak utworzyć sieciową grupę zabezpieczeń, zobacz [Praca z sieciowymi grupami zabezpieczeń.](manage-network-security-group.md#work-with-network-security-groups) Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Grupy zabezpieczeń](./network-security-groups-overview.md).
