---
title: Wdrażanie maszyn wirtualnych na platformie Azure przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia wdrożyć maszyny wirtualne Azure spot w celu oszczędności kosztów.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a06dea64ffb348477308979fa07c0e2ce822b0c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825400"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Wdrażanie maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z [maszyn wirtualnych platformy Azure](spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](../spot-vms.md#pricing).

Proces tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam jak szczegółowy w [artykule szybki start](./quick-create-cli.md). Po prostu Dodaj parametr "--Priority", ustaw `--eviction-policy` opcję na "Cofnij alokację" (jest to ustawienie domyślne) lub `Delete` , a następnie podaj maksymalną cenę lub `-1` . 


## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Aby tworzyć maszyny wirtualne, musisz uruchomić interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Tworzenie doaktywnej maszyny wirtualnej

Ten przykład pokazuje, jak wdrożyć maszynę wirtualną z systemem Linux, która nie zostanie wykluczona na podstawie ceny. Zasada wykluczenia jest ustawiona na cofnięcie alokacji maszyny wirtualnej, dzięki czemu można ją uruchomić ponownie w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk podstawowy podczas wykluczania maszyny wirtualnej, ustaw na wartość `--eviction-policy` `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę rozliczeniową dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Symulowanie wykluczenia

Możesz [symulować wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej na miejscu, aby przetestować, w jaki sposób aplikacja będzie odistnieć do nagłego wykluczenia. 

Zastąp następujące informacje następującymi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Następne kroki**

Możesz również utworzyć maszynę wirtualną na miejscu przy użyciu [Azure PowerShell](../windows/spot-powershell.md), [portalu](../spot-portal.md)lub [szablonu](spot-template.md).

Zapoznaj się z bieżącymi informacjami o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o cenach dodatkowych. Elementy `meterName` i `skuName` będą zawierać `Spot` .

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
