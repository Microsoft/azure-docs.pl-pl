---
title: Kierowanie ruchu sieciowego — interfejs wiersza polecenia platformy Azure | Microsoft Docs
description: W tym artykule dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 027165f797977311fd77f3cd3e626b126c26e47b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494676"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Kierowanie ruchu sieciowego za pomocą tabeli tras przy użyciu interfejsu wiersza polecenia platformy Azure

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Przed utworzeniem tabeli tras Utwórz grupę zasobów za pomocą metody [AZ Group Create](/cli/azure/group) dla wszystkich zasobów utworzonych w tym artykule. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz tabelę tras za pomocą [AZ Network Route-Table Create](/cli/azure/network/route-table#az-network-route-table-create). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Tworzenie trasy

Utwórz trasę w tabeli tras za pomocą [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

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

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną z jedną podsiecią za pomocą [AZ Network VNET Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Utwórz dwie dodatkowe podsieci za pomocą [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet).

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

Skojarz tabelę tras *myRouteTablePublic* z podsiecią *publiczną* przy użyciu [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

Utwórz urządzenie WUS w podsieci *DMZ* przy użyciu [AZ VM Create](/cli/azure/vm). Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy i przypisuje publiczny adres IP do maszyny wirtualnej. `--public-ip-address ""`Parametr instruuje platformę Azure, aby nie utworzył i przypisał publicznego adresu IP do maszyny wirtualnej, ponieważ maszyna wirtualna nie musi być połączona z Internetem. Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj do następnego kroku, dopóki platforma Azure nie zakończy tworzenia maszyny wirtualnej i nie zwróci danych wyjściowych dotyczących maszyny wirtualnej. 

Aby karta sieciowa mogła przekazywać dalej wysyłany do niej ruch sieciowy, który nie jest przeznaczony dla jej własnego adresu IP, musi być włączone przekazywanie adresu IP dla interfejsu sieciowego. Włącz przekazywanie IP dla interfejsu sieciowego za pomocą funkcji [AZ Network nic Update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włącz przekazywanie adresów IP w ramach systemu operacyjnego maszyny wirtualnej za pomocą funkcji [AZ VM Extension Set](/cli/azure/vm/extension):

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

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby można było sprawdzić, czy ruch z podsieci *publicznej* jest kierowany do podsieci *prywatnej* za pośrednictwem urządzenie WUS w późniejszym kroku. 

Utwórz maszynę wirtualną w podsieci *publicznej* za pomocą [AZ VM Create](/cli/azure/vm). `--no-wait`Parametr umożliwia platformie Azure wykonywanie polecenia w tle, dzięki czemu można przejść do następnego polecenia. W celu uproszczenia tego artykułu jest używane hasło. Klucze są zwykle używane w wdrożeniach produkcyjnych. W przypadku korzystania z kluczy należy również skonfigurować przekazywanie agentów SSH. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją klienta SSH. Zastąp wartość `<replace-with-your-password>` w poniższym poleceniu, wybierając wybrane hasło.

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

Utwórz maszynę wirtualną w podsieci *prywatnej* .

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następującego przykładu: 

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

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną *myVmPrivate* . Zastąp *\<publicIpAddress>* wartość publicznym adresem IP maszyny wirtualnej. W powyższym przykładzie adres IP to *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Po wyświetleniu monitu o podanie hasła wprowadź hasło wybrane w obszarze [Tworzenie maszyn wirtualnych](#create-virtual-machines).

Użyj następującego polecenia, aby zainstalować trasę śledzenia na maszynie wirtualnej *myVmPrivate* :

```bash
sudo apt-get install traceroute
```

Użyj poniższego polecenia, aby przetestować Routing ruchu sieciowego do maszyny wirtualnej *myVmPublic* z maszyny wirtualnej *myVmPrivate* .

```bash
traceroute myVmPublic
```

Odpowiedź jest podobna do poniższego przykładu:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Trasy domyślne platformy Azure, kierowanie ruchu bezpośrednio między podsieciami. 

Użyj następującego polecenia, aby SSH do maszyny wirtualnej *myVmPublic* z maszyny wirtualnej *myVmPrivate* :

```bash
ssh azureuser@myVmPublic
```

Użyj następującego polecenia, aby zainstalować trasę śledzenia na maszynie wirtualnej *myVmPublic* :

```bash
sudo apt-get install traceroute
```

Użyj poniższego polecenia, aby przetestować Routing ruchu sieciowego do maszyny wirtualnej *myVmPrivate* z maszyny wirtualnej *myVmPublic* .

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

Zamknij sesje SSH zarówno na maszynach wirtualnych *myVmPublic* , jak i *myVmPrivate* .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono tabelę tras i skojarzono ją z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrożono różne wstępnie skonfigurowane urządzenia NVA wykonujące funkcje sieciowe, takie jak zapora i optymalizacja sieci WAN, z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się, jak to zrobić, zobacz [ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources-cli.md).
