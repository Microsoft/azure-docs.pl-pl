---
title: Konfigurowanie samoobsługowego zarządzania grupami — Azure Active Directory | Microsoft Docs
description: Tworzenie grup zabezpieczeń lub grup Office 365 w usłudze Azure Active Directory oraz zarządzanie nimi i żądanie członkostwa w grupach zabezpieczeń lub grupach usługi Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541deb5cf44ad5440e31641b673ed5da5b5d2b26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768543"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurowanie zarządzania grupami samoobsługowymi w usłudze Azure Active Directory 

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup usługi Office 365 w usłudze Azure Active Directory (Azure AD) i zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa i może delegować kontrolę nad członkostwem w grupie. Funkcje zarządzania grupami samoobsługowymi nie są dostępne dla grup zabezpieczeń z obsługą poczty ani list dystrybucyjnych.

## <a name="self-service-group-membership-defaults"></a>Domyślne ustawienia członkostwa w grupach samoobsługowych

Gdy grupy zabezpieczeń są tworzone w witrynie Azure portal lub przy użyciu usługi Azure AD PowerShell, tylko właściciele grupy mogą aktualizować członkostwo. Grupy zabezpieczeń utworzone przez samoobsługę w [panelu Access](https://account.activedirectory.windowsazure.com/r#/joinGroups) i we wszystkich grupach usługi Office 365 są dostępne do dołączenia dla wszystkich użytkowników, niezależnie od tego, czy są zatwierdzone przez właściciela, czy zatwierdzone automatycznie. W panelu Dostęp można zmienić opcje członkostwa podczas tworzenia grupy.

Grupy utworzone w | Domyślne zachowanie grupy zabezpieczeń | Domyślne zachowanie grupy usługi Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do dołączenia w panelu programu Access | Otwórz, aby dołączyć dla wszystkich użytkowników
[Azure Portal](https://portal.azure.com) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do dołączenia w panelu programu Access<br>Właściciel nie jest przypisywany automatycznie podczas tworzenia grupy | Otwórz, aby dołączyć dla wszystkich użytkowników
[Panel dostęp](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otwórz, aby dołączyć dla wszystkich użytkowników<br>Opcje członkostwa można zmieniać podczas tworzenia grupy | Otwórz, aby dołączyć dla wszystkich użytkowników<br>Opcje członkostwa można zmieniać podczas tworzenia grupy

## <a name="self-service-group-management-scenarios"></a>Scenariusze zarządzania grupami samoobsługi

* **Delegowane zarządzanie grupami** Przykładem może być administrator zarządzający dostępem do aplikacji SaaS używanej w firmie. Zarządzanie prawami dostępu jest coraz większym obciążeniem, więc administrator zwraca się do właściciela firmy o utworzenie nowej grupy. Administrator przypisuje dostęp do aplikacji do nowej grupy i dodaje do grupy wszystkie osoby już uzyskujące dostęp do aplikacji. Właściciel firmy może dodawać kolejnych użytkowników, dla których aplikacja jest automatycznie aprowizowana. Właściciel firmy nie musi czekać na administratora w celu zarządzania dostępem dla użytkowników. Jeśli administrator udziela tego samego uprawnienia menedżerowi w innej grupie biznesowej, może również zarządzać dostępem dla własnych członków grupy. Ani właściciel firmy, ani menedżer nie mogą przeglądać ani zarządzać wzajemnymi członkostwami w grupach. Administrator nadal może wyświetlać wszystkich użytkowników mających dostęp do aplikacji i w razie potrzeby blokować prawa dostępu.
* **Samoobsługowe zarządzanie grupami** Przykładem tego scenariusza jest dwóch użytkowników mających niezależnie skonfigurowane witryny usługi SharePoint Online. Chcą dać swoim zespołom dostęp do swoich stron. W tym celu mogą utworzyć jedną grupę w usłudze Azure AD i wybrać tę grupę do przydzielenia dostępu do witryn w usłudze SharePoint Online. Gdy inna osoba będzie chciała uzyskać dostęp, wprowadzi żądanie w Panelu dostępu, a po zatwierdzeniu automatycznie uzyska dostęp do obu witryn usługi SharePoint Online. Jeden z użytkowników może stwierdzić później, że wszystkie osoby mające dostęp do witryny powinny również mieć dostęp do określonej aplikacji SaaS. Administrator aplikacji SaaS może dodać do witryny programu SharePoint Online prawa dostępu dla aplikacji. Od tego momentu zatwierdzenie żądania dostępu spowoduje przyznanie dostępu do obu witryn usługi SharePoint Online oraz do tej aplikacji SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Włączanie samoobsługi użytkowników w grupie

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **Grupy**, a następnie wybierz pozycję Ustawienia **ogólne.**
1. Ustaw **właścicieli można zarządzać żądania członkostwa grupy w Panelu dostępu** do **Tak**.
1. Ustaw **pozycję Ogranicz dostęp do grup w Panelu dostępu** na **Nie**.
1. Jeśli ustawisz **Użytkownicy mogą tworzyć grupy zabezpieczeń w witrynach platformy Azure** lub **użytkownicy mogą tworzyć grupy usługi Office 365 w witrynach platformy Azure w** celu

    - **Tak:** Wszyscy użytkownicy w organizacji usługi Azure AD mogą tworzyć nowe grupy zabezpieczeń i dodawać członków do tych grup. Te nowe grupy będą również wyświetlane na panelu dostępu dla innych użytkowników. Jeśli ustawienie zasad w grupie na to zezwala, inni użytkownicy mogą tworzyć żądania dołączenia do tych grup
    - **Nie:** użytkownicy nie mogą tworzyć grup i nie mogą zmieniać istniejących grup, których są właścicielami. Jednak nadal mogą zarządzać członkostwem w tych grupach i zatwierdzać żądania dołączenia do ich grup pochodzące od innych użytkowników.

Można również użyć **właścicieli, którzy mogą przypisywać członków jako właścicieli grup w witrynach Azure portals** i **właścicieli, którzy mogą przypisać członków jako właścicieli grup w witrynach platformy Azure,** aby uzyskać bardziej szczegółową kontrolę dostępu nad zarządzaniem grupami samoobsługowymi dla użytkowników.

Gdy użytkownicy mogą tworzyć grupy, wszyscy użytkownicy w organizacji mogą tworzyć nowe grupy, a następnie, jako domyślny właściciel, dodawać członków do tych grup. Nie można określić osób, które mogą tworzyć własne grupy. Osoby można określić tylko w celu nadanie innej grupie właściciela grupy.

> [!NOTE]
> Licencja usługi Azure Active Directory Premium (P1 lub P2) jest wymagana dla użytkowników, aby zażądać dołączenia do grupy zabezpieczeń lub grupy usługi Office 365, a właściciele zatwierdzić lub odrzucić żądania członkostwa. Bez licencji usługi Azure Active Directory Premium użytkownicy nadal mogą zarządzać swoimi grupami w Panelu dostępu, ale nie mogą utworzyć grupy, która wymaga zatwierdzenia przez właściciela w Panelu programu Access i nie mogą żądać dołączenia do grupy. 

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](groups-settings-cmdlets.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
