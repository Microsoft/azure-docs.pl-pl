---
title: Tworzenie obrazu maszyny wirtualnej i używanie tożsamości zarządzanej przypisanej przez użytkownika do uzyskiwania dostępu do plików w usłudze Azure Storage (wersja zapoznawcza)
description: Utwórz obraz maszyny wirtualnej za pomocą narzędzia Azure Image Builder, który może uzyskiwać dostęp do plików przechowywanych w usłudze Azure Storage przy użyciu tożsamości zarządzanej przypisanej przez użytkownika.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: f5734d4b1871dd285fc83a72631f7d645e0b72ff
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307266"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Tworzenie obrazu i używanie tożsamości zarządzanej przypisanej przez użytkownika w celu uzyskiwania dostępu do plików w usłudze Azure Storage 

Usługa Azure Image Builder obsługuje używanie skryptów lub kopiowanie plików z wielu lokalizacji, takich jak GitHub i Azure Storage itp. Aby można było korzystać z nich, muszą one mieć dostęp zewnętrznie do programu Azure Image Builder, ale obiekty blob usługi Azure Storage mogą być chronione przy użyciu tokenów SAS.

W tym artykule pokazano, jak utworzyć dostosowany obraz przy użyciu konstruktora obrazów maszyn wirtualnych platformy Azure, w którym usługa będzie korzystać z [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md) w celu uzyskiwania dostępu do plików w usłudze Azure Storage w celu dostosowania obrazu, bez konieczności udostępnienia publicznie plików lub skonfigurowania tokenów SAS.

W poniższym przykładzie utworzysz dwie grupy zasobów, jedna zostanie użyta w przypadku obrazu niestandardowego, a druga będzie hostować konto usługi Azure Storage, które zawiera plik skryptu. Symuluje to realne scenariusze, w których mogą występować artefakty kompilacji lub pliki obrazów na różnych kontach magazynu poza konstruktorem obrazów. Utworzysz tożsamość przypisaną przez użytkownika, a następnie przyznasz te uprawnienia do odczytu pliku skryptu, ale nie ustawisz publicznego dostępu do tego pliku. Następnie użyjesz konfiguratora powłoki do pobrania i uruchomienia tego skryptu z konta magazynu.


> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Rejestrowanie funkcji
Aby korzystać z usługi Azure Image Builder w wersji zapoznawczej, należy zarejestrować nową funkcję.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Sprawdź stan rejestracji funkcji.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Sprawdź swoją rejestrację.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą polecenia `az account show | grep id` .

```console
subscriptionID=<Your subscription ID>
```

Utwórz grupy zasobów dla obrazu i magazynu skryptów.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Utwórz tożsamość przypisaną przez użytkownika i Ustaw uprawnienia do grupy zasobów.

Konstruktor obrazów będzie używał podanej [tożsamości użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) w celu dodania obrazu do grupy zasobów. W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania dystrybucji obrazu. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Utwórz magazyn i skopiuj przykładowy skrypt do niego z usługi GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Nadaj konstruktorowi obrazu uprawnienia do tworzenia zasobów w grupie zasobów obrazu. `--assignee`Wartość jest identyfikatorem użytkownika-tożsamości.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Modyfikowanie przykładu

Pobierz przykładowy plik JSON i skonfiguruj go przy użyciu utworzonych zmiennych.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Poczekaj na zakończenie kompilacji. Może to potrwać około 15 minut.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie obrazu. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Po utworzeniu maszyny wirtualnej Rozpocznij sesję SSH z maszyną wirtualną.

```console
ssh aibuser@<publicIp>
```

Obraz został dostosowany wraz z komunikatem dnia zaraz po nawiązaniu połączenia SSH.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Czyszczenie

Gdy skończysz, możesz usunąć te zasoby, jeśli nie są już potrzebne.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z pracą z konstruktorem obrazów platformy Azure, zobacz [Rozwiązywanie problemów](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).
