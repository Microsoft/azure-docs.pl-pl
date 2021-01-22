---
title: Kopiowanie obrazu z innej galerii przy użyciu programu PowerShell
description: Skopiuj obraz z innej galerii przy użyciu Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 85bc6f4468e0365d247b0adc204bd53ce9fa3006
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676720"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Kopiowanie obrazu z innej galerii przy użyciu programu PowerShell

Jeśli masz wiele galerii w organizacji, możesz tworzyć obrazy z obrazów przechowywanych w innych galeriach. Na przykład możesz mieć galerię deweloperskią i testową do tworzenia i testowania nowych obrazów. Gdy są gotowe do użycia w środowisku produkcyjnym, można je skopiować do galerii produkcyjnej przy użyciu tego przykładu. Możesz również utworzyć obraz z obrazu w innej galerii przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejącą galerię źródłową, definicję obrazu i wersję obrazu. Należy również mieć galerię docelową. 

Wersja obrazu źródłowego musi być replikowana do regionu, w którym znajduje się Galeria docelowa. 

Zostanie utworzona nowa definicja obrazu i wersja obrazu w galerii docelowej.


W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.


## <a name="get-the-source-image"></a>Pobierz obraz źródłowy 

Będziesz potrzebować informacji z definicji obrazu źródłowego, aby można było utworzyć kopię jej w galerii docelowej.

Wyświetl informacje o istniejących galeriach, definicjach obrazów i wersjach obrazów przy użyciu polecenia cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) .

Wyniki są w formacie `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Jeśli masz wszystkie potrzebne informacje, możesz uzyskać identyfikator wersji obrazu źródłowego przy użyciu polecenia [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). W tym przykładzie pobieramy `1.0.0` wersję obrazu `myImageDefinition` definicji w `myGallery` galerii źródła w `myResourceGroup` grupie zasobów.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Tworzenie definicji obrazu 

Należy utworzyć nową definicję obrazu, która pasuje do definicji obrazu źródła. Wszystkie informacje potrzebne do ponownego utworzenia definicji obrazu można wyświetlić za pomocą polecenia [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

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

Utwórz nową definicję obrazu w galerii docelowej przy użyciu polecenia cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) i informacji z danych wyjściowych powyżej.


W tym przykładzie definicja obrazu ma nazwę *myDestinationImgDef* w galerii o nazwie *myDestinationGallery*.


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


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). Należy przekazać Identyfikator obrazu źródłowego w `--managed-image` parametrze tworzenia wersji obrazu w galerii docelowej. 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie Galeria docelowa ma nazwę *myDestinationGallery* w grupie zasobów *MyDestinationRG* w lokalizacji *zachodnie stany USA* . Wersja naszego obrazu to *1.0.0* , a firma Microsoft chce utworzyć 1 replikę w regionie *Południowo-środkowe stany USA* i 2 repliki w regionie *zachodnie stany USA* . 


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

Replikowanie obrazu do wszystkich regionów docelowych może chwilę potrwać, więc utworzyliśmy zadanie, aby można było śledzić postęp. Aby wyświetlić postęp zadania, wpisz polecenie `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać obraz w magazynie Premiun przez dodanie `-StorageAccountType Premium_LRS` lub [nadmiarowy magazyn stref](../storage/common/storage-redundancy.md) przez dodanie `-StorageAccountType Standard_ZRS` go podczas tworzenia wersji obrazu.
>


## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie [uogólnionej](vm-generalized-image-version-powershell.md) lub [wyspecjalizowanej](vm-specialized-image-version-powershell.md) wersji obrazu.

[Usługa Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](./linux/image-builder-gallery-update-image-version.md). 

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).