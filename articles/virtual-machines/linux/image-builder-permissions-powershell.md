---
title: Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell
description: Konfigurowanie wymagań dotyczących usługi Azure VM Image Builder, w tym uprawnień i uprawnień przy użyciu programu PowerShell
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9f8793b6ea0ba454b66c525c2d53c1de2197d539
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440211"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Konfigurowanie uprawnień usługi Azure Image Builder przy użyciu programu PowerShell

Gdy zarejestrujesz się w usłudze (AIB), spowoduje to przyznanie usługi AIB uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów (IT_ *) oraz do dodawania do niej zasobów, które są wymagane dla kompilacji obrazu. Jest to realizowane przez główną nazwę usługi AIB (SPN), która jest dostępna w Twojej subskrypcji podczas pomyślnej rejestracji.

Aby umożliwić programowi Azure VM Image Builder dystrybuowanie obrazów do obrazów zarządzanych lub do galerii obrazów udostępnionych, należy utworzyć tożsamość przypisaną przez użytkownika platformy Azure, która ma uprawnienia do odczytu i zapisu obrazów. Jeśli uzyskujesz dostęp do usługi Azure Storage, będzie to wymagało uprawnień do odczytywania kontenerów prywatnych i publicznych.

Przed utworzeniem obrazu należy skonfigurować uprawnienia i uprawnienia. W poniższych sekcjach szczegółowo opisano sposób konfigurowania możliwych scenariuszy przy użyciu programu PowerShell.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Rejestrowanie funkcji

Najpierw musisz zarejestrować się w usłudze Azure Image Builder. Rejestracja przyznaje usłudze uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów oraz zarządzania nią. Usługa ma również uprawnienia do dodawania zasobów do grupy, która jest wymagana dla kompilacji obrazu.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika platformy Azure

Konstruktor obrazów platformy Azure wymaga utworzenia [tożsamości zarządzanej przypisanej przez użytkownika na platformie Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Konstruktor obrazów platformy Azure używa tożsamości zarządzanej przypisanej przez użytkownika do odczytywania obrazów, zapisywania obrazów i uzyskiwania dostępu do kont usługi Azure Storage. Przyznasz tożsamości uprawnienia do wykonywania określonych akcji w ramach subskrypcji.

> [!NOTE]
> Wcześniej Konstruktor obrazów platformy Azure używał głównej nazwy usługi Azure Image Builder (SPN) do przyznawania uprawnień do grup zasobów obrazu. Korzystanie z nazwy SPN będzie przestarzałe. Zamiast tego użyj tożsamości zarządzanej przypisanej przez użytkownika.

W poniższym przykładzie przedstawiono sposób tworzenia tożsamości zarządzanej przypisanej przez użytkownika na platformie Azure. Zastąp ustawienia symboli zastępczych, aby ustawić zmienne.

| Ustawienie | Opis |
|---------|-------------|
| \<Resource group\> | Grupa zasobów, w której ma zostać utworzona tożsamość zarządzana przypisana przez użytkownika. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
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

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Przykład istniejącej roli sieci wirtualnej platformy Azure

Poniższy przykład tworzy rolę platformy Azure do użycia i dystrybucji istniejącego obrazu sieci wirtualnej. Następnie przydziel rolę niestandardową do tożsamości zarządzanej przypisanej przez użytkownika dla programu Azure Image Builder.

Aby uprościć zastępowanie wartości w przykładzie, najpierw należy ustawić następujące zmienne. Zastąp ustawienia symboli zastępczych, aby ustawić zmienne.

| Ustawienie | Opis |
|---------|-------------|
| \<Subscription ID\> | Identyfikator subskrypcji platformy Azure |
| \<Resource group\> | Grupa zasobów sieci wirtualnej |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Image Builder](../image-builder-overview.md).
