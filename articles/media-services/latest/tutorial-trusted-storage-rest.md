---
title: Azure Media Services zaufanego magazynu
description: W ramach tego samouczka dowiesz się, jak włączyć zaufany magazyn dla Azure Media Services, korzystać z usług zarządzanej tożsamości dla zaufanego magazynu i nadawać usługi platformy Azure dostęp do konta magazynu w przypadku korzystania z zapory lub sieci VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590654"
---
# <a name="tutorial-media-services-trusted-storage"></a>Samouczek: Media Services zaufanego magazynu

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak włączyć zaufany magazyn dla Azure Media Services
> - Jak używać tożsamości zarządzanych dla zaufanego magazynu
> - Jak zapewnić usługom platformy Azure dostęp do konta magazynu w przypadku korzystania z kontroli dostępu do sieci, takiej jak zapora lub sieć VPN

Za pomocą interfejsu API 2020-05-01 można włączyć zaufany magazyn, kojarząc zarządzaną tożsamość z kontem Media Services.

>[!NOTE]
>Magazyn zaufany jest dostępny tylko w interfejsie API i nie jest obecnie włączony w Azure Portal.

Media Services może automatycznie uzyskiwać dostęp do konta magazynu przy użyciu uwierzytelniania systemu. Media Services sprawdza, czy konto Media Services i konto magazynu znajdują się w tej samej subskrypcji. Sprawdza również, czy użytkownik, który doda skojarzenie, ma dostęp do konta magazynu przy użyciu Azure Resource Manager RBAC.

Jeśli jednak chcesz użyć kontroli dostępu do sieci w celu zabezpieczenia konta magazynu i włączenia magazynu zaufanego, wymagane jest uwierzytelnianie [tożsamości zarządzanych](concept-managed-identities.md) . Umożliwia Media Services dostępu do konta magazynu, które zostało skonfigurowane przy użyciu zapory lub ograniczenia sieci wirtualnej za pomocą zaufanego dostępu do magazynu.

## <a name="overview"></a>Omówienie

> [!IMPORTANT]
> Użyj interfejsu API 2020-05-01 dla wszystkich żądań do Media Services.

Poniżej przedstawiono ogólne kroki tworzenia zaufanego magazynu dla Media Services:

1. Utwórz grupę zasobów.
1. Tworzenie konta magazynu
1. Sondowanie konta magazynu do momentu jego gotowości. Gdy konto magazynu jest gotowe, żądanie zwróci identyfikator jednostki usługi.
1. Znajdź identyfikator roli *współautor danych obiektu blob magazynu* .
1. Wywołaj dostawcę autoryzacji i Dodaj przypisanie roli.
1. Zaktualizuj konto usługi Media Services w celu uwierzytelnienia na koncie magazynu przy użyciu tożsamości zarządzanej.
1. Usuń zasoby, jeśli nie chcesz ich używać i obsłużyć ich opłaty.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz mieć subskrypcję platformy Azure.  Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Uzyskaj identyfikator dzierżawy i Identyfikator subskrypcji

Jeśli nie wiesz, jak uzyskać identyfikator dzierżawy i Identyfikator subskrypcji, zobacz [jak znaleźć identyfikator dzierżawy](how-to-set-azure-tenant.md) i [Znajdź swój identyfikator dzierżawy](how-to-set-azure-tenant.md).

### <a name="create-a-service-principal-and-secret"></a>Tworzenie nazwy głównej usługi i wpisu tajnego

Jeśli nie wiesz, jak utworzyć nazwę główną i klucz tajny usługi, zobacz [pobieranie poświadczeń w celu uzyskania dostępu Media Services interfejsie API](access-api-howto.md).

## <a name="use-a-rest-client"></a>Korzystanie z klienta REST

Ten skrypt jest przeznaczony do użytku z klientem REST, takim jak dostępność w rozszerzeniach programu Visual Studio Code.  Dostosuj ją do środowiska deweloperskiego.

## <a name="set-initial-variables"></a>Ustaw zmienne początkowe

Ta część skryptu jest używana w przypadku klienta REST. Zmienne mogą być używane inaczej w środowisku programistycznym.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Pobierz token dla Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Pobierz token dla interfejs API programu Graph

Ta część skryptu jest używana w przypadku klienta REST. Zmienne mogą być używane inaczej w środowisku programistycznym.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Pobierz szczegóły jednostki usługi

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Zapisz identyfikator jednostki usługi

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Pobierz stan konta magazynu

Jeśli to żądanie będzie sondowane do stanu, trwa przygotowywanie konta magazynu.  Powtórz to żądanie do momentu, aż konto magazynu będzie gotowe.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Pobierz szczegóły konta magazynu

Gdy konto magazynu jest gotowe, Pobierz właściwości konta magazynu.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Uzyskaj token dla usługi ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Tworzenie konta Media Services przy użyciu tożsamości zarządzanej przypisanej przez system

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Pobieranie definicji roli danych obiektów blob magazynu magazynu

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Ustaw przypisanie roli magazynu

Przypisanie roli wskazuje, że jednostka usługi dla konta Media Services ma *współautor danych obiektów blob magazynu* roli magazynu.  Może to chwilę potrwać i należy oczekiwać, a konto Media Services nie zostanie prawidłowo skonfigurowane.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Przyznaj dostęp do obejścia tożsamości zarządzanej do konta magazynu

Ta akcja powoduje zmianę dostępu z tożsamości zarządzanej przez system do tożsamości zarządzanej. W ten sposób konto magazynu będzie miało dostęp do konta magazynu przez zaporę, ponieważ usługi platformy Azure mogą uzyskiwać dostęp do konta magazynu niezależnie od zasad dostępu do adresów IP (ACL).

Ponownie Zaczekaj, aż rola została przypisana na koncie magazynu, lub konto Media Services zostanie niepoprawnie skonfigurowane.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Aktualizowanie konta Media Services, aby można było korzystać z tożsamości zarządzanej

Może być konieczne ponowne ponowienie tego żądania, ponieważ Propagacja roli magazynu może potrwać kilka minut.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Dostęp do testów

Dostęp do testów przez utworzenie zasobu na koncie magazynu.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Usuwanie zasobów

Jeśli nie chcesz przechowywać zasobów, które zostały utworzone i w dalszym ciągu mają być naliczone opłaty, usuń je.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć element zawartości](how-to-create-asset.md)
