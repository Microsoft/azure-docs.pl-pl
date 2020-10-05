---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: deb2eb877743d533c5daeee8b6636edd62418fe0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89494311"
---
Najprostszym sposobem uwierzytelniania aplikacji opartej na chmurze jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz Uwierzytelnianie w Key Vault](/azure/key-vault/general/authentication) .

W tym przewodniku szybki start można jednak utworzyć aplikację klasyczną, która wymaga użycia nazwy głównej usługi i zasad kontroli dostępu. Nazwa główna usługi wymaga unikatowej nazwy w formacie "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

Utwórz nazwę główną usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac --skip-assignment -n "http://<my-unique-service-principal-name>" --sdk-auth
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
