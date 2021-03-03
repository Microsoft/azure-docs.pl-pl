---
title: Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure
description: Skonfiguruj wymagania dotyczące usługi Azure VM Image Builder, w tym uprawnienia i uprawnienia za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: accefe6644f8959bb9426a154d4f33e0834fc06c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674881"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure Image Builder wymaga konfiguracji uprawnień i uprawnień przed rozpoczęciem tworzenia obrazu. W poniższych sekcjach szczegółowo opisano, jak skonfigurować możliwe scenariusze przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Rejestrowanie funkcji

Najpierw musisz zarejestrować się w usłudze Azure Image Builder. Rejestracja przyznaje usłudze uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów oraz zarządzania nią. Usługa ma również uprawnienia do dodawania zasobów do grupy, która jest wymagana dla kompilacji obrazu.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika platformy Azure

Konstruktor obrazów platformy Azure wymaga utworzenia [tożsamości zarządzanej przypisanej przez użytkownika na platformie Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Konstruktor obrazów platformy Azure używa tożsamości zarządzanej przypisanej przez użytkownika do odczytywania obrazów, zapisywania obrazów i uzyskiwania dostępu do kont usługi Azure Storage. Przyznasz tożsamości uprawnienia do wykonywania określonych akcji w ramach subskrypcji.

> [!NOTE]
> Wcześniej Konstruktor obrazów platformy Azure używał głównej nazwy usługi Azure Image Builder (SPN) do przyznawania uprawnień do grup zasobów obrazu. Korzystanie z nazwy SPN będzie przestarzałe. Zamiast tego użyj tożsamości zarządzanej przypisanej przez użytkownika.

W poniższym przykładzie przedstawiono sposób tworzenia tożsamości zarządzanej przypisanej przez użytkownika na platformie Azure. Zastąp ustawienia symboli zastępczych, aby ustawić zmienne.

| Ustawienie | Opis |
|---------|-------------|
| \<Resource group\> | Grupa zasobów, w której ma zostać utworzona tożsamość zarządzana przypisana przez użytkownika. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Aby uzyskać więcej informacji na temat tożsamości przypisanych przez użytkownika platformy Azure, zobacz dokumentację dotyczącą [zarządzanej tożsamości przypisanej przez użytkownika platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) dotyczącą sposobu tworzenia tożsamości.

## <a name="allow-image-builder-to-distribute-images"></a>Zezwalaj programowi Image Builder na dystrybuowanie obrazów

Aby program Azure Image Builder rozpowszechniał obrazy (obrazy zarządzane/Galeria obrazów udostępnionych), usługa Azure Image Builder musi dodawać obrazy do tych grup zasobów. Aby udzielić wymaganych uprawnień, należy utworzyć przypisaną użytkownikowi tożsamość zarządzaną oraz udzielić uprawnień IT w grupie zasobów, w której utworzono obraz. Konstruktor obrazów platformy **Azure nie ma** uprawnień dostępu do zasobów w innych grupach zasobów w ramach subskrypcji. Należy wykonać jawne akcje, aby zezwolić na dostęp, aby uniknąć awarii kompilacji.

Nie musisz przyznawać uprawnień współautora tożsamości zarządzanej przez użytkownika w grupie zasobów na potrzeby dystrybuowania obrazów. Jednak tożsamość zarządzana przypisana przez użytkownika wymaga następujących uprawnień platformy Azure `Actions` w grupie zasobów dystrybucji:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Jeśli jest dystrybuowana do galerii obrazów udostępnionych, potrzebne są również:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Uprawnienie do dostosowywania istniejących obrazów

Aby program Azure Image Builder mógł tworzyć obrazy ze źródłowych obrazów niestandardowych (obrazów zarządzanych/galerii obrazów udostępnionych), usługa Azure Image Builder musi mieć możliwość odczytywania obrazów w tych grupach zasobów. Aby udzielić wymaganych uprawnień, należy utworzyć przypisaną użytkownikowi tożsamość zarządzaną oraz udzielić praw IT w grupie zasobów, w której znajduje się obraz.

Kompiluj z istniejącego obrazu niestandardowego:

```Actions
Microsoft.Compute/galleries/read
```

Kompiluj z istniejącej wersji galerii obrazów udostępnionych:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Uprawnienie do dostosowywania obrazów w sieci wirtualnych

Usługa Azure Image Builder oferuje możliwość wdrażania istniejącej sieci wirtualnej w ramach subskrypcji i używania jej w taki sposób, aby umożliwić dostosowywanie dostępu do połączonych zasobów.

Nie musisz przyznawać uprawnień współautora tożsamości zarządzanej przez użytkownika w grupie zasobów, aby wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej. Jednak tożsamość zarządzana przypisana przez użytkownika wymaga następujących uprawnień platformy Azure `Actions` w grupie zasobów sieci wirtualnej:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Tworzenie definicji roli platformy Azure

Poniższe przykłady tworzą definicję roli platformy Azure z akcji opisanych w poprzednich sekcjach. Przykłady są stosowane na poziomie grupy zasobów. Oceń i przetestuj, czy przykłady są wystarczająco szczegółowe dla wymagań. W scenariuszu może być konieczne przekazanie go do określonej galerii obrazów udostępnionych.

Akcje obrazu zezwalają na odczyt i zapis. Zdecyduj, co jest odpowiednie dla danego środowiska. Można na przykład utworzyć rolę umożliwiającą programowi Azure Image Builder odczytywanie obrazów z grupy zasobów *przykład-RG-1* i zapisanie obrazów do grupy zasobów *przykład-RG-2*.

### <a name="custom-image-azure-role-example"></a>Przykład niestandardowego obrazu na platformie Azure

Poniższy przykład tworzy rolę platformy Azure do użycia i dystrybucji obrazu niestandardowego. Następnie przydziel rolę niestandardową do tożsamości zarządzanej przypisanej przez użytkownika dla programu Azure Image Builder.

Aby uprościć zastępowanie wartości w przykładzie, najpierw należy ustawić następujące zmienne. Zastąp ustawienia symboli zastępczych, aby ustawić zmienne.

| Ustawienie | Opis |
|---------|-------------|
| \<Subscription ID\> | Identyfikator subskrypcji platformy Azure |
| \<Resource group\> | Grupa zasobów dla obrazu niestandardowego |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Przykład istniejącej roli sieci wirtualnej platformy Azure

Poniższy przykład tworzy rolę platformy Azure do użycia i dystrybucji istniejącego obrazu sieci wirtualnej. Następnie przydziel rolę niestandardową do tożsamości zarządzanej przypisanej przez użytkownika dla programu Azure Image Builder.

Aby uprościć zastępowanie wartości w przykładzie, najpierw należy ustawić następujące zmienne. Zastąp ustawienia symboli zastępczych, aby ustawić zmienne.

| Ustawienie | Opis |
|---------|-------------|
| \<Subscription ID\> | Identyfikator subskrypcji platformy Azure |
| \<Resource group\> | Grupa zasobów sieci wirtualnej |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Korzystanie z tożsamości zarządzanej na potrzeby dostępu do usługi Azure Storage

Jeśli chcesz bezproblemowe uwierzytelnianie przy użyciu usługi Azure Storage i używać prywatnych kontenerów, usługa Azure Image Builder wymaga tożsamości zarządzanej przez użytkownika. Usługa Azure Image Builder używa tożsamości do uwierzytelniania w usłudze Azure Storage.

> [!NOTE]
> Konstruktor obrazów platformy Azure używa tylko tożsamości w czasie przesłania szablonu obrazu. Maszyna wirtualna kompilacji nie ma dostępu do tożsamości podczas kompilowania obrazu.

Tworzenie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

W szablonie konstruktora obrazów należy podać tożsamość zarządzaną przypisaną przez użytkownika.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Zastąp następujące ustawienia symboli zastępczych:

| Ustawienie | Opis |
|---------|-------------|
| \<Region\> | Region szablonu |
| \<Resource group\> | Grupa zasobów |
| \<Storage account container\> | Nazwa kontenera konta magazynu |
| \<Subscription ID\> | Subskrypcja platformy Azure |

Aby uzyskać więcej informacji przy użyciu tożsamości zarządzanej przypisanej przez użytkownika, zobacz [Tworzenie niestandardowego obrazu, który będzie używać tożsamości zarządzanej User-Assigned platformy Azure do bezproblemowe dostępu do plików usługi Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage). Przewodnik Szybki Start przedstawia sposób tworzenia i konfigurowania tożsamości zarządzanej przypisanej przez użytkownika w celu uzyskania dostępu do konta magazynu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Image Builder](../image-builder-overview.md).