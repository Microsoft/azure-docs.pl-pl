---
title: Typowe scenariusze związane z zarządzaniem prawami — Azure AD
description: Zapoznaj się z ogólnymi krokami, które należy wykonać w przypadku typowych scenariuszy w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8a8f76ce7d46c0a44dd86ef1490c1c74a7992a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979510"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Typowe scenariusze zarządzania prawami w usłudze Azure AD

Istnieje kilka sposobów konfigurowania zarządzania prawami dla organizacji. Jednak jeśli dopiero zaczynasz pracę, warto zrozumieć typowe scenariusze dla administratorów, właścicieli wykazu, menedżerów pakietów dostępu, osób zatwierdzających i osoby żądające.

## <a name="delegate"></a>Delegat

### <a name="administrator-delegate-management-of-resources"></a>Administrator: delegowanie zarządzania zasobami

1. [Obejrzyj wideo: delegowanie z działu IT do menadżera działów](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegowanie użytkowników do roli twórcy katalogu](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Autor wykazu: delegowanie zarządzania zasobami

- [Utwórz nowy katalog](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Właściciel wykazu: delegowanie zarządzania zasobami

1. [Dodaj współwłaścicieli do wykazu](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Dodawanie zasobów do wykazu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Właściciel wykazu: delegowanie zarządzania pakietami dostępu

1. [Obejrzyj wideo: delegowanie od właściciela wykazu do Menedżera pakietów](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegowanie użytkowników w celu uzyskania dostępu do roli menedżera pakietów](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Zarządzanie dostępem użytkowników w organizacji

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Menedżer pakietów dostępu: umożliwianie pracownikom w organizacji żądania dostępu do zasobów

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, zespołów, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-create.md#resource-roles)
1. [Dodaj zasady żądań, aby umożliwić użytkownikom w katalogu żądanie dostępu](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Określ ustawienia wygasania](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Obiekt żądający: Zażądaj dostępu do zasobów

1. [Logowanie się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Znajdź pakiet dostępu
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: zatwierdzanie żądań do zasobów

1. [Otwórz żądanie w portalu dostępu](entitlement-management-request-approve.md#open-request)
1. [Zatwierdź lub Odmów żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Osoba żądająca: wyświetlanie zasobów, do których masz już dostęp

1. [Logowanie się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetl aktywne pakiety dostępu

## <a name="govern-access-for-users-outside-your-organization"></a>Zarządzanie dostępem użytkowników spoza organizacji

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrator: współpraca z zewnętrzną organizacją partnera

1. [Przeczytaj, jak działa dostęp dla użytkowników zewnętrznych](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Przejrzyj ustawienia dla użytkowników zewnętrznych](entitlement-management-external-users.md#settings-for-external-users)
1. [Dodawanie połączenia z organizacją zewnętrzną](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Menedżer pakietów dostępu: współpraca z zewnętrzną organizacją partnera

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, zespołów, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Dodaj zasady żądań, aby umożliwić użytkownikom nieznajdującym się w katalogu żądanie dostępu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Określ ustawienia wygasania](entitlement-management-access-package-create.md#lifecycle)
1. [Skopiuj link, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md)
1. Wyślij link do partnera kontaktowego partnera zewnętrznego w celu udostępnienia ich użytkownikom

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Obiekt żądający: Zażądaj dostępu do zasobów jako użytkownik zewnętrzny

1. Znajdź link pakietu dostępu otrzymany od kontaktu
1. [Logowanie się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: zatwierdzanie żądań do zasobów

1. [Otwórz żądanie w portalu dostępu](entitlement-management-request-approve.md#open-request)
1. [Zatwierdź lub Odmów żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Obiekt żądający: Wyświetl zasoby, do których masz już dostęp

1. [Logowanie się do portalu My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetl aktywne pakiety dostępu

## <a name="day-to-day-management"></a>Zarządzanie codziennym dniem

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie zasobów dla projektu

1. [Obejrzyj wideo: bieżące zarządzanie: zmieniono elementy](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwórz pakiet dostępu
1. [Dodawanie lub usuwanie grup, zespołów, aplikacji lub witryn programu SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie czasu trwania dla projektu

1. [Obejrzyj wideo: bieżące zarządzanie: zmieniono elementy](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwórz pakiet dostępu
1. [Otwórz ustawienia cyklu życia](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualizowanie ustawień wygasania](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie sposobu zatwierdzania dostępu dla projektu

1. [Obejrzyj wideo: bieżące zarządzanie: zmieniono elementy](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Otwórz istniejące zasady ustawień żądania](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aktualizowanie ustawień zatwierdzania](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie osób dla projektu

1. [Obejrzyj wideo: bieżące zarządzanie: zmieniono elementy](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Usuń użytkowników, którzy już nie potrzebują dostępu](entitlement-management-access-package-assignments.md)
1. [Otwórz istniejące zasady ustawień żądania](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Dodawanie użytkowników, którzy potrzebują dostępu](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Menedżer pakietów dostępu: bezpośrednie przypisywanie określonych użytkowników do pakietu dostępu

1. [Jeśli użytkownicy potrzebują innych ustawień cyklu życia, Dodaj nowe zasady do pakietu dostępu](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Bezpośrednie przypisywanie określonych użytkowników do pakietu dostępu](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Przypisania i raporty

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrator: Wyświetlanie przypisań do pakietu dostępu

1. Otwórz pakiet dostępu
1. [Wyświetlanie przypisań](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiwizowanie raportów i dzienników](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrator: wyświetlanie zasobów przypisanych do użytkowników

1. [Wyświetlanie pakietów dostępu dla użytkownika](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Wyświetlanie przypisań zasobów dla użytkownika](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administracja programowa

Można także zarządzać pakietami dostępu, katalogami, zasadami, żądaniami i przypisaniami przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienie, `EntitlementManagement.ReadWrite.All` może wywołać [interfejs API zarządzania uprawnieniami](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta ).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie i role](entitlement-management-delegate.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)