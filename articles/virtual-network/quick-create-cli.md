---
title: Tworzenie sieci wirtualnej — Szybki start — Interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure. Dzięki sieci wirtualnej zasoby platformy Azure komunikują się ze sobą i z Internetem.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3f4cd0a09c64c8c89116bf3a7dec40bae9f05f71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199071"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Dzięki sieci wirtualnej zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie ze sobą i z Internetem. 

W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, wdrożysz w niej dwie maszyny wirtualne. Następnie nawiążesz połączenie z jedną z maszyn wirtualnych z Internetu i rozpoczniesz prywatną komunikację przez nową sieć wirtualną.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki Start wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów, która będzie hostowała tę sieć wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Ten przykład tworzy grupę zasobów o nazwie **CreateVNetQS-RG** w lokalizacji **Wschodnie** :

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Ten przykład tworzy domyślną sieć wirtualną o nazwie **myVNet** z jedną podsiecią o nazwie **default**:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). 

Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. 

Opcja `--no-wait` tworzy maszynę wirtualną w tle. Możesz przejść do następnego kroku. 

Ten przykład tworzy maszynę wirtualną o nazwie **myVM1**:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Użyto `--no-wait` opcji w poprzednim kroku. Możesz kontynuować i utworzyć drugą maszynę wirtualną o nazwie **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Komunikat wyjściowy interfejsu wiersza polecenia platformy Azure

Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Kiedy platforma Azure utworzy maszyny wirtualne, interfejs wiersza polecenia platformy Azure zwróci dane wyjściowe podobne do poniższych:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>Publiczny adres IP maszyny wirtualnej

Aby uzyskać publiczny adres IP **myVM2**, użyj [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

W tym poleceniu Zastąp `<publicIpAddress>` wartość publicznym adresem IP maszyny wirtualnej **myVM2** :

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Aby potwierdzić prywatną komunikację między maszynami wirtualnymi **myVM2** i **myVM1** , wprowadź następujące polecenie:

```bash
ping myVM1 -c 4
```

Otrzymasz cztery odpowiedzi z adresu *10.0.0.4*.

Zakończ sesję SSH z maszyną wirtualną **myVM2** .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przyjęto następujące założenia: 

* Utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. 
* Z Internetu nawiązano połączenie z jedną z maszyn wirtualnych, a następnie rozpoczęto prywatną komunikację między tymi dwiema maszynami wirtualnymi.

Prywatna komunikacja między maszynami wirtualnymi nie jest ograniczona w sieci wirtualnej. 

Przejdź do następnego artykułu, aby dowiedzieć się więcej o konfigurowaniu różnych typów komunikacji sieciowej maszyny wirtualnej:
> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md)
