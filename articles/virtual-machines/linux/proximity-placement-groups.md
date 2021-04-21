---
title: Tworzenie grupy umieszczania w pobliżu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się więcej o tworzeniu i używaniu grup umieszczania w pobliżu dla maszyn wirtualnych na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771447"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Wdrażanie maszyn wirtualnych w grupach umieszczania w pobliżu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby jak najbardziej zbliżyć się do maszyn wirtualnych w celu osiągnięcia najmniejszego możliwego opóźnienia, należy je wdrożyć w grupie [umieszczania w pobliżu.](../co-location.md#proximity-placement-groups)

Grupa umieszczania w pobliżu to grupa logiczna używana do upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania w pobliżu są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienie.


## <a name="create-the-proximity-placement-group"></a>Tworzenie grupy umieszczania w pobliżu
Utwórz grupę umieszczania w pobliżu przy użyciu programu [`az ppg create`](/cli/azure/ppg#az_ppg_create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista grup umieszczania w pobliżu

Listę wszystkich grup umieszczania w pobliżu można wyświetlić za pomocą [narzędzia az ppg list](/cli/azure/ppg#az_ppg_list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w grupie umieszczania w pobliżu przy użyciu [nowego az vm](/cli/azure/vm#az_vm_create).

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

Maszynę wirtualną można wyświetlić w grupie umieszczania w pobliżu przy użyciu [narzędzia az ppg show](/cli/azure/ppg#az_ppg_show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Zestawy dostępności
Zestaw dostępności można również utworzyć w grupie umieszczania w pobliżu. Użyj tego samego parametru z parametrem `--ppg` [az vm availability-set create,](/cli/azure/vm/availability-set#az_vm_availability_set_create) aby utworzyć zestaw dostępności, a wszystkie maszyny wirtualne w zestawie dostępności również zostaną utworzone w tej samej grupie umieszczania w pobliżu.

## <a name="scale-sets"></a>Zestawy skalowania

Zestaw skalowania można również utworzyć w grupie umieszczania w pobliżu. Użyj tego samego parametru z parametrem `--ppg` [az vmss create,](/cli/azure/vmss#az_vmss_create) aby utworzyć zestaw skalowania, a wszystkie wystąpienia zostaną utworzone w tej samej grupie umieszczania w pobliżu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [poleceniach interfejsu wiersza polecenia](/cli/azure/ppg) platformy Azure dla grup umieszczania w pobliżu.