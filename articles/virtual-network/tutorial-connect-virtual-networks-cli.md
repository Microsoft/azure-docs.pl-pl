---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej VNet — interfejs wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak połączyć sieci wirtualne za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 855ea936ff91d4c22b0670cd989f91c692c567c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502600"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie dwóch sieci wirtualnych
* Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
* Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
* Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork1* z prefiksem adresu *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Utwórz sieć wirtualną o nazwie *myVirtualNetwork2* z prefiksem adresu *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

Komunikacja równorzędna jest ustanawiana między identyfikatorami sieci wirtualnych, dlatego należy najpierw uzyskać identyfikator każdej sieci wirtualnej za pomocą [AZ Network VNET show](/cli/azure/network/vnet) i Zapisz identyfikator w zmiennej.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Utwórz komunikację równorzędną od *myVirtualNetwork1* do *myVirtualNetwork2* przy użyciu [AZ Network VNET peering Create](/cli/azure/network/vnet/peering). Jeśli `--allow-vnet-access` parametr nie jest określony, Komunikacja równorzędna zostanie ustanowiona, ale nie będzie można z niej przepływać.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

W danych wyjściowych zwróconych po poprzednim wykonaniu polecenia zobaczysz, że **peeringState** jest *inicjowana*. Komunikacja równorzędna pozostaje w stanie *zainicjowania* , dopóki nie utworzysz komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. Utwórz komunikację równorzędną od *myVirtualNetwork2* do *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

W danych wyjściowych zwróconych po poprzednim wykonaniu polecenia zobaczysz, że **peeringState** jest *połączony*. Na platformie Azure zmieniono także stan komunikacji równorzędnej *myVirtualNetwork1-myVirtualNetwork2* , aby *nawiązać połączenie*. Upewnij się, że stan komunikacji równorzędnej dla komunikacji równorzędnej *myVirtualNetwork1-myVirtualNetwork2* został zmieniony na *podłączony* przy użyciu [AZ Network VNET peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Zasoby w jednej sieci wirtualnej nie mogą komunikować się z zasobami w innej sieci wirtualnej, dopóki nie zostanie *nawiązane* **peeringState** połączeń równorzędnych w obu sieciach wirtualnych. 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy MASZYNę wirtualną o nazwie *myVm1* w sieci wirtualnej *myVirtualNetwork1* . Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. `--no-wait`Opcja tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Utwórz MASZYNę wirtualną w sieci wirtualnej *myVirtualNetwork2* .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następującego przykładu: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej z Internetu w późniejszym kroku.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVm2* . Zastąp `<publicIpAddress>` wartość publicznym adresem IP maszyny wirtualnej. W poprzednim przykładzie publiczny adres IP to *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Wyślij polecenie ping do maszyny wirtualnej w *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Otrzymujesz cztery odpowiedzi. 

Zamknij sesję SSH z maszyną wirtualną *myVm2* . 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób łączenia dwóch sieci w tym samym regionie świadczenia usługi Azure przy użyciu komunikacji równorzędnej sieci wirtualnych. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#cli), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Możesz [połączyć własny komputer z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za pośrednictwem sieci VPN i korzystać z zasobów w sieci wirtualnej lub w sieciach wirtualnych połączonych za pomocą komunikacji równorzędnej. Aby skrypty wielokrotnego użytku wykonali wiele zadań objętych artykułami sieci wirtualnych, zobacz [przykłady skryptów](cli-samples.md).
