---
title: Tworzenie obrazu z maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć obraz w Shared Image Gallery z maszyny wirtualnej na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7bfe8b1255c88878c2dc4661e9daa3e16397e9f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792277"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Tworzenie wersji obrazu z maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli masz istniejącą maszynę wirtualną, za pomocą których chcesz utworzyć wiele identycznych maszyn wirtualnych, możesz użyć tej maszyny wirtualnej do utworzenia obrazu w Shared Image Gallery przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz również utworzyć obraz z maszyny wirtualnej przy użyciu [Azure PowerShell](image-version-vm-powershell.md).

Wersja **obrazu jest** tym, czego używasz do tworzenia maszyny wirtualnej podczas korzystania z Shared Image Gallery. W razie potrzeby w środowisku może być potrzebnych wiele wersji obrazu. Gdy tworzysz maszynę wirtualną przy użyciu wersji obrazu, wersja obrazu jest używana do tworzenia dysków dla nowej maszyny wirtualnej. Wersje obrazów mogą być używane wiele razy.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejącą Shared Image Gallery. 

Musisz również mieć istniejącą maszynę wirtualną na platformie Azure w tym samym regionie co galeria. 

Jeśli maszyna wirtualna ma dołączony dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem w razie potrzeby zastąp nazwy zasobów.

## <a name="get-information-about-the-vm"></a>Uzyskiwanie informacji o maszynie wirtualnej

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne za pomocą [narzędzia az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

Gdy znasz nazwę maszyny wirtualnej i grupę zasobów, w której się znajduje, uzyskaj identyfikator maszyny wirtualnej przy użyciu narzędzia [az vm get-instance-view.](/cli/azure/vm#az_vm_get_instance_view) 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, łączników i kropek. 

Upewnij się, że definicja obrazu jest właściwym typem. Jeśli maszyna wirtualna jest uogólniona (przy użyciu narzędzia Sysprep dla systemu Windows lub waagent -deprovision dla systemu Linux), należy utworzyć uogólnione definicje obrazu przy użyciu narzędzia `--os-state generalized` . Jeśli chcesz użyć maszyny wirtualnej bez usuwania istniejących kont użytkowników, utwórz wyspecjalizowaną definicję obrazu przy `--os-state specialized` użyciu .

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

W tym przykładzie definicja obrazu nosi nazwę *myImageDefinition* i jest dla [wyspecjalizowanego](./shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję obrazów przy użyciu systemu operacyjnego Windows, `--os-type Windows` użyj . 

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


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z maszyny wirtualnej za pomocą [narzędzia az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą być w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0* i zamierzamy utworzyć  2 repliki w regionie  Zachodnio-środkowe stany USA, 1 replikę w regionie Południowo-środkowe stany USA i 1 replikę w regionie Wschodnie stany USA *2* przy użyciu magazynu strefowo nadmiarowego. Regiony replikacji muszą zawierać region, w których znajduje się źródłową maszynę wirtualną.

Zastąp wartość w `--managed-image` tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

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
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Obraz można również przechowywać w magazynie Premium Storage, dodając magazyn strefowo nadmiarowy lub dodając go podczas `--storage-account-type  premium_lrs` tworzenia wersji [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na pomocą [uogólnionych obrazów przy](vm-generalized-image-version-cli.md) użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)