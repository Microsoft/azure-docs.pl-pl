---
title: Kopiowanie wersji obrazu z innej galerii przy użyciu interfejsu wiersza polecenia
description: Skopiuj wersję obrazu z innej galerii za pomocą interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e8aabcd1c68272a78b3c1fe88913c5a62496f681
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225839"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopiowanie obrazu z innej galerii przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli masz wiele galerii w organizacji, możesz również utworzyć wersje obrazu z istniejących wersji obrazu przechowywanych w innych galeriach. Na przykład możesz mieć galerię deweloperskią i testową do tworzenia i testowania nowych obrazów. Gdy są gotowe do użycia w środowisku produkcyjnym, można je skopiować do galerii produkcyjnej przy użyciu tego przykładu. Możesz również utworzyć obraz z obrazu w innej galerii przy użyciu [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejącą galerię źródłową, definicję obrazu i wersję obrazu. Należy również mieć galerię docelową. 

Wersja obrazu źródłowego musi być replikowana do regionu, w którym znajduje się Galeria docelowa. 

W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.



## <a name="get-information-from-the-source-gallery"></a>Pobierz informacje z galerii źródłowej

Będziesz potrzebować informacji ze źródłowej definicji obrazu, aby można było utworzyć kopię jej w nowej galerii.

Wyświetl informacje o dostępnych galeriach obrazów przy użyciu polecenia [AZ SIG list](/cli/azure/sig#az-sig-list) , aby znaleźć informacje o galerii źródłowej.

```azurecli-interactive 
az sig list -o table
```

Wyświetl listę definicji obrazów w galerii przy użyciu polecenia [AZ SIG Image-Definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list). W tym przykładzie szukamy definicji obrazów w galerii o nazwie Moja *Galeria* w grupie zasobów *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Wyświetl listę wersji obrazu w galerii, używając polecenia [AZ SIG Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) , aby znaleźć wersję obrazu, którą chcesz skopiować do nowej galerii. W tym przykładzie szukamy wszystkich wersji obrazu, które są częścią definicji obrazu *myImageDefinition* .

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Jeśli masz wszystkie potrzebne informacje, możesz uzyskać identyfikator wersji obrazu źródłowego przy użyciu polecenia [AZ SIG Image-Version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Tworzenie definicji obrazu 

Należy utworzyć definicję obrazu zgodną z definicją obrazu źródłowej wersji obrazu. Możesz wyświetlić wszystkie informacje potrzebne do odtworzenia definicji obrazu w nowej galerii za pomocą polecenia [AZ SIG Image-Definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Dane wyjściowe będą przypominać następujące:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Utwórz nową definicję obrazu w nowej galerii, korzystając z informacji podanych powyżej.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Tworzenie wersji za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). Należy przekazać identyfikator zarządzanego obrazu, który będzie używany jako linia bazowa do tworzenia wersji obrazu. Możesz użyć [AZ Image list](/cli/azure/image?view#az-image-list) , aby uzyskać informacje o obrazach, które znajdują się w grupie zasobów. 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft chce utworzyć 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA* przy użyciu magazynu Strefowo nadmiarowego.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać obraz w magazynie w warstwie Premium, dodając `--storage-account-type  premium_lrs` [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md) , dodając `--storage-account-type  standard_zrs` go do wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie [uogólnionej](vm-generalized-image-version-cli.md) lub [wyspecjalizowanej](vm-specialized-image-version-cli.md) wersji obrazu.

Ponadto Wypróbuj program [Azure Image Builder (wersja zapoznawcza)](./linux/image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. Możesz nawet użyć go do zaktualizowania i [utworzenia nowej wersji obrazu z istniejącej wersji obrazu](./linux/image-builder-gallery-update-image-version.md). 

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).
