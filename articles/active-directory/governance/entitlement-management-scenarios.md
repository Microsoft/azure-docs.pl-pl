---
title: Typowe scenariusze zarządzania uprawnieniami — Azure AD
description: Zapoznaj się z instrukcjami wysokiego poziomu, które należy wykonać w przypadku typowych scenariuszy Azure Active Directory zarządzania uprawnieniami.
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
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531963"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Typowe scenariusze zarządzania uprawnieniami w usłudze Azure AD

Istnieje kilka sposobów konfigurowania zarządzania uprawnieniami dla organizacji. Jeśli jednak dopiero zaczynasz, warto zrozumieć typowe scenariusze dla administratorów, właścicieli wykazu, menedżerów pakietów dostępu, osób zatwierdzających i osób żądających.

## <a name="delegate"></a>Delegat

### <a name="administrator-delegate-management-of-resources"></a>Administrator: Delegowanie zarządzania zasobami

1. [Obejrzyj wideo: Delegowanie z działu IT do kierownika działu](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegowanie użytkowników do roli twórcy katalogu](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Twórca katalogu: Delegowanie zarządzania zasobami

- [Tworzenie nowego katalogu](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Właściciel katalogu: delegowanie zarządzania zasobami

1. [Dodawanie współwłaścicieli do katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Dodawanie zasobów do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Właściciel katalogu: delegowanie zarządzania pakietami dostępu

1. [Obejrzyj wideo: Delegowanie od właściciela wykazu do uzyskiwania dostępu do menedżera pakietów](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegowanie użytkowników do uzyskiwania dostępu do roli menedżera pakietów](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Reguluj dostęp dla użytkowników w organizacji

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Menedżer pakietów dostępu: zezwala pracownikom w organizacji na żądanie dostępu do zasobów

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-create.md#resource-roles)
1. [Dodawanie zasad żądań w celu umożliwienia użytkownikom w katalogu żądania dostępu](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Określanie ustawień wygasania](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Requestor: Request access to resources (Żądaj dostępu do zasobów)

1. [Zaloguj się do portalu Mój dostęp portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Znajdowanie pakietu dostępu
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: Zatwierdzanie żądań do zasobów

1. [Otwieranie żądania w Mój dostęp portal](entitlement-management-request-approve.md#open-request)
1. [Zatwierdzanie lub odrzucanie żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Żądajca: wyświetl zasoby, do których masz już dostęp

1. [Zaloguj się do portalu Mój dostęp portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetlanie aktywnych pakietów dostępu

## <a name="govern-access-for-users-outside-your-organization"></a>Reguluj dostęp dla użytkowników spoza organizacji

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administrator: Współpraca z zewnętrzną organizacją partnerską

1. [Przeczytaj, jak działa dostęp dla użytkowników zewnętrznych](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Przeglądanie ustawień dla użytkowników zewnętrznych](entitlement-management-external-users.md#settings-for-external-users)
1. [Dodawanie połączenia do organizacji zewnętrznej](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Menedżer pakietów dostępu: współpraca z zewnętrzną organizacją partnerską

1. [Tworzenie nowego pakietu dostępu](entitlement-management-access-package-create.md#start-new-access-package)
1. [Dodawanie grup, zespołów, aplikacji lub witryn programu SharePoint w celu uzyskania dostępu do pakietu](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Dodawanie zasad żądań w celu umożliwienia użytkownikom, którzy nie mają dostępu do katalogu, żądania dostępu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Określanie ustawień wygasania](entitlement-management-access-package-create.md#lifecycle)
1. [Skopiuj link, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md)
1. Wyślij link do partnera zewnętrznego partnera kontaktowego, aby udostępnić go użytkownikom

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Requestor: Request access to resources as an external user (Żądaj dostępu do zasobów jako użytkownik zewnętrzny)

1. Znajdowanie linku do pakietu dostępu otrzymanego od osoby kontaktowej
1. [Zaloguj się do portalu Mój dostęp portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Żądanie dostępu](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Osoba zatwierdzająca: Zatwierdzanie żądań do zasobów

1. [Otwieranie żądania w Mój dostęp portal](entitlement-management-request-approve.md#open-request)
1. [Zatwierdzanie lub odrzucanie żądania dostępu](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Requestor: Wyświetl zasoby, do których masz już dostęp

1. [Zaloguj się do portalu Mój dostęp portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Wyświetlanie aktywnych pakietów dostępu

## <a name="day-to-day-management"></a>Zarządzanie na co dzień

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie zasobów dla projektu

1. [Obejrzyj wideo: Day-to-day management: Things have changed (Zarządzanie na co dzień: zmiany)](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwieranie pakietu dostępu
1. [Dodawanie lub usuwanie grup, aplikacji, aplikacji lub witryn programu SharePoint](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie czasu trwania projektu

1. [Obejrzyj wideo: Day-to-day management: Things have changed (Zarządzanie na co dzień: zmiany)](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otwieranie pakietu dostępu
1. [Otwieranie ustawień cyklu życia](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualizowanie ustawień wygasania](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie sposobu zatwierdzenia dostępu do projektu

1. [Obejrzyj wideo: Day-to-day management: Things have changed (Zarządzanie na co dzień: co się zmieniło)](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Otwieranie istniejących zasad ustawień żądania](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aktualizowanie ustawień zatwierdzania](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Menedżer pakietów dostępu: aktualizowanie osób dla projektu

1. [Obejrzyj wideo: Day-to-day management: Things have changed (Zarządzanie na co dzień: co się zmieniło)](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Usuwanie użytkowników, którzy już nie potrzebują dostępu](entitlement-management-access-package-assignments.md)
1. [Otwieranie istniejących zasad ustawień żądania](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Dodawanie użytkowników, którzy potrzebują dostępu](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Menedżer pakietów dostępu: bezpośrednie przypisywanie określonych użytkowników do pakietu dostępu

1. [Jeśli użytkownicy potrzebują różnych ustawień cyklu życia, dodaj nowe zasady do pakietu dostępu](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Bezpośrednie przypisywanie określonych użytkowników do pakietu dostępu](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Przypisania i raporty

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administrator: Wyświetlanie osób, które mają przypisania do pakietu dostępu

1. Otwieranie pakietu dostępu
1. [Wyświetlanie przypisań](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiwizowanie raportów i dzienników](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administrator: Wyświetlanie zasobów przypisanych do użytkowników

1. [Wyświetlanie pakietów dostępu dla użytkownika](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Wyświetlanie przypisań zasobów dla użytkownika](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Administracja programowa

Możesz również zarządzać pakietami dostępu, katalogami, zasadami, żądaniami i przypisaniami przy użyciu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może `EntitlementManagement.ReadWrite.All` wywołać interfejs API zarządzania [uprawnieniami](/graph/tutorial-access-package-api).

## <a name="next-steps"></a>Następne kroki

- [Delegowanie i role](entitlement-management-delegate.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)