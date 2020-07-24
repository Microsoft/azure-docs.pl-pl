---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 07f856994b57e415ece063050b76dcd891698943
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013129"
---
Najprostszym sposobem uwierzytelniania aplikacji w języku Python opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz używanie Azure Key Vault tożsamości zarządzanej App Service](/azure/key-vault/general/managed-identity) . 

W tym przewodniku szybki start można jednak utworzyć aplikację klasyczną, która wymaga użycia nazwy głównej usługi i zasad kontroli dostępu. Nazwa główna usługi wymaga unikatowej nazwy w formacie "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

Utwórz nazwę główną usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

Ta operacja zwróci serię par klucz/wartość.

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```