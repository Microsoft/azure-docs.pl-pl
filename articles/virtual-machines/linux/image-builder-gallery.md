---
title: Korzystanie z programu Azure Image Builder & udostępnionej galerii obrazów dla maszyn wirtualnych z systemem Linux (wersja zapoznawcza)
description: Dowiedz się, jak używać programu Azure Image Builder i interfejsu wiersza polecenia platformy Azure, aby utworzyć wersję obrazu w galerii obrazów udostępnionych, a następnie dystrybuować obraz globalnie.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: cf7f5ed4a27772a89d0356c60e6f7135786ca07d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695604"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Wersja zapoznawcza: Tworzenie obrazu systemu Linux i dystrybuowanie go do galerii obrazów udostępnionych 

W tym artykule pokazano, jak można użyć konstruktora obrazów platformy Azure i interfejsu wiersza polecenia platformy Azure, aby utworzyć wersję obrazu w [galerii obrazów udostępnionych](../shared-image-galleries.md), a następnie dystrybuować obraz globalnie. Można to również zrobić przy użyciu [Azure PowerShell](../windows/image-builder-gallery.md).


Aby skonfigurować obraz, będziemy używać szablonu przykład. JSON. Używany plik JSON jest tutaj: [helloImageTemplateforSIG.json](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

W celu rozesłania obrazu do galerii obrazów udostępnionych szablon używa [sharedImage](image-builder-json.md#distribute-sharedimage) jako wartości `distribute` sekcji szablonu.

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

Będziemy wielokrotnie używać niektórych informacji, więc utworzymy pewne zmienne do przechowywania tych informacji.

W przypadku wersji zapoznawczej Konstruktor obrazów będzie obsługiwał tworzenie obrazów niestandardowych w tej samej grupie zasobów co źródłowy obraz zarządzany. Zaktualizuj nazwę grupy zasobów w tym przykładzie do tej samej grupy zasobów co źródłowy obraz zarządzany.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Utwórz zmienną dla identyfikatora subskrypcji. Można to zrobić za pomocą polecenia `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Tworzenie tożsamości przypisanej do użytkownika i Ustawianie uprawnień do grupy zasobów
Konstruktor obrazów będzie używał podanej [tożsamości użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) w celu dodania obrazu do galerii udostępnionych obrazów systemu Azure (SIG). W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania dystrybucji obrazu do SIG. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Tworzenie definicji obrazu i galerii

Aby użyć konstruktora obrazów z udostępnioną galerią obrazów, musisz mieć istniejącą galerię obrazów i definicję obrazu. Konstruktor obrazów nie utworzy galerii obrazów i definicji obrazu.

Jeśli nie masz jeszcze galerii i definicji obrazu do użycia, Zacznij od utworzenia ich. Najpierw utwórz galerię obrazów.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Następnie Utwórz definicję obrazu.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Pobierz i Skonfiguruj plik JSON

Pobierz szablon JSON i skonfiguruj go przy użyciu zmiennych.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Ta Następna część spowoduje utworzenie wersji obrazu w galerii. 

Prześlij konfigurację obrazu do usługi Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Tworzenie obrazu i replikowanie go do obu regionów może chwilę potrwać. Przed przejściem do tworzenia maszyny wirtualnej Zaczekaj, aż ta część zostanie zakończona.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie wersji obrazu, która została utworzona przez usługę Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Nawiąż połączenie SSH z maszyną wirtualną.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Obraz został dostosowany wraz z *komunikatem dnia* zaraz po nawiązaniu połączenia SSH.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz teraz spróbować ponownie dostosować wersję obrazu, aby utworzyć nową wersję tego samego obrazu, pomiń następne kroki i przejdź do, aby [użyć usługi Azure Image Builder do utworzenia innej wersji obrazu](image-builder-gallery-update-image-version.md).


Spowoduje to usunięcie utworzonego obrazu wraz ze wszystkimi innymi plikami zasobów. Przed usunięciem zasobów upewnij się, że to wdrożenie zostało zakończone.

W przypadku usuwania zasobów galerii obrazów należy usunąć wszystkie wersje obrazu, aby można było usunąć definicję obrazu używaną do ich tworzenia. Aby usunąć galerię, należy najpierw usunąć wszystkie definicje obrazu z galerii.

Usuń szablon Konstruktor obrazów.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Usuwanie przypisań uprawnień, ról i tożsamości
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Pobierz wersję obrazu utworzoną przez konstruktora obrazów, która zawsze zaczyna się od `0.` , a następnie Usuń wersję obrazu

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Usuń definicję obrazu.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Usuń galerię.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Usuń grupę zasobów.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [udostępnionych galerii obrazów platformy Azure](../shared-image-galleries.md).
