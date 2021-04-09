---
title: Wprowadzenie do integrowania Azure Active Directory z aplikacjami
description: Ten artykuł zawiera Przewodnik wprowadzający do integrowania Azure Active Directory (AD) z aplikacjami lokalnymi i aplikacjami w chmurze.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: de06bb4f97568eaa40b0b09e9bc2b50608424aa8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775599"
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
* Czy konieczne będzie oczyszczenie baz danych użytkownika/grupy przed integracją?  (To jest ważne pytanie. Wyrzucanie elementów bezużytecznych.

### <a name="access-management-inventory"></a>Dostęp do spisu zarządzania
* Jak obecnie zarządzasz dostępem użytkowników do aplikacji? Czy trzeba zmienić?  Czy zostały uznane za inne sposoby zarządzania dostępem, na przykład z użyciem [usługi Azure RBAC](../../role-based-access-control/role-assignments-portal.md) ?
* Kto musi mieć dostęp do tego co?

Być może nie masz odpowiedzi na wszystkie pytania na pierwszy z nich, ale jest to dobry.  Ten przewodnik pomoże Ci odpowiedzieć na niektóre z tych pytań i podejmować świadome decyzje.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Znajdowanie niezaakceptowanych oficjalnie aplikacji w chmurze za pomocą Cloud Discovery

Jak wspomniano powyżej, mogą istnieć aplikacje, które nie były zarządzane przez Twoją organizację do tej pory.  W ramach procesu spisu można znaleźć niezaakceptowane oficjalnie aplikacje w chmurze. Zobacz [konfigurowanie Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrowanie aplikacji z usługą Azure AD
W poniższych artykułach omówiono różne sposoby integracji aplikacji z usługą Azure AD, a ponadto przedstawiono wskazówki.

* [Określanie, których Active Directory użyć](../fundamentals/active-directory-whatis.md)
* [Korzystanie z aplikacji w galerii aplikacji platformy Azure](what-is-single-sign-on.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Możliwości dla aplikacji, które nie są wymienione w galerii usługi Azure AD

Możesz dodać dowolną aplikację, która już istnieje w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. W zależności od [umowy licencyjnej](https://azure.microsoft.com/pricing/details/active-directory/)dostępne są następujące funkcje:

- Samoobsługowa integracja dowolnej aplikacji, która obsługuje [SAML (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) dostawców tożsamości (zainicjowanych przez program SP lub dostawcy tożsamości)
- Samoobsługowa integracja dowolnej aplikacji sieci Web, która ma stronę logowania opartą na języku HTML przy użyciu [logowania jednokrotnego opartego na hasłach](sso-options.md#password-based-sso)
- Samoobsługowe połączenie aplikacji korzystających z [systemu do obsługi międzydomenowego protokołu zarządzania tożsamościami (standard scim) na potrzeby aprowizacji użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania linków do dowolnej aplikacji w programie [uruchamiania aplikacji pakietu Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) lub [moich aplikacjach](sso-options.md#linked-sign-on)

Jeśli szukasz wskazówek dla deweloperów, jak zintegrować aplikacje niestandardowe z usługą Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-vs-authorization.md). Podczas tworzenia aplikacji, która korzysta z nowoczesnego protokołu, takiego jak [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) do uwierzytelniania użytkowników, możesz zarejestrować ją na platformie tożsamości firmy Microsoft, korzystając z [Rejestracje aplikacji](../develop/quickstart-register-app.md) środowiska w Azure Portal.

### <a name="authentication-types"></a>Typy uwierzytelniania
Każda aplikacja może mieć inne wymagania dotyczące uwierzytelniania. Za pomocą usługi Azure AD certyfikaty podpisywania mogą być używane z aplikacjami, które korzystają z protokołu SAML 2,0, WS-Federation lub OpenID Connect łączenie protokołów i haseł logowania jednokrotnego. Aby uzyskać więcej informacji na temat typów uwierzytelniania aplikacji, zobacz [Zarządzanie certyfikatami dla federacyjnego jednego Sign-On w Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) i [hasła logowania jednokrotnego](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Włączanie logowania jednokrotnego przy użyciu serwera proxy aplikacja usługi Azure AD
Za pomocą serwera proxy aplikacji Microsoft Azure AD można zapewnić bezpieczny dostęp do aplikacji znajdujących się w sieci prywatnej, z dowolnego miejsca i na dowolnym urządzeniu. Po zainstalowaniu łącznika serwera proxy aplikacji w środowisku można go łatwo skonfigurować przy użyciu usługi Azure AD.

### <a name="integrating-custom-applications"></a>Integrowanie aplikacji niestandardowych
Jeśli chcesz dodać aplikację niestandardową do galerii aplikacji platformy Azure, zobacz temat [publikowanie aplikacji w galerii aplikacji usługi Azure AD](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Zarządzanie dostępem do aplikacji
W poniższych artykułach opisano sposoby zarządzania dostępem do aplikacji, które zostały zintegrowane z usługą Azure AD przy użyciu łączników usługi Azure AD i usługi Azure AD.

* [Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD](what-is-access-management.md)
* [Automatyzacja za pomocą łączników usługi Azure AD](../app-provisioning/user-provisioning.md)
* [Assigning users to an application](./assign-user-or-group-access-portal.md) (Przypisywanie użytkowników do aplikacji)
* [Assigning groups to an application](./assign-user-or-group-access-portal.md) (Przypisywanie grup do aplikacji)
* [Udostępnianie kont](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje, możesz pobrać Azure Active Directory plany wdrożenia z usługi [GitHub](../fundamentals/active-directory-deployment-plans.md). W przypadku aplikacji galerii można pobrać plany wdrożenia dotyczące logowania jednokrotnego, dostępu warunkowego i aprowizacji użytkowników za pomocą [Azure Portal](https://portal.azure.com).

Aby pobrać plan wdrożenia z Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **aplikacje przedsiębiorstwa** wybierz  |    |  **plan wdrożenia** aplikacji.
