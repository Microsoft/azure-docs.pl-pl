---
title: Używanie usługi Azure Image Builder dla maszyn wirtualnych z systemem Linux, które umożliwiają dostęp do istniejącej sieci wirtualnej platformy Azure (wersja zapoznawcza)
description: Tworzenie obrazów maszyn wirtualnych z systemem Linux za pomocą usługi Azure Image Builder umożliwiającej dostęp do istniejącej sieci wirtualnej platformy Azure
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565312"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Korzystanie z usługi Azure Image Builder dla maszyn wirtualnych z systemem Linux, które umożliwiają dostęp do istniejącej sieci wirtualnej platformy Azure

W tym artykule pokazano, jak za pomocą programu Azure Image Builder utworzyć podstawowy dostosowany obraz systemu Linux, który ma dostęp do istniejących zasobów w sieci wirtualnej. Utworzona maszyna wirtualna kompilacji jest wdrażana w nowej lub istniejącej sieci wirtualnej określonej w ramach subskrypcji. Gdy korzystasz z istniejącej sieci wirtualnej platformy Azure, usługa Azure Image Builder nie wymaga łączności z siecią publiczną.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Rejestrowanie funkcji

Najpierw musisz zarejestrować się w usłudze Azure Image Builder. Rejestracja przyznaje usłudze uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów oraz zarządzania nią. Usługa ma również uprawnienia do dodawania zasobów do grupy, która jest wymagana dla kompilacji obrazu.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Ustawianie zmiennych i uprawnień 

Będziesz wielokrotnie używać niektórych informacji. Utwórz niektóre zmienne do przechowywania tych informacji.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Utwórz grupę zasobów.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Konfigurowanie sieci

Jeśli nie masz istniejącego VNET\Subnet\NSG, użyj następującego skryptu, aby go utworzyć.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Dodaj regułę sieciowej grupy zabezpieczeń

Ta reguła umożliwia łączność z modułu równoważenia obciążenia programu Azure Image Builder z maszyną wirtualną serwera proxy. Port 60001 dotyczy usługi OSs dla systemu Linux, a port 60000 jest przeznaczony dla usługi Windows OSs. Maszyna wirtualna serwera proxy nawiązuje połączenie z maszyną wirtualną kompilacji przy użyciu portu 22 dla systemu Linux lub portu 5986 dla usługi Windows OSs.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Wyłącz zasady usługi prywatnej w podsieci

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Aby uzyskać więcej informacji na temat sieci konstruktora obrazu, zobacz [Opcje sieci usługi Azure Image Builder](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Modyfikowanie przykładowego szablonu i tworzenie roli

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Ustawianie uprawnień do grupy zasobów

Konstruktor obrazów będzie używał podanej [tożsamości użytkownika](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) w celu dodania obrazu do galerii udostępnionych obrazów systemu Azure (SIG). W tym przykładzie utworzysz definicję roli platformy Azure, która zawiera szczegółowe akcje do wykonania dystrybucji obrazu do SIG. Definicja roli zostanie następnie przypisana do tożsamości użytkownika.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Zamiast udzielania mniejszej szczegółowości i zwiększonego poziomu konstruktora obrazu, można utworzyć dwie role. Jeden z nich daje uprawnienia konstruktora do tworzenia obrazu, drugi pozwala na połączenie maszyny wirtualnej kompilacji i modułu równoważenia obciążenia z siecią wirtualną.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Aby uzyskać więcej informacji o uprawnieniach, zobacz [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure](image-builder-permissions-cli.md) lub [Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Tworzenie obrazu

Prześlij konfigurację obrazu do usługi Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Uruchom kompilację obrazu.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Tworzenie obrazu i replikowanie go do obu regionów może chwilę potrwać. Przed przejściem do tworzenia maszyny wirtualnej Zaczekaj, aż ta część zostanie zakończona.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie wersji obrazu, która została utworzona przez usługę Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Nawiąż połączenie SSH z maszyną wirtualną.

```bash
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


Poniższe elementy usuwają utworzony obraz oraz wszystkie inne pliki zasobów. Przed usunięciem zasobów upewnij się, że to wdrożenie zostało zakończone.

W przypadku usuwania zasobów galerii obrazów należy usunąć wszystkie wersje obrazu, aby można było usunąć definicję obrazu używaną do ich tworzenia. Aby usunąć galerię, należy najpierw usunąć wszystkie definicje obrazu z galerii.

Usuń szablon Konstruktor obrazów.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Usuwanie przypisań, ról i tożsamości uprawnień
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Usuń grupę zasobów.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Jeśli utworzono sieć wirtualną dla tego przewodnika Szybki Start, możesz usunąć sieć wirtualną, jeśli nie jest już używana.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [udostępnionych galerii obrazów platformy Azure](../shared-image-galleries.md).
