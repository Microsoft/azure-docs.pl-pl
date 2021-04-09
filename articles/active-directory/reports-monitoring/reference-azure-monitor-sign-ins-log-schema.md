---
title: Schemat dziennika logowania w Azure Monitor | Microsoft Docs
description: Opisz logowanie do usługi Azure AD w schemacie rejestrowania do użycia w Azure Monitor
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
ms.date: 03/12/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: bad8ae86827144269e816a6c2e01d6af3f4d88ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225422"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Interpretuj schemat dzienników logowania usługi Azure AD w Azure Monitor

W tym artykule opisano schemat dziennika logowania Azure Active Directory (Azure AD) w programie Azure Monitor. Większość informacji związanych z logowaniem jest udostępniana w atrybucie *Właściwości* `records` obiektu.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Opisy pól

| Nazwa pola | Klucz | Opis |
| --- | --- | --- | 
| Godzina |  - | Data i godzina w formacie UTC. |
| ResourceId | - | Ta wartość jest niezamapowana i można bezpiecznie zignorować to pole.  |
| OperationName | - | W przypadku logowania ta wartość jest zawsze *aktywność logowania*. |
| OperationVersion | - | Wersja interfejsu API REST, która jest wymagana przez klienta programu. |
| Kategoria | - | W przypadku logowania ta wartość jest zawsze *rejestracja*. | 
| TenantId | - | Identyfikator GUID dzierżawy, który jest skojarzony z dziennikami. |
| ResultType | - | Wynik operacji logowania może być spowodowany *sukcesem* lub *niepowodzeniem*. | 
| ResultSignature | - | Zawiera kod błędu (jeśli istnieje) dla operacji logowania. |
| ResultDescription | Nie dotyczy lub puste | Zawiera opis błędu dla operacji logowania. |
| riskDetail | riskDetail | Zapewnia "powód" w odniesieniu do określonego stanu ryzykownego użytkownika, logowania lub wykrywania ryzyka. Możliwe wartości to: `none` ,,,, `adminGeneratedTemporaryPassword` ,,,, `userPerformedSecuredPasswordChange` `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe` `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy` `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised` , `unknownFutureValue` . Wartość `none` oznacza, że nie wykonano żadnych akcji na użytkowniku lub logowanie do tej pory. <br>**Uwaga:** Szczegóły tej właściwości wymagają licencji na Azure AD — wersja Premium P2. Inne licencje zwracają wartość `hidden` . |
| riskEventTypes | riskEventTypes | Typy wykrywania ryzyka skojarzone z logowaniem. Możliwe wartości to:,,,,,,,, `unlikelyTravel` `anonymizedIPAddress` `maliciousIPAddress` `unfamiliarFeatures` `malwareInfectedIPAddress` `suspiciousIPAddress` `leakedCredentials` `investigationsThreatIntelligence`  `generic` , i `unknownFutureValue` . |
| authProcessingDetails | Biblioteka uwierzytelniania aplikacji usługi Azure AD | Zawiera informacje o rodzinie, bibliotece i platformie w formacie: "Rodzina: Biblioteka ADAL: ADAL.JS 1.0.0 Platform: JS" |
| authProcessingDetails | IsCAEToken | Wartości mają wartość PRAWDA lub FAŁSZ |
| riskLevelAggregated | riskLevel | Zagregowany poziom ryzyka. Możliwe wartości to: `none` ,,,, `low` `medium` `high` `hidden` i `unknownFutureValue` . Wartość `hidden` oznacza, że użytkownik lub logowanie nie zostało włączone dla Azure AD Identity Protection. **Uwaga:** Szczegóły dotyczące tej właściwości są dostępne tylko dla klientów Azure AD — wersja Premium P2. Zostaną zwrócone wszyscy klienci `hidden` . |
| riskLevelDuringSignIn | riskLevel | Poziom ryzyka podczas logowania. Możliwe wartości to: `none` ,,,, `low` `medium` `high` `hidden` i `unknownFutureValue` . Wartość `hidden` oznacza, że użytkownik lub logowanie nie zostało włączone dla Azure AD Identity Protection. **Uwaga:** Szczegóły dotyczące tej właściwości są dostępne tylko dla klientów Azure AD — wersja Premium P2. Zostaną zwrócone wszyscy klienci `hidden` . |
| riskState | riskState | Zgłasza stan ryzykownego użytkownika, logowania lub wykrywania ryzyka. Możliwe wartości to:,,,,, `none` `confirmedSafe` `remediated` `dismissed` `atRisk` `confirmedCompromised` , `unknownFutureValue` . |
| DurationMs | - | Ta wartość jest niezamapowana i można bezpiecznie zignorować to pole. |
| CallerIpAddress | - | Adres IP klienta, który wykonał żądanie. | 
| CorrelationId | - | Opcjonalny identyfikator GUID, który jest przesyłany przez klienta. Ta wartość może pomóc skorelować operacje po stronie klienta przy użyciu operacji po stronie serwera i jest przydatna podczas śledzenia dzienników obejmujących usługi. |
| Tożsamość | - | Tożsamość z tokenu, która została przedstawiona podczas żądania. Może to być konto użytkownika, konto System lub nazwa główna usługi. |
| Poziom | - | Udostępnia typ komunikatu. W przypadku inspekcji zawsze jest to *informacja*. |
| Lokalizacja | - | Udostępnia lokalizację działania związanego z logowaniem. |
| Właściwości | - | Wyświetla wszystkie właściwości, które są skojarzone z logowaniem.|

## <a name="next-steps"></a>Następne kroki

* [Interpretuj schemat dzienników inspekcji w Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Przeczytaj więcej na temat dzienników platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md)