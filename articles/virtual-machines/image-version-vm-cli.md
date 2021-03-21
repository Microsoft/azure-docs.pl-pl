---
title: Tworzenie obrazu na podstawie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć obraz w galerii obrazów udostępnionych z poziomu maszyny wirtualnej na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c35be8821b6763531b43ec85b10325e91f8bc5f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556864"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Tworzenie wersji obrazu z maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli masz istniejącą maszynę wirtualną, która ma być używana do tworzenia wielu identycznych maszyn wirtualnych, możesz użyć tej maszyny wirtualnej do utworzenia obrazu w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz również utworzyć obraz z maszyny wirtualnej przy użyciu [Azure PowerShell](image-version-vm-powershell.md).

**Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. W przypadku tworzenia maszyny wirtualnej przy użyciu wersji obrazu, wersja obrazu służy do tworzenia dysków dla nowej maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Do wykonania tego artykułu jest wymagana istniejąca Galeria obrazów udostępnionych. 

Musisz również mieć istniejącą maszynę wirtualną na platformie Azure w tym samym regionie, w którym znajduje się Twoja Galeria. 

Jeśli maszyna wirtualna ma dołączony dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.

## <a name="get-information-about-the-vm"></a>Pobierz informacje o maszynie wirtualnej

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne za pomocą polecenia [AZ VM list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Gdy znasz nazwę maszyny wirtualnej i grupę zasobów, w której znajduje się, Pobierz identyfikator maszyny wirtualnej za pomocą polecenia [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Upewnij się, że definicja obrazu jest odpowiednim typem. W przypadku uogólnionej maszyny wirtualnej (przy użyciu programu Sysprep dla systemu Windows lub waagent-anulowania aprowizacji w systemie Linux) należy utworzyć uogólnioną definicję obrazu przy użyciu programu `--os-state generalized` . Jeśli chcesz użyć maszyny wirtualnej bez usuwania istniejących kont użytkowników, Utwórz wyspecjalizowaną definicję obrazu za pomocą polecenia `--os-state specialized` .

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą polecenia [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest dla [WYSPECJALIZOWANEGO](./shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Windows, użyj polecenia `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu z maszyny wirtualnej za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft tworzy 2 repliki w regionie *zachodnie Stany* USA, 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA 2* , korzystając z magazynu Strefowo nadmiarowego. Regiony replikacji muszą zawierać region, w którym znajduje się źródłowa maszyna wirtualna.

Zastąp wartość `--managed-image` w tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać obraz w magazynie w warstwie Premium, dodając `--storage-account-type  premium_lrs` [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md) , dodając `--storage-account-type  standard_zrs` go do wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie [uogólnionego obrazu](vm-generalized-image-version-cli.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).