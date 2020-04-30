---
title: Używanie grup umieszczania w sąsiedztwie
description: Dowiedz się więcej na temat tworzenia i używania grup umieszczania sąsiedztwa dla maszyn wirtualnych na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759256"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Wdrażanie maszyn wirtualnych w grupach umieszczania sąsiedztwa przy użyciu interfejsu wiersza polecenia platformy Azure

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie [grupy umieszczania sąsiedztwa](co-location.md#proximity-placement-groups).

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania zbliżeniowe
Utwórz grupę umieszczania sąsiedztwa [`az ppg create`](/cli/azure/ppg#az-ppg-create)przy użyciu. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Wyświetl listę grup umieszczania zbliżeniowe

Możesz wyświetlić listę wszystkich grup umieszczania w sąsiedztwie przy użyciu polecenia [AZ PPG list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie umieszczania bliskości przy użyciu [nowej AZ VM](/cli/azure/vm#az-vm-create).

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

Maszynę wirtualną można zobaczyć w grupie położenia zbliżeniowe przy użyciu polecenia [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `--ppg` parametru z parametrem [AZ VM Availability-Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) w celu utworzenia zestawu dostępności i wszystkich maszyn wirtualnych w zestawie dostępności również zostanie utworzona w tej samej grupie położenia sąsiedztwa.

## <a name="scale-sets"></a>Zestawy skalowania

Zestaw skalowania można również utworzyć w grupie umieszczania sąsiedztwa. Użyj tego samego `--ppg` parametru z [AZ VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) , aby utworzyć zestaw skalowania i wszystkie wystąpienia zostaną utworzone w tej samej grupie umieszczania sąsiedztwa.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/ppg) dla grup umieszczania sąsiedztwa.
