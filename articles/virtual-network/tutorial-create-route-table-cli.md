---
title: Przekieruj ruch sieciowy — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule dowiesz się, jak rozsyłać ruch sieciowy za pomocą tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762501"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Route network traffic with a route table using the Azure CLI

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Przed utworzeniem tabeli tras utwórz grupę zasobów za pomocą az [group create dla](/cli/azure/group) wszystkich zasobów utworzonych w tym artykule. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz tabelę tras za pomocą [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Tworzenie trasy

Utwórz trasę w tabeli tras za pomocą az [network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną z jedną podsiecią za pomocą [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Utwórz dwie dodatkowe podsieci za pomocą az [network vnet subnet create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

*Skojarz tabelę tras myRouteTablePublic* z *podsiecią publiczną* za pomocą [az network vnet subnet update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

Utwórz urządzenie WUS w podsieci *DMZ* za pomocą [az vm create](/cli/azure/vm). Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy i przypisuje do maszyny wirtualnej publiczny adres IP. Parametr instruuje platformę Azure, aby nie tworzyć i przypisywać publicznego adresu IP do maszyny wirtualnej, ponieważ maszyna wirtualna nie musi być połączona `--public-ip-address ""` z Internetu. Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie przejdź do następnego kroku, dopóki platforma Azure nie zakończy tworzenia maszyny wirtualnej i nie zwróci danych wyjściowych dotyczących maszyny wirtualnej. 

Aby karta sieciowa mogła przekazywać dalej wysyłany do niej ruch sieciowy, który nie jest przeznaczony dla jej własnego adresu IP, musi być włączone przekazywanie adresu IP dla interfejsu sieciowego. Włącz przekazywanie ip dla interfejsu sieciowego za pomocą [az network nic update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włącz przekazywanie ip w systemie operacyjnym maszyny wirtualnej za pomocą [az vm extension set:](/cli/azure/vm/extension)

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Wykonanie polecenia może potrwać do minuty.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby w późniejszym kroku sprawdzić, czy ruch z podsieci *Public* jest przekierowyowany do podsieci *Private* za pośrednictwem urządzenia WUS. 

Utwórz maszynę wirtualną w podsieci *Public* za pomocą [az vm create](/cli/azure/vm). Parametr `--no-wait` umożliwia platformie Azure wykonanie polecenia w tle, dzięki czemu można przejść do następnego polecenia. Aby usprawnić ten artykuł, używane jest hasło. Klucze są zwykle używane we wdrożeniach produkcyjnych. Jeśli używasz kluczy, musisz również skonfigurować przekazywanie agenta SSH. Aby uzyskać więcej informacji, zobacz dokumentację klienta SSH. Zastąp `<replace-with-your-password>` w poniższym poleceniu swoim hasłem.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Utwórz maszynę wirtualną w podsieci *Private.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetla informacje podobne do następujących: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej z Internetu w późniejszym kroku.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną *wirtualną myVmPrivate.* Zastąp *\<publicIpAddress>* publicznym adresem IP maszyny wirtualnej. W powyższym przykładzie adres IP to *13.90.242.231.*

```bash
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o hasło wprowadź hasło wybrane w te sposób, [aby utworzyć maszyny wirtualne.](#create-virtual-machines)

Użyj następującego polecenia, aby zainstalować trasę śledzenia na maszynie *wirtualnej myVmPrivate:*

```bash
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing dla ruchu sieciowego do maszyny wirtualnej *myVmPublic* z *maszyny wirtualnej myVmPrivate.*

```bash
traceroute myVmPublic
```

Odpowiedź jest podobna do poniższego przykładu:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Trasy domyślne platformy Azure, które kieruje ruch bezpośrednio między podsieciami. 

Użyj następującego polecenia, aby na maszynie wirtualnej *myVmPublic* na maszynie wirtualnej myVmPrivate na maszynie wirtualnej SSH nawiążesz z maszyną wirtualną *myVmPrivate:*

```bash
ssh azureuser@myVmPublic
```

Użyj następującego polecenia, aby zainstalować trasę śledzenia na *maszynie wirtualnej myVmPublic:*

```bash
sudo apt-get install traceroute
```

Użyj następującego polecenia, aby przetestować routing dla ruchu sieciowego do maszyny *wirtualnej myVmPrivate* z *maszyny wirtualnej myVmPublic.*

```bash
traceroute myVmPrivate
```

Odpowiedź jest podobna do poniższego przykładu:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Teraz możesz zobaczyć, że pierwszym przeskokiem jest 10.0.2.4, czyli prywatny adres IP urządzenia NVA. Drugim przeskokiem jest 10.0.1.4, prywatny adres IP maszyny wirtualnej *myVmPrivate*. Trasa dodana do tabeli tras *myRouteTablePublic* i powiązana z podsiecią *Public* spowodowała, że platforma Azure skierowała ruch przez urządzenie NVA, a nie bezpośrednio do podsieci *Private*.

Zamknij sesje SSH dla maszyn wirtualnych *myVmPublic* *i myVmPrivate.*

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zasoby w niej dostępne nie będą już potrzebne, użyj przycisku [az group delete.](/cli/azure/group)

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono tabelę tras i skojarzono ją z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrożono różne wstępnie skonfigurowane urządzenia NVA wykonujące funkcje sieciowe, takie jak zapora i optymalizacja sieci WAN, z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się, jak to zrobić, [zobacz Ograniczanie dostępu sieciowego do zasobów PaaS.](tutorial-restrict-network-access-to-resources-cli.md)
