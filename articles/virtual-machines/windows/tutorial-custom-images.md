---
title: Samouczek — Tworzenie niestandardowych obrazów maszyn wirtualnych przy użyciu Azure PowerShell
description: W tym samouczku dowiesz się, jak za pomocą Azure PowerShell utworzyć niestandardowy obraz maszyny wirtualnej w systemie Windows przechowywany w galerii udostępnionych obrazów systemu Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0e3521603c4651133f1420809e4921ad27279860
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873194"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Samouczek: Tworzenie obrazów maszyn wirtualnych z systemem Windows za pomocą Azure PowerShell

Obrazy mogą służyć do uruchamiania wdrożeń i zapewnienia spójności między wieloma maszynami wirtualnymi. W tym samouczku utworzysz własny obraz maszyny wirtualnej platformy Azure przy użyciu programu PowerShell i zapiszesz go w galerii obrazów udostępnionych. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na podstawie obrazu 
> * Udostępnianie galerii obrazów



## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poniższych krokach szczegółowo opisano, jak utworzyć istniejącą maszynę wirtualną i przekształcić ją w obraz niestandardowy wielokrotnego użytku, który służy do tworzenia nowych maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby można zobaczyć [Przewodnik Szybki Start dla programu PowerShell](quick-create-powershell.md) , aby utworzyć maszynę wirtualną do użycia w tym samouczku. Podczas pracy z samouczkiem Zastąp nazwy zasobów, w razie konieczności.

## <a name="overview"></a>Omówienie

[Galeria obrazów udostępnionych](../shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="get-the-vm"></a>Pobierz maszynę wirtualną

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne w grupie zasobów za pomocą polecenia [Get-AzVM](/powershell/module/az.compute/get-azvm). Jeśli znasz nazwę maszyny wirtualnej i grupę zasobów, możesz użyć jej ponownie, `Get-AzVM` Aby pobrać obiekt maszyny wirtualnej i zapisać go w zmiennej do użycia później. Ten przykład pobiera maszynę wirtualną o nazwie *sourceVM* z grupy zasobów "Grupa zasobów" i przypisuje ją do zmiennej *$sourceVM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie grupa zasobów o nazwie *myGalleryRG* jest tworzona w regionie *wschodnim* :

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek. Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu polecenia [New-AzGallery](/powershell/module/az.compute/new-azgallery). Poniższy przykład tworzy galerię o nazwie Moja *Gallery* w grupie zasobów *myGalleryRG* .

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](../shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu przy użyciu polecenia [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie obraz galerii ma nazwę *myGalleryImage* i jest tworzony dla obrazu specjalistycznego. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z maszyny wirtualnej przy użyciu polecenia [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja obrazu to *1.0.0* i jest replikowana zarówno do centrów danych *Wschodnie stany USA* , jak i *Południowo-środkowe stany USA* . Podczas wybierania regionów docelowych na potrzeby replikacji należy uwzględnić region *źródłowy* jako element docelowy dla replikacji.

Aby utworzyć wersję obrazu z maszyny wirtualnej, użyj `$vm.Id.ToString()` dla `-Source` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Replikowanie obrazu do wszystkich regionów docelowych może chwilę potrwać.


## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej 

Po utworzeniu wyspecjalizowanego obrazu można utworzyć co najmniej jedną nową maszynę wirtualną. Za pomocą polecenia cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) . Aby użyć obrazu, należy użyć `Set-AzVMSourceImage` i ustawić `-Id` na identyfikator definicji obrazu ($GalleryImage. ID w tym przypadku), aby zawsze używał najnowszej wersji obrazu. 

W tym przykładzie Zastąp nazwy zasobów zgodnie z wymaganiami. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy Udostępnianie dostępu na poziomie galerii obrazów. Użyj adresu e-mail i polecenia cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , aby uzyskać identyfikator obiektu dla użytkownika, a następnie użyj polecenie [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) w celu uzyskania dostępu do galerii. Zastąp przykładową wiadomość e-mail, alinne_montes@contoso.com w tym przykładzie, własnymi informacjami.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Konstruktor obrazów platformy Azure

Platforma Azure oferuje również usługę, która jest oparta na pakiecie [Konstruktor obrazów maszyn wirtualnych platformy Azure](../image-builder-overview.md). Wystarczy opisać dostosowania w szablonie i obsłużyć Tworzenie obrazu. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono wyspecjalizowany obraz maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na podstawie obrazu 
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć maszyny wirtualne o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)