---
title: Kopiowanie obrazu z innej galerii przy użyciu programu PowerShell
description: Skopiuj obraz z innej galerii przy użyciu Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 35346836767bc1da8c498e23fd3b42afe7a9c350
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531191"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopiowanie obrazu z innej galerii przy użyciu programu PowerShell

Jeśli masz wiele galerii w swojej organizacji, możesz tworzyć obrazy z obrazów przechowywanych w innych galeriach. Na przykład możesz mieć galerię deweloperskiej i testowej do tworzenia i testowania nowych obrazów. Gdy będą gotowe do użycia w środowisku produkcyjnym, możesz skopiować je do galerii produkcyjnej przy użyciu tego przykładu. Możesz również utworzyć obraz z obrazu w innej galerii przy użyciu interfejsu wiersza [polecenia platformy Azure.](image-version-another-gallery-cli.md)


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejącą galerię źródłową, definicję obrazu i wersję obrazu. Powinna być również mieć galeria docelowa. 

Wersja obrazu źródłowego musi być replikowana do regionu, w którym znajduje się galeria docelowa. 

Będziemy tworzyć nową definicję obrazu i wersję obrazu w galerii docelowej.


Podczas pracy z tym artykułem w razie potrzeby zastąp nazwy zasobów.


## <a name="get-the-source-image"></a>Uzyskiwanie obrazu źródłowego 

Potrzebne będą informacje z definicji obrazu źródłowego, aby można było utworzyć jego kopię w galerii docelowej.

Lista informacji o istniejących galeriach, definicjach obrazów i wersjach obrazów za pomocą polecenia cmdlet [Get-AzResource.](/powershell/module/az.resources/get-azresource)

Wyniki mają format `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Gdy masz już wszystkie potrzebne informacje, możesz uzyskać identyfikator wersji obrazu źródłowego przy użyciu [get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). W tym przykładzie dostaliśmy wersję obrazu definicji w galerii źródłowej `1.0.0` `myImageDefinition` w grupie `myGallery` `myResourceGroup` zasobów.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Tworzenie definicji obrazu 

Musisz utworzyć nową definicję obrazu, która będzie odpowiadać definicji obrazu źródła. Aby wyświetlić wszystkie informacje potrzebne do ponownego tworzenia definicji obrazu, możesz użyć [get-AzGalleryImageDefinition.](/powershell/module/az.compute/get-azgalleryimagedefinition)

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
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
  "osType": "Windows",
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

Utwórz nową definicję obrazu w galerii docelowej przy użyciu polecenia cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) i informacji z powyższych danych wyjściowych.


W tym przykładzie definicja obrazu nosi nazwę *myDestinationImgDef* w galerii o nazwie *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu przy użyciu [new-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). W celu utworzenia wersji obrazu w galerii docelowej należy przekazać identyfikator obrazu źródłowego w `-Source` parametrze . 

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą być w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie galeria docelowa nosi nazwę *myDestinationGallery* w grupie zasobów *myDestinationRG* w lokalizacji *Zachodnie stany* USA. Wersja naszego obrazu to *1.0.0* i zamierzamy utworzyć 1 replikę w regionie Południowo-środkowe stany USA i 2 repliki w regionie *Zachodnie* stany USA.  


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Replikacja obrazu do wszystkich regionów docelowych może zająć trochę czasu, dlatego utworzono zadanie, aby można było śledzić postęp. Aby wyświetlić postęp zadania, wpisz `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Obraz można również przechowywać w magazynie Premiun przez dodanie magazynu , lub magazynu strefowo nadmiarowego, dodając go podczas `-StorageAccountType Premium_LRS` tworzenia wersji [](../storage/common/storage-redundancy.md) `-StorageAccountType Standard_ZRS` obrazu.
>


## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na [pomocą wersji uogólnionych](vm-generalized-image-version-powershell.md) lub [wyspecjalizowanych](vm-specialized-image-version-powershell.md) obrazów.

[Usługa Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) może pomóc w automatyzacji tworzenia wersji obrazu. Można jej nawet użyć do zaktualizowania i utworzenia nowej wersji obrazu z [istniejącej wersji obrazu.](./linux/image-builder-gallery-update-image-version.md) 

Aby uzyskać informacje na temat sposobu dostarczania informacji o planie zakupu, zobacz Supply Azure Marketplace purchase plan information when creating images (Dostarczanie informacji o planie zakupu podczas [tworzenia obrazów).](marketplace-images.md)
