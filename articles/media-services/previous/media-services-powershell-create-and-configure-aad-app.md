---
title: Tworzenie aplikacji usługi Azure AD w celu uzyskania dostępu do interfejsu API Azure Media Services za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację Azure Active Directory (Azure AD) przy użyciu programu PowerShell i skonfigurować ją w celu uzyskania dostępu do interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d16c2bbb16a19e5cb22b2b2b0378880ec9aa48b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009661"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Tworzenie aplikacji usługi Azure AD do użycia z interfejsem API Azure Media Services przy użyciu programu PowerShell

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Dowiedz się, jak używać skryptu programu PowerShell do tworzenia aplikacji Azure Active Directory (Azure AD) i nazwy głównej usługi, aby uzyskać dostęp do zasobów Azure Media Services.  

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Zacznij od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services w Azure Portal](media-services-portal-create-account.md).

- Azure PowerShell. Aby uzyskać więcej informacji, zobacz [jak używać Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Tworzenie aplikacji usługi Azure AD przy użyciu programu PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Jak ręcznie skonfigurować aplikacje demona przy użyciu certyfikatów](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-portal-upload-files.md).
