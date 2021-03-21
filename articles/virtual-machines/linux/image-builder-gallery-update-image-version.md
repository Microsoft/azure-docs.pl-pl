---
title: Tworzenie nowej wersji obrazu maszyny wirtualnej na podstawie istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder (wersja zapoznawcza)
description: Utwórz nową wersję obrazu maszyny wirtualnej z istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder w systemie Linux.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 0887051ffa396f1eac8bc00dc2437b8e92bec45a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695638"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Wersja zapoznawcza: Tworzenie nowej wersji obrazu maszyny wirtualnej na podstawie istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder w systemie Linux

W tym artykule pokazano, jak zastosować istniejącą wersję obrazu w [galerii obrazów udostępnionych](../shared-image-galleries.md), zaktualizować ją i opublikować jako nową wersję obrazu w galerii.

Aby skonfigurować obraz, będziemy używać szablonu przykład. JSON. Używany plik JSON jest tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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
az provider show -n Microsoft.Network | grep registrationState
```

Jeśli nie powiedzie się, uruchom następujące polecenie:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>Ustawianie zmiennych i uprawnień

Jeśli użyto [tworzenia obrazu i dystrybucji do galerii obrazów udostępnionych](image-builder-gallery.md) w celu utworzenia galerii obrazów udostępnionych, należy wcześniej utworzyć pewne zmienne, których potrzebujemy. Jeśli nie, skonfiguruj kilka zmiennych, które mają być używane w tym przykładzie.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą polecenia `az account show | grep id` .

```console
subscriptionID=<Subscription ID>
```

Pobierz wersję obrazu, którą chcesz zaktualizować.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej do użytkownika i Ustawianie uprawnień do grupy zasobów
Po skonfigurowaniu tożsamości użytkownika w poprzednim przykładzie wystarczy uzyskać identyfikator zasobu, a następnie dodać ten szablon do tego szablonu programu.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Jeśli masz już własną galerię obrazów udostępnionych i nie wykonano jej w poprzednim przykładzie, musisz przypisać uprawnienia dla konstruktora obrazów, aby uzyskać dostęp do tej grupy zasobów, dzięki czemu będzie można uzyskać dostęp do galerii. Zapoznaj się z instrukcjami przedstawionymi w przykładzie [Tworzenie obrazu i dystrybucja do galerii obrazów udostępnionych](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>Modyfikuj przykład helloImage
Możesz przejrzeć przykład, którego zamierzasz użyć, otwierając plik. JSON tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołaniem do szablonu konstruktora obrazów](image-builder-json.md). 


Pobierz przykład. JSON i skonfiguruj go przy użyciu zmiennych. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Konstruktor obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Poczekaj na skompilowanie obrazu i replikację przed przejściem do następnego kroku.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Utwórz połączenie SSH z maszyną wirtualną przy użyciu publicznego adresu IP maszyny wirtualnej.

```console
ssh azureuser@<pubIp>
```

Obraz został dostosowany przy użyciu "komunikatu o dniu" zaraz po nawiązaniu połączenia SSH.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Wpisz, `exit` Aby zamknąć połączenie SSH.

Możesz również wyświetlić listę wersji obrazu, które są teraz dostępne w galerii.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku JSON używanego w tym artykule, zobacz [Dokumentacja szablonu konstruktora obrazów](../linux/image-builder-json.md).
