---
title: Tworzenie aplikacji usługi Azure AD w celu uzyskania dostępu do interfejsu API Azure Media Services za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację Azure Active Directory (Azure AD) przy użyciu programu PowerShell i skonfigurować ją w celu uzyskania dostępu do interfejsu API Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "64680136"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Tworzenie aplikacji usługi Azure AD do użycia z interfejsem API Azure Media Services przy użyciu programu PowerShell

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Dowiedz się, jak używać skryptu programu PowerShell do tworzenia aplikacji Azure Active Directory (Azure AD) i nazwy głównej usługi, aby uzyskać dostęp do zasobów Azure Media Services.  

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz konta, Zacznij od [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services w Azure Portal](media-services-portal-create-account.md).

- Azure PowerShell. Aby uzyskać więcej informacji, zobacz [jak używać Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

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

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Zarządzanie Access Controlami opartymi na rolach za pomocą Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Jak ręcznie skonfigurować aplikacje demona przy użyciu certyfikatów](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-portal-upload-files.md).
