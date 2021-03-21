---
title: Usuń skojarzenie publicznego adresu IP z maszyny wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak usunąć skojarzenie publicznego adresu IP z maszyny wirtualnej
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
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934190"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Usuń skojarzenie publicznego adresu IP z maszyny wirtualnej platformy Azure 

W tym artykule dowiesz się, jak usunąć skojarzenie publicznego adresu IP z maszyny wirtualnej platformy Azure.

Aby usunąć skojarzenie publicznego adresu IP z maszyny wirtualnej, można użyć [Azure Portal](#azure-portal), [interfejsu wiersza polecenia](#azure-cli) platformy Azure lub [programu PowerShell](#powershell) .

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do lub Wyszukaj maszynę wirtualną, z której chcesz usunąć skojarzenie publicznego adresu IP, a następnie wybierz ją.
3. Na stronie maszyna wirtualna wybierz pozycję **Przegląd**, a następnie wybierz publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybierz publiczny adres IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na stronie publiczny adres IP wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Usuń skojarzenie**, jak pokazano na poniższej ilustracji:

    ![Usuń skojarzenie publicznego adresu IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. W obszarze **Usuń skojarzenie publicznego adresu IP** wybierz pozycję **tak**.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** za pomocą poleceń interfejsu wiersza polecenia, które obserwują. Wybranie przycisku **Wypróbuj** wywołuje Cloud Shell, aby można było zalogować się do konta platformy Azure za pomocą usługi.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie w bash, zaloguj się do platformy Azure za pomocą `az login` .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Za pomocą polecenia [AZ Network nic-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) Usuń skojarzenie publicznego adresu IP z konfiguracji adresu IP. Poniższy przykład powoduje skojarzenie publicznego adresu IP o nazwie *myVMPublicIP* z konfiguracji protokołu IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* , który jest dołączony do maszyny wirtualnej o nazwie *MyVM* w grupie zasobów o nazwie Moja *resourceName*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [AZ VM nic list](/cli/azure/vm/nic#az-vm-nic-list) , aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *myVMVMNic* jest nazwą interfejsu sieciowego.

   - Jeśli nie znasz nazwy konfiguracji protokołu IP dla interfejsu sieciowego, użyj polecenia [AZ Network nic IP-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) , aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw publicznych adresów IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moje *zasoby*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Jeśli nie znasz nazwy publicznej konfiguracji adresu IP dla interfejsu sieciowego, użyj polecenia [AZ Network nic IP-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) , aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw publicznych adresów IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moje *zasoby*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Zainstaluj program [PowerShell](/powershell/azure/install-az-ps)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Program PowerShell jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach programu PowerShell. Wybranie przycisku **Wypróbuj** wywołuje Cloud Shell, aby można było zalogować się do konta platformy Azure za pomocą usługi.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się do platformy Azure za pomocą polecenia `Connect-AzAccount` .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego dołączonego do maszyny wirtualnej. Użyj polecenia [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) , aby uzyskać interfejs sieciowy. Ustaw wartość ustawienia publiczny adres IP na wartość null, a następnie użyj polecenia [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) , aby zapisać nową konfigurację adresu IP w interfejsie sieciowym.

   Poniższy przykład powoduje skojarzenie publicznego adresu IP o nazwie *myVMPublicIP* z interfejsu sieciowego o nazwie *myVMVMNic* , który jest dołączony do maszyny wirtualnej o nazwie *myVM*. Wszystkie zasoby znajdują się w grupie zasobów *o nazwie moja grupa zasobów.*
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny wirtualnej, użyj polecenia [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *zasobów*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *myVMVMNic* jest nazwą interfejsu sieciowego.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy konfiguracji protokołu IP dla interfejsu sieciowego, użyj polecenia [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) , aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji protokołu IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie Moja *zasobów*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Dane wyjściowe obejmują co najmniej jeden wiersz, który jest podobny do poniższego przykładu. W przykładowym wyjściu *ipconfigmyVM* jest nazwą konfiguracji adresu IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skojarzyć publiczny adres IP z maszyną wirtualną](associate-public-ip-address-vm.md).
