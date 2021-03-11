---
title: Klonowanie zarządzanego obrazu do wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak sklonować zarządzany obraz do wersji obrazu w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cae82072785838d410453b2eb83685905b0ba04e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553786"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klonowanie zarządzanego obrazu do wersji obrazu przy użyciu interfejsu wiersza polecenia platformy Azure
Jeśli masz istniejący obraz zarządzany, który chcesz sklonować do galerii obrazów udostępnionych, możesz utworzyć obraz udostępnionej galerii obrazów bezpośrednio z poziomu zarządzanego obrazu. Po przetestowaniu nowego obrazu można usunąć źródłowy obraz zarządzany. Możesz także przeprowadzić migrację z zarządzanego obrazu do udostępnionej galerii obrazów przy użyciu [programu PowerShell](image-version-managed-image-powershell.md).

Obrazy w galerii obrazów mają dwa składniki, które zostaną utworzone w tym przykładzie:
- **Definicja obrazu** przenosi informacje o obrazie i wymaganiach dotyczących korzystania z niego. Obejmuje to zarówno system Windows, jak i Linux, wyspecjalizowane lub uogólnione informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Do wykonania tego artykułu jest wymagana istniejąca [Galeria obrazów udostępnionych](shared-images-cli.md). 

Aby ukończyć ten przykład w tym artykule, musisz mieć istniejący obraz zarządzany uogólnionej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [przechwytywanie zarządzanego obrazu](./linux/capture-image.md). Jeśli zarządzany obraz zawiera dysk z danymi, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, jeśli są one odpowiednie.



## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Ponieważ obrazy zarządzane są zawsze uogólnione, utworzysz definicję obrazu przy użyciu `--os-state generalized` dla uogólnionego obrazu.

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą polecenia [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i ma na celu [uogólniony](./shared-image-galleries.md#generalized-and-specialized-images) obraz systemu operacyjnego Linux. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Windows, użyj polecenia `--os-type Windows` . 

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


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Tworzenie wersji za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Należy przekazać identyfikator zarządzanego obrazu, który będzie używany jako linia bazowa do tworzenia wersji obrazu. Możesz użyć [AZ Image list](/cli/azure/image?view#az-image-list) , aby pobrać identyfikatory dla obrazów. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft chce utworzyć 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA 2* , korzystając z magazynu Strefowo nadmiarowego. Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* jako element docelowy dla replikacji.

Przekaż identyfikator zarządzanego obrazu w `--managed-image` parametrze.


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
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać wszystkie repliki wersji obrazu w [magazynie strefowo nadmiarowy](../storage/common/storage-redundancy.md) przez dodanie `--storage-account-type standard_zrs` podczas tworzenia wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie [uogólnionej wersji obrazu](vm-generalized-image-version-cli.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).