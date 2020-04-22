---
title: Korzystanie z grup miejsc docelowych zbliżeniowych
description: Dowiedz się więcej o tworzeniu i używaniu grup miejsc docelowych zbliżeniowych dla maszyn wirtualnych na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759256"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Wdrażanie maszyn wirtualnych w grupach miejsc docelowych zbliżeniowych przy użyciu interfejsu wiersza polecenia platformy Azure

Aby maszyny wirtualne były jak najbliżej, osiągając możliwie najniższe opóźnienie, należy wdrożyć je w [grupie miejsc docelowych w pobliżu](co-location.md#proximity-placement-groups).

Grupa miejsc docelowych zbliżeniowych jest logicznym grupowaniem używanym w celu upewnienia się, że zasoby obliczeniowe platformy Azure są fizycznie zlokalizowane blisko siebie. Grupy miejsc docelowych zbliżeniowych są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy miejsc docelowych zbliżeniowych
Utwórz grupę miejsc [`az ppg create`](/cli/azure/ppg#az-ppg-create)docelowych zbliżeniowych za pomocą programu . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Wyświetlanie listy grup miejsc docelowych zbliżeniowych

Możesz wyświetlić listę wszystkich grup miejsc docelowych zbliżeniowych za pomocą [listy az ppg](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie miejsc docelowych w pobliżu przy użyciu [nowego az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Maszynę Wirtualną można wyświetlić w grupie miejsc docelowych zbliżeniowych za pomocą [programu AZ ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Zestawy dostępności
Można również utworzyć zestaw dostępności w grupie miejsc docelowych zbliżeniowych. Użyj tego `--ppg` samego parametru z [az vm dostępność zestaw tworzenia,](/cli/azure/vm/availability-set#az-vm-availability-set-create) aby utworzyć zestaw dostępności i wszystkie maszyny wirtualne w zestawie dostępności zostaną również utworzone w tej samej grupie miejsc docelowych zbliżeniowych.

## <a name="scale-sets"></a>Zestawy skalowania

Można również utworzyć zestaw skalowania w grupie miejsc docelowych zbliżeniowych. Użyj tego `--ppg` samego parametru z [az vmss utworzyć,](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) aby utworzyć zestaw skalowania i wszystkie wystąpienia zostaną utworzone w tej samej grupie miejsc docelowych zbliżeniowych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o poleceniach [interfejsu wiersza polecenia platformy Azure](/cli/azure/ppg) dla grup miejsc docelowych zbliżeniowych.
