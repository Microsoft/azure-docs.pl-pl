---
title: Korzystanie z programu Azure Image Builder z galerią obrazów dla maszyn wirtualnych systemu Windows (wersja zapoznawcza)
description: Tworzenie wersji obrazów usługi Azure Shared Gallery przy użyciu usługi Azure Image Builder i Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869521"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Podgląd: tworzenie obrazu systemu Windows i rozpowszechnianie go w galerii obrazów udostępnionych 

W tym artykule pokazano, jak można użyć usługi Azure Image Builder i Azure PowerShell, aby utworzyć wersję obrazu w [galerii obrazów udostępnionych,](shared-image-galleries.md)a następnie rozpowszechniać obraz globalnie. Można to również zrobić za pomocą [interfejsu wiersza polecenia platformy Azure](../linux/image-builder-gallery.md).

Będziemy używać szablonu .json do skonfigurowania obrazu. Plik .json używamy jest tutaj: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Będziemy pobierać i edytować lokalną wersję szablonu, więc ten artykuł jest napisany przy użyciu lokalnej sesji programu PowerShell.

Aby rozpowszechniać obraz w galerii obrazów udostępnionych, `distribute` szablon używa [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) jako wartości sekcji szablonu.

Usługa Azure Image Builder automatycznie uruchamia narzędzie sysprep w celu uogólniania obrazu, jest to ogólne polecenie sysprep, które można [zastąpić](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) w razie potrzeby. 

Należy pamiętać, ile razy dostosowalnie warstwy. Polecenie Sysprep można uruchomić maksymalnie 8 razy na jednym obrazie systemu Windows. Po uruchomieniu programu Sysprep 8 razy należy ponownie utworzyć obraz systemu Windows. Aby uzyskać więcej informacji, zobacz [Limity dotyczące liczby uruchomień Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Zarejestruj funkcje
Aby korzystać z usługi Azure Image Builder podczas podglądu, należy zarejestrować nową funkcję.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Sprawdź stan rejestracji funkcji.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Poczekaj, `RegistrationState` `Registered` aż zostanie przesuń do następnego kroku.

Sprawdź rejestracje dostawcy. Upewnij się, `Registered`że każdy zwraca .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Jeśli nie zwracają, `Registered`należy użyć następujących czynności, aby zarejestrować dostawców:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Tworzenie zmiennych

Będziemy wielokrotnie wykorzystywać niektóre informacje, więc utworzymy pewne zmienne do przechowywania tych informacji. Zastąp wartości zmiennych, takich jak `username` i `vmpassword`, własnymi informacjami.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów i nadaj platformie Azure Image Builder uprawnienia do tworzenia zasobów w tej grupie zasobów.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Aby korzystać z Kreatora obrazów z galerią obrazów udostępnionych, musisz mieć istniejącą galerię obrazów i definicję obrazu. Kreator obrazów nie utworzy dla Ciebie galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, zacznij od ich utworzenia. Najpierw utwórz galerię obrazów.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Pobieranie i konfigurowanie szablonu

Pobierz szablon .json i skonfiguruj go ze zmiennymi.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Szablon musi zostać przesłany do usługi, spowoduje to pobranie wszelkich zależnych artefaktów, takich jak skrypty, i przechowywanie ich w tymczasowej grupie zasobów, poprzedzone *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Aby utworzyć obraz, musisz wywołać "Uruchom" na szablonie.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Tworzenie obrazu i replikowanie go do obu regionów może zająć trochę czasu. Poczekaj, aż ta część zostanie zakończona przed przejściem do tworzenia maszyny Wirtualnej.

Aby uzyskać informacje na temat opcji automatyzacji uzyskiwania stanu kompilacji obrazu, zobacz [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) dla tego szablonu w usłudze GitHub.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną z wersji obrazu, który został utworzony przez usługę Azure Image Builder.

Pobierz wersję obrazu, którą utworzono.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Utwórz maszynę wirtualną w drugim regionie, który był obraz został zreplikowany.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Weryfikowanie dostosowywania
Utwórz połączenie pulpitu zdalnego z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawionego podczas tworzenia maszyny Wirtualnej. Wewnątrz maszyny Wirtualnej otwórz monit cmd i wpisz:

```console
dir c:\
```

Powinien zostać wyświetlony katalog `buildActions` o nazwie, który został utworzony podczas dostosowywania obrazu.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, **pomiń ten krok** i przejdź do [programu Azure Image Builder, aby utworzyć inną wersję obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie obrazu, który został utworzony, wraz ze wszystkimi innymi plikami zasobów. Upewnij się, że to wdrożenie zostało zakończone przed usunięciem zasobów.

Najpierw usuń szablon grupy zasobów, w przeciwnym razie grupa zasobów przemieszczania (*IT_*) używana przez AIB nie zostanie wyczyszczona.

Pobierz identyfikator zasobu szablonu obrazu. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Usuń szablon obrazu.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

usunąć grupę zasobów.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaktualizować utworzoną wersję obrazu, zobacz [Tworzenie innej wersji obrazu za pomocą programu Azure Image Builder.](image-builder-gallery-update-image-version.md)
