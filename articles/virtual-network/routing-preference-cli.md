---
title: Konfigurowanie preferencji routingu dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć publiczny adres IP z preferencją routingu ruchu internetowego za pomocą interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 2291767c162953f8339fb8cc27e55b96290ef795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665953"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurowanie preferencji routingu dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób konfigurowania preferencji routingu za pośrednictwem sieci usługodawcy internetowego (opcja **internetowa** ) dla publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure. Po utworzeniu publicznego adresu IP możesz skojarzyć go z poniższymi zasobami platformy Azure dla ruchu przychodzącego i wychodzącego do Internetu:

* Maszyna wirtualna
* Zestaw skalowania maszyn wirtualnych
* Azure Kubernetes Service (AKS)
* Moduł równoważenia obciążenia dostępny z Internetu
* Application Gateway
* Azure Firewall

Domyślnie preferencja routingu dla publicznego adresu IP jest ustawiana na sieć globalna firmy Microsoft dla wszystkich usług platformy Azure i może być skojarzona z dowolną usługą platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.49 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure **Wschodnie stany USA** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Utwórz publiczny adres IP z preferencją routingu typu **internetowego** za pomocą polecenia [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)w formacie, jak pokazano poniżej.

Następujące polecenie tworzy nowy publiczny adres IP z preferencją routingu **internetowego** w regionie platformy Azure **Wschodnie stany USA** .

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
>  Obecnie preferencje routingu obsługują tylko publiczne adresy IP w protokole IPV4.

Powyższy utworzony publiczny adres IP można skojarzyć z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Użyj sekcji interfejsu wiersza polecenia na stronie samouczka: [Skojarz publiczny adres IP z maszyną wirtualną](associate-public-ip-address-vm.md#azure-cli) w celu skojarzenia publicznego adresu IP z maszyną wirtualną. Możesz również skojarzyć publiczny adres IP utworzony powyżej z programem przy użyciu [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu** modułu równoważenia obciążenia. Publiczny adres IP służy jako wirtualny adres IP (VIP) o zrównoważonym obciążeniu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP](routing-preference-overview.md). 
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](configure-routing-preference-virtual-machine-cli.md).

