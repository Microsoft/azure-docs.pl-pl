---
title: Tworzenie nowej wersji obrazu z istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder (wersja zapoznawcza)
description: Utwórz nową wersję obrazu maszyny wirtualnej z istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder w systemie Windows.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 0a53e8de8dd832e793ae12034c96ce9fe634ed7a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694108"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Wersja zapoznawcza: Tworzenie nowej wersji obrazu maszyny wirtualnej na podstawie istniejącej wersji obrazu przy użyciu narzędzia Azure Image Builder w systemie Windows

W tym artykule pokazano, jak zastosować istniejącą wersję obrazu w [galerii obrazów udostępnionych](../shared-image-galleries.md), zaktualizować ją i opublikować jako nową wersję obrazu w galerii.

Aby skonfigurować obraz, będziemy używać szablonu przykład. JSON. Używany plik JSON jest tutaj: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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

Jeśli użyto [tworzenia obrazu i dystrybucji do galerii obrazów udostępnionych](image-builder-gallery.md) w celu utworzenia galerii obrazów udostępnionych, należy utworzyć już potrzebne zmienne. Jeśli nie, skonfiguruj kilka zmiennych, które mają być używane w tym przykładzie.

W przypadku wersji zapoznawczej Konstruktor obrazów będzie obsługiwał tworzenie obrazów niestandardowych w tej samej grupie zasobów co źródłowy obraz zarządzany. Zaktualizuj nazwę grupy zasobów w tym przykładzie do tej samej grupy zasobów co źródłowy obraz zarządzany.

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

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą polecenia `az account show | grep id` .

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

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej do użytkownika i Ustawianie uprawnień do grupy zasobów
Po skonfigurowaniu tożsamości użytkownika w poprzednim przykładzie wystarczy uzyskać identyfikator zasobu, a następnie dodać ten szablon do tego szablonu programu.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Jeśli masz już własną galerię obrazów udostępnionych i nie wykonano jej w poprzednim przykładzie, musisz przypisać uprawnienia dla konstruktora obrazów, aby uzyskać dostęp do tej grupy zasobów, dzięki czemu będzie można uzyskać dostęp do galerii. Zapoznaj się z instrukcjami przedstawionymi w przykładzie [Tworzenie obrazu i dystrybucja do galerii obrazów udostępnionych](image-builder-gallery.md) .


## <a name="modify-helloimage-example"></a>Modyfikuj przykład helloImage
Możesz przejrzeć przykład, którego zamierzasz użyć, otwierając plik. JSON tutaj: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) wraz z [odwołaniem do szablonu konstruktora obrazów](../linux/image-builder-json.md). 


Pobierz przykład. JSON i skonfiguruj go przy użyciu zmiennych. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Konstruktor obrazów maszyn wirtualnych.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Poczekaj na skompilowanie obrazu i replikację przed przejściem do następnego kroku.


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

## <a name="verify-the-customization"></a>Sprawdzanie dostosowania
Utwórz połączenie Pulpit zdalny z maszyną wirtualną przy użyciu nazwy użytkownika i hasła ustawione podczas tworzenia maszyny wirtualnej. Na maszynie wirtualnej Otwórz wiersz polecenia i wpisz:

```console
dir c:\
```

Powinny teraz być widoczne dwa katalogi:
- `buildActions` została utworzona w pierwszej wersji obrazu.
- `buildActions2` Ten element został utworzony w ramach aktualizacji pierwszej wersji obrazu w celu utworzenia drugiej wersji obrazu.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składnikach pliku JSON używanego w tym artykule, zobacz [Dokumentacja szablonu konstruktora obrazów](../linux/image-builder-json.md).
