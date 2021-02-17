---
title: Użyj interfejsu wiersza polecenia, aby wdrożyć Virtual Machines platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia wdrożyć Virtual Machines platformy Azure w celu oszczędności kosztów.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: abb29c0826e38af0bbbc1b59e41234acdaaca0f9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554779"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Wdrażanie Virtual Machines usługi Azure spot przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z [usługi Azure Spot Virtual Machines](../spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines na platformie Azure. Z tego względu Virtual Machines na platformie Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Cennik usługi Azure Virtual Machines w miejscu to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej usługi Azure Spot można ustawić w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to bieżąca cena za maszynę wirtualną platformy Azure lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, pod warunkiem, że dostępna jest pojemność i przydział. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Virtual Machines platformy Azure — cennik](../spot-vms.md#pricing).

Proces tworzenia maszyny wirtualnej platformy Azure w miejscu przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam jak szczegółowy w [artykule szybki start](./quick-create-cli.md). Po prostu Dodaj parametr "--Priority", ustaw `--eviction-policy` opcję na "Cofnij alokację" (jest to ustawienie domyślne) lub `Delete` , a następnie podaj maksymalną cenę lub `-1` . 


## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Aby utworzyć Virtual Machines w miejscu na platformie Azure, musisz uruchomić interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure w miejscu

Ten przykład pokazuje, jak wdrożyć maszynę wirtualną platformy Azure w systemie Linux, która nie zostanie wykluczona na podstawie ceny. Zasada wykluczenia jest ustawiona na cofnięcie alokacji maszyny wirtualnej, dzięki czemu można ją uruchomić ponownie w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk podstawowy podczas wykluczania maszyny wirtualnej, ustaw na wartość `--eviction-policy` `Delete` .

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

Możesz [symulować wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej platformy Azure w celu przetestowania, w jaki sposób aplikacja będzie odstawa w przypadku nagłego wykluczenia. 

Zastąp następujące informacje następującymi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Następne kroki**

Możesz również utworzyć maszynę wirtualną platformy Azure w sieci za pomocą [Azure PowerShell](../windows/spot-powershell.md), [portalu](../spot-portal.md)lub [szablonu](spot-template.md).

Zapoznaj się z bieżącymi informacjami o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o maszynie wirtualnej platformy Azure. Elementy `meterName` i `skuName` będą zawierać `Spot` .

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md).