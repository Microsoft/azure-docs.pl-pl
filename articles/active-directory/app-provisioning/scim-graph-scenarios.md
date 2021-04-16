---
title: Używanie standardu SCIM, Microsoft Graph i usługi Azure AD do aprowizować użytkowników i wzbogacać aplikacje o dane
description: Używanie standardu SCIM Microsoft Graph aprowizować użytkowników i wzbogacić aplikację o potrzebne dane.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: 87df7efcbab89c87a42e611f5fc1219239de6873
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530519"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Używanie standardu SCIM i Microsoft Graph do aprowizować użytkowników i wzbogacić aplikację o potrzebne dane

**Docelowi odbiorcy:** Ten artykuł jest skierowany do deweloperów, którzy będą budować aplikacje zintegrowane z Azure Active Directory (Azure AD). Jeśli chcesz korzystać z aplikacji już zintegrowanych z usługą Azure AD, takich jak Zoom, ServiceNow i [](../saas-apps/tutorial-list.md) DropBox, możesz pominąć ten artykuł i przejrzeć samouczki dotyczące określonych aplikacji lub sprawdzić, jak działa usługa aprowowania. [](./how-provisioning-works.md)

**Typowe scenariusze**

Usługa Azure AD zapewnia usługę aprowizowania i rozszerzalna platformę do kompilowania aplikacji. Drzewo decyzyjne przedstawia sposób, w jaki deweloper będzie używać standardu [SCIM](https://aka.ms/scimoverview) [i Microsoft Graph](/graph/overview) do automatyzacji aprowzowania. 

> [!div class="checklist"]
> * Automatyczne tworzenie użytkowników w mojej aplikacji
> * Automatycznie usuwaj użytkowników z mojej aplikacji, gdy nie powinni już mieć dostępu
> * Integrowanie mojej aplikacji z wieloma dostawcami tożsamości w celu aprowizowania
> * Wzbogać moją aplikację o dane usługi firmy Microsoft aplikacji, takich jak Teams, Outlook i Office.
> * Automatyczne tworzenie, aktualizowanie i usuwanie użytkowników i grup w usługach Azure AD i Active Directory

![Drzewo decyzyjne wykresu SCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenariusz 1. Automatyczne tworzenie użytkowników w mojej aplikacji
Obecnie administratorzy IT aprowizuje użytkowników, ręcznie tworząc konta użytkowników lub okresowo przesyłając pliki CSV do mojej aplikacji. Proces jest czasochłonny dla klientów i spowalnia przyjęcie mojej aplikacji. Potrzebuję tylko podstawowych informacji o użytkowniku, takich jak nazwa, adres e-mail i userPrincipalName, aby utworzyć użytkownika. 

**Rekomendacja**: 
* Jeśli klienci korzystają z różnych tożsamości i nie chcesz utrzymywać aparatu synchronizacji do integracji z każdym z nich, należy obsługiwać punkt końcowy [/Users](https://aka.ms/scimreferencecode) zgodny ze standardem SCIM. Twoi klienci będą mogli łatwo korzystać z tego punktu końcowego do integracji z usługą aprowiwizowania Usługi Azure AD i automatycznie tworzyć konta użytkowników, gdy potrzebują dostępu. Punkt końcowy można skompilować raz i będzie on zgodny ze wszystkimi tożsamościami. Zapoznaj się z poniższym przykładem żądania, aby dowiedzieć się, jak utworzyć użytkownika przy użyciu standardu SCIM.
* Jeśli potrzebujesz danych użytkownika znalezionych w obiekcie użytkownika w usłudze Azure AD i innych danych firmy Microsoft, rozważ sbudowania punktu końcowego SCIM na celu aprowizowanie użytkowników i wywoływanie interfejsu Microsoft Graph w celu uzyskania pozostałych danych. 

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

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenariusz 2. Automatyczne usuwanie użytkowników z aplikacji
Klienci korzystający z mojej aplikacji koncentrują się na zabezpieczeniach i mają wymagania dotyczące ładu, aby usunąć konta, gdy pracownicy już ich nie potrzebują. Jak zautomatyzować coprowizowanie z aplikacji?

**Zalecenie:** Obsługa punktu końcowego zgodnego ze standardem SCIM /Users. Usługa aprowizowania usługi Azure AD będzie wysyłać żądania wyłączenia i usunięcia, gdy użytkownik nie powinien już mieć dostępu. Zalecamy obsługę zarówno wyłączania, jak i usuwania użytkowników. Zobacz poniższe przykłady, aby zobaczyć, jak wygląda żądanie wyłączenia i usunięcia. 

Wyłączanie użytkownika
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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenariusz 3. Automatyzowanie zarządzania członkostwem w grupach w mojej aplikacji
Moja aplikacja korzysta z grup w celu uzyskania dostępu do różnych zasobów, a klienci chcą ponownie używać grup, które mają w usłudze Azure AD. Jak mogę importować grupy z usługi Azure AD i aktualizować je wraz ze zmianą członkostwa?  

**Zalecenie:** Obsługa punktu końcowego [/Groups](https://aka.ms/scimreferencecode)zgodnego ze standardem SCIM. Usługa aprowizowania Azure AD zajmie się tworzeniem grup i zarządzaniem aktualizacjami członkostwa w aplikacji. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenariusz 4. Wzbogacanie mojej aplikacji o dane z usługi firmy Microsoft, takich jak Teams, Outlook i OneDrive
Moja aplikacja jest wbudowana w aplikację Microsoft Teams i opiera się na danych komunikatów. Ponadto przechowujemy pliki dla użytkowników w usłudze OneDrive. Jak mogę wzbogacić moją aplikację o dane z tych usług i od firmy Microsoft?

**Zalecenie:** Punkt [Microsoft Graph](/graph/) punkt wejścia do uzyskiwania dostępu do danych firmy Microsoft. Każde obciążenie uwidacznia interfejsy API z potrzebnymi danymi. Dla powyższych scenariuszy można używać programu Microsoft Graph wraz [z aprowizowaniem](./use-scim-to-provision-users-and-groups.md) standardu SCIM. Przy użyciu standardu SCIM można aprowizować podstawowe atrybuty użytkownika w aplikacji podczas wywoływania grafu w celu uzyskania innych potrzebnych danych. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenariusz 5. Śledzenie zmian w aplikacjach usługi firmy Microsoft, takich jak Teams, Outlook i Azure AD
Chcę mieć możliwość śledzenia zmian w komunikatach aplikacji Teams i Outlook oraz reagowania na nie w czasie rzeczywistym. Jak mogę wypchnąć te zmiany do mojej aplikacji?

**Zalecenie:** Ten Microsoft Graph powiadomienia [o zmianach i](/graph/webhooks) śledzenie [zmian](/graph/delta-query-overview) dla różnych zasobów. Zwróć uwagę na następujące ograniczenia dotyczące powiadomień o zmianach:
- Jeśli odbiornik zdarzeń potwierdzi zdarzenie, ale z jakiegokolwiek powodu nie będzie mógł na nim działać, zdarzenie może zostać utracone.
- Nie ma gwarancji, że zmiany będą odbierane w kolejności chronologicznej.
- Powiadomienia o zmianie nie [](/graph/webhooks-with-resource-data) zawsze zawierają dane zasobów Z powyższych powodów deweloperzy często używają powiadomień o zmianach wraz ze śledzeniem zmian w scenariuszach synchronizacji. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenariusz 6. Aprowizuj użytkowników i grupy w usłudze Azure AD
Moja aplikacja tworzy informacje o użytkowniku, których klienci potrzebują w usłudze Azure AD. Może to być aplikacja działu kadr, a nie aplikacja do zarządzania zatrudnianiem, aplikacja do komunikacji tworząca numery telefonów dla użytkowników lub inna aplikacja generująca dane, które byłyby cenne w usłudze Azure AD. Jak mogę wypełniać rekord użytkownika w usłudze Azure AD przy użyciu tych danych? 

**Zalecenie** Program Microsoft Graph uwidacznia punkty końcowe /Users i /Groups, które można obecnie zintegrować, aby aprowizować użytkowników w usłudze Azure AD. Należy pamiętać, Azure Active Directory nie obsługuje zapisywania tych użytkowników z powrotem w usłudze Active Directory. 

> [!NOTE]
> Firma Microsoft ma usługę aprowizowania, która ściąga dane z aplikacji hr, takich jak Workday i SuccessFactors. Te integracje są budowaną i zarządzaną przez firmę Microsoft. W przypadku dołączania nowej aplikacji HR do naszej usługi możesz zażądać jej w [usłudze UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zapoznaj się z dokumentacją Microsoft Graph synchronizacji](/graph/api/resources/synchronization-overview)
- [Integrowanie niestandardowej aplikacji SCIM z usługą Azure AD](use-scim-to-provision-users-and-groups.md)