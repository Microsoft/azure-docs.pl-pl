---
title: Konfigurowanie samoobsługowego zarządzania grupami — Azure Active Directory | Microsoft Docs
description: Tworzenie grup zabezpieczeń lub grup Microsoft 365 i zarządzanie nimi w Azure Active Directory i zażądaj grupy zabezpieczeń lub członkostwa w grupach Microsoft 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1730507c022d57495efa56c98215ff72d07e180
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578182"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Konfigurowanie samoobsługowego zarządzania grupami w Azure Active Directory 

Można umożliwić użytkownikom tworzenie własnych grup zabezpieczeń lub grup Microsoft 365 w usłudze Azure Active Directory (Azure AD) oraz zarządzanie nimi. Właściciel grupy może zatwierdzać lub odrzucać żądania członkostwa, a także delegować kontrolę nad członkostwem w grupie. Funkcje samoobsługowego zarządzania grupami są niedostępne w przypadku grup zabezpieczeń obsługujących pocztę lub listy dystrybucyjnej.

## <a name="self-service-group-membership-defaults"></a>Ustawienia domyślne członkostwa w grupach samoobsługi

Po utworzeniu grup zabezpieczeń w Azure Portal lub przy użyciu programu Azure AD PowerShell tylko właściciele grupy mogą aktualizować członkostwo. Grupy zabezpieczeń utworzone przez samoobsługowe w [panelu dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) i wszystkie grupy Microsoft 365 są dostępne do przyłączenia do wszystkich użytkowników, niezależnie od tego, czy są zatwierdzone przez właściciela, czy automatycznie zatwierdzone. W panelu dostępu można zmienić opcje członkostwa podczas tworzenia grupy.

Grupy utworzone w | Zachowanie domyślne grupy zabezpieczeń | Zachowanie domyślne grupy Microsoft 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do przyłączenia w panelu dostępu | Otwórz do dołączenia dla wszystkich użytkowników
[Witryna Azure Portal](https://portal.azure.com) | Tylko właściciele mogą dodawać członków<br>Widoczne, ale niedostępne do przyłączenia w panelu dostępu<br>Właściciel nie jest przypisywany automatycznie podczas tworzenia grupy | Otwórz do dołączenia dla wszystkich użytkowników
[Panel dostępu](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Otwórz do dołączenia dla wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy | Otwórz do dołączenia dla wszystkich użytkowników<br>Opcje członkostwa można zmienić po utworzeniu grupy

## <a name="self-service-group-management-scenarios"></a>Scenariusze samoobsługowego zarządzania grupami

* **Delegowane zarządzanie grupami** Przykładem może być administrator zarządzający dostępem do aplikacji SaaS używanej w firmie. Zarządzanie prawami dostępu jest coraz większym obciążeniem, więc administrator zwraca się do właściciela firmy o utworzenie nowej grupy. Administrator przypisuje aplikacji dostęp do nowej grupy i dodaje do grupy wszystkie osoby, które już uzyskują dostęp do aplikacji. Właściciel firmy może dodawać kolejnych użytkowników, dla których aplikacja jest automatycznie aprowizowana. Właściciel firmy nie musi czekać na administratora w celu zarządzania dostępem dla użytkowników. Jeśli administrator przyznaje to samo uprawnienie do Menedżera w innej grupie biznesowej, może również zarządzać dostępem dla swoich własnych członków grupy. Właściciel biznesowy i Menedżer nie mogą wyświetlać ani zarządzać członkostwem w grupach poszczególnych grup. Administrator nadal może wyświetlać wszystkich użytkowników mających dostęp do aplikacji i w razie potrzeby blokować prawa dostępu.
* **Samoobsługowe zarządzanie grupami** Przykładem tego scenariusza jest dwóch użytkowników mających niezależnie skonfigurowane witryny usługi SharePoint Online. Chcą udzielić innym zespołom dostępu do swoich witryn. W tym celu mogą utworzyć jedną grupę w usłudze Azure AD i wybrać tę grupę do przydzielenia dostępu do witryn w usłudze SharePoint Online. Gdy inna osoba będzie chciała uzyskać dostęp, wprowadzi żądanie w Panelu dostępu, a po zatwierdzeniu automatycznie uzyska dostęp do obu witryn usługi SharePoint Online. Jeden z użytkowników może stwierdzić później, że wszystkie osoby mające dostęp do witryny powinny również mieć dostęp do określonej aplikacji SaaS. Administrator aplikacji SaaS może dodać do witryny programu SharePoint Online prawa dostępu dla aplikacji. Od tego momentu zatwierdzenie żądania dostępu spowoduje przyznanie dostępu do obu witryn usługi SharePoint Online oraz do tej aplikacji SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Włączanie samoobsługi użytkowników w grupie

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
1. Wybierz pozycję **grupy** , a następnie wybierz pozycję Ustawienia **Ogólne** .
1. **Właściciele zestawu mogą zarządzać żądaniami członkostwa w grupach w panelu dostępu** do wartości **tak**.
1. Dla opcji **Ogranicz dostęp do grup w panelu dostępu** wybierz wartość **nie**.
1. W przypadku wybrania opcji **Użytkownicy mogą tworzyć grupy zabezpieczeń w portalach platformy Azure** lub **Użytkownicy mogą tworzyć grupy Microsoft 365 w portalach platformy Azure** , aby

    - **Tak** : Wszyscy użytkownicy w organizacji usługi Azure AD mogą tworzyć nowe grupy zabezpieczeń i dodawać do nich członków. Te nowe grupy będą również wyświetlane na panelu dostępu dla innych użytkowników. Jeśli ustawienie zasad w grupie zezwala na to, inni użytkownicy mogą tworzyć żądania dołączenia do tych grup.
    - **Nie** : użytkownicy nie mogą tworzyć grup ani zmieniać istniejących grup, których są właścicielami. Jednak nadal mogą zarządzać członkostwem w tych grupach i zatwierdzać żądania dołączenia do ich grup pochodzące od innych użytkowników.

Można również użyć **właścicieli, którzy mogą przypisywać członków jako właścicieli grup w Azure Portal,** Aby uzyskać bardziej szczegółową kontrolę dostępu do samoobsługowego zarządzania grupami dla użytkowników.

Gdy użytkownicy mogą tworzyć grupy, wszyscy użytkownicy w organizacji mogą tworzyć nowe grupy, a następnie, jako domyślny właściciel, dodawać członków do tych grup. Nie można określić osób, które mogą tworzyć własne grupy. Można określić tylko osoby, które będą miały właściciela grupy.

> [!NOTE]
> Licencja na Azure Active Directory — wersja Premium (P1 lub P2) jest wymagana, aby użytkownicy mogli zażądać dołączenia do grupy zabezpieczeń lub grupy Microsoft 365 i dla właścicieli w celu zatwierdzenia lub odmowy żądań członkostwa. Bez licencji Azure Active Directory — wersja Premium użytkownicy nadal mogą zarządzać swoimi grupami w panelu dostępu, ale nie mogą utworzyć grupy wymagającej zatwierdzenia przez właściciela w panelu dostępu i nie mogą żądać dołączenia do grupy.

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Co to jest usługa Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
