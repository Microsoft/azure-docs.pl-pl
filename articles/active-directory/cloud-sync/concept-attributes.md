---
title: Informacje o schemacie i wyrażeniach niestandardowych usługi Azure AD
description: W tym artykule opisano schemat usługi Azure AD, atrybuty obsługiwane przez agenta aprowizacji i wyrażenia niestandardowe.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613903"
---
# <a name="understand-the-azure-ad-schema"></a>Informacje o schemacie usługi Azure AD
Obiekt w Azure Active Directory (Azure AD), podobnie jak każdy katalog, to programowa konstrukcja danych wysokiego poziomu, która reprezentuje takie elementy jak użytkownicy, grupy i kontakty. Gdy tworzysz nowego użytkownika lub kontakt w usłudze Azure AD, tworzysz nowe wystąpienie tego obiektu. Te wystąpienia można rozróżnić w zależności od ich właściwości.

Właściwości w usłudze Azure AD to elementy odpowiedzialne za przechowywanie informacji o wystąpieniu obiektu w usłudze Azure AD.

Schemat usługi Azure AD definiuje reguły, dla których właściwości mogą być używane w danym wpisie, rodzaje wartości, które mogą mieć te właściwości, oraz sposób, w jaki użytkownicy mogą współdziałać z tymi wartościami. 

Usługa Azure AD ma dwa typy właściwości:
- **Wbudowane właściwości**: właściwości, które są wstępnie zdefiniowane przez schemat usługi Azure AD. Te właściwości zapewniają różne zastosowania i mogą być niedostępne.
- **Rozszerzenia katalogu**: właściwości, które są udostępniane, aby można było dostosować usługę Azure AD do własnych potrzeb. Na przykład jeśli rozszerzono Active Directory lokalne z określonym atrybutem i chcesz przepływać ten atrybut, możesz użyć jednej z podanych właściwości niestandardowych. 

## <a name="attributes-and-expressions"></a>Atrybuty i wyrażenia
Gdy obiekt taki jak użytkownik jest zainicjowany do usługi Azure AD, zostanie utworzone nowe wystąpienie obiektu użytkownika. To tworzenie obejmuje właściwości tego obiektu, które są również znane jako atrybuty. Początkowo nowo utworzony obiekt ma atrybuty ustawione na wartości, które są określone przez reguły synchronizacji. Te atrybuty są następnie aktualne za pośrednictwem agenta aprowizacji w chmurze.

![Inicjowanie obsługi obiektów](media/concept-attributes/attribute-1.png)

Na przykład użytkownik może być częścią działu marketingu. Atrybut działu usługi Azure AD jest początkowo tworzony, gdy są one inicjowane, a wartość jest ustawiana na marketing. Sześć miesięcy później, jeśli zmienią się na sprzedaż, atrybut działu Active Directory lokalnego został zmieniony na sprzedaż. Ta zmiana jest synchronizowana z usługą Azure AD i jest odzwierciedlana w obiekcie użytkownika usługi Azure AD.

Synchronizacja atrybutów może być bezpośrednia, gdzie wartość w usłudze Azure AD jest ustawiona bezpośrednio na wartość atrybutu lokalnego. Lub wyrażenie programistyczne może obsłużyć synchronizację. Wyrażenie programowe jest wymagane w przypadkach, gdy konieczne jest wykonanie pewnych logiki lub określenia w celu wypełnienia wartości.

Na przykład, jeśli masz atrybut poczty " john.smith@contoso.com " i jest wymagany do oddzielenia części " @contoso.com " i przeniesieniu tylko wartości "Jan. Smith", używasz podobnej do tego:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Przykładowe dane wejściowe/wyjściowe:** <br>

* **Dane wejściowe** (poczta): " john.smith@contoso.com "
* **Dane wyjściowe**: "Jan. Kowalski"

Aby uzyskać więcej informacji na temat pisania wyrażeń niestandardowych i składni, zobacz [Pisanie wyrażeń pod kątem mapowań atrybutów w Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).

W poniższej tabeli wymieniono typowe atrybuty i sposób ich synchronizacji z usługą Azure AD.


|Lokalna usługa Active Directory|Typ mapowania|Azure AD|
|-----|-----|-----|
|CN|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Wyrażenie|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>Wyświetl schemat
> [!WARNING]
> Konfiguracja synchronizacji z chmurą tworzy jednostkę usługi. Nazwa główna usługi jest widoczna w Azure Portal. Nie należy modyfikować mapowań atrybutów przy użyciu funkcji głównej jednostki usługi w Azure Portal.  To nie jest obsługiwane.

Aby wyświetlić schemat i sprawdzić go, wykonaj następujące kroki.

1.  Przejdź do [Eksploratora grafów](https://developer.microsoft.com/graph/graph-explorer).
1.  Zaloguj się przy użyciu konta administratora globalnego.
1.  Po lewej stronie wybierz pozycję **Modyfikuj uprawnienia** i upewnij się, że jest *wyrażana zgoda* na **katalog. ReadWrite. All** .
1.  Uruchom zapytanie `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . To zapytanie zwraca przefiltrowaną listę jednostek usługi.
1.  Znajdź `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` i zanotuj wartość parametru `"id"` .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Zamień `{Service Principal id}` na wartość i uruchom zapytanie `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. Znajdź `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` i zanotuj wartość parametru `"id"` .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Teraz uruchom zapytanie `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Przykład: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Zastąp `{Service Principal Id}` `{AD2ADD Provisioning Id}` wartości i wartościami.

1. To zapytanie zwraca schemat.

   ![Zwrócony schemat](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>Następne kroki

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)