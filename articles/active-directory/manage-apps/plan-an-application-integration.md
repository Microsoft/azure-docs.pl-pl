---
title: Wprowadzenie do integrowania usługi Azure AD z aplikacjami | Microsoft Docs
description: Ten artykuł zawiera Przewodnik wprowadzający do integrowania Azure Active Directory (AD) z aplikacjami lokalnymi i aplikacjami w chmurze.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc691148723e610f0885f7c20fd8684f11a81dc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182330"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Przewodnik po rozpoczęciu integracji Azure Active Directory z aplikacjami

Ten temat zawiera podsumowanie procesu integracji aplikacji z usługą Azure Active Directory (AD). Każda z poniższych sekcji zawiera krótkie podsumowanie bardziej szczegółowego tematu, dzięki czemu można określić, które części tego przewodnika wprowadzającego są odpowiednie dla Ciebie.

Aby pobrać szczegółowe plany wdrożenia, zobacz [następne kroki](#next-steps).

## <a name="take-inventory"></a>Utwórz spis
Przed integracją aplikacji z usługą Azure AD ważne jest, aby wiedzieć, gdzie jesteś i gdzie chcesz go umieścić.  Poniższe pytania mają na celu pomyślną opinię na temat projektu integracji aplikacji usługi Azure AD.

### <a name="application-inventory"></a>Spis aplikacji
* Gdzie znajdują się wszystkie aplikacje? Kto jest właścicielem?
* Jakiego rodzaju uwierzytelniania wymagają Twoje aplikacje?
* Kto musi mieć dostęp do aplikacji?
* Czy chcesz wdrożyć nową aplikację?
  * Czy zostanie on skompilowany i wdrożony w wystąpieniu obliczeniowym platformy Azure?
  * Czy będziesz używać takiego, który jest dostępny w galerii aplikacji platformy Azure?

### <a name="user-and-group-inventory"></a>Spis użytkowników i grup
* Gdzie znajdują się konta użytkowników?
  * Lokalna usługa Active Directory
  * Azure AD
  * W ramach oddzielnej bazy danych aplikacji, której jesteś własnym
  * W aplikacjach niezaakceptowanych oficjalnie
  * Wszystkie powyższe
* Jakie uprawnienia i przypisania ról są obecnie dostępne dla poszczególnych użytkowników? Czy musisz przejrzeć swój dostęp lub czy masz pewność, że masz teraz odpowiednie uprawnienia dostępu użytkownika i roli?
* Czy grupy są już ustanowione w Active Directory lokalnym?
  * Jak zorganizowane są grupy?
  * Kto jest członkiem grupy?
  * Jakie uprawnienia/przypisania ról są obecnie dostępne w grupach?
* Czy konieczne będzie oczyszczenie baz danych użytkownika/grupy przed integracją?  (Jest to bardzo ważne pytanie. Wyrzucanie elementów bezużytecznych.

### <a name="access-management-inventory"></a>Dostęp do spisu zarządzania
* Jak obecnie zarządzasz dostępem użytkowników do aplikacji? Czy trzeba zmienić?  Czy zostały uznane za inne sposoby zarządzania dostępem, na przykład z użyciem [usługi Azure RBAC](../../role-based-access-control/role-assignments-portal.md) ?
* Kto musi mieć dostęp do tego co?

Być może nie masz odpowiedzi na wszystkie pytania na pierwszy z nich, ale jest to dobry.  Ten przewodnik pomoże Ci odpowiedzieć na niektóre z tych pytań i podejmować świadome decyzje.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Znajdowanie niezaakceptowanych oficjalnie aplikacji w chmurze za pomocą Cloud Discovery

Jak wspomniano powyżej, mogą istnieć aplikacje, które nie były zarządzane przez Twoją organizację do tej pory.  W ramach procesu spisu można znaleźć niezaakceptowane oficjalnie aplikacje w chmurze. Zobacz [konfigurowanie Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
W poniższych artykułach omówiono różne sposoby integracji aplikacji z usługą Azure AD, a ponadto przedstawiono wskazówki.

* [Określanie, których Active Directory użyć](../fundamentals/active-directory-administer.md)
* [Korzystanie z aplikacji w galerii aplikacji platformy Azure](what-is-single-sign-on.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Typy uwierzytelniania
Każda aplikacja może mieć inne wymagania dotyczące uwierzytelniania. Za pomocą usługi Azure AD certyfikaty podpisywania mogą być używane z aplikacjami, które używają protokołów SAML 2,0, WS-Federation lub OpenID Connect Connect, a także logowania jednokrotnego hasła. Aby uzyskać więcej informacji o typach uwierzytelniania aplikacji do użycia z usługą Azure AD, zobacz [Zarządzanie certyfikatami dla federacyjnych pojedynczych Sign-On w Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) i [hasła logowania jednokrotnego](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie logowania jednokrotnego przy użyciu serwera proxy aplikacja usługi Azure AD
Za pomocą serwera proxy aplikacji Microsoft Azure AD można zapewnić bezpieczny dostęp do aplikacji znajdujących się w sieci prywatnej, z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w środowisku można go łatwo skonfigurować przy użyciu usługi Azure AD.

### <a name="integrating-custom-applications"></a>Integrowanie aplikacji niestandardowych
Jeśli piszesz nową aplikację i chcesz, aby pomóc deweloperom w korzystaniu z zalet usługi Azure AD, zobacz temat Tworzenie aplikacji dla [deweloperów](../active-directory-applications-guiding-developers-for-lob-applications.md).

Jeśli chcesz dodać aplikację niestandardową do galerii aplikacji platformy Azure, zobacz ["Przenoszenie własnej aplikacji" przy użyciu konfiguracji SAML usługi Azure AD Self-Service](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
W poniższych artykułach opisano sposoby zarządzania dostępem do aplikacji, które zostały zintegrowane z usługą Azure AD przy użyciu łączników usługi Azure AD i usługi Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](what-is-access-management.md)
* [Automatyzacja za pomocą łączników usługi Azure AD](../app-provisioning/user-provisioning.md)
* [Assigning users to an application](../active-directory-applications-guiding-developers-assigning-users.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](../active-directory-applications-guiding-developers-assigning-groups.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje, możesz pobrać Azure Active Directory plany wdrożenia z usługi [GitHub](https://aka.ms/deploymentplans). W przypadku aplikacji galerii można pobrać plany wdrożenia dotyczące logowania jednokrotnego, dostępu warunkowego i aprowizacji użytkowników za pomocą [Azure Portal](https://portal.azure.com). 

Aby pobrać plan wdrożenia z Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **aplikacje przedsiębiorstwa**wybierz  |  **Pick an App**  |  **plan wdrożenia**aplikacji.

Przekaż opinię na temat planów wdrażania, pobierając [ankietę dotyczącą planu wdrożenia](https://aka.ms/DeploymentPlanFeedback).
