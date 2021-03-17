---
title: Konstruktor obrazów — Tworzenie obrazu pulpitu wirtualnego systemu Windows
description: Utwórz obraz maszyny wirtualnej platformy Azure dla pulpitu wirtualnego systemu Windows przy użyciu narzędzia Azure Image Builder w programie PowerShell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602022"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Tworzenie obrazu pulpitu wirtualnego systemu Windows przy użyciu narzędzia Azure VM Image Builder i programu PowerShell

W tym artykule opisano sposób tworzenia obrazu pulpitu wirtualnego systemu Windows przy użyciu następujących dostosowań:

* Instalowanie [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Uruchamianie [skryptu optymalizacji pulpitu wirtualnego systemu Windows](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) z repozytorium społeczności.
* Zainstaluj [program Microsoft Teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd).
* [Uruchom ponownie](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* Uruchom [Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer)

Zobaczymy, jak zautomatyzować ten proces przy użyciu konstruktora obrazów maszyn wirtualnych platformy Azure, a następnie rozproszyć obraz do [udostępnionej galerii obrazów](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), gdzie można przeprowadzić replikację do innych regionów, kontrolować skalę i udostępniać obraz wewnątrz organizacji i poza nią.


Aby uprościć wdrażanie konfiguracji konstruktora obrazu, w tym przykładzie używa się szablonu Azure Resource Manager z szablonem konstruktora obrazów zagnieżdżone wewnątrz. Zapewnia to inne korzyści, takie jak zmienne i dane wejściowe parametrów. Parametry można także przekazać z wiersza polecenia.

Ten artykuł jest przeznaczony do wykonania kopiowania i wklejania.

> [!NOTE]
> Skrypty umożliwiające zainstalowanie aplikacji znajdują się w witrynie [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). Są one przeznaczone wyłącznie do testowania, a nie dla obciążeń produkcyjnych. 

## <a name="tips-for-building-windows-images"></a>Porady dotyczące tworzenia obrazów systemu Windows 

- Rozmiar maszyny wirtualnej — domyślny rozmiar maszyny wirtualnej to `Standard_D1_v2` , która nie jest odpowiednia dla systemu Windows. Użyj `Standard_D2_v2` lub nowszej.
- W tym przykładzie są wykorzystywane [skrypty dostosowywania programu PowerShell](../linux/image-builder-json.md). Musisz użyć tych ustawień lub kompilacja przestanie odpowiadać.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Na przykład:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Dodawanie komentarza do kodu — dziennik kompilacji AIB (Customization. log) jest niezwykle pełny, w przypadku dodawania komentarzy do skryptów przy użyciu polecenia "write-host" te dane będą wysyłane do dzienników i ułatwiają rozwiązywanie problemów.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Kody zakończenia — AIB oczekuje, że wszystkie skrypty zwracają kod zakończenia 0, dowolny niezerowy kod zakończenia spowoduje AIB niepowodzenie dostosowania i zatrzymywanie kompilacji. Jeśli masz złożone skrypty, Dodaj instrumentację i wyemituj kody zakończenia, zostaną one wyświetlone w obszarze Dostosowywanie. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test: Przetestuj i Przetestuj swój kod przed autonomiczną maszyną wirtualną, upewnij się, że nie ma żadnych monitów użytkownika, używasz odpowiedniego uprawnienia itp.

- Sieć — `Set-NetAdapterAdvancedProperty` . Jest to ustawiane w skrypcie optymalizacji, ale kończy się niepowodzeniem kompilacji AIB, ponieważ odłącza sieć. Jest on objęty badaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć zainstalowane najnowsze polecenia CmdLet Azure PowerShell. zobacz [tutaj](https://docs.microsoft.com/powershell/azure/overview) , aby uzyskać szczegółowe informacje dotyczące instalacji.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Skonfiguruj środowisko i zmienne

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Uprawnienia, tożsamość użytkownika i rola 


 Utwórz tożsamość użytkownika.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Przypisywanie uprawnień do tożsamości w celu dystrybucji obrazów. To polecenie spowoduje pobranie i zaktualizowanie szablonu przy użyciu parametrów określonych wcześniej.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Jeśli widzisz ten błąd: przekroczono limit "New-AzRoleDefinition: definicja roli. Nie można utworzyć więcej definicji ról. ' Zapoznaj się z tym artykułem, aby rozwiązać ten problem: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych 

Jeśli nie masz jeszcze udostępnionej galerii obrazów, musisz ją utworzyć.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Konfigurowanie szablonu obrazu

Na potrzeby tego przykładu szablon jest gotowy do pobrania i zaktualizowania szablonu przy użyciu parametrów określonych wcześniej. spowoduje to zainstalowanie FsLogix, optymalizacji systemu operacyjnego, Microsoft Teams i uruchomienie Windows Update na końcu.

Jeśli otworzysz szablon, zobaczysz w właściwości source używanym obrazie, w tym przykładzie jest używany obraz wielosesyjny win 10. 

### <a name="windows-10-images"></a>Obrazy systemu Windows 10
Dwa typy kluczy, z którymi należy się zapoznać: wielosesyjne i pojedyncze sesje.

Obrazy wielu sesji są przeznaczone do użycia w puli. Oto przykład szczegółów obrazu na platformie Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Pojedyncze obrazy sesji są przeznaczone do użycia. Oto przykład szczegółów obrazu na platformie Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Możesz również zmienić dostępne obrazy Win10:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Pobierz szablon i skonfiguruj

Teraz musisz pobrać szablon i skonfigurować go do użycia.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Możesz wyświetlić [szablon](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json), który będzie widoczny dla wszystkich kodów.


## <a name="submit-the-template"></a>Prześlij szablon

Szablon musi zostać przesłany do usługi. spowoduje to pobranie wszelkich artefaktów zależnych (takich jak skrypty), zweryfikowanie, sprawdzenie uprawnień i zapisanie ich w tymczasowej grupie zasobów, z prefiksem *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Tworzenie obrazu
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Polecenie nie czeka, aż usługa Image Builder ukończy kompilację obrazu, można wysłać zapytanie do poniższego stanu.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
Teraz kompilacja została zakończona można utworzyć maszynę wirtualną na podstawie obrazu, używając przykładów z tego [miejsca](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Czyszczenie

Najpierw należy usunąć szablon grupy zasobów. nie należy usuwać całej grupy zasobów, w przeciwnym razie nie będzie można oczyścić grupy zasobów przemieszczania (*IT_*) używanej przez AIB.

Usuń szablon obrazu.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Usuń przypisanie roli.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Usuń grupę zasobów.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Więcej przykładów można uzyskać [w witrynie GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
