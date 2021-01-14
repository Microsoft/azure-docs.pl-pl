---
title: Tworzenie maszyny wirtualnej z systemem Windows za pomocą narzędzia Azure Image Builder przy użyciu programu PowerShell
description: Utwórz maszynę wirtualną z systemem Windows przy użyciu modułu Azure Image Builder PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f94147a09a6d9da75a0d04630822f1e6f738700a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200944"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Wersja zapoznawcza: Tworzenie maszyny wirtualnej z systemem Windows za pomocą narzędzia Azure Image Builder przy użyciu programu PowerShell

W tym artykule pokazano, jak utworzyć dostosowany obraz systemu Windows przy użyciu modułu programu PowerShell dla narzędzia Azure VM Image Builder.

> [!CAUTION]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Gdy moduły programu PowerShell **AZ. ImageBuilder** i **AZ. ManagedServiceIdentity** są w wersji zapoznawczej, należy je zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet z `AllowPrerelease` parametrem. Gdy te moduły programu PowerShell staną się ogólnie dostępne, stają się one częścią przyszłej wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Rejestrowanie funkcji

Jeśli używasz narzędzia Azure Image Builder w wersji zapoznawczej, zarejestruj nową funkcję **VirtualMachineTemplatePreview** .

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

> [!NOTE]
> **RegistrationState** może być w `Registering` stanie przez kilka minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Zarejestruj następujących dostawców zasobów do użycia z subskrypcją platformy Azure, jeśli nie zostały one jeszcze zarejestrowane.

- Microsoft.Compute
- Microsoft. kluczy — magazyn
- Microsoft.Storage
- Microsoft. VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definiowanie zmiennych

Będziesz wielokrotnie używać kilku informacji. Utwórz zmienne do przechowywania informacji.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Utwórz zmienną dla identyfikatora subskrypcji platformy Azure. Aby potwierdzić, że `subscriptionID` zmienna zawiera identyfikator subskrypcji, możesz uruchomić drugi wiersz w poniższym przykładzie.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów na podstawie nazwy w `$imageResourceGroup` zmiennej w regionie określonym w `$location` zmiennej. Ta grupa zasobów służy do przechowywania artefaktu szablonu konfiguracji obrazu i obrazu.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Utwórz tożsamość użytkownika i Ustaw uprawnienia roli

Przyznaj programowi Azure Image Builder uprawnienia do tworzenia obrazów w określonej grupie zasobów przy użyciu poniższego przykładu. Bez tego uprawnienia proces kompilacji obrazu nie zostanie ukończony pomyślnie.

Utwórz zmienne dla definicji roli i nazw tożsamości. Te wartości muszą być unikatowe.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Utwórz tożsamość użytkownika.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Przechowywanie zasobów tożsamości i identyfikatorów głównych w zmiennych.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Przypisywanie uprawnień tożsamości do dystrybucji obrazów

Pobierz plik konfiguracji JSON i zmodyfikuj go na podstawie ustawień zdefiniowanych w tym artykule.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Utwórz definicję roli.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Przypisz definicję roli do nazwy głównej usługi Image Builder.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat o błędzie:_Przekroczono limit "New-AzRoleDefinition: definicja roli. Nie można utworzyć więcej definicji ról._", zobacz [Rozwiązywanie problemów z usługą Azure RBAC](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Tworzenie galerii obrazów udostępnionych

Utwórz galerię.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Utwórz definicję galerii.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Tworzenie obrazu

Utwórz obiekt źródłowy programu Azure Image Builder. Zobacz sekcję [Znajdowanie obrazów maszyn wirtualnych z systemem Windows w witrynie Azure Marketplace z Azure PowerShell](./cli-ps-findimage.md) dla prawidłowych wartości parametrów.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Utwórz obiekt dystrybutora programu Azure Image Builder.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Utwórz obiekt dostosowania programu Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

Utwórz szablon usługi Azure Image Builder.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Po zakończeniu zostanie zwrócony komunikat, a szablon Konfiguracja konstruktora obrazów zostanie utworzony w `$imageResourceGroup` .

Aby określić, czy proces tworzenia szablonu zakończył się pomyślnie, można użyć poniższego przykładu.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

W tle Konstruktor obrazów tworzy również tymczasową grupę zasobów w ramach subskrypcji. Ta grupa zasobów jest używana do kompilowania obrazu. Jest w formacie: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Nie usuwaj bezpośrednio tymczasowej grupy zasobów. Usuń artefakt szablonu obrazu, co spowoduje usunięcie tymczasowej grupy zasobów.

Jeśli usługa zgłasza błąd podczas przesłania szablonu konfiguracji obrazu:

- Zobacz [Rozwiązywanie problemów z błędami kompilacji obrazu maszyny wirtualnej platformy Azure (AIB)](../linux/image-builder-troubleshoot.md).
- Przed ponowieniem próby Usuń szablon przy użyciu poniższego przykładu.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Uruchom kompilację obrazu

Prześlij konfigurację obrazu do usługi Konstruktor obrazów maszyn wirtualnych.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Poczekaj na zakończenie procesu kompilacji obrazu. Ten krok może potrwać do godziny.

Jeśli wystąpią błędy, przejrzyj [temat Rozwiązywanie problemów z błędami kompilacji obrazu maszyny wirtualnej platformy Azure (AIB)](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przechowywanie poświadczeń logowania dla maszyny wirtualnej w zmiennej. Hasło musi być złożone.

```azurepowershell-interactive
$Cred = Get-Credential
```

Utwórz maszynę wirtualną przy użyciu utworzonego obrazu.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>Sprawdzanie dostosowania

Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. W ramach maszyny wirtualnej Otwórz program PowerShell i uruchom polecenie `Get-Content` , jak pokazano w następującym przykładzie:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Dane wyjściowe powinny być wyświetlane na podstawie zawartości pliku utworzonego podczas procesu dostosowywania obrazu.

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, można je usunąć, uruchamiając następujące przykłady.

### <a name="delete-the-image-builder-template"></a>Usuwanie szablonu konstruktora obrazów

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Usuwanie grupy zasobów obrazu

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku JSON używanego w tym artykule, zobacz [Dokumentacja szablonu konstruktora obrazów](../linux/image-builder-json.md).
