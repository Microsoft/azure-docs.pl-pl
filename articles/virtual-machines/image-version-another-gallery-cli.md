---
title: Kopiowanie wersji obrazu z innej galerii przy użyciu interfejsu wiersza polecenia
description: Skopiuj wersję obrazu z innej galerii przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784987"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopiowanie obrazu z innej galerii przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli masz wiele galerii w organizacji, możesz również tworzyć wersje obrazów z istniejących wersji obrazów przechowywanych w innych galeriach. Na przykład możesz mieć galerię deweloperskiej i testowej do tworzenia i testowania nowych obrazów. Gdy będą gotowe do użycia w środowisku produkcyjnym, możesz skopiować je do galerii produkcyjnej przy użyciu tego przykładu. Możesz również utworzyć obraz z obrazu w innej galerii przy użyciu Azure PowerShell [.](image-version-another-gallery-powershell.md)



## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejącą galerię źródłową, definicję obrazu i wersję obrazu. Powinna być również mieć galeria docelowa. 

Wersja obrazu źródłowego musi być replikowana do regionu, w którym znajduje się galeria docelowa. 

Podczas pracy z tym artykułem w razie potrzeby zastąp nazwy zasobów.



## <a name="get-information-from-the-source-gallery"></a>Uzyskiwanie informacji z galerii źródłowej

Potrzebne będą informacje z definicji obrazu źródłowego, aby można było utworzyć jego kopię w nowej galerii.

Lista informacji o dostępnych galeriach obrazów przy użyciu [narzędzia az sig list](/cli/azure/sig#az_sig_list) w celu znalezienia informacji o galerii źródłowej.

```azurecli-interactive 
az sig list -o table
```

Aby wyświetlić listę definicji obrazów w galerii, należy użyć [narzędzia az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list). W tym przykładzie wyszukujemy definicje obrazów w galerii o nazwie *myGallery* w grupie zasobów *myGalleryRG.*

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Aby wyświetlić listę wersji obrazu w galerii, znajdź wersję obrazu, którą chcesz skopiować do nowej galerii, za pomocą narzędzia [az sig image-version list.](/cli/azure/sig/image-version#az_sig_image_version_list) W tym przykładzie szukamy wszystkich wersji obrazu, które są częścią definicji obrazu *myImageDefinition.*

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Gdy masz wszystkie potrzebne informacje, możesz uzyskać identyfikator wersji obrazu źródłowego przy użyciu [narzędzia az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Tworzenie definicji obrazu 

Należy utworzyć definicję obrazu, która będzie odpowiadać definicji obrazu w wersji obrazu źródłowego. Wszystkie informacje potrzebne do ponownego tworzenia definicji obrazu można wyświetlić w nowej galerii za pomocą [narzędzia az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show).

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

Utwórz nową definicję obrazu w nowej galerii, korzystając z informacji z powyższych danych wyjściowych.


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


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersje za pomocą [narzędzia az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). Musisz przekazać identyfikator obrazu zarządzanego, aby użyć go jako podstawy do utworzenia wersji obrazu. Możesz użyć az [image list,](/cli/azure/image?view#az_image_list) aby uzyskać informacje o obrazach, które znajdują się w grupie zasobów. 

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą być w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0* i zamierzamy  utworzyć 1 replikę w  regionie Południowo-środkowe usa i 1 replikę w regionie Wschodnie usa przy użyciu magazynu strefowo nadmiarowego.


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
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Obraz można również przechowywać w magazynie Premium Storage, dodając magazyn strefowo nadmiarowy lub dodając go podczas `--storage-account-type  premium_lrs` tworzenia wersji [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na [pomocą wersji uogólnionych](vm-generalized-image-version-cli.md) lub [wyspecjalizowanych](vm-specialized-image-version-cli.md) obrazów.

Ponadto wypróbuj usługę [Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) i zautomatyzuj tworzenie wersji obrazu. Możesz nawet użyć jej do zaktualizowania i utworzenia nowej wersji obrazu z istniejącej [wersji obrazu.](./linux/image-builder-gallery-update-image-version.md) 

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)