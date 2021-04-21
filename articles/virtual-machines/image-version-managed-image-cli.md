---
title: Klonowanie obrazu zarządzanego do wersji obrazu za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak sklonować obraz zarządzany do wersji obrazu w Shared Image Gallery przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d0644b9ec9009fe5d1db7701834cb9788f86ab0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790171"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klonowanie obrazu zarządzanego do wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
Jeśli masz istniejący obraz zarządzany, który chcesz sklonować do Shared Image Gallery, możesz utworzyć obraz Shared Image Gallery bezpośrednio z obrazu zarządzanego. Po przetestowania nowego obrazu możesz usunąć źródłowy obraz zarządzany. Możesz również przeprowadzić migrację z obrazu zarządzanego do obrazu Shared Image Gallery [programie PowerShell.](image-version-managed-image-powershell.md)

Obrazy w galerii obrazów mają dwa składniki, które utworzymy w tym przykładzie:
- Definicja **obrazu zawiera** informacje o obrazie i wymagania dotyczące jego używania. Dotyczy to również tego, czy obraz jest systemem Windows lub Linux, wyspecjalizowany lub uogólniony, informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- Wersja **obrazu służy** do tworzenia maszyny wirtualnej podczas korzystania z maszyny Shared Image Gallery. W razie potrzeby w środowisku może być potrzebnych wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów mogą być używane wiele razy.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć [istniejącą](shared-images-cli.md)Shared Image Gallery . 

Aby ukończyć przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionych maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Capture a managed image (Przechwytywanie obrazu zarządzanego).](./linux/capture-image.md) Jeśli obraz zarządzany zawiera dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem zastąp nazwy grup zasobów i maszyn wirtualnych w razie potrzeby.



## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Ponieważ obrazy zarządzane są zawsze uogólnione, utworzysz definicję obrazu przy użyciu klasy `--os-state generalized` dla uogólnionych obrazów.

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, łączników i kropek. 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

W tym przykładzie definicja obrazu nosi nazwę *myImageDefinition* i jest dla [uogólniony obraz systemu](./shared-image-galleries.md#generalized-and-specialized-images) operacyjnego Linux. Aby utworzyć definicję obrazów przy użyciu systemu operacyjnego Windows, `--os-type Windows` użyj . 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersje za pomocą [narzędzia az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). Musisz przekazać identyfikator obrazu zarządzanego, aby użyć go jako podstawy do utworzenia wersji obrazu. Aby uzyskać [identyfikatory obrazów,](/cli/azure/image?view#az_image_list) możesz użyć az image list. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą być w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0* i zamierzamy  utworzyć 1 replikę w regionie Południowo-środkowe usa i 1 replikę w regionie Wschodnie usa *2* przy użyciu magazynu strefowo nadmiarowego. Podczas wybierania regionów docelowych do replikacji należy również uwzględnić *region* źródłowy jako element docelowy replikacji.

Przekaż identyfikator obrazu zarządzanego w `--managed-image` parametrze .


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Możesz również przechowywać wszystkie repliki wersji obrazu w magazynie [strefowo nadmiarowym,](../storage/common/storage-redundancy.md) dodając `--storage-account-type standard_zrs` podczas tworzenia wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na [platformie uogólnionych wersji obrazu.](vm-generalized-image-version-cli.md)

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)