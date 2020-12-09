---
title: Synchronizacja ruchu przychodzącego dla aprowizacji chmury przy użyciu usługi MS interfejs API programu Graph
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
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860359"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Synchronizacja ruchu przychodzącego dla aprowizacji chmury przy użyciu usługi MS interfejs API programu Graph

W poniższym dokumencie opisano sposób replikowania profilu synchronizacji od podstaw przy użyciu tylko interfejsów API MSGraph.  
Struktura, w jaki należy to zrobić, składa się z następujących kroków.  Są to:

- [Konfiguracja podstawowa](#basic-setup)
- [Tworzenie jednostek usługi](#create-service-principals)
- [Utwórz zadanie synchronizacji](#create-sync-job)
- [Aktualizowanie domeny dostosowanej](#update-targeted-domain)
- [Rozpocznij zadanie synchronizacji](#start-sync-job)
- [Przegląd stanu](#review-status)

Użyj tych poleceń [moduł Microsoft Azure Active Directory dla Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) , aby włączyć synchronizację dla dzierżawy produkcyjnej, wymaganie wstępne do wywołania administracyjnej usługi sieci Web dla tej dzierżawy.

## <a name="basic-setup"></a>Konfiguracja podstawowa

### <a name="enable-tenant-flags"></a>Włącz flagi dzierżawy

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Pierwszy z tych dwóch poleceń wymaga poświadczeń Azure Active Directory. Polecenia cmdlet te niejawnie identyfikują dzierżawę i umożliwiają jej synchronizację.

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
Następnie musimy utworzyć [aplikację AD2AAD/nazwę główną usługi](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

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

Dokumentację dotyczącą tworzenia zadania synchronizacji można znaleźć [tutaj](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

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
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

Oczekiwana odpowiedź to... HTTP 204/Brak zawartości

W tym miejscu wyróżniona wartość "domena" jest nazwą lokalnej domeny Active Directory, z której mają być obsługiwane wpisy Azure Active Directory.

## <a name="start-sync-job"></a>Rozpocznij zadanie synchronizacji
Zadanie można pobrać ponownie za pomocą następującego polecenia:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Dokumentację dotyczącą pobierania zadań można znaleźć [tutaj](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Aby uruchomić zadanie, wydaj to żądanie, używając identyfikatora objectId jednostki usługi utworzonej w pierwszym kroku i identyfikatora zadania zwróconego przez żądanie, które utworzyło zadanie.

Dokumentację dotyczącą sposobu uruchamiania zadania można znaleźć [tutaj](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Oczekiwana odpowiedź to... HTTP 204/Brak zawartości.

Inne polecenia kontrolowania zadania są udokumentowane w [tym miejscu](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
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

- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
- [Przekształcenia](how-to-transformation.md)
- [Interfejs API synchronizacji usługi Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
