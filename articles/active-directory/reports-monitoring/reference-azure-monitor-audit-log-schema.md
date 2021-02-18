---
title: Interpretuj schemat dziennika inspekcji Azure Active Directory w Azure Monitor | Microsoft Docs
description: Opisz schemat dziennika inspekcji usługi Azure AD do użycia w Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d32e0b939731d64f3ef2495ab606b5dc0780e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592284"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Interpretuj schemat dzienników inspekcji usługi Azure AD w Azure Monitor (wersja zapoznawcza)

W tym artykule opisano schemat dziennika inspekcji usługi Azure Active Directory (Azure AD) w programie Azure Monitor. Każdy pojedynczy wpis dziennika jest przechowywany jako tekst i sformatowany jako obiekt BLOB JSON, jak pokazano w poniższych dwóch przykładach: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Opisy pól i właściwości

| Nazwa pola | Opis |
|------------|-------------|
| time       | Data i godzina (UTC). |
| operationName | Nazwa operacji. |
| operationVersion | Wersja interfejsu API REST, która jest wymagana przez klienta programu. |
| category | Obecnie *Inspekcja* jest jedyną obsługiwaną wartością. |
| tenantId | Identyfikator GUID dzierżawy, który jest skojarzony z dziennikami. |
| resultType | Wyniki operacji. Wynik może być spowodowany *sukcesem* lub *niepowodzeniem*. |
| resultSignature |  To pole jest niezamapowane i można je bezpiecznie zignorować. | 
| resultDescription | Dodatkowy opis wyniku, o ile jest dostępny. | 
| durationMs |  To pole jest niezamapowane i można je bezpiecznie zignorować. |
| callerIpAddress | Adres IP klienta, który wykonał żądanie. | 
| correlationId | Opcjonalny identyfikator GUID, który jest przesyłany przez klienta. Może pomóc skorelować operacje po stronie klienta przy użyciu operacji po stronie serwera i jest przydatne podczas śledzenia dzienników obejmujących usługi. |
| identity | Tożsamość z tokenu, która została przedstawiona podczas żądania. Tożsamość może być kontem użytkownika, kontem systemowym lub jednostką usługi. |
| poziom | Typ komunikatu. W przypadku dzienników inspekcji poziom jest zawsze *informacyjny*. |
| location | Lokalizacja centrum danych. |
| properties | Wyświetla listę obsługiwanych właściwości, które są powiązane z dziennikiem inspekcji. Aby uzyskać więcej informacji, zobacz następną tabelę. | 

<br>

| Nazwa właściwości | Opis |
|---------------|-------------|
| AuditEventCategory | Typ zdarzenia inspekcji. Może to być *Zarządzanie użytkownikami*, *Zarządzanie aplikacjami* lub inny typ.|
| Typ tożsamości | Typ może być *aplikacją* lub *użytkownikiem*. |
| Typ operacji | Typ może być *dodawany*, *Aktualizowanie* i *usuwanie*. lub *inne*. |
| Typ zasobu docelowego | Określa typ zasobu docelowego, na którym wykonano operację. Typem może być *aplikacja*, *użytkownik*, *rola*, *zasady* | 
| Nazwa zasobu docelowego | Nazwa zasobu docelowego. Może to być nazwa aplikacji, nazwa roli, główna nazwa użytkownika lub nazwa główna usługi. |
| additionalTargets | Wyświetla wszystkie dodatkowe właściwości dla określonych operacji. Na przykład w przypadku operacji aktualizacji stare wartości i nowe wartości są wyświetlane w obszarze *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Następne kroki

* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Dzienniki diagnostyczne platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)