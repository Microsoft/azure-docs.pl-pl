---
title: Wdrażanie maszyn wirtualnych platformy Azure w punktach wirtualnych za pomocą interfejsu wiersza polecenia
description: Dowiedz się, jak używać interfejsu wiersza polecenia do wdrażania maszyn wirtualnych platformy Azure w miejscu, aby zaoszczędzić koszty.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 3f341271c208cc56a704c836433c33af0129a4ac
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758365"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Wdrażanie maszyn wirtualnych punktowych przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z [maszyn wirtualnych platformy Azure spot](spot-vms.md) umożliwia korzystanie z naszej niewykorzystanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure będzie eksmitować maszyny wirtualne spot. W związku z tym maszyny wirtualne spot są idealne dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperów/testów, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych punktowych są zmienne na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz Ceny maszyn wirtualnych dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Masz możliwość, aby ustawić maksymalną cenę jesteś gotów zapłacić, za godzinę, dla maszyny Wirtualnej. Maksymalna cena maszyny Wirtualnej spot można ustawić w dolarach amerykańskich (USD), przy użyciu miejsc po przecinku do 5. Na przykład wartość `0.98765`będzie maksymalna cena $0.98765 USD za godzinę. Jeśli ustawisz maksymalną `-1`cenę, maszyna wirtualna nie zostanie eksmitowana na podstawie ceny. Cena za maszynę wirtualną będzie bieżącą ceną spotu lub ceną standardowej maszyny Wirtualnej, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i przydział. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Maszyny wirtualne spot - Cennik](spot-vms.md#pricing).

Proces tworzenia maszyny Wirtualnej z spot przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam, jak opisano w [artykule szybki start](/azure/virtual-machines/linux/quick-create-cli). Wystarczy dodać parametr '--priority Spot' i `-1`podać maksymalną cenę lub .


## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Aby utworzyć maszyny wirtualne spot, musisz uruchomić platformę Azure CLI w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się na platformie Azure przy użyciu [logowania az](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Tworzenie maszyny wirtualnej punktowej

W tym przykładzie pokazano, jak wdrożyć maszynę wirtualną punktu systemu Linux, która nie zostanie eksmitowana na podstawie ceny. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Po utworzeniu maszyny Wirtualnej można sprawdzić maksymalną cenę rozliczeniową dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Następne kroki**

Maszynę wirtualną punktu można również utworzyć przy użyciu [programu Azure PowerShell](../windows/spot-powershell.md) lub [szablonu.](spot-template.md)

Jeśli wystąpi błąd, zobacz [Kody błędów](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
