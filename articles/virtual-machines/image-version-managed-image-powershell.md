---
title: Klonowanie zarządzanego obrazu do galerii obrazów udostępnionych
description: Dowiedz się, jak używać Azure PowerShell do klonowania zarządzanego obrazu do wersji obrazu w udostępnionej galerii obrazów.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556984"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>Klonowanie zarządzanego obrazu do obrazu udostępnionej galerii obrazów

Jeśli masz istniejący obraz zarządzany, który chcesz sklonować i przenieść do galerii obrazów udostępnionych, możesz utworzyć obraz udostępnionej galerii obrazów bezpośrednio z poziomu zarządzanego obrazu. Po przetestowaniu nowego obrazu można usunąć źródłowy obraz zarządzany. Możesz także przeprowadzić migrację z zarządzanego obrazu do udostępnionej galerii obrazów przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-managed-image-cli.md).

Obrazy w galerii obrazów mają dwa składniki, które zostaną utworzone w tym przykładzie:
- **Definicja obrazu** przenosi informacje o obrazie i wymaganiach dotyczących korzystania z niego. Obejmuje to zarówno system Windows, jak i Linux, wyspecjalizowane lub uogólnione informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć istniejący obraz zarządzany. Jeśli zarządzany obraz zawiera dysk z danymi, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z tym artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, jeśli są one odpowiednie.

## <a name="get-the-gallery"></a>Uzyskaj galerię

Wszystkie galerie i definicje obrazów można wyświetlić według nazwy. Wyniki są w formacie `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Po znalezieniu odpowiedniej galerii Utwórz zmienną do użycia później. Ten przykład pobiera galerię o nazwie "Moja *Galeria* " w grupie zasobów *WebResource* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o obrazie. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Podczas tworzenia definicji obrazu upewnij się, że zawiera on wszystkie prawidłowe informacje. Ponieważ zarządzane obrazy są zawsze uogólnione, należy ustawić `-OsState generalized` . 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu przy użyciu polecenia [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i ma na celu uogólniony system operacyjny Windows. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Linux, użyj polecenia `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Pobierz obraz zarządzany

Możesz wyświetlić listę obrazów dostępnych w grupie zasobów za pomocą polecenia [Get-AzImage](/powershell/module/az.compute/get-azimage). Gdy znasz nazwę obrazu i grupę zasobów, w której znajduje się, możesz ponownie użyć, `Get-AzImage` Aby pobrać obiekt obrazu i zapisać go w zmiennej do użycia później. Ten przykład pobiera obraz *o nazwie NazwaMojejZmiennej* z grupy zasobów "Grupa zasobów" i przypisuje go do zmiennej *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z zarządzanego obrazu przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja obrazu to *1.0.0* i jest replikowana do centrów danych *zachodnio-środkowe stany USA* i południowo- *środkowe stany USA* . Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* jako element docelowy dla replikacji. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Replikowanie obrazu do wszystkich regionów docelowych może chwilę potrwać, więc utworzyliśmy zadanie, aby można było śledzić postęp. Aby zobaczyć postęp, wpisz `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu. 
>
> Możesz również przechowywać obraz w magazynie w warstwie Premium, dodając `-StorageAccountType Premium_LRS` [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md) , dodając `-StorageAccountType Standard_ZRS` go do wersji obrazu.
>

## <a name="delete-the-managed-image"></a>Usuń obraz zarządzany

Po sprawdzeniu, czy nowa wersja obrazu działa prawidłowo, można usunąć obraz zarządzany.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Po sprawdzeniu, czy replikacja została ukończona, można utworzyć maszynę wirtualną na podstawie [uogólnionego obrazu](vm-generalized-image-version-powershell.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).