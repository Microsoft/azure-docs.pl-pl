---
title: Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
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
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221346"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu interfejsu wiersza polecenia platformy Azure

Można utworzyć maszynę wirtualną ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie ulegnie zmianie. Dowiedz się więcej na temat [statycznych publicznych adresów IP](./public-ip-addresses.md#allocation-method). Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub do pracy z prywatnymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP mają [opłata nominalną](https://azure.microsoft.com/pricing/details/ip-addresses), a liczba publicznych adresów IP, których można użyć na subskrypcję, jest [ograniczona](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Poniższe kroki można wykonać z komputera lokalnego lub przy użyciu Azure Cloud Shell. Aby korzystać z komputera lokalnego, upewnij się, że jest [zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby użyć Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu dowolnego poniższego pola polecenia. Cloud Shell umożliwia zalogowanie się do platformy Azure.

1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj się do platformy Azure za pomocą usługi `az login` .
2. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure Wschodnie stany USA:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). `--public-ip-address-allocation=static`Opcja przypisuje statyczny publiczny adres IP do maszyny wirtualnej. Poniższy przykład tworzy maszynę wirtualną Ubuntu ze statycznym publicznym adresem IP podstawowej jednostki SKU o nazwie *myPublicIpAddress*:

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

   Jeśli publiczny adres IP musi być standardową jednostką SKU, Dodaj `--public-ip-sku Standard` do poprzedniego polecenia. Dowiedz się więcej o jednostkach [SKU publicznych adresów IP](./public-ip-addresses.md#sku). Jeśli maszyna wirtualna zostanie dodana do puli zaplecza Azure Load Balancer publicznej, jednostka SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Azure Load Balancer](../load-balancer/skus.md).

4. Wyświetl przypisany publiczny adres IP i upewnij się, że został on utworzony jako statyczny, podstawowy adres jednostki SKU z poleceniem [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Na platformie Azure przypisano publiczny adres IP z adresów używanych w regionie, w którym została utworzona maszyna wirtualna. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny nie rozpoznaje publicznych adresów IP platformy Azure. Mimo że można dodać ustawienia prywatnych adresów IP do systemu operacyjnego, zaleca się, aby nie było to możliwe, chyba że jest to konieczne, a nie do momentu [dodania prywatnego adresu IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o prywatnych adresach [IP](./private-ip-addresses.md) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn wirtualnych z systemem [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
