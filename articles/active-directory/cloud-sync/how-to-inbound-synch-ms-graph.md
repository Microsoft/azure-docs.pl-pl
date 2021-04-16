---
title: Jak programowo skonfigurować synchronizację chmury przy użyciu programu MS interfejs Graph API
description: W tym temacie opisano sposób włączania synchronizacji ruchu przychodzącego przy użyciu tylko interfejs Graph API
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
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530262"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Jak programowo skonfigurować synchronizację chmury przy użyciu programu MS interfejs Graph API

W poniższym dokumencie opisano sposób replikowania profilu synchronizacji od podstaw przy użyciu tylko interfejsów API usługi MSGraph.  
Struktura tego, jak to zrobić, składa się z następujących kroków.  Są to:

- [Jak programowo skonfigurować synchronizację chmury przy użyciu programu MS interfejs Graph API](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Konfiguracja podstawowa](#basic-setup)
    - [Włączanie flag dzierżawy](#enable-tenant-flags)
  - [Tworzenie jednostek usługi](#create-service-principals)
  - [Tworzenie zadania synchronizacji](#create-sync-job)
  - [Aktualizowanie domeny docelowej](#update-targeted-domain)
  - [Włączanie synchronizacji skrótów haseł w bloku konfiguracji](#enable-sync-password-hashes-on-configuration-blade)
  - [Przypadkowe usunięcie](#accidental-deletes)
    - [Włączanie i ustawianie progu](#enabling-and-setting-the-threshold)
    - [Zezwalanie na usuwanie](#allowing-deletes)
  - [Uruchamianie zadania synchronizacji](#start-sync-job)
  - [Stan przeglądu](#review-status)
  - [Następne kroki](#next-steps)

Użyj tych [Moduł Microsoft Azure Active Directory dla Windows PowerShell,](/powershell/module/msonline/) aby włączyć synchronizację dla dzierżawy produkcyjnej, która jest warunkiem wstępnym umożliwiającym wywołanie administracyjnej usługi sieci Web dla tej dzierżawy.

## <a name="basic-setup"></a>Konfiguracja podstawowa

### <a name="enable-tenant-flags"></a>Włączanie flag dzierżawy

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Pierwsze z tych dwóch poleceń wymaga Azure Active Directory poświadczeń. Te polecenia wiersza polecenia niejawnie identyfikują dzierżawę i umożliwiają jej synchronizację.

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
Następnie musimy utworzyć aplikację/jednostkę usługi [AD2AAD](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)

Musisz użyć tego identyfikatora aplikacji 1a4721b3-e57f-4451-ae87-ef078703ec94. DisplayName jest adres URL domeny usługi AD, jeśli jest używany w portalu (na przykład contoso.com), ale może mieć nazwę coś innego.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Tworzenie zadania synchronizacji
Dane wyjściowe powyższego polecenia zwracają identyfikator objectId utworzonej jednostki usługi. W tym przykładzie objectId to 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Użyj Microsoft Graph, aby dodać do tej jednostki usługi synchronizationJob.  

Dokumentację tworzenia zadania synchronizacji można znaleźć [tutaj.](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)

Jeśli nie zanotowałsz powyższego identyfikatora, możesz znaleźć jednostkę usługi, uruchamiając następujące wywołanie programu MS Graph. Aby wywołać to wywołanie, musisz mieć uprawnienia Directory.Read.All:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Następnie poszukaj nazwy aplikacji w danych wyjściowych.

Uruchom następujące dwa polecenia, aby utworzyć dwa zadania: jedno dla aprowzowania użytkowników/grup i jedno dla synchronizacji skrótów haseł. Jest to to samo żądanie dwa razy, ale z różnymi identyfikatorami szablonów.


Wywołaj następujące dwa żądania:

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

Jeśli chcesz utworzyć oba wywołania, potrzebujesz dwóch wywołań.

Przykładowa wartość zwracana (do aprowowania):

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

## <a name="update-targeted-domain"></a>Aktualizowanie domeny docelowej
W przypadku tej dzierżawy identyfikator obiektu i identyfikator aplikacji jednostki usługi są następujące:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-0000000000000 DisplayName: testApp

Będziemy musieli zaktualizować domenę, dla których ta konfiguracja jest ukierunkowana, więc zaktualizujemy wpisy tajne dla tej domeny.

Upewnij się, że nazwa domeny jest taka sama jak ustawiona dla kontrolera domeny w sieci

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Dodaj następującą parę klucz/wartość w poniższej tablicy wartości na podstawie tego, co próbujesz zrobić:
 - Włącz zarówno phs, jak i synchronizuj flagi dzierżawy { key: "AppKey", value: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Włącz tylko flagę dzierżawy synchronizacji (nie włączaj phs) { key: "AppKey", value: "{"appKeyScenario":"AD2AADProvisioning"}" }
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

Oczekiwana odpowiedź to ... HTTP 204/Brak zawartości

W tym miejscu wyróżniona wartość "Domena" jest nazwą domeny lokalna usługa Active Directory, z której mają być aprowowane wpisy Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Włączanie synchronizacji skrótów haseł w bloku konfiguracji

 Ta sekcja obejmuje włączanie synchronizacji skrótów haseł dla określonej konfiguracji. Różni się to od klucza tajnego AppKey, który włącza flagę funkcji na poziomie dzierżawy — dotyczy to tylko jednej domeny/konfiguracji. Aby wszystko działało, należy ustawić klucz aplikacji na klucz PHS.

1. Chwyć schemat (ostrzeżenie jest dość duże) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Weź to mapowanie atrybutów CredentialData:
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
3. Znajdź następujące mapowania obiektów o następujących nazwach w schemacie
 - Aprowizuj użytkowników usługi Active Directory
 - Aprowizuj inetOrgPersons usługi Active Directory

 Mapowania obiektów znajdują się w schema.synchronizationRules[0].objectMappings (na razie można założyć, że istnieje tylko 1 reguła synchronizacji)

4. Przejmij mapowanie credentialdata z kroku (2) i wstaw je do mapowań obiektów w kroku (3)

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
 Skopiuj/wklej mapowanie z kroku Tworzenie **zadań AD2AADProvisioning i AD2AADPasswordHash** powyżej do tablicy attributeMappings. 

 Kolejność elementów w tej tablicy nie ma znaczenia (zaplecza będą sortowane dla Ciebie). Należy zachować ostrożność podczas dodawania tego mapowania atrybutów, jeśli nazwa istnieje już w tablicy (np. jeśli istnieje już element w atrybutu attributeMappings, który ma targetAttributeName CredentialData) — mogą wystąpić błędy powodujące konflikt lub mogą wystąpić błędy powodujące konflikt i nowe mapowania mogą zostać połączone (zazwyczaj nie jest to pożądany wynik). Zaplecza nie deduują za Ciebie. 

 Pamiętaj, aby to zrobić zarówno dla użytkowników, jak i inetOrgpersons

5. Zapisywanie utworzonego schematu 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Dodaj schemat w treści żądania. 

## <a name="accidental-deletes"></a>Przypadkowe usunięcie
W tej sekcji opisano sposób programowego włączania/wyłączania oraz programowego używania [przypadkowego](how-to-accidental-deletes.md) usunięcia.


### <a name="enabling-and-setting-the-threshold"></a>Włączanie i ustawianie progu
Istnieją dwa ustawienia zadania, których można użyć:

 - DeleteThresholdEnabled — włącza zapobieganie przypadkowemu usuwaniu zadania w przypadku ustawienia wartości "true". Domyślnie ustawiono wartość "true".
 - DeleteThresholdValue — określa maksymalną liczbę usuwań, które będą dozwolone w każdym wykonaniu zadania po włączeniu zapobiegania przypadkowemu usuwaniu. Wartość jest domyślnie ustawiona na 500.  Dlatego jeśli wartość jest ustawiona na 500, maksymalna dozwolona liczba usuwań będzie 499 w każdym wykonaniu.

Ustawienia progu usuwania są częścią obiektu `SyncNotificationSettings` i można je modyfikować za pomocą grafu. 

Będziemy musieli zaktualizować ustawienia SyncNotificationSettings, których dotyczy ta konfiguracja, więc zaktualizujemy wpisy tajne.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Dodaj następującą parę klucz/wartość w poniższej tablicy wartości na podstawie tego, co próbujesz zrobić:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

Ustawienie "Włączone" w powyższym przykładzie dotyczy włączania/wyłączania wiadomości e-mail z powiadomieniami, gdy zadanie jest poddane kwarantannie.


Obecnie nie obsługujemy żądań PATCH dla wpisów tajnych, dlatego należy dodać wszystkie wartości w treści żądania PUT (jak w powyższym przykładzie), aby zachować inne wartości.

Istniejące wartości dla wszystkich wpisów tajnych można pobrać przy użyciu 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Zezwalanie na usuwanie
Aby zezwolić na przepływ usuwania po kwarantannie w ramach zadania, należy uruchomić ponownie tylko z zakresem "ForceDeletes". 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Uruchamianie zadania synchronizacji
Zadanie można pobrać ponownie za pomocą następującego polecenia:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentację pobierania zadań można znaleźć [tutaj.](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true) 
 
Aby uruchomić zadanie, wyślij to żądanie, używając identyfikatora objectId jednostki usługi utworzonej w pierwszym kroku oraz identyfikatora zadania zwróconego z żądania, które utworzyło zadanie.

Dokumentację uruchamiania zadania można znaleźć [tutaj.](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true) 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Oczekiwana odpowiedź to ... HTTP 204/Brak zawartości.

Inne polecenia służące do kontrolowania zadania są udokumentowane [tutaj.](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)
 
Aby ponownie uruchomić zadanie, należy użyć ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Stan przeglądu
Pobieranie stanów zadań za pośrednictwem ...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Sprawdź odpowiednie szczegóły w sekcji "status" obiektu return

## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect w chmurze?](what-is-cloud-sync.md)
- [Przekształcenia](how-to-transformation.md)
- [Interfejs API synchronizacji usługi Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
