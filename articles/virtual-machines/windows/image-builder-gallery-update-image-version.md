---
title: Tworzenie nowej wersji obrazu z istniejącej wersji obrazu przy użyciu programu Azure Image Builder (wersja zapoznawcza)
description: Utwórz nową wersję obrazu maszyny Wirtualnej z istniejącej wersji obrazu przy użyciu programu Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869537"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Wersja zapoznawcza: tworzenie nowej wersji obrazu maszyny Wirtualnej z istniejącej wersji obrazu przy użyciu programu Azure Image Builder

W tym artykule pokazano, jak zrobić istniejącą wersję obrazu w [galerii obrazów udostępnionych,](shared-image-galleries.md)zaktualizować ją i opublikować jako nową wersję obrazu w galerii.

Będziemy używać przykładowego szablonu .json do skonfigurowania obrazu. Plik .json używamy jest tutaj: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Zarejestruj funkcje
Aby korzystać z usługi Azure Image Builder podczas podglądu, należy zarejestrować nową funkcję.

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
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
```

Jeśli nie mówią zarejestrowane, uruchom następujące czynności:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Ustawianie zmiennych i uprawnień

Jeśli [użyto utworzenia obrazu i dystrybucji do galerii obrazów udostępnionych](image-builder-gallery.md) w celu utworzenia galerii obrazów udostępnionych, utworzono już potrzebne zmienne. Jeśli nie, należy skonfigurować niektóre zmienne, które mają być używane w tym przykładzie.

W przypadku wersji zapoznawczej konstruktor obrazów będzie obsługiwał tworzenie obrazów niestandardowych tylko w tej samej grupie zasobów, co obraz zarządzany źródłowy. Zaktualizuj nazwę grupy zasobów w tym przykładzie, aby była tą samą grupą zasobów co obraz zarządzany źródłowy.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to uzyskać `az account show | grep id`za pomocą programu .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Pobierz wersję obrazu, którą chcesz zaktualizować.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Jeśli masz już własną galerię obrazów udostępnionych i nie postępuj zgodnie z poprzednim przykładem, musisz przypisać uprawnienia do Konstruktora obrazów, aby uzyskać dostęp do grupy zasobów, aby mogła uzyskać dostęp do galerii.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modyfikowanie przykładu helloImage
Możesz przejrzeć przykład, którego mamy zamiar użyć, otwierając plik .json tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołaniem do szablonu Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Pobierz przykład .json i skonfiguruj go ze zmiennymi. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi konstruktora obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Rozpocznij kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Poczekaj, aż obraz zostanie utworzony i replikacji przed przejściem do następnego kroku.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Weryfikowanie dostosowywania
Utwórz połączenie pulpitu zdalnego z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawionego podczas tworzenia maszyny Wirtualnej. Wewnątrz maszyny Wirtualnej otwórz monit cmd i wpisz:

```console
dir c:\
```

Powinny być teraz widoczne dwa katalogi:
- `buildActions`który został utworzony w pierwszej wersji obrazu.
- `buildActions2`który został utworzony jako część aktualizacji pierwszej wersji obrazu, aby utworzyć drugą wersję obrazu.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku .json użytego w tym artykule, zobacz [Odwołanie do szablonu konstruktora obrazów](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).