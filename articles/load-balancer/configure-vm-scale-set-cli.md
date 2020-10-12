---
title: Konfigurowanie zestawu skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 990380d553cc12d1a87b2e1c7ca9b09864801294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333998"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurowanie zestawu skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący moduł równoważenia obciążenia standardowej jednostki SKU w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych.
- Virtual Network platformy Azure dla zestawu skalowania maszyn wirtualnych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się używać interfejsu wiersza polecenia lokalnie, ten artykuł będzie wymagał wersji interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się do platformy Azure.

```azurecli-interactive
az login
```

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
                                