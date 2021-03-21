---
title: PowerShell — Tworzenie obrazu na podstawie migawki lub dysku zarządzanego w galerii obrazów udostępnionych
description: Dowiedz się, jak utworzyć obraz na podstawie migawki lub dysku zarządzanego w galerii obrazów udostępnionych przy użyciu programu PowerShell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556907"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Tworzenie obrazu z dysku zarządzanego lub migawki w udostępnionej galerii obrazów przy użyciu programu PowerShell

Jeśli masz istniejącą migawkę lub dysk zarządzany, który chcesz migrować do galerii obrazów udostępnionych, możesz utworzyć obraz udostępnionej galerii obrazów bezpośrednio z dysku zarządzanego lub migawki. Po przetestowaniu nowego obrazu można usunąć źródłowy dysk zarządzany lub migawkę. Możesz również utworzyć obraz z dysku zarządzanego lub migawki w udostępnionej galerii obrazów przy użyciu [interfejsu wiersza polecenia platformy Azure](image-version-snapshot-cli.md).

Obrazy w galerii obrazów mają dwa składniki, które zostaną utworzone w tym przykładzie:
- **Definicja obrazu** przenosi informacje o obrazie i wymaganiach dotyczących korzystania z niego. Obejmuje to zarówno system Windows, jak i Linux, wyspecjalizowane lub uogólnione informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć migawkę lub dysk zarządzany. 

Jeśli chcesz dołączyć dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.


## <a name="get-the-snapshot-or-managed-disk"></a>Pobierz migawkę lub dysk zarządzany

Można wyświetlić listę migawek, które są dostępne w grupie zasobów za pomocą polecenia [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Jeśli znasz nazwę migawki i grupę zasobów, w której znajduje się, możesz użyć jej `Get-AzSnapshot` ponownie, aby uzyskać obiekt migawki i zapisać go w zmiennej do użycia później. Ten przykład pobiera migawkę o nazwie Moja *migawka* z grupy zasobów "Grupa zasobów" i przypisuje ją do zmiennej *$Source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Można również użyć dysku zarządzanego zamiast migawki. Aby uzyskać dysk zarządzany, użyj polecenie [Get-AzDisk](/powershell/module/az.compute/get-azdisk). 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Następnie należy uzyskać dysk zarządzany i przypisać go do `$source` zmiennej.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Za pomocą tych samych poleceń cmdlet można pobrać dowolne dyski danych, które mają zostać uwzględnione w obrazie. Należy przypisać je do zmiennych, a następnie użyć tych zmiennych później podczas tworzenia wersji obrazu.


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

Podczas tworzenia definicji obrazu upewnij się, że zawiera on wszystkie prawidłowe informacje. W tym przykładzie zakładamy, że migawka lub dysk zarządzany pochodzą z maszyny wirtualnej, która jest używana, i nie został uogólniony. Jeśli na dysku zarządzanym lub migawek utworzono uogólniony system operacyjny (po uruchomieniu programu Sysprep dla systemu Windows lub [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` lub `-deprovision+user` Linux), Zmień wartość `-OsState` na `generalized` . 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu przy użyciu polecenia [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest przeznaczona dla wyspecjalizowanego systemu operacyjnego Windows. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Linux, użyj polecenia `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Informacje o planie zakupu

W niektórych przypadkach należy przekazać informacje o planie zakupu podczas tworzenia maszyny wirtualnej na podstawie obrazu opartego na obrazie portalu Azure Marketplace. W takich przypadkach zalecamy uwzględnienie informacji o planie zakupu w definicji obrazu. W takim przypadku zapoznaj się z tematem [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z migawki przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

Jeśli chcesz, aby obraz zawierał dysk danych, oprócz dysku systemu operacyjnego, Dodaj `-DataDiskImage` parametr i ustaw go na identyfikator migawki dysku danych lub dysku zarządzanego.

W tym przykładzie wersja obrazu to *1.0.0* i jest replikowana do centrów danych *zachodnio-środkowe stany USA* i południowo- *środkowe stany USA* . Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* jako element docelowy dla replikacji.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Replikowanie obrazu do wszystkich regionów docelowych może chwilę potrwać, więc utworzyliśmy zadanie, aby można było śledzić postęp. Aby wyświetlić postęp zadania, wpisz polecenie `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Musisz poczekać na całkowite zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tej samej migawki do utworzenia innej wersji obrazu. 
>
> Wersję obrazu można również zapisać w [magazynie strefowo nadmiarowy](../storage/common/storage-redundancy.md) , dodając `-StorageAccountType Standard_ZRS` podczas tworzenia wersji obrazu.
>

## <a name="delete-the-source"></a>Usuń Źródło

Po sprawdzeniu, czy nowa wersja obrazu działa prawidłowo, można usunąć źródło obrazu za pomocą opcji [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) lub [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Następne kroki

Po sprawdzeniu, czy replikacja została ukończona, można utworzyć maszynę wirtualną na podstawie [obrazu specjalistycznego](vm-specialized-image-version-powershell.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).