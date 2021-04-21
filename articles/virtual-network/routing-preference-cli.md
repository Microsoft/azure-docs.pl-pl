---
title: Konfigurowanie preferencji routingu dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć publiczny adres IP z preferencją routingu ruchu internetowego przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776559"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurowanie preferencji routingu dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak skonfigurować preferencję routingu za pośrednictwem sieci ISP **(opcja** internetowa) dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure. Po utworzeniu publicznego adresu IP możesz skojarzyć go z następującymi zasobami platformy Azure dla ruchu przychodzącego i wychodzącego do Internetu:

* Maszyna wirtualna
* Zestaw skalowania maszyn wirtualnych
* Azure Kubernetes Service (AKS)
* Równoważenie obciążenia z Internetu
* Application Gateway
* Azure Firewall

Domyślnie preferencja routingu dla publicznego adresu IP jest ustawiona na globalną sieć firmy Microsoft dla wszystkich usług platformy Azure i może być skojarzona z dowolną usługą platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów w regionie świadczenia usługi Azure **Wschodnie usa:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Utwórz publiczny adres IP z preferencją routingu **typu Internet** przy użyciu polecenia az [network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)w formacie pokazanym poniżej.

Następujące polecenie tworzy nowy publiczny adres IP z **preferencją routingu** internetowego w regionie świadczenia usługi Azure **Wschodnie usa.**

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

> [!NOTE]
>  Obecnie preferencja routingu obsługuje tylko publiczne adresy IP IPV4.

Powyższy utworzony publiczny adres IP można skojarzyć z maszyną [wirtualną](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z systemem Windows lub [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Użyj sekcji interfejsu wiersza polecenia na stronie samouczka: [Kojarzenie](associate-public-ip-address-vm.md#azure-cli) publicznego adresu IP z maszyną wirtualną w celu skojarzenia publicznego adresu IP z maszyną wirtualną. Możesz również skojarzyć utworzony powyżej publiczny adres IP z usługą [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu usługi równoważenia** obciążenia. Publiczny adres IP służy jako wirtualny adres IP (VIP) o zrównoważonym obciążeniu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP.](routing-preference-overview.md) 
- [Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.](configure-routing-preference-virtual-machine-cli.md)
