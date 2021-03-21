---
title: Korzystanie z standard scim, Microsoft Graph i usługi Azure AD w celu aprowizacji użytkowników i wzbogacania aplikacji przy użyciu danych
description: Korzystając z standard scim i Microsoft Graph, aby udostępnić użytkownikom i wzbogacić swoją aplikację przy użyciu potrzebnych danych.
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
ms.openlocfilehash: 0a5d84585f28f6d13cbceb1fec41d6cdabf6d08c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255631"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Korzystanie z standard scim i Microsoft Graph ze sobą w celu aprowizacji użytkowników i wzbogacania aplikacji o potrzebne dane

**Odbiorcy docelowi:** Ten artykuł jest przeznaczony dla deweloperów tworzących aplikacje, które mają być zintegrowane z usługą Azure Active Directory (Azure AD). Jeśli zamierzasz korzystać z aplikacji już zintegrowanych z usługą Azure AD, takich jak zoom, usługi ServiceNow i DropBox, możesz pominąć ten artykuł i zapoznać się z [samouczkami](../saas-apps/tutorial-list.md) dotyczącymi konkretnych aplikacji lub sprawdzić, [jak działa usługa aprowizacji](./how-provisioning-works.md).

**Typowe scenariusze**

Usługa Azure AD zapewnia wbudowaną usługę usługi Box do aprowizacji i rozszerzalną platformę do kompilowania aplikacji. Drzewo decyzyjne zawiera informacje o tym, w jaki sposób deweloper będzie używać [Standard scim](https://aka.ms/scimoverview) oraz [Microsoft Graph](/graph/overview) do automatyzowania aprowizacji. 

> [!div class="checklist"]
> * Automatycznie twórz użytkowników w mojej aplikacji
> * Automatycznie usuwaj użytkowników z mojej aplikacji, gdy nie mają już dostępu
> * Integrowanie aplikacji z wieloma dostawcami tożsamości na potrzeby aprowizacji
> * Wzbogacaj moją aplikację o dane z usług firmy Microsoft, takich jak zespoły, Outlook i Office.
> * Automatycznie twórz, Aktualizuj i usuwaj użytkowników i grupy w usłudze Azure AD i Active Directory

![Drzewo decyzyjne programu Standard scim Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenariusz 1: automatyczne tworzenie użytkowników w mojej aplikacji
Obecnie Administratorzy IT udostępniają użytkownikom ręcznie Tworzenie kont użytkowników lub okresowe przekazywanie plików CSV do aplikacji. Ten proces jest czasochłonny dla klientów i spowalnia wdrażanie aplikacji. Wszystko, czego potrzebuję, to podstawowe informacje o użytkowniku, takie jak imię i nazwisko, adres e-mail i userPrincipalName, aby utworzyć użytkownika. 

**Rekomendacja**: 
* Jeśli klienci korzystają z różnych dostawców tożsamości i nie chcesz zachować aparatu synchronizacji do integracji z każdym z nich, obsługuj [punkt końcowy zgodny z standard scim](https://aka.ms/scimreferencecode) . Klienci będą mogli łatwo korzystać z tego punktu końcowego w celu integracji z usługą Azure AD Provisioning i automatycznego tworzenia kont użytkowników, gdy potrzebują dostępu. Punkt końcowy można skompilować raz i będzie zgodny ze wszystkimi dostawców tożsamości. Zapoznaj się z przykładowym żądaniem poniżej, aby dowiedzieć się, jak utworzyć użytkownika przy użyciu Standard scim.
* Jeśli wymagane jest znalezienie danych użytkownika w obiekcie użytkownika w usłudze Azure AD i innych danych firmy Microsoft, należy rozważyć utworzenie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników i wywołanie w Microsoft Graph, aby uzyskać resztę danych. 

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
Klienci korzystający z mojej aplikacji są ukierunkowani na zabezpieczenia i mają wymagania dotyczące nadzoru, aby usunąć konta, gdy pracownicy nie potrzebują ich już. Jak mogę zautomatyzować cofanie aprowizacji z mojej aplikacji?

**Zalecenie:** Obsługa punktu końcowego zgodnego z standard scim. Usługa Azure AD Provisioning wyśle żądania do wyłączenia i usunięcia, gdy użytkownik nie powinien już mieć dostępu. Zalecamy obsługę zarówno wyłączania, jak i usuwania użytkowników. Zapoznaj się z poniższymi przykładami, które wyglądają jak żądanie Disable i DELETE. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenariusz 3. Automatyzowanie zarządzania członkostwem w grupach w mojej aplikacji
Aplikacja korzysta z grup, aby uzyskać dostęp do różnych zasobów, a klienci chcą ponownie użyć grup, które mają w usłudze Azure AD. Jak mogę zaimportować grupy z usługi Azure AD i aktualizować je, gdy zmienią się członkostwa?  

**Zalecenie:** Obsługa Standard scim zgodnego [punktu końcowego](https://aka.ms/scimreferencecode)/groups. Usługa Azure AD Provisioning zajmie się tworzeniem grup i zarządzaniem aktualizacjami członkostwa w aplikacji. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenariusz 4: wzbogacanie mojej aplikacji o dane z usług firmy Microsoft, takich jak zespoły, program Outlook i usługa OneDrive
Moja aplikacja jest wbudowana w Microsoft Teams i opiera się na danych komunikatów. Ponadto przechowujemy pliki dla użytkowników w usłudze OneDrive. Jak mogę wzbogacić swoją aplikację z danymi z tych usług i między firmą Microsoft?

**Zalecenie:** [Microsoft Graph](/graph/) jest punktem wejścia, aby uzyskać dostęp do danych firmy Microsoft. Każde obciążenie udostępnia interfejsy API z wymaganymi danymi. Programu Microsoft Graph można używać razem z [Standard scim aprowizacji](./use-scim-to-provision-users-and-groups.md) dla powyższych scenariuszy. Możesz użyć standard scim, aby udostępnić podstawowe atrybuty użytkownika w aplikacji podczas wywoływania do programu Graph, aby uzyskać inne potrzebne dane. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenariusz 5: śledzenie zmian w usługach firmy Microsoft, takich jak zespoły, Outlook i Azure AD
Muszę mieć możliwość śledzenia zmian w zespołach i wiadomościach programu Outlook i reagowania na nie w czasie rzeczywistym. Jak można pobrać te zmiany do mojej aplikacji?

**Zalecenie:** Microsoft Graph zapewnia [powiadomienia o zmianach](/graph/webhooks) i [śledzenie zmian](/graph/delta-query-overview) dla różnych zasobów. Należy pamiętać o następujących ograniczeniach dotyczących powiadomień o zmianach:
- Jeśli odbiorca zdarzenia potwierdzi zdarzenie, ale nie będzie działać z jakiegokolwiek powodu, może to spowodować utratę zdarzenia.
- Kolejność, w której zmiany są odbierane, nie gwarantuje, że jest to chronologiczne.
- Powiadomienia o zmianach nie zawierają zawsze [danych zasobów](/graph/webhooks-with-resource-data) dla powyższych powodów, deweloperzy często używają powiadomień o zmianach wraz ze śledzeniem zmian w scenariuszach synchronizacji. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenariusz 6. Udostępnianie użytkowników i grup w usłudze Azure AD
Moja aplikacja tworzy informacje o użytkowniku, którego potrzebują klienci w usłudze Azure AD. Może to być aplikacja KADRowa niż zarządzanie zatrudnieniem, aplikacją komunikacyjną, która tworzy numery telefonów dla użytkowników, lub inną aplikację, która generuje dane, które byłyby przydatne w usłudze Azure AD. Jak mogę wypełnić rekordem użytkownika w usłudze Azure AD tymi danymi? 

**Zalecenie** Program Microsoft Graph udostępnia/users i/Groups punkty końcowe, które można zintegrować z dzisiaj, aby udostępnić użytkownikom usługę Azure AD. Należy pamiętać, że Azure Active Directory nie obsługuje zapisywania tych użytkowników z powrotem do Active Directory. 

> [!NOTE]
> Firma Microsoft oferuje usługę aprowizacji, która pobiera dane z aplikacji KADRowych, takich jak Workday i SuccessFactors. Te integracje są kompilowane i zarządzane przez firmę Microsoft. W celu dołączenia nowej aplikacji KADRowej do naszej usługi możesz zażądać jej w usłudze [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zapoznaj się z dokumentacją Microsoft Graph synchronizacji](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrowanie niestandardowej aplikacji Standard scim z usługą Azure AD](use-scim-to-provision-users-and-groups.md)