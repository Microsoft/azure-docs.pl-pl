---
title: Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć aplikację usługi Azure AD i skonfigurować ją pod kątem dostępu do interfejsu API Azure Media Services | Microsoft Docs
description: W tym temacie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć aplikację usługi Azure AD i skonfigurować ją w celu uzyskania dostępu do Azure Media Services API.
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
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: ceb01c97ec50f4e2a7aa146448c3da662d4314ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89258354"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure w celu utworzenia aplikacji usługi Azure AD i skonfigurowania jej w celu uzyskania dostępu do interfejsu API Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

W tym temacie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć aplikację Azure Active Directory (Azure AD) i nazwę główną usługi, aby uzyskać dostęp do zasobów Azure Media Services. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Użyj Azure Cloud Shell

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Uruchom Cloud Shell w górnym okienku nawigacji portalu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Aby uzyskać więcej informacji, zobacz [omówienie Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Tworzenie aplikacji usługi Azure AD i Konfigurowanie dostępu do konta multimediów przy użyciu interfejsu wiersza polecenia platformy Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Na przykład:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

W tym przykładzie **zakres** jest pełną ścieżką zasobu dla konta usługi Media Services. Jednak **zakres** może być na dowolnym poziomie.

Na przykład może to być jeden z następujących poziomów:
 
* Poziom **subskrypcji** .
* Poziom **grupy zasobów** .
* Poziom **zasobu** (na przykład konto multimediów).

Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

Zobacz też temat [zarządzanie Role-Based Access Control za pomocą interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-portal-upload-files.md).
