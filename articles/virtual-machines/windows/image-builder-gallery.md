---
title: Korzystanie z programu Azure Image Builder z galerią obrazów dla maszyn wirtualnych z systemem Windows (wersja zapoznawcza)
description: Tworzenie wersji obrazu udostępnionej galerii platformy Azure przy użyciu narzędzia Azure Image Builder i Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: e8caf9f742217161c60ce90351989999f18adabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694091"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu systemu Windows i dystrybuowanie go do galerii obrazów udostępnionych 

W tym artykule opisano, jak można użyć programu Azure Image Builder i Azure PowerShell, aby utworzyć wersję obrazu w [galerii obrazów udostępnionych](../shared-image-galleries.md), a następnie dystrybuować obraz globalnie. Można to również zrobić przy użyciu [interfejsu wiersza polecenia platformy Azure](../linux/image-builder-gallery.md).

Aby skonfigurować obraz, będziemy używać szablonu JSON. Używany plik JSON jest tutaj: [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Będziemy pobierać i edytować lokalną wersję szablonu, więc ten artykuł zostanie zapisany przy użyciu lokalnej sesji programu PowerShell.

W celu rozesłania obrazu do galerii obrazów udostępnionych szablon używa [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) jako wartości `distribute` sekcji szablonu.

Usługa Azure Image Builder automatycznie uruchamia program Sysprep w celu uogólnienia obrazu. jest to ogólne polecenie programu Sysprep, które można [przesłonić](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) w razie potrzeby. 

Dowiedzieć się, ile razy są dostosowywane warstwy. Można uruchomić polecenie Sysprep do 8 razy w pojedynczym obrazie systemu Windows. Po uruchomieniu programu Sysprep 8 należy ponownie utworzyć obraz systemu Windows. Aby uzyskać więcej informacji, zobacz [limity czasu, w którym można uruchomić program Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby korzystać z usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Sprawdź stan rejestracji funkcji.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Poczekaj `RegistrationState` `Registered` przed przejściem do następnego kroku.

Sprawdź rejestracje dostawcy. Upewnij się, że każdy zwraca wartość `Registered` .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Jeśli dostawcy nie zwracają żadnych zwrotów `Registered` , należy zarejestrować dostawców w następujących przypadkach:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

Zainstaluj moduły programu PowerShell:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Tworzenie zmiennych

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji. Zastąp wartości dla zmiennych, takich jak `username` i `vmpassword` , z własnymi informacjami.

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

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej do użytkownika i Ustawianie uprawnień do grupy zasobów
Konstruktor obrazów będzie używał podanej [tożsamości użytkownika](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) w celu dodania obrazu do galerii udostępnionych obrazów systemu Azure (SIG). W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania dystrybucji obrazu do SIG. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Przypisywanie uprawnień tożsamości do dystrybucji obrazów

To polecenie spowoduje pobranie szablonu definicji roli platformy Azure i zaktualizowanie szablonu za pomocą parametrów określonych wcześniej.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Aby użyć konstruktora obrazów z udostępnioną galerią obrazów, musisz mieć istniejącą galerię obrazów i definicję obrazu. Konstruktor obrazów nie utworzy galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, Zacznij od utworzenia ich. Najpierw utwórz galerię obrazów.

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



## <a name="download-and-configure-the-template"></a>Pobieranie i Konfigurowanie szablonu

Pobierz szablon JSON i skonfiguruj go przy użyciu zmiennych.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
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
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Szablon musi zostać przesłany do usługi. spowoduje to pobranie wszelkich artefaktów zależnych, takich jak skrypty, i zapisanie ich w tymczasowej grupie zasobów, z prefiksem *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

W celu skompilowania obrazu potrzebnego do wywołania elementu "Run" w szablonie.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

Tworzenie obrazu i replikowanie go do obu regionów może chwilę potrwać. Przed przejściem do tworzenia maszyny wirtualnej Zaczekaj, aż ta część zostanie zakończona.

Aby uzyskać informacje na temat opcji automatyzowania pobierania stanu kompilacji obrazu, zobacz [Readme]
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie wersji obrazu, która została utworzona przez usługę Azure Image Builder.

Pobierz utworzoną wersję obrazu.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Utwórz maszynę wirtualną w drugim regionie, w której zainstalowano obraz.

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

## <a name="verify-the-customization"></a>Sprawdzanie dostosowania
Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. Na maszynie wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinien zostać wyświetlony katalog o nazwie `buildActions` , który został utworzony podczas dostosowywania obrazu.


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, **Pomiń ten krok** i przejdź do, aby [użyć usługi Azure Image Builder do utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie utworzonego obrazu wraz ze wszystkimi innymi plikami zasobów. Przed usunięciem zasobów upewnij się, że to wdrożenie zostało zakończone.

Najpierw należy usunąć szablon grupy zasobów. w przeciwnym razie tymczasowa Grupa zasobów (*IT_*) używana przez AIB nie zostanie wyczyszczona.

Pobieranie ResourceID szablonu obrazu. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Usuń szablon obrazu.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Usuń przypisanie roli

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Usuń definicje

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

Usuń tożsamość

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

Usuń grupę zasobów.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaktualizować utworzoną wersję obrazu, zobacz [Korzystanie z programu Azure Image Builder w celu utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).
