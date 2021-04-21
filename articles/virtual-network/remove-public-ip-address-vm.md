---
title: Skojarzenie publicznego adresu IP z maszyną wirtualną platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skojarzyć publiczny adres IP z maszyny wirtualnej
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776544"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Skojarzenie publicznego adresu IP z maszyną wirtualną platformy Azure 

Z tego artykułu dowiesz się, jak skojarzyć publiczny adres IP z maszyną wirtualną platformy Azure.

Aby skojarzyć publiczny adres IP [Azure Portal z](#azure-portal)maszyną wirtualną, możesz użyć interfejsu wiersza polecenia platformy [Azure](#azure-cli) lub programu [PowerShell.](#powershell)

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj maszynę wirtualną, z której chcesz usunąć skojarzenie publicznego adresu IP, a następnie wybierz ją.
3. Na stronie maszyny wirtualnej wybierz **pozycję Przegląd** i wybierz publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybieranie publicznego adresu IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na stronie publicznego adresu IP wybierz pozycję **Przegląd,** a następnie wybierz pozycję **Skojarz**, jak pokazano na poniższej ilustracji:

    ![Desociate Public IP (Cosocij publiczny adres IP)](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. W **skojarz publiczny adres IP**, wybierz **tak.**

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj w** poleceniach interfejsu wiersza polecenia, które należy wykonać. Wybranie **opcji Wypróbuj** wywołuje Cloud Shell, za pomocą których możesz zalogować się do konta platformy Azure.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie w powłoce Bash, zaloguj się do platformy Azure za pomocą polecenia `az login` .
2. Publiczny adres IP jest skojarzony z konfiguracją adresu IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj [polecenia az network nic-ip-config update,](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) aby skojarzyć publiczny adres IP z konfiguracji adresu IP. Poniższy przykład umożliwia skojarzenie publicznego adresu IP o nazwie *myVMPublicIP* z konfiguracji adresu IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* dołączonego do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup.*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj [polecenia az vm nic list,](/cli/azure/vm/nic#az_vm_nic_list) aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *nazwa interfejsu sieciowego to myVMVMNic.*

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla interfejsu sieciowego, użyj polecenia [az network nic ip-config list,](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji publicznego adresu IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Jeśli nie znasz nazwy konfiguracji publicznego adresu IP dla interfejsu sieciowego, użyj polecenia [az network nic ip-config show,](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji publicznego adresu IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Zainstaluj [program PowerShell](/powershell/azure/install-az-ps)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Program PowerShell jest wstępnie zainstalowany i skonfigurowany do używania z Twoim kontem. Wybierz przycisk **Wypróbuj w** poleceniach programu PowerShell, które należy wykonać. Wybranie **opcji Wypróbuj** wywołuje Cloud Shell, za pomocą których można zalogować się do konta platformy Azure.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się do platformy Azure za pomocą polecenia `Connect-AzAccount` .
2. Publiczny adres IP jest skojarzony z konfiguracją adresu IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby uzyskać interfejs sieciowy. Ustaw wartość Publiczny adres IP na null, a następnie użyj polecenia [Set-AzNetworkInterface,](/powershell/module/Az.Network/Set-AzNetworkInterface) aby zapisać nową konfigurację adresu IP w interfejsie sieciowym.

   Poniższy przykład umożliwia skojarzenie publicznego adresu IP o nazwie *myVMPublicIP* z interfejsu sieciowego o nazwie *myVMVMNic* dołączonego do maszyny wirtualnej *o nazwie myVM.* Wszystkie zasoby znajdują się w grupie zasobów o *nazwie myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [Get-AzVM,](/powershell/module/Az.Compute/Get-AzVM) aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *o nazwie myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych *nazwa myVMVMNic* jest nazwą interfejsu sieciowego.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla interfejsu sieciowego, pobierz je za pomocą polecenia [Get-AzNetworkInterface.](/powershell/module/Az.Network/Get-AzNetworkInterface) Na przykład następujące polecenie wyświetla listę nazw konfiguracji adresów IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów *o nazwie myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Dane wyjściowe zawierają jeden lub więcej wierszy, które są podobne do następującego przykładu. W przykładowych danych wyjściowych nazwa konfiguracji adresu IP to *ipconfigmyVM.*
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skojarzyć publiczny adres IP z maszyną wirtualną.](associate-public-ip-address-vm.md)
