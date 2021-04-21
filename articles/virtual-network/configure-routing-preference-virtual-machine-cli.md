---
title: Konfigurowanie preferencji routingu dla maszyny wirtualnej — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć maszynę wirtualną z publicznym adresem IP z preferencją routingu przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764481"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule przedstawiono sposób konfigurowania preferencji routingu dla maszyny wirtualnej. Ruch związany z Internetem z maszyny wirtualnej będzie trasowany za pośrednictwem sieci isp po wybraniu **Internetu** jako opcji preferencji routingu. Domyślny routing jest za pośrednictwem sieci globalnej firmy Microsoft.

W tym artykule pokazano, jak utworzyć maszynę wirtualną z publicznym adresem IP ustawionym na trasę ruchu za pośrednictwem publicznego Internetu przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję poleceń i zaloguj się do platformy Azure za pomocą polecenia `az login` .
2. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów w regionie świadczenia usługi Azure Wschodnie usa:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do maszyn wirtualnych z Internetu, należy utworzyć publiczny adres IP. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Poniższy przykład tworzy publiczny adres IP typu preferencji routingu *Internet w* regionie *Wschodnie usa:*

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Przed wdrożeniem maszyny wirtualnej należy utworzyć zasoby sieciowe — sieciową grupę zabezpieczeń, sieć wirtualną i wirtualną kartę sieciową.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń dla reguł, które będą zarządzać komunikacją przychodzącymi i wychodzącymi w sieci wirtualnej za pomocą [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVNET* z podsieciami *mySubNet:*

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Tworzenie karty sieciowej

Utwórz wirtualną kartę sieciową dla maszyny wirtualnej za pomocą [az network nic create](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy wirtualną kartę sieciową, która zostanie dołączona do maszyny wirtualnej.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy maszynę wirtualną z systemem Windows Server 2019 i wymagane składniki sieci wirtualnej, jeśli jeszcze nie istnieją.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP.](routing-preference-overview.md)
- Dowiedz się więcej o [publicznych adresach IP na](./public-ip-addresses.md#public-ip-addresses) platformie Azure.
- Dowiedz się więcej o [ustawieniach publicznego adresu IP.](virtual-network-public-ip-address.md#create-a-public-ip-address)
