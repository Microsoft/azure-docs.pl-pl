---
title: Konfigurowanie preferencji routingu dla maszyny wirtualnej — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć maszynę wirtualną z publicznym adresem IP z wyborem preferencji routingu za pomocą interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: ccd04a43e6781e8d58234cc382b2739d800e5fe7
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510679"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób konfigurowania preferencji routingu dla maszyny wirtualnej. Ruch związany z Internetem z maszyny wirtualnej będzie kierowany przez sieć usługodawcy internetowego w przypadku wybrania opcji **Internet** jako preferencji routingu. Domyślny Routing odbywa się za pośrednictwem sieci globalnej firmy Microsoft.

W tym artykule pokazano, jak utworzyć maszynę wirtualną z publicznym adresem IP, która jest ustawiona na kierowanie ruchu za pośrednictwem publicznej sieci Internet przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Preferencje routingu są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Jeśli używasz Cloud Shell, przejdź do kroku 2. Otwórz sesję polecenia i zaloguj się do platformy Azure za pomocą usługi `az login` .
2. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure Wschodnie stany USA:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do maszyn wirtualnych z Internetu, należy utworzyć publiczny adres IP. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Poniższy przykład tworzy publiczny adres IP z preferencjami routingu typ *Internet* w regionie *Wschodnie stany USA* :

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

Przed wdrożeniem maszyny wirtualnej należy utworzyć pomocnicze zasoby sieciowe — sieciową grupę zabezpieczeń, sieć wirtualną i wirtualną kartę sieciową.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń dla reguł, które będą zarządzać komunikacją przychodzącą i wychodzącą w sieci wirtualnej za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVNET* z *podsiecią* podsieci:

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

Utwórz wirtualną kartę sieciową dla maszyny wirtualnej za pomocą [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Poniższy przykład tworzy wirtualną kartę sieciową, która zostanie dołączona do maszyny wirtualnej.

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

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Poniższy przykład tworzy maszynę wirtualną z systemem Windows Server 2019 i wymagane składniki sieci wirtualnej, jeśli jeszcze nie istnieją.

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

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP](routing-preference-overview.md).
- Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej o [ustawieniach publicznych adresów IP](virtual-network-public-ip-address.md#create-a-public-ip-address).
