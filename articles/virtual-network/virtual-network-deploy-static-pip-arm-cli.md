---
title: Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790135"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia platformy Azure

Maszynę wirtualną można utworzyć przy użyciu statycznego publicznego adresu IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy się nie zmienia. Dowiedz się więcej o [statycznych publicznych adresach IP.](./public-ip-addresses.md#allocation-method) Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub pracować z prywatnymi adresami IP, zobacz Dodawanie, zmienianie lub [usuwanie adresów IP.](virtual-network-network-interface-addresses.md) Za publiczne adresy [](https://azure.microsoft.com/pricing/details/ip-addresses)IP naliczana jest nominalna opłata, [a](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) limit liczby publicznych adresów IP, których można używać w ramach subskrypcji.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Poniższe kroki można wykonać z komputera lokalnego lub przy użyciu Azure Cloud Shell. Aby użyć komputera lokalnego, upewnij się, że masz zainstalowany interfejs wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) Aby użyć Azure Cloud Shell, wybierz **pozycję Wypróbuj** w prawym górnym rogu dowolnego następującego pola polecenia. Ten Cloud Shell cię na platformie Azure.

1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję poleceń i zaloguj się do platformy Azure za pomocą polecenia `az login` .
2. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów w regionie świadczenia usługi Azure Wschodnie usa:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Opcja `--public-ip-address-allocation=static` przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną z systemem Ubuntu ze statycznym, podstawowym publicznym adresem IP SKU o nazwie *myPublicIpAddress:*

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Jeśli publiczny adres IP musi być standardową sku, dodaj `--public-ip-sku Standard` do poprzedniego polecenia. Dowiedz się więcej o [publicznych jednostkach SKU adresów IP.](./public-ip-addresses.md#sku) Jeśli maszyna wirtualna zostanie dodana do puli serwerów Azure Load Balancer publicznych, wartość SKU publicznego adresu IP maszyny wirtualnej musi być dopasowana do wartości SKU publicznego adresu IP usługi równoważenia obciążenia. Aby uzyskać szczegółowe informacje, [zobacz Azure Load Balancer](../load-balancer/skus.md).

4. Wyświetl przypisany publiczny adres IP i upewnij się, że został on utworzony jako statyczny, podstawowy adres SKU, za pomocą az [network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Platforma Azure przypisała publiczny adres IP z adresów używanych w regionie, w których utworzono maszynę wirtualną. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w systemie operacyjnym maszyny wirtualnej. System operacyjny nie zna publicznych adresów IP platformy Azure. Mimo że ustawienia prywatnego adresu IP można dodać do systemu operacyjnego, zaleca się nie robić tego, chyba że jest to konieczne, a dopiero po przeczytaniu tematu Dodawanie prywatnego adresu [IP do systemu operacyjnego.](virtual-network-network-interface-addresses.md#private)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP na](./public-ip-addresses.md#public-ip-addresses) platformie Azure
- Dowiedz się więcej o wszystkich [ustawieniach publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej na [temat prywatnych adresów IP](./private-ip-addresses.md) i przypisywania statycznego [prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn [wirtualnych z systemami Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
