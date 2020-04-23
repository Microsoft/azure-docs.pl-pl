---
title: Korzystanie z narzędzia SCIM, programu Microsoft Graph i usługi inicjowania obsługi administracyjnej usługi Azure AD w celu aprowizowania użytkowników i wzbogacania aplikacji o potrzebne dane | Dokumenty firmy Microsoft
description: Korzystanie z scim i microsoft graph razem aprowizować użytkowników i wzbogacić aplikację o dane, których potrzebuje .
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087623"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Używanie scim i Microsoft Graph razem do aprowizowania użytkowników i wzbogacenia aplikacji o potrzebne dane

**Grupa docelowa:** Ten dokument jest przeznaczony dla deweloperów tworzenia aplikacji zintegrowanych z usługą Azure AD. Dla innych, którzy chcą zintegrować istniejącą aplikację, takich jak Zoom, ServiceNow i DropBox można pominąć to i przejrzeć aplikacje specyficzne [tutoriale](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 

**Typowe scenariusze**

> [!div class="checklist"]
> * Automatyczne tworzenie użytkowników w mojej aplikacji
> * Automatyczne usuwanie użytkowników z mojej aplikacji, gdy nie powinni już mieć dostępu
> * Integracja aplikacji z wieloma dostawcami tożsamości w celu inicjowania obsługi administracyjnej
> * Wzbogać moją aplikację o dane z usług firmy Microsoft, takich jak Sharepoint, Outlook i Office.
> * Automatyczne tworzenie, aktualizowanie i usuwanie użytkowników i grup w usłudze Azure AD i usłudze Active Directory

![Scim Graph drzewo decyzyjne](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenariusz 1: Automatyczne tworzenie użytkowników w mojej aplikacji
Obecnie administratorzy IT ręcznie tworzą konta użytkowników w mojej aplikacji za każdym razem, gdy ktoś potrzebuje dostępu lub okresowo przesyła pliki CSV. Proces ten jest czasochłonny dla klientów i spowalnia przyjęcie mojej aplikacji. Wszystko, czego potrzebuję, to podstawowe informacje o [użytkowniku,](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) takie jak nazwa, adres e-mail i userPrincipalName do utworzenia użytkownika. Ponadto moi klienci używają różnych identyfikatorów i nie mam zasobów do obsługi aparatu synchronizacji i niestandardowych integracji z każdym IdP. 

**Zalecenie:** Obsługa punktu końcowego zgodnego ze standardem SCIM [/Users.](https://aka.ms/scimreferencecode) Klienci będą mogli łatwo korzystać z tego punktu końcowego do integracji z usługą inicjowania obsługi administracyjnej usługi Azure AD i automatycznego tworzenia kont użytkowników, gdy potrzebują dostępu. Punkt końcowy można utworzyć raz i będzie zgodny ze wszystkimi identyfikatorami, bez konieczności utrzymywania aparatu synchronizacji. Zapoznaj się z poniższym przykładowym żądaniem, jak użytkownik zostanie utworzony.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenariusz 2: Automatyczne usuwanie użytkowników z mojej aplikacji
Klienci korzystający z mojej aplikacji są skoncentrowani na zabezpieczeniach i mają wymagania dotyczące nadzoru, aby usunąć konta, gdy pracownicy nie potrzebują ich już. Jak zautomatyzować anulowanie obsługi administracyjnej z mojej aplikacji?

**Zalecenie:** Obsługa punktu końcowego zgodnego ze standardem SCIM /Users. Usługa inicjowania obsługi administracyjnej usługi Azure AD będzie wysyłać żądania, aby wyłączyć i usunąć, gdy użytkownik nie powinien mieć już dostępu. Zalecamy obsługę zarówno wyłączania, jak i usuwania użytkowników. Zobacz poniższe przykłady, jak wygląda żądanie wyłączenia i usunięcia. 

Wyłącz użytkownika
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Usuwanie użytkownika
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenariusz 3: Automatyzacja zarządzania członkostwem w grupach w mojej aplikacji
Moja aplikacja opiera się na grupach dostępu do różnych zasobów, a klienci chcą ponownie użyć grup, które mają w usłudze Azure AD. Jak mogę importować grupy z usługi Azure AD i aktualizować je w miarę zmiany członkostwa?  

**Zalecenie:** Obsługa punktu [końcowego](https://aka.ms/scimreferencecode)zgodnego ze standardem SCIM /Groups . Usługa inicjowania obsługi administracyjnej usługi Azure AD zajmie się tworzeniem grup i zarządzaniem aktualizacjami członkostwa w aplikacji. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenariusz 4: Wzbogać moją aplikację o dane z usług firmy Microsoft, takich jak zespoły, outlook i onedrive.
Moja aplikacja jest wbudowana w usługę Microsoft Teams i opiera się na danych wiadomości. Ponadto przechowujemy pliki dla użytkowników w usłudze OneDrive. Jak mogę wzbogacić aplikację o dane z tych usług i całej firmy Microsoft?

**Zalecenie:** [Microsoft Graph](https://docs.microsoft.com/graph/) jest punktem wejścia, aby uzyskać dostęp do danych firmy Microsoft. Każde obciążenie udostępnia interfejsy API z danymi, które są potrzebne. Wykres firmy Microsoft może służyć wraz z [obsługi administracyjnej scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) dla scenariuszy powyżej. Za pomocą scim do inicjowania obsługi administracyjnej podstawowych atrybutów użytkownika w aplikacji podczas wywoływania do wykresu, aby uzyskać inne dane, które są potrzebne. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenariusz 5: Śledzenie zmian w usługach firmy Microsoft, takich jak teams, Outlook i Azure AD.
Muszę być w stanie śledzić zmiany w wiadomościach teams i outlook i reagować na nie w czasie rzeczywistym. Jak mogę uzyskać te zmiany wypchnięte do mojej aplikacji?

**Zalecenie:** Program Microsoft Graph udostępnia [powiadomienia o zmianach](https://docs.microsoft.com/graph/webhooks) i śledzenie zmian dla różnych zasobów. Zwróć uwagę na następujące ograniczenia powiadomień o zmianach:
- Jeśli odbiorca zdarzenia potwierdzi zdarzenie, ale z jakiegokolwiek powodu nie podejmie działań, zdarzenie może zostać utracone
- Jeśli odbiorca zdarzenia potwierdzi zdarzenie, ale z jakiegokolwiek powodu nie podejmie działań, zdarzenie może zostać utracone
- Powiadomienia o zmianach nie zawsze zawierają [dane zasobów](https://docs.microsoft.com/graph/webhooks-with-resource-data) Z powyższych powodów deweloperzy często używają powiadomień o zmianach wraz ze śledzeniem zmian w scenariuszach synchronizacji. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenariusz 6: Aprowizuj użytkowników i grupy w usłudze Azure AD.
Moja aplikacja tworzy informacje o użytkowniku, których klienci potrzebują w usłudze Azure AD. Może to być aplikacja HR niż zarządza zatrudniania, aplikacja komunikacyjna, która tworzy numery telefonów dla użytkowników lub innej aplikacji, która generuje dane, które byłyby cenne w usłudze Azure AD. Jak wypełnić rekord użytkownika w usłudze Azure AD tymi danymi? 

**Zalecenie** Wykres firmy Microsoft udostępnia /Użytkownicy i /Grupy punktów końcowych, które można zintegrować z dzisiaj do inicjowania obsługi administracyjnej użytkowników do usługi Azure AD. Należy pamiętać, że usługa Azure Active Directory nie obsługuje zapisywania tych użytkowników z powrotem do usługi Active Directory. 

> [!NOTE]
> Firma Microsoft ma usługę inicjowania obsługi administracyjnej, która pobiera dane z aplikacji HR, takich jak Workday i SuccessFactors. Te integracje są tworzone i zarządzane przez firmę Microsoft. Aby włączyć nową aplikację HR do naszej usługi, możesz poprosić o nią na [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Przejrzyj dokumentację dotyczącą synchronizacji programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrowanie niestandardowej aplikacji SCIM z usługą Azure AD](use-scim-to-provision-users-and-groups.md)
