---
title: Samouczek — używanie niestandardowego obrazu maszyny wirtualnej w zestawie skalowania z Azure PowerShell
description: Dowiedz się, jak za pomocą programu Azure PowerShell utworzyć niestandardowy obraz maszyny wirtualnej, którego można użyć do wdrożenia zestawu skalowania maszyny wirtualnej
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 8a0ee8565d22e935535bbe1e830d8399b2489f5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86527527"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem programu Azure PowerShell

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania z obrazu 
> * Udostępnianie galerii obrazów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach szczegółowo opisano sposób tworzenia istniejącej maszyny wirtualnej i przełączenia jej do obrazu niestandardowego, którego można użyć do utworzenia zestawu skalowania.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby można zobaczyć [Przewodnik Szybki Start dla programu PowerShell](quick-create-powershell.md) , aby utworzyć maszynę wirtualną do użycia w tym samouczku. Podczas pracy z samouczkiem Zastąp nazwy zasobów, w razie konieczności.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="get-the-vm"></a>Pobierz maszynę wirtualną

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne w grupie zasobów za pomocą polecenia [Get-AzVM](/powershell/module/az.compute/get-azvm). Jeśli znasz nazwę maszyny wirtualnej i grupę zasobów, możesz użyć jej ponownie, `Get-AzVM` Aby pobrać obiekt maszyny wirtualnej i zapisać go w zmiennej do użycia później. Ten przykład pobiera maszynę wirtualną o nazwie *sourceVM* z grupy zasobów "Grupa zasobów" i przypisuje ją do zmiennej *$VM*. 

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

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

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

## <a name="create-a-scale-set-from-the-image"></a>Tworzenie zestawu skalowania na podstawie obrazu
Utwórz zestaw skalowania za pomocą polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss) z parametrem `-ImageName` umożliwiającym zdefiniowanie niestandardowego obrazu maszyny wirtualnej utworzonego w poprzednim kroku. Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


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

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Konstruktor obrazów platformy Azure

Platforma Azure oferuje również usługę, która jest oparta na pakiecie [Konstruktor obrazów maszyn wirtualnych platformy Azure](../virtual-machines/windows/image-builder-overview.md). Wystarczy opisać dostosowania w szablonie i obsłużyć Tworzenie obrazu. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem programu Azure PowerShell:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania z obrazu 
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-powershell.md)
