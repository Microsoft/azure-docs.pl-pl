---
title: Interfejs wiersza polecenia — tworzenie obrazu z migawki lub dysku zarządzanego w Shared Image Gallery
description: Dowiedz się, jak utworzyć obraz z migawki lub dysku zarządzanego w Shared Image Gallery przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792295"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Tworzenie obrazu z dysku zarządzanego lub migawki w witrynie Shared Image Gallery pomocą interfejsu wiersza polecenia platformy Azure

Jeśli masz istniejącą migawkę lub dysk zarządzany, który chcesz zmigrować do Shared Image Gallery, możesz utworzyć obraz Shared Image Gallery bezpośrednio z dysku zarządzanego lub migawki. Po przetestowania nowego obrazu możesz usunąć źródłowy dysk zarządzany lub migawkę. Można również utworzyć obraz z dysku zarządzanego lub migawki w Shared Image Gallery za pomocą [Azure PowerShell.](image-version-snapshot-powershell.md)

Obrazy w galerii obrazów mają dwa składniki, które utworzymy w tym przykładzie:
- Definicja **obrazu zawiera** informacje o obrazie i wymagania dotyczące jego używania. Dotyczy to również tego, czy obraz jest systemem Windows lub Linux, wyspecjalizowany lub uogólniony, informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- Wersja **obrazu służy** do tworzenia maszyny wirtualnej podczas korzystania z maszyny Shared Image Gallery. W razie potrzeby w środowisku może być potrzebnych wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów mogą być używane wiele razy.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć migawkę lub dysk zarządzany. 

Jeśli chcesz dołączyć dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem w razie potrzeby zastąp nazwy zasobów.

## <a name="find-the-snapshot-or-managed-disk"></a>Znajdowanie migawki lub dysku zarządzanego 

Listę migawek dostępnych w grupie zasobów można wyświetlić za pomocą [narzędzia az snapshot list](/cli/azure/snapshot#az_snapshot_list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Zamiast migawki można również użyć dysku zarządzanego. Aby uzyskać dysk zarządzany, użyj [narzędzia az disk list](/cli/azure/disk#az_disk_list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Gdy masz już identyfikator migawki lub dysku zarządzanego i przypiszesz go do zmiennej o nazwie `$source` , która będzie używana później.

Możesz użyć tego samego procesu, aby uzyskać wszystkie dyski danych, które chcesz uwzględnić w obrazie. Przypisz je do zmiennych, a następnie użyj tych zmiennych później podczas tworzenia wersji obrazu.


## <a name="find-the-gallery"></a>Znajdowanie galerii

Aby utworzyć definicję obrazu, potrzebne są informacje o galerii obrazów.

Lista informacji o dostępnych galeriach obrazów za pomocą [narzędzia az sig list](/cli/azure/sig#az_sig_list). Zanotuj nazwę galerii, w której grupa zasobów znajduje się w galerii, do późniejszego użycia.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o obrazie. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, łączników i kropek. 

Podczas tworzenia definicji obrazu upewnij się, że masz wszystkie poprawne informacje. W tym przykładzie zakładamy, że migawka lub dysk zarządzany pochodzi z maszyny wirtualnej, która jest w użyciu i nie została uogólniona. Jeśli dysk zarządzany lub migawka zostały wykonane dla uogólnionych systemów operacyjnych (po uruchomieniu narzędzia Sysprep dla systemu Windows lub [waagent](https://github.com/Azure/WALinuxAgent) bądź dla systemu Linux), zmień na `-deprovision` `-deprovision+user` `-OsState` `generalized` . 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

W tym przykładzie definicja obrazu nosi nazwę *myImageDefinition* i jest dla [wyspecjalizowanego](./shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję obrazów przy użyciu systemu operacyjnego Windows, `--os-type Windows` użyj . 

W tym przykładzie galeria nosi nazwę *myGallery*, znajduje się w grupie zasobów *myGalleryRG,* a nazwa definicji obrazu to *mImageDefinition.*

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
   --os-state specialized
```


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu przy użyciu [narzędzia az image gallery create-image-version.](/cli/azure/sig/image-version#az_sig_image_version_create) 

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą znajdować się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0* i zamierzamy  utworzyć 1 replikę w regionie Południowo-środkowe usa i 1 replikę w regionie Wschodnie usa *2* przy użyciu magazynu strefowo nadmiarowego. Podczas wybierania regionów docelowych do replikacji należy również uwzględnić *region* źródłowy dysku zarządzanego lub migawkę jako miejsce docelowe replikacji.

Przekaż identyfikator migawki lub dysku zarządzanego w `--os-snapshot` parametrze .


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Jeśli chcesz uwzględnić dyski danych w obrazie, musisz dołączyć zarówno parametr ustawiony na numer LUN, jak i identyfikator dysku danych lub `--data-snapshot-luns` `--data-snapshots` migawki.

> [!NOTE]
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Możesz również przechowywać wszystkie repliki wersji obrazu w magazynie [strefowo](../storage/common/storage-redundancy.md) nadmiarowym, dodając `--storage-account-type standard_zrs` podczas tworzenia wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na pomocą [wyspecjalizowanej wersji obrazu.](vm-specialized-image-version-cli.md)

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)