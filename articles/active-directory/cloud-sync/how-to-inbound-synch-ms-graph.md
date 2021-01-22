---
title: Synchronizacja ruchu przychodzącego dla synchronizacji z chmurą przy użyciu usługi MS interfejs API programu Graph
description: W tym temacie opisano sposób włączania synchronizacji przychodzącej przy użyciu tylko interfejs API programu Graph
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3796b3d86f647e38cf2ff018e8c0c903d9a64e41
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682042"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>Synchronizacja ruchu przychodzącego dla synchronizacji z chmurą przy użyciu usługi MS interfejs API programu Graph

W poniższym dokumencie opisano sposób replikowania profilu synchronizacji od podstaw przy użyciu tylko interfejsów API MSGraph.  
Struktura, w jaki należy to zrobić, składa się z następujących kroków.  Są to:

- [Konfiguracja podstawowa](#basic-setup)
- [Tworzenie jednostek usługi](#create-service-principals)
- [Utwórz zadanie synchronizacji](#create-sync-job)
- [Aktualizowanie domeny dostosowanej](#update-targeted-domain)
- [Włącz skróty haseł synchronizacji](#enable-sync-password-hashes-on-configuration-blade)
- [Rozpocznij zadanie synchronizacji](#start-sync-job)
- [Przegląd stanu](#review-status)

Użyj tych poleceń [moduł Microsoft Azure Active Directory dla Windows PowerShell](/powershell/module/msonline/) , aby włączyć synchronizację dla dzierżawy produkcyjnej, wymaganie wstępne do wywołania administracyjnej usługi sieci Web dla tej dzierżawy.

## <a name="basic-setup"></a>Konfiguracja podstawowa

### <a name="enable-tenant-flags"></a>Włącz flagi dzierżawy

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Pierwszy z tych dwóch poleceń wymaga poświadczeń Azure Active Directory. Polecenia cmdlet te niejawnie identyfikują dzierżawę i umożliwiają jej synchronizację.

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
Następnie musimy utworzyć [aplikację AD2AAD/nazwę główną usługi](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Musisz użyć tego identyfikatora aplikacji 1a4721b3-e57f-4451-ae87-ef078703ec94. Nazwa wyświetlana to adres URL domeny usługi AD, jeśli jest używany w portalu (na przykład contoso.com), ale może mieć inną nazwę.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Utwórz zadanie synchronizacji
Dane wyjściowe powyższego polecenia zwracają identyfikator objectId jednostki usługi, która została utworzona. W tym przykładzie objectId jest 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Użyj Microsoft Graph, aby dodać synchronizationJob do tej nazwy głównej usługi.  

Dokumentację dotyczącą tworzenia zadania synchronizacji można znaleźć [tutaj](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta).

Jeśli nie zarejestrowano powyższego identyfikatora, można znaleźć jednostkę usługi, uruchamiając następujące wywołanie programu MS Graph. Musisz mieć katalog. odczyt. wszystkie uprawnienia, aby to wywołać:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Następnie poszukaj nazwy aplikacji w danych wyjściowych.

Uruchom następujące dwa polecenia, aby utworzyć dwa zadania: jeden dla aprowizacji użytkownika/grupy i jeden na potrzeby synchronizacji skrótów haseł. Jest to takie samo żądanie dwa razy, ale z różnymi identyfikatorami szablonów.


Wywołaj dwa następujące żądania:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Wymagane są dwa wywołania, jeśli chcesz utworzyć oba te elementy.

Przykładowa wartość zwrotna (dla aprowizacji):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Aktualizowanie domeny dostosowanej
W przypadku tej dzierżawy identyfikator obiektu i identyfikator aplikacji nazwy głównej usługi są następujące:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Będziemy musieli zaktualizować domenę, w której znajduje się ta konfiguracja, więc zaktualizuj wpisy tajne dla tej domeny.

Upewnij się, że nazwa domeny, której używasz, jest tym samym adresem URL ustawionym dla kontrolera domeny Premium

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Dodaj następującą parę klucz/wartość w tabeli poniżej wartości na podstawie tego, co próbujesz zrobić:
 - Włącz flagi dzierżawy PHS i Sync {Key: "AppKey", Value: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Włącz tylko flagę synchronizacji dzierżawy (nie włączaj PHS) {Key: "AppKey", Value: "{" appKeyScenario ":" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

Oczekiwana odpowiedź to... HTTP 204/Brak zawartości

W tym miejscu wyróżniona wartość "domena" jest nazwą lokalnej domeny Active Directory, z której mają być obsługiwane wpisy Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Włącz skróty synchronizacji haseł w bloku konfiguracji

 Ta sekcja będzie obejmować włączenie synchronizacji skrótów haseł dla określonej konfiguracji. Jest to inne niż AppKey tajny, który włącza flagę funkcji na poziomie dzierżawy — jest to tylko dla jednej domeny/konfiguracji. Aby to zrobić, należy ustawić klucz aplikacji na PHS.

1. Przechwyć schemat (Ostrzeżenie to jest dość duże) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Zrób to mapowanie atrybutów CredentialData:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Znajdź następujące mapowania obiektów z następującymi nazwami w schemacie
 - Udostępnianie użytkownikom Active Directory
 - Inicjowanie obsługi Active Directory inetOrgPersons

 Mapowania obiektów znajdują się w schemacie. synchronizationRules [0]. objectMappings (dla teraz można założyć, że istnieje tylko 1 reguła synchronizacji)

4. Przełącz mapowanie CredentialData z kroku (2) i Wstaw je do mapowań obiektów w kroku (3)

 Mapowanie obiektu wygląda następująco:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Skopiuj/wklej mapowanie z kroku **Utwórz AD2AADProvisioning i AD2AADPasswordHash zadania** powyżej do tablicy attributeMappings. 

 Kolejność elementów w tej tablicy nie ma znaczenia (zaplecze zostanie posortowane dla Ciebie). Należy zachować ostrożność przy dodawaniu tego mapowania atrybutów, jeśli nazwa istnieje już w tablicy (np. Jeśli istnieje już element w attributeMappings, który ma targetAttributeName CredentialData) — mogą wystąpić błędy konfliktów lub istniejące i nowe mapowania mogą być połączone razem (zazwyczaj nie jest to pożądane wyniki). Zaplecze nie powoduje deduplikowania. 

 Pamiętaj, aby to zrobić zarówno dla użytkowników, jak i dla obiektów inetOrgperson

5. Zapisz utworzony schemat 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Dodaj schemat w treści żądania. 

## <a name="start-sync-job"></a>Rozpocznij zadanie synchronizacji
Zadanie można pobrać ponownie za pomocą następującego polecenia:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentację dotyczącą pobierania zadań można znaleźć [tutaj](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta). 
 
Aby uruchomić zadanie, wydaj to żądanie, używając identyfikatora objectId jednostki usługi utworzonej w pierwszym kroku i identyfikatora zadania zwróconego przez żądanie, które utworzyło zadanie.

Dokumentację dotyczącą sposobu uruchamiania zadania można znaleźć [tutaj](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Oczekiwana odpowiedź to... HTTP 204/Brak zawartości.

Inne polecenia kontrolowania zadania są udokumentowane w [tym miejscu](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Aby ponownie uruchomić zadanie, jeden z nich użyje...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Przegląd stanu
Pobieranie stanów zadań za pośrednictwem...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Zapoznaj się z sekcją "status" obiektu Return, aby uzyskać odpowiednie szczegóły.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Przekształcenia](how-to-transformation.md)
- [Interfejs API synchronizacji usługi Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta)