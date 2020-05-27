---
title: Przypisywanie ról administratorów usługi Azure AD za pomocą interfejsu API Microsoft Graph | Microsoft Docs
description: Przypisywanie i usuwanie ról administratorów usługi Azure AD za pomocą interfejs API programu Graph w programie Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ac6f06a936893f26c634951e50a3579401c15d5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869614"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Przypisywanie niestandardowych ról administratora przy użyciu interfejsu API Microsoft Graph w programie Azure Active Directory 

Można zautomatyzować sposób przypisywania ról do kont użytkowników przy użyciu interfejsu API Microsoft Graph. W tym artykule opisano operacje POST, GET i DELETE w witrynie roleAssignments.

## <a name="required-permissions"></a>Wymagane uprawnienia

Połącz się z organizacją usługi Azure AD przy użyciu konta administratora globalnego lub administratora tożsamości uprzywilejowanej, aby przypisać lub usunąć role.

## <a name="post-operations-on-roleassignment"></a>Operacje POST na RoleAssignment

Żądanie HTTP do utworzenia przypisania roli między użytkownikiem a definicją roli.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Treść

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Odpowiedź

``` HTTP
HTTP/1.1 201 Created
```

Żądanie HTTP do utworzenia przypisania roli, w którym podmiot zabezpieczeń lub definicja roli nie istnieje

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Odpowiedź

``` HTTP
HTTP/1.1 404 Not Found
```

Żądanie HTTP do utworzenia przypisania roli o pojedynczym zakresie zasobu dla wbudowanej definicji roli.

> [!NOTE] 
> Obecnie wbudowane role mają ograniczenie, w którym mogą być ograniczone do zakresu "/" całej organizacji lub zakresu "/AU/*". Określanie zakresu zasobów pojedynczego nie działa w przypadku ról wbudowanych, ale działa w przypadku ról niestandardowych.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Treść

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
}
```

Odpowiedź

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>Pobierz operacje na RoleAssignment

Żądanie HTTP, aby uzyskać przypisanie roli dla danego podmiotu zabezpieczeń

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

Żądanie HTTP, aby uzyskać przypisanie roli dla danej definicji roli.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

Żądanie HTTP, aby uzyskać przypisanie roli według identyfikatora.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
}
```

## <a name="delete-operations-on-roleassignment"></a>Operacje usuwania na RoleAssignment

Żądanie HTTP, aby usunąć przypisanie roli między użytkownikiem a definicją roli.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź
``` HTTP
HTTP/1.1 204 No Content
```

Żądanie HTTP, aby usunąć przypisanie roli, które już nie istnieje

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 404 Not Found
```

Żądanie HTTP, aby usunąć przypisanie roli między definicją roli samodzielnej i wbudowanej

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Odpowiedź

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
