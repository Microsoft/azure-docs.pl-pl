---
title: Tworzenie przypisań ról i zarządzanie nimi — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się więcej na temat tworzenia przypisań ról i zarządzania nimi w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77110409"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Tworzenie przypisań ról i zarządzanie nimi w usłudze Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji używa kontroli dostępu opartej na rolach ([RBAC](./security-role-based-access-control.md)) do zarządzania dostępem do zasobów.

## <a name="role-assignments-overview"></a>Omówienie przypisań ról

Każde przypisanie roli jest zgodne z następującą definicją:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

W poniższej tabeli opisano każdy atrybut:

| Atrybut | Nazwa | Wymagany | Typ | Opis |
| --- | --- | --- | --- | --- |
| roleId | Identyfikator definicji roli | Tak | String | Unikatowy identyfikator żądanego przypisania roli. Znajdź definicje ról i ich identyfikatory, wykonując zapytania dotyczące systemowego interfejsu API lub tabeli przeglądu poniżej. |
| Obiektu | Identyfikator obiektu | Tak | String | Identyfikator Azure Active Directory, identyfikator obiektu jednostki usługi lub nazwa domeny. Do czego jest przypisane przypisanie roli. Przypisanie roli musi być sformatowane zgodnie ze skojarzonym z nim typem. Dla obiektu `DomainName` objectidtype identyfikator objectid musi rozpoczynać się `“@”` od znaku. |
| objectIdtype | Typ identyfikatora obiektu | Tak | String | Rodzaj używanego identyfikatora obiektu. Zobacz sekcję **obsługiwane ObjectIdTypes** poniżej. |
| ścieżka | Ścieżka miejsca | Tak | String | Pełna ścieżka dostępu do `Space` obiektu. Może to być na przykład `/{Guid}/{Guid}`. Jeśli identyfikator wymaga przypisania roli dla całego wykresu, określ `"/"`. Ten znak określa katalog główny, ale jego użycie nie jest zalecane. Zawsze stosuj zasadę najniższych uprawnień. |
| tenantId | Identyfikator dzierżawy | Różna | String | W większości przypadków Azure Active Directory identyfikator dzierżawy. Niedozwolone dla `DeviceId` i `TenantId` ObjectIdTypes. Wymagane dla `UserId` i `ServicePrincipalId` ObjectIdTypes. Opcjonalne dla nazwa_domeny. |

### <a name="supported-role-definition-identifiers"></a>Obsługiwane Identyfikatory definicji ról

Każde przypisanie roli kojarzy definicję roli z jednostką w środowisku Digital bliźniaczych reprezentacji systemu Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Obsługiwane typy identyfikatorów obiektów

Wcześniej wprowadzono atrybut **objectidtype** .

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operacje przypisywania ról

Usługa Azure Digital bliźniaczych reprezentacji obsługuje pełne operacje *tworzenia*, *odczytywania*i *usuwania* dla przypisań ról. Operacje *aktualizacji* są obsługiwane przez dodawanie przypisań ról, usuwanie przypisań ról lub modyfikowanie węzłów [wykresu analizy przestrzennej](./concepts-objectmodel-spatialgraph.md) , do których przypisań ról daje dostęp.

[![Punkty końcowe przypisania roli](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Podana dokumentacja referencyjna struktury Swagger zawiera dodatkowe informacje na temat wszystkich dostępnych punktów końcowych interfejsu API, operacji żądań i definicji.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Przyznawanie uprawnień do nazwy głównej usługi

Przyznanie uprawnień do nazwy głównej usługi jest często jednym z pierwszych kroków, które należy wykonać podczas pracy z usługą Azure Digital bliźniaczych reprezentacji. Wiąże się to z:

1. Logowanie do wystąpienia platformy Azure za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub [programu PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Uzyskiwanie informacji o jednostce usługi.
1. Przypisywanie żądanej roli do nazwy głównej usługi.

Identyfikator aplikacji jest dostarczany w Azure Active Directory. Aby dowiedzieć się więcej o konfigurowaniu i aprowizacji usługi Azure Digital bliźniaczych reprezentacji w Active Directory, zapoznaj się z [przewodnikiem Szybki Start](./quickstart-view-occupancy-dotnet.md).

Po utworzeniu identyfikatora aplikacji wykonaj jedno z następujących poleceń. W interfejsie wiersza polecenia platformy Azure:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

W programie PowerShell:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Użytkownik z rolą **administratora** może następnie przypisać do użytkownika rolę administratora miejsca, wykonując UWIERZYTELNIONE żądanie HTTP post na adres URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Z następującą treścią JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Pobierz wszystkie role

[![Role systemu](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Aby wyświetlić listę wszystkich dostępnych ról (definicje ról), wykonaj uwierzytelnione żądanie HTTP GET:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Pomyślne żądanie zwróci tablicę JSON z wpisami dla każdej roli, która może być przypisana:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>Sprawdzanie przypisania określonego roli

Aby sprawdzić konkretne przypisanie roli, wykonaj uwierzytelnione żądanie HTTP GET:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Wartość parametru** | **Wymagane** |  **Typ** |  **Opis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  Prawda | String |   Identyfikator obiektu dla identyfikatora obiektu UserIdtype. |
| YOUR_PATH | Prawda | String |   Wybrana ścieżka do sprawdzenia dostępu. |
| YOUR_ACCESS_TYPE |  Prawda | String |   *Odczytaj*, *Utwórz*, *zaktualizuj*lub *Usuń* |
| YOUR_RESOURCE_TYPE | Prawda | String |  *Device*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *rozszerzonytype*, *punkt końcowy*, *Magazyn*kluczy, *dopasowanie*, *Ontology*, *raport*, *definicji*, *czujnik*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *system*, *UerDefinedFunction*, *użytkownik*, *UserBlobMetadata*lub *UserExtendedProperty* |

Pomyślne żądanie zwróci wartość logiczną `true` lub `false` wskazuje, czy typ dostępu został przypisany do użytkownika dla danej ścieżki i zasobu.

### <a name="get-role-assignments-by-path"></a>Pobieranie przypisań ról według ścieżki

Aby uzyskać wszystkie przypisania ról dla ścieżki, wykonaj uwierzytelnione żądanie HTTP GET:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_PATH | Pełna ścieżka do miejsca |

Pomyślne żądanie zwróci tablicę JSON z każdym przypisaniem roli skojarzonym z wybranym parametrem **ścieżki** :

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Odwołaj uprawnienie

Aby odwołać uprawnienie od adresata, usuń przypisanie roli, wykonując uwierzytelnione żądanie HTTP DELETE:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Identyfikator** przypisania roli do usunięcia |

Pomyślne żądanie usunięcia zwróci 204 stan odpowiedzi. Sprawdź, czy usunięto przypisanie roli, [sprawdzając](#check-a-specific-role-assignment) , czy przypisanie roli nadal jest przechowywane.

### <a name="create-a-role-assignment"></a>Utwórz przypisanie roli

Aby utworzyć przypisanie roli, wykonaj uwierzytelnione żądanie HTTP POST na adres URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Sprawdź, czy treść JSON jest zgodna z następującym schematem:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Pomyślne żądanie zwróci 201 stan odpowiedzi wraz z **identyfikatorem** nowo utworzonego przypisania roli:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Przykłady konfiguracji

W poniższych przykładach pokazano, jak skonfigurować treść JSON w kilka często spotykanych scenariuszy przypisywania ról.

* **Przykład**: użytkownik musi mieć dostęp administracyjny do piętra przestrzeni dzierżawy.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Przykład**: aplikacja uruchamia scenariusze testów, które mają na celu imitację urządzeń i czujników.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Przykład**: Wszyscy użytkownicy, którzy są częścią domeny, otrzymują dostęp do odczytu dla spacji, czujników i użytkowników. Ten dostęp obejmuje odpowiednie powiązane obiekty.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z kontrolą dostępu do usługi Azure Digital bliźniaczych reprezentacji opartej na rolach, Odczytaj [role-Base-Access-Control](./security-authenticating-apis.md).

- Aby dowiedzieć się więcej o uwierzytelnianiu interfejsu API Digital bliźniaczych reprezentacji na platformie Azure, przeczytaj temat [uwierzytelnianie interfejsu API](./security-authenticating-apis.md).
