---
title: Tworzenie obrazu na podstawie maszyny wirtualnej (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą Azure PowerShell utworzyć obraz w galerii obrazów udostępnionych z istniejącej maszyny wirtualnej na platformie Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3a7ca8236307bbf8a419d2988e1a6dc1e4c40597
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964868"
---
# <a name="preview-create-an-image-from-a-vm"></a>Wersja zapoznawcza: Tworzenie obrazu na podstawie maszyny wirtualnej

Jeśli masz istniejącą maszynę wirtualną, która ma być używana do tworzenia wielu identycznych maszyn wirtualnych, możesz użyć tej maszyny wirtualnej do utworzenia obrazu w udostępnionej galerii obrazów przy użyciu Azure PowerShell. Możesz również utworzyć obraz z maszyny wirtualnej przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-vm-cli.md).

Można przechwycić obraz z [wyspecjalizowanych i uogólnionych](./windows/shared-image-galleries.md#generalized-and-specialized-images) maszyn wirtualnych przy użyciu Azure PowerShell. 

Obrazy w galerii obrazów mają dwa składniki, które zostaną utworzone w tym przykładzie:
- **Definicja obrazu** przenosi informacje o obrazie i wymaganiach dotyczących korzystania z niego. Obejmuje to zarówno system Windows, jak i Linux, wyspecjalizowane lub uogólnione informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć ten artykuł, musisz mieć istniejącą udostępnioną galerię obrazów oraz istniejącą maszynę wirtualną na platformie Azure, która będzie używana jako źródło. 

Jeśli maszyna wirtualna ma dołączony dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.


## <a name="get-the-gallery"></a>Uzyskaj galerię

Wszystkie galerie i definicje obrazów można wyświetlić według nazwy. Wyniki są w formacie `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Po znalezieniu odpowiednich definicji galerii i obrazów Utwórz zmienne do użycia w przyszłości. Ten przykład pobiera galerię o nazwie "Moja *Galeria* " w grupie zasobów *WebResource* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Pobierz maszynę wirtualną

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne w grupie zasobów za pomocą polecenia [Get-AzVM](/powershell/module/az.compute/get-azvm). Jeśli znasz nazwę maszyny wirtualnej i grupę zasobów, w której znajduje się, możesz użyć `Get-AzVM` jej ponownie, aby pobrać obiekt maszyny wirtualnej i zapisać go w zmiennej do użycia później. Ten przykład pobiera maszynę wirtualną o nazwie *sourceVM* z grupy zasobów "Grupa zasobów" i przypisuje ją do zmiennej *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Najlepszym rozwiązaniem jest stop\deallocate maszyny wirtualnej przed utworzeniem obrazu przy użyciu polecenia [stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o obrazie. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Podczas tworzenia definicji obrazu upewnij się, że zawiera on wszystkie prawidłowe informacje. W przypadku uogólnionej maszyny wirtualnej (przy użyciu programu Sysprep dla systemu Windows lub waagent-anulowania aprowizacji w systemie Linux) należy utworzyć definicję obrazu przy użyciu polecenia `-OsState generalized` . Jeśli maszyna wirtualna nie została uogólniona, Utwórz definicję obrazu za pomocą polecenia `-OsState specialized` .

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./windows/shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu przy użyciu polecenia [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest przeznaczona dla WYSPECJALIZOWANEJ maszyny wirtualnej z systemem Windows. Aby utworzyć definicję dla obrazów przy użyciu systemu Linux, użyj polecenia `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja obrazu to *1.0.0* i jest replikowana do centrów danych *zachodnio-środkowe stany USA* i południowo- *środkowe stany USA* . Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* jako element docelowy dla replikacji.

Aby utworzyć wersję obrazu z maszyny wirtualnej, użyj `$vm.Id.ToString()` dla `-SourceImageId` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
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
> Możesz również przechowywać obraz w magazynie w warstwie Premium, dodając `-StorageAccountType Premium_LRS` [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md) , dodając `-StorageAccountType Standard_ZRS` go do wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Po sprawdzeniu, czy nowa wersja obrazu działa prawidłowo, można utworzyć maszynę wirtualną. Utwórz maszynę wirtualną z poziomu [wyspecjalizowanej wersji obrazu](vm-specialized-image-version-powershell.md) lub [uogólnionej wersji obrazu](vm-generalized-image-version-powershell.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).
