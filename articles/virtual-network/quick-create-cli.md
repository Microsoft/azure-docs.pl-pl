---
title: Tworzenie sieci wirtualnej — Szybki start — Interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure. Dzięki sieci wirtualnej zasoby platformy Azure komunikują się ze sobą i z Internetem.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056792"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Dzięki sieci wirtualnej zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie ze sobą i z Internetem. W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, wdrożysz w niej dwie maszyny wirtualne. Następnie nawiążesz połączenie z jedną z maszyn wirtualnych z Internetu i rozpoczniesz prywatną komunikację przez nową sieć wirtualną.
## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group-and-a-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów, która będzie hostowała tę sieć wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Ten przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Ten przykład obejmuje tworzenie domyślnej sieci wirtualnej o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie *default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Opcja `--no-wait` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku. W tym przykładzie jest tworzona maszyna wirtualna o nazwie *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Ze względu na użycie opcji `--no-wait` w poprzednim kroku, możesz utworzyć drugą maszynę wirtualną o nazwie *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Komunikat wyjściowy interfejsu wiersza polecenia platformy Azure

Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Kiedy platforma Azure utworzy maszyny wirtualne, interfejs wiersza polecenia platformy Azure zwróci dane wyjściowe podobne do poniższych:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Zanotuj wartość adresu **publicIpAddress**. Użyjesz tego adresu do nawiązania połączenia z maszyną wirtualną z Internetu w następnym kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

W tym poleceniu zastąp `<publicIpAddress>` publicznym adresem IP swojej maszyny wirtualnej *myVm2*:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Aby potwierdzić nawiązanie prywatnej komunikacji między maszynami wirtualnymi *myVm2* i *myVm1*, wprowadź następujące polecenie:

```bash
ping myVm1 -c 4
```

Otrzymasz cztery odpowiedzi z adresu *10.0.0.4*.

Zakończ sesję SSH z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Z Internetu nawiązano połączenie z jedną z maszyn wirtualnych, a następnie rozpoczęto prywatną komunikację między tymi dwiema maszynami wirtualnymi.
Azure umożliwia nieograniczoną komunikację prywatną między maszynami wirtualnymi. Azure domyślnie zezwala tylko na połączenia przychodzące pulpitu zdalnego z maszynami wirtualnymi Windows z Internetu. Przejdź do następnego artykułu, aby dowiedzieć się więcej o konfigurowaniu różnych typów komunikacji sieciowej maszyny wirtualnej:
> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md)
