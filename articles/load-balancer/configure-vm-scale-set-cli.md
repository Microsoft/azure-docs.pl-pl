---
title: Konfigurowanie zestawu skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518213"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

- W subskrypcji, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych, potrzebna jest istniejąca usługa równoważenia obciążenia w warstwie Standardowa.

- Potrzebujesz Virtual Network platformy Azure dla zestawu skalowania maszyn wirtualnych.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdrażanie zestawu skalowania maszyn wirtualnych z istniejącym modułem równoważenia obciążenia

Zastąp wartości w nawiasach nazwami zasobów w konfiguracji.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Poniższy przykład służy do wdrażania zestawu skalowania maszyn wirtualnych przy użyciu:

- Zestaw skalowania maszyn wirtualnych o nazwie **myVMSS**
- Azure Load Balancer o nazwie **myLoadBalancer**
- Pula zaplecza modułu równoważenia obciążenia o nazwie **myBackendPool**
- Virtual Network platformy Azure o nazwie **myVnet**
- Podsieć o nazwie **Nosubnet**
- Grupa zasobów o nazwie Moja **zasobów**
- Obraz serwera Ubuntu dla zestawu skalowania maszyn wirtualnych

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Po utworzeniu zestawu skalowania nie można zmodyfikować portu zaplecza dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyn wirtualnych platformy Azure, zaktualizować port, a następnie ponownie skonfiguruj sondę kondycji.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wdrożenie zestawu skalowania maszyn wirtualnych z istniejącym Azure Load Balancer.  Aby dowiedzieć się więcej na temat usługi Virtual Machine Scale Sets i modułu równoważenia obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
                                