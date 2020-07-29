---
title: Używanie Microsoft Graph interfejsów API do konfigurowania aprowizacji — Azure Active Directory | Microsoft Docs
description: Potrzebujesz skonfigurować Inicjowanie obsługi dla wielu wystąpień aplikacji? Dowiedz się, jak zaoszczędzić czas przy użyciu interfejsów API Microsoft Graph, aby zautomatyzować konfigurację automatycznej aprowizacji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 01d4475e73fd436fd0cd2a8aca1e7a946cdd7562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782062"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Konfigurowanie aprowizacji przy użyciu Microsoft Graph interfejsów API

Azure Portal to wygodny sposób konfigurowania aprowizacji pojedynczych aplikacji pojedynczo. Ale jeśli tworzysz kilka, a nawet setki — wystąpienia aplikacji, łatwiejsze może być Automatyzacja tworzenia i konfigurowania aplikacji przy użyciu Microsoft Graph interfejsów API. W tym artykule opisano sposób automatyzacji konfigurowania aprowizacji za poorednictwem interfejsów API. Ta metoda jest często używana dla aplikacji, takich jak [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**Przegląd kroków dotyczących używania Microsoft Graph interfejsów API do automatyzowania konfigurowania aprowizacji**


|Krok  |Szczegóły  |
|---------|---------|
|[Krok 1. Tworzenie aplikacji galerii](#step-1-create-the-gallery-application)     |Logowanie do klienta interfejsu API <br> Pobierz szablon aplikacji galerii <br> Tworzenie aplikacji galerii         |
|[Krok 2. Utwórz zadanie aprowizacji na podstawie szablonu](#step-2-create-the-provisioning-job-based-on-the-template)     |Pobieranie szablonu łącznika aprowizacji <br> Tworzenie zadania aprowizacji         |
|[Krok 3. Autoryzuj dostęp](#step-3-authorize-access)     |Testowanie połączenia z aplikacją <br> Zapisz poświadczenia         |
|[Krok 4. Uruchom zadanie aprowizacji](#step-4-start-the-provisioning-job)     |Uruchamianie zadania         |
|[Krok 5. Monitorowanie aprowizacji](#step-5-monitor-provisioning)     |Sprawdź stan zadania aprowizacji <br> Pobierz dzienniki aprowizacji         |

> [!NOTE]
> Obiekty odpowiedzi pokazane w tym artykule mogą zostać skrócone w celu zapewnienia czytelności. Wszystkie właściwości zostaną zwrócone z rzeczywistego wywołania.

## <a name="step-1-create-the-gallery-application"></a>Krok 1. Tworzenie aplikacji galerii

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Zaloguj się do Microsoft Graph Explorer (zalecane), programu Poster lub dowolnego innego klienta interfejsu API, którego używasz

1. Uruchom [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Wybierz przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego lub administratora aplikacji usługi Azure AD.

    ![Logowanie grafu](./media/application-provisioning-configure-api/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w okienku po lewej stronie.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Pobierz identyfikator szablonu aplikacji galerii
Aplikacje w galerii aplikacji usługi Azure AD zawierają [szablon aplikacji](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) opisujący metadane dla tej aplikacji. Za pomocą tego szablonu można utworzyć wystąpienie aplikacji i nazwy głównej usługi w dzierżawie na potrzeby zarządzania.

#### <a name="request"></a>*Żądanie*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Reakcji*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Tworzenie aplikacji galerii

Użyj identyfikatora szablonu pobranego dla aplikacji w ostatnim kroku, aby [utworzyć wystąpienie](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) aplikacji i nazwy głównej usługi w dzierżawie.

#### <a name="request"></a>*Żądanie*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Reakcji*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>Krok 2. Tworzenie zadania aprowizacji na podstawie szablonu

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Pobieranie szablonu łącznika aprowizacji

Aplikacje w galerii, w których włączono obsługę aprowizacji, mają szablony usprawniające konfigurację. Użyj poniższego żądania, aby [pobrać szablon konfiguracji aprowizacji](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http). Należy pamiętać, że należy podać identyfikator. IDENTYFIKATOR odwołuje się do poprzedniego zasobu, który w tym przypadku jest obiektem serviceprincipal. 

#### <a name="request"></a>*Żądanie*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Tworzenie zadania aprowizacji
Aby włączyć obsługę administracyjną, należy najpierw [utworzyć zadanie](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). Użyj poniższego żądania, aby utworzyć zadanie aprowizacji. Użyj templateId z poprzedniego kroku, aby określić szablon, który ma być używany dla tego zadania.

#### <a name="request"></a>*Żądanie*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>Krok 3. autoryzowanie dostępu

### <a name="test-the-connection-to-the-application"></a>Testowanie połączenia z aplikacją

Przetestuj połączenie z aplikacją innej firmy. Poniższy przykład dotyczy aplikacji wymagającej clientSecret i secretToken. Każda aplikacja ma swoje wymagania. Aplikacje często używają BaseAddress zamiast ClientSecret. Aby określić, jakie poświadczenia są wymagane przez aplikację, przejdź do strony Konfiguracja aprowizacji dla aplikacji i w trybie dewelopera kliknij pozycję Testuj połączenie. Ruch sieciowy będzie zawierać parametry używane do poświadczeń. Pełną listę poświadczeń można znaleźć [tutaj](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http). 

#### <a name="request"></a>*Żądanie*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Zapisz swoje poświadczenia

Konfigurowanie aprowizacji wymaga ustanowienia relacji zaufania między usługą Azure AD a aplikacją. Autoryzuj dostęp do aplikacji innych firm. Poniższy przykład dotyczy aplikacji wymagającej clientSecret i secretToken. Każda aplikacja ma swoje wymagania. Zapoznaj się z [dokumentacją interfejsu API](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) , aby wyświetlić dostępne opcje. 

#### <a name="request"></a>*Żądanie*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>Krok 4. uruchomienie zadania aprowizacji
Teraz, gdy zadanie aprowizacji jest skonfigurowane, użyj następującego polecenia, aby [uruchomić zadanie](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Żądanie*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>Krok 5. monitorowanie aprowizacji

### <a name="monitor-the-provisioning-job-status"></a>Monitorowanie stanu zadania aprowizacji

Teraz, gdy zadanie aprowizacji jest uruchomione, użyj następującego polecenia, aby śledzić postęp bieżącego cyklu aprowizacji, a także dane statystyczne, takie jak liczba użytkowników i grup utworzonych w systemie docelowym. 

#### <a name="request"></a>*Żądanie*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Monitorowanie zdarzeń aprowizacji przy użyciu dzienników aprowizacji
Oprócz monitorowania stanu zadania aprowizacji można użyć [dzienników aprowizacji](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) do wykonywania zapytań dotyczących wszystkich zdarzeń, które są wykonywane (np. zapytanie dla określonego użytkownika i określić, czy zostały one pomyślnie zainicjowane).

#### <a name="request"></a>*Żądanie*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Reakcji*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Pokrewne artykuły:

- [Zapoznaj się z dokumentacją Microsoft Graph synchronizacji](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrowanie niestandardowej aplikacji Standard scim z usługą Azure AD](use-scim-to-provision-users-and-groups.md)
